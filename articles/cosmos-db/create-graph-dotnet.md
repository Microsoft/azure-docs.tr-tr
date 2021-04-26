---
title: Gremlin API kullanarak çekirdek uygulama Azure Cosmos DB .NET Framework oluşturma
description: Azure Cosmos DB’ye bağlanmak ve veritabanını sorgulamak için kullanabileceğiniz bir .NET Framework/Core kod örneği sunar
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: chrande
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1953f4a21df6f550320592fbe009834a7b573887
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360539"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Hızlı başlangıç: Azure Cosmos DB Gremlin API hesabını kullanarak .NET Framework veya çekirdek uygulama oluşturma
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin konsolu](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Bu hizmetle belge, anahtar/değer ve grafik veritabanlarını kolayca oluşturup sorgulayabilir ve tüm bunları yaparken Azure Cosmos DB'nin genel dağıtım ve yatay ölçeklendirme özelliklerinden faydalanabilirsiniz. 

Bu hızlı başlangıçta Azure portal kullanılarak Azure Cosmos DB [Gremlin API](graph-introduction.md) hesabı, veritabanı ve Graf (kapsayıcı) oluşturma gösterilmektedir. Daha sonra açık kaynaklı [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) sürücüsünü kullanarak bir konsol uygulaması oluşturabilir ve çalıştırabilirsiniz.  

## <a name="prerequisites"></a>Önkoşullar

Zaten Visual Studio 2019 yüklü değilse, **ücretsiz** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)' ı indirip kullanabilirsiniz. Visual Studio kurulumu sırasında **Azure dağıtımını** etkinleştirdiğinizden emin olun.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Graf ekleme

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi GitHub'dan bir Gremlin API'si uygulaması kopyalayalım, bağlantı dizesini ayarlayalım ve uygulamayı çalıştıralım. Verilerle program aracılığıyla çalışmanın ne kadar kolay olduğunu göreceksiniz. 

1. Bir komut istemini açın, git-samples adlı yeni bir klasör oluşturun ve komut istemini kapatın.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere yeni bir klasör olarak değiştirmek için `cd` komutunu kullanın.

    ```bash
    cd "C:\git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Ardından Visual Studio’yu ve çözüm dosyasını açın.

5. Projedeki NuGet paketlerini geri yükleyin. Bu Gremlin.Net sürücüsünü ve Newtonsoft.Json paketini içermelidir.


6. Ayrıca NuGet paket yöneticisi veya [NuGet komut satırı yardımcı programını](/nuget/install-nuget-client-tools) kullanarak Gremlin.Net sürücüsünü kendiniz de yükleyebilirsiniz: 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Veritabanı kaynaklarının kodda nasıl oluşturulduğunu öğrenmekle ilgileniyorsanız, aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Aksi durumda, [Bağlantı dizenizi güncelleştirme](#update-your-connection-string) bölümüne atlayabilirsiniz. 

Aşağıdaki kod parçacıklarının tamamı, Program.cs dosyasından alınır.

* Bağlantı parametrelerinizi yukarıda oluşturulan hesaba göre ayarlayın: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* Yürütülecek Gremlin komutları bir sözlükte listelenmiştir:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* `GremlinServer` `GremlinClient` Yukarıda belirtilen parametreleri kullanarak yeni ve bağlantı nesneleri oluşturun:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Zaman uyumsuz bir görevle nesneyi kullanarak her Gremlin sorgusunu yürütün `GremlinClient` . Önceki adımda tanımlanan sözlükten Gremlin sorgularını okuyabilir ve bunları yürütebilirsiniz. Daha sonra, sonucu elde edin ve bir sözlük olarak biçimlendirilen değerleri, `JsonSerializer` Newtonsoft.Jspaketteki sınıfı kullanarak kullanın:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>Bağlantı dizenizi güncelleştirme

Bu adımda Azure portalına dönerek bağlantı dizesi bilgilerinizi kopyalayıp uygulamaya ekleyin.

1. [Azure portalda](https://portal.azure.com/) graf veritabanı hesabınıza gidin. **Genel Bakış** sekmesinde iki uç nokta görebilirsiniz: 
 
   **.NET SDK URI** -bu değer, Graph hesabına Microsoft. Azure. Graf kitaplığı kullanarak bağlandığınızda kullanılır. 

   **Gremlin Uç Noktası** - Bu değer, Gremlin.Net kitaplığını kullanarak graf hesabına bağlandığınızda kullanılır.

    :::image type="content" source="./media/create-graph-dotnet/endpoint.png" alt-text="Uç noktayı kopyalama":::

   Bu örneği çalıştırmak için, **Gremlin uç noktası** değerini kopyalayın, sonundaki bağlantı noktası numarasını SILIN, URI ise olur `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com` . Uç nokta değeri şöyle görünmelidir `testgraphacct.gremlin.cosmosdb.azure.com`

1. Sonra, **anahtarlar** sekmesine gidin ve Azure Portal **birincil anahtar** değerini kopyalayın. 

1. Hesabınızın URI 'sini ve BIRINCIL anahtarını kopyaladıktan sonra, uygulamayı çalıştıran yerel makinede yeni bir ortam değişkenine kaydedin. Ortam değişkenini ayarlamak için bir komut istemi penceresi açın ve aşağıdaki komutu çalıştırın. <Your_Azure_Cosmos_account_URI> ve <Your_Azure_Cosmos_account_PRIMARY_KEY> değerlerini değiştirdiğinizden emin olun.

   ```console
   setx Host "<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. *Program. cs* dosyasını açın ve yukarıda oluşturulan "veritabanı ve" kapsayıcı "değişkenlerini veritabanı ve kapsayıcı (aynı zamanda grafik adı) ile güncelleştirin.

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Program.cs dosyasını kaydedin. 

