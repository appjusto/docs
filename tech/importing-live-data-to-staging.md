# 1. (Live) Fazer backup

`npm run build && node lib/admin/cli/maintenance/index run-backup --firestore --storage --env live`

ou:

gcloud config set project app-justo-dev
gcloud firestore export gs://app-justo-dev-backups/firestore/orders --collection-ids=orders

`gcloud firestore export gs://app-justo-live-backups/firestore/{timestamp}`
`gsutil -m -o GSUtil:parallel_process_count=1 -o GSUtil:parallel_thread_count=2 cp -r gs://app-justo-live.appspot.com gs://app-justo-live-backups/storage/{timestamp}`

# 2. (Live -> Staging) Copiar backup do Firestore do bucket live para o bucket staging

`gsutil -m -o GSUtil:parallel_process_count=1 -o GSUtil:parallel_thread_count=2 cp -r gs://app-justo-live-backups/firestore/{timestamp} gs://app-justo-staging-backups/firestore/imported-from-live/{timestamp}`

# 3. (Live -> Staging) Copiar arquivos do Storage do bucket live para o bucket staging

`gsutil -m -o GSUtil:parallel_process_count=1 -o GSUtil:parallel_thread_count=2 cp -r gs://app-justo-live-backups/storage/{timestamp} gs://app-justo-staging-backups/storage/imported-from-live/{timestamp}`

# 4. (Staging) Limpar os dados do Firestore e Authentication

`npm run build && node lib/admin/cli/data/delete --delete-users --env staging`
`
firebase --project app-justo-staging firestore:delete advances -r && \
firebase --project app-justo-staging firestore:delete businesses -r && \
firebase --project app-justo-staging firestore:delete consumers -r && \
firebase --project app-justo-staging firestore:delete couriers -r && \
firebase --project app-justo-staging firestore:delete chats -r && \
firebase --project app-justo-staging firestore:delete fleets -r && \
firebase --project app-justo-staging firestore:delete invoices -r && \
firebase --project app-justo-staging firestore:delete managers -r && \
firebase --project app-justo-staging firestore:delete orders -r && \
firebase --project app-justo-staging firestore:delete platform -r && \
firebase --project app-justo-staging firestore:delete recommendations -r && \
firebase --project app-justo-staging firestore:delete reviews -r
firebase --project app-justo-staging firestore:delete transfers -r && \
firebase --project app-justo-staging firestore:delete users -r && \
firebase --project app-justo-staging firestore:delete warehouse -r && \
firebase --project app-justo-staging firestore:delete withdraws -r
`

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