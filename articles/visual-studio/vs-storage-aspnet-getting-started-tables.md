---
title: Visual Studio kullanarak Azure Tablo depolama ile çalışmaya başlama (ASP.NET)
description: Visual Studio bağlı hizmetler 'i kullanarak bir depolama hesabına bağlandıktan sonra Visual Studio 'da bir ASP.NET projesinde Azure Tablo Depolamayı kullanmaya başlama
services: storage
author: ghogen
manager: jillfra
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ec391db0e18128ad85d3bacb79fa51770aa70ea1
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298886"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Azure Tablo depolama ve Visual Studio bağlı hizmetlerini kullanmaya başlama (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Genel Bakış

Azure Tablo depolaması, büyük miktarlarda yapılandırılmış verileri depolamanıza olanak sağlar. Hizmet, Azure bulutu içinden ve dışından kimliği doğrulanmış çağrıları kabul eden bir NoSQL veri deposu olur. Azure tabloları, yapılandırılmış ve ilişkisel olmayan verilerin depolanması için idealdir.

Bu öğreticide, Azure Tablo depolama varlıklarını kullanarak bazı yaygın senaryolar için ASP.NET kodu yazma gösterilmektedir. Bu senaryolar tablo oluşturma ve tablo varlıklarını ekleme, sorgulama ve silme içerir. 

## <a name="prerequisites"></a>Önkoşullar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure depolama hesabı](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC denetleyicisi oluşturma 

