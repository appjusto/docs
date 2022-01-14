# 1. (Live) Fazer backup

`npm run build && node lib/admin/cli/maintenance/index run-backup --firestore --storage --env live`

ou:

`gcloud firestore export gs://app-justo-live-backups/firestore/{timestamp}`
`gsutil -m cp -r gs://app-justo-live.appspot.com gs://app-justo-live-backups/storage/{timestamp}`

# 2. (Live -> Staging) Copiar backup do Firestore do bucket live para o bucket staging

`gsutil -m cp -r gs://app-justo-live-backups/firestore/2021-12-16T07:00:03.990Z gs://app-justo-staging-backups/firestore/imported-from-live/2021-12-16T07:00:03.990Z`

# 3. (Live -> Staging) Copiar arquivos do Storage do bucket live para o bucket staging

`gsutil -m cp -r gs://app-justo-live-backups/storage/{timestamp} gs://app-justo-staging-backups/storage/imported-from-live/{timestamp}`

# 4. (Staging) Limpar os dados do Firestore e Authentication

`npm run build && node lib/admin/cli/data/delete collections advances businesses consumers couriers fleets invoices managers orders platform recommendations users withdraws transfers --delete-users --env staging`

# 5. (Staging) Apagar os arquivos do Storage

`gsutil -m rm -r gs://app-justo-staging.appspot.com/businesses gs://app-justo-staging.appspot.com/couriers gs://app-justo-staging.appspot.com/consumers gs://app-justo-staging.appspot.com/orders gs://app-justo-staging.appspot.com/platform`

# 6. (Staging) Importar os dados do Firestore

`gcloud --project app-justo-staging firestore import gs://app-justo-staging-backups/firestore/imported-from-live/{timestamp} --collection-ids=businesses,consumers,couriers,orders`

# 7. Remover os dados sensíveis

`npm run build && node lib/admin/cli/profile/index remove-notification-tokens --env staging | tee remove-notification-tokens.txt`

`npm run build && node lib/admin/cli/profile/index remove-marketplaces --env staging | tee remove-marketplaces.txt`

# 7. (Staging) Importar os arquivos do Storage

`gsutil -m cp -r gs://app-justo-staging-backups/storage/imported-from-live/{timestamp}/platform gs://app-justo-staging.appspot.com/platform`

`gsutil -m cp -r gs://app-justo-staging-backups/storage/imported-from-live/{timestamp}/businesses gs://app-justo-staging.appspot.com/businesses`

`gsutil -m cp -r gs://app-justo-staging-backups/storage/imported-from-live/{timestamp}/couriers gs://app-justo-staging.appspot.com/couriers`

`gsutil -m cp -r gs://app-justo-staging-backups/storage/imported-from-live/{timestamp}/orders gs://app-justo-staging.appspot.com/orders`

# 8. (Staging) Atualizar dados