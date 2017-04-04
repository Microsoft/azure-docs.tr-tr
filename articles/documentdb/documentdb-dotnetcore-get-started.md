---
title: "NoSQL öğreticisi: DocumentDB .NET Core SDK | Microsoft Docs"
description: "DocumentDB .NET Core SDK&quot;yı kullanarak çevrimiçi bir veritabanı ve C# konsol uygulaması oluşturan bir NoSQL öğreticisi. DocumentDB, JSON için bir NoSQL veritabanıdır."
services: documentdb
documentationcenter: .net
author: arramac
manager: jhubbard
editor: 
ms.assetid: 9f93e276-9936-4efb-a534-a9889fa7c7d2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/23/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: c8a915055318697ade229837653df4c105279299
ms.lasthandoff: 03/24/2017


---
# <a name="nosql-tutorial-build-a-documentdb-c-console-application-on-net-core"></a>NoSQL öğreticisi: .NET Core’da DocumentDB C# konsol uygulaması oluşturma
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [MongoDB için Node.js](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Azure DocumentDB .NET Core SDK için NoSQL öğreticisine hoş geldiniz! Bu öğreticiyi uyguladıktan sonra, DocumentDB kaynaklarını oluşturan ve sorgulayan bir konsol uygulamasına sahip olacaksınız.

Şu konulara değineceğiz:

* DocumentDB hesabı oluşturma ve DocumentDB hesabına bağlanma
* Visual Studio Çözümünüzü yapılandırma
* Çevrimiçi bir veritabanı oluşturma
* Koleksiyon oluşturma
* JSON belgeleri oluşturma
* Koleksiyonu sorgulama
* Bir belgeyi değiştirme
* Bir belgeyi silme
* Veritabanını silme

Zamanınız yok mu? Endişelenmeyin! Eksiksiz çözümü [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started)'da bulabilirsiniz. Hızlı yönergeler için [Tam çözümü edinme bölümüne](#GetSolution) atlayın.

DocumentDB .NET Core SDK’yı kullanarak bir Xamarin iOS, Android, veya Forms uygulaması mı derlemek istiyorsunuz? Bkz. [DocumentDB kullanarak Xamarin mobil uygulamaları geliştirme](documentdb-mobile-apps-with-xamarin.md).

Ardından bize geri bildirim sağlamak için lütfen bu sayfanın üst veya alt kısmındaki oylama düğmelerini kullanın. Doğrudan sizinle iletişim kurmamızı isterseniz yorumlarınıza e-posta adresinizi ekleyin.

> [!NOTE]
> Bu öğreticide kullanılan DocumentDB .NET Core SDK'sı, henüz Evrensel Windows Platformu (UWP) uygulamalarıyla uyumlu değildir. .NET Core SDK'sının UWP uygulamalarını destekleyen bir önizleme sürümü için [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) adresine e-posta gönderin.

Şimdi başlayalım!

## <a name="prerequisites"></a>Önkoşullar
Lütfen aşağıdakilere sahip olduğunuzdan emin olun:

* Etkin bir Azure hesabı. Bir aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) için kaydolabilirsiniz. 
    * Alternatif olarak bu öğretici için [Azure DocumentDB Öykünücüsü](documentdb-nosql-local-emulator.md)’nü kullanabilirsiniz.