1. **Çözüm Gezgini**, **denetleyiciler**' e sağ tıklayın ve bağlam menüsünde, **Ekle-> denetleyicisi**' ni seçin.

    ![ASP.NET MVC uygulamasına denetleyici ekleme](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. **Yapı Iskelesi Ekle** Iletişim kutusunda **MVC 5 denetleyici-boş**öğesini seçin ve **Ekle**' yi seçin.

    ![MVC denetleyici türünü belirtin](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. **Denetleyici Ekle** iletişim kutusunda denetleyiciyi *tablescontroller*olarak adlandırın ve **Ekle**' yi seçin.

    ![MVC denetleyicisini adlandırın](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Aşağıdaki *using* yönergelerini `TablesController.cs` dosyasına ekleyin:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Model sınıfı oluşturma

Bu makaledeki örneklerin birçoğu, **customerentity**adlı **tableentity**ile türetilmiş bir sınıfı kullanır. Aşağıdaki adımlar, bu sınıfı bir model sınıfı olarak bildirirken size rehberlik gösterir:

1. **Çözüm Gezgini** **modeller**' a sağ tıklayın ve bağlam menüsünde, **Add-> Class**' ı seçin.

1. **Yeni öğe Ekle** iletişim kutusunda, **customerentity**sınıfını adlandırın.

1. @No__t-0 dosyasını açın ve aşağıdaki **using** yönergesini ekleyin:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Sınıfı, tamamlandığında, sınıf aşağıdaki kodda olarak bildirildiği şekilde değiştirin. Sınıfı, satır anahtarı olarak müşterinin adını ve bölüm anahtarı olarak soyadı ' nı kullanan **customerentity** adlı bir varlık sınıfını bildirir.

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>Bir tablo oluşturma

Aşağıdaki adımlarda bir tablonun nasıl oluşturulacağı gösterilmektedir:

> [!NOTE]
> 
> Bu bölümde, [geliştirme ortamını ayarlama](#set-up-the-development-environment)adımlarını tamamladığınız varsayılmaktadır. 

1. `TablesController.cs` dosyasını açın.

1. **Eylem sonucu**döndüren **CreateTable** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **CreateTable** yöntemi içinde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. **Cloudtableclient** nesnesi Get bir tablo hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. İstenen tablo adına bir başvuruyu temsil eden bir **Cloudtable** nesnesi alın. **Cloudtableclient. GetTableReference** yöntemi tablo depolamaya karşı bir istek yapmaz. Başvuru, tablonun var olup olmadığına bakılmaksızın döndürülür. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Henüz yoksa tablo oluşturmak için **Cloudtable. CreateIfNotExists** yöntemini çağırın. **Cloudtable. CreateIfNotExists** yöntemi, tablo yoksa **true** değerini döndürür ve başarıyla oluşturulur. Aksi takdirde, **false** döndürülür.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. **ViewBag** öğesini tablonun adıyla güncelleştirin.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Tablolar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda, görünüm adı Için **CreateTable** girin ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **tablo oluştur** ' u seçin:
  
    ![Tablo oluşturma](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Daha önce belirtildiği gibi **Cloudtable. CreateIfNotExists** yöntemi yalnızca tablo mevcut olmadığında ve oluşturulduğunda **true** değerini döndürür. Bu nedenle, uygulamayı tablo varken çalıştırırsanız, yöntem **false**döndürür. Uygulamayı birden çok kez çalıştırmak için, uygulamayı yeniden çalıştırmadan önce tabloyu silmeniz gerekir. Tablo silindiğinde **Cloudtable. Delete** yöntemi aracılığıyla yapılabilir. Tabloyu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) veya [Microsoft Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md)kullanarak da silebilirsiniz.  

## <a name="add-an-entity-to-a-table"></a>Tabloya bir varlık ekleme

*Varlıklar* , **tableentity**'dan türetilmiş özel bir sınıf kullanarak C @ no__t-1 nesnelerine eşlenir. Tabloya bir varlık eklemek için varlığınızın özelliklerini tanımlayan bir sınıf oluşturun. Bu bölümde, satır anahtarı olarak müşterinin adını ve bölüm anahtarı olarak soyadı ' nı kullanan bir varlık sınıfının nasıl tanımlanacağını göreceksiniz. Birlikte, bir varlığın bölüm ve sıra anahtarı varlığı tabloda benzersiz şekilde tanımlar. Aynı bölüm anahtarına sahip varlıklar farklı bölüm anahtarlı varlıklara göre daha hızlı sorgulanabilir ancak farklı bölüm anahtarlarının kullanılması paralel işlemler için daha büyük ölçeklendirme sağlar. Tablo hizmetinde depolanması gereken tüm özellikler için, özelliği, hem ayar hem de alma değerlerini kullanıma sunan desteklenen bir türün ortak özelliği olmalıdır.
Varlık *sınıfı ortak* parametre-daha az bir Oluşturucu bildirmelidir.

> [!NOTE]
> 
> Bu bölümde, [geliştirme ortamını ayarlama](#set-up-the-development-environment)adımlarını tamamladığınız varsayılmaktadır.

1. `TablesController.cs` dosyasını açın.

1. @No__t-0 dosyasındaki kodun **Customerentity** sınıfına erişebilmesi için aşağıdaki yönergeyi ekleyin:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Bir **ActionResult**döndüren **AddEntity** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **AddEntity** yöntemi içinde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. **Cloudtableclient** nesnesi Get bir tablo hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Yeni varlığı ekleyeceğiniz tabloya bir başvuruyu temsil eden bir **Cloudtable** nesnesi alın. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **Customerentity** sınıfını oluşturun ve başlatın.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Müşteri varlığını ekleyen bir **Tableoperation** nesnesi oluşturun.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. **Cloudtable. Execute** yöntemini çağırarak ekleme işlemini yürütün. **Tableresult. HttpStatusCode** özelliğini inceleyerek işlemin sonucunu doğrulayabilirsiniz. 2xx durum kodu, istemci tarafından istenen eylemin başarıyla işlendiğini gösterir. Örneğin, yeni varlıkların başarılı eklemeleri 204 HTTP durum koduna neden olur, yani işlemin başarıyla işlendiği ve sunucunun herhangi bir içerik döndürmediği anlamına gelir.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. **Görünüm çantasından** tablo adını ve ekleme işleminin sonuçlarını güncelleştirin.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Tablolar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda Görünüm adı Için **AddEntity** girin ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **varlık Ekle** ' yi seçin:
  
    ![Varlık ekleme](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Bölümündeki adımları izleyerek varlığın eklendiğini doğrulayabilirsiniz, [tek bir varlık alın](#get-a-single-entity). Tablolarınızın tüm varlıklarını görüntülemek için [Microsoft Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md) de kullanabilirsiniz.

## <a name="add-a-batch-of-entities-to-a-table"></a>Tabloya bir varlık toplu işi ekleme

[Bir tabloya tek seferde bir varlık ekleyebilmenin](#add-an-entity-to-a-table)yanı sıra, toplu işteki varlıklar da ekleyebilirsiniz. Toplu iş içinde varlık eklemek, kodunuz ve Azure Tablo hizmeti arasındaki gidiş dönüşlerin sayısını azaltır. Aşağıdaki adımlarda, tek bir ekleme işlemi ile bir tabloya birden çok varlık ekleme gösterilmektedir:

> [!NOTE]
> 
> Bu bölümde, [geliştirme ortamını ayarlama](#set-up-the-development-environment)adımlarını tamamladığınız varsayılmaktadır.

1. `TablesController.cs` dosyasını açın.

1. **ActionResult**döndüren **addentities** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **Addentities** yöntemi içinde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. **Cloudtableclient** nesnesi Get bir tablo hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Yeni varlıkları ekleyeceğiniz tabloya bir başvuruyu temsil eden bir **cloudtable** nesnesi alın. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Bölümünde sunulan **customerentity** model sınıfına göre bazı müşteri nesneleri örneği oluşturun, [tabloya bir varlık ekleyin](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Bir **Tablebatchoperation** nesnesi alın.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Toplu ekleme işlemi nesnesine varlık ekleyin.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. **Cloudtable. ExecuteBatch** yöntemini çağırarak Batch ekleme işlemini yürütün.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. **Cloudtable. ExecuteBatch** yöntemi her bir **tableresult** nesnesinin, her bir işlemin başarısını veya başarısızlığını tespit etmek üzere incelenbileceği bir **tableresult** nesnelerinin listesini döndürür. Bu örnek için listeyi bir görünüme geçirin ve görünümün her bir işlemin sonuçlarını görüntülemesine izin verin. 
 
    ```csharp
    return View(results);
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Tablolar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda Görünüm adı Için **addentities** yazın ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki gibi görünmesi için değiştirin.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **varlık Ekle** ' yi seçin:
  
    ![Varlık ekleme](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Bölümündeki adımları izleyerek varlığın eklendiğini doğrulayabilirsiniz, [tek bir varlık alın](#get-a-single-entity). Tablolarınızın tüm varlıklarını görüntülemek için [Microsoft Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md) de kullanabilirsiniz.

## <a name="get-a-single-entity"></a>Tek bir varlık alın

Bu bölüm, varlığın satır anahtarını ve bölüm anahtarını kullanarak bir tablodan tek bir varlığın nasıl alınacağını gösterir. 

> [!NOTE]
> 
> Bu bölüm, [geliştirme ortamını ayarlama](#set-up-the-development-environment)içindeki adımları tamamladığınız ve [bir tabloya bir varlık toplu işi ekleme](#add-a-batch-of-entities-to-a-table)içindeki verileri kullanmakta olduğunuz varsayılmaktadır. 

1. `TablesController.cs` dosyasını açın.

1. Bir **ActionResult**döndüren **GetSingle** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **GetSingle** yöntemi içinde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. **Cloudtableclient** nesnesi Get bir tablo hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Varlığı aldığınız tabloya yönelik bir başvuruyu temsil eden bir **Cloudtable** nesnesi alın. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **Tableentity**'dan türetilmiş bir varlık nesnesi alan bir alma işlemi nesnesi oluşturun. İlk parametre *Partitionkey*, ikinci parametresi Ise *rowkey*olur. [Bir tabloya bir varlık toplu Işi ekleme](#add-a-batch-of-entities-to-a-table)bölümünde sunulan **customerentity** sınıfını ve verilerini kullanarak, aşağıdaki kod parçacığı, "Smith" ve bir, *partitionkey* değeri "Smith" **ve a**"Ben" öğesinin rowKey değeri:

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Alma işlemini yürütün.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Sonucu görüntüleme görünümüne geçirin.

    ```csharp
    return View(result);
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Tablolar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda, görünüm adı Için **GetSingle** girin ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **tek tek al** ' ı seçin:
  
    ![Single al](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Bir bölümdeki tüm varlıkları al

Bölümünde belirtildiği gibi, tabloya bir [varlık ekleyin](#add-an-entity-to-a-table), bölüm ve satır anahtarı birleşimi bir tablodaki varlığı benzersiz şekilde tanımlar. Aynı bölüm anahtarına sahip varlıklar, farklı bölüm anahtarları olan varlıklardan daha hızlı sorgulanabilir. Bu bölümde, belirli bir bölümdeki tüm varlıklar için bir tabloyu sorgulama gösterilmektedir.  

> [!NOTE]
> 
> Bu bölüm, [geliştirme ortamını ayarlama](#set-up-the-development-environment)içindeki adımları tamamladığınız ve [bir tabloya bir varlık toplu işi ekleme](#add-a-batch-of-entities-to-a-table)içindeki verileri kullanmakta olduğunuz varsayılmaktadır. 

1. `TablesController.cs` dosyasını açın.

1. **ActionResult**döndüren **getpartition** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **Getpartition** yöntemi içinde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. **Cloudtableclient** nesnesi Get bir tablo hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Varlıkları aldığınız tabloya yönelik bir başvuruyu temsil eden bir **Cloudtable** nesnesi elde edin. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **WHERE** yan tümcesinde sorguyu belirten bir **tablequery** nesnesi örneği oluşturun. [Bir tabloya bir varlık toplu Işi ekleme](#add-a-batch-of-entities-to-a-table)bölümünde sunulan **customerentity** sınıfını ve verilerini kullanarak, aşağıdaki kod parçacığı, **partitionkey** (müşterinin son adı) "Smith" değerine sahip tüm varlıklar için tabloyu sorgular:

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Bir döngü içinde, önceki adımda örnekettiğiniz sorgu nesnesini geçirerek **Cloudtable. Executequerykesimli** yöntemini çağırın.  **Cloudtable. Executequerykesimli** yöntemi, **null** olduğunda, alınacak daha fazla varlık olmadığını gösteren bir **tablecontinuationtoken** nesnesi döndürür. Döngü içinde, döndürülen varlıkları yinelemek için başka bir döngü kullanın. Aşağıdaki kod örneğinde döndürülen her varlık bir listeye eklenir. Döngü sona erdikten sonra liste, görüntüleme için bir görünüme geçirilir: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Tablolar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda Görünüm adı Için **getpartition** yazın ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **bölüm al** ' ı seçin:
  
    ![Bölüm al](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Bir varlığı silme

Bu bölümde bir varlığın bir tablodan nasıl silineceği gösterilmektedir.

> [!NOTE]
> 
> Bu bölüm, [geliştirme ortamını ayarlama](#set-up-the-development-environment)içindeki adımları tamamladığınız ve [bir tabloya bir varlık toplu işi ekleme](#add-a-batch-of-entities-to-a-table)içindeki verileri kullanmakta olduğunuz varsayılmaktadır. 

1. `TablesController.cs` dosyasını açın.

1. Bir **ActionResult**döndüren **deleteentity** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **Deleteentity** yönteminde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. **Cloudtableclient** nesnesi Get bir tablo hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Varlığı sildiğiniz tabloya bir başvuruyu temsil eden bir **Cloudtable** nesnesi alın. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. **Tableentity**'dan türetilmiş bir varlık nesnesi alan silme işlemi nesnesi oluşturun. Bu durumda, [bir tabloya bir varlık toplu Işi ekleme](#add-a-batch-of-entities-to-a-table)bölümünde sunulan **customerentity** sınıfını ve verileri kullanırız. Varlığın **ETag** 'i geçerli bir değer olarak ayarlanmalıdır.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Silme işlemini yürütün.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Sonucu görüntüleme görünümüne geçirin.

    ```csharp
    return View(result);
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Tablolar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda, görünüm adı Için **deleteentity** yazın ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **varlığı Sil** ' i seçin:
  
    ![Single al](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Sonraki adımlar
Azure’da veri depolama ile ilgili ek seçenekler hakkında daha fazla bilgi edinmek için daha fazla özellik kılavuzu görüntüleyin.

  * [Azure Blob depolama ve Visual Studio bağlı hizmetlerini kullanmaya başlama (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Azure kuyruk depolama ve Visual Studio bağlı hizmetler 'i kullanmaya başlama (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
