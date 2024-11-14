## noSQL

O **noSQL** é um termo genérico que se refere a qualquer banco de dados que não seja um banco de dados relacional. O noSQL é uma abordagem alternativa para o gerenciamento de dados que é mais adequada para certos tipos de aplicativos - e o MongoDB é um dos bancos de dados noSQL mais populares.

### MongoDB

O MongoDB armazena dados em **coleções**. Uma coleção é um grupo de documentos BSON. Um **documento** é um conjunto de pares de chave-valor. Cada documento é uma instância de um **tipo de dados BSON**.

- **Base de Dados**: Container de coleções.
- **Coleção**: Grupo de documentos.
- **Documento**: Instância de um tipo de dados BSON.
- **Campo**: Par chave-valor em um documento.
- **Chave**: Nome do campo em um documento.
- **Valor**: Dado associado a uma chave em um documento.


## MongoDB Shell (mongosh)

### `<database>.<collection>.insertOne()`
```javascript
db.users.insertOne({ name: "John Doe", email: "john.doe@example.com" });
```

Este comando insere um novo documento na coleção `users` com o nome "John Doe" e o email "john.doe@example.com". Retorna:

```json
{ "acknowledged": true, "insertedId": ObjectId("5f5f7b3b3f3b9b1b3f3b9b1b") }
```

- `acknowledged`: Indica se a operação foi bem-sucedida.
- `insertedId`: ID do documento inserido.

### `<database>.<collection>.find()`
```javascript
db.users.find({ email: "john.doe@example.com" });
```

Este comando recupera todos os documentos da coleção `users` onde o email é igual a "john.doe@example.com". Retorna:

```json
{ "_id": ObjectId("5f5f7b3b3f3b9b1b3f3b9b1b"), "name": "John Doe", "email": "john.doe@example.com" }
```

#### `<database>.<collection>.find().limit()` 
```javascript
db.users.find({ email: "john.doe@example.com" }).limit(1);
```

Este comando recupera apenas um documento da coleção `users` onde o email é igual a "john.doe@example.com". Retorna:

```json
{ "_id": ObjectId("5f5f7b3b3f3b9b1b3f3b9b1b"), "name": "John Doe", "email": "john.doe@example.com" }
```

### `<database>.<collection>.updateOne()`
```javascript
db.users.updateOne({ name: "John Doe" }, { $set: { email: "john.newemail@example.com" } });
```

Este comando atualiza o email de "John Doe" para "john.newemail@example.com" na coleção `users`. Retorna:

```json
{ "acknowledged": true, "matchedCount": 1, "modifiedCount": 1 }
```

- `acknowledged`: Indica se a operação foi bem-sucedida.
- `matchedCount`: Número de documentos correspondentes.
- `modifiedCount`: Número de documentos modificados.

### `<database>.<collection>.deleteOne()`
```javascript
db.users.deleteOne({ name: "John Doe" });
```

Este comando remove todos os documentos que têm o campo `name` igual a "John Doe" da coleção `users`. Retorna:

```json
{ "acknowledged": true, "deletedCount": 1 }
```

### `<database>.<collection>.aggregate()`
```javascript
db.users.aggregate([
  {
    $lookup: {
      from: "orders",
      localField: "_id",
      foreignField: "user_id",
      as: "orders"
    }
  },
  {
    $project: {
      name: 1,
      "orders.total": 1
    }
  }
]);
```

Este comando recupera o nome do usuário e o total de todas as ordens associadas a esse usuário, combinando as coleções `users` e `orders` com base na coluna `_id` da coleção `users` e na coluna `user_id` da coleção `orders`. Retorna:

```json
{ "_id": ObjectId("5f5f7b3b3f3b9b1b3f3b9b1b"), "name": "John Doe", "orders": [ { "total": 100 }, { "total": 200 } ] }
```

## Coleções

### `<database>.createCollection()`
```javascript
db.createCollection("users");
```

Este comando cria uma coleção chamada `users`. Retorna:

```json
{ "ok": 1 }
```

### `<database>.getCollectionNames()`
MongoDB não suporta diretamente a criação de coleções apenas se elas ainda não existirem, mas você pode verificar se a coleção existe antes de criá-la.

```javascript
if (!db.getCollectionNames().includes("users")) {
  db.createCollection("users");
}
```

