# dio-live-dynamodb
Repositório para o live coding do dia 30/09/2021 sobre o Amazon DynamoDB

### Serviço utilizado
  - Amazon DynamoDB
  - Amazon CLI para execução em linha de comando

### Comandos para execução do experimento:7

#Utilizado o Sistema Operacionao Windows

####Criações (tabela, inserções e index)

> Criar uma tabela

```
aws dynamodb create-table --table-name Music --attribute-definitions AttributeName=Artist,AttributeType=S AttributeName=SongTitle,AttributeType=S --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE --provisioned-throughput ReadCapacityUnits=10,WriteCapacityUnits=5
```

> Inserir um item (com arquivo json - é preciso que você esteja acessando a pasta que contém os arquivos no terminal)

```
aws dynamodb put-item --table-name Music --item file://itemmusic.json
```

> Inserir múltiplos itens

```
aws dynamodb batch-write-item --request-items file://batchmusic.json
```

> Criar um index global secundário baseado no título do álbum

```
aws dynamodb update-table --table-name Music --attribute-definitions AttributeName=AlbumTitle,AttributeType=S --global-secondary-index-updates "[{\"Create\":{\"IndexName\":\"AlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"HASH\"}],\"ProvisionedThroughput\":{\"ReadCapacityUnits\":10,\"WriteCapacityUnits\":5},\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

> Criar um index global secundário baseado no nome do artista e no título do álbum

```
aws dynamodb update-table --table-name Music --attribute-definitions AttributeName=Artist,AttributeType=S AttributeName=AlbumTitle,AttributeType=S --global-secondary-index-updates "[{\"Create\":{\"IndexName\": \"ArtistAlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"Artist\",\"KeyType\":\"HASH\"},{\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"RANGE\"}],\"ProvisionedThroughput\":{\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\":5},\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"

```

> Criar um index global secundário baseado no título da música e no ano

```
aws dynamodb update-table --table-name Music --attribute-definitions AttributeName=SongTitle,AttributeType=S AttributeName=SongYear,AttributeType=S --global-secondary-index-updates "[{\"Create\":{\"IndexName\": \"SongTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"SongTitle\",\"KeyType\":\"HASH\"},{\"AttributeName\":\"SongYear\",\"KeyType\":\"RANGE\"}],\"ProvisionedThroughput\":{\"ReadCapacityUnits\":10, \"WriteCapacityUnits\": 5},\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

#### Consultas

- Pesquisar item por artista

```
aws dynamodb query --table-name Music --key-condition-expression "Artist=:artist" --expression-attribute-values "{\":artist\":{\"S\":\"Iron Maiden\"}}"
```

- Pesquisar item por artista e título da música

```
aws dynamodb query --table-name Music --key-condition-expression "Artist = :artist and SongTitle = :title" --expression-attribute-values file://keyconditions.json
```

- Pesquisa pelo index secundário baseado no título do álbum

```
aws dynamodb query --table-name Music --index-name AlbumTitle-index --key-condition-expression "AlbumTitle = :name" --expression-attribute-values "{\":name\":{\"S\":\"Fear of the Dark\"}}"
```
