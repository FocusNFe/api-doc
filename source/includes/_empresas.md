# Empresas

A API para empresas consiste em uma série de métodos para criar e habilitar automaticamente uma empresa para emissão de documentos fiscais (NFe, NFCe ou NFSe). São disponibilizados os métodos para criar uma empresa, buscar dados de uma empresa ou listar todas as empresas. Cada empresa possui um identificador único (campo id) que você deverá armazenar em sua base de dados para poder alterar ou buscar dados da empresa posteriormente.

**Ambientes**

A API de Empresas opera exclusivamente no ambiente de produção, não sendo possível realizar operações em homologação. Porém, quando aplicável, adicionamos o parâmetro opcional dry_run=1 que permite que a criação da empresa seja simulada sem que seja efetivamente efetuada no nosso banco de dados. Desta forma você poderá testar as chamadas antes de implementá-las em produção.

Uma vez criada a empresa no ambiente de produção, você poderá emitir documentos fiscais no ambiente de homologação e produção usando esta empresa.

## URLs

Método | URL (recurso) | Ação
-------|---------------|------
POST|/v2/empresas | Cria uma nova empresa.
GET|/v2/empresas | Consulta todas as empresas.
GET|/v2/empresas/ID | Consulta uma a empresa a partir do seu identificador.
PUT|/v2/empresas/ID | Altera os dados de uma empresa específica.
DELETE|/v2/empresas/ID | Exclui uma empresa

## Campos

O formato de envio dos campos é no formato JSON, que deverá ser enviado no formato:

 {empresa: {atributo1: valor1, atributo2: valor2, … } }

Abaixo são listados todos os campos de uma empresa.

>Exemplo de um arquivo JSON de envio

```json
{
   "nome":"Nome da empresa Ltda",
   "nome_fantasia":"Nome Fantasia",
   "bairro":"Vila Isabel",
   "cep":80210000,
   "cnpj":"10964044000164",
   "complemento":"Loja 1",
   "discrimina_impostos":true,
   "email":"test@example.com",
   "enviar_email_destinatario":true,
   "inscricao_estadual":1234,
   "inscricao_municipal":46532,
   "logradouro":"Rua João da Silva",
   "numero":153,
   "regime_tributario":1,
   "telefone":"4130333333",
   "municipio":"Curitiba",
   "uf":"PR",
   "habilita_nfe":true,
   "habilita_nfce":true,
   "arquivo_certificado_base64":"MIIj4gIBAzCCI54GCSqGSIb3DQEHAaCC..apagado…ASD==",
   "senha_certificado":123456,
   "csc_nfce_producao":"ABCDEF",
   "id_token_nfce_producao":"00001"
}
```

