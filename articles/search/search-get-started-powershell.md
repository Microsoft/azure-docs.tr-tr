---
title: "Hızlı başlangıç: REST API 'Leri kullanarak PowerShell 'de arama dizini oluşturma"
titleSuffix: Azure Cognitive Search
description: PowerShell 'in Invoke-RestMethod ve Azure Bilişsel Arama REST API kullanarak dizin oluşturmayı, verileri yüklemeyi ve sorguları çalıştırmayı açıklar.
manager: nitinme
author: heidisteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/04/2019
ms.openlocfilehash: e9b2b8e8b3585bc747efb5b2916ddf1fe07d3645
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792244"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>Hızlı başlangıç: REST API 'Leri kullanarak PowerShell 'de Azure Bilişsel Arama dizini oluşturma
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-create-index-portal.md)
> 

Bu makalede, PowerShell ve [azure BILIŞSEL arama REST API 'leri](https://docs.microsoft.com/rest/api/searchservice/)kullanarak bir Azure bilişsel arama dizini oluşturma, yükleme ve sorgulama işlemi adım adım açıklanmaktadır. Bu makalede, PowerShell komutlarının etkileşimli olarak nasıl çalıştırılacağı açıklanmaktadır. Alternatif olarak, aynı işlemleri gerçekleştiren [bir PowerShell betiğini indirebilir ve çalıştırabilirsiniz](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) .

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç için aşağıdaki hizmetler ve araçlar gereklidir. 

+ Sıralı ve etkileşimli adımlar için [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) kullanarak [PowerShell 5,1 veya sonraki bir sürümü](https://github.com/PowerShell/PowerShell).

+ Geçerli aboneliğinizde [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz. 

## <a name="get-a-key-and-url"></a>Anahtar ve URL al

REST çağrıları için her istekte hizmet URL'sinin ve bir erişim anahtarının iletilmesi gerekir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

2. **Ayarlar** > **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

![HTTP uç noktası ve erişim anahtarı al](media/search-get-started-postman/get-url-key.png "HTTP uç noktası ve erişim anahtarı al")

Tüm istekler hizmetinize gönderilen her istekte bir API anahtarı gerektirir. İstek başına geçerli bir anahtara sahip olmak, isteği gönderen uygulama ve bunu işleyen hizmet arasında güven oluşturur.

## <a name="connect-to-azure-cognitive-search"></a>Azure Bilişsel Arama bağlanma

1. PowerShell 'de, içerik türü ve API anahtarını depolamak için bir **$Headers** nesnesi oluşturun. Yönetim API anahtarını (-ADMIN-API-KEY), arama hizmetiniz için geçerli olan bir anahtarla değiştirin. Bu üst bilgiyi oturum süresince bir kez ayarlamanız yeterlidir, ancak bu üstbilgiyi her isteğe eklersiniz. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Hizmetin dizinler koleksiyonunu belirten bir **$URL** nesnesi oluşturun. Hizmet adını (-SEARCH-SERVICE-NAME) geçerli bir arama hizmeti ile değiştirin.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name"
    ```

3. Hizmete bir GET isteği göndermek ve bağlantıyı doğrulamak için **Invoke-RestMethod** komutunu çalıştırın. Hizmetten geri gönderilen yanıtları görüntüleyebilmek **için, ConvertTo-JSON** ekleyin.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Hizmet boşsa ve dizin yoksa, sonuçlar aşağıdaki örneğe benzer. Aksi takdirde, Dizin tanımlarının JSON gösterimini görürsünüz.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 - Dizin oluşturma

Portalı kullanmıyorsanız, verileri yükleyebilmeniz için önce hizmette bir dizin bulunmalıdır. Bu adım, dizini tanımlar ve hizmete gönderir. [Create ındex REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) , bu adım için kullanılır.

Bir dizinin gerekli öğeleri bir ad ve alanlar koleksiyonu içerir. Alanlar koleksiyonu bir *belgenin*yapısını tanımlar. Her bir alan, nasıl kullanıldığını tanımlayan bir ad, tür ve özniteliklere sahiptir (örneğin, tam metin aranabilir, filtrelenebilir veya arama sonuçlarında alınabilir mi olduğunu belirtir). Bir dizin içinde, `Edm.String` türündeki alanlardan biri belge kimliği için *anahtar* olarak atanmalıdır.

Bu dizin "oteller-QuickStart" olarak adlandırılmıştır ve aşağıda gördüğünüz alan tanımlarına sahiptir. Diğer izlenecek yollarda kullanılan daha büyük bir [oteller dizininin](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) bir alt kümesidir. Bu hızlı başlangıçta breçekimi için kırpıyoruz.

1. Dizin şemasını içeren bir **$Body** nesnesi oluşturmak için bu örneği PowerShell 'e yapıştırın.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. URI 'yi hizmetinize ve *oteller-hızlı başlangıç* dizininde dizin koleksiyonuna ayarlayın.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Sunucuda dizin oluşturmak için **$URL**, **$Headers**ve **$Body** ile komutu çalıştırın. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Sonuçlar şuna benzer görünmelidir (breçekimi için ilk iki alana kesilir):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Doğrulama için portaldaki dizinler listesini de kontrol edebilirsiniz.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2-belge yükleme

Belgeleri göndermek için, dizininizin URL uç noktasına bir HTTP POST isteği kullanın. Bu görevin REST API [belge ekleme, güncelleştirme veya silme](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Karşıya yüklemek istediğiniz belgeleri içeren **$Body** bir nesne oluşturmak için bu örneği PowerShell 'e yapıştırın. 

    Bu istek iki tam ve bir kısmi kayıt içerir. Kısmi kayıt, eksik belgeleri karşıya yükleyebileceğinizi gösterir. `@search.action` parametresi, dizin oluşturmanın nasıl yapılacağını belirtir. Geçerli değerler karşıya yükleme, birleştirme, mergeOrUpload ve DELETE değerleridir. MergeOrUpload davranışı, Hotelıd = 3 için yeni bir belge oluşturur ya da zaten varsa içeriği güncelleştirir.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. Uç noktayı *oteller-hızlı başlangıç* belgeleri koleksiyonuna ayarlayın ve Dizin işlemini (dizinler/oteller-hızlı başlangıç/docs/dizin) ekleyin.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. **$URL**, **$Headers**ve **$Body** kullanarak, dosyaları oteller-hızlı başlangıç dizinine yüklemek için komutunu çalıştırın.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Sonuçlar aşağıdaki örneğe benzer görünmelidir. 201 için bir [durum kodu](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)görmeniz gerekir.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 - Dizin arama

Bu adımda, [belgeleri ara API 'sini](https://docs.microsoft.com/rest/api/searchservice/search-documents)kullanarak bir dizinin nasıl sorgulanyapılacağı gösterilir.

Arama $urls tek tırnakları kullandığınızdan emin olun. Sorgu dizeleri **$** karakterler içerir ve tüm dize tek tırnak içine alınsa bunları kaçış zorunluluğunu atlayabilirsiniz.

1. Son noktayı *oteller-hızlı başlangıç* belgeleri koleksiyonuna ayarlayın ve sorgu dizesinde geçirilecek bir **arama** parametresi ekleyin. 
  
   Bu dize, rastgele belgeler için bir boş arama (Search = *) yürütür ve dereceli bir liste (arama puanı = 1,0) döndürüyor. Azure Bilişsel Arama, varsayılan olarak her seferinde 50 eşleşme döndürür. Yapılandırılmış olarak, bu sorgu tüm belge yapısını ve değerlerini döndürür. Sonuçlarda tüm belgelerin sayısını almak için **$Count = true** ekleyin.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. **$URL** hizmete göndermek için komutunu çalıştırın.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Sonuçlar aşağıdaki çıktıya benzer görünmelidir.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Söz dizimi için bir fikir almak üzere birkaç başka sorgu örneği deneyin. Dize araması yapabilir, tam $filter sorgular yapabilir, sonuç kümesini sınırlayabilir, aramanın belirli alanlarla kapsamını ve daha fazlasını yapabilirsiniz.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up"></a>Temizleme 

Kendi aboneliğinizde çalışırken, sizin oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek için bir projenin sonunda iyi bir fikir olur. Çalışan kaynaklar sizin için ücret verebilir. Kaynakları tek tek silebilir veya kaynak grubunu silerek tüm kaynak kümesini silebilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure Bilişsel Arama 'da içerik oluşturma ve bunlara erişme için temel iş akışını adım adım yapmak üzere PowerShell kullandınız. Kavramlar göz önünde bulundurularak, Azure veri kaynaklarından dizin oluşturma gibi daha gelişmiş senaryolara geçmeyi öneririz;

> [!div class="nextstepaction"]
> [REST öğreticisi: Azure Bilişsel Arama yarı yapılandırılmış verileri (JSON blob 'ları) dizin ve arama](search-semi-structured-data.md)