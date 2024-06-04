# NFSe Nacional

Através da API NFSe Nacional é possível:

 * Emitir NFSe utilizando dados simplificados. Este processo é **assíncrono**. Ou seja, após a emissão a nota será enfileirada para processamento.
 * Cancelar NFSe
 * Consultar NFSe’s emitidas

## URLs

Método | URL (recurso) | Ação
-------|---------------|------
POST|/v2/nfsen?ref=REFERENCIA | Cria uma nota fiscal e a envia para processamento.
GET|/v2/nfsen/REFERENCIA | Consulta a nota fiscal com a referência informada e o seu status de processamento
DELETE|/v2/nfsen/REFERENCIA | Cancela uma nota fiscal com a referência informada


## Campos

> Abaixo um exemplo de dados de uma NFSe Nacional:

```json
{
  "data_emissao": "2024-05-07T07:34:56-0300",
  "data_competencia": "2024-05-07",
  "codigo_municipio_emissora": 4106902,

  "cnpj_prestador": "18765499000199",
  "inscricao_municipal_prestador": "12345",
  "codigo_opcao_simples_nacional": 2,
  "regime_especial_tributacao": 0,

  "cnpj_tomador": "07504505000132",
  "razao_social_tomador": "Acras Tecnologia da Informação LTDA",
  "codigo_municipio_tomador": 4106902,
  "cep_tomador": "80045165",
  "logradouro_tomador": "Rua Dias da Rocha Filho",
  "numero_tomador": "999",
  "complemento_tomador": "Prédio 04 - Sala 34C",
  "bairro_tomador": "Alto da XV",
  "telefone_tomador": "41 3256-8060",
  "email_tomador": "contato@focusnfe.com.br",

  "codigo_municipio_prestacao": 4106902,
  "codigo_tributacao_nacional_iss": "010701",
  "descricao_servico": "Nota emitida em caráter de TESTE",
  "valor_servico": 1.00,
  "tributacao_iss": 1,
  "tipo_retencao_iss": 1
}
```

A NFSe Nacional possui vários campos para os mais variados tipos e formas de operações, por isso, criamos uma página exclusiva que mostra todos os campos da nossa API para o envio de NFSe Nacional. Nela, você pode buscar os campos pela TAG XML ou pela nossa tradução para API.

