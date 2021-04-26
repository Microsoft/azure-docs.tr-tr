---
title: Azure portal 'de gezgin sorgu aracında ara
titleSuffix: Azure Cognitive Search
description: Bu Azure portal hızlı başlangıçta, sorgu sözdizimini öğrenmek, sorgu ifadelerini test etmek veya bir arama belgesi denetlemek için arama Gezgini 'ni kullanın. Azure Bilişsel Arama 'de arama Gezgini sorgu dizinleri.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: e9607a71ed6b045ac704c43bf4ea54c9f181bbf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179784"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Hızlı başlangıç: portalda sorgu çalıştırmak için arama Gezgini 'ni kullanma

**Arama Gezgini** , Azure bilişsel arama 'de arama dizinine karşı sorgu çalıştırmak için kullanılan yerleşik bir sorgu aracıdır. Bu araç sorgu söz dizimini öğrenmenizi, bir sorgu veya filtre ifadesini test etmeyi veya dizinde yeni içerik olup olmadığını denetleyerek veri yenilemeyi silmeyi kolaylaştırır.

Bu hızlı başlangıç, arama Gezginini göstermek için mevcut bir dizini kullanır. İstekler, ayrıntılı JSON belgeleri olarak döndürülen yanıtları içeren [arama REST API](/rest/api/searchservice/search-documents)kullanılarak formüle eklenir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki önkoşulları yerine getirin:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

