# Criação & configuração de subconta

## Fluxo

1. Criar subconta

[Documentação](https://dev.iugu.com/reference#criar-subconta)
Endpoint: `marketplace/create_account`
Token: de produção\* conta mestre
Parâmetros: apenas o nome da conta; nosso padrão é: 'Entregador <id>' ou 'Restaurante <id>'
Retorno: id da conta e tokens (`{account_id: '', user_token: '', live_api_token: '', test_api_token: '', name: ''}`)

\*Sempre utilizar o de produção, mesmo em ambiente de teste;

2. Configurar subconta

[Documentação](https://dev.iugu.com/reference#configurar-conta)
Endpoint: `accounts/configuration`
Token: `user_token` da subconta
Parâmetros: habilitação de cartão de crédito como meio de pagamento (`soft_descriptor`: 'AppJusto Entrega', `two_step_transaction`: true, `installments`: false), habilitação de de saque automático (`auto_withdraw`), desabilitação da antecipação automática (`auto_advance`), etc.;
Retorno: objeto da conta

3. Enviar conta para verificação

[Documentação](https://dev.iugu.com/reference#enviar-verificacao-de-conta)
Endpoint: `accounts/<account_id>/request_verification`
Token: `user_token` da subconta
Parâmetros: limite de valor fatura, informações da PF/PJ, informações da conta bancária, etc.
Retorno: objeto com dados de verificação

4. Verificar subconta

[Documentação](https://dev.iugu.com/reference#informacoes-da-conta)
Endpoint: `accounts/<account_id>`
Token: `user_token` da subconta
Retorno: objeto com dados da subconta incluindo um `is_verified?` que indica se a validação foi concluída com sucesso

5. Enable pix

[Documentação]()
Endpoint: `payments/pix`
Token: `user_token` da subconta
Parâmetros: `enabled: true | false`
Retorno: objeto de confirmação

## Observações

- Uma vez criada, a conta não pode ser mais excluída; é possível criar uma nova conta com os mesmos dados.

# Tokenização e criação de meio de pagamento

1. Criar token (feita pelo cliente via app diretamente na iugu)

[Documentação](https://dev.iugu.com/reference#criar-token)
Endpoint: `/payment_token`
Token: sem token
Parâmetros: `{account_id: '', method: 'credit_card', test: true|false, data: {first_name: '', last_name: '', number: '', month: '', year: '', verification_value: '', full_name: ''}}`
Retorno: dados sobre o token (`id`, `display_number`, `holder_name`, etc.)

2. Criar cliente na conta master

Método: POST
Endpoint: `/customers`
Token: `livetoken` | `testtoken`
Parâmetros: `{name: '', email: '', custom_variables: [{name: 'consumerId', value: ''}]`
Retorno:

3. Criar meio de pagamento para o cliente

Método: POST
Endpoint: `customers/<customerId>/payment_methods`
Token: `livetoken` | `testtoken`

# Emissão de faturas