| Campo                                              | Tipo             | Obrig. NFe | Obrig. NFCe | Obrig. NFSe | Descrição
|----------------------------------------------------|------------------|------------|-------------|-------------|--------------------------------
| nome                                               | texto            | Sim        | Sim         | Sim         | Razão social da empresa
| nome_fantasia                                      | texto            | Sim        | Sim         | Sim         | Nome fantasia
| inscricao_estadual                                 | numérico         | Sim        | Sim         |             | Inscrição estadual
| inscricao_municipal                                | numérico         |            |             | Sim         | Inscrição municipal
| cnpj                                               | numérico         | Sim        | Sim         | Sim         | CNPJ
| regime_tributario                                  | enumeração       | Sim        | Sim         | Sim         | Regime tributário. Valores possíveis:<br> 1 - Simples Nacional;<br> 2 - Simples Nacional - Excesso de sublimite de receita bruta;<br> 3 - Regime Normal;<br> 4 - Simples Nacional - Microempreendedor Individual - MEI
| email                                              | email            | Sim        | Sim         | Sim         | Email de contato da empresa
| telefone                                           | numérico         | Sim        | Sim         |             | Telefone da empresa
| logradouro                                         | texto            | Sim        | Sim         | Sim         | Endereço: logradouro
| numero                                             | numérico         | Sim        | Sim         | Sim         | Endereço: número
| complemento                                        | texto            |            |             |             | Endereço: complemento
| bairro                                             | texto            | Sim        | Sim         | Sim         | Endereço: bairro
| cep                                                | numérico         | Sim        | Sim         | Sim         | Endereço: CEP completo
| municipio                                          | texto            | Sim        | Sim         | Sim         | Endereço: nome do município sem abreviações
| uf                                                 | texto            | Sim        | Sim         | Sim         | Endereço: UF com 2 caracteres
| enviar_email_destinatario                          | booleano         |            |             |             | Habilita ou não envio de e-mail ao destinatário/tomador do serviço após emissão do documento fiscal
| discrimina_impostos                                | booleano         |            |             | N/A         | Habilita ou não o cálculo automático de impostos totais aproximados de acordo com a Lei da Transparência. Não é utilizado para NFSe.
| cpf_cnpj_contabilidade                             | numérico         |            |             | N/A         | CPF/CNPJ da contabilidade da empresa. Alguns estados necessitam que esta informação seja adicionado (no momento apenas BA obriga).
| habilita_nfe                                       | booleano         | Sim        | N/A         | N/A         | Informa se empresa será habilitada para emissão de NFe – Nota Fiscal Eletrônica modelo 55
| habilita_nfce                                      | booleano         | N/A        | Sim         | N/A         | Informa se empresa será habilitada para emissão de NFCe – Nota Fiscal ao Consumidor Eletrônica modelo 65
| habilita_nfse                                      | booleano         | N/A        | N/A         | Sim _*_     | Informa se empresa será habilitada para emissão de NFSe – Nota Fiscal de Serviço Eletrônica. _* Não pode ser habilitada simultaneamente com NFSe Nacional em produção (campo habilita_nfsen_producao)_
| habilita_nfsen_producao                            | booleano         | N/A        | N/A         | Sim _*_     | Informa se empresa será habilitada para emissão de NFSe Nacional em ambiente de produção – Nota Fiscal de Serviço Eletrônica Nacional. _* Não pode ser habilitada simultaneamente com NFSe (campo habilita_nfse)_
| habilita_nfsen_homologacao                         | booleano         | N/A        | N/A         | Sim         | Informa se empresa será habilitada para emissão de NFSe Nacional em ambiente de homologação – Nota Fiscal de Serviço Eletrônica Nacional
| habilita_cte                                       | booleano         | N/A        | N/A         | N/A         | Informa se empresa será habilitada para emissão de CTe ou CTeOS (Conhecimento de Transporte)
| habilita_mdfe                                      | booleano         | N/A        | N/A         | N/A         | Informa se empresa será habilitada para emissão de MDFe (Manifesto Eletrônico de Documentos Fiscais - usado por emitentes de CTe)
| habilita_manifestacao                              | booleano         | N/A        | N/A         | N/A         | Informa se empresa será habilitada para busca de NFe recebidas para realização de manifestação do destinatário (MDe)
| habilita_manifestacao_cte                          | booleano         | N/A        | N/A         | N/A         | Informa se empresa será habilitada para busca de CTes recebidas
| habilita_contingencia_offline_nfce                 | booleano         | N/A        | N/A         | N/A         | Informa se empresa será habilitada para contingência offline de NFCe
| reaproveita_numero_nfce_contingencia               | booleano         | N/A        | N/A         | N/A         | Informa se empresa será habilitada para reaproveitar número de NFCe emitido em contingência
| mostrar_danfse_badge                               | booleano         | N/A        | N/A         | N/A         | Define se a DANFSe exibirá um distintivo (badge) com o nome da Focus. Se verdadeiro, o badge será exibido. Se falso, a DANFSe será emitida no formato white label, ou seja, sem o badge da empresa.
| orientacao_danfe                                   | texto            | Não        | N/A         | N/A         | Orientação da DANFe. Valores possíveis: portrait, para orientação retrato; ou ladscape, para orientação paisagem.
| recibo_danfe                                       | booleano         | Não        | N/A         | N/A         | Informa se empresa será habilitada para exibir recibo na DANFe.
| exibe_sempre_ipi_danfe                             | booleano         | Não        | N/A         | N/A         | Informa se empresa será habilitada para imprimir sempre colunas do IPI
| exibe_issqn_danfe                                  | booleano         | Não        | N/A         | N/A         | Informa se empresa será habilitada para mostra dados do ISSQN
| exibe_impostos_adicionais_danfe                    | booleano         | Não        | N/A         | N/A         | Informa se empresa será habilitada para imprimir impostos adicionais na DANFe (II, PIS, COFINS, ICMS UF Destino, ICMS UF Remetente, valor total tributos)
| exibe_sempre_volumes_danfe                         | booleano         | Não        | N/A         | N/A         | Informa se empresa será habilitada para sempre mostrar volumes na DANFe
| exibe_composicao_carga_mdfe                        | booleano         | Não        | N/A         | N/A         | Informa se empresa será habilitada para mostrar composição da carga na MDFe
| enviar_email_destinatario                          | booleano         |            |             |             | Informa se empresa será habilitada para enviar email ao destinatário em produção
| enviar_email_homologacao                           | booleano         |            |             |             | Informa se empresa será habilitada para enviar email ao destinatário em homologação
| discrimina_impostos                                | booleano         |            |             |             | Informa se empresa será habilitada para discriminar impostos de NFe e NFCe
| csc_nfce_producao                                  | texto            | N/A        | Sim         | N/A         | CSC para emissão de NFCe em ambiente de produção. Sem este campo não será possível emitir NFCe em produção. Veja com o SEFAZ do seu estado como gerar este código.
| id_token_nfce_producao                             | numérico         | N/A        | Sim         | N/A         | Id do CSC para emissão de NFCe em ambiente de produção. Sem este campo não será possível emitir NFCe em produção. Veja com o SEFAZ do seu estado como gerar este número.
| csc_nfce_homologacao                               | texto            | N/A        |             | N/A         | CSC para emissão de NFCe em ambiente de homologação. Sem este campo não será possível emitir NFCe em homologação.
| id_token_nfce_homologacao                          | numérico         | N/A        |             |             | Id do CSC para emissão de NFCe em ambiente dehomologação. Sem este campo não será possível emitir NFCe em homologação.
| proximo_numero_nfe_producao                        | numérico         |            | N/A         | N/A         | Próximo número da NFe a ser emitida em produção. Calculado automaticamente.
| proximo_numero_nfe_homologacao                     | numérico         |            | N/A         | N/A         | Próximo número da NFe a ser emitida em homologação. Calculado automaticamente.
| serie_nfe_producao                                 | numérico         |            | N/A         | N/A         | Série da NFe a ser emitida em produção. Valor padrão: 1
| serie_nfe_homologacao                              | numérico         |            | N/A         | N/A         | Série da NFe a ser emitida em homologação. Valor padrão: 1
| proximo_numero_nfce_producao                       | numérico         |            | N/A         | N/A         | Próximo número da NFCe a ser emitida em produção. Calculado automaticamente.
| proximo_numero_nfce_homologacao                    | numérico         |            | N/A         | N/A         | Próximo número da NFCe a ser emitida em homologação. Calculado automaticamente.
| serie_nfce_producao                                | numérico         |            | N/A         | N/A         | Série da NFCe a ser emitida em produção. Valor padrão: 1
| serie_nfce_homologacao                             | numérico         |            | N/A         | N/A         | Série da NFCe a ser emitida em homologação. Valor padrão: 1
| proximo_numero_nfse_producao                       | numérico         | N/A        | N/A         |             | Próximo número do RPS da NFSe a ser emitida em produção. Calculado automaticamente.
| proximo_numero_nfse_homologacao                    | numérico         | N/A        | N/A         |             | Próximo número do RPS da NFSe a ser emitida em homologação. Calculado automaticamente.
| serie_nfse_producao                                | numérico         | N/A        | N/A         |             | Série do RPS para envio de NFSe em produção. Algumas prefeituras não utilizam.
| serie_nfse_homologacao                             | numérico         | N/A        | N/A         |             | Série do RPS em homologação.
| proximo_numero_nfsen_producao                      | numérico         | N/A        | N/A         |             | Próximo número do RPS da NFSe Nacional a ser emitida em produção. Calculado automaticamente.
| proximo_numero_nfsen_homologacao                   | numérico         | N/A        | N/A         |             | Próximo número do RPS da NFSe Nacional a ser emitida em homologação. Calculado automaticamente.
| serie_nfsen_producao                               | numérico         | N/A        | N/A         |             | Série do RPS para envio de NFSe Nacional em produção. Algumas prefeituras não utilizam.
| serie_nfsen_homologacao                            | numérico         | N/A        | N/A         |             | Série do RPS para envio de NFSe Nacional em homologação. Algumas prefeituras não utilizam.
| proximo_numero_cte_producao                        | numérico         |            | N/A         | N/A         | Próximo número da CTe a ser emitida em produção. Calculado automaticamente.
| proximo_numero_cte_homologacao                     | numérico         |            | N/A         | N/A         | Próximo número da CTe a ser emitida em homologação. Calculado automaticamente.
| serie_cte_producao                                 | numérico         |            | N/A         | N/A         | Série da CTe a ser emitida em produção. Valor padrão: 1
| serie_cte_homologacao                              | numérico         |            | N/A         | N/A         | Série da CTe a ser emitida em homologação. Valor padrão: 1
| proximo_numero_cte_os_producao                     | numérico         |            | N/A         | N/A         | Próximo número da CTeOS a ser emitida em produção. Calculado automaticamente.
| proximo_numero_cte_os_homologacao                  | numérico         |            | N/A         | N/A         | Próximo número da CTeOS a ser emitida em homologação. Calculado automaticamente.
| serie_cte_os_producao                              | numérico         |            | N/A         | N/A         | Série da CTeOS a ser emitida em produção. Valor padrão: 1
| serie_cte_os_homologacao                           | numérico         |            | N/A         | N/A         | Série da CTeOS a ser emitida em homologação. Valor padrão: 1
| proximo_numero_mdfe_producao                       | numérico         |            | N/A         | N/A         | Próximo número da MDFe a ser emitida em produção. Calculado automaticamente.
| proximo_numero_mdfe_homologacao                    | numérico         |            | N/A         | N/A         | Próximo número da MDFe a ser emitida em homologação. Calculado automaticamente.
| serie_mdfe_producao                                | numérico         |            | N/A         | N/A         | Série da MDFe a ser emitida em produção. Valor padrão: 1
| serie_mdfe_homologacao                             | numérico         |            | N/A         | N/A         | Série da MDFe a ser emitida em homologação. Valor padrão: 1
| mdfe_sincrono                                      | booleano         |            | N/A         | N/A         | Informa se a MDFe será emitida de forma síncrona ou assíncrona. Se verdadeiro, a MDFe será emitida de forma síncrona. Se falso, a MDFe será emitida de forma assíncrona. Por padrão, será emitida de forma assíncrona.
| mdfe_sincrono_homologacao                          | booleano         |            | N/A         | N/A         | Informa se a MDFe será emitida de forma síncrona ou assíncrona em homologação. Se verdadeiro, a MDFe será emitida de forma síncrona. Se falso, a MDFe será emitida de forma assíncrona. Por padrão, será emitida de forma assíncrona.
| arquivo_certificado_base64                         | texto em base 64 | Sim        | Sim         | Sim _*_     | Arquivo do certificado digital, em formato PFX ou P12, codificado em base64. _* Nem todas as prefeituras necessitam de certificado para emissão de NFSe_
| senha_certificado                                  | texto            | Sim        | Sim         | Sim _*_     | Senha do certificado digital. _* Obrigatório apenas se informado arquivo_certificado_base64_
| certificado_especifico                             | booleano         | Não        | Não         | Não         | Informa se a atualização de certificado é exclusiva para esta empresa. Se não informado ou false, atualização de certificado é propagada para todas empresas com o mesmo CNPJ base (matriz e filiais), exceto as que já possuam este campo definido como true.
| arquivo_logo_base64                                | texto em base 64 |            |             |             | Logomarca da empresa para ser usada na DANFE. Nem todas as prefeituras aceitam o uso de logo. Utilize uma imagem em formato PNG de no máximo 200×200 pixels
| nome_responsavel                                   | texto            |            |             |             | Nome do responsável pela empresa
| cpf_responsavel                                    | texto            |            |             |             | CPF do resonsável pela empresa
| login_responsavel                                  | texto            |            |             |             | Login para acesso da prefeitura. Necessário para emissão de NFSe em alguns municípios que não utilizam certificado digital.
| senha_responsavel                                  | texto            |            |             |             | Senha para acesso da prefeitura. Necessário para emissão de NFSe em alguns municípios que não utilizam certificado digital. Por motivos de segurança, este atributo não é exibido na consulta da empresa após ser salvo.
| senha_responsavel_preenchida                       | booleano         |            |             |             | Indica se o campo senha_responsavel está preenchido.
| data_inicio_recebimento_nfe                        | data             |            |             |             | Caso MDe esteja habilitado, permite especificar qual a data em que os documentos começarão ser recebidos. Documentos com datas anteriores serão ignorados e não serão cobrados. Se deixado em branco, serão buscados todos os documentos disponíveis. Uma vez definido, não poderá ser modificado.
| data_inicio_recebimento_cte                        | data             |            |             |             | Caso a manifestação de CTes recebidas esteja habilitado, permite especificar qual a data em que os documentos começarão ser recebidos. Documentos com datas anteriores serão ignorados e não serão cobrados. Se deixado em branco, serão buscados todos os documentos disponíveis. Uma vez definido, não poderá ser modificado.
| smtp_endereco                                      | texto            |            |             |             | Endereço do servidor SMTP para envio de e-mails.
| smtp_dominio                                       | texto            |            |             |             | Domínio do servidor SMTP para envio de e-mails. Se você precisa especificar um domínio HELO, você pode fazê-lo aqui.
| smtp_autenticacao                                  | texto            |            |             |             | Tipo de autenticação do servidor SMTP para envio de e-mails. Se o seu servidor de e-mail exigir autenticação, você deve inserir o tipo de autenticação aqui. Valores possíveis: plain, login, cram_md5
| smtp_porta                                         | numérico         |            |             |             | Porta do servidor SMTP para envio de e-mails.
| smtp_login                                         | texto            |            |             |             | Login do servidor SMTP para envio de e-mails. Se o seu servidor de e-mail exigir autenticação, você deve inserir o nome de usuário aqui.
| smtp_senha                                         | texto            |            |             |             | Senha do servidor SMTP para envio de e-mails. Se o seu servidor de e-mail exigir autenticação, você deve inserir a senha aqui.
| smtp_remetente                                     | texto            |            |             |             | Remetente (from) dos e-mails enviados pelo servidor SMTP para envio de e-mails.
| smtp_responder_para                                | texto            |            |             |             | Responder para (reply to) dos e-mails enviados pelo servidor SMTP para envio de e-mails.
| smtp_modo_verificacao_openssl                      | texto            |            |             |             | Modo de verificação do servidor SMTP para envio de e-mails. Valores possíveis: peer, none
| smtp_habilita_starttls                             | booleano         |            |             |             | Utiliza o STARTTLS quando estiver conectado ao servidor SMTP para envio de e-mails.
| smtp_ssl                                           | booleano         |            |             |             | Utiliza SSL quando estiver conectado ao servidor SMTP para envio de e-mails.
| smtp_tls                                           | booleano         |            |             |             | Utiliza TLS quando estiver conectado ao servidor SMTP para envio de e-mails.

