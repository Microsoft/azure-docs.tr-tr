---
title: "Hızlı başlangıç: PostgreSQL için Azure veritabanı 'na bağlanmak için Node.js kullanın-tek sunucu"
description: Bu hızlı başlangıçta, PostgreSQL için Azure veritabanı 'na bağlanmak ve bu verileri sorgulamak için kullanabileceğiniz bir Node.js kod örneği sunulmaktadır-tek sunucu.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom:
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- devx-track-js
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 7569606429740de23b56767d490b9bb14283d468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93331701"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Hızlı başlangıç: PostgreSQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için Node.js kullanma-tek sunucu

Bu hızlı başlangıçta, bir Node.js uygulaması kullanarak PostgreSQL için Azure veritabanı 'na bağlanırsınız. Ayrıca veritabanında veri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini nasıl kullanacağınız da gösterilmiştir. Bu makaledeki adımlarda, Node.js kullanarak geliştirmeyle ilgili bilgi sahibi olduğunuz ve PostgreSQL için Azure Veritabanı ile çalışmaya yeni başladığınız varsayılır.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Hızlı başlangıç: Azure portal veya [hızlı başlangıç: Azure CLI kullanarak](quickstart-create-server-database-azure-cli.md)PostgreSQL [için Azure veritabanı sunucusu oluşturma](quickstart-create-server-database-portal.md) .

- [Node.js](https://nodejs.org)

## <a name="install-pg-client"></a>pg istemcisini yükleme
Node.js için bir PostgreSQL istemcisi olan [pg](https://www.npmjs.com/package/pg)’yi yükleyin.

Bunu yapmak için komut satırından JavaScript’e yönelik düğüm paketi yöneticisini (npm) çalıştırarak pg istemcisini yükleyin.
```bash
npm install pg
```

Yüklü paketleri listeleyerek yüklemeyi doğrulayın.
```bash
npm list
```

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma
PostgreSQL için Azure Veritabanı'na bağlanmak üzere gereken bağlantı bilgilerini alın. Tam sunucu adına ve oturum açma kimlik bilgilerine ihtiyacınız vardır.

1. [Azure Portal](https://portal.azure.com/), oluşturduğunuz sunucuyu (örneğin, **demosunucum**) arayın ve seçin.

1. Sunucunun **genel bakış** panelinden **sunucu adı** ve **Yönetici Kullanıcı adı**' nı bir yere göz atın. Parolanızı unutursanız, bu panelden parolayı da sıfırlayabilirsiniz.

   :::image type="content" source="./media/connect-nodejs/server-details-azure-database-postgresql.png" alt-text="PostgreSQL için Azure veritabanı bağlantı dizesi":::

## <a name="running-the-javascript-code-in-nodejs"></a>Node.js’de JavaScript kodunu çalıştırma
`node` yazarak Node.js’yi Bash kabuğundan, Terminal’den veya Windows Komut İstemi’nden başlatabilir, ardından isteme kopyalayıp yapıştırarak örnek JavaScript kodunu etkili bir şekilde çalıştırabilirsiniz. Alternatif olarak, JavaScript kodunu bir metin dosyasına kaydedebilir ve `node filename.js` öğesini, bunu çalıştıracak bir parametre olarak dosya adıyla başlatabilirsiniz.

## <a name="connect-create-table-and-insert-data"></a>Bağlanma, tablo oluşturma ve veri ekleme
Aşağıdaki kodu kullanarak bağlanın ve **CREATE TABLE** ve **INSERT INTO** SQL deyimlerini kullanarak verileri yükleyin.
[pg.Client](https://github.com/brianc/node-postgres/wiki/Client) nesnesi, PostgreSQL sunucusuyla arabirim oluşturmak için kullanılır. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) işlevi, sunucuyla bağlantı kurmak için kullanılır. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) işlevi, PostgreSQL veritabanına karşı SQL sorgusunu yürütmek için kullanılır. 

Ana bilgisayar, veritabanı adı, kullanıcı ve parola parametrelerini, sunucuyu ve veritabanını oluştururken belirttiğiniz değerlerle değiştirin.

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>Verileri okuma
Bağlanmak ve **SELECT** SQL deyimi kullanarak verileri okumak için aşağıdaki kodu kullanın. [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) nesnesi, PostgreSQL sunucusuyla arabirim oluşturmak için kullanılır. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) işlevi, sunucuyla bağlantı kurmak için kullanılır. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) işlevi, PostgreSQL veritabanına karşı SQL sorgusunu yürütmek için kullanılır. 

Ana bilgisayar, veritabanı adı, kullanıcı ve parola parametrelerini, sunucuyu ve veritabanını oluştururken belirttiğiniz değerlerle değiştirin. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>Verileri güncelleştirme
Bağlanmak ve **UPDATE** SQL deyimi kullanarak verileri okumak için aşağıdaki kodu kullanın. [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) nesnesi, PostgreSQL sunucusuyla arabirim oluşturmak için kullanılır. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) işlevi, sunucuyla bağlantı kurmak için kullanılır. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) işlevi, PostgreSQL veritabanına karşı SQL sorgusunu yürütmek için kullanılır. 

Ana bilgisayar, veritabanı adı, kullanıcı ve parola parametrelerini, sunucuyu ve veritabanını oluştururken belirttiğiniz değerlerle değiştirin. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>Verileri silme
Bağlanmak ve **DELETE** SQL deyimi kullanarak verileri okumak için aşağıdaki kodu kullanın. [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) nesnesi, PostgreSQL sunucusuyla arabirim oluşturmak için kullanılır. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) işlevi, sunucuyla bağlantı kurmak için kullanılır. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) işlevi, PostgreSQL veritabanına karşı SQL sorgusunu yürütmek için kullanılır. 

Ana bilgisayar, veritabanı adı, kullanıcı ve parola parametrelerini, sunucuyu ve veritabanını oluştururken belirttiğiniz değerlerle değiştirin. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç sırasında kullanılan tüm kaynakları temizlemek için, aşağıdaki komutu kullanarak kaynak grubunu silin:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Dışarı Aktarma ve İçeri Aktarma seçeneğini kullanarak veritabanınızı geçirme](./howto-migrate-using-export-and-import.md)
