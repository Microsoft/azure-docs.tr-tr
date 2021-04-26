---
title: 'Azure Cosmos DB: Xamarin ile todo uygulaması derleme'
description: Azure Cosmos DB'ye bağlanmak ve veritabanını sorgulamak için kullanabileceğiniz bir Xamarin kod örneği sunar
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a940f4bb519332e147577e4a9172406c401d152
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365748"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Hızlı başlangıç: Azure Cosmos DB SQL API hesabı kullanarak Xamarin ile ToDo uygulaması oluşturma
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

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Bu hizmetle belge, anahtar/değer ve grafik veritabanlarını kolayca oluşturup sorgulayabilir ve tüm bunları yaparken Azure Cosmos DB'nin genel dağıtım ve yatay ölçeklendirme özelliklerinden faydalanabilirsiniz.

> [!NOTE]
> CosmosDB de dahil birçok Azure teklifini gösteren tamamen kurallı örnek bir Xamarin uygulaması için örnek koda GitHub’da [buradan](https://github.com/xamarinhq/app-geocontacts) erişilebilir. Bu uygulama, coğrafi olarak dağınık kişilerin görüntülenmesini sergileyerek bu kişilerin konumlarını güncelleştirmesine olanak sağlar.

Bu hızlı başlangıç belgesinde Azure portalını kullanarak bir Azure Cosmos DB SQL API hesabını, belge veritabanını ve kapsayıcısını nasıl oluşturacağınız anlatılmıştır. Daha sonra [SQL .NET API](sql-api-sdk-dotnet.md) ve [Xamarin](/xamarin/) [. Forms](/xamarin/) ve [MVVM mimari deseninin](/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm)kullanıldığı bir yapılacaklar listesi mobil uygulaması derleyip dağıtacaksınız.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png" alt-text="iOS’ta çalışan Xamarin todo uygulaması":::

## <a name="prerequisites"></a>Önkoşullar

Windows üzerinde geliştirme yapıyorsanız ve henüz Visual Studio 2019 yüklü değilse, **ücretsiz** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)' ı indirip kullanabilirsiniz. Visual Studio kurulumu sırasında **Azure geliştirme** ve **.NET ile Mobil Dağıtım** iş yüklerini etkinleştirdiğinizden emin olun.

