---
title: 'Hızlı başlangıç: Visual Studio Code kullanarak Azure Bilişsel Arama kullanmaya başlama'
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama için Visual Studio Code uzantısını yüklemeyi ve kullanmayı öğrenin.
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/10/2021
ms.openlocfilehash: 3237a32a90e3964644ff84958a065656cdf7f3ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015854"
---
# <a name="get-started-with-azure-cognitive-search-using-visual-studio-code"></a>Visual Studio Code kullanarak Azure Bilişsel Arama kullanmaya başlama

Bu makalede, [Azure BILIŞSEL arama REST API 'leri](/rest/api/searchservice) ve [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)kullanarak REST API isteklerini etkileşimli olarak nasıl ifade edeceğinizi açıklanmaktadır. [Azure bilişsel arama (Önizleme) Için Visual Studio Code uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) ve bu yönergeler ile, herhangi bir kod yazmadan önce istek gönderebilir ve yanıtları görüntüleyebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!IMPORTANT] 
> Bu özellik şu anda genel önizleme aşamasındadır. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç için aşağıdaki hizmetler ve araçlar gereklidir. 

+ [Visual Studio Code](https://code.visualstudio.com/download)

+ [Visual Studio Code için Azure Bilişsel Arama (Önizleme)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)

+ Geçerli aboneliğinizde [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz. 

## <a name="install-the-extension"></a>Uzantıyı yükleme

[Vs Code](https://code.visualstudio.com)açarak başlayın. Etkinlik çubuğunda **Uzantılar** sekmesini seçin ve ardından *Azure bilişsel arama* aratın. Arama sonuçlarında uzantıyı bulun ve ardından **Install**' ı seçin.

![VS Code uzantısı bölmesi](media/search-get-started-rest/download-extension.png "VS Code uzantısı indiriliyor")

Alternatif olarak, [Azure bilişsel arama uzantısını](https://aka.ms/vscode-search) bir web tarayıcısına vs Code marketi 'nden yükleyebilirsiniz.

Zaten sahip değilseniz etkinlik çubuğunda yeni bir Azure sekmesi göründüğünü görmeniz gerekir.

![VS Code Azure bölmesi](media/search-get-started-rest/azure-pane.png "VS Code Azure bölmesi")

## <a name="connect-to-your-subscription"></a>Aboneliğinize bağlanma

**Azure 'Da oturum aç...** ' ı seçin ve Azure hesabınızda oturum açın.

Aboneliklerinizin göründüğünü görmeniz gerekir. Abonelikteki arama hizmetlerinin listesini görmek için aboneliği seçin.

![VS Code Azure abonelikleri](media/search-get-started-rest/subscriptions.png "VS Code abonelikler")

Görüntülenecek abonelikleri sınırlandırmak için, komut paletini açın (CTRL + SHIFT + P veya cmd + SHIFT + P) ve *Azure* için arama yapın veya *abonelikleri seçin*. Azure hesabınızda oturum açma ve kapatma için de kullanılabilecek komutlar vardır.

Arama hizmetini genişlettiğinizde, Bilişsel Arama kaynaklarından her biri için ağaç öğelerini görürsünüz: dizinler, veri kaynakları, Dizin oluşturucular, becerileri ve eş anlamlı haritalar.

![Azure Search ağacı VS Code](media/search-get-started-rest/search-tree.png "Azure Search ağacı VS Code")

Bu ağaç öğeleri, arama hizmetinizde sahip olduğunuz kaynakları göstermek için Genişletilebilir

## <a name="1---create-an-index"></a>1 - Dizin oluşturma

Azure Bilişsel Arama kullanmaya başlamak için önce bir arama dizini oluşturmanız gerekir. Bu, [Create ındex REST API](/rest/api/searchservice/create-index)kullanılarak yapılır. 

VS Code uzantısıyla, yalnızca isteğin gövdesi hakkında endişelenmeniz gerekir. Bu hızlı başlangıçta örnek dizin tanımı ve ilgili belgeler sağlıyoruz.

### <a name="index-definition"></a>Dizin tanımı

Aşağıdaki dizin tanımı, kurgusal oteller için örnek bir şemadır.

`fields`Koleksiyon, arama dizinindeki belgelerin yapısını tanımlar. Her alan, alanın nasıl kullanılabileceğini belirleyecek bir veri türüne ve birkaç ek özniteliğe sahiptir.

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Yeni bir dizin oluşturmak için, **dizinler** ' e sağ tıklayın ve ardından **Yeni dizin oluştur**' u seçin. Aşağıdakine benzer bir ada sahip bir düzenleyici `indexes-new-28c972f661.azsindex` açılır. 

Üstteki Dizin tanımını pencereye yapıştırın. Dosyayı kaydedin ve istendiğinde **karşıya yükle** ' yi seçin. Bu, dizini oluşturur ve ağaç görünümünde kullanılabilir hale gelir.

![Dizin oluşturma için GIF](media/search-get-started-rest/create-index.gif)

Dizin tanımınızda bir sorun varsa, hatayı açıklayan bir hata iletisi açılır.

![Dizin oluşturma hata iletisi](media/search-get-started-rest/create-index-error.png)

Bu durumda, sorunu giderip dosyayı yeniden kaydedin.

## <a name="2---load-documents"></a>2-belge yükleme

Dizini oluşturma ve dizini doldurma ayrı adımlardır. Azure Bilişsel Arama 'de Dizin, tüm aranabilir verileri içerir. Bu senaryoda, veriler JSON belgeleri olarak sağlanır. Bu görev için [REST API belge ekleme, güncelleştirme veya silme](/rest/api/searchservice/addupdate-or-delete-documents) işlemi kullanılıyor. 

VS Code yeni belgeler eklemek için:

1. `hotels-quickstart`Oluşturduğunuz dizini genişletin. **Belgeler** ' e sağ tıklayın ve **Yeni belge oluştur**' u seçin.

    ![Belge oluşturma](media/search-get-started-rest/create-document.png)

2. Bu, dizininizin şemasını çıkartılan bir JSON Düzenleyicisi açar.

    ![JSON belgesi oluşturma](media/search-get-started-rest/create-document-2.png)

3. Aşağıdaki JSON 'a yapıştırın ve dosyayı kaydedin. Değişikliklerinizi onaylamanızı isteyen bir istem açılır. Değişiklikleri kaydetmek için **karşıya yükle** ' yi seçin.

    ```json
    {
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
        } 
    }
    ```

4. Kalan üç belge için bu işlemi tekrarlayın

    Belge 2:
    ```json
    {
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
        } 
    }
    ```

    Belge 3:
    ```json
    {
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
        } 
    }
    ```

    Belge 4:
    ```json
    {
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
        }
    }
    ```

Bu noktada, belgeler bölümünde dört belgenin tümünün kullanılabilir olduğunu görmeniz gerekir.

![Tüm belgeleri karşıya yükledikten sonra durum](media/search-get-started-rest/create-document-finish.png)

## <a name="3---search-an-index"></a>3 - Dizin arama

Artık dizin ve belge kümesi yüklendikten sonra, [arama belgelerini](/rest/api/searchservice/search-documents)kullanarak bunlara yönelik sorgular verebilirsiniz REST API.

Bunu yapmak için VS Code:

1. Aramak istediğiniz dizine sağ tıklayın ve **arama dizini**' ni seçin. Bu, benzer bir ada sahip bir düzenleyici açar `sandbox-b946dcda48.azs` .

    ![uzantının arama görünümü](media/search-get-started-rest/search-vscode.png)

2. Basit bir sorgu, oto doldurulur. Sorguyu göndermek için **Ctrl + Alt + r** ya da **cmd + alt + r** tuşlarına basın. Sonuçları sol taraftaki bir pencerede görürsünüz.

    ![uzantıdaki arama sonuçları](media/search-get-started-rest/search-results.png)


### <a name="example-queries"></a>Örnek sorgular

Söz dizimi için bir fikir almak üzere birkaç başka sorgu örneği deneyin. Deneyebileceğiniz dört ek sorgu daha vardır. Aynı düzenleyiciye birden çok sorgu ekleyebilirsiniz. **Ctrl + Alt + r** ya da **cmd + alt + r** tuşlarına bastığınızda, imlecinizin hangi sorgunun gönderildiğini belirleyen çizgi görüntülenir.

![sorgular ve sonuçlar yan yana](media/search-get-started-rest/all-searches.png)

İlk sorguda `boutique` ve `select` yalnızca belirli alanları aradık. Yalnızca ihtiyacınız olan alanlara en iyi uygulamadır `select` . Bu, gereksiz verileri geri çekmek, sorgularınıza gecikme süresi ekleyebildiğinden. Sorgu ayrıca `$count=true` arama sonuçlarıyla Toplam sonuç sayısını döndürecek şekilde ayarlanır.

```
// Query example 1 - Search `boutique` with select and return count
search=boutique&$count=true&$select=HotelId,HotelName,Rating,Category
```

Sonraki sorguda arama terimini belirttik `wifi` ve yalnızca durumun eşit olduğu sonuçları döndürmek için bir filtre de içerir `'FL'` . Sonuçlar Ayrıca otel tarafından sıralanır `Rating` .

```
// Query example 2 - Search with filter, orderBy, select, and count
search=wifi&$filter=Address/StateProvince eq 'FL'&$select=HotelId,HotelName,Rating&$orderby=Rating desc
```

Sonra, arama parametresi kullanılarak tek bir aranabilir alanla sınırlandırılmıştır `searchFields` . Bu, yalnızca belirli alanlardaki eşleştirmelerle ilgilendiğinizi bildiğiniz durumlarda sorgunuzu daha verimli hale getirmek için harika bir seçenektir.

```
// Query example 3 - Limit searchFields
search=submlime cliff&$select=HotelId,HotelName,Rating&searchFields=HotelName
```

Bir sorguya dahil etmek için başka bir ortak seçenek de vardır `facets` . Modeller, kullanıcıların filtreleyebilecekleri değerleri bilmesini kolaylaştırmak için Kullanıcı arabiriminizdeki filtreleri oluşturmanızı sağlar.

```
// Query example 4 - Take the top two results, and show only HotelName and Category in the results
search=*&$select=HotelId,HotelName,Rating&searchFields=HotelName&facet=Category
```

## <a name="open-index-in-the-portal"></a>Portalda dizin açma

Portalda arama hizmetinizi görüntülemek istiyorsanız, arama hizmetinin adına sağ tıklayın ve **portalda aç**' ı seçin. Bu, sizi Azure portal arama hizmetine götürür.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kendi aboneliğinizde çalışırken, projenin sonunda oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Temel görevlerin nasıl gerçekleştirileceğini öğrenmiş olduğunuza göre, Dizin oluşturucular veya dizin oluşturma için içerik dönüştürmeleri ekleyen [bir zenginleştirme işlem hattı ayarlama](cognitive-search-tutorial-blob.md) gibi daha gelişmiş özellikler için daha ileri bir REST API çağrısı yapabilirsiniz. Sonraki adımınız için aşağıdaki bağlantıyı öneririz:

> [!div class="nextstepaction"]
> [Öğretici: Azure Bloblarından aranabilir içerik oluşturmak için REST ve AI kullanma](cognitive-search-tutorial-blob.md)