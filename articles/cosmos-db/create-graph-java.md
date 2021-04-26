---
title: Azure Cosmos DB Java ile grafik veritabanı oluşturma
description: Gremlin kullanarak Azure Cosmos DB'ye bağlanmak ve içindeki grafik verilerini sorgulamak için kullanabileceğiniz bir Java kodu örneği sunar.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: chrande
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5a793eb69b344e361679759e46db47c78885831e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360981"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-gremlin-api"></a>Hızlı başlangıç: Java SDK ve Azure Cosmos DB Gremlin API 'SI ile bir grafik veritabanı oluşturma
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin konsolu](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Bu hızlı başlangıçta, Azure portal bir Azure Cosmos DB Gremlin (Graf) API hesabı oluşturup yönetirsiniz ve GitHub ' dan kopyalanmış bir Java uygulamasını kullanarak veri eklersiniz. Azure Cosmos DB, genel dağıtım ve yatay ölçeklendirme özellikleri ile belge, tablo, anahtar değer ve grafik veritabanlarını hızlıca oluşturmanıza ve sorgulamanızı sağlayan çok modelli bir veritabanı hizmetidir.

## <a name="prerequisites"></a>Önkoşullar
- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). `JAVA_HOME`Ortam değişkeninizi JDK 'nin yüklü olduğu klasöre işaret edin.
- [Maven ikili Arşivi](https://maven.apache.org/download.cgi). 
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma

Bir grafik veritabanı oluşturmadan önce Azure Cosmos DB ile bir Gremlin (Graf) veritabanı hesabı oluşturmanız gerekir.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Graf ekleme

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi kod ile çalışmaya geçelim. GitHub'dan bir Gremlin API'si uygulaması kopyalayalım, bağlantı dizesini ayarlayalım ve uygulamayı çalıştıralım. Verilerle program aracılığıyla çalışmanın ne kadar kolay olduğunu göreceksiniz.  

1. Bir komut istemini açın, git-samples adlı yeni bir klasör oluşturun ve komut istemini kapatın.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash gibi bir Git terminal penceresi açın ve örek uygulamayı yüklemek üzere bir klasör olarak değiştirmek için `cd` komutunu kullanın.  

    ```bash
    cd "C:\git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Veritabanı kaynaklarının kodda nasıl oluşturulduğunu öğrenmekle ilgileniyorsanız, aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Aksi durumda, [Bağlantı dizenizi güncelleştirme](#update-your-connection-information) bölümüne atlayabilirsiniz.

Aşağıdaki kod parçacıklarının tamamı, *C:\git-samples\azure-Cosmos-DB-Graph-Java-getting-started\src\GetStarted\Program.Java* dosyasından alınmıştır.

Bu Java konsol uygulaması, OSS [Apache TinkerPop](https://tinkerpop.apache.org/) sürücüsüyle [GREMLIN API](graph-introduction.md) veritabanını kullanır. 

- Gremlin, `Client` *C:\git-samples\azure-Cosmos-DB-Graph-Java-getting-started\src\remote.YAML* dosyasındaki yapılandırmadan başlatılır.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

- Bir dizi Gremlin adımı `client.submit` yöntemi kullanılarak çalıştırılır.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>Bağlantı bilgilerinizi güncelleştirme

Şimdi, Azure portalına dönerek bağlantı bilgilerinizi kopyalayıp uygulamaya ekleyin. Bu ayarlar, uygulamanızın barındırılan veritabanıyla iletişim kurmasına olanak tanır.

1. [Azure portal](https://portal.azure.com/)Azure Cosmos DB hesabınızda **anahtarlar**' ı seçin. 

    URI değerinin ilk parçasını kopyalayın.

    :::image type="content" source="./media/create-graph-java/copy-access-key-azure-portal.png" alt-text="Azure portalında erişim anahtarı görüntüleme ve kopyalama, Anahtarlar sayfası":::

2. *Src/Remote. YAML* dosyasını açın ve benzersiz kimlik değerini `$name$` içine yapıştırın `hosts: [$name$.graphs.azure.com]` .

    *Uzak. YAML* 'nin 1. satırı şuna benzer görünmelidir 

    `hosts: [test-graph.graphs.azure.com]`

3. `endpoint` değerindeki `graphs` ifadesini `gremlin.cosmosdb` ile değiştirin. (Grafik veritabanı hesabınızı 20 Aralık 20, 2017’den önce oluşturduysanız, uç nokta değerinde bir değişiklik yapmayın ve sonraki adıma geçin.)

    Uç nokta değeri şimdi şöyle görünmelidir:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. Azure portalında, kopyala düğmesini kullanarak PRIMARY KEY’i kopyalayın ve `password: $masterKey$` içindeki `$masterKey$` öğesine yapıştırın.

    *Uzak. YAML* 'nin 5. satırı şuna benzer görünmelidir 

    `password: 2Ggkr662ifxz2Mg==`

5. *Uzak. YAML* 'nin 3. satırını değiştir

    `username: /dbs/$database$/colls/$collection$`

    kullanıcısı 

    `username: /dbs/sample-database/colls/sample-graph`

    Örnek veritabanı veya grafınız için benzersiz bir ad kullandıysanız değerleri uygun şekilde güncelleştirin.

6. *Remote. YAML* dosyasını kaydedin.

## <a name="run-the-console-app"></a>Konsol uygulamasını çalıştırma

1. Git terminal penceresinde `cd` komutuyla azure-cosmos-db-graph-java-getting-started klasörüne ulaşın.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. Git terminal penceresinde aşağıdaki komutu kullanarak gerekli Java paketlerini yükleyin.

   ```git
   mvn package
   ```

3. Git terminal penceresinde, Java uygulamasını başlatmak için aşağıdaki komutları kullanın.
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    Terminal penceresinde grafiğe eklenmekte olan köşeler gösterilir. 
    
    Zaman aşımı hatası alırsanız, bağlantı bilgilerini, [Bağlantı bilgilerinizi güncelleştirme](#update-your-connection-information), konusunda belirtildiği şekilde güncelleştirdiğinizden emin olun ve son komutu çalıştırmayı yeniden deneyin. 
    
    Program durdurulduğunda, gir ' i seçin ve ardından İnternet tarayıcınızda Azure portal geri dönün. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Örnek verileri inceleme ve ekleme

Şimdi Veri Gezgini’ne dönüp grafiğe eklenen köşeleri görebilir ve ek veri noktaları ekleyebilirsiniz.

1. Azure portal Azure Cosmos DB hesabınızda, **Veri Gezgini**, **örnek grafik**' i ve **Graf**' ı seçin ve ardından **Filtre Uygula**' yı seçin. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png" alt-text="Ekran görüntüsü, filtre uygulama seçeneğiyle bir P I 'den seçilen grafiği gösterir.":::

2. **Sonuç listesinde**, grafiğe yeni kullanıcıların eklendiğini görürsünüz. **Ben**’i seçin ve kullanıcının robin’e bağlı olduğuna dikkat edin. Köşeleri sürükleyip bırakarak hareket ettirebilir, farenizin tekerleğini kaydırarak öğeleri yakınlaştırabilir ve uzaklaştırabilir, ayrıca çift okla grafiğin boyutunu genişletebilirsiniz. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png" alt-text="Azure portalında Veri Gezgini'ndeki grafikte yeni köşeler":::

3. Şimdi birkaç yeni kullanıcı ekleyelim. Grafiğinize veri eklemek için **Yeni köşe** ' ı seçin.

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Ekran görüntüsü, değer girebileceğiniz yeni köşe bölmesini gösterir.":::

4. Etiket kutusuna *kişi* yazın.

5. Aşağıdaki özelliklerden her birini eklemek için **Özellik Ekle** ' yi seçin. Graftaki her kişi için benzersiz özellikler oluşturabileceğinizi görürsünüz. Yalnızca kimliği anahtarı gereklidir.

    anahtar|değer|Notlar
    ----|----|----
    kimlik|ashley|Köşe için benzersiz tanımlayıcı. Kimlik belirtmezseniz, bir kimlik otomatik olarak oluşturulur.
    cinsiyet|kadın| 
    teknoloji | java | 

    > [!NOTE]
    > Bu hızlı başlangıçta bölümlenmemiş bir koleksiyon oluşturacaksınız. Ancak koleksiyon oluşturma sırasında bir bölüm anahtarı belirterek bölümlendirilmiş bir koleksiyon oluşturursanız, daha sonra bölüm anahtarını her yeni köşede anahtar olarak eklemeniz gerekir. 

6. **Tamam**’ı seçin. Ekranın en altındaki **Tamam** seçeneğini görmek için ekranınızı genişletmeniz gerekebilir.

7. **Yeni köşe** ' i yeniden seçin ve ek bir yeni kullanıcı ekleyin. 

8. *Kişi* etiketi girin.

9. Aşağıdaki özelliklerden her birini eklemek için **Özellik Ekle** ' yi seçin:

    anahtar|değer|Notlar
    ----|----|----
    kimlik|rakesh|Köşe için benzersiz tanımlayıcı. Kimlik belirtmezseniz, bir kimlik otomatik olarak oluşturulur.
    cinsiyet|erkek| 
    okul|MIT| 

10. **Tamam**’ı seçin. 

11. Grafikteki tüm değerleri göstermek için varsayılan filtreyle **Filtre Uygula** düğmesini seçin `g.V()` . Tüm kullanıcılar **Sonuç listesinde** gösterilir. 

    Daha fazla veri ekledikçe sonuçlarınızı sınırlamak için filtreleri kullanabilirsiniz. Veri Gezgini, varsayılan olarak bir grafikteki tüm köşeleri almak için `g.V()` kullanır. JSON biçimindeki bir grafikteki tüm köşelerin sayımını döndürmek için, bu değeri `g.V().count()` gibi farklı bir [grafik sorgusu](tutorial-query-graph.md) olarak değiştirebilirsiniz. Filtreyi değiştirdiyseniz, filtreyi olarak `g.V()` yeniden değiştirin ve tüm sonuçları yeniden göstermek Için **Filtreyi Uygula** ' yı seçin.

12. Artık rakesh ve ashley arasında bağlantı kurabilirsiniz. **Sonuçlar** listesinde **Ashley** ' in seçili olduğundan emin olun, sonra sağ alt köşedeki **hedefler** ' in yanında bulunan bir grafikteki bir köşenin :::image type="content" source="./media/create-graph-java/edit-pencil-button.png" alt-text="hedefini Değiştir"::: ' i seçin. Düğmeyi görmek için pencerenizi genişletmeniz gerekebilir.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Grafikteki bir köşenin hedefini değiştirme-Azure CosmosDB":::

13. **Hedef** kutusunda *Rakesh* girin ve **kenar etiketi** kutusuna *bilir* yazın ve onay kutusunu seçin.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png" alt-text="Veri Gezgini bağlantı ekleme-Azure CosmosDB":::

14. Sonuç listesinden **rakesh**’i seçin, ashley ve rakesh’in bağlantılı olduğunu görürsünüz. 

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer.png" alt-text="Veri Gezgini bağlı iki köşe-Azure CosmosDB":::

Bu işlemle birlikte, bu öğreticideki kaynak oluşturma bölümünü tamamladınız. Grafiğinize köşe eklemeye, var olan köşeleri veya sorguları değiştirmeye devam edebilirsiniz. Şimdi, Azure Cosmos DB’nin sağladığı ölçümleri gözden geçirip kaynakları temizleyelim. 

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Azure Cosmos DB hesabı oluşturmayı, Veri Gezgini kullanarak bir grafik oluşturmayı ve grafiğe veri ekleyen bir Java uygulamasını çalıştırmayı öğrendiniz. Artık daha karmaşık sorgular derleyebilir ve Gremlin kullanarak güçlü grafik geçişi mantığını kullanabilirsiniz. 

> [!div class="nextstepaction"]
> [Gremlin kullanarak sorgulama](tutorial-query-graph.md)

