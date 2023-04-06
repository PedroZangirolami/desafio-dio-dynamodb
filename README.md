# Desafio DIO Boas práticas com DynamoDB

### Serviço utilizado
  - Amazon DynamoDB
  - Amazon CLI para execução em linha de comando

### Comandos executados para criação da tabela e consultas 

- Criar uma tabela para cadastrar jogadores da NBA

```
aws dynamodb create-table ^
    --table-name Jogador ^
    --attribute-definitions ^
        AttributeName=Nome,AttributeType=S ^
        AttributeName=Posicao,AttributeType=S ^
    --key-schema ^
        AttributeName=Nome,KeyType=HASH ^
        AttributeName=Posicao,KeyType=RANGE ^
    --provisioned-throughput ^
        ReadCapacityUnits=5,WriteCapacityUnits=5 ^
    --table-class STANDARD
```


- Inserir jogadores na tabela utilizando um arquivo JSON

```
aws dynamodb batch-write-item --request-items file://batchJogadores.json
```

### CRIANDO ÍNDICES GLOBAIS SECUNDÁRIOS E REALIZANDO CONSULTAS COM ELES:


- Criando Index global secundário baseado no nome do jogador

```
aws dynamodb update-table ^
    --table-name Jogador^
    --attribute-definitions AttributeName=Nome,AttributeType=S ^
    --global-secondary-index-updates "[{\"Create\":{\"IndexName\": \"Nome-index\",\"KeySchema\":[{\"AttributeName\":\"Nome\",\"KeyType\":\"HASH\"}], \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5},\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Realizando pesquisa pelo indice nome do jogador

```
aws dynamodb query ^
    --table-name Jogador ^
    --index-name Nome-index ^
    --key-condition-expression "Nome = :nome" ^
    --expression-attribute-values  "{\":nome\":{\"S\":\"Giannis Antetokounmpo\"}}"
```

- Criando Index global secundário baseado na posisão dos jogadores

```
aws dynamodb update-table ^
    --table-name Jogador^
    --attribute-definitions AttributeName=Posicao,AttributeType=S ^	
    --global-secondary-index-updates "[{\"Create\":{\"IndexName\": \"Posicao-index\",\"KeySchema\":[{\"AttributeName\":\"Posicao\",\"KeyType\":\"HASH\"}], \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5},\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Realizando pesquisa pelo indice posição dos jogadores

```
aws dynamodb query ^
    --table-name Jogador ^
    --index-name Posicao-index ^
    --key-condition-expression "Posicao = :posicao" ^
    --expression-attribute-values  "{\":posicao\":{\"S\":\"forward\"}}"
```

- Realizando pesquisa por nome do jogador e posicao

```
aws dynamodb get-item --consistent-read ^
    --table-name Jogador ^
    --key "{\"Nome\": {\"S\": \"Lebron James\"}, \"Posicao\": {\"S\": \"forward\"}}"
```
