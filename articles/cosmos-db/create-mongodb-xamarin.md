---
title: .NET ve Azure Cosmos DB MongoDB API 'SI ile bir Xamarin uygulaması oluşturun
description: MongoDB için Azure Cosmos DB API 'sine bağlanmak ve sorgulamak için kullanabileceğiniz bir Xamarin kodu örneği sunar
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/09/2020
ms.author: masoucou
ms.custom: devx-track-csharp
ms.openlocfilehash: 75db62b4f8a5c6512ca5fc84d149513fe81f6019
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658240"
---
# <a name="quickstart-build-a-xamarinforms-app-with-net-sdk-and-azure-cosmos-dbs-api-for-mongodb"></a>Hızlı başlangıç: .NET SDK ve Azure Cosmos DB MongoDB için API 'SI ile bir Xamarin. Forms uygulaması oluşturma
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Bu hizmetle belge, anahtar/değer ve grafik veritabanlarını kolayca oluşturup sorgulayabilir ve tüm bunları yaparken Azure Cosmos DB'nin genel dağıtım ve yatay ölçeklendirme özelliklerinden faydalanabilirsiniz.

Bu hızlı başlangıçta, Azure portal kullanılarak MongoDB, belge veritabanı ve koleksiyon [için Azure Cosmos DB API 'si ile yapılandırılan Cosmos hesabının](mongodb-introduction.md)nasıl oluşturulacağı gösterilmektedir. Daha sonra [MongoDB .net sürücüsünü](https://docs.mongodb.com/ecosystem/drivers/csharp/)kullanarak bir ToDo uygulaması Xamarin. Forms uygulaması oluşturacaksınız.

## <a name="prerequisites-to-run-the-sample-app"></a>Örnek uygulamayı çalıştırmak için önkoşullar

Örneği çalıştırmak için [Visual Studio](https://www.visualstudio.com/downloads/) veya [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/) ve geçerli bir Azure CosmosDB hesabı olması gerekir.

Visual Studio henüz yoksa, [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/) 'ı kurulum ile birlikte yüklenen .NET iş yüküne **sahip mobil geliştirmeyle** indirin.

Mac'te çalışmayı tercih ediyorsanız [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'yu indirin ve kurulumu çalıştırın.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Bu makalede açıklanan örnek, MongoDB.Driver 2.6.1 sürümü ile uyumludur.

## <a name="clone-the-sample-app"></a>Örnek uygulamayı kopyalama

İlk olarak, GitHub 'dan örnek uygulamayı indirin. Bu, MongoDB’nin belge depolama modeliyle bir yapılacak işler uygulamasını uygular.



# <a name="windows"></a>[Windows](#tab/windows)

1. Windows 'ta, bir komut istemi açın veya Mac 'te terminali açın, git-Samples adlı yeni bir klasör oluşturun ve ardından pencereyi kapatın.

    ```batch
    md "C:\git-samples"
    ```

    ```bash
    mkdir '$home\git-samples\
    ```

2. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere yeni bir klasör olarak değiştirmek için `cd` komutunu kullanın.

    ```bash
    cd "C:\git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Git kullanmak istemiyorsanız [projeyi ZIP dosyası olarak da indirebilirsiniz](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Veritabanı kaynaklarının kodda nasıl oluşturulduğunu öğrenmekle ilgileniyorsanız, aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Aksi durumda, [Bağlantı dizenizi güncelleştirme](#update-your-connection-string) bölümüne atlayabilirsiniz.

Aşağıdaki kod parçacıkları, şu `MongoService` yolda bulunan sınıfından alınır: src/tasklist. Core/Services/MongoService. cs.

* Mongo İstemcisini başlatın.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(new MongoUrl(APIKeys.ConnectionString));

    settings.SslSettings = new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    settings.RetryWrites = false;

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Veritabanı ve koleksiyona bir başvuru alın. Mevcut değilse MongoDB .NET SDK'sı otomatik olarak veritabanını ve koleksiyonu oluşturur.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings 
    {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Tüm belgeleri Liste olarak alın.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Belirli belgeleri sorgulayın.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Bir görev oluşturun ve koleksiyona ekleyin.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Bir koleksiyondaki görevi güncelleştirin.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Bir koleksiyondan bir görevi silin.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>Bağlantı dizenizi güncelleştirme

Bu adımda Azure portalına dönerek bağlantı dizesi bilgilerinizi kopyalayıp uygulamaya ekleyin.

1. [Azure portalında](https://portal.azure.com/), Azure Cosmos DB hesabınızın sol taraftaki gezinti menüsünden **Bağlantı Dizesi**'ne ve ardından **Okuma-Yazma Anahtarları**'na tıklayın. Ekranın sağ tarafındaki kopyala düğmesini kullanarak sonraki adımlarda bulunan Birincil Bağlantı Dizesini kopyalayın.

2. **TaskList.Core** projesinin **Helpers** dizinindeki **APIKeys.cs** dosyasını açın.

3. Portaldan **birincil bağlantı dizesi** değerinizi kopyalayın (kopyala düğmesini kullanarak) ve **APIKeys.cs** dosyanızdaki **ConnectionString** alanının değeri olarak kullanın.

4. `&replicaSet=globaldb`Bağlantı dizesinden kaldır. Bu değeri sorgu dizesinden kaldırdıysanız bir çalışma zamanı hatası alırsınız.

> [!IMPORTANT]
> `&replicaSet=globaldb`Çalışma zamanı hatasından kaçınmak için, anahtar/değer çiftini bağlantı dizesinin sorgu dizesinden kaldırmanız gerekir.

Bu adımlarla uygulamanıza Azure Cosmos DB ile iletişim kurması için gereken tüm bilgileri eklemiş oldunuz.

## <a name="run-the-app"></a>Uygulamayı çalıştırma

### <a name="visual-studio-2019"></a>Visual Studio 2019

1. Visual Studio'nun **Çözüm Gezgini** bölümünde her bir projeye sağ tıklayın ve ardından **NuGet Paketlerini Yönet**'e tıklayın.
2. **Tüm NuGet paketlerini geri yükle**'ye tıklayın.
3. **TaskList.Android**'e sağ tıklayın ve **Başlangıç projesi olarak ayarla**'yı seçin.
4. Uygulamada hata ayıklamaya başlamak için F5'e basın.
5. iOS üzerinde çalıştırmak istiyorsanız ilk olarak makinenizin bir Mac'e bağlı olduğundan ([talimatlara](/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio) buradan ulaşabilirsiniz) emin olun.
6. **TaskList.iOS** projesine sağ tıklayın ve **Başlangıç projesi olarak ayarla**'yı seçin.
7. Uygulamada hata ayıklamaya başlamak için F5'e basın.

### <a name="visual-studio-for-mac"></a>Mac için Visual Studio

1. Platform açılan listesinde çalıştırmak istediğiniz platforma bağlı olarak TaskList.iOS veya TaskList.Android girişini seçin.
2. Uygulamada hata ayıklamaya başlamak için Cmd+Enter tuşlarına basın.

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Azure Cosmos DB hesabı oluşturmayı ve MongoDB API’sini kullanarak bir Xamarin.Forms uygulamasını çalıştırmayı öğrendiniz. Şimdi Cosmos DB hesabınıza ek veri aktarabilirsiniz.

> [!div class="nextstepaction"]
> [MongoDB için Azure Cosmos DB API 'siyle yapılandırılan Azure Cosmos DB verileri içeri aktarın](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)