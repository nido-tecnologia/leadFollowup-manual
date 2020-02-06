# Manual de integração

## Itens necessários
- \_\_ENDPOINT_CLIENTE__
   - Ex: http://sistemadocliente.nidoimovel.com.br/index.php/api/leadFollowup
- \_\_TOKEN_CLIENTE__
   - Token JWT, Ex: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC9uaWRvLmNvbS5iciIsImF1ZCI6Imh0dHA6XC9cL25pZG9pbW92ZWwuY29tLmJyIiwiaWF0IjoxNTQ2MzA4MDAwLCJuYmYiOjE1NDYzMDgwMDAsImRhdGEiOnsiY2xpZW50ZV9pZCI6IjMxIiwiY29kYWdlbmNpYSI6Ik5JIiwid2ViX2NvbmZpZ19pZCI6IjgyIn19.VARfNYjb9yIY7pB01HcUJipMC1HEnaG028307Elfz1s

## Autenticação
Sistema de autenticação com [Bearer Token](https://tools.ietf.org/html/rfc6750) e validação de User-Agent.<br />
Lembrar se sempre colocar na requisição os cabeçalhos:
- `User-Agent: Nido/1.0.0 Decussi/1.0.0`
- `Authorization: Bearer __TOKEN_CLIENTE__`

## Campos do JSON
- **fac_id**, identificador da FAC
- **fac_referencia**, referência da FAC apenas para validação
- **tipo**, tipo de follow-up
- **mensagem**, mensagem do follow-up

## Tipos de follow-up
|Tipo|Descrição|
|----|---------|
|email|E-mail|
|atualizacao|Atualização|
|reclamacao|Reclamação|
|mensagem_ao_corretor|Mensagem ao Corretor|
|whatsapp|Whatsapp|
|outro|Qualquer outro tipo|

## Retornos da aplicação
|Status|Descrição|
|-:|-|
|201|Follow-up inserido com sucesso|
|40x|Problemas na validação dos dados enviados|
|50x|Problema de Token ou genérico|

`Todos os erros informados retornam um descritivo explicando o problema.`

# Exemplos de retornos
```json
{
  "status": 201,
  "return": {
    "model": {
      "id": 17754,
      "fac_id": 133,
      "codagencia": "FL",
      "codfac": 109,
      "codprofagencia": "NI",
      "codprofequipe": 1,
      "codprofissional": "NIDO",
      "datahora": "06\/02\/2020 16:54:16",
      "codfacandamento": 99,
      "historico": "Ola este sera o novo followup"
    }
  }
}
```

```json
{
  "status": 400,
  "return": {
    "errorMessage": "Error",
    "errorDetail": "Invalid 'tipo', accepted values: 'email', 'atualizacao', 'reclamacao', 'mensagem_ao_corretor', 'whatsapp', 'outro'",
    "errorTrace": null
  }
}
```

```json
{
  "status": 500,
  "return": {
    "errorMessage": "Invalid Access Token",
    "errorDetail": "Refused token",
    "errorTrace": null
  }
}<
```

# Exemplos de Consumo
## PHP
```php
$request = new HttpRequest();
$request->setUrl('__ENDPOINT_CLIENTE__');
$request->setMethod(HTTP_METH_POST);

$body = json_encode(array(
    "fac_id"  => "133",
    "fac_referencia" => "FL109",
    "tipo" => "atualizacao",
    "mensagem" => "O cliente está interessado em outro imóvel."
));

$request->setHeaders(array(
  'Content-Type' => 'application/json',
  'User-Agent' => 'Nido/1.0.0 Decussi/1.0.0',
  'Authorization' => 'Bearer __TOKEN_CLIENTE__'
));

$request->setBody($body);

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}
```

## C#
```C#
var client = new RestClient("__ENDPOINT_CLIENTE__");
var request = new RestRequest(Method.POST);

request.AddHeader("Content-Type", "application/json");
request.AddHeader("User-Agent", "Nido/1.0.0 Decussi/1.0.0");
request.AddHeader("Authorization", "Bearer __TOKEN_CLIENTE__");

var body = new {
    "fac_id" = "133",
    "fac_referencia" = "FL109",
    "tipo" = "atualizacao",
    "mensagem" = "O cliente está interessado em outro imóvel."
};

request.AddParameter("undefined", JsonConvert.SerializeObject(body), ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```