## Status API

Aqui você encontra um resumo dos status possíveis para a API de empresas.

HTTP CODE/STATUS | Status API Focus | Descrição | Correção
---|---|---|---|
401 - unauthorized | <em branco> | <em branco> | Verifique o seu token de acesso
404 - not found | nao_encontrado | Empresa não encontrada | O id ou cnpj da empresa não foi encontrado
400 - bad request | parametros_invalidos | Existe um problema no JSON recebido | Verifique o formato do arquivo JSON
422 - unprocessable entity | erro_validacao | Arquivo certificado base64 Houve um erro ao instalar o certificado, verifique se a senha está correto e o arquivo está no formato PFX ou P12 codificado em base64 | Verifique se o certificado foi enviado corretamente
422 - unprocessable entity | erro_validacao | Arquivo certificado base64 Certificado não pertence ao CNPJ informado | O certificado enviado não bate com o CNPJ informado
422 - unprocessable entity | erro_validacao | Arquivo certificado base64 Certificado com prazo de validade vencido | Certificado precisa ser renovado

## Criação de empresa

```shell
curl -u "token obtido no cadastro da empresa:" \
  -T empresa.json
  https://api.focusnfe.com.br/v2/empresas
```

Uma empresa pode ser criada usando o seguinte endereço

> Exemplos de respostas da API por **status** para a requisição de envio:

