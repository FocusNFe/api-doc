# NFSes Recebidas

De forma similar à [manifestação de NFe](#manifestacao-nfe), a API Focus para busca de _NFSes Recebidas_ permite que você consulte notas de serviço em que sua empresa é a tomadora do serviço. Denominamos _notas recebidas_ pois NFSe não contempla a operação de manifestação.

A recuperação de notas é possível apenas em municípios que disponibilizam a consulta de notas recebidas / tomadas - como São Paulo, Sorocaba, Barueri e outras dezenas de [municípios integrados em nossa API](https://focusnfe.com.br/cidades-integradas-nfse/).

A presente documentação deve ser suficiente para realizar a integração com a API do Focus NFe. Em caso de dúvidas, entre em contato com nosso excelente suporte pelo e-mail suporte@focusnfe.com.br. Neste canal você também pode verificar a viabilidade de integração de novos municípios.


## URLs

Método | URL (recurso) | Ação
-------|---------------|------
GET | /v2/nfses_recebidas?cnpj=CNPJ  | Busca os dados de todas as NFSes recebidas para o CNPJ
GET | /v2/nfses_recebidas/CHAVE      | Consulta a nota fiscal completa identificada pela chave informada


## Status API

Aqui você encontra os status possíveis para NFSes Recebidas.

HTTP CODE/STATUS | Status API Focus | Descrição | Correção
---|---|---|---|
400 - bad request | requisicao_invalida | CNPJ do emitente não autorizado ou não informado. | Verifique no Painel API se esse emitente está habilitado para realizar a busca de NFSes recebidas. Verifique se o CNPJ foi informado no JSON de envio.
400 - bad request | requisicao_invalida | CNPJ/UF do emitente não autorizado ou não informado. | Verifique no Painel API se esse emitente está habilitado para realizar a busca de NFSes recebidas. Verifique se o CNPJ foi informado no JSON de envio.
403 - forbidden | permissao_negada | CNPJ do emitente não autorizado. | O emitente utilizado não está autorizado a realizar a operação de busca ou foi informado o CNPJ do emitente incorretamente no JSON.


## Consulta de NFSe Recebidas

As NFSes recebidas possuem um campo chamado "**versao**", que é único entre todos os documentos recebidos do mesmo CNPJ.
Isto permite buscar apenas documentos que seu sistema não conhece, mantendo um único número incremental por CNPJ.

Como uma NFSe pode ser cancelada após ter sido recebida, a **versao** associada é atualizada a cada alteração nesta NFSe.
Por exemplo, se você recebe uma NFSe com `versao: 60` e ela posteriormente for cancelada, a versão da NFSe será atualizada para algum número maior que `60`.

A API busca notas recentes nas prefeituras duas vezes por dia. Atualizações retroativas, como cancelamentos de notas mais antigas, são buscadas aos finais de semana.


### Método de Consulta

> Exemplo de como consultar todas as notas recebidas de uma empresa.

```python
# Faça o download e instalação da biblioteca requests, através do python-pip.
import requests

'''
Para ambiente de produção use a variável abaixo:
url = "https://api.focusnfe.com.br"
'''
url = "https://homologacao.focusnfe.com.br/v2/nfses_recebidas?cnpj="

token="token obtido no cadastro da empresa"

cnpj = "cnpj_do_destinatario_da_nota"

r = requests.get(url+cnpj, auth=(token,""))

# Mostra na tela o codigo HTTP da requisicao e a mensagem de retorno da API
print(r.status_code, r.text)

```


```shell
curl -u "token obtido no cadastro da empresa:" \
  "https://homologacao.focusnfe.com.br/v2/nfses_recebidas?cnpj=SEU_CNPJ"
```


```java
import com.sun.jersey.api.client.Client;
import com.sun.jersey.api.client.ClientResponse;
import com.sun.jersey.api.client.WebResource;
import com.sun.jersey.api.client.config.ClientConfig;
import com.sun.jersey.api.client.config.DefaultClientConfig;
import com.sun.jersey.api.client.filter.HTTPBasicAuthFilter;

public class ConsultarTodosManifestos {

  public static void main(String[] args) {

    String login = "Token_obtido_no_cadastro_da_empresa";
    String cnpj = "CNPJ_da_sua_empresa";

    /* Para ambiente de produção use a variável abaixo:
    String server = "https://api.focusnfe.com.br/"; */
    String server = "https://homologacao.focusnfe.com.br/";
    String url = server.concat("v2/nfses_recebidas?cnpj="+cnpj);

    /* Configuração para realizar o HTTP BasicAuth. */
    Object config = new DefaultClientConfig();
    Client client = Client.create((ClientConfig) config);
    client.addFilter(new HTTPBasicAuthFilter(login, ""));

    WebResource request =  client.resource(url);
    ClientResponse resposta = request.get(ClientResponse.class);
    int httpCode = resposta.getStatus();
    String body = resposta.getEntity(String.class);

    /* As três linhas abaixo imprimem as informações retornadas pela API.
     * Aqui o seu sistema deverá interpretar e lidar com o retorno. */
    System.out.print("HTTP Code: ");
    System.out.print(httpCode);
    System.out.printf(body);
  }
}
```


```ruby
# encoding: UTF-8

require "net/http"
require "net/https"

# token enviado pelo suporte
token = "codigo_alfanumerico_token"

cnpj = "CNPJ_da_sua_empresa"

# endereço da api que deve ser usado conforme o ambiente: produção ou homologação
servidor_producao = "https://api.focusnfe.com.br/"
servidor_homologacao = "https://homologacao.focusnfe.com.br/"

# no caso do ambiente de envio ser em produção, utilizar servidor_producao
url_envio = servidor_homologacao + "v2/nfses_recebidas?cnpj=" + cnpj

# criamos uma objeto uri para envio da nota
uri = URI(url_envio)

# também criamos um objeto da classe HTTP a partir do host da uri
http = Net::HTTP.new(uri.hostname, uri.port)

# aqui criamos um objeto da classe Get a partir da uri de requisição
requisicao = Net::HTTP::Get.new(uri.request_uri)

# adicionando o token à requisição
requisicao.basic_auth(token, '')

# no envio de notas em produção, é necessário utilizar o protocolo ssl
# para isso, basta retirar o comentário da linha abaixo
# http.use_ssl = true

# aqui enviamos a requisição ao servidor e obtemos a resposta
resposta = http.request(requisicao)

# imprimindo o código HTTP da resposta
puts "Código retornado pela requisição: " + resposta.code

# imprimindo o corpo da resposta
puts "Corpo da resposta: " + resposta.body

```


```php
<?php
// Solicite o seu token para realizar as requisições com nossa equipe de suporte.
 $login = "Token_obtido_no_cadastro_da_empresa";
 $cnpj = "CNPJ_da_sua_empresa";
// Para ambiente de Produção, utilize a URL: https://api.focusnfe.com.br/.
 $server = "https://homologacao.focusnfe.com.br/";

 $ch = curl_init();
   curl_setopt($ch, CURLOPT_URL, $server."v2/nfses_recebidas?cnpj=".$cnpj);
   curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
   curl_setopt($ch, CURLOPT_HTTPHEADER, array());
/* Métodos para realizar a autenticação básica do HTTP.
   Não é necessário informar campo senha, apenas o campo login. */
   curl_setopt($ch, CURLOPT_HTTPAUTH, CURLAUTH_BASIC);
   curl_setopt($ch, CURLOPT_USERPWD, "$login");

 $body = curl_exec($ch);
 $http_code = curl_getinfo($ch, CURLINFO_HTTP_CODE);
// Mostra na tela o HTTP Code da sua requisição.
   print($http_code);
// Mostra na tela a mensagem de retorno da API.
   print($body);
   curl_close($ch);
?>
```


```javascript
/*
A orientacao a seguir foram extraidas do site do NPMJS: https://www.npmjs.com/package/xmlhttprequest
Here's how to include the module in your project and use as the browser-based XHR object.
Note: use the lowercase string "xmlhttprequest" in your require(). On case-sensitive systems (eg Linux) using uppercase letters won't work.
*/
var XMLHttpRequest = require("xmlhttprequest").XMLHttpRequest;

var request = new XMLHttpRequest();

var token = "Token_obtido_no_cadastro_da_empresa";

var cnpj = "cnpj_do_destinatario_da_nota";

/*
Para ambiente de producao use a URL abaixo:
"https://api.focusnfe.com.br"
*/
var url = "https://homologacao.focusnfe.com.br/v2/nfses_recebidas?cnpj=" + cnpj;

/*
Use o valor 'false', como terceiro parametro para que a requisicao aguarde a resposta da API
Passamos o token como quarto parametro deste metodo, como autenticador do HTTP Basic Authentication.
*/
request.open('GET', url, false, token);

request.send();

// Sua aplicacao tera que ser capaz de tratar as respostas da API.
console.log("HTTP code: " + request.status);
console.log("Corpo: " + request.responseText);

```

Para consultar os documentos fiscais recebidos, utilize o endereço abaixo:

`https://api.focusnfe.com.br/v2/nfses_recebidas?cnpj=CNPJ`

Utilize o método **HTTP GET** para consultar as notas. Esta requisição aceita os seguintes parâmetros na URL:

* **cnpj**(*): CNPJ da empresa. Campo obrigatório.
* **versao**: Se informado, busca apenas os documentos cuja versão seja maior que o parâmetro recebido. Utilize este parâmetro para buscar apenas as notas que seu sistema ainda não conhece.
* **completa**: Se informado com o valor "1", devolve os dados completos do XML da nota. Caso contrário, devolve os dados resumidos de forma simplificada, conforme descrito na próxima seção.
* **api_version**: Se informado, devolve os dados estruturados conforme seção [Versões da API](#nfses-recebidas_versoes-da-api). Caso contrário, adota a última versão estável.

Serão devolvidas as 100 primeiras notas encontradas. Para recuperar as demais notas você deverá fazer uma nova requisição alterando o campo **versão**.

> Exemplo dos dados de resposta:

```json
[
  {
      "chave": "NFSe3238662000010XXXXXXXXXXXXXXXXXX00000019552-TS25-2AIE",
        "versao": 1,
        "status": "autorizado",
        "numero": "202XXXXXXX19552",
        "numero_rps": null,
        "serie_rps": null,
        "data_emissao": "2023-02-07T14:35:44-03:00",
        "data_emissao_rps": null,
        "codigo_verificacao": "TSXXXXXIE",
        "valor_servicos": "140.0",
        "documento_prestador": "3238XXXXXX0105",
        "nome_prestador": "TREINAMENTOS LTDA",
        "inscricao_municipal_prestador": "32XX19",
        "nome_municipio": "Itajaí",
        "sigla_uf": "SC",
        "codigo_municipio": "4208203",
        "documento_tomador": "1854XXXXXX0830",
        "url": "LINk DA DANFSE,
        "url_xml": "LINK DO XML"
  }
  }
]
```

### Dados Devolvidos

A API irá devolver os seguintes cabeçalhos HTTP:

* **X-Total-Count**: O número total de registros (incluindo aqueles que não foram devolvidos pelo limite de 100 registros)
* **X-Max-Version**: Valor máximo da versão dos documentos devolvidos. Utilize este cabeçalho para utilizar na próxima busca de versão, caso seja necessário.

Os dados devolvidos podem vir em dois formatos: modo simplificado (default) ou completo.
Se utilizado o formato simplificado o corpo da resposta será um array de objetos em JSON no seguinte formato:

* **chave**: Chave única de identificação da NFSe.
* **versao**: Versão do documento fiscal. Este número irá mudar apenas se o documento for alterado de alguma forma.
* **status**: Status do documento fiscal. Pode ser _autorizado_ ou _cancelado_.
* **numero**: Número da NFSe.
* **numero_rps**: Número do RPS, se existir.
* **serie_rps**: Série do RPS, se existir.
* **data_emissao**: Data de emissão da NFSe.
* **data_emissao_rps**: Data de emissão do RPS, se existir.
* **codigo_verificacao**: Código de verificação da NFSe.
* **valor_servicos**: Valor total dos serviços da NFSe.
* **documento_prestador**: CNPJ ou CPF do emitente do documento fiscal.
* **nome_prestador**: Razão Social ou Nome do emitente do documento fiscal, se existir.
* **inscricao_municipal_prestador**: Inscrição Municipal do emitente do documento fiscal, se existir.
* **nome_municipio**: Nome do município do prestador.
* **sigla_uf**: UF do prestador.
* **codigo_municipio**: Código IBGE do município do prestador.
* **documento_tomador**: CNPJ ou CPF do destinatário do documento fiscal.
* **url**: URL para visualização da NFSe no site da prefeitura.
* **url_xml**: URL para download do XML da NFSe.

Você pode configurar o gatilho `nfse_recebida` para receber estes dados diretamente em sua aplicação, tão logo estejam disponíveis na API. Consulte a seção de [Gatilhos / Webhooks](#gatilhos-webhooks).

> Exemplo dos dados de resposta usando o parâmetro `completa=1`:

```json
[
  {
   "chave": "NFSe3238662000010XXXXXXXXXXXXXXXXXX00000019552-TS25-2AIE",
    "versao": 1,
    "status": "autorizado",
    "numero": "202XXXXXXX19552",
    "serie": "A1",
    "codigo_verificacao": "TSXXXXXIE,
    "data_emissao": "2023-02-07T14:35:44-03:00",
    "numero_rps": null,
    "serie_rps": null,
    "tipo_rps": null,
    "data_emissao_rps": null,
    "natureza_operacao": "501",
    "regime_especial_tributacao": null,
    "optante_simples_nacional": true,
    "incentivador_cultural": true,
    "competencia": "2023-02-01",
    "numero_nfse_substituida": null,
    "outras_informacoes": "- Consulte a autenticidade desta Nota Fiscal no portal da NFS-e de Itajaí: http://nfse.itajai.sc.gov.br/\n- DOCUMENTO FISCAL EMITIDO POR ME OU EPP OPTANTE PELO SIMPLES NACIONAL",
    "informacoes_adicionais_contribuinte": null,
    "prestador": {
        "cpf": null,
        "cnpj": "3238XXXXXX0105",
        "inscricao_municipal": "32XX19",
        "razao_social": "TREINAMENTOS LTDA",
        "nome_fantasia": null,
        "telefone": null,
        "email": null,
        "endereco": {
            "logradouro": "NOME DA RUA",
            "numero": "1",
            "complemento": "SALA",
            "bairro": "NOME DO BAIRRO",
            "codigo_municipio": "4208203",
            "nome_municipio": "Itajaí",
            "uf": "SC",
            "codigo_pais": null,
            "cep": "88307090"
        }
    },
    "tomador": {
        "cpf": null,
        "cnpj": "1854XXXXXX0830",
        "inscricao_municipal": null,
        "razao_social": "MINERAÇÃO E CONSTRUÇÃO S/A",
        "telefone": null,
        "email": "email@TESTE.com.br",
        "endereco": {
            "logradouro": "NOME DA RUA",
            "numero": "01",
            "complemento": "Condominio",
            "bairro": "NOME DO BAIRRO",
            "codigo_municipio": "3140803",
            "nome_municipio": "Matias Barbosa",
            "uf": "MG",
            "codigo_pais": "1058",
            "cep": "36120000"
        }
    },
    "servicos": [
        {
            "valor_servicos": "140.00",
            "valor_deducoes": "0.00",
            "valor_pis": "0.00",
            "valor_cofins": "0.00",
            "valor_inss": "0.00",
            "valor_ir": "0.00",
            "valor_csll": "0.00",
            "iss_retido": false,
            "valor_iss": "0.00",
            "valor_iss_retido": "0.00",
            "outras_retencoes": "0.00",
            "base_calculo": "140.00",
            "aliquota": "0.00",
            "valor_liquido": "140.00",
            "valor_credito": null,
            "desconto_incondicionado": "0.00",
            "desconto_condicionado": "0.00",
            "item_lista_servico": "08.02",
            "codigo_cnae": null,
            "codigo_tributario_municipio": null,
            "discriminacao": "DISCRIMINAÇÃO",
            "codigo_municipio": "4208203",
            "codigo_pais": null,
            "codigo_municipio_incidencia": null,
            "quantidade": "1.00",
            "valor_unitario": "140.00"
        }
    ],
    "url": "LINK DA DANFSE",
    "url_xml": "LINK DO XML"
]
```

Caso utilize o argumento `completa=1`, os dados serão devolvidos em formato estendido, similar ao de emissão de NFSe com algumas melhorias. Para mais detalhes consulte a [Documentação Completa de Campos NFSes Recebidas](https://campos.focusnfe.com.br/nfser/NfseRecebida.html). Informações adicionais na seção [Versões da API](#nfses-recebidas_versoes-da-api).


## Consulta de NFSe Individual

Para obter informações completas referentes a uma NFSe específica, consulte informando a _chave_:

`https://api.focusnfe.com.br/v2/nfses_recebidas/CHAVE`

Utilize o método **HTTP GET** para consultar os dados da nota fiscal.
Isto irá devolver os mesmos campos da nota completa, conforme descritos na [seção anterior](#nfses-recebidas_consulta-de-nfse-recebidas).


## Versões da API

> Exemplo de consulta de NFSe a ser devolvida no formato da versão _beta_ da API:

```shell
curl -u "token obtido no cadastro da empresa:" \
  "https://homologacao.focusnfe.com.br/v2/nfses_recebidas/CHAVE?api_version=2020-11-17"
```

Eventualmente são integradas melhorias nos dados retornados pela API de _NFSes Recebidas_.
Para garantir compatibilidade e facilitar transições, é possível consultar dados de _versões específicas da API_ informando o parâmetro **api_version** (recomendado).

api_version | Padrão¹ | Descrição | Links | Release | <abbr title="End of Life">EOL</abbr>
------------|:-------:|:---------:|:----: | :-----: | :-----:
`2020-11-17` |  | Versão preliminar (beta) | [documentação legada](https://github.com/FocusNFe/api-doc/blob/v2.6.3/source/includes/_nfses-recebidas.md#dados-devolvidos) | 01/12/2020 | 01/10/2024
`2023-03-01` | **x** | Primeira versão estável | [documentação](https://campos.focusnfe.com.br/nfser/NfseRecebida.html), [changelog](https://focusnfe.com.br/blog/changelog-nfser-v2023-03-01/) | 02/10/2023 | -
<small>(¹) Última versão estável da API de _NFSes Recebidas_, assumida como padrão quando não informado o parâmetro.</small>
