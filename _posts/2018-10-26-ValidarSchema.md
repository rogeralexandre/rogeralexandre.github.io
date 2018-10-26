---
layout: post
title: VALIDANDO UM JSON SCHEMA EM C#
---

Vamos fazer um exemplo simples em C# para validar um JSON Schema, para isso usaremos o pacote [NewtonSoft](https://www.newtonsoft.com/jsonschema/help/html/Introduction.htm).

Para isso:
1 criei um projeto console (.Net Core) no visual studio;

2 instalei o pacote Newtonsoft.Json.Schema pelo Package Manager

3 Fiz o seguinte código

```csharp
public static void Validar()
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

            // Um Cliente escrito em JSON
            string ClienteEmJSON = @"{
                                      'nome'      : 'Bianca',
                                      'cpf'       : 123456789,
                                      'telefones' : ['1234-5678', '9012-3456']
                                     }";
            // Fazendo o Parse do Cliente com o Schema.
            JObject cliente = JObject.Parse(ClienteEmJSON);
            // Verificando se é válido.
            bool valido = cliente.IsValid(schema);
            // Retorna TRUE
            Console.WriteLine($"Validação retornou: {valido}");

            // Um exemplo de Cliente inválido
            string ClienteEmJSON_Invalido = @"{
                                                'nome'      : 00,
                                                'cpf'       : 'fsd',
                                                'telefones' : ['1234-5678', 6667]
                                              }";
            JObject clienteInvalido = JObject.Parse(ClienteEmJSON_Invalido);
            valido = clienteInvalido.IsValid(schema);

            // Retorna FALSE
            Console.WriteLine($"Validação retornou: {valido}");
        }
        ```
