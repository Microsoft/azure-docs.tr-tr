---
title: Azure portal 'de gezgin sorgu aracında ara
titleSuffix: Azure Cognitive Search
description: Arama Gezgini Azure portal yerleşik olarak bulunur, içeriği keşfetmek ve Azure Bilişsel Arama sorguları doğrulamak için kullanışlıdır. Terim veya tümcecik araması için dizeler veya gelişmiş sözdizimi ile tam nitelikli arama ifadeleri girin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ff2986c4e90cb997df250d647bdfbd068d70e51f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112104"
---
# <a name="use-search-explorer-in-the-azure-portal-for-querying-documents-in-azure-cognitive-search"></a>Azure 'da belge sorgulamak için Azure portal arama Gezgini 'ni kullanın Bilişsel Arama 

Bu makalede, Azure portal **Arama Gezgini** 'ni kullanarak mevcut bir Azure bilişsel arama dizininin nasıl sorgulanyapılacağı gösterilir. Hizmetinizdeki mevcut herhangi bir dizine basit veya tam Lucene sorgu ifadeleri göndermek için komut çubuğundan arama Gezgini 'ni başlatabilirsiniz. 

   ![Portalda gezgin komutu ara](./media/search-explorer/search-explorer-cmd2.png "Portalda gezgin komutu ara")

## <a name="basic-search-strings"></a>Temel arama dizeleri

Aşağıdaki örneklerde, yerleşik gerçek emlak örnek dizini varsayılır. Bu dizini portalda verileri Içeri aktar sihirbazını kullanarak, veri kaynağı olarak **örnekler** ' i seçerek oluşturabilirsiniz.

### <a name="example-1---empty-search"></a>Örnek 1-boş arama

İçeriğinize ilk bakış için, bir terim sağlanmadıysa **Ara** ' ya tıklayarak boş bir arama yürütün. Boş bir arama ilk sorgu olarak faydalıdır çünkü belge oluşturmayı gözden geçirebilmeniz için tüm belgeleri geri döndürür. Boş bir aramada, Arama derecelendirmesi yoktur ve belgeler rastgele sırada (tüm belgeler için`"@search.score": 1`) döndürülür. Varsayılan olarak, 50 belge bir arama isteğinde döndürülür.

Boş bir arama için eşdeğer sözdizimi `*` veya `search=*`.

   ```Input
   search=*
   ```

   **Sonuçlar**
   
   ![Boş sorgu örneği](./media/search-explorer/search-explorer-example-empty.png "Nitelenmemiş veya boş sorgu örneği")

### <a name="example-2---free-text-search"></a>Örnek 2-ücretsiz metin arama

İşleçlere sahip veya olmayan serbest biçimli sorgular, özel bir uygulamadan Azure Bilişsel Arama gönderilen Kullanıcı tanımlı sorguların benzetimini yapmak için faydalıdır. Sorgu terimleri veya ifadeler sağladığınızda arama sırasının oynatıldığına dikkat edin. Aşağıdaki örnekte, ücretsiz bir metin araması gösterilmektedir.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Sonuçlar**

   CTRL-F ' y i kullanarak belirli bir ilgi koşulları için sonuçların içinde arama yapabilirsiniz.

   ![Serbest metin sorgusu örneği](./media/search-explorer/search-explorer-example-freetext.png "Serbest metin sorgusu örneği")

### <a name="example-3---count-of-matching-documents"></a>Örnek 3-eşleşen belge sayısı 

Bir dizinde bulunan eşleşmelerin sayısını almak için **$Count** ekleyin. Boş bir aramada, sayı dizindeki toplam belge sayısıdır. Nitelikli aramada, sorgu girişiyle eşleşen belge sayısıdır.

   ```Input1
   $count=true
   ```
   **Sonuçlar**

   ![Belge sayısı örneği](./media/search-explorer/search-explorer-example-count.png "Dizindeki eşleşen belge sayısı")

### <a name="example-4---restrict-fields-in-search-results"></a>Örnek 4-arama sonuçlarında alanları kısıtlama

**Arama Gezgini**'nde daha okunaklı çıkış için sonuçları açıkça adlandırılmış alanlarla sınırlamak üzere **$Select** ekleyin. Arama dizesini ve **$Count = true**tutmak için, **&** bağımsız değişkenlerini önek olarak ekleyin. 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Sonuçlar**

   ![Limit alanları örneği](./media/search-explorer/search-explorer-example-selectfield.png "Arama sonuçlarındaki alanları kısıtla")

### <a name="example-5---return-next-batch-of-results"></a>Örnek 5-sonraki sonuç toplu işlemini döndürür

