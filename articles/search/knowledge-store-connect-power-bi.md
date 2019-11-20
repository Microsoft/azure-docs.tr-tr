---
title: Power BI ile bir bilgi deposuna (Önizleme) bağlanma
titleSuffix: Azure Cognitive Search
description: Analiz ve araştırma için Power BI bir Azure Bilişsel Arama bilgi deposu (Önizleme) bağlayın.
author: lisaleib
manager: nitinme
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 7b12f0f14003389d36e2df5bcffe7828c135cf2b
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715498"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Bilgi deposunu Power BI bağlama

> [!IMPORTANT] 
> bilgi deposu Şu anda genel önizleme aşamasındadır. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API sürüm 2019-05-06-önizleme](search-api-preview.md) , Önizleme özellikleri sağlar. Şu anda sınırlı sayıda portal desteği var ve .NET SDK desteği yok.

Bu makalede Power BI Desktop uygulamasındaki Power Query kullanarak bir bilgi deposuna nasıl bağlanacağınızı ve keşfedeceğinizi öğrenin. Şablonlarla daha hızlı çalışmaya başlayabilir veya sıfırdan özel bir pano oluşturabilirsiniz.

+ Bu kılavuzda kullanılan örnek bilgi deposunu oluşturmak için, [Azure Portal bilgi deposu oluşturma](knowledge-store-create-portal.md) ' daki adımları IZLEYIN veya [rest kullanarak bir Azure bilişsel arama bilgi deposu oluşturun](knowledge-store-create-rest.md) . Ayrıca, bilgi deposunu oluşturmak için kullandığınız Azure depolama hesabının adına, Azure portal erişim anahtarıyla birlikte ihtiyacınız olacaktır.

+ [Power BI Desktop yüklensin](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Örnek Power BI şablonu-yalnızca Azure portal

[Bilgi deponuzu Azure Portal kullanarak oluşturduysanız](knowledge-store-create-portal.md), Power BI görselleştirmeleri görüntülemek ve denemek için [Azure bilişsel arama Power BI şablon örneğini](https://github.com/Azure-Samples/cognitive-search-templates) kullanabilirsiniz. Bu şablon, **verileri Içeri aktarma** Sihirbazı ' nı adım adım yaptığınızda da indirilebilir.

Örnek şablon, bu makalenin geri kalanında belirtilen kurulum adımlarını otomatik olarak gerçekleştirir. Bununla birlikte, bilgi deponuzu oluşturmak için REST API kullandıysanız, şablonu atlayın ve bilgi deponuzu Power BI bağlamak için bu makaledeki geri kalan bölümleri kullanın. [Power BI Connect](#connect-with-power-bi)ile başlayın.

Örnek şablon, WordCloud ve Network Navigator gibi çeşitli görselleştirmeler içerir. Şablondaki bazı görselleştirmeler, örneğin konumlar Haritası ve varlık-grafik Görüntüleyici, [Azure Portal bilgi deposu oluşturma](knowledge-store-create-portal.md)bölümünde oluşturulan örnek bilgi deposu için verileri göstermez. Bunun nedeni, **verileri Içeri aktarma** Sihirbazı 'nda yalnızca bir AI zenginleştirme 'nin kullanılabilir olduğu bir alt kümesidir.

![Örnek Azure Bilişsel Arama Power BI şablonu](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Örnek Power BI şablonu")

## <a name="connect-with-power-bi"></a>Power BI ile bağlanma

1. Power BI Desktop başlatın ve **veri al**' a tıklayın.

1. **Veri al** penceresinde **Azure**' ı seçin ve ardından **Azure Tablo depolama**' yı seçin.

1. **Bağlan**'a tıklayın.

1. **Hesap adı veya URL 'si**Için Azure depolama hesabınızın adını girin (sizin IÇIN tam URL oluşturulur).

1. İstenirse, depolama hesabı anahtarını girin.

1. *Hotelbelge\sdocument*, *HotelReviewsSsKeyPhrases*ve *Hotelbelge\spages* tablolarını seçin. Bu tablolar, otel 'in Azure Tablo projeksiyonları, örnek verileri inceler ve bilgi deposu oluşturulduğunda seçilen AI zenginlerini içerir.

1. **Yükle**' ye tıklayın.

1. **Power Query düzenleyicisini**açmak için üst şeritte **sorguları Düzenle** ' ye tıklayın.

   ![Power Query açın](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Power Query açın")

1. *Hotelcode Ssındocument*' ı seçin ve ardından *partitionkey*, *rowkey*ve *timestamp* sütunlarını kaldırın. 

   ![Tabloları Düzenle](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Tabloları Düzenle")

1. *İçeriği*genişletmek için tablonun sağ üst tarafındaki karşıt oklu simgeye tıklayın. Sütun listesi göründüğünde, tüm sütunlar ' ı seçin ve ardından ' Metadata ' ile başlayan sütunların seçimini kaldırın. Seçilen sütunları göstermek için **Tamam** ' ı tıklatın.

   ![Tabloları Düzenle](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "İçeriği Genişlet")

1. Sütunun sol üst kısmındaki ABC-123 simgesine tıklayarak aşağıdaki sütunlar için veri türünü değiştirin.

   + *Content. Enlem* ve *Content. Boylam*için **ondalık sayı**' yı seçin.
   + *Content. reviews_date* ve *Content. reviews_dateAdded*için **Tarih/saat**' i seçin.

   ![Veri türlerini değiştirme](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Veri türlerini değiştirme")

1. *Hotelcode Ssspages*öğesini seçin ve ardından 9 ve 10. adımları yineleyerek sütunları silip *içeriği*genişletin.
1. *Content. Sentımentscore* için veri türünü **ondalık sayı**olarak değiştirin.
1. *HotelReviewsSsKeyPhrases* ' ı seçin ve sütunları silip *içeriği*genişletmek için 9. ve 10. adımları yineleyin. Bu tablo için veri türü değişikliği yok.

1. Komut çubuğunda **Kapat ve Uygula**' ya tıklayın.

1. Sol gezinti bölmesindeki model kutucuğuna tıklayın ve Power BI üç tablo arasındaki ilişkileri gösterir.

   ![İlişkileri doğrula](media/knowledge-store-connect-power-bi/powerbi-relationships.png "İlişkileri doğrula")

1. Her bir ilişkiye çift tıklayın ve **çapraz filtre yönünün** **her ikisine de**ayarlandığından emin olun.  Bu, bir filtre uygulandığında görsellerinizin yenilenmesini sağlar.

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Temizleme

Kendi aboneliğinizde çalışırken, sizin oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek için bir projenin sonunda iyi bir fikir olur. Çalışan kaynaklar sizin için ücret verebilir. Kaynakları tek tek silebilir veya kaynak grubunu silerek tüm kaynak kümesini silebilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu bilgi deposunu Depolama Gezgini kullanarak nasıl keşfedeceğinizi öğrenmek için aşağıdaki makaleye bakın.

> [!div class="nextstepaction"]
> [Depolama Gezgini ile görüntüle](knowledge-store-view-storage-explorer.md)

REST API 'Leri ve Postman kullanarak bilgi deposu oluşturmayı öğrenmek için aşağıdaki makaleye bakın.  

> [!div class="nextstepaction"]
> [REST 'te bilgi deposu oluşturma](knowledge-store-howto.md)
