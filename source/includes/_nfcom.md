# NFCom

Através da API NFCom é possível:

* Emitir NFCom (Nota Fiscal de Serviço de Comunicação Eletrônica) utilizando dados simplificados. Este processo é **assíncrono**. Ou seja, após a emissão a nota será enfileirada para processamento.
* Consultar o status de NFCom emitidas.
* Cancelar NFCom.

## URLs

Método | URL (recurso)            | Ação
-------|-------                   |-----
POST   | /v2/nfcom?ref=REFERENCIA | Emite uma NFCom.
GET    | /v2/nfcom/REFERENCIA     | Consulta a NFCom com a referência informada e seu status de processamento.
DELETE |  /v2/nfcom/REFERENCIA    | Cancela a NFCom com a referência informada.

## Campos da NFCom

A NFCom possui vários campos para os mais variados tipos e formas de operações, por isso, criamos uma página exclusiva que mostra todos os campos da nossa API para o envio de NFCom. Nela, você pode buscar os campos pela TAG XML ou pela nossa tradução para API.

[Documentação completa dos campos NFCom](https://campos.focusnfe.com.br/nfcom/NotaFiscalComunicacaoXML.html)

## Status API

Aqui você encontra os status possíveis para NFCom.

HTTP CODE/STATUS | Status API Focus | Descrição | Correção
---|---|---|---|
422 - unprocessable entity | erro_validacao_schema | Erro na validação do Schema XML. | Verifique o detalhamento do erro na resposta da API.
422 - unprocessable entity | nao_autorizado | NFCom não autorizada. | O cancelamento só é possível para NFCom's autorizadas.
404 - not found | nao_encontrado | Utilize o método POST. | O método de envio usado é diferente de POST, por favor, use o HTTP POST.
404 - not found | nao_encontrado | NFCom não encontrada. | Verifique se a nota a ser cancelada realmente existe antes de enviar o cancelamento.
400 - bad request | requisicao_invalida | Parâmetro "justificativa" não informado. | Você precisa usar o parâmetro 'justificativa'. Consulte a nossa documentação.
400 - bad request | requisicao_invalida | Parâmetro "justificativa" deve ter entre 15 e 255 caracteres. | A sua justificativa não possui de 15 à 255 careacteres.
400 - bad request | requisicao_invalida | Parâmetro X não informado. | Onde X é o campo que não foi informado em sua requisição.
400 - bad request | requisicao_invalida | Não existe série com os critérios informados. | Os critérios de inutilização não existem. Verifique a nossa documentação.
400 - bad request | requisicao_invalida | CNPJ do emitente não autorizado ou não informado. | Verifique o campo "cnpj_emitente" em seu JSON. É preciso habilitar a emissão de NFCom no cadastro do emitente(Painel API).
400 - bad request | requisicao_invalida | CNPJ/UF do emitente não autorizado ou não informado. | Verifique os campos "cnpj_emitente" e "uf_emitente". É preciso habilitar a emissão de NFCom no cadastro do emitente(Painel API).
403 - forbidden | permissao_negada | CNPJ do emitente não autorizado. | O emitente utilizado não está autorizado a emitir NFCom ou foi informado o CNPJ do emitente incorretamente no JSON.

## Envio

```shell
# arquivo.json deve conter os dados da NFCom
curl -u "token obtido no cadastro da empresa:" \
  -X POST -T nfcom.json https://homologacao.focusnfe.com.br/v2/nfcom?ref=12345
```

> Exemplos de respostas da API por **status**:
>
> **Sucesso**
>
> Código HTTP: `202 Accepted`

```json
{
    "cnpj_emitente": "53681445000141",
    "ref": "teste_emissao_nfcom",
    "status": "processando_autorizacao"
}
```

> **O CNPJ informado não está autorizado a emitir NFCom**
>
> Código HTTP: `403 Forbidden`

```json
{
    "codigo": "permissao_negada",
    "mensagem": "CNPJ do emitente não autorizado."
}
```

Para enviar uma **NFCom** utilize a URL abaixo, alterando o ambiente de produção para homologação, caso esteja emitindo notas de testes.

**Método HTTP:** `POST`

**URL:** `https://api.focusnfe.com.br/v2/nfcom?ref=REFERENCIA`

Envie no corpo do POST os dados da nota fiscal em formato JSON.

Nesta etapa, é feita uma primeira validação dos dados da nota. Caso ocorra algum problema, por exemplo, algum campo faltante, formato incorreto
ou algum problema com o emitente a nota **não será aceita para processamento** e será devolvida a mensagem de erro apropriada. Veja a seção [erros](#introducao_erros).

Caso a nota seja validada corretamente, a nota será **aceita para processamento**. Isto significa que a nota irá para uma fila de processamento
onde eventualmente será processada (processamento assíncrono). Com isto, a nota poderá ser autorizada ou ocorrer um erro na autorização, de acordo com a validação da SEFAZ.

Para verificar se a nota já foi autorizada, você terá que efetuar uma [consulta](#nfcom_consulta).

### Emissão em contingência offline

Para enviar uma NFCom em contingência offline utilize a URL abaixo, alterando o ambiente de produção para homologação, caso esteja emitindo notas de teste.

`https://api.focusnfe.com.br/v2/nfcom?ref=REFERENCIA&contingencia=1`

É importante ressaltar que atualmente não há a possibilidade de envio de NFCom em contingência offline de forma automática, apenas de forma manual, utilizando o parâmetro **contingencia=1**.

Ao enviar a NFCom por este endpoint, a NFCom será autorizada de forma síncrona com o status HTTP 201 (created) **sem comunicação com a SEFAZ**, fazendo uma tentativa de efetivação posterior. 

## Consulta

```shell
curl -u "token obtido no cadastro da empresa:" \
  https://homologacao.focusnfe.com.br/v2/nfcom/12345
```

> Exemplos de respostas da API por **status**:
>
> **Sucesso**
>
> Código HTTP: `200 OK`

```json
{
    "cnpj_emitente": "53681445000141",
    "ref": "teste_emissao_nfcom",
    "status": "cancelado",
    "status_sefaz": "135",
    "mensagem_sefaz": "Evento registrado e vinculado a NFCom ",
    "chave": "NFCom41250353681445000141620010000000061006102424",
    "numero": "6",
    "serie": "1",
    "modelo": "62",
    "caminho_xml": "https://focusnfe.s3.sa-east-1.amazonaws.com/arquivos_development/53681445000141_721/202503/XMLs/NFCom41250353681445000141620010000000061006102424-nfcom.xml",
    "caminho_xml_cancelamento": "https://focusnfe.s3.sa-east-1.amazonaws.com/arquivos_development/53681445000141_721/202503/XMLs/41250353681445000141620010000000061006102424-nfcom-can.xml"
}
```

> **Não foi encontrada uma NFCom para a refêrencia informada**
>
> Código HTTP: `404 Not Found`

```json
{
    "codigo": "nao_encontrado",
    "mensagem": "Nfcom não encontrado"
}
```

Para consultar uma **NFCom** utilize a URL abaixo, alterando o ambiente de produção para homologação, caso esteja emitindo notas de testes.

**Método HTTP:** `GET`

**URL:** `https://api.focusnfe.com.br/v2/nfcom/REFERENCIA?completa=(0|1)`

Parâmetro Opcional | Ação
-------|-------|
completa = 0 ou 1 | Habilita a API há mostrar campos adicionais na requisição de consulta.

Campos de retorno:

* **cnpj_emitente**: O CNPJ emitente da NFCom (o CNPJ de sua empresa).
* **ref**: A referência da emissão.
* **status**: A situação atual da NFCom, podendo ser:
  - **autorizado**: A nota foi autorizada. Neste caso, é fornecido os dados completos da nota, como chave e arquivos para download.
  - **cancelado**: O documento foi cancelado. Neste caso, é fornecido o caminho para download do XML de cancelamento (caminho_xml_cancelamento).
  - **erro_autorizacao**: Houve um erro de autorização por parte da SEFAZ. A mensagem de erro você encontrará nos campos status_sefaz e mensagem_sefaz. É possível fazer o reenvio da nota com a mesma referência, se ela estiver neste estado.
  - **denegado**: O documento foi denegado. A SEFAZ pode denegar uma nota se houver algum erro cadastral nos dados do destinatário ou do emitente. A mensagem de erro você encontrará nos campos status_sefaz e mensagem_sefaz. Não é possível reenviar a nota caso este estado seja alcançado, pois é gerado um número, série, chave de NFCom e XML para esta nota. O XML deverá ser armazenado pelo mesmo período de uma nota autorizada ou cancelada.
* **status_sefaz**: O status da nota na SEFAZ.
* **mensagem_sefaz**: Mensagem descritiva da SEFAZ detalhando o status.
* **chave**: A chave da NFCom, caso ela tenha sido autorizada.
* **numero**: O número da NFCom, caso ela tenha sido autorizada.
* **serie**: A série da NFCom, caso ela tenha sido autorizada.
* **modelo**: O modelo da NFCom, caso ela tenha sido autorizada.
* **caminho_xml**: Caso a nota tenha sido autorizada, retorna o caminho para download do XML.
* **caminho_danfecom**: Caso a nota tenha sido autorizada retorna o caminho para download do DANFe-COM.
* **caminho_xml_cancelamento**: Caso a nota esteja cancelada, é fornecido o caminho para fazer o download do XML de cancelamento.

Caso na requisição seja passado o parâmetro `completa=1` serão adicionados 4 campos:

* **requisicao**: Inclui os dados completos da requisição da NFCom, da mesma forma que constam no XML da nota.
* **protocolo**: Inclui os dados completos do protocolo devolvido pela SEFAZ.
* **requisicao_cancelamento**: Inclui os dados completos da requisição de cancelamento da NFCom.
* **protocolo_cancelamento**: Inclui os dados completos do protocolo devolvido pela SEFAZ.

Caso a NFCom tenha sido emitida em contingência, serão adicionados os seguintes campos:

* **contingencia_offline**: O valor 'true' indica que a emissão foi feita em contingência offline
* **contingencia_offline_efetivada**: Indica se a NFCom já foi efetivada (transmitida) para a SEFAZ.

## Cancelamento

```shell
curl -u "token obtido no cadastro da empresa:" \
  -X DELETE -d '{"justificativa":"Informe aqui a sua justificativa para realizar o cancelamento da NFCom."}' \
  https://homologacao.focusnfe.com.br/v2/nfcom/12345
```

> Exemplos de respostas da API por **status**:
>
> **Sucesso**
>
> Código HTTP: `200 OK`

```json
{
    "status": "cancelado",
    "status_sefaz": "135",
    "mensagem_sefaz": "Evento registrado e vinculado a NFCom ",
    "caminho_xml": "https://focusnfe.s3.sa-east-1.amazonaws.com/arquivos_development/53681445000141_721/202503/XMLs/41250353681445000141620010000000091034577557-nfcom-can.xml"
}
```

> **NFCom ainda não processada**
>
> Código HTTP: `400 Bad Request`

```json
{
    "codigo": "nao_autorizado",
    "mensagem": "NFCom não autorizado"
}
```

Para cancelar uma **NFCom**, basta fazer uma requisição à URL abaixo, alterando o ambiente de produção para homologação, caso esteja emitindo notas de testes.

**Método HTTP:** `DELETE`

**URL:** `https://api.focusnfe.com.br/v2/nfcom/REFERENCIA`

Este método é síncrono, ou seja, a comunicação com a SEFAZ será feita imediatamente e devolvida a resposta na mesma requisição.

O parâmetro de cancelamento deverá ser enviado da seguinte forma:

* **justificativa**: Justificativa do cancelamento. Deverá conter de 15 a 255 caracteres.

A API devolverá os seguintes campos:

* **status**: cancelado, caso a nota seja cancelada, ou erro_cancelamento, se houve algum erro ao cancelar a nota.
* **status_sefaz**: O status do cancelamento na SEFAZ.
* **mensagem_sefaz**: Mensagem descritiva da SEFAZ detalhando o status.
* **caminho_xml**: Caso a nota tenha sido cancelada, será informado aqui o caminho para download do XML de cancelamento.

### Prazo de cancelamento
A NFCom poderá ser cancelada em até 24 horas após a emissão. No entanto, alguns estados podem permitir um prazo maior para o cancelamento.