[Documentação completa dos campos NFSe Nacional](https://campos.focusnfe.com.br/nfse_nacional/EmissaoDPSXml.html)

## Status API

Aqui você encontra os status possíveis para NFSe Nacional.

HTTP CODE/STATUS | Status API Focus | Descrição | Correção
---|---|---|---|
404 - not found | nao_encontrado | Nota fiscal não encontrada | Verifique o método utilizado (deve-se usar POST) ou a nota fiscal não foi encontrada.
400 - bad request | nfe_cancelada| Nota fiscal já cancelada| Não é possível realizar a operação solicitada, pois a nota fiscal já foi cancelada.
400 - bad request | nfe_nao_autorizada | Nota fiscal não autorizada não pode ser cancelada | O cancelamento só é possível para NFSe's autorizadas.
400 - bad request | requisicao_invalida | | Sua requisição é inválida porque alguns dos paramêtros básicos não foram cumpridos. Consulte a nossa documentação.
400 - bad request | empresa_nao_habilitada | Emitente ainda não habilitado para emissão de NFSe Nacional | Configure a emissão de NFSe Nacional através do Painel API e tente novamente.
400 - bad request | certificado_vencido | O certificado do emitente está vencido | É necessário renovar ou adquirir um novo certificado digital modelo A1.
422 - unprocessable entity | nfe_autorizada | Nota fiscal já autorizada | A operação solicitada não pode ser realizada, pois a NFSe Nacional já foi autorizada.
422 - unprocessable entity | em_processamento | Nota fiscal em processamento | Sua nota está sendo processada, aguarde alguns minutos antes de consultá-la novamente.

## Envio
```python
# Faça o download e instalação da biblioteca requests, através do python-pip.
import json
import requests

'''
Para ambiente de produção use a variável abaixo:
url = "https://api.focusnfe.com.br"
'''
url = "https://homologacao.focusnfe.com.br/v2/nfsen"

# Substituir pela sua identificação interna da nota
ref = {"ref":"12345"}

token="token obtido no cadastro da empresa"

'''
Usamos dicionarios para armazenar os campos e valores que em seguida,
serao convertidos em JSON e enviados para nossa API
'''
nfse = {}
nfse["data_emissao"] = "2024-05-07T07:34:56-0300"
nfse["data_competencia"] = "2024-05-07"
nfse["codigo_municipio_emissora"] =  "4106902"
nfse["cnpj_prestador"] = "18765499000199"
nfse["inscricao_municipal_prestador"] = "12345"
nfse["codigo_opcao_simples_nacional"] = "2"
nfse["regime_especial_tributacao"] = "0"
nfse["cnpj_tomador"] = "07504505000132"
nfse["razao_social_tomador"] = "Acras Tecnologia da Informação LTDA"
nfse["codigo_municipio_tomador"] = "4106902"
nfse["cep_tomador"] = "80045165"
nfse["logradouro_tomador"] = "Rua Dias da Rocha Filho"
nfse["numero_tomador"] = "999"
nfse["complemento_tomador"] = "Prédio 04 - Sala 34C"
nfse["bairro_tomador"] = "Alto da XV"
nfse["telefone_tomador"] = "41 3256-8060"
nfse["email_tomador"]= "contato@focusnfe.com.br"
nfse["codigo_municipio_prestacao"] = "4106902"
nfse["codigo_tributacao_nacional_iss"] = "010701"
nfse["descricao_servico"] = "Nota emitida em caráter de TESTE"
nfse["valor_servico"] = "1.00"
nfse["tributacao_iss"] = "1"
nfse["tipo_retencao_iss"] = "1"

#print (json.dumps(nfse))
r = requests.post(url, params=ref, data=json.dumps(nfse), auth=(token,""))

# Mostra na tela o codigo HTTP da requisicao e a mensagem de retorno da API
print(r.status_code, r.text)


```

```shell
# arquivo.json deve conter os dados da NFSe Nacional
curl -u "token obtido no cadastro da empresa:" \
  -X POST -T arquivo.json https://homologacao.focusnfe.com.br/v2/nfsen?ref=12345
```

```java
import java.util.HashMap;
import org.codehaus.jettison.json.JSONException;
import org.codehaus.jettison.json.JSONObject;
import com.sun.jersey.api.client.Client;
import com.sun.jersey.api.client.ClientResponse;
import com.sun.jersey.api.client.WebResource;
import com.sun.jersey.api.client.config.ClientConfig;
import com.sun.jersey.api.client.config.DefaultClientConfig;
import com.sun.jersey.api.client.filter.HTTPBasicAuthFilter;

public class NFSeAutorizar {

  public static void main(String[] args) throws JSONException{

    String login = "Token_obtido_no_cadastro_da_empresa";

    /* Substituir pela sua identificação interna da nota. */
    String ref = "12345";

    /* Para ambiente de produção use a variável abaixo:
    String server = "https://api.focusnfe.com.br/"; */
    String server = "https://homologacao.focusnfe.com.br/";

    String url = server.concat("v2/nfsen?ref="+ref);

    /* Configuração para realizar o HTTP BasicAuth. */
    Object config = new DefaultClientConfig();
    Client client = Client.create((ClientConfig) config);
    client.addFilter(new HTTPBasicAuthFilter(login, ""));

    /* Aqui é criado o hash que recebera os dados da nota. */
    HashMap<String, String> nfse = new HashMap<String, String>();

    nfse.put("data_emissao", "2024-05-07T07:34:56-0300");
    nfse.put("data_competencia", "2024-05-07");
    nfse.put("codigo_municipio_emissora", "4106902");
    nfse.put("cnpj_prestador", "18765499000199");
    nfse.put("inscricao_municipal_prestador", "12345");
    nfse.put("codigo_opcao_simples_nacional", "2");
    nfse.put("regime_especial_tributacao", "0");
    nfse.put("cnpj_tomador", "07504505000132");
    nfse.put("razao_social_tomador", "Acras Tecnologia da Informação LTDA");
    nfse.put("codigo_municipio_tomador", "4106902");
    nfse.put("cep_tomador", "80045165");
    nfse.put("logradouro_tomador", "Rua Dias da Rocha Filho");
    nfse.put("numero_tomador", "999");
    nfse.put("complemento_tomador", "Prédio 04 - Sala 34C");
    nfse.put("bairro_tomador", "Alto da XV");
    nfse.put("telefone_tomador", "41 3256-8060");
    nfse.put("email_tomador", "contato@focusnfe.com.br");
    nfse.put("codigo_municipio_prestacao", "4106902");
    nfse.put("codigo_tributacao_nacional_iss", "010701");
    nfse.put("descricao_servico", "Nota emitida em caráter de TESTE");
    nfse.put("valor_servico", "1.00");
    nfse.put("tributacao_iss", "1");
    nfse.put("tipo_retencao_iss", "1");  

    /* Depois de fazer o input dos dados, é criado o objeto JSON já com os valores da hash. */
    JSONObject json = new JSONObject (nfse);

    /* É recomendado verificar como os dados foram gerados em JSON e se ele está seguindo a estrutura especificada em nossa documentação.
    System.out.print(json); */

    WebResource request = client.resource(url);

    ClientResponse resposta = request.post(ClientResponse.class, json);

    int httpCode = resposta.getStatus();

    String body = resposta.getEntity(String.class);

    /* As três linhas a seguir exibem as informações retornadas pela nossa API.
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
require "json"

# token enviado pelo suporte
token = "codigo_alfanumerico_token"

# referência da nota - deve ser única para cada nota enviada
ref = "id_referencia_nota"

# endereço da api que deve ser usado conforme o ambiente: produção ou homologação
servidor_producao = "https://api.focusnfe.com.br/"
servidor_homologacao = "https://homologacao.focusnfe.com.br/"

# no caso do ambiente de envio ser em produção, utilizar servidor_producao
url_envio = servidor_homologacao + "v2/nfsen?ref=" + ref

# altere os campos conforme a nota que será enviada
dados_da_nota = {
  data_emissao: "2024-05-07T07:34:56-0300",
  data_competencia: "2024-05-07",
  codigo_municipio_emissora: "4106902",

  cnpj_prestador: "18765499000199",
  inscricao_municipal_prestador: "12345",
  codigo_opcao_simples_nacional: "2",
  regime_especial_tributacao: "0",

  cnpj_tomador: "07504505000132",
  razao_social_tomador: "Acras Tecnologia da Informação LTDA",
  codigo_municipio_tomador: "4106902",
  cep_tomador: "80045165",
  logradouro_tomador: "Rua Dias da Rocha Filho",
  numero_tomador: "999",
  complemento_tomador: "Prédio 04 - Sala 34C",
  bairro_tomador: "Alto da XV",
  telefone_tomador: "41 3256-8060",
  email_tomador: "contato@focusnfe.com.br",

  codigo_municipio_prestacao: "4106902",
  codigo_tributacao_nacional_iss: "010701",
  descricao_servico: "Nota emitida em caráter de TESTE",
  valor_servico: "1.00",
  tributacao_iss: "1",
  tipo_retencao_iss: "1"
}

# criamos um objeto uri para envio da nota
uri = URI(url_envio)

# também criamos um objeto da classe HTTP a partir do host da uri
http = Net::HTTP.new(uri.hostname, uri.port)

# aqui criamos um objeto da classe Post a partir da uri de requisição
requisicao = Net::HTTP::Post.new(uri.request_uri)

# adicionando o token à requisição
requisicao.basic_auth(token, '')

# convertemos os dados da nota para o formato JSON e adicionamos ao corpo da requisição
requisicao.body = dados_da_nota.to_json

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
 // Você deve definir isso globalmente para sua aplicação
 // Para ambiente de produção use a variável abaixo:
 // $server = "https://api.focusnfe.com.br";
 $server = "https://homologacao.focusnfe.com.br";
 // Substituir pela sua identificação interna da nota
 $ref = "12345";
 $login = "token obtido no cadastro da empresa";
 $password = "";
 $nfse = array (
    "data_emissao" => "2024-05-07T07:34:56-0300",
    "data_competencia" => "2024-05-07",
    "codigo_municipio_emissora" => "4106902",

    "cnpj_prestador" => "18765499000199",
    "inscricao_municipal_prestador" => "12345",
    "codigo_opcao_simples_nacional" => "2",
    "regime_especial_tributacao" => "0",

    "cnpj_tomador" => "07504505000132",
    "razao_social_tomador" => "Acras Tecnologia da Informação LTDA",
    "codigo_municipio_tomador" => "4106902",
    "cep_tomador" => "80045165",
    "logradouro_tomador" => "Rua Dias da Rocha Filho",
    "numero_tomador" => "999",
    "complemento_tomador" => "Prédio 04 - Sala 34C",
    "bairro_tomador" => "Alto da XV",
    "telefone_tomador" => "41 3256-8060",
    "email_tomador" => "contato@focusnfe.com.br",

    "codigo_municipio_prestacao" => "4106902",
    "codigo_tributacao_nacional_iss" => "010701",
    "descricao_servico" => "Nota emitida em caráter de TESTE",
    "valor_servico" => "1.00",
    "tributacao_iss" => "1",
    "tipo_retencao_iss" => "1"
  );
 // Inicia o processo de envio das informações usando o cURL
 $ch = curl_init();
 curl_setopt($ch, CURLOPT_URL, $server."/v2/nfsen?ref=" . $ref);
 curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
 curl_setopt($ch, CURLOPT_POST, 1);
 curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($nfse));
 curl_setopt($ch, CURLOPT_HTTPAUTH, CURLAUTH_BASIC);
 curl_setopt($ch, CURLOPT_USERPWD, "$login:$password");
 $body = curl_exec($ch);
 $http_code = curl_getinfo($ch, CURLINFO_HTTP_CODE);
 //as três linhas abaixo imprimem as informações retornadas pela API, aqui o seu sistema deverá
 //interpretar e lidar com o retorno
 print($http_code."\n");
 print($body."\n\n");
 print("");
 curl_close($ch);
 ?>
```

```javascript

/*
As orientacoes a seguir foram extraidas do site do NPMJS: https://www.npmjs.com/package/xmlhttprequest
Here's how to include the module in your project and use as the browser-based XHR object.
Note: use the lowercase string "xmlhttprequest" in your require(). On case-sensitive systems (eg Linux) using uppercase letters won't work.
*/
var XMLHttpRequest = require("xmlhttprequest").XMLHttpRequest;

var request = new XMLHttpRequest();

var token = "Token_obtido_no_cadastro_da_empresa";

// Substituir pela sua identificação interna da nota
var ref = "12345";

/*
Para ambiente de producao use a URL abaixo:
"https://api.focusnfe.com.br"
*/
var url = "https://homologacao.focusnfe.com.br/v2/nfsen?ref=" + ref;

/*
Use o valor 'false', como terceiro parametro para que a requisicao aguarde a resposta da API
Passamos o token como quarto parametro deste metodo, como autenticador do HTTP Basic Authentication.
*/
request.open('POST', url, false, token);

var nfse = {
  "data_emissao":"2024-05-07T07:34:56-0300",
  "data_competencia":"2024-05-07",
  "codigo_municipio_emissora":"4106902",

  "cnpj_prestador":"18765499000199",
  "inscricao_municipal_prestador":"12345",
  "codigo_opcao_simples_nacional":"2",
  "regime_especial_tributacao":"0",

  "cnpj_tomador": "07504505000132",
  "razao_social_tomador": "Acras Tecnologia da Informação LTDA",
  "codigo_municipio_tomador":"4106902",
  "cep_tomador": "80045165",
  "logradouro_tomador": "Rua Dias da Rocha Filho",
  "numero_tomador": "999",
  "complemento_tomador": "Prédio 04 - Sala 34C",
  "bairro_tomador": "Alto da XV",
  "telefone_tomador": "41 3256-8060",
  "email_tomador": "contato@focusnfe.com.br",

  "codigo_municipio_prestacao":"4106902",
  "codigo_tributacao_nacional_iss": "010701",
  "descricao_servico": "Nota emitida em caráter de TESTE",
  "valor_servico":"1.00",
  "tributacao_iss":"1",
  "tipo_retencao_iss":"1"
};

// Aqui fazermos a serializacao do JSON com os dados da nota e enviamos atraves do metodo usado.
request.send(JSON.stringify(nfse));

// Sua aplicacao tera que ser capaz de tratar as respostas da API.
console.log("HTTP code: " + request.status);
console.log("Corpo: " + request.responseText);

```


> Exemplos de respostas da API por **status** para a requisição de envio:

> **processando_autorizacao** (requisição enviada com sucesso para API)

```json
{
  "cnpj_prestador": "CNPJ_PRESTADOR",
  "ref": "REFERENCIA",
  "status": "processando_autorizacao"
}
```

> **requisicao_invalida** (requisição com campos faltantes/erro de estrutura no JSON)

```json
{
  "codigo": "requisicao_invalida",
  "mensagem": "Parâmetro \"codigo_municipio_emissora\" não informado"
}
```

Para enviar uma NFSe Nacional utilize a URL abaixo, alterando o ambiente de produção para homologação, caso esteja emitindo notas de teste.

Envia uma NFSe Nacional para autorização:

`https://api.focusnfe.com.br/v2/nfsen?ref=REFERENCIA`

Utilize o comando **HTTP POST** para enviar a sua nota para nossa API.

Nesta etapa, é feita uma primeira validação dos dados da nota. Caso ocorra algum problema, por exemplo, algum campo faltante, formato incorreto
ou algum problema com o prestador, a nota **não será aceita para processamento** e será devolvida a mensagem de erro apropriada. Veja a seção [erros](#introducao_erros).

Caso a nota seja validada corretamente, a nota será **aceita para processamento**. Isto significa que a nota irá para uma fila de processamento
onde eventualmente será processada (processamento assíncrono). Com isto, a nota poderá ser autorizada ou ocorrer um erro na autorização de acordo com a validação do ambiente nacional.

Para verificar se a nota já foi autorizada, você terá que efetuar uma [consulta](#nfse-nacional_consulta) ou se utilizar de [gatilhos / webhooks](#gatilhos-webhooks).

## Consulta
```python
# Faça o download e instalação da biblioteca requests, através do python-pip.
import requests

'''
Para ambiente de produção use a variável abaixo:
url = "https://api.focusnfe.com.br"
'''
url = "https://homologacao.focusnfe.com.br/v2/nfsen/"

# Substituir pela sua identificação interna da nota
ref = "12345"

token="token obtido no cadastro da empresa"

r = requests.get(url+ref, params=completa, auth=(token,""))

# Mostra na tela o codigo HTTP da requisicao e a mensagem de retorno da API
print(r.status_code, r.text)


```

```shell
curl -u "token obtido no cadastro da empresa:" \
  https://homologacao.focusnfe.com.br/v2/nfsen/12345
```

```java
import com.sun.jersey.api.client.Client;
import com.sun.jersey.api.client.ClientResponse;
import com.sun.jersey.api.client.WebResource;
import com.sun.jersey.api.client.config.ClientConfig;
import com.sun.jersey.api.client.config.DefaultClientConfig;
import com.sun.jersey.api.client.filter.HTTPBasicAuthFilter;

public class NFSeConsulta {

  public static void main(String[] args){

    String login = "Token_obtido_no_cadastro_da_empresa";

    /* Substituir pela sua identificação interna da nota. */
    String ref = "12345";

    /* Para ambiente de produção use a variável abaixo:
    String server = "https://api.focusnfe.com.br/"; */
    String server = "https://homologacao.focusnfe.com.br/";

    String url = server.concat("v2/nfsen/"+ref);

    /* Configuração para realizar o HTTP BasicAuth. */
    Object config = new DefaultClientConfig();
    Client client = Client.create((ClientConfig) config);
    client.addFilter(new HTTPBasicAuthFilter(login, ""));

    WebResource request = client.resource(url);

    ClientResponse resposta = (ClientResponse) request.get(ClientResponse.class);

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

# referência da nota - deve ser única para cada nota enviada
ref = "id_referencia_nota"

# endereço da api que deve ser usado conforme o ambiente: produção ou homologação
servidor_producao = "https://api.focusnfe.com.br/"
servidor_homologacao = "https://homologacao.focusnfe.com.br/"

# no caso do ambiente de envio ser em produção, utilizar servidor_producao
url_envio = servidor_homologacao + "v2/nfsen/" + ref

# criamos um objeto uri para envio da nota
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
 // Você deve definir isso globalmente para sua aplicação
 //Substituir pela sua identificação interna da nota
 $ref = "12345";
 $login = "token obtido no cadastro da empresa";
 $password = "";
 // Para ambiente de produção use a variável abaixo:
 // $server = "https://api.focusnfe.com.br";
 $server = "https://homologacao.focusnfe.com.br"; // Servidor de homologação
 $ch = curl_init();
 curl_setopt($ch, CURLOPT_URL, $server."/v2/nfsen/" . $ref);
 curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
 curl_setopt($ch, CURLOPT_HTTPHEADER, array());
 curl_setopt($ch, CURLOPT_HTTPAUTH, CURLAUTH_BASIC);
 curl_setopt($ch, CURLOPT_USERPWD, "$login:$password");
 $body = curl_exec($ch);
 $http_code = curl_getinfo($ch, CURLINFO_HTTP_CODE);
 //as três linhas abaixo imprimem as informações retornadas pela API, aqui o seu sistema deverá
 //interpretar e lidar com o retorno
 print($http_code."\n");
 print($body."\n\n");
 print("");
 curl_close($ch);
 ?>
```

```javascript

/*
As orientacoes a seguir foram extraidas do site do NPMJS: https://www.npmjs.com/package/xmlhttprequest
Here's how to include the module in your project and use as the browser-based XHR object.
Note: use the lowercase string "xmlhttprequest" in your require(). On case-sensitive systems (eg Linux) using uppercase letters won't work.
*/
var XMLHttpRequest = require("xmlhttprequest").XMLHttpRequest;

var request = new XMLHttpRequest();

var token = "Token_obtido_no_cadastro_da_empresa";

// Substituir pela sua identificação interna da nota
var ref = "12345";

/*
Para ambiente de producao use a URL abaixo:
"https://api.focusnfe.com.br"
*/
var url = "https://homologacao.focusnfe.com.br/v2/nfsen/" + ref + "?completa=0";

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

> Exemplos de respostas da API por **status** para a requisição de consulta:

> **autorizado**

```json
{                            
  "cnpj_prestador": "18765499000199", 
  "ref": "12345",  
  "numero_rps": "123",          
  "serie_rps": "1",            
  "tipo_rps": "1",             
  "status": "autorizado",     
  "numero": "1245",             
  "codigo_verificacao": "12345678901234567890123456789012345678901234567890",
  "data_emissao": "2024-05-07T07:34:56-03:00",
  "url": "https://www.nfse.gov.br/consultapublica/?tpc=1&chave=12345678901234567890123456789012345678901234567890",
  "caminho_xml_nota_fiscal": "/arquivos/18765499000199_166/202405/XMLsNFSe/187654990001994106902-14018919393-43-12345678901234567890123456789012345678901234567890-nfse.xml",                                      
  "url_danfse": "https://focusnfe.s3.sa-east-1.amazonaws.com/arquivos/18765499000199_166/202405/DANFSEs/NFSe187654990001994106902-14018919393-43-12345678901234567890123456789012345678901234567890.pdf"           
}
```

> **cancelado**

```json
{                            
  "cnpj_prestador": "18765499000199", 
  "ref": "12345",  
  "numero_rps": "123",          
  "serie_rps": "1",            
  "tipo_rps": "1",             
  "status": "cancelado",     
  "numero": "1245",             
  "codigo_verificacao": "12345678901234567890123456789012345678901234567890",
  "data_emissao": "2024-05-07T07:34:56-03:00",
  "url": "https://www.nfse.gov.br/consultapublica/?tpc=1&chave=12345678901234567890123456789012345678901234567890",
  "caminho_xml_nota_fiscal": "/arquivos/18765499000199_166/202405/XMLsNFSe/187654990001994106902-14018919393-43-12345678901234567890123456789012345678901234567890-nfse.xml",
  "caminho_xml_cancelamento": "/arquivos/18765499000199_166/202405/XMLsNFSe/NFS12345678901234567890123456789012345678901234567890-can.xml",                                      
  "url_danfse": "https://focusnfe.s3.sa-east-1.amazonaws.com/arquivos/18765499000199_166/202405/DANFSEs/NFSe187654990001994106902-14018919393-43-12345678901234567890123456789012345678901234567890.pdf"           
}
```

> **erro_autorizacao**

```json
{
  "cnpj_prestador": "18765499000199",
  "ref": "12345",
  "numero_rps": "123",
  "serie_rps": "1",
  "status": "erro_autorizacao",
  "erros": [
    {
      "codigo": "E0014",
      "mensagem": "Conjunto de Série, Número, Código do Município Emissor e CNPJ/CPF informado nesta DPS já existe em uma NFS-e gerada a partir de uma DPS enviada anteriormente.",
      "correcao": null
    }
  ]
}
```

> **processando_autorizacao**

```json
{
  "cnpj_prestador": "18765499000199",
  "ref": "12345",
  "numero_rps": "123",
  "serie_rps": "1",
  "tipo_rps": "1",
  "status": "processando_autorizacao"
}
```

Após emitir uma nota, você poderá usar a operação de consulta para verificar se a nota já foi aceita para processamento, se está
ainda em processamento ou se a nota já foi processada.

Para consultar uma NFSe Nacional utilize a URL abaixo, alterando o ambiente de produção para homologação, caso esteja emitindo notas de teste.

Recupera informações sobre a NFSe Nacional:

`https://api.focusnfe.com.br/v2/nfsen/REFERENCIA`

Utilize o comando **HTTP GET** para consultar a sua nota para nossa API.


* **status:** Indica a etapa do processamento interno da nota fiscal (API Focus NFe e/ou Ambiente Nacional), podendo ser:
   * **autorizado:** A NFSe Nacional foi autorizada com sucesso, neste caso, é fornecido os caminhos para acessar a DANFSe e XML.
   * **cancelado:** Indica que a operação de cancelamento do documento foi realizada com sucesso.
   * **erro_autorizacao:** Houve algum erro durante a emissão da NFSe Nacional. A mensagem de erro você encontrará dentro do campo "erros". É possível reenviar a nota com a mesma referência após realizar as correções indicadas.
   * **processando_autorizacao:** A NFSe Nacional está sendo processada internamente (API Focus NFe) e/ou pelo Ambiente Nacional, consulte após alguns minutos.
* **cnpj_prestador:** O CNPJ emitente da nota fiscal (conhecido também como "prestador do serviço").
* **ref:** Essa é a referência usada na sua requisição.
* **numero_rps:** Número do RPS de controle do Ambiente Nacional.
* **serie_rps:** A série do RPS de controle do Ambiente Nacional.
* **tipo_rps:** O tipo do RPS.
* **erros:** Quando ocorrerem erros na emissão, será aqui que mostraremos a orientação do Ambiente Nacional.
* **url:** URL para a consulta da nota fiscal no portal do Ambiente Nacional.
* **url_danfse:** URL para acesso e download do DANFSe (versão PDF).
* **data_emissao:** Data da emissão da nota fiscal.
* **caminho_xml_nota_fiscal:** Caminho para acesso e download do XML da nota fiscal.
* **codigo_verificacao:** Código de verificação para consulta da NFSe Nacional, pode ser usado no portal do Ambiente Nacional para consulta.

### Download do XML e consulta do documento auxiliar da NFSe Nacional

Após a autorização da nota fiscal de serviço eletrônica será disponibilizado os campos:

*  **caminho_xml_nota_fiscal** - Representa o caminho para montar a URL para download do XML. Por exemplo, se você utilizou o servidor api.focusnfe.com.br e o caminho_xml_nota_fiscal contém o caminho "/notas_fiscais_servico/NFSe075045050001324106902-004940940-428-DUMMY.xml" você poderá acessar o XML pela URL completa https://api.focusnfe.com.br/notas_fiscais_servico/NFSe075045050001324106902-004940940-428-DUMMY.xml
* **url**. A URL para consultar a NFSe direto no portal do Ambiente Nacional.

Utilize o método **HTTP GET** para ambas as consultas.

## Cancelamento
```python
# Faça o download e instalação da biblioteca requests, através do python-pip.
import json
import requests

'''
Para ambiente de produção use a variável abaixo:
url = "https://api.focusnfe.com.br"
'''
url = "https://homologacao.focusnfe.com.br/v2/nfsen/"

# Substituir pela sua identificação interna da nota
ref = "12345"

token="token obtido no cadastro da empresa"

'''
Usamos um dicionario para armazenar os campos e valores que em seguida,
serao convertidos a JSON e enviados para nossa API
'''
justificativa={}
justificativa["justificativa"] = "Sua justificativa aqui!"

r = requests.delete(url+ref, data=json.dumps(justificativa), auth=(token,""))

# Mostra na tela o codigo HTTP da requisicao e a mensagem de retorno da API
print(r.status_code, r.text)


```

```shell
curl -u "token obtido no cadastro da empresa:" \
  -X DELETE -d '{"justificativa":"Teste de cancelamento de nota"}' \
  https://homologacao.focusnfe.com.br/v2/nfsen/12345
```

```java
import java.util.HashMap;
import org.codehaus.jettison.json.JSONObject;
import com.sun.jersey.api.client.Client;
import com.sun.jersey.api.client.ClientResponse;
import com.sun.jersey.api.client.WebResource;
import com.sun.jersey.api.client.config.ClientConfig;
import com.sun.jersey.api.client.config.DefaultClientConfig;
import com.sun.jersey.api.client.filter.HTTPBasicAuthFilter;

public class NFSeCancelamento {

  public static void main(String[] args){

    String login = "Token_obtido_no_cadastro_da_empresa";

    /* Substituir pela sua identificação interna da nota. */
    String ref = "12345";

    /* Para ambiente de produção use a variável abaixo:
    String server = "https://api.focusnfe.com.br/"; */
    String server = "https://homologacao.focusnfe.com.br/";

    String url = server.concat("v2/nfsen/"+ref);

    /* Aqui criamos um hashmap para receber a chave "justificativa" e o valor desejado. */
    HashMap<String, String> justificativa = new HashMap<String, String>();
    justificativa.put("justificativa", "Informe aqui a sua justificativa para realizar o cancelamento da NFSe Nacional.");

    /* Criamos um objeto JSON para receber a hash com os dados esperado pela API. */
    JSONObject json = new JSONObject(justificativa);

    /* Configuração para realizar o HTTP BasicAuth. */
    Object config = new DefaultClientConfig();
    Client client = Client.create((ClientConfig) config);
    client.addFilter(new HTTPBasicAuthFilter(login, ""));

    WebResource request = client.resource(url);

    ClientResponse resposta = request.delete(ClientResponse.class, json);

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
require "json"

# token enviado pelo suporte
token = "codigo_alfanumerico_token"

# referência da nota - deve ser única para cada nota enviada
ref = "id_referencia_nota"

# endereço da api que deve ser usado conforme o ambiente: produção ou homologação
servidor_producao = "https://api.focusnfe.com.br/"
servidor_homologacao = "https://homologacao.focusnfe.com.br/"

# no caso do ambiente de envio ser em produção, utilizar servidor_producao
url_envio = servidor_homologacao + "v2/nfsen/" + ref

# altere os campos conforme a nota que será enviada
justificativa_cancelamento = {
  justificativa: "Informe aqui a sua justificativa para realizar o cancelamento da NFSe."
}

# criamos um objeto uri para envio da nota
uri = URI(url_envio)

# também criamos um objeto da classe HTTP a partir do host da uri
http = Net::HTTP.new(uri.hostname, uri.port)

# aqui criamos um objeto da classe Delete a partir da uri de requisição
requisicao = Net::HTTP::Delete.new(uri.request_uri)

# adicionando o token à requisição
requisicao.basic_auth(token, '')

# convertemos a hash de justificativa do cancelamento para o formato JSON e adicionamos ao corpo da requisição
requisicao.body = justificativa_cancelamento.to_json

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
 // Você deve definir isso globalmente para sua aplicação
 $ch = curl_init();
 // Substituir pela sua identificação interna da nota
 $ref   = "12345";
 // Para ambiente de produção use a variável abaixo:
 // $server = "https://api.focusnfe.com.br";
 $server = "https://homologacao.focusnfe.com.br";
 $justificativa = array ("justificativa" => "Teste de cancelamento de nota");
 $login = "token obtido no cadastro da empresa";
 $password = "";
 curl_setopt($ch, CURLOPT_URL, $server . "/v2/nfsen/" . $ref);
 curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
 curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
 curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($justificativa));
 curl_setopt($ch, CURLOPT_HTTPAUTH, CURLAUTH_BASIC);
 curl_setopt($ch, CURLOPT_USERPWD, "$login:$password");
 $body = curl_exec($ch);
 $result = curl_getinfo($ch, CURLINFO_HTTP_CODE);
 //as três linhas abaixo imprimem as informações retornadas pela API, aqui o seu sistema deverá
 //interpretar e lidar com o retorno
 print($result."\n");
 print($body."\n\n");
 print("");
 curl_close($ch);
 ?>
```

```javascript

/*
As orientacoes a seguir foram extraidas do site do NPMJS: https://www.npmjs.com/package/xmlhttprequest
Here's how to include the module in your project and use as the browser-based XHR object.
Note: use the lowercase string "xmlhttprequest" in your require(). On case-sensitive systems (eg Linux) using uppercase letters won't work.
*/
var XMLHttpRequest = require("xmlhttprequest").XMLHttpRequest;

var request = new XMLHttpRequest();

var token = "Token_obtido_no_cadastro_da_empresa";

// Substituir pela sua identificação interna da nota.
var ref = "12345";

/*
Para ambiente de producao use a URL abaixo:
"https://api.focusnfe.com.br"
*/
var url = "https://homologacao.focusnfe.com.br/v2/nfsen/"+ ref;

/*
Use o valor 'false', como terceiro parametro para que a requisicao aguarde a resposta da API
Passamos o token como quarto parametro deste metodo, como autenticador do HTTP Basic Authentication.
*/
request.open('DELETE', url, false, token);

var cancelar = {

  "justificativa": "Sua justificativa aqui!"
};

// Aqui fazermos a serializacao do JSON com os dados da nota e enviamos atraves do metodo usado.
request.send(JSON.stringify(cancelar));

// Sua aplicacao tera que ser capaz de tratar as respostas da API.
console.log("HTTP code: " + request.status);
console.log("Corpo: " + request.responseText);

```

> Exemplos de respostas da API por **status** para a requisição de cancelamento:

> **cancelado** (requisição realizada com sucesso)

```json
{
  "status": "cancelado"
}
```

> **erro_cancelamento** (requisição com erro)

```json
{
  "status": "erro_cancelamento",
  "erros": [
    {
      "codigo": "E523",
      "mensagem": "nota que você está tentando cancelar está fora do prazo permitido para cancelamento",
      "correcao": null
    }
  ]
}
```

> **nfe_cancelada** (quando a nota já consta como cancelada)

```json
{
  "codigo": "nfe_cancelada",
  "mensagem": "Nota Fiscal já cancelada"
}
```


Para cancelar uma NFSe Nacional, basta fazer uma requisição à URL abaixo, alterando o ambiente de produção para homologação, caso esteja emitindo notas de teste.

**Cancelar uma NFSe Nacional já autorizada:**

`https://api.focusnfe.com.br/v2/nfsen/REFERENCIA`

Utilize o comando **HTTP DELETE** para cancelar a sua nota para nossa API.
Este método é síncrono, ou seja, a comunicação com o Ambiente Nacional será feito imediatamente e devolvida a resposta na mesma requisição.

O parâmetro de cancelamento deverá ser enviado da seguinte forma:

* **justificativa**: Justificativa do cancelamento. Deverá conter de 15 a 255 caracteres.

A API irá em seguida devolver os seguintes campos:

* **status**: cancelado, se a nota pode ser cancelada, ou erro_cancelamento, se houve algum erro ao cancelar a nota.
* **erros**: um array de mensagens de erro que impedem que a nota seja cancelada .
