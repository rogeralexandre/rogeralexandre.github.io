---
layout: post
title: VALIDANDO UM JSON SCHEMA EM C# (com mensagens de erro)
---

Agora vamos usar um outro pequeno recurso que é obter as mensagens de erro de validação. Continuamos usando o pacote da [NewtonSoft](https://www.newtonsoft.com/jsonschema/help/html/Introduction.htm).

Neste exemplo 90% do código é o mesmo do post anterior (o caso de erro). Acontece que neste eu adicionei o seguinte trecho de código:

```csharp
  IList<string> mensagens;
  bool valid = clienteInvalido.IsValid(schema, out mensagens);
  Console.WriteLine("Mensagens de erro da validação do JSON Object.");
  foreach (var item in mensagens)
  {
    Console.WriteLine(item.ToString());
  }
```

Note que utilizamos a sobrecarga do método `IsValid`, passei como parâmetro de saída uma lista para poder receber as mensagens de erro retornadas pelo método.
O código completo está abaixo:

```csharp
public static void ValidarComDetalhes()
{
    // JSON Schema
    // String com um SCHEMA em JSON
    string schemaJSON = @"{
                           'description': 'Cliente',
                           'type'       : 'object',
                           'properties' : {
                                           'nome'      : {'type':['string','null']},
                                           'cpf'       : {'type': 'integer' },
                                           'telefones' : {
                                                          'type'  : 'array',
                                                          'items' : {'type' : 'string'},
                                                          'format': 'phone' 
                                                         }
                                          }
                          }";
    // Criando o objeto de parse.
    JSchema schema = JSchema.Parse(schemaJSON);

    // Um exemplo de Cliente inválido
    string ClienteEmJSON_Invalido = @"{
                                        'nome'      : 00,
                                        'cpf'       : 'fsd',
                                        'telefones' : ['1234-5678', 6667]
                                      }";
    JObject clienteInvalido = JObject.Parse(ClienteEmJSON_Invalido);

    IList<string> mensagens;
    bool valid = clienteInvalido.IsValid(schema, out mensagens);

    Console.WriteLine("Mensagens de erro da validação do JSON Object.");
    foreach (var item in mensagens)
    {
        Console.WriteLine(item.ToString());
    }
}
```

O resultado em tela ficou assim:
