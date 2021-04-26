---
title: Hızlı başlangıç-Azure portal Azure Cosmos DB kaynak oluşturma
description: Bu hızlı başlangıçta Azure portal kullanarak bir Azure Cosmos veritabanı, kapsayıcı ve öğeleri oluşturma gösterilmektedir.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/10/2020
ms.openlocfilehash: 041b930a0c2fa82727216b4ca0c8367387d0d801
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367273"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Hızlı başlangıç: Azure portal bir Azure Cosmos hesabı, veritabanı, kapsayıcı ve öğe oluşturma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Azure portalı](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Azure Cosmos DB, bir bütün olarak genel dağıtım ve yatay Azure Cosmos DB ölçek özelliğinden yararlanan anahtar/değer veritabanlarını, belge veritabanlarını ve grafik veritabanlarını hızlıca oluşturmak ve sorgulamak için kullanabilirsiniz. 

Bu hızlı başlangıçta Azure portal kullanarak Azure Cosmos DB bir [SQL API](./introduction.md) hesabı oluşturma, bir belge veritabanı ve kapsayıcı oluşturma ve kapsayıcıya veri ekleme işlemlerinin nasıl yapılacağı gösterilmiştir. 

## <a name="prerequisites"></a>Önkoşullar

Bir Azure aboneliği veya ücretsiz Azure Cosmos DB deneme hesabı
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB hesabı oluşturma

Azure Cosmos DB hesabı oluşturmak için [Azure portalına](https://portal.azure.com/) gidin. **Azure Cosmos DB** için arama yapın ve bunu seçin.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png" alt-text="Azure portalındaki Veritabanları bölmesi":::

1. **Add (Ekle)** seçeneğini belirleyin.
1. **Azure Cosmos DB Hesabı Oluştur** sayfasında, yeni Azure Cosmos hesabına yönelik temel ayarları girin. 

    |Ayar|Değer|Açıklama |
    |---|---|---|
    |Abonelik|Abonelik adı|Bu Azure Cosmos hesabı için kullanmak istediğiniz Azure aboneliğini seçin. |
    |Kaynak Grubu|Kaynak grubu adı|Bir kaynak grubu seçin veya **Yeni oluştur** seçeneğini belirleyin ve yeni kaynak grubu için benzersiz bir ad girin. |
    |Hesap Adı|Benzersiz bir ad|Azure Cosmos hesabınızı tanımlayan bir ad girin. URI’nizi oluşturmak için sağladığınız ada *documents.azure.com* ekleneceği için benzersiz bir ad kullanın.<br><br>Ad yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. Ad, 3-31 karakter arası uzunlukta olmalıdır.|
    |API|Oluşturulacak hesap türü|SQL söz dizimini kullanarak belge veritabanı ve sorgusu oluşturmak için **Cekirdek (SQL)** seçeneğini belirleyin. <br><br>API, oluşturulacak hesap türünü belirler. Azure Cosmos DB beş API sağlar: belge verileri için çekirdek (SQL) ve MongoDB, Graf verileri için Gremlin, Azure tablosu ve Cassandra. Şu anda, her API için farklı bir hesap oluşturmanız gerekir. <br><br>[SQL API’si hakkında daha fazla bilgi edinin](introduction.md).|
    |Kapasite modu|Sağlanan aktarım hızı veya sunucusuz|[Sağlanan aktarım](set-throughput.md) hızı modunda bir hesap oluşturmak için **sağlanan aktarım hızı** ' nı seçin. [Sunucusuz modda bir](serverless.md) hesap oluşturmak Için **sunucusuz** ' ı seçin.|
    |Ücretsiz katman indirimi Azure Cosmos DB Uygula|Uygula veya Uygula|Azure Cosmos DB ücretsiz katman sayesinde ilk 400 RU/sn ve 5 GB depolama alanını hesapta ücretsiz olarak alırsınız. [Ücretsiz katman](https://azure.microsoft.com/pricing/details/cosmos-db/)hakkında daha fazla bilgi edinin.|
    |Konum|Kullanıcılarınıza en yakın bölge|Azure Cosmos DB hesabınızın barındırılacağı coğrafi konumu seçin. Verilere en hızlı erişimi sağlamak için kullanıcılarınıza en yakın olan konumu kullanın.|
    |Hesap Türü|Üretim veya üretim dışı|Hesap bir üretim iş yükü için kullanılacaksa, **Üretim** ' ı seçin. Hesap üretim dışı, örneğin geliştirme, test, QA veya hazırlama için kullanılacaksa, **Üretim dışı** seçeneğini belirleyin. Bu, Portal deneyimini denetleyen ancak temel alınan Azure Cosmos DB hesabını etkilemeyen bir Azure Kaynak etiketi ayarıdır. Bu değeri dilediğiniz zaman değiştirebilirsiniz.|
    |Coğrafi Yedeklilik|Etkinleştir veya devre dışı bırak|Bölgenizi bir çift bölge ile eşleştirerek hesabınızda genel dağıtımı etkinleştirin veya devre dışı bırakın. Daha sonra hesabınıza daha fazla bölge ekleyebilirsiniz.|
    |Birden Çok Bölgeli Yazmalar|Etkinleştir veya devre dışı bırak|Çok bölgeli yazma özelliği, veritabanları ve iş parçacıklarınız için dünya genelindeki sağlanan aktarım hızı avantajlarından yararlanmanıza olanak sağlar.|
    |Kullanılabilirlik Alanları|Etkinleştir veya devre dışı bırak|Kullanılabilirlik Alanları uygulamanızın kullanılabilirliğini ve esnekliğini daha da artırmanıza yardımcı olur.|

> [!NOTE]
> Azure aboneliği başına en fazla bir ücretsiz katman Azure Cosmos DB hesabınız olabilir ve hesabı oluştururken kabul etmeniz gerekir. Ücretsiz katman indirimi uygulama seçeneğini görmüyorsanız bu, abonelikteki başka bir hesabın ücretsiz katmanla zaten etkinleştirildiği anlamına gelir.

> [!NOTE]
> **Kapasite modu** olarak **sunucusuz** ' i seçerseniz aşağıdaki seçenekler kullanılamaz:
> - Ücretsiz Katman İndirimi Uygula
> - Coğrafi yedeklilik
> - Birden Çok Bölgeli Yazmalar
   
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Azure Cosmos DB için yeni hesap sayfası":::

1. **Gözden geçir ve oluştur**’u seçin. **Ağ** ve **Etiketler** bölümlerini atlayabilirsiniz.

1. Hesap ayarlarını gözden geçirip **Oluştur** seçeneğini belirleyin. Hesabın oluşturulması birkaç dakika sürer. Portal sayfasında **Dağıtımınız tamamlandı** iletisinin görüntülenmesini bekleyin. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Azure portaldaki Bildirimler bölmesi":::

1. Azure Cosmos DB hesabı sayfasına gitmek için **Kaynağa git** seçeneğini belirleyin. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png" alt-text="Azure Cosmos DB hesabı sayfası":::

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Veritabanı ve kapsayıcı ekleme 

Veritabanı ve kapsayıcı oluşturmak için Azure portal Veri Gezgini kullanabilirsiniz. 

1.  Azure Cosmos DB hesabı sayfanızda sol gezinti **Veri Gezgini** seçin ve ardından **yeni kapsayıcı**' yı seçin. 
    
    **Kapsayıcı Ekle** penceresini görmek için sağa kaydırmanız gerekebilir.
    
    :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png" alt-text="Azure portalındaki Veri Gezgini, Kapsayıcı Ekle bölmesi":::
    
1.  **Kapsayıcı Ekle** bölmesinde, yeni kapsayıcının ayarlarını girin.
    
    |Ayar|Önerilen değer|Açıklama
    |---|---|---|
    |**Veritabanı Kimliği**|ToDoList|Yeni veritabanının adı olarak *ToDoList* girin. Veritabanı adları 1 ila 255 karakterden oluşmalıdır ve `/, \\, #, ?` boşluk içeremez veya sonunda boşluk olamaz. Veritabanı **Işleme sağlama** seçeneğini kontrol edin, veritabanı içindeki tüm kapsayıcılar üzerinde veritabanı için sağlanan aktarım hızını paylaşmanıza olanak sağlar. Bu seçenek maliyet tasarruflarıyla de yardımcı olur. |
    |**İşleme hızı**|400|Aktarım hızını saniyede 400 istek birimi (RU/s) olarak bırakın. Daha sonra gecikme süresini azaltmak isterseniz işlem hızının ölçeğini artırabilirsiniz.<br><br>**Not**: Bu ayar, sunucusuz hesapta yeni bir kapsayıcı oluşturulurken kullanılamaz.| 
    |**Kapsayıcı Kimliği**|Öğeler|*Öğeleri* yeni kapsayıcının adı olarak girin. Kapsayıcı kimliklerinin karakter gereksinimleri, veritabanı adlarına ilişkin karakter gereksinimleri ile aynıdır.|
    |**Bölüm anahtarı**| /kategori| Bu makalede açıklanan örnek, bölüm anahtarı olarak */category* kullanır.|

    
    Bu örnek için **benzersiz anahtarlar** eklemeyin. Benzersiz anahtarlar, bölüm anahtarı başına bir veya daha fazla değerin benzersizliğini sağlayarak veritabanına veri bütünlüğü katmanı eklemenizi sağlar. Daha fazla bilgi için bkz. [Azure Cosmos DB Içindeki benzersiz anahtarlar](unique-keys.md).
    
1.  **Tamam**’ı seçin. Veri Gezgini yeni veritabanını ve oluşturduğunuz kapsayıcıyı görüntüler.

## <a name="add-data-to-your-database"></a>Veritabanınıza veri ekleme

Veri Gezgini kullanarak yeni veritabanınıza veri ekleyin.

1. **Veri Gezgini**' de, **ToDoList** veritabanını genişletin ve **öğeler** kapsayıcısını genişletin. Sonra, **öğeler**' i ve sonra **Yeni öğe**' yi seçin. 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="Azure portalındaki Veri Gezgini'nde yeni belge oluşturma":::
   
1. **Belgeler** bölmesinin sağ tarafındaki belgeye aşağıdaki yapıyı ekleyin:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. **Kaydet**’i seçin.
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="JSON verilerini kopyalayın ve Azure portal Veri Gezgini Kaydet ' i seçin":::
   
1. **Yeni belge** ' yi yeniden seçin ve benzersiz `id` ve istediğiniz diğer özellikleri ve değerleri içeren başka bir belge oluşturun ve kaydedin. Azure Cosmos DB verileriniz üzerinde herhangi bir şema uygulamayan, belgeleriniz herhangi bir yapıya sahip olabilir.

## <a name="query-your-data"></a>Verilerinizi sorgulayın

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Yalnızca veritabanını silmek ve gelecekte Azure Cosmos hesabını kullanmak istiyorsanız, aşağıdaki adımlarla veritabanını silebilirsiniz:

* Azure Cosmos hesabınıza alındı.
* **Veri Gezgini** açın, silmek istediğiniz veritabanına sağ tıklayın ve **veritabanını sil**' i seçin.
* Silme işlemini onaylamak için veritabanı KIMLIĞI/veritabanı adını girin. 

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Veri Gezgini kullanarak bir Azure Cosmos DB hesabı oluşturmayı, veritabanı ve kapsayıcı oluşturmayı öğrendiniz. Şimdi Azure Cosmos DB hesabınıza ek veriler aktarabilirsiniz. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB hesabınıza veri aktarma](import-data.md)