> **sucesso**

```json
{
  "id": 17625,
  "nome": "Nome da empresa Ltda",
  "nome_fantasia": "Nome Fantasia",
  "inscricao_estadual": "1234",
  "inscricao_municipal": "46532",
  "bairro": "Vila Isabel",
  "cargo_responsavel": null,
  "cep": "80210000",
  "cnpj": "88776429000116",
  "cpf": null,
  "codigo_municipio": "4106902",
  "codigo_pais": "1058",
  "codigo_uf": "41",
  "complemento": "Loja 1",
  "cpf_cnpj_contabilidade": null,
  "cpf_responsavel": null,
  "discrimina_impostos": true,
  "email": "test@example.com",
  "enviar_email_destinatario": true,
  "habilita_nfce": false,
  "habilita_nfe": true,
  "habilita_nfse": false,
  "logradouro": "Rua João da Silva",
  "municipio": "Curitiba",
  "nome_responsavel": null,
  "numero": "153",
  "pais": "Brasil",
  "regime_tributario": "1",
  "telefone": "4130333333",
  "uf": "PR",
  "csc_nfce_producao": null,
  "id_token_nfce_producao": null,
  "csc_nfce_homologacao": null,
  "id_token_nfce_homologacao": null,
  "proximo_numero_nfe_producao": 1,
  "proximo_numero_nfe_homologacao": 1,
  "serie_nfe_producao": "1",
  "serie_nfe_homologacao": "1",
  "proximo_numero_nfse_producao": null,
  "proximo_numero_nfse_homologacao": null,
  "serie_nfse_producao": null,
  "serie_nfse_homologacao": null,
  "proximo_numero_nfce_producao": null,
  "proximo_numero_nfce_homologacao": null,
  "serie_nfce_producao": null,
  "serie_nfce_homologacao": null,
  "certificado_valido_ate": null,
  "certificado_valido_de": null,
  "certificado_cnpj": null,
  "data_ultima_emissao": null,
  "caminho_logo": null
}
```

