---
layout: post
title: VALIDANDO UM JSON SCHEMA EM C# (com classe ValidationError)
---

No pacote da [NewtonSoft](https://www.newtonsoft.com/jsonschema/help/html/Introduction.htm) podemos usar uma forma mais profissional de capturar os erros de validação no nosso objeto JSON.

Você pode encontrar a documentação da classe ValidationError [aqui](https://www.newtonsoft.com/jsonschema/help/html/T_Newtonsoft_Json_Schema_ValidationError.htm).

```csharp
  IList<ValidationError> erros;
  bool valid = clienteInvalido.IsValid(schema, out erros);
```

Note que de novo somente usamos a sobrecarga do método `IsValid`.
Abaixo temos o código completo:

```csharp
public static void ValidarComMaisDetalhes()
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

    // Aqui começa a diferença, o uso da classe ValidationError
    IList<ValidationError> erros;
    bool valid = clienteInvalido.IsValid(schema, out erros);
    foreach (var item in erros)
    {
        Console.WriteLine($"Tipo de erro: {item.ErrorType.ToString()}");
        Console.WriteLine($"Mensagem    : {item.Message}");
        Console.WriteLine($"Linha       : {item.LineNumber}");
        Console.WriteLine($"Caminho     : {item.Path}");
        Console.WriteLine($"Schema      : {item.Schema.ToString()}");
    }
}
```

Dê uma olhada nas mensagens de erro que esse código exibe:

*Erro no atributo nome*

```
Tipo de erro: Type
Mensagem    : Invalid type. Expected String, Null but got Integer.
Linha       : 2
Caminho     : nome
Schema      : {
  "type": [
    "string",
    "null"
  ]
}
```
  
*Erro no atributo CPF*

```
Tipo de erro: Type
Mensagem    : Invalid type. Expected Integer but got String.
Linha       : 3
Caminho     : cpf
Schema      : {
  "type": "integer"
}
```

*Erro no atributo Telefone*

```
Tipo de erro: Type
Mensagem    : Invalid type. Expected String but got Integer.
Linha       : 4
Caminho     : telefones[1]
Schema      : {
  "type": "string"
}
```
