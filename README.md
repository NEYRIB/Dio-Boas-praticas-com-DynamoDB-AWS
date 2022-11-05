<h1 align="center">
  <br>
  <img src="https://hermes.digitalinnovation.one/tracks/342f7392-a8b5-421f-bea9-d29f1fd8aae9.png" alt="logo" height="300">
</h1>

#### Geração Tech Unimed-BH - Ciência de Dados    -   DIO - Digital Innovation One Inc. 

# Boas práticas com DynamoDB - AWS

## Professor: Cassiano Peres

# dio-live-dynamodb
Repositório para o live coding do dia 25/10/2022 sobre o Amazon DynamoDB

### Serviço utilizado
  - AWS
  - Amazon DynamoDB
  - Amazon CLI para execução em linha de comando
  
  
Features Relacionais (SQL) e Não Relacionais (NoSQL) usando o mesmo banco de dados? Isso é possível? 

Com o DynamoDB sim! 

Entenda um pouco das possibilidades desse banco de dados totalmente gerenciado da AWS. 

Para isso, nosso super expert apresenta uma série de boas práticas para o DynamoDB.

### Comandos para execução do experimento:


- Criar uma tabela

```
aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```
<h1 align="left">
  <br>
  <img src="image\01-1.PNG" alt="captura1" height="300">
</h1>

<h1 align="left">
  <br>
  <img src="image\tabela\1.png" alt="captura1" height="500">
</h1>
<h1 align="left">
  <br>
  <img src="image\tabela\2.png" alt="captura2" height="500">
</h1>
<h1 align="left">
  <br>
  <img src="image\tabela\3.png" alt="captura3" height="500">
</h1>
<h1 align="left">
  <br>
  <img src="image\tabela\4.png" alt="captura4" height="500">
</h1>



- Inserir um item

```
aws dynamodb put-item \
    --table-name Music \
    --item file://itemmusic.json \
```

- Inserir múltiplos itens

```
aws dynamodb batch-write-item \
    --request-items file://batchmusic.json
```

- Criar um index global secundário baseado no título do álbum

```
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"AlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criar um index global secundário baseado no nome do artista e no título do álbum

```
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
        AttributeName=Artist,AttributeType=S \
        AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"ArtistAlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"Artist\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criar um index global secundário baseado no título da música e no ano

```
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
        AttributeName=SongTitle,AttributeType=S \
        AttributeName=SongYear,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"SongTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"SongTitle\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"SongYear\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```
<h1 align="left">
  <br>
  <img src="image\2-2.PNG" alt="captura4" height="300">
</h1>

<h1 align="left">
  <br>
  <img src="image\index\5.png" alt="captura4" height="300">
</h1>

<h1 align="left">
  <br>
  <img src="image\index\6.png" alt="captura4" height="300">
</h1>

<h1 align="left">
  <br>
  <img src="image\index\7.png" alt="captura4" height="300">
</h1>

- Pesquisar item por artista

```
aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist" \
    --expression-attribute-values  '{":artist":{"S":"Iron Maiden"}}'
```
- Pesquisar item por artista e título da música

```
aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist and SongTitle = :title" \
    --expression-attribute-values file://keyconditions.json
```

- Pesquisa pelo index secundário baseado no título do álbum

```
aws dynamodb query \
    --table-name Music \
    --index-name AlbumTitle-index \
    --key-condition-expression "AlbumTitle = :name" \
    --expression-attribute-values  '{":name":{"S":"Fear of the Dark"}}'
```

- Pesquisa pelo index secundário baseado no nome do artista e no título do álbum

```
aws dynamodb query \
    --table-name Music \
    --index-name ArtistAlbumTitle-index \
    --key-condition-expression "Artist = :v_artist and AlbumTitle = :v_title" \
    --expression-attribute-values  '{":v_artist":{"S":"Iron Maiden"},":v_title":{"S":"Fear of the Dark"} }'
```

- Pesquisa pelo index secundário baseado no título da música e no ano

```
aws dynamodb query \
    --table-name Music \
    --index-name SongTitleYear-index \
    --key-condition-expression "SongTitle = :v_song and SongYear = :v_year" \
    --expression-attribute-values  '{":v_song":{"S":"Wasting Love"},":v_year":{"S":"1992"} }'
```
