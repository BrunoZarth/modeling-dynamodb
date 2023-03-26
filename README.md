
# Criar uma tabela

    aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist, AttributeType=S \
        AttributeName=SongTitle, AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \    
	AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnit=5

# Inserir um item

    aws dynamodb put-item \
    --table-name Music \
    --item {
    	     "Artist": {"S": "The Rolling Stones"},
	     "SongTitle": {"S": "Rip This Joint"},
 	     "AlbumTitle": {"S": "Exile on Main St"},
             "SongYear": {"S": "1972"}
            }
            
# Inserir múltiplos itens
    
    aws dynamodb batch-write-item \
    --request-items {
    "Music": [
        {
            "PutRequest": {
                "Item": {
                  "Artist": {"S": "The Rolling Stones"},
                  "SongTitle": {"S": "Beast of Burden"},
                  "AlbumTitle": {"S": "Some Girls"},
                  "SongYear": {"S": "1978"}
                }
            }
        },
        {
            "PutRequest": {
                "Item": {
                  "Artist": {"S": "The Rolling Stones"},
                  "SongTitle": {"S": "Casino Boogie"},
                  "AlbumTitle": {"S": "Exile on Main St"},
                  "SongYear": {"S": "1972"}
                }
            }
        },
        {
            "PutRequest": {
                "Item": {
                  "Artist": {"S": "The Rolling Stones"},
                  "SongTitle": {"S": "Black Limousine"},
                  "AlbumTitle": {"S": "Tattoo You"},
                  "SongYear": {"S": "1972"}
                }
            }
        }
      ]
}


# Criar um index global secundário baseado no título do álbum

    aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
        AttributeName=Artist,AttributeType=S \
        AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"ArtistAlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"Artist\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"

# Criar um index global secundário baseado no nome do artista e no título do álbum

    aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
        AttributeName=SongTitle,AttributeType=S \
        AttributeName=SongYear,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"SongTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"SongTitle\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"SongYear\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"

# Criar um index global secundário baseado no título da música e no ano

    aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
        AttributeName=SongTitle,AttributeType=S \
        AttributeName=SongYear,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"SongTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"SongTitle\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"SongYear\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"

# Pesquisar item por artista

    aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist" \
    --expression-attribute-values  '{":artist":{"S":"The Rolling Stones"}}'
    
    
# Pesquisar item por artista e título da música

    aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist and SongTitle = :title" \
    --expression-attribute-values {
    				    ":artist":{"S":"The Rolling Stones"},
    				    ":title":{"S":"Black Limousine"}
				   }

# Pesquisa pelo index secundário baseado no título do álbum

    aws dynamodb query \
    --table-name Music \
    --index-name AlbumTitle-index \
    --key-condition-expression "AlbumTitle = :name" \
    --expression-attribute-values  '{":name":{"S":"Some Girls"}}'

# Pesquisa pelo index secundário baseado no nome do artista e no título do álbum

    aws dynamodb query \
    --table-name Music \
    --index-name ArtistAlbumTitle-index \
    --key-condition-expression "Artist = :v_artist and AlbumTitle = :v_title" \
    --expression-attribute-values  '{":v_artist":{"S":"The Rolling Stones"},":v_title":{"S":"Some Girls"} }'

    # Pesquisa pelo index secundário baseado no título da música e no ano
    
    aws dynamodb query \
    --table-name Music \
    --index-name SongTitleYear-index \
    --key-condition-expression "SongTitle = :v_song and SongYear = :v_year" \
    --expression-attribute-values  '{":v_song":{"S":"Casino Boogie"},":v_year":{"S":"1972"} }'