> **erro_validacao** (certificado inválido)

```json
{
  "codigo": "erro_validacao",
  "mensagem": "Erro de validação",
  "erros": [
    {
      "codigo": "erro_validacao",
      "mensagem": "Arquivo certificado base64 Houve um erro ao instalar o certificado, verifique se a senha está correto e o arquivo está no formato PFX ou P12 codificado em base64",
      "campo": "arquivo_certificado_base64"
    }
  ]
}
```

> **erro_validacao** (senha do certificado inválida)

```json
{
  "codigo": "erro_validacao",
  "mensagem": "Erro de validação",
  "erros": [
    {
      "codigo": "erro_validacao",
      "mensagem": "Arquivo certificado base64 Certificado não pertence ao CNPJ informado",
      "campo": "arquivo_certificado_base64"
    }
  ]
}
```

> **erro_validacao** (certificado de outra empresa)

```json
{
  "codigo": "erro_validacao",
  "mensagem": "Erro de validação",
  "erros": [
    {
      "codigo": "erro_validacao",
      "mensagem": "Arquivo certificado base64 Certificado não pertence ao CNPJ informado",
      "campo": "arquivo_certificado_base64"
    }
  ]
}
```

> **erro_validacao** (certificado vencido)

```json
{
  "codigo": "erro_validacao",
  "mensagem": "Erro de validação",
  "erros": [
    {
      "codigo": "erro_validacao",
      "mensagem": "Arquivo certificado base64 Certificado com prazo de validade vencido",
      "campo": "arquivo_certificado_base64"
    }
  ]
}
```