Mac kullanıyorsanız, **ücretsiz** [Mac için Visual Studio](https://www.visualstudio.com/vs/mac/)indirebilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Kapsayıcı ekleme

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Örnek verileri ekleme

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Verilerinizi sorgulayın

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi Xamarin SQL API uygulamasını GitHub 'dan kopyalayalım, kodu gözden geçirin, API anahtarlarını edinin ve çalıştırın. Verilerle program aracılığıyla çalışmanın ne kadar kolay olduğunu göreceksiniz.

1. Bir komut istemini açın, git-samples adlı yeni bir klasör oluşturun ve komut istemini kapatın.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere yeni bir klasör olarak değiştirmek için `cd` komutunu kullanın.

    ```bash
    cd "C:\git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. Visual Studio 'da **C:\git-samples\azure-Cosmos-db-SQL-Xamarin-getting-started\src\ToDoItems.sln** açın 

## <a name="obtain-your-api-keys"></a>API anahtarlarınızı edinme

Azure portalına geri dönerek API anahtarı bilgilerini alın ve uygulamaya kopyalayın.

1. [Azure portalında](https://portal.azure.com/), Azure Cosmos DB SQL API hesabınızın sol taraftaki gezinti menüsünden **Anahtarlar**'a ve ardından **Okuma/Yazma Anahtarları**'na tıklayın. Ekranın sağ tarafındaki kopyalama düğmelerini kullanarak URI ve Birincil Anahtar değerlerini kopyalayarak sonraki adımda APIKeys.cs dosyasına yapıştırın.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/keys.png" alt-text="Azure portalında erişim anahtarı görüntüleme ve kopyalama, Anahtarlar dikey penceresi":::

2. Visual Studio 'da **todoıtems. Core/yardımcılar/APIKeys. cs** öğesini açın.

3. Azure portal, Kopyala düğmesini kullanarak, **URI** değerini kopyalayın ve `CosmosEndpointUrl` apikeys. cs içindeki değişkenin değerini yapın.

    ```csharp
    //#error Enter the URL of your Azure Cosmos DB endpoint here
    public static readonly string CosmosEndpointUrl = "[URI Copied from Azure portal]";
    ```

4. Azure portal, Kopyala düğmesini kullanarak, **BIRINCIL anahtar** değerini kopyalayın ve `Cosmos Auth Key` ' ın apikeys. cs içindeki değerini yapın.

    ```csharp
    //#error Enter the read/write authentication key of your Azure Cosmos DB endpoint here
    public static readonly string CosmosAuthKey = "[PRIMARY KEY copied from Azure portal";
    ```

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu çözüm, Azure Cosmos DB SQL API ve Xamarin.Forms kullanarak bir ToDo uygulaması oluşturma işlemini gösterir. Uygulamada iki sekme bulunur; birinci sekme henüz tamamlanmamış todo öğelerini gösteren bir liste görünümü içerir. İkinci sekme tamamlanmış todo öğelerini gösterir. Birinci sekmede tamamlanmamış todo öğelerini görüntülemeye ek olarak yeni todo öğeleri ekleyebilir, var olanları düzenleyebilir ve öğeleri tamamlandı olarak işaretleyebilirsiniz.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/android-todo-screen.png" alt-text="Azure portalında JSON verilerini kopyalayın ve Veri Gezgini'ne kaydedin":::

ToDoItems çözümündeki kod şunları içerir:

* **Todoıtems. Core**
   * Bu, Azure Cosmos DB içindeki Todo öğelerini tutan bir Xamarin. Forms projesi ve paylaşılan uygulama mantığı kodu tutan bir .NET Standard projem.
* **Todoıtems. Android**
  * Bu proje Android uygulamasını içerir.
* **Todoıtems. iOS**
  * Bu proje iOS uygulamasını içerir.

Şimdi uygulamanın Azure Cosmos DB ile nasıl iletişim kurduğuna hızlıca göz atalım.

* [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) NuGet paketinin tüm projelere eklenmesi gerekir.
* `ToDoItem` **Todoıtems. Core/modeller** klasöründeki sınıfı, yukarıda oluşturulan **öğeler** kapsayıcısındaki belgeleri modelledir. Özellik adlarının büyük/küçük harfe duyarlı olduğunu unutmayın.
* `CosmosDBService` **Todoıtems. Core/Services** klasöründeki sınıfı, Azure Cosmos DB iletişimini kapsüller.
* `CosmosDBService` sınıfında bir `DocumentClient` tür değişkeni bulunur. , `DocumentClient` Azure Cosmos DB hesabına karşı istekleri yapılandırmak ve yürütmek için kullanılır ve örneği oluşturulur:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Belgeler için bir kapsayıcı sorgulanırken, `DocumentClient.CreateDocumentQuery<T>` yöntemi işlevinde görüldüğü gibi kullanılır `CosmosDBService.GetToDoItems` :

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    , `CreateDocumentQuery<T>` Önceki bölümde oluşturulan kapsayıcıyı işaret eden BIR URI alır. Ayrıca, bir `Where` yan tümcesi gibi LINQ işleçleri belirtebilirsiniz. Bu durumda yalnızca tamamlanmamış todo öğeleri döndürülür.

    `CreateDocumentQuery<T>` işlevi zaman uyumlu olarak yürütülür ve bir `IQueryable<T>` döndürür. Ancak, `AsDocumentQuery` yöntemi `IQueryable<T>` `IDocumentQuery<T>` zaman uyumsuz olarak yürütülebilecek bir nesnesine dönüştürür. Böylece, mobil uygulamalar için kullanıcı arabirimi iş parçacığı engellenmez.

    İşlevi, daha `IDocumentQuery<T>.ExecuteNextAsync<T>` `HasMoreResults` fazla sonuç döndürülmek üzere olup olmadığını görmek için Azure Cosmos DB sonuçları sayfasını alır.

> [!TIP]
> Azure Cosmos kapsayıcılarında ve belgelerinde çalışan çeşitli işlevler, bir URI 'yi kapsayıcı veya belgenin adresini belirten bir parametre olarak alır. Bu URI `URIFactory` sınıfı kullanılarak oluşturulur. Veritabanları, kapsayıcılar ve belgelerin URI 'Leri bu sınıfla oluşturulabilir.

* `ComsmosDBService.InsertToDoItem`İşlevi nasıl yeni bir belge ekleneceğini gösterir:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    Öğe URI 'sinin yanı sıra eklenecek öğe.

* `CosmosDBService.UpdateToDoItem`İşlevi, var olan bir belgenin yeni bir belgenin nasıl değiştirileceğini gösterir:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Burada, değiştirilecek belgeyi benzersiz bir şekilde tanımlamak için yeni bir URI gerekir ve kullanarak, `UriFactory.CreateDocumentUri` veritabanı ve kapsayıcı adları ve BELGENIN kimliği geçirerek elde edilir.

    `DocumentClient.ReplaceDocumentAsync`, URI tarafından tanımlanan belgeyi parametre olarak belirtilen bir belgeyle değiştirir.

* Bir öğeyi silme işlevi ile gösterilmiştir `CosmosDBService.DeleteToDoItem` :

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Oluşturulan ve işleve geçirilen benzersiz belge URI 'sini yeniden aklınızda edin `DocumentClient.DeleteDocumentAsync` .

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Bu adımlarla uygulamanıza Azure Cosmos DB ile iletişim kurması için gereken tüm bilgileri eklemiş oldunuz.

Aşağıdaki adımlarda, Mac için Visual Studio hata ayıklayıcısı kullanılarak uygulamayı çalıştırma işlemi gösterilecektir.

> [!NOTE]
> Android sürümündeki bir uygulama da tamamen aynı şekilde kullanılır ve her türlü değişiklik aşağıdaki adımlarda belirtilir. Windows üzerinde Visual Studio hatalarını ayıklamak isterseniz buna yönelik belgeler [iOS için burada](/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin) ve [Android için burada](/xamarin/android/deploy-test/debugging/) bulunabilir.

1. İlk olarak vurgulanan açılır listeye tıklayarak ve iOS için ToDoItems.iOS, Android için ToDoItems.Android öğesini seçerek hedeflemek istediğiniz platformu seçin.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-select-platform.png" alt-text="Mac için Visual Studio'da hata ayıklamak için platform seçme":::

2. Uygulamada hata ayıklamaya başlamak için cmd+Enter tuşlarına basın veya oynat düğmesine tıklayın.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-start-debug.png" alt-text="Mac için Visual Studio'da hata ayıklamayı başlatma":::

3. İOS simülatörü veya Android öykünücüsü başlatmayı bitirdiğinde, uygulama iOS ekranının en altında ve Android ekranının üst kısmında iki sekme görüntüler. İlki, tamamlanmamış Todo öğelerini, ikincisi ise tamamlanmış Todo öğelerini gösterir.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-droid-started.png" alt-text="ToDo uygulamasının başlatma ekranı":::

4. iOS’ta bir todo öğesini tamamlamak için sola kaydırın > **Tamamla** düğmesine dokunun. Android’de bir todo öğesini tamamlamak için öğeye uzun basın > ardından tamamla düğmesine dokunun.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-complete.png" alt-text="Todo öğesini tamamlama":::

5. Bir todo öğesini düzenlemek için > öğeye dokunun > yeni değerler girmenize olanak tanıyan yeni bir ekran görüntülenir. Kaydet düğmesine dokunduğunuzda değişiklikler Azure Cosmos DB’de kalıcı hale gelir.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-edit.png" alt-text="Todo öğesini düzenleme":::

6. Bir todo öğesi eklemek için > giriş ekranının sağ üst köşesindeki **Ekle** düğmesine dokunun > yeni ve boş bir düzenleme sayfası görüntülenir.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-add.png" alt-text="Todo öğesi ekleme":::

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Azure Cosmos hesabı oluşturmayı, Veri Gezgini kullanarak bir kapsayıcı oluşturmayı ve Xamarin uygulaması derleyip dağıtmayı öğrendiniz. Artık Azure Cosmos hesabınıza daha fazla veri aktarabilirsiniz.

> [!div class="nextstepaction"]
> [Azure Cosmos DB hesabınıza veri aktarma](import-data.md)