Este comando cria a coleção `users` apenas se ela ainda não existir.

#### `<database>.<collection>.renameCollection()`
```javascript
db.users.renameCollection("customers");
```

Este comando renomeia a coleção `users` para `customers`. Retorna:

```json
{ "ok": 1 }
```

### `<database>.<collection>.drop()`
```javascript
db.users.drop();
```

Este comando remove a coleção `users` do banco de dados, junto com todos os seus documentos. Retorna:

```json
{ "ok": 1 }
```

## Índices

### `<database>.<collection>.createIndex()`
```javascript
db.users.createIndex({ email: 1 }, { name: "idx_users_email" });
```

Este comando cria um índice chamado `idx_users_email` na coluna `email` da coleção `users`, acelerando as consultas baseadas no email. Retorna:

```json
{ "createdCollectionAutomatically": false, "numIndexesBefore": 1, "numIndexesAfter": 2, "ok": 1 }
```

### `<database>.<collection>.getIndexes()`
```javascript
db.users.getIndexes().forEach(index => {
  if (index.name === "idx_users_email") {
    db.users.dropIndex("idx_users_email");
    db.users.createIndex({ email: 1 }, { name: "index_users_email" });
  }
});
```

Este comando renomeia o índice `idx_users_email` para `index_users_email`. Retorna:

```json
{ "createdCollectionAutomatically": false, "numIndexesBefore": 1, "numIndexesAfter": 1, "ok": 1 }
```

### `<database>.<collection>.dropIndex()`
```javascript
db.users.dropIndex("idx_users_email");
```

Este comando remove o índice `idx_users_email` da coleção `users`. Retorna:

```json
{ "nIndexesWas": 2, "ok": 1 }
```

## Backup with mongodump

  mongodump is used to create a binary backup of a MongoDB database or collection. It saves data as BSON files, which are efficient for storage and easy to restore.

bash
# Backup an entire database
mongodump --db mydatabase --out /path/to/backup/

# Backup a specific collection
mongodump --db mydatabase --collection mycollection --out /path/to/backup/

# Backup with authentication (if your MongoDB requires it)
  This will create a folder structure at /path/to/backup/ containing BSON files for each collection in the specified database.

´´´
mongodump --host localhost --port 27017 --username myUser --password myPassword --authenticationDatabase admin --db mydatabase --out /path/to/backup/
´´´

## Restore with mongorestore
mongorestore restores data from BSON files created by mongodump.

bash
# Restore the entire database from a backup
mongorestore --db mydatabase /path/to/backup/mydatabase/

# Restore a specific collection
mongorestore --db mydatabase --collection mycollection /path/to/backup/mydatabase/mycollection.bson

# Restore with authentication
'''
mongorestore --host localhost --port 27017 --username myUser --password myPassword --authenticationDatabase admin --db mydatabase /path/to/backup/mydatabase/
'''

These commands will populate mydatabase (or specific collections) with the data from the backup files.

## Database Statistics with db.stats()
  The db.stats() command provides various statistics about the database, such as storage size, collection count, and data size. This is useful for monitoring database usage and performance.

javascript

use mydatabase;
db.stats();
Sample Output:
json
Copiar código
{
  "db": "mydatabase",
  "collections": 5,
  "views": 0,
  "objects": 320,
  "avgObjSize": 54.12,
  "dataSize": 17318,
  "storageSize": 32768,
  "indexes": 2,
  "indexSize": 8192,
  ...
}
This output provides metrics on data size, average object size, storage size, index count, and index size, helping administrators optimize database resources.


## Check Collection Statistics with db.collection.stats()
  db.collection.stats() gives information about a specific collection, including document count, index size, and average document size.
  '''

use mydatabase;
db.mycollection.stats();
Sample Output:
json
Copiar código
{
  "ns": "mydatabase.mycollection",
  "count": 500,
  "size": 12000,
  "avgObjSize": 24,
  "storageSize": 16384,
  "totalIndexSize": 4096,
  ...
}
'''

## Database Repair with db.repairDatabase()
  db.repairDatabase() is used to attempt repair of a corrupted database. It’s important to back up data first, as this operation can sometimes result in data loss.

'''
use mydatabase;
db.repairDatabase();
'''

This operation scans the database for inconsistencies and attempts to fix any it encounters. However, it can be time-consuming and requires sufficient disk space to complete.

