---
title: .NET v4 SDK kullanarak Azure Cosmos DB SQL API kaynaklarını yönetme
description: Azure Cosmos DB SQL API hesabı kaynaklarını yönetmek için .NET v4 SDK 'sını kullanarak bir konsol uygulaması derlemek için hızlı başlangıç.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/07/2021
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.openlocfilehash: 6d3728596842702f20a96ca568ecd1a16d20c4d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873874"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Hızlı başlangıç: Azure Cosmos DB SQL API hesabı kaynaklarını yönetmek için .NET v4 SDK 'sını (Önizleme) kullanarak bir konsol uygulaması oluşturun.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK’sı v4](create-sql-api-java.md)
> * [Spring Verileri v3](create-sql-api-spring-data.md)
> * [Spark v3 Bağlayıcısı](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

.NET için Azure Cosmos DB SQL API istemci kitaplığı ile çalışmaya başlayın. .NET v4 (Azure. Cosmos) paketini yüklemek, bir uygulama derlemek ve Azure Cosmos DB depolanan verilerdeki temel CRUD işlemleri için örnek kodu denemek üzere bu belgedeki adımları izleyin.

> [!IMPORTANT]
> Azure Cosmos DB için .NET v4 SDK 'Sı Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB, tüm ölçeklendirmeye yönelik açık API 'lerle birlikte Microsoftâ €™ s hızlı NoSQL veritabanıdır. Anahtar/değer, belge ve grafik veritabanlarını hızlıca oluşturmak ve sorgulamak için Azure Cosmos DB kullanabilirsiniz. .NET için Azure Cosmos DB SQL API istemci kitaplığı 'nı kullanarak şunları yapın:

* Azure Cosmos veritabanı ve kapsayıcısı oluşturma
* Kapsayıcıya örnek veri ekleme
* Verileri sorgulama 
* Veritabanını silme

[Kitaplık kaynak kodu](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/) veya Azure aboneliği olmadan [ücretsiz Azure Cosmos DB deneyebilir](https://azure.microsoft.com/try/cosmosdb/) , ücretsiz ve taahhütlere sahip olabilirsiniz. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Çalıştırıp ortamınızda hangi sürümün kullanılabilir olduğunu doğrulayabilirsiniz `dotnet --version` .

## <a name="setting-up"></a>Ayarlanıyor

Bu bölümde, kaynakları yönetmek için .NET için Azure Cosmos DB SQL API istemci kitaplığı 'nı kullanan bir projeyi ayarlama ve Azure Cosmos hesabı oluşturma işlemi adım adım açıklanmaktadır. Bu makalede açıklanan örnek kod, bu `FamilyDatabase` veritabanı içindeki bir veritabanı ve aile üyelerini (her aile üyesi bir öğedir) oluşturur. Her aile üyesinin gibi özellikleri vardır `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . `LastName`Özelliği kapsayıcı için bölüm anahtarı olarak kullanılır. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Azure Cosmos hesabı oluşturma

Azure Cosmos hesabı oluşturmak için [ücretsiz deneme Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) seçeneğini kullanırsanız, **SQL apı** türünde bir Azure Cosmos DB hesabı oluşturmanız gerekir. Sizin için bir Azure Cosmos DB test hesabı zaten oluşturulmuş. Hesabı açıkça oluşturmanız gerekmez; bu sayede bu bölümü atlayabilir ve sonraki bölüme geçebilirsiniz.

Kendi Azure aboneliğiniz varsa veya ücretsiz bir abonelik oluşturduysanız, açıkça bir Azure Cosmos hesabı oluşturmanız gerekir. Aşağıdaki kod, oturum tutarlılığı olan bir Azure Cosmos hesabı oluşturacaktır. Hesap ve ' de çoğaltılır `South Central US` `North Central US` .  

Azure Cosmos hesabını oluşturmak için Azure Cloud Shell kullanabilirsiniz. Azure Cloud Shell, Azure kaynaklarını yönetmek için kullanabileceğiniz etkileşimli, kimliği doğrulanmış ve tarayıcı ile erişilebilen bir kabuktur. Bu hizmet, Bash veya PowerShell ile çalışma şeklinize en uygun kabuk deneyimini seçme esnekliği getirir. Bu hızlı başlangıçta **Bash** modu ' nu seçin. Azure Cloud Shell Ayrıca bir depolama hesabı gerektirir, istendiğinde bir tane oluşturabilirsiniz.

Aşağıdaki kodun yanındaki **deneyin** düğmesini seçin, **Bash** modu ' nu seçin, **depolama hesabı oluştur** ' u seçin ve Cloud Shell için oturum açın. Sonra Azure Cloud Shell ve çalıştırmak için aşağıdaki kodu yapıştırın. Azure Cosmos hesap adı genel olarak benzersiz olmalıdır, `mysqlapicosmosdb` komutu çalıştırmadan önce değeri güncelleştirdiğinizden emin olun.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Azure Cosmos hesabını oluşturma işlemi biraz zaman alır, işlem başarılı olduktan sonra onay çıkışını görebilirsiniz. Komut başarıyla tamamlandıktan sonra, [Azure Portal](https://portal.azure.com/) oturum açın ve belirtilen ada sahip Azure Cosmos hesabının mevcut olduğundan emin olun. Kaynak oluşturulduktan sonra Azure Cloud Shell penceresini kapatabilirsiniz. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Yeni bir .NET uygulaması oluşturun

Tercih ettiğiniz düzenleyicide veya IDE 'de yeni bir .NET uygulaması oluşturun. Yerel bilgisayarınızdan Windows komut istemi veya bir Terminal penceresi açın. Komut istemi veya terminalden sonraki bölümlerde bulunan tüm komutları çalıştıracaksınız.  Adında yeni bir uygulama oluşturmak için aşağıdaki DotNet New komutunu çalıştırın `todo` . --LangVersion parametresi, oluşturulan proje dosyasındaki LangVersion özelliğini ayarlar.

   ```bash
   dotnet new console â€“langVersion:8 -n todo
   ```

Dizininizi yeni oluşturulan uygulama klasörüyle değiştirin. Uygulamayı ile oluşturabilirsiniz:

   ```bash
   cd todo
   dotnet build
   ```

Derlemeden beklenen çıktı şuna benzemelidir:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Azure Cosmos DB paketini yükler

Hala uygulama dizininde, DotNet Add Package komutunu kullanarak .NET Core için Azure Cosmos DB istemci kitaplığını yükleyebilirsiniz.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Azure Cosmos hesabı kimlik bilgilerinizi Azure portal kopyalayın

Örnek uygulamanın Azure Cosmos hesabınızda kimlik doğrulaması yapması gerekir. Kimlik doğrulaması yapmak için Azure Cosmos hesabı kimlik bilgilerini uygulamaya geçirmeniz gerekir. Aşağıdaki adımları izleyerek Azure Cosmos hesabı kimlik bilgilerinizi alın:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Azure Cosmos hesabınıza gidin.

1. **Anahtarlar** bölmesini açın ve hesabınızın **URI** ve **birincil anahtarını** kopyalayın. Bir sonraki adımda bir ortam değişkenine URI ve anahtar değerleri ekleyeceksiniz.

## <a name="object-model"></a><a id="object-model"></a>Nesne modeli

Uygulamayı oluşturmaya başlamadan önce, Azure Cosmos DB içindeki kaynak hiyerarşisine ve bu kaynakları oluşturmak ve bunlara erişmek için kullanılan nesne modeline göz atalım. Azure Cosmos DB, kaynakları aşağıdaki sırayla oluşturur:

* Azure Cosmos hesabı 
* Veritabanları 
* Kapsayıcılar 
* Öğeler

Farklı varlıkların hiyerarşisi hakkında daha fazla bilgi edinmek için, [Azure Cosmos DB makalesinde veritabanları, kapsayıcılar ve öğelerle çalışma](account-databases-containers-items.md) makalesine bakın. Şu kaynaklarla etkileşim kurmak için aşağıdaki .NET sınıflarını kullanacaksınız:

* CosmosClient-Bu sınıf Azure Cosmos DB hizmeti için istemci tarafı mantıksal temsili sağlar. İstemci nesnesi, hizmete yönelik istekleri yapılandırmak ve yürütmek için kullanılır.
* CreateDatabaseIfNotExistsAsync-bu Yöntem (yoksa) oluşturur veya bir veritabanı kaynağını zaman uyumsuz bir işlem olarak alır (varsa). 
* Createcontainerıfnotexistsasync-bu Yöntem (yoksa) oluşturur veya bir kapsayıcıyı zaman uyumsuz bir işlem olarak (zaten varsa) alır. Kapsayıcının yeni oluşturulup oluşturulmayacağını (201) veya var olan bir kapsayıcının döndürülüp döndürülmediğini (200) öğrenmek için yanıttan durum kodunu kontrol edebilirsiniz. 
* CreateItemAsync-bu yöntem kapsayıcı içinde bir öğe oluşturur.
* UpsertItemAsync-bu yöntem, zaten yoksa kapsayıcı içinde bir öğe oluşturur veya zaten varsa öğeyi değiştirir. 
* Getıtemqueryyineleyici-bu yöntem, parametreli değerler içeren bir SQL ifadesini kullanarak bir Azure Cosmos veritabanındaki kapsayıcı altındaki öğeler için bir sorgu oluşturur. 
* DeleteAsync-belirtilen veritabanını Azure Cosmos hesabınızdan siler. `DeleteAsync` Yöntem yalnızca veritabanını siler.

 ## <a name="code-examples"></a><a id="code-examples"></a>Kod örnekleri

Bu makalede açıklanan örnek kod, Azure Cosmos DB bir aile veritabanı oluşturur. Aile veritabanı ad, adres, konum, ilişkili üst öğeler, alt öğeler ve Evcil hayvan gibi aile ayrıntılarını içerir. Verileri Azure Cosmos hesabınıza doldurmadan önce, bir aile öğesinin özelliklerini tanımlayın. Örnek uygulamanızın kök düzeyinde adlı yeni bir sınıf oluşturun `Family.cs` ve buna aşağıdaki kodu ekleyin:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>İstemci nesnesini tanımlamak & using yönergelerini ekleyin

Proje dizininden `Program.cs` dosyayı Düzenleyicinizde açın ve uygulamanızın en üstüne aşağıdaki kullanım yönergelerini ekleyin:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Sınıfınıza aşağıdaki genel değişkenleri ekleyin `Program` . Bunlar, uç nokta ve yetkilendirme anahtarlarını, veritabanının adını ve oluşturacağınız kapsayıcıyı içerir. Uç nokta ve yetkilendirme anahtarları değerlerini ortamınıza göre değiştirdiğinizden emin olun. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Son olarak, `Main` yöntemini değiştirin:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Veritabanı oluşturma 

`CreateDatabaseAsync`Sınıfında yöntemini tanımlayın `program.cs` . Bu yöntem, `FamilyDatabase` zaten yoksa oluşturur.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Kapsayıcı oluşturma

`CreateContainerAsync`Sınıfında yöntemini tanımlayın `Program` . Bu yöntem, `FamilyContainer` zaten yoksa oluşturur. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Öğe oluşturma

Aşağıdaki kodla metodunu ekleyerek bir aile öğesi oluşturun `AddItemsToContainerAsync` . `CreateItemAsync` `UpsertItemAsync` Bir öğe oluşturmak için veya yöntemlerini kullanabilirsiniz:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Öğeleri sorgulama

Bir öğeyi ekledikten sonra, "Andersen" ailesinin ayrıntılarını almak için bir sorgu çalıştırabilirsiniz. Aşağıdaki kod, SQL sorgusunu doğrudan kullanarak sorgunun nasıl yürütüleceğini gösterir. "Anderson" Aile ayrıntılarını almak için SQL sorgusu: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . `QueryItemsAsync`Sınıfında yöntemi tanımlayın `Program` ve aşağıdaki kodu buna ekleyin:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Bir öğeyi değiştirme 

Bir aile öğesini okuyun ve `ReplaceFamilyItemAsync` yöntemi aşağıdaki kodla ekleyerek güncelleştirin.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Öğeyi silme 

Aşağıdaki kodla yöntemini ekleyerek bir aile öğesini silin `DeleteFamilyItemAsync` .

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Veritabanını silme 

Son olarak, `DeleteDatabaseAndCleanupAsync` aşağıdaki kodla yöntemini ekleyerek veritabanını silebilirsiniz:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Gerekli tüm yöntemleri ekledikten sonra `Program` dosyayı kaydedin. 

## <a name="run-the-code"></a>Kodu çalıştırma

Sonra Azure Cosmos DB kaynaklarını oluşturmak için uygulamayı derleyin ve çalıştırın.

   ```bash
   dotnet run
   ```

Uygulamayı çalıştırdığınızda aşağıdaki çıktı oluşturulur. Ayrıca Azure portal oturum açabilir ve kaynakların oluşturulduğunu doğrulayabilirsiniz:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Azure portal oturum açıp Azure Cosmos hesabınızda gerekli öğeleri görmek için verilerin oluşturulduğunu doğrulayabilirsiniz. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmayan Azure Cosmos hesabını ve ilgili kaynak grubunu kaldırmak için Azure CLı veya Azure PowerShell kullanabilirsiniz. Aşağıdaki komut, Azure CLı kullanılarak kaynak grubunun nasıl silineceğini göstermektedir:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Azure Cosmos hesabı oluşturmayı, bir veritabanı ve bir .NET Core uygulamasını kullanarak kapsayıcıyı oluşturmayı öğrendiniz. Artık aşağıdaki makaledeki yönergeler int ile Azure Cosmos hesabınıza ek veri aktarabilirsiniz. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB hesabınıza veri aktarma](import-data.md)