`https://api.focusnfe.com.br/v2/empresas`

Caso queira apenas testar a criação de uma empresa, utilize o endereço abaixo:

`https://api.focusnfe.com.br/v2/empresas?dry_run=1`

Utilize o comando **HTTP POST**.
O conteúdo do POST deverá conter os dados da empresa. O resultado será os dados da empresa criados ou uma mensagem de erro de validação. É importante salvar o campo id gerado para posterior consulta ou alteração da empresa. Considere o campo "id" como sendo alfanumérico, pois no futuro o formato deverá ser alterado para permitir letras e números na identificação.


## Consulta de empresas

```shell
curl -u "token obtido no cadastro da empresa:" \
  https://api.focusnfe.com.br/v2/empresas/123
```

> Exemplos de respostas da API por **status** para a requisição de envio:

> **sucesso**

```json
{
  "id": 17625,
  "nome": "Nome da empresa Ltda",
  "nome_fantasia": "Nome Fantasia",
  "inscricao_estadual": 1234,
  "inscricao_municipal": 46532,
  "bairro": "Vila Isabel",
  "cargo_responsavel": null,
  "cep": 80210000,
  "cnpj": "88776429000116",
  "cpf": null,
  "codigo_municipio": "4106902",
  "codigo_pais": "1058",
  "codigo_uf": "41",
  "complemento": "Loja 1",
  "cpf_cnpj_contabilidade": null,
  "cpf_responsavel": null,
  "discrimina_impostos": true,
  "email": "test@example.com",
  "enviar_email_destinatario": true,
  "habilita_nfce": false,
  "habilita_nfe": true,
  "habilita_nfse": false,
  "logradouro": "Rua João da Silva",
  "municipio": "Curitiba",
  "nome_responsavel": null,
  "numero": 153,
  "pais": "Brasil",
  "regime_tributario": 1,
  "telefone": "4130333333",
  "uf": "PR",
  "csc_nfce_producao": null,
  "id_token_nfce_producao": null,
  "csc_nfce_homologacao": null,
  "id_token_nfce_homologacao": null,
  "proximo_numero_nfe_producao": 1,
  "proximo_numero_nfe_homologacao": 1,
  "serie_nfe_producao": "1",
  "serie_nfe_homologacao": "1",
  "proximo_numero_nfse_producao": null,
  "proximo_numero_nfse_homologacao": null,
  "serie_nfse_producao": null,
  "serie_nfse_homologacao": null,
  "proximo_numero_nfce_producao": null,
  "proximo_numero_nfce_homologacao": null,
  "serie_nfce_producao": null,
  "serie_nfce_homologacao": null,
  "certificado_valido_ate": null,
  "certificado_valido_de": null,
  "certificado_cnpj": null,
  "data_ultima_emissao": null,
  "caminho_logo": null,
  "token_producao": null,
  "token_homologacao": null
}
```

> **nao_encontrado**

```
{
  "codigo": "nao_encontrado",
  "mensagem": "Empresa não encontrada"
}
```

Uma empresa pode ser visualizada usando o seguinte endereço

