---
title: 'Hızlı başlangıç: Rust ile bağlanma-PostgreSQL için Azure veritabanı-tek sunucu'
description: Bu hızlı başlangıçta, PostgreSQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için kullanabileceğiniz, tek sunuculu kod örnekleri sunulmaktadır.
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505861"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Hızlı başlangıç: PostgreSQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için Rust kullanma-tek sunucu

Bu makalede, örnek kodda uygulanan CRUD (oluşturma, okuma, güncelleştirme, silme) işlemlerini inceleyerek PostgreSQL için Azure veritabanı ile etkileşim kurmak üzere [PostgreSQL sürücüsünü](https://github.com/sfackler/rust-postgres) nasıl kullanacağınızı öğreneceksiniz. Son olarak, uygulamayı eylemde görüntülemek için yerel olarak çalıştırabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu hızlı başlangıç için şunlar gerekir:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free).
- Yeni bir [Rust](https://www.rust-lang.org/tools/install) sürümü yüklendi.
- PostgreSQL için Azure veritabanı tek sunucu- [Azure Portal](./quickstart-create-server-database-portal.md) kullanarak bir tane oluşturun <br/> veya [Azure CLI](./quickstart-create-server-database-azure-cli.md).
- Genel veya özel erişim kullanıyor olmanız temelinde, bağlantıyı etkinleştirmek için aşağıdaki eylemlerden **birini** doldurun.

  |Eylem| Bağlantı yöntemi|Nasıl yapılır kılavuzu|
  |:--------- |:--------- |:--------- |
  | **Güvenlik duvarı kurallarını yapılandırma** | Genel | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Hizmet uç noktasını yapılandır** | Genel | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Özel bağlantıyı Yapılandır** | Özel | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Git](https://git-scm.com/downloads) yüklendi.

## <a name="get-database-connection-information"></a>Veritabanı bağlantı bilgilerini al
PostgreSQL için Azure veritabanı veritabanına bağlanmak için tam sunucu adı ve oturum açma kimlik bilgileri gerekir. Bu bilgileri Azure portal alabilirsiniz.

1. [Azure Portal](https://portal.azure.com/), PostgreSQL Için Azure veritabanı sunucu adı ' nı arayıp seçin.
1. Sunucunun **genel bakış** sayfasında, tam **sunucu adı** ve **Yönetici Kullanıcı adı**' nı kopyalayın. Tam **sunucu adı** her zaman *\<my-server-name> . Postgres.Database.Azure.com* olur ve **Yönetici Kullanıcı adı** her zaman formundadır *\<my-admin-username>@\<my-server-name>* .

## <a name="review-the-code-optional"></a>Kodu gözden geçirme (isteğe bağlı)

Kodun nasıl çalıştığını öğrenmek istiyorsanız aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Aksi takdirde, [Uygulamayı çalıştırmaya](#run-the-application)devam etmek için atlayabilirsiniz.

### <a name="connect"></a>Bağlanma

`main`İşlev, PostgreSQL için Azure veritabanı 'na bağlanarak başlar ve bağlantı bilgileri `POSTGRES_HOST` ,, `POSTGRES_USER` `POSTGRES_PASSWORD` ve, için aşağıdaki ortam değişkenlerine bağımlıdır `POSTGRES_DBNAME` . Varsayılan olarak, PostgreSQL veritabanı hizmeti bağlantı gerektirecek şekilde yapılandırılmıştır `TLS` . `TLS`İstemci uygulamanız bağlantıyı desteklemiyorsa, gerektirmeyi devre dışı bırakmayı seçebilirsiniz `TLS` . Ayrıntılar için lütfen [PostgreSQL Için Azure veritabanı-tek sunucu ' da TLS bağlantısını yapılandırma](./concepts-ssl-connection-security.md)konusuna bakın.

Bu makaledeki örnek uygulama, [Postgres-OpenSSL erişebilmeniz](https://crates.io/crates/postgres-openssl/)ile TLS kullanır. [Postgres:: Client:: Connect](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) işlevi bağlantıyı başlatmak için kullanılır ve bu durum başarısız olursa program çıkıyor.

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>Bırak ve tablo oluştur

Örnek uygulama, `inventory` CRUD (oluşturma, okuma, güncelleştirme, silme) işlemlerini göstermek için basit bir tablo kullanır.

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

`drop_create_table`İşlev, `DROP` Yeni bir `inventory` tane oluşturmadan önce tabloya ilk kez çalışır. Bu, bilinen (temiz) bir durumla her zaman başladığınızda öğrenme/deneme için daha kolay hale gelir. [Execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) yöntemi, oluşturma ve bırakma işlemleri için kullanılır. 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>Veri ekleme

`insert_data` tabloya giriş ekler `inventory` . [Prepare](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare) işlevi ile [hazırlanmış bir ifade](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html) oluşturur. 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

Ayrıca, sorgu parametrelerinin türlerinin açıkça belirtilmesini sağlayan [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed) yöntemi kullanımını unutmayın.

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

Son olarak, `for` `item-3` `item-4` `item-5` her biri için rastgele üretilmiş miktar eklemek üzere bir döngü kullanılır.

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>Verileri sorgulama

`query_data` işlev, tablodaki verilerin nasıl alınacağını gösterir `inventory` . [Query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) yöntemi tarafından bir öğeyi almak için kullanılır `id` . 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

Envanter tablosundaki tüm satırlar `select * from` [sorgu](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query) yöntemiyle bir sorgu kullanılarak getirilir. Döndürülen satırlar, [Al](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get)kullanılarak her bir sütunun değerini çıkarmak için yinelenir. 

> [!TIP]
> `get`Sütunu, satırdaki sayısal diziniyle veya sütun adına göre belirtmeyi nasıl mümkün hale getirir.

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>Verileri güncelleştirme

`update_date`İşlevi tüm öğelerin miktarını rastgele olarak güncelleştirir. İşlevin satırları eklediği için, bu, `insert_data` `5` `for` döngüde hesaba alınır `for n in 1..=5`

> [!TIP]
> `query` `execute` `id` ' İ ve yeni oluşturulan `quantity` ( [döndürme yan tümcesini](https://www.postgresql.org/docs/current/dml-returning.html)kullanarak) geri almayı düşüntiğimiz için yerine kullandığımızdan emin olmanız gerektiğini unutmayın.

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>Verileri silme

Son olarak, `delete` işlevi tablosundan bir öğenin nasıl kaldırılacağını gösterir `inventory` `id` . `id`Rastgele seçilir- `1` `5` `5` `insert_data` işlevin `5` ile başlamak için satırlar eklediği için (dahil) arasında rastgele bir tamsayıdır. 

> [!TIP]
> `query` `execute` Az önce silindiğimiz öğe hakkındaki bilgileri geri almayı planladığımızdan ( [döndürülen yan tümce](https://www.postgresql.org/docs/current/dml-returning.html)kullanılarak) bunun yerine kullanıyoruz.

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

1. İle başlamak için, örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın:

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Gerekli ortam değişkenlerini Azure portal kopyaladığınız değerlerle ayarlayın:

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. Uygulamayı çalıştırmak için Klonladığınız dizine geçin ve yürütün `cargo run` :

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    Aşağıdakine benzer bir çıktı görmeniz gerekir:

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. Onaylamak için, [psql kullanarak](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) PostgreSQL Için Azure veritabanı 'na bağlanıp veritabanına yönelik sorguları çalıştırabilirsiniz, örneğin:

    ```sql
    select * from inventory;
    ```

[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç sırasında kullanılan tüm kaynakları temizlemek için, aşağıdaki komutu kullanarak kaynak grubunu silin:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Portalı kullanarak PostgreSQL için Azure veritabanı sunucusunu yönetme](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [CLı kullanarak PostgreSQL için Azure veritabanı sunucusunu yönetme](./how-to-manage-server-cli.md)<br/>

[Aradığınızı bulamıyor musunuz? Bize bilgi verin.](https://aka.ms/postgres-doc-feedback)