Bu adımlarla uygulamanıza Azure Cosmos DB ile iletişim kurması için gereken tüm bilgileri eklemiş oldunuz. 

## <a name="run-the-console-app"></a>Konsol uygulamasını çalıştırma

Uygulamayı çalıştırmak için CTRL+F5 tuşlarına basın. Uygulama hem Gremlin sorgu komutlarını hem de konsol sonuçlarını yazdırır.

   Konsol penceresinde grafiğe eklenmekte olan kenarlar ve köşeler gösterilir. Betik tamamlandığında, ENTER tuşuna basarak konsol penceresini kapatın.

## <a name="browse-using-the-data-explorer"></a>Veri Gezgini'ni kullanarak göz atma

Şimdi Azure portalındaki Veri Gezgini'ne dönerek yeni grafik verilerinize göz atıp sorgu gönderebilirsiniz.

1. Yeni veritabanı, Veri Gezgini'nin Graflar bölmesinde görüntülenir. Veritabanını ve kapsayıcı düğümlerini genişletip **Graf**’a tıklayın.

2. Graftaki tüm köşeleri görüntülemek üzere varsayılan sorguyu kullanmak için **Filtre Uygula** düğmesine tıklayın. Örnek uygulama tarafından oluşturulan veriler Grafikler bölmesinde görüntülenir.

    Grafiği yakınlaştırıp uzaklaştırabilir, grafik görüntüleme alanını genişletebilir, başka köşeler ekleyebilir ve görüntüleme alanında köşeleri taşıyabilirsiniz.

    :::image type="content" source="./media/create-graph-dotnet/graph-explorer.png" alt-text="Azure portalındaki Veri Gezgini'nde grafiği görüntüleme":::

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Azure Cosmos DB hesabı oluşturmayı, Veri Gezgini'ni kullanarak grafik oluşturmayı ve bir uygulamayı çalıştırmayı öğrendiniz. Artık daha karmaşık sorgular derleyebilir ve Gremlin kullanarak güçlü grafik geçişi mantığını kullanabilirsiniz. 

> [!div class="nextstepaction"]
> [Gremlin kullanarak sorgulama](tutorial-query-graph.md)