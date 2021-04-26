---
title: MongoDB için bir Go uygulamasını Azure Cosmos DB API 'sine bağlama
description: Bu hızlı başlangıçta, mevcut bir Go uygulamasının MongoDB için Azure Cosmos DB API 'sine nasıl bağlanacağı gösterilmektedir.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: c9829b49662c90df685388691c04b201a7010eb8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765224"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Hızlı başlangıç: bir Go uygulamasını MongoDB için Azure Cosmos DB API 'sine bağlama
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB, genel dağıtım ve yatay ölçeklendirme özellikleri ile belge, tablo, anahtar değer ve grafik veritabanlarını hızlıca oluşturmanıza ve sorgulamanızı sağlayan çok modelli bir veritabanı hizmetidir. Bu hızlı başlangıçta, Azure Cloud Shell kullanarak bir Azure Cosmos DB hesabı oluşturup yönetebilir, GitHub 'dan mevcut bir örnek uygulamayı klonlayabilir ve Azure Cosmos DB ile çalışacak şekilde yapılandırırsınız. 

Örnek uygulama, go 'da yazılmış bir komut satırı tabanlı `todo` yönetim aracıdır. MongoDB için Azure Cosmos DB API 'SI, MongoDB [kablo protokolüyle uyumludur](./mongodb-introduction.md#wire-protocol-compatibility)ve bu, herhangi bir MongoDB istemci sürücüsünün bu sunucuya bağlanmasını mümkün hale getirir. Bu uygulama, [MongoDB Için Go sürücüsünü](https://github.com/mongodb/mongo-go-driver) , verilerin bir Azure Cosmos DB veritabanında depolandığı uygulamaya saydam bir şekilde kullanır.

## <a name="prerequisites"></a>Önkoşullar
- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free). Veya Azure aboneliği olmadan [ücretsiz Azure Cosmos DB deneyin](https://azure.microsoft.com/try/cosmosdb/) . [Azure Cosmos DB öykünücüsünü](https://aka.ms/cosmosdb-emulator) bağlantı dizesiyle de kullanabilirsiniz `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- Bilgisayarınızda yüklü ve çalışır bir go hakkında bilgi sahibi [olun](https://golang.org/) .
- [Git](https://git-scm.com/downloads).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Örnek depoyu kopyalamak için aşağıdaki komutları çalıştırın.

1. Bir komut istemi açın, adlı yeni bir klasör oluşturun `git-samples` ve ardından komut istemi ' ni kapatın.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere yeni bir klasör olarak değiştirmek için `cd` komutunu kullanın.

    ```bash
    cd "C:\git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Uygulamanın nasıl çalıştığını öğrenmek istiyorsanız aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Aksi takdirde, [Uygulamayı çalıştırmaya](#run-the-application)devam edebilirsiniz. Uygulama düzeni aşağıdaki gibidir:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

Aşağıdaki kod parçacıklarının tümü `todo.go` dosyasından alınmıştır.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Azure Cosmos DB’yi kullanarak Go uygulamasına bağlanma

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) bir ortam değişkeni kullanılarak geçirilen Azure Cosmos DB için bağlantı dizesini kapsüller (yaklaşan bölümde Ayrıntılar). Bağlantı, [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) `clientOptions` Örneğin geçirildiği kullanılarak başlatılır. başarılı bağlantıyı onaylamak için [ `Ping` işlev](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) çağrıldı (başarısız-hızlı bir stratejidir)

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> Yapılandırmanın kullanılması [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) önemlidir, ancak şu bağlantı hatasını alırsınız: `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Öğe oluşturma `todo`

Oluşturmak için `todo` , bir ' a yönelik bir tanıtıcı alır [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) ve işlevini çağırır [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) . 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

`Todo`Açıklamayı ve durumu içeren bir struct (başlangıçta olarak ayarlanır `pending` )

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>Liste `todo` öğeleri

Ölçütlere göre TODOs listeliyoruz. [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D)Filtre ölçütlerini kapsüllemek için bir oluşturulur

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) , filtre temelinde belge aramak için kullanılır ve sonuç bir dilime dönüştürülür `Todo`

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

Son olarak, bilgiler tablolu biçimde işlenir

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>Öğe güncelleştirme `todo`

`todo`, ' A göre güncelleştirilebilen olabilir `_id` . [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) `_id` Bu durumda yeni bir durum (veya) olan güncelleştirilmiş bilgiler için bir filtre oluşturulur `completed` `pending` . Son olarak, [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) işlev filtreyle ve güncelleştirilmiş belgeyle çağrılır

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>Sil `todo`

, Öğesine `todo` göre silinir `_id` ve bir örnek biçiminde kapsüllenir [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) . [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) belgeyi silmek için çağrılır.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>Uygulama oluşturma

Uygulamayı Klonladığınız dizine değiştirin ve oluşturun (kullanarak `go build` ).

```bash
cd monogdb-go-quickstart
go build -o todo
```

Uygulamanın düzgün şekilde oluşturulduğunu doğrulamak için.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Kurulum Azure Cosmos DB

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu konu başlığı için Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLı 'yı yüklemek]. 

Yüklü bir Azure CLı kullanıyorsanız, [az Login](/cli/azure/reference-index#az_login) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin. Azure Cloud Shell'i kullanıyorsanız bu adımı atlayabilirsiniz.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Azure Cosmos DB modülü ekleme

Yüklenen bir Azure CLI kullanıyorsanız `az` komutunu çalıştırarak `cosmosdb` bileşeninin zaten yüklü olup olmadığını kontrol edin. `cosmosdb`, temel komutlar listesindeyse bir sonraki komuta geçin. Azure Cloud Shell'i kullanıyorsanız bu adımı atlayabilirsiniz.

`cosmosdb`, temel komutlar listesinde değilse [Azure CLI](/cli/azure/install-azure-cli)'yi yeniden yükleyin.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[Az Group Create](/cli/azure/group#az_group_create)ile bir [kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun. Azure kaynak grubu; web uygulamaları, veritabanları ve depolama hesapları gibi Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

Aşağıdaki örnekte Batı Avrupa bölgesinde bir kaynak grubu oluşturulmaktadır. Kaynak grubu için benzersiz bir ad seçin.

Azure Cloud Shell kullanıyorsanız, **deneyin**' i seçin, oturum açmak için ekrandaki istemleri izleyin, sonra komutu komut istemine kopyalayın.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB hesabı oluşturma

[Az cosmosdb Create](/cli/azure/cosmosdb#az_cosmosdb_create) komutuyla bir Cosmos hesabı oluşturun.

Aşağıdaki komutta, lütfen yer tutucuyu gördüğünüz benzersiz Cosmos hesabınızın adını yerine koyun `<cosmosdb-name>` . Bu benzersiz ad Cosmos DB uç noktasının bir parçası olarak kullanılır `https://<cosmosdb-name>.documents.azure.com/` . bu nedenle, adın Azure 'daki tüm Cosmos hesaplarında benzersiz olması gerekir. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

`--kind MongoDB` parametresi MongoDB istemci bağlantılarını etkinleştirir.

Azure Cosmos DB hesabı oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir. 

> [!NOTE]
> Bu örnek, varsayılan Azure CLI çıktı biçimi olarak JSON kullanır. Başka bir çıktı biçimi kullanmak için bkz. [Azure CLI komutları için çıktı biçimleri](/cli/azure/format-output-azure-cli). 

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>Veritabanı anahtarını alma

Cosmos veritabanına bağlanmak için veritabanı anahtarına ihtiyacınız vardır. Birincil anahtarı almak için [az cosmosdb Keys List](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) komutunu kullanın.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI aşağıdaki örneğe benzer bilgiler çıkarır. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>Uygulamayı yapılandırma 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Bağlantı dizesini, MongoDB veritabanını ve koleksiyon adlarını ortam değişkenleri olarak dışa aktarın. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> `ssl=true`Cosmos DB gereksinimler nedeniyle bu seçenek önemlidir. Daha fazla bilgi için bkz. [bağlantı dizesi gereksinimleri](connect-mongodb-account.md#connection-string-requirements).
>

`MONGODB_CONNECTION_STRING`Ortam değişkeni için, ve için yer tutucuları değiştirin `<COSMOSDB_ACCOUNT_NAME>``<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: Oluşturduğunuz Azure Cosmos DB hesabının adı
2. `<COSMOSDB_PASSWORD>`: Önceki adımda ayıklanan veritabanı anahtarı

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

Ve için tercih ettiğiniz değerleri seçebilir `MONGODB_DATABASE` `MONGODB_COLLECTION` veya olduğu gibi bırakabilirsiniz.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Oluşturmak için `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

Başarılı olursa, yeni oluşturulan belgenin MongoDB ile bir çıktı görmeniz gerekir `_id` :

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Başka bir oluştur `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Tüm öğeleri Listele `todo`

```bash
./todo --list all
```

Yeni ekledikleriniz gibi tablolu bir biçimde eklemiş olduklarınızı görmeniz gerekir.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

Bir a 'nın durumunu güncelleştirmek için `todo` (ör. `completed` durumu durum olarak değiştirin), kimliği kullanın `todo`

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Yalnızca tamamlanan öğeleri Listele `todo`

```bash
./todo --list completed
```

Güncelleştirdiğiniz bir tane görmeniz gerekir

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Veri Gezgini’nde verileri görüntüleme

Azure Cosmos DB depolanan veriler, Azure portal görüntülemek ve sorgulamak için kullanılabilir.

Önceki adımda oluşturulan verileri görüntülemek, sorgulamak ve üzerinde çalışmak için web tarayıcınızda [Azure portalı](https://portal.azure.com) oturumunu açın.

Üst arama kutusuna **Azure Cosmos DB** girin. Cosmos hesabı dikey penceresi açıldığında, Cosmos hesabınızı seçin. Sol gezinti bölmesinde **Veri Gezgini**' yi seçin. Koleksiyonlar bölmesinde koleksiyonunuzu genişletin; bundan sonra koleksiyondaki belgeleri görüntüleyebilir, verileri sorgulayabilir ve hatta saklı yordam, tetikleyici ve UDF’ler oluşturup çalıştırabilirsiniz. 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="Yeni oluşturulan belgeyi görüntüleyen Veri Gezgini":::


KIMLIĞI kullanarak bir öğesini silme `todo`

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

`todo`Onaylanacak öğeleri listeleyin

```bash
./todo --list all
```

`todo`Yeni sildiğiniz yer bulunmamalıdır

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure Cloud Shell kullanarak bir Azure Cosmos DB MongoDB API hesabı oluşturmayı ve yönetmek için bir go komut satırı uygulaması oluşturmayı ve çalıştırmayı öğrendiniz `todo` . Şimdi Azure Cosmos DB hesabınıza ek veriler aktarabilirsiniz.

> [!div class="nextstepaction"]
> [Azure Cosmos DB’ye MongoDB verileri aktarma](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)