Azure Bilişsel Arama, arama derecesine göre ilk 50 eşleşme döndürür. Sonraki eşleşen belge kümesini almak için, **$Top = 100 & $Skip = 50 '** i ekleyerek sonucu 100 belgeler olarak ayarlayın (varsayılan değer 50, en fazla 1000), ilk 50 belgeleri atlar. Derecelendirilen sonuçları almak için Sorgu terimi veya ifade gibi arama ölçütleri sağlamanız gerektiğini hatırlayın. Arama puanlarını, arama sonuçlarına ulaşmanıza daha derin bir şekilde azaldığına dikkat edin.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Sonuçlar**

   ![Toplu işlem arama sonuçları](./media/search-explorer/search-explorer-example-topskip.png "Sonraki arama sonuçları toplu işlemini döndür")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filtre ifadeleri (büyüktür, küçüktür, eşittir)

Serbest metin araması yerine kesin ölçütler belirtmek istediğinizde **$Filter** parametresini kullanın. Bu örnek 3 ' ten büyük yatak odaları arar:

   ```Input
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Sonuçlar**

   ![Filtre ifadesi](./media/search-explorer/search-explorer-example-filter.png "Ölçütlere göre filtrele")

## <a name="order-by-expressions"></a>Sıralama ifadesi

Sonuçları arama puanının yanı sıra başka bir alana göre sıralamak için **$OrderBy** ekleyin. Bunu test etmek için kullanabileceğiniz örnek bir ifade şunlardır:

   ```Input
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Sonuçlar**

   ![OrderBy ifadesi](./media/search-explorer/search-explorer-example-ordery.png "Sıralama düzenini değiştirme")

**$Filter** ve **$OrderBy** ifadeleri OData kurulumlarını. Daha fazla bilgi edinmek için bkz. [OData söz dizimini filtreleme](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Arama Gezgini 'ni başlatma

1. [Azure Portal](https://portal.azure.com), panodaki arama hizmeti sayfasını açın veya hizmet listesinde [hizmetinizi bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) .

2. Hizmete genel bakış sayfasında, **Arama Gezgini**' ne tıklayın.

   ![Portalda gezgin komutu ara](./media/search-explorer/search-explorer-cmd2.png "Portalda gezgin komutu ara")

3. Sorgulanacak dizini seçin.

   ![Sorgulanacak dizini seçin](./media/search-explorer/search-explorer-changeindex-se2.png "Dizini seçin")

4. İsteğe bağlı olarak, API sürümünü ayarlayın. Varsayılan olarak, geçerli genel olarak kullanılabilir API sürümü seçilidir, ancak kullanmak istediğiniz söz dizimi sürüme özgü ise Önizleme veya daha eski bir API seçebilirsiniz.

5. Dizin ve API sürümü seçildikten sonra, arama çubuğunda arama terimleri veya tam olarak nitelenmiş sorgu ifadeleri girin ve yürütmek için **Ara** ' yı tıklatın.

   ![Arama terimlerini girin ve ara 'yı tıklatın](./media/search-explorer/search-explorer-query-string-example.png "Arama terimlerini girin ve ara 'yı tıklatın")

**Arama Gezgini**'nde arama ipuçları:

+ Belge oluşturmayı ve içeriğini tamamen görüntüleyebilmeniz için sonuçlar ayrıntılı JSON belgeleri olarak döndürülür. Hangi alanların döndürüleceğini sınırlamak için örneklerde gösterilen sorgu ifadelerini kullanabilirsiniz.

+ Belgeler, dizinde **alınabilir** olarak işaretlenmiş tüm alanlardan oluşur. Portalda Dizin özniteliklerini görüntülemek için, aramaya Genel Bakış sayfasındaki **dizinler** listesinde *realestate-US-Sample* öğesine tıklayın.

+ Ticari bir Web tarayıcısına girebilecekleri gibi serbest biçimli sorgular, son kullanıcı deneyiminin test edilmesi için yararlıdır. Örneğin, yerleşik realestate örnek dizinini varsayarsak, "Seattle apartmanlar Gölü" girebilir ve sonra arama sonuçları içindeki terimleri bulmak için CTRL-F ' y i kullanabilirsiniz. 

+ Sorgu ve filtre ifadeleri Azure Bilişsel Arama tarafından desteklenen bir sözdiziminde ifade alınmalıdır. Varsayılan değer basit bir [sözdizimidir](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), ancak daha güçlü sorgular için isteğe bağlı olarak [tam Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) kullanabilirsiniz. [Filtre ifadeleri](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) bir OData sözdizimidir.


## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki kaynaklar ek sorgu söz dizimi bilgileri ve örnekler içerir.

 + [Basit sorgu söz dizimi](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene sorgu söz dizimi](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene sorgu örnekleri](search-query-lucene-examples.md) 
 + [OData Filtre ifadesinin söz dizimi](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