`https://api.focusnfe.com.br/v2/empresas/ID`

Onde ID é o id da empresa fornecido no momento da criação. Utilize o comando **HTTP GET**. O resultado será todos os dados da empresa.
Além dos campos utilizados para criação da empresa, a consulta retornará também os campos `token_producao` e `token_homologacao` que são gerados pela API para interações nos ambientes de produção e homologação.

## Alteração de empresa

```shell
curl -X PUT -u "token obtido no cadastro da empresa:" \
  -T empresa.json
  https://api.focusnfe.com.br/v2/empresas/123
```

> Exemplos de respostas da API por **status** para a requisição de envio:

> **sucesso**

```json
{
  "id": 17625,
  "nome": "Nome da empresa Ltda",
  "nome_fantasia": "Nome Fantasia",
  "inscricao_estadual": 1234,
  "inscricao_municipal": 46532,
  "bairro": "Vila Isabel",
  "cargo_responsavel": null,
  "cep": 80210000,
  "cnpj": "88776429000116",
  "cpf": null,
  "codigo_municipio": "4106902",
  "codigo_pais": "1058",
  "codigo_uf": "41",
  "complemento": "Loja 1",
  "cpf_cnpj_contabilidade": null,
  "cpf_responsavel": null,
  "discrimina_impostos": true,
  "email": "test@example.com",
  "enviar_email_destinatario": true,
  "habilita_nfce": false,
  "habilita_nfe": true,
  "habilita_nfse": false,
  "logradouro": "Rua João da Silva",
  "municipio": "Curitiba",
  "nome_responsavel": null,
  "numero": 153,
  "pais": "Brasil",
  "regime_tributario": 1,
  "telefone": "4130333333",
  "uf": "PR",
  "csc_nfce_producao": null,
  "id_token_nfce_producao": null,
  "csc_nfce_homologacao": null,
  "id_token_nfce_homologacao": null,
  "proximo_numero_nfe_producao": 1,
  "proximo_numero_nfe_homologacao": 1,
  "serie_nfe_producao": "1",
  "serie_nfe_homologacao": "1",
  "proximo_numero_nfse_producao": null,
  "proximo_numero_nfse_homologacao": null,
  "serie_nfse_producao": null,
  "serie_nfse_homologacao": null,
  "proximo_numero_nfce_producao": null,
  "proximo_numero_nfce_homologacao": null,
  "serie_nfce_producao": null,
  "serie_nfce_homologacao": null,
  "certificado_valido_ate": null,
  "certificado_valido_de": null,
  "certificado_cnpj": null,
  "data_ultima_emissao": null,
  "caminho_logo": null
}
```

> **erro_validacao** (certificado inválido)

```json
{
  "codigo": "erro_validacao",
  "mensagem": "Erro de validação",
  "erros": [
    {
      "codigo": "erro_validacao",
      "mensagem": "Arquivo certificado base64 Houve um erro ao instalar o certificado, verifique se a senha está correto e o arquivo está no formato PFX ou P12 codificado em base64",
      "campo": "arquivo_certificado_base64"
    }
  ]
}
```

> **erro_validacao** (senha do certificado inválida)

```json
{
  "codigo": "erro_validacao",
  "mensagem": "Erro de validação",
  "erros": [
    {
      "codigo": "erro_validacao",
      "mensagem": "Arquivo certificado base64 Certificado não pertence ao CNPJ informado",
      "campo": "arquivo_certificado_base64"
    }
  ]
}
```

> **erro_validacao** (certificado de outra empresa)

```json
{
  "codigo": "erro_validacao",
  "mensagem": "Erro de validação",
  "erros": [
    {
      "codigo": "erro_validacao",
      "mensagem": "Arquivo certificado base64 Certificado não pertence ao CNPJ informado",
      "campo": "arquivo_certificado_base64"
    }
  ]
}
```

> **erro_validacao** (certificado vencido)

```json
{
  "codigo": "erro_validacao",
  "mensagem": "Erro de validação",
  "erros": [
    {
      "codigo": "erro_validacao",
      "mensagem": "Arquivo certificado base64 Certificado com prazo de validade vencido",
      "campo": "arquivo_certificado_base64"
    }
  ]
}
```

Uma empresa pode ser alterada usando o seguinte endereço.

`https://api.focusnfe.com.br/v2/empresas/ID`

Caso queira apenas testar a alteração de uma empresa, utilize o endereço abaixo:

`https://api.focusnfe.com.br/v2/empresas?dry_run=1`