+ Bir Azure Bilişsel Arama hizmeti. Geçerli aboneliğiniz kapsamında [bir hizmet oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz. 

+ Bu hızlı başlangıç için *reatastate-US-Sample-Index* kullanılır. Varsayılan değerleri kullanarak dizini oluşturmak için [hızlı başlangıç: Dizin oluşturma](search-import-data-portal.md) ' yı kullanın. Microsoft tarafından barındırılan yerleşik bir örnek veri kaynağı (**reatastate-US-Sample**), verileri sağlar.

## <a name="start-search-explorer"></a>Arama Gezginini Başlat

1. [Azure Portal](https://portal.azure.com), panodaki arama genel bakış sayfasını açın veya [hizmetinizi bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Komut çubuğundan arama Gezgini 'ni açın:

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Portalda gezgin komutu ara" border="true":::

    Ya da açık bir dizin üzerinde katıştırılmış **Arama Gezgini** sekmesini kullanın:

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Arama Gezgini sekmesi" border="true":::

## <a name="unspecified-query"></a>Belirtilmeyen sorgu

İçeriğe ilk bakış için, bir terim sağlanmadıysa **Ara** ' ya tıklayarak boş bir arama yürütün. Boş bir arama ilk sorgu olarak faydalıdır çünkü belge oluşturmayı gözden geçirebilmeniz için tüm belgeleri geri döndürür. Boş bir aramada, Arama derecelendirmesi yoktur ve belgeler rastgele sırada döndürülür ( `"@search.score": 1` tüm belgeler için). Varsayılan olarak, 50 belge bir arama isteğinde döndürülür.

Boş bir arama için eşdeğer sözdizimi, `*` veya `search=*` .
   
   ```http
   search=*
   ```

   **Sonuçlar**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Nitelenmemiş veya boş sorgu örneği" border="true":::

## <a name="free-text-search"></a>Serbest metin araması

İşleçlere sahip veya olmayan serbest biçimli sorgular, özel bir uygulamadan Azure Bilişsel Arama gönderilen Kullanıcı tanımlı sorguların benzetimini yapmak için faydalıdır. Yalnızca dizin tanımında **aranabilir** olarak öznitelikli alanlar eşleşmeler için taranır. 

Sorgu terimleri veya ifadeler gibi arama ölçütleri sağladığınızda, arama derecesi 'nin yürütmeye geldiğini unutmayın. Aşağıdaki örnekte, ücretsiz bir metin araması gösterilmektedir.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Sonuçlar**

   CTRL-F ' y i kullanarak belirli bir ilgi koşulları için sonuçların içinde arama yapabilirsiniz.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Serbest metin sorgusu örneği" border="true":::

## <a name="count-of-matching-documents"></a>Eşleşen belge sayısı 

Bir dizinde bulunan eşleşmelerin sayısını almak için **$Count = true** ekleyin. Boş bir aramada, sayı dizindeki toplam belge sayısıdır. Nitelikli aramada, sorgu girişiyle eşleşen belge sayısıdır. Hizmetin, en üstteki 50 eşleşmelerin varsayılan olarak döndürdüğünden emin olmak için, dizinde sonuçlardan fazlasına göre daha fazla eşleşme olabilir.

   ```http
   $count=true
   ```

   **Sonuçlar**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="Dizindeki eşleşen belge sayısı" border="true":::

## <a name="limit-fields-in-search-results"></a>Arama sonuçlarındaki alanları sınırlama

**Arama Gezgini**'nde daha okunaklı çıkış için sonuçları açıkça adlandırılmış alanlarla sınırlamak üzere [**$Select**](search-query-odata-select.md) ekleyin. Arama dizesini ve **$Count = true** tutmak için, bağımsız değişkenlerini ile öneki **&** . 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Sonuçlar**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Arama sonuçlarındaki alanları kısıtla" border="true":::

## <a name="return-next-batch-of-results"></a>Sonraki sonuç toplu işini döndür

Azure Bilişsel Arama, arama derecesine göre ilk 50 eşleşme döndürür. Sonraki eşleşen belge kümesini almak için **$Top = 100, &$Skip = 50 '** i ve sonuç olarak, 100 belgeler kümesini (varsayılan değer 50, en fazla 1000), ilk 50 belgeyi atlayarak ekleyin. Belge anahtarını (Listingıd) bir belgeyi tanımlamak için kontrol edebilirsiniz. 

Derecelendirilen sonuçları almak için Sorgu terimi veya ifade gibi arama ölçütleri sağlamanız gerektiğini hatırlayın. Arama puanlarını, arama sonuçlarına ulaşmanıza daha derin bir şekilde azaldığına dikkat edin.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Sonuçlar**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Sonraki arama sonuçları toplu işlemini döndür" border="true":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filtre ifadeleri (büyüktür, küçüktür, eşittir)

Serbest metin araması yerine kesin ölçütler belirtmek istediğinizde [**$Filter**](search-query-odata-filter.md) parametresini kullanın. Alanın, dizinde **filtrelenebilir** olarak ilişkilendirilmesi gerekir. Bu örnek 3 ' ten büyük yatak odaları arar:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Sonuçlar**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Ölçütlere göre filtrele" border="true":::

## <a name="order-by-expressions"></a>Sıralama ifadesi

Sonuçları arama puanının yanı sıra başka bir alana göre sıralamak için [**$OrderBy**](search-query-odata-orderby.md) ekleyin. Alan, dizinde **sıralanabilir** olmalıdır. Bunu test etmek için kullanabileceğiniz örnek bir ifade şunlardır:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Sonuçlar**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="Sıralama düzenini değiştirme" border="true":::

**$Filter** ve **$OrderBy** ifadeleri OData kurulumlarını. Daha fazla bilgi edinmek için bkz. [OData söz dizimini filtreleme](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Paketler

Bu hızlı başlangıçta, REST API kullanarak bir dizini sorgulamak için **Arama Gezgini** ' ni kullandınız.

+ Belge oluşturmayı ve içeriğini tamamen görüntüleyebilmeniz için sonuçlar ayrıntılı JSON belgeleri olarak döndürülür. Bir sorgu ifadesindeki **$Select** parametresi, döndürülecek alanları sınırlandırabilir.

+ Belgeler, dizinde **alınabilir** olarak işaretlenmiş tüm alanlardan oluşur. Portalda Dizin özniteliklerini görüntülemek için, aramaya Genel Bakış sayfasındaki **dizinler** listesinde *realestate-US-Sample* öğesine tıklayın.

+ Ticari bir Web tarayıcısına girebilecekleri gibi serbest biçimli sorgular, son kullanıcı deneyiminin test edilmesi için yararlıdır. Örneğin, yerleşik realestate örnek dizinini varsayarsak, "Seattle apartmanlar Gölü" girebilir ve sonra arama sonuçları içindeki terimleri bulmak için CTRL-F ' y i kullanabilirsiniz. 

+ Sorgu ve filtre ifadeleri, Azure Bilişsel Arama tarafından uygulanan bir sözdiziminde ifade edilir. Varsayılan değer basit bir [sözdizimidir](/rest/api/searchservice/simple-query-syntax-in-azure-search), ancak daha güçlü sorgular için isteğe bağlı olarak [tam Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) kullanabilirsiniz. [Filtre ifadeleri](/rest/api/searchservice/odata-expression-syntax-for-azure-search) bir OData sözdizimidir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kendi aboneliğinizde çalışırken, projenin sonunda oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Sorgu yapıları ve sözdizimi hakkında daha fazla bilgi için, API 'nin daha fazla bölümünden yararlanan sorgu ifadeleri oluşturmak için Postman veya eşdeğer bir araç kullanın. [Arama REST API](/rest/api/searchservice/search-documents) özellikle öğrenme ve araştırma için faydalıdır.

> [!div class="nextstepaction"]
> [Postman 'da temel sorgu oluşturma](search-get-started-rest.md)