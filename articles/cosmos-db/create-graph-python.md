---
title: 'Hızlı Başlangıç: Python ile Gremlin API-Azure Cosmos DB'
description: Bu hızlı başlangıçta Azure portalı ve Python ile konsol uygulaması oluşturmak için Azure Cosmos DB Gremlin API’sinin nasıl kullanılacağı gösterilmektedir
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: lbosq
ms.openlocfilehash: 545f679b11295485567a817d144225b361a262ce
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815208"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Hızlı Başlangıç: Python ve Azure portal kullanarak Azure Cosmos DB grafik veritabanı oluşturma

> [!div class="op_single_selector"]
> * [Gremlin konsolu](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Bu hızlı başlangıçta GitHub’dan bir örneği kopyalayarak bir konsol uygulaması oluşturmak için Python ve Azure Cosmos DB [Gremlin API’sini](graph-introduction.md) nasıl kullanacağınız gösterilmektedir. Bu hızlı başlangıç ayrıca web tabanlı Azure portalını kullanarak bir Azure Cosmos DB hesabı oluşturma adımlarını da gösterir.   

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Bu hizmetle belge, tablo, anahtar/değer ve grafik veritabanlarını kolayca oluşturup sorgulayabilir ve tüm bunları yaparken Azure Cosmos DB'nin genel dağıtım ve yatay ölçeklendirme özelliklerinden faydalanabilirsiniz.  

> [!NOTE]
> Bu hızlı başlangıç için 20 Aralık 2017’den sonra oluşturulmuş bir grafik veritabanı hesabı gerekir. Mevcut hesaplar genel kullanılabilirliğe geçirildikten sonra Python’u destekleyecektir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Alternatif olarak, [Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)’yi ücretsiz olarak, Azure aboneliği olmadan ve herhangi bir taahhütte bulunmadan deneyebilirsiniz.

Buna ek olarak:
* [Python](https://www.python.org/downloads/) sürüm v3.5 ya da daha yeni
* [pip paket yöneticisi](https://pip.pypa.io/en/stable/installing/)
* [Git](https://git-scm.com/)
* [Gremlin için Python Sürücüsü](https://github.com/apache/tinkerpop/tree/master/gremlin-python)

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

2. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere bir klasör olarak değiştirmek için `cd` komutunu kullanın.  

    ```bash
    cd "C:\git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Veritabanı kaynaklarının kodda nasıl oluşturulduğunu öğrenmekle ilgileniyorsanız aşağıdaki kod parçacıklarını gözden geçirebilirsiniz. Kod parçacıklarının tümü C:\git-samples\azure-cosmos-db-graph-python-getting-started\ klasöründeki connect.py dosyasından alınmıştır. Aksi durumda, [Bağlantı dizenizi güncelleştirme](#update-your-connection-information) bölümüne atlayabilirsiniz. 

* Gremlin `client`, `connect.py` içindeki 104. satırda başlatılır:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* `connect.py` dosyasının başında bir dizi Gremlin adımı bildirilmiştir. Bu adımlar daha sonra `client.submitAsync()` yöntemi kullanılarak yürütülür:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>Bağlantı bilgilerinizi güncelleştirme

Şimdi, Azure portalına dönerek bağlantı bilgilerinizi kopyalayıp uygulamaya ekleyin. Bu ayarlar, uygulamanızın barındırılan veritabanıyla iletişim kurmasına olanak tanır.

1. [Azure portalında](https://portal.azure.com/), **Anahtarlar**’a tıklayın. 

    URI değerinin ilk parçasını kopyalayın.

    ![Azure portalında erişim anahtarı görüntüleme ve kopyalama, Anahtarlar sayfası](./media/create-graph-python/keys.png)

2. Connect.py dosyasını açın ve 104. satırdaki URI değerini şuradaki `<YOUR_ENDPOINT>` üzerine yapıştırın:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    İstemci nesnesinin URI kısmı artık şu koda benzer görünmelidir:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. `client` nesnesinin ikinci parametresini `<YOUR_DATABASE>` ve `<YOUR_COLLECTION_OR_GRAPH>` dizelerinin yerine geçecek şekilde değiştirin. Önerilen değerleri kullandıysanız, parametre şu kod gibi görünmelidir:

    `username="/dbs/sample-database/colls/sample-graph"`

    Tüm `client` nesnesi artık şu kod gibi görünmelidir:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. Azure portalında, kopyala düğmesini kullanarak PRIMARY KEY’i kopyalayın ve `password=<YOUR_PASSWORD>` parametresindeki `<YOUR_PASSWORD>` öğesine yapıştırın.

    Tüm `client` nesne tanımı artık şu kod gibi görünmelidir:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. `connect.py` dosyasını kaydedin.

## <a name="run-the-console-app"></a>Konsol uygulamasını çalıştırma

1. Git terminal penceresinde `cd` komutuyla azure-cosmos-db-graph-python-getting-started klasörüne ulaşın.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. Git terminal penceresinde aşağıdaki komutu kullanarak gerekli Python paketlerini yükleyin.

   ```
   pip install -r requirements.txt
   ```

3. Git terminal penceresinde, Python uygulamasını başlatmak için aşağıdaki komutları kullanın.
    
    ```
    python connect.py
    ```

    Terminal penceresinde grafiğe eklenmekte olan köşe ve kenarlar gösterilir. 
    
    Zaman aşımı hatası alırsanız, bağlantı bilgilerini, [Bağlantı bilgilerinizi güncelleştirme](#update-your-connection-information), konusunda belirtildiği şekilde güncelleştirdiğinizden emin olun ve son komutu çalıştırmayı yeniden deneyin. 
    
    Program durduktan sonra Enter tuşuna basın ve ardından İnternet tarayıcınızdaki Azure portalına geçin.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Örnek verileri inceleme ve ekleme

Köşeler ve kenarlar eklendikten sonra artık Veri Gezgini geri dönüp grafiğe eklenen köşeleri görebilir ve ek veri noktaları ekleyebilirsiniz.

1. **Veri Gezgini**’ne tıklayın, **sample-graph** öğesini genişletin, **Graph**’a ve son olarak **Filtre Uygula**’ya tıklayın. 

   ![Azure portalındaki Veri Gezgini'nde yeni belge oluşturma](./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png)

2. **Sonuçlar** listesinde, grafiğe üç yeni kullanıcı eklendiğine dikkat edin. Köşeleri sürükleyip bırakarak hareket ettirebilir, farenizin tekerleğini kaydırarak öğeleri yakınlaştırabilir ve uzaklaştırabilir, ayrıca çift okla grafiğin boyutunu genişletebilirsiniz. 

   ![Azure portalında Veri Gezgini'ndeki grafikte yeni köşeler](./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png)

3. Şimdi birkaç yeni kullanıcı ekleyelim. Grafa veri eklemek için **yeni köşe** düğmesine tıklayın.

   ![Azure portalındaki Veri Gezgini'nde yeni belge oluşturma](./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png)

4. *Kişi* etiketi girin.

5. Aşağıdaki özelliklerin her birini eklemek için **Özellik ekle** seçeneğine tıklayın. Graftaki her kişi için benzersiz özellikler oluşturabileceğinizi görürsünüz. Yalnızca kimliği anahtarı gereklidir.

    anahtar|değer|Notlar
    ----|----|----
    ba|/PK| 
    id|ashley|Köşe için benzersiz tanımlayıcı. Kimlik belirtmezseniz, bir kimlik otomatik olarak oluşturulur.
    cinsiyet|kadın| 
    teknoloji | java | 

    > [!NOTE]
    > Bu hızlı başlangıçta bölümlenmemiş bir koleksiyon oluşturun. Ancak koleksiyon oluşturma sırasında bir bölüm anahtarı belirterek bölümlendirilmiş bir koleksiyon oluşturursanız, daha sonra bölüm anahtarını her yeni köşede anahtar olarak eklemeniz gerekir. 

6. **Tamam** düğmesine tıklayın. Ekranın en altındaki **Tamam** seçeneğini görmek için ekranınızı genişletmeniz gerekebilir.

7. Tekrar **Yeni Köşe**’ye tıklayın ve ek yeni kullanıcıyı ekleyin. 

8. *Kişi* etiketi girin.

9. Aşağıdaki özelliklerin her birini eklemek için **Özellik ekle** seçeneğine tıklayın:

    anahtar|değer|Notlar
    ----|----|----
    ba|/PK| 
    id|rakesh|Köşe için benzersiz tanımlayıcı. Kimlik belirtmezseniz, bir kimlik otomatik olarak oluşturulur.
    cinsiyet|erkek| 
    okul|MIT| 

10. **Tamam** düğmesine tıklayın. 

11. Grafikteki tüm değerleri görüntülemek için, varsayılan `g.V()` filtresine sahip **Filtre Uygula** düğmesine tıklayın. Tüm kullanıcılar **Sonuç listesinde** gösterilir. 

    Daha fazla veri ekledikçe sonuçlarınızı sınırlamak için filtreleri kullanabilirsiniz. Veri Gezgini, varsayılan olarak bir grafikteki tüm köşeleri almak için `g.V()` kullanır. JSON biçimindeki bir grafikteki tüm köşelerin sayımını döndürmek için, bu değeri `g.V().count()` gibi farklı bir [grafik sorgusu](tutorial-query-graph.md) olarak değiştirebilirsiniz. Filtre değiştirdiyseniz, tüm sonuçları yeniden görüntülemek içinn filtreyi `g.V()` durumuna döndürün ve **Filtre Uygula**’ya tıklayın.

12. Artık rakesh ve ashley arasında bağlantı kurabiliriz. **Sonuç listesinde** **ashley**’nin seçili olduğundan emin olun ve ardından sağ alttaki **Hedefler**’in yanında bulunan Düzenle düğmesine tıklayın. **Özellikler** alanını görmek için pencerenizi genişletmeniz gerekebilir.

    ![Hedef grafikteki bir köşeyi değiştirme](./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png)

13. **Hedef** kutusunda *rakesh* yazın, **Kenar etiketi** kutusunda *tanıyor* yazın ve ardından onay işaretine tıklayın.

    ![Veri Gezgininde ashley ve rakesh arasında bir bağlantı ekleyin](./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png)

14. Sonuç listesinden **rakesh**’i seçin, ashley ve rakesh’in bağlantılı olduğunu görürsünüz. 

    ![Veri Gezgini'nde bağlı iki köşe](./media/create-graph-python/azure-cosmosdb-graph-explorer.png)

    Bu işlemle birlikte, bu öğreticideki kaynak oluşturma bölümünü tamamladınız. Grafiğinize köşe eklemeye, var olan köşeleri veya sorguları değiştirmeye devam edebilirsiniz. Şimdi, Azure Cosmos DB’nin sağladığı ölçümleri gözden geçirip kaynakları temizleyelim. 

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Azure Cosmos DB hesabı oluşturmayı, Veri Gezgini'ni kullanarak grafik oluşturmayı ve bir uygulamayı çalıştırmayı öğrendiniz. Artık daha karmaşık sorgular oluşturabilir ve Gremlin kullanarak güçlü grafik geçişi mantığını kullanabilirsiniz. 

> [!div class="nextstepaction"]
> [Gremlin kullanarak sorgulama](tutorial-query-graph.md)