* [Visual Studio 2017](https://www.visualstudio.com/vs/) 
    * MacOS veya Linux’ta çalışıyorsanız tercih ettiğiniz platforma yönelik [.NET Core SDK](https://www.microsoft.com/net/core#macos)’yı yükleyerek komut satırından .NET Core uygulamaları geliştirebilirsiniz. 
    * Windows’da çalışıyorsanız [.NET Core SDK](https://www.microsoft.com/net/core#windows)’yı yükleyerek komut satırından .NET Core uygulamaları geliştirebilirsiniz. 
    * Kendi düzenleyicinizi kullanabilir ya da ücretsiz olan ve Windows, Linux ve MacOS’de çalışan [Visual Studio Code](https://code.visualstudio.com/)’u indirebilirsiniz. 

## <a name="step-1-create-a-documentdb-account"></a>1. Adım: DocumentDB hesabı oluşturma
Bir DocumentDB hesabı oluşturalım. Kullanmak istediğiniz bir hesap zaten varsa [Visual Studio Çözümünüzü Kurma](#SetupVS)'ya atlayabilirsiniz. DocumentDB Öykünücüsü’nü kullanıyorsanız öykünücünün kurulumunu gerçekleştirmek için lütfen [Azure DocumentDB Öykünücüsü](documentdb-nosql-local-emulator.md) konusundaki adımları izleyin ve [Visual Studio Çözümünüzü Ayarlama](#SetupVS) adımına atlayın.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupVS"></a>2. Adım: Visual Studio çözümünüzü kurma
1. Bilgisayarınızda **Visual Studio 2017**'yi açın.
2. **Dosya** menüsünde **Yeni**'yi seçin ve ardından **Proje**'yi seçin.
3. **Yeni Proje** iletişim kutusunda **Şablonlar** / **Visual C#** / **.NET Core**/**Konsol Uygulaması (.NET Core)** seçeneğini belirleyin, projenizi **DocumentDBGettingStarted** olarak adlandırın ve **Tamam**’a tıklayın.

   ![Yeni Proje penceresinin ekran görüntüsü](./media/documentdb-dotnetcore-get-started/nosql-tutorial-new-project-2.png)
4. **Çözüm Gezgini** içinde, **DocumentDBGettingStarted**’a sağ tıklayın.
5. Ardından, menüden çıkmadan **NuGet Paketlerini Yönet...** öğesine tıklayın.

   ![Proje için Sağ Tıklama Menüsünün ekran görüntüsü](./media/documentdb-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. **NuGet** sekmesinde, pencerenin üst kısmındaki **Gözat**'a tıklayın ve arama kutusuna **azure documentdb** yazın.
7. Sonuçlardan **Microsoft.Azure.DocumentDB.Core** seçeneğini bulup **Yükle**’ye tıklayın.
   .NET Core için DocumentDB İstemci Kitaplığı’nın paket kimliği [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)’dur. Bu .NET Core NuGet paketi tarafından desteklenmeyen bir .NET Framework sürümünü (net461 gibi) hedefliyorsanız, .NET Framework 4.5’ten itibaren tüm .NET Framework sürümlerini destekleyen [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)’yi kullanın.
8. Komut istemlerinde, NuGet paket yüklemelerini ve lisans sözleşmesini kabul edin.

Harika! Kurulumu tamamladığımıza göre, biraz kod yazmaya başlayalım. Bu öğreticinin tamamlanmış kod projesini [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started)'da bulabilirsiniz.

## <a id="Connect"></a>3. Adım: DocumentDB hesabına bağlanma
İlk olarak, Program.cs dosyasında C# uygulamanızın başlangıcına bu başvuruları ekleyin:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

> [!IMPORTANT]
> Bu NoSQL öğreticisini tamamlamak için, yukarıdaki bağımlılıkları eklediğinizden emin olun.

Şimdi bu iki sabiti ve *client* değişkeninizi *Program* ortak sınıfının altına ekleyin.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Ardından, URI ve birincil anahtarınızı almak için [Azure Portal](https://portal.azure.com)'a gidin. DocumentDB URI ve birincil anahtar, uygulamanızın nereye bağlanacağını anlaması ve DocumentDB'nin uygulamanızın bağlantısına güvenmesi için gereklidir.

Azure Portal'da DocumentDB hesabınıza gidin ve ardından **Anahtarlar**’a tıklayın.

Portaldaki URI’yi kopyalayın ve program.cs dosyasındaki `<your endpoint URI>` içine yapıştırın. Ardından portaldan BİRİNCİL ANAHTARI kopyalayın ve `<your key>` içine yapıştırın. Azure DocumentDB Öykünücüsü’nü kullanıyorsanız uç nokta olarak `https://localhost:8081` değerini ve [DocumentDB Öykünücüsü’nü kullanarak geliştirme](documentdb-nosql-local-emulator.md) bölümünden elde edilen iyi tanımlanmış yetkilendirme anahtarını kullanın. < ve > işaretini kaldırdığınızdan, ancak uç noktası ve anahtarın başı ile sonundaki çift tırnağı bıraktığınızdan emin olun.

![Bir C# konsol uygulaması oluşturmak için NoSQL öğreticisi tarafından kullanılan Azure Portal'ın ekran görüntüsü DocumentDB hesabı dikey penceresinde ANAHTARLAR düğmesi vurgulanmış, ETKİN hub'ı vurgulanmış ve Anahtarlar dikey penceresinde URI, BİRİNCİL ANAHTAR ve İKİNCİL ANAHTAR değerleri vurgulanmış bir DocumentDB hesabını gösterir][keys]

**DocumentClient**'ın yeni bir örneğini oluşturarak başlarken uygulamasına başlayacağız.

**Main** yönteminin altına yeni **DocumentClient**'ımızın örneğini oluşturacak **GetStartedDemo** adlı bu zaman uyumsuz yeni görevi ekleyin.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Zaman uyumsuz görevinizi **Main** yönteminizden çalıştırmak için aşağıdaki kodu ekleyin. **Main** yöntemi özel durumları yakalar ve bunları konsola yazar.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

Uygulamayı derlemek ve çalıştırmak için **DocumentDBGettingStarted** düğmesine basın.

Tebrikler! Bir DocumentDB hesabına başarıyla bağlandınız, şimdi DocumentDB kaynaklarıyla çalışmaya bakalım.  

## <a name="step-4-create-a-database"></a>4. Adım: Veritabanı oluşturma
Bir veritabanı oluşturmak için kodu eklemeden önce, konsola yazma için bir yardımcı yöntemi ekleyin.

**WriteToConsoleAndPromptToContinue** yöntemini kopyalayın ve **GetStartedDemo** yönteminin altına yapıştırın.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

DocumentDB [veritabanınız](documentdb-resources.md#databases), **DocumentClient** sınıfının [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) yöntemi kullanılarak oluşturulabilir. Veritabanı, koleksiyonlar genelinde bölümlenmiş JSON belgesi depolama alanının mantıksal bir kapsayıcısıdır.

Aşağıdaki kodu kopyalayın ve istemci oluşturmanın altında **GetStartedDemo** yönteminize yapıştırın. Bu, *FamilyDB* adlı bir veritabanı oluşturur.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Uygulamanızı çalıştırmak için **DocumentDBGettingStarted** düğmesine basın.

Tebrikler! Başarılı bir şekilde bir DocumentDB veritabanı oluşturdunuz.  

## <a id="CreateColl"></a>5. Adım: Koleksiyon oluşturma
> [!WARNING]
> **CreateDocumentCollectionAsync**, ayrılmış işleme ile yeni bir koleksiyon oluşturur, bu da ücret ödenmesini gerektirebilir. Daha ayrıntılı bilgi için lütfen [fiyatlandırma sayfamızı](https://azure.microsoft.com/pricing/details/documentdb/) ziyaret edin.

Bir [koleksiyon](documentdb-resources.md#collections), **DocumentClient** sınıfının [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) yöntemi kullanılarak oluşturulabilir. Koleksiyon, JSON belgelerinin ve ilişkili JavaScript uygulama mantığının bir kapsayıcısıdır.

Aşağıdaki kodu kopyalayın ve veritabanı oluşturmanın altında **GetStartedDemo** yönteminize yapıştırın. Bunun yapılması *FamilyCollection_oa* adlı bir belge koleksiyonu oluşturur.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.CreateDatabaseIfNotExists("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Uygulamanızı çalıştırmak için **DocumentDBGettingStarted** düğmesine basın.

Tebrikler! Bir DocumentDB belge koleksiyonunu başarıyla oluşturdunuz.  

## <a id="CreateDoc"></a>6. Adım: JSON belgeleri oluşturma
Bir [belge](documentdb-resources.md#documents), **DocumentClient** sınıfının [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) yöntemi kullanılarak oluşturulabilir. Belgeler, kullanıcı tanımlı (rastgele) JSON içeriğidir. Şimdi bir veya daha fazla belge ekleyebiliriz. Veritabanınızda depolamak istediğiniz veriler zaten varsa DocumentDB'nin [Veri Geçiş Aracı](documentdb-import-data.md)'nı kullanabilirsiniz.

İlk olarak, bu örnekte DocumentDB içinde depolanan nesneleri temsil edecek bir **Family** sınıfı oluşturmamız gerekir. **Family**'nin içinde kullanılan **Parent**, **Child**, **Pet**, **Address** alt sınıflarını da oluşturacağız. Belgelerin, JSON'da **id** olarak seri hale getirilmiş bir **Id** özelliğine sahip olmaları gerektiğini unutmayın. Bu sınıfları oluşturmak için **GetStartedDemo** yönteminden sonra aşağıdaki iç alt sınıfları ekleyin.

**Family**, **Parent**, **Child**, **Pet** ve **Address** sınıflarını kopyalayın ve **WriteToConsoleAndPromptToContinue** yönteminin altına yapıştırın.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
public class Family
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    public string LastName { get; set; }
    public Parent[] Parents { get; set; }
    public Child[] Children { get; set; }
    public Address Address { get; set; }
    public bool IsRegistered { get; set; }
    public override string ToString()
    {
            return JsonConvert.SerializeObject(this);
    }
}

public class Parent
{
    public string FamilyName { get; set; }
    public string FirstName { get; set; }
}

public class Child
{
    public string FamilyName { get; set; }
    public string FirstName { get; set; }
    public string Gender { get; set; }
    public int Grade { get; set; }
    public Pet[] Pets { get; set; }
}

public class Pet
{
    public string GivenName { get; set; }
}

public class Address
{
    public string State { get; set; }
    public string County { get; set; }
    public string City { get; set; }
}
```

**CreateFamilyDocumentIfNotExists** yöntemini kopyalayın ve **CreateDocumentCollectionIfNotExists** yönteminizin altına yapıştırın.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

Andersen Ailesi ve Wakefield Ailesi için birer tane olmak üzere iki belge yerleştirin.

Aşağıdaki kodu kopyalayın ve belge koleksiyonu oluşturmanın altında **GetStartedDemo** yönteminize yapıştırın.

```csharp
await this.CreateDatabaseIfNotExists("FamilyDB_oa");

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
Family andersenFamily = new Family
{
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
                new Child
                {
                        FirstName = "Henriette Thaulow",
                        Gender = "female",
                        Grade = 5,
                        Pets = new Pet[]
                        {
                                new Pet { GivenName = "Fluffy" }
                        }
                }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
};

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

Family wakefieldFamily = new Family
{
        Id = "Wakefield.7",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
                new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
                new Child
                {
                        FamilyName = "Merriam",
                        FirstName = "Jesse",
                        Gender = "female",
                        Grade = 8,
                        Pets = new Pet[]
                        {
                                new Pet { GivenName = "Goofy" },
                                new Pet { GivenName = "Shadow" }
                        }
                },
                new Child
                {
                        FamilyName = "Miller",
                        FirstName = "Lisa",
                        Gender = "female",
                        Grade = 1
                }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
};

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Uygulamanızı çalıştırmak için **DocumentDBGettingStarted** düğmesine basın.

Tebrikler! İki DocumentDB belgesini başarıyla oluşturdunuz.  

![Bir C# konsol uygulaması oluşturmak için NoSQL öğreticisi tarafından kullanılan belgeler, hesap, çevrimiçi veritabanı ve koleksiyon arasındaki hiyerarşik ilişkiyi gösteren diyagram](./media/documentdb-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>7. Adım: DocumentDB kaynaklarını sorgulama
DocumentDB, her bir koleksiyonda depolanan JSON belgelerde yapılan zengin [sorguları](documentdb-sql-query.md) destekler.  Aşağıdaki örnek kod, önceki adımda yerleştirdiğimiz belgelerde hem DocumentDB SQL söz dizimi hem de LINQ kullanarak çalıştırabileceğimiz çeşitli sorguları gösterir.

**ExecuteSimpleQuery** yöntemini kopyalayın ve **CreateFamilyDocumentIfNotExists** yönteminizin altına yapıştırın.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Aşağıdaki kodu kopyalayın ve ikinci belge oluşturmanın altında **GetStartedDemo** yönteminize yapıştırın.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Uygulamanızı çalıştırmak için **DocumentDBGettingStarted** düğmesine basın.

Tebrikler! Bir DocumentDB koleksiyonunu başarıyla sorguladınız.

Aşağıdaki diyagram oluşturduğunuz koleksiyonda DocumentDB SQL sorgusu söz diziminin nasıl çağrıldığını gösterir, aynı mantık LINQ sorgusu için de geçerlidir.

![Bir C# konsol uygulaması oluşturmak için NoSQL öğreticisi tarafından kullanılan sorgunun kapsamını ve anlamını gösteren diyagram](./media/documentdb-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

DocumentDB sorguları zaten tek bir koleksiyon kapsamında olduğundan, sorgudaki [FROM](documentdb-sql-query.md#FromClause) anahtar sözcüğü isteğe bağlıdır. Bu nedenle, "FROM Families f", "FROM root r" veya seçtiğiniz herhangi bir başka değişken adıyla değiştirilebilir. DocumentDB; Families, root veya seçtiğiniz değişken adının varsayılan olarak geçerli koleksiyona başvurduğu sonucuna varır.

## <a id="ReplaceDocument"></a>8. Adım: JSON belgesini değiştirme
DocumentDB, JSON belgelerini değiştirmeyi destekler.  

**ReplaceFamilyDocument** yöntemini kopyalayın ve **ExecuteSimpleQuery** yönteminizin altına yapıştırın.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    try
    {
        await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
        this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
    }
    catch (DocumentClientException de)
    {
        throw;
    }
}
```

Aşağıdaki kodu kopyalayın ve sorgu yürütmenin altında **GetStartedDemo** yönteminize yapıştırın. Belgeyi değiştirdikten sonra, aynı sorgu tekrar çalıştırılarak değiştirilen belge görüntülenir.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Uygulamanızı çalıştırmak için **DocumentDBGettingStarted** düğmesine basın.

Tebrikler! Bir DocumentDB belgesini başarıyla değiştirdiniz.

## <a id="DeleteDocument"></a>9. Adım: JSON belgesini silme
DocumentDB, JSON belgelerini silmeyi destekler.  

**DeleteFamilyDocument** yöntemini kopyalayın ve **ReplaceFamilyDocument** yönteminizin altına yapıştırın.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    try
    {
        await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine("Deleted Family {0}", documentName);
    }
    catch (DocumentClientException de)
    {
        throw;
    }
}
```

Aşağıdaki kodu kopyalayın ve ikinci sorguyu yürütmenin altında **GetStartedDemo** yönteminize yapıştırın.

```cshrp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Uygulamanızı çalıştırmak için **DocumentDBGettingStarted** düğmesine basın.

Tebrikler! Bir DocumentDB belgesini başarıyla sildiniz.

## <a id="DeleteDatabase"></a>10. Adım: Veritabanını silme
Oluşturulan veritabanı silindiğinde, veritabanı ve tüm alt kaynaklar (koleksiyonlar, belgeler vb.) kaldırılır.

Tüm veritabanını ve tüm alt kaynaklarını silmek için aşağıdaki kodu kopyalayın ve belge silmenin altında **GetStartedDemo** yönteminize yapıştırın.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Uygulamanızı çalıştırmak için **DocumentDBGettingStarted** düğmesine basın.

Tebrikler! Bir DocumentDB veritabanını başarıyla sildiniz.

## <a id="Run"></a>11. Adım: C# konsol uygulamanızı hep birlikte çalıştırın!
Uygulamayı hata ayıklama modunda derlemek için, Visual Studio’da **DocumentDBGettingStarted** düğmesine basın.

Başlarken uygulamanızın çıktısını görmeniz gerekir. Çıktı, eklediğimiz sorguların sonuçlarını gösterir ve aşağıdaki örnek metinle eşleşmelidir.

```
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

Tebrikler! Bu NoSQL öğreticisini tamamladınız ve çalışan bir C# konsol uygulamasına sahipsiniz!

## <a id="GetSolution"></a> NoSQL öğreticisi tam çözümünü edinme
Bu makaledeki tüm örnekleri içeren GetStarted çözümünü derlemek için aşağıdakilere ihtiyacınız vardır:

* Etkin bir Azure hesabı. Bir aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) için kaydolabilirsiniz.
* Bir [DocumentDB hesabı][documentdb-create-account].
* GitHub'da bulunan [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) çözümü.

Başvuruları Visual Studio'daki DocumentDB .NET Core SDK'ya geri yüklemek için, Çözüm Gezgini'nde **GetStarted** çözümüne sağ tıklayın ve ardından **NuGet Paketi Geri Yüklemeyi Etkinleştir**'e tıklayın. Ardından, Program.cs dosyasındaki EndpointUrl ve AuthorizationKey değerlerini [DocumentDB hesabına bağlanma](#Connect) bölümünde açıklandığı gibi güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar
* Daha karmaşık bir ASP.NET MVC NoSQL öğreticisi mi istiyorsunuz? Bkz. [DocumentDB kullanarak ASP.NET MVC ile bir web uygulaması oluşturma](documentdb-dotnet-application.md).
* DocumentDB .NET Core SDK’yı kullanarak bir Xamarin iOS, Android, veya Forms uygulaması mı geliştirmek istiyorsunuz? Bkz. [DocumentDB kullanarak Xamarin mobil uygulamaları geliştirme](documentdb-mobile-apps-with-xamarin.md).
* DocumentDB ile ölçek ve performans testi mi yapmak istiyorsunuz? Bkz. [Azure DocumentDB ile Performans ve Ölçek Testi](documentdb-performance-testing.md)
* [Bir DocumentDB hesabını izleme](documentdb-monitor-accounts.md) hakkında bilgi edinin.
* [Query Playground](https://www.documentdb.com/sql/demo)'daki örnek veri kümelerimizde sorgular çalıştırın.
* [DocumentDB belge sayfasının](https://azure.microsoft.com/documentation/services/documentdb/) Geliştirme bölümünde programlama modeli hakkında daha fazla bilgi edinin.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-dotnetcore-get-started/nosql-tutorial-keys.png

