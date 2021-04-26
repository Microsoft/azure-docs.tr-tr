---
title: Bir Rust uygulamayı MongoDB için Azure Cosmos DB API 'sine bağlama
description: Bu hızlı başlangıçta, MongoDB için Azure Cosmos DB API 'SI tarafından desteklenen bir Rust uygulamasının nasıl oluşturulacağı gösterilmektedir.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: 91e7bafe98b1aceaf8fe27b07029291a48a31351
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555661"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Hızlı başlangıç: bir Rust uygulamayı MongoDB için Azure Cosmos DB API 'sine bağlama
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB, genel dağıtım ve yatay ölçeklendirme özellikleri ile belge, tablo, anahtar değer ve grafik veritabanlarını hızlıca oluşturmanıza ve sorgulamanızı sağlayan çok modelli bir veritabanı hizmetidir. Bu makalede sunulan örnek, [MongoDB Için Rust sürücüsünü](https://github.com/mongodb/mongo-rust-driver)kullanan basit bir komut satırı tabanlı uygulamadır. MongoDB için Azure Cosmos DB API 'SI [MongoDB kablo protokolüyle uyumlu](./mongodb-introduction.md#wire-protocol-compatibility)olduğundan, her MongoDB istemci sürücüsünün bu sunucuya bağlanması mümkündür.

MongoDB Rust sürücüsünü, örnek kodda uygulanan CRUD (oluşturma, okuma, güncelleştirme, silme) işlemlerini inceleyerek MongoDB için Azure Cosmos DB API 'siyle etkileşim kurmak üzere nasıl kullanacağınızı öğreneceksiniz. Son olarak, uygulamayı eylemde görüntülemek için yerel olarak çalıştırabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free). Veya Azure aboneliği olmadan [ücretsiz Azure Cosmos DB deneyin](https://azure.microsoft.com/try/cosmosdb/) . [Azure Cosmos DB öykünücüsünü](https://aka.ms/cosmosdb-emulator) bağlantı dizesiyle de kullanabilirsiniz `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Rust](https://www.rust-lang.org/tools/install) (sürüm 1,39 veya üzeri)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB ayarlama

Azure Cosmos DB bir hesabı ayarlamak için [buradaki yönergeleri](create-mongodb-dotnet.md)izleyin. Uygulamanın, Azure portal kullanarak ekleyebileceğiniz MongoDB bağlantı dizesine ihtiyacı olacaktır. Ayrıntılar için bkz., [özelleştirmek Için MongoDB bağlantı dizesini alın](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize).

## <a name="run-the-application"></a>Uygulamayı çalıştırma

### <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Örnek depoyu kopyalamak için aşağıdaki komutları çalıştırın.

1. Bir komut istemi açın, adlı yeni bir klasör oluşturun `git-samples` ve ardından komut istemi ' ni kapatın.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere yeni bir klasör olarak değiştirmek için `cd` komutunu kullanın.

    ```bash
    cd "C:\git-samples"
    ```

1. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>Uygulama oluşturma

İkiliyi oluşturmak için:

```bash
cargo build --release
```

### <a name="configure-the-application"></a>Uygulamayı yapılandırma 

Bağlantı dizesini, MongoDB veritabanını ve koleksiyon adlarını ortam değişkenleri olarak dışa aktarın. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> `ssl=true`Cosmos DB gereksinimler nedeniyle bu seçenek önemlidir. Daha fazla bilgi için bkz. [bağlantı dizesi gereksinimleri](connect-mongodb-account.md#connection-string-requirements).
>

`MONGODB_URL`Ortam değişkeni için, ve için yer tutucuları değiştirin `<COSMOSDB_ACCOUNT_NAME>``<COSMOSDB_PASSWORD>`

- `<COSMOSDB_ACCOUNT_NAME>`: Oluşturduğunuz Azure Cosmos DB hesabının adı
- `<COSMOSDB_PASSWORD>`: Önceki adımda ayıklanan veritabanı anahtarı

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

Ve için tercih ettiğiniz değerleri seçebilir `MONGODB_DATABASE` `MONGODB_COLLECTION` veya olduğu gibi bırakabilirsiniz.

Uygulamayı çalıştırmak için, doğru klasöre (uygulama ikilisinin bulunduğu konum) geçin:

```bash
cd target/release
```

Oluşturmak için `todo`

```bash
./todo create "Create an Azure Cosmos DB database account"
```

Başarılı olursa, yeni oluşturulan belgenin MongoDB ile bir çıktı görmeniz gerekir `_id` :

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

Başka bir oluştur `todo`

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

Tüm öğeleri Listele `todo`

```bash
./todo list all
```

Yeni ekledikleriniz görmeniz gerekir:

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

Bir a 'nın durumunu güncelleştirmek için `todo` (örneğin, durum olarak değiştirin `completed` ), kimliği şu şekilde kullanın `todo` :

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

Yalnızca tamamlanan öğeleri Listele `todo`

```bash
./todo list completed
```

Güncelleştirdiğiniz bir tane görmeniz gerekir

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

KIMLIĞI kullanarak bir öğesini silme `todo`

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

`todo`Onaylanacak öğeleri listeleyin

```bash
./todo list all
```

`todo`Yeni sildiğiniz için mevcut olmamalıdır.

### <a name="view-data-in-data-explorer"></a>Veri Gezgini’nde verileri görüntüleme

Azure Cosmos DB depolanan veriler, Azure portal görüntülemek ve sorgulamak için kullanılabilir.

Önceki adımda oluşturulan verileri görüntülemek, sorgulamak ve üzerinde çalışmak için web tarayıcınızda [Azure portalı](https://portal.azure.com) oturumunu açın.

Üst arama kutusuna **Azure Cosmos DB** girin. Cosmos hesabı dikey penceresi açıldığında, Cosmos hesabınızı seçin. Sol gezinti bölmesinde **Veri Gezgini**' yi seçin. Koleksiyonlar bölmesinde koleksiyonunuzu genişletin; bundan sonra koleksiyondaki belgeleri görüntüleyebilir, verileri sorgulayabilir ve hatta saklı yordam, tetikleyici ve UDF’ler oluşturup çalıştırabilirsiniz.

## <a name="review-the-code-optional"></a>Kodu gözden geçirme (isteğe bağlı)

Uygulamanın nasıl çalıştığını öğrenmek istiyorsanız, bu bölümdeki kod parçacıklarını gözden geçirebilirsiniz. Aşağıdaki kod parçacıkları `src/main.rs` dosyadan alınmıştır.

`main`İşlevi, uygulamanın giriş noktasıdır `todo` . MongoDB için Azure Cosmos DB API 'sinin, ortam değişkeni tarafından sağlanması için bağlantı URL 'sini bekliyor `MONGODB_URL` . Yeni bir örneği `TodoManager` oluşturulur ve ardından Kullanıcı tarafından seçilen işlem temelinde uygun yöntemi temsil eden bir [ `match` ifade](https://doc.rust-lang.org/book/ch06-02-match.html) gelir `TodoManager` `create` `update` `list` `delete` .

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` , `struct` [MongoDB:: Sync:: toplamasını](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html)kapsülleyen bir. İşlevini kullanarak bir örneğini oluşturmaya çalıştığınızda `TodoManager` `new` , MongoDB IÇIN Azure Cosmos DB API 'sine bir bağlantı başlatır.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

En önemlisi, ' nin `TodoManager` yönetilmesine yardımcı olmak için yöntemler içerir `todo` . Bunlara birer tane geçelim.

`add_todo`Yöntemi `todo` Kullanıcı tarafından sağlanmış bir açıklama alır ve yapının bir örneğini oluşturur ve `Todo` aşağıdaki gibi görünür. [Serde](https://github.com/serde-rs/serde) çerçevesi, bSon verileri yapı örneklerine eşlemek (seri hale getirmek/devre dışı bırakmak) için kullanılır `Todo` . `serde`Alan özniteliklerinin serileştirme/seri hale getirme işlemini özelleştirmek için nasıl kullanıldığına dikkat edin. Örneğin, `todo_id` Todo içindeki alan, `struct` `ObjectId` MongoDB 'de olarak depolanır `_id` .

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) eklenecek ayrıntıları temsil eden [belgeyi](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) kabul eder `todo` . ' Dan ' a dönüştürmenin `Todo` `Document` iki adımlı bir işlemdir ve [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) ve [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document)birleşimini kullanarak elde edildiğini unutmayın.

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[Koleksiyon. Find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) , *Tüm* `todo` öğeleri alma veya Kullanıcı tarafından belirtilen durum (veya) temelinde filtre uygulamak için kullanılır `pending` `completed` . `while`Döngüde, `Document` aramanın sonucu olarak elde edilen her biri, `Todo` [bson:: from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html)kullanarak bir yapıya nasıl dönüştürülebileceğine göz önünde. Bu, yönteminde yapılan şeydir `add_todo` .

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

Bir `todo` durum güncelleştirilemeyebilir ( `pending` `completed` veya tersi). , `todo` Güncelleştirilmesi gereken belgeyi bulmak için[Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) yöntemi tarafından kullanılan bir [bSon:: OID:: ObjectID](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) değerine dönüştürülür.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

`todo` [Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one) yöntemi kullanılarak basit bir şekilde silinir.


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure Cloud Shell kullanarak bir Azure Cosmos DB MongoDB API hesabı oluşturmayı öğrendiniz ve yönetmek için bir Rust komut satırı uygulaması oluşturup çalıştırın `todo` . Şimdi Azure Cosmos DB hesabınıza ek veriler aktarabilirsiniz.

> [!div class="nextstepaction"]
> [Azure Cosmos DB’ye MongoDB verileri aktarma](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
