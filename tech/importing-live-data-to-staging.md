# 1. (Live) Fazer backup do live

`npm run admin -- run-backup --firestore --storage --env live`

ou:

`gcloud firestore export gs://app-justo-live-backups/firestore/{timestamp}`
`gsutil -m cp -r gs://app-justo-live.appspot.com gs://app-justo-live-backups/storage/{timestamp}`

# 3. (Live -> Staging) Copiar backup do Firestore do bucket live para o bucket staging

`gsutil -m cp -r gs://app-justo-live-backups/firestore/{timestamp} gs://app-justo-staging-backups/firestore/imported-from-live/{timestamp}`

# 4. (Live -> Staging) Copiar arquivos do Storage do bucket live para o bucket staging

`gsutil -m cp -r gs://app-justo-live-backups/storage/{timestamp} gs://app-justo-staging-backups/storage/imported-from-live/{timestamp}`

# 5. (Staging) Limpar os dados do Firestore e Authentication

`npm run admin -- delete-collections --env staging --fleets --managers --businesses --consumers --couriers --orders --platform --users`

# 6. (Staging) Apagar os arquivos do Storage

`gsutil -m rm -r gs://app-justo-staging.appspot.com/businesses`
`gsutil -m rm -r gs://app-justo-staging.appspot.com/couriers`
`gsutil -m rm -r gs://app-justo-staging.appspot.com/orders`
`gsutil -m rm -r gs://app-justo-staging.appspot.com/platform`

# 7. (Staging) Apagar os índices do staging no Algolia

# 8. (Staging) Importar os dados do Firestore

`gcloud config set project app-justo-staging`
`gcloud firestore import gs://app-justo-staging-backups/firestore/imported-from-live/{timestamp}`

# 9. (Staging) Importar os arquivos do Storage

`gsutil -m cp -r gs://app-justo-staging-backups/storage/imported-from-live/{timestamp}/platform gs://app-justo-staging.appspot.com/platform`

`gsutil -m cp -r gs://app-justo-staging-backups/storage/imported-from-live/{timestamp}/businesses gs://app-justo-staging.appspot.com/businesses`

`gsutil -m cp -r gs://app-justo-staging-backups/storage/imported-from-live/{timestamp}/couriers gs://app-justo-staging.appspot.com/couriers`

`gsutil -m cp -r gs://app-justo-staging-backups/storage/imported-from-live/{timestamp}/orders gs://app-justo-staging.appspot.com/orders`