Onde ID é o id da empresa fornecido no momento da criação Utilize o comando **HTTP PUT** para alterar a empresa. O conteúdo do PUT deverá conter os dados da empresa que serão alterados. Os demais dados permanecerão inalterados. O resultado será todos os dados da empresa criados ou uma mensagem de erro de validação. Os dados devem seguir o mesmo formato para criação da empresa.

Atributos que não serão modificados podem ser omitidos.

## Exclusão de empresa

```shell
curl -X DELETE -u "token obtido no cadastro da empresa:" \
  https://api.focusnfe.com.br/v2/empresas/123
```

> Exemplos de respostas da API por **status** para a requisição de envio:

> **sucesso**

```json
{
  "id": 17625,
  "nome": "Nome da empresa Ltda",
  "nome_fantasia": "Nome Fantasia",
  "inscricao_estadual": 1234,
  "inscricao_municipal": 46532,
  "bairro": "Vila Isabel",
  "cargo_responsavel": null,
  "cep": 80210000,
  "cnpj": "88776429000116",
  "cpf": null,
  "codigo_municipio": "4106902",
  "codigo_pais": "1058",
  "codigo_uf": "41",
  "complemento": "Loja 1",
  "cpf_cnpj_contabilidade": null,
  "cpf_responsavel": null,
  "discrimina_impostos": true,
  "email": "test@example.com",
  "enviar_email_destinatario": true,
  "habilita_nfce": false,
  "habilita_nfe": true,
  "habilita_nfse": false,
  "logradouro": "Rua João da Silva",
  "municipio": "Curitiba",
  "nome_responsavel": null,
  "numero": 153,
  "pais": "Brasil",
  "regime_tributario": 1,
  "telefone": "4130333333",
  "uf": "PR",
  "csc_nfce_producao": null,
  "id_token_nfce_producao": null,
  "csc_nfce_homologacao": null,
  "id_token_nfce_homologacao": null,
  "proximo_numero_nfe_producao": 1,
  "proximo_numero_nfe_homologacao": 1,
  "serie_nfe_producao": "1",
  "serie_nfe_homologacao": "1",
  "proximo_numero_nfse_producao": null,
  "proximo_numero_nfse_homologacao": null,
  "serie_nfse_producao": null,
  "serie_nfse_homologacao": null,
  "proximo_numero_nfce_producao": null,
  "proximo_numero_nfce_homologacao": null,
  "serie_nfce_producao": null,
  "serie_nfce_homologacao": null,
  "certificado_valido_ate": null,
  "certificado_valido_de": null,
  "certificado_cnpj": null,
  "data_ultima_emissao": null,
  "caminho_logo": null
}
```

> **nao_encontrado**

```json
{
  "codigo": "nao_encontrado",
  "mensagem": "Empresa não encontrada"
}
```

> **permissao_negada** (empresa não pertencente ao domínio do cliente)

```json
{
  "codigo": "permissao_negada",
  "mensagem": "Empresa não encontrada como propriedade da revenda"
}
```

> **parametros_invalidos**

```json
{
  "erros": [
    {
      "codigo": "parametros_invalidos",
      "mensagem": "Existe um problema no JSON recebido: 822: unexpected token at 'empresa_id=12079'"
    }
  ]
}
```


Uma empresa pode ser cancelada usando o seguinte endereço

`https://api.focusnfe.com.br/v2/empresas/ID`

Onde ID é o id da empresa fornecido no momento da criação. Utilize o comando **HTTP DELETE**. O resultado será todos os dados da empresa excluída. Após esta operação você não terá mais acesso aos dados da empresa ou aos documentos emitidos. Esta operação não é reversível, mas você poderá cadastrar a empresa novamente no futuro se for necessário.

## Listagem de empresas

```shell
curl -u "token obtido no cadastro da empresa:" \
  https://api.focusnfe.com.br/v2/empresas
```

É possível listar todas as empresas criadas usando o seguinte método HTTP:

`https://api.focusnfe.com.br/v2/empresas`

Utilize o comando **HTTP GET**. O resultado será todos os dados de todas a empresas.


## Paginação

Ao fazer uma pesquisa, a API irá devolver o cabeçalho HTTP *X-Total-Count* que representa
o número total de ocorrências da pesquisa, porém a API devolve apenas 50 registros por vez.
Para buscar os demais registros, utilize o parâmetro **offset**. Exemplo:

Vamos supor que a chamada abaixo devolva 123 ocorrências:

`https://api.focusnfe.com.br/v2/empresas`

A segunda e terceira páginas da consulta poderão ser acessados desta forma:

Registros 51 a 100:
`https://api.focusnfe.com.br/v2/empresas?offset=50`

Registros 101 a 123:
`https://api.focusnfe.com.br/v2/empresas?offset=100`
