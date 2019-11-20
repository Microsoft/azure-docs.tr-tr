---
title: "Hızlı başlangıç: REST API 'Leri kullanarak Python 'da arama dizini oluşturma"
titleSuffix: Azure Cognitive Search
description: Python, jupi Not defterleri ve Azure Bilişsel Arama REST API kullanarak dizin oluşturmayı, verileri yüklemeyi ve sorguları çalıştırmayı açıklar.
author: heidisteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/04/2019
ms.openlocfilehash: c663fae47de1e161314aa3bf2fdb9966ae80d3c6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792271"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Hızlı başlangıç: Jupyter not defterlerini kullanarak Python 'da Azure Bilişsel Arama dizini oluşturma

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Portal](search-create-index-portal.md)
> 

Python ve [azure BILIŞSEL arama REST API 'lerini](https://docs.microsoft.com/rest/api/searchservice/)kullanarak bir Azure bilişsel arama dizini oluşturan, yükleyen ve sorgulayan bir Jupyter Not defteri oluşturun. Bu makalede, adım adım bir not defteri adım oluşturma açıklanır. Alternatif olarak, [tamamlanmış bir Jupyter Python Not defteri indirebilir ve çalıştırabilirsiniz](https://github.com/Azure-Samples/azure-search-python-samples).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç için aşağıdaki hizmetler ve araçlar gereklidir. 

+ Python 3. x ve Jupyıter not defterlerini sağlayan [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section).

+ Geçerli aboneliğinizde [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz katmanı kullanabilirsiniz. 

## <a name="get-a-key-and-url"></a>Anahtar ve URL al

REST çağrıları için her istekte hizmet URL'sinin ve bir erişim anahtarının iletilmesi gerekir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

1. **Ayarlar** > **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

![HTTP uç noktası ve erişim anahtarı al](media/search-get-started-postman/get-url-key.png "HTTP uç noktası ve erişim anahtarı al")

Tüm istekler hizmetinize gönderilen her istekte bir API anahtarı gerektirir. İstek başına geçerli bir anahtara sahip olmak, isteği gönderen uygulama ve bunu işleyen hizmet arasında güven oluşturur.

## <a name="connect-to-azure-cognitive-search"></a>Azure Bilişsel Arama bağlanma

Bu görevde, bir Jupyter Not defteri başlatın ve Azure Bilişsel Arama 'e bağlanabildiğinizi doğrulayın. Bunu, hizmetinizdeki dizinlerin bir listesini isteyerek gerçekleştirirsiniz. Anaconda3 ile Windows 'da Anaconda gezginini kullanarak bir not defteri başlatabilirsiniz.

1. Yeni bir Python3 Not defteri oluşturun.

1. İlk hücrede, JSON ile çalışma için kullanılan kitaplıkları yükleyin ve HTTP isteklerini formüle göre düzenleyin.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. İkinci hücrede, her istekte sabitler olacak istek öğelerini girin. Arama hizmeti adı 'nı (-SEARCH-SERVICE-NAME) ve yönetici API anahtarınızı (-ADMIN-API-KEY) geçerli değerlerle değiştirin. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   ConnectionError `"Failed to establish a new connection"`alırsanız, API anahtarının bir birincil veya ikincil yönetici anahtarı olduğunu ve tüm baştaki ve sondaki karakterlerin (`?` ve `/`) hazır olduğunu doğrulayın.

1. Üçüncü hücrede, isteği formüle yazın. Bu GET isteği, arama hizmetinizin dizinler koleksiyonunu hedefler ve mevcut dizinlerin ad özelliğini seçer.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Her adımı çalıştırın. Dizinler varsa, yanıt dizin adlarının bir listesini içerir. Aşağıdaki ekran görüntüsünde, hizmette zaten bir azureblob-index ve reatastate-US-Sample dizini vardır.

   ![Azure Bilişsel Arama HTTP istekleri içeren Jupyter not defterinde Python betiği](media/search-get-started-python/connect-azure-search.png "Azure Bilişsel Arama HTTP istekleri içeren Jupyter not defterinde Python betiği")

   Buna karşılık, boş bir dizin koleksiyonu şu yanıtı döndürür: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - Dizin oluşturma

Portalı kullanmıyorsanız, verileri yükleyebilmeniz için önce hizmette bir dizin bulunmalıdır. Bu adım, bir dizin şemasını hizmete göndermek için [Create ındex REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) kullanır.

Bir dizinin gerekli öğeleri bir ad, alan koleksiyonu ve bir anahtar içerir. Alanlar koleksiyonu bir *belgenin*yapısını tanımlar. Her alan, alanın nasıl kullanıldığını tanımlayan bir ad, tür ve özniteliklere sahiptir (örneğin, tam metin aranabilir, filtrelenebilir veya arama sonuçlarında alınabilir mi olduğunu belirtir). Bir dizin içinde, `Edm.String` türündeki alanlardan biri belge kimliği için *anahtar* olarak atanmalıdır.

Bu dizin "oteller-QuickStart" olarak adlandırılmıştır ve aşağıda gördüğünüz alan tanımlarına sahiptir. Diğer izlenecek yollarda kullanılan daha büyük bir [oteller dizininin](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) bir alt kümesidir. Bu hızlı başlangıçta breçekimi için kırpıyoruz.

1. Sonraki hücrede, şemayı sağlamak için aşağıdaki örneği bir hücreye yapıştırın. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. Başka bir hücrede, isteği formüle yazın. Bu PUT isteği, arama hizmetinizin dizinler toplamasını hedefler ve önceki hücrede belirttiğiniz dizin şemasını temel alan bir dizin oluşturur.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Her adımı çalıştırın.

   Yanıt, şemanın JSON gösterimini içerir. Aşağıdaki ekran görüntüsü yanıtın yalnızca bir kısmını gösteriyor.

    ![Dizin oluşturma isteği](media/search-get-started-python/create-index.png "Dizin oluşturma isteği")

> [!Tip]
> Dizin oluşturmayı doğrulamak için bir diğer yol ise portaldaki dizinler listesini denetleydir.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2-belge yükleme

Belgeleri göndermek için, dizininizin URL uç noktasına bir HTTP POST isteği kullanın. REST API [belge ekleme, güncelleştirme veya silme](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Belgeler GitHub 'daki [Hotelsdata](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) 'dan geliyor.

1. Yeni bir hücrede, dizin şemasına uygun dört belge sağlayın. Her belge için bir karşıya yükleme eylemi belirtin.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
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
    ]
    }
    ```   

2. Başka bir hücrede, isteği formüle yazın. Bu POST isteği, oteller-Hızlı Başlangıç dizininin docs koleksiyonunu hedefler ve önceki adımda belirtilen belgeleri gönderir.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Belgeleri arama hizmetinizde bir dizine göndermek için her adımı çalıştırın. Sonuçlar aşağıdaki örneğe benzer görünmelidir. 

    ![Bir dizine belge gönder](media/search-get-started-python/load-index.png "Bir dizine belge gönder")

## <a name="3---search-an-index"></a>3 - Dizin arama

Bu adımda, [arama belgelerini](https://docs.microsoft.com/rest/api/searchservice/search-documents)kullanarak bir dizinin nasıl sorgulankullanılacağı gösterilmektedir REST API.

1. Bir hücrede, boş bir aramayı yürüten bir sorgu ifadesi sağlayın (Search = *), düzensiz bir liste (arama puanı = 1,0) döndürür. Azure Bilişsel Arama, varsayılan olarak her seferinde 50 eşleşme döndürür. Yapılandırılmış olarak, bu sorgu tüm belge yapısını ve değerlerini döndürür. Sonuçlarda tüm belgelerin sayısını almak için $count = true ekleyin.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. Yeni bir hücrede, "oteller" ve "WiFi" terimlerinde arama yapmak için aşağıdaki örneği sağlayın. Arama sonuçlarına hangi alanların ekleneceğini belirlemek için $select ekleyin.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. Başka bir hücrede, bir isteği formüle koyun. Bu GET isteği, oteller-Hızlı Başlangıç dizininin docs koleksiyonunu hedefler ve önceki adımda belirttiğiniz sorguyu iliştirir.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Her adımı çalıştırın. Sonuçlar aşağıdaki çıktıya benzer görünmelidir. 

    ![Dizin arama](media/search-get-started-python/search-index.png "Dizin arama")

1. Söz dizimi için bir fikir almak üzere birkaç başka sorgu örneği deneyin. `searchstring` aşağıdaki örneklerle değiştirip arama isteğini yeniden çalıştırabilirsiniz. 

   Filtre Uygula: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   En üstteki iki sonuç alın:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Belirli bir alana göre sırala:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Temizleme

Kendi aboneliğinizde çalışırken, sizin oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek için bir projenin sonunda iyi bir fikir olur. Çalışan kaynaklar sizin için ücret verebilir. Kaynakları tek tek silebilir veya kaynak grubunu silerek tüm kaynak kümesini silebilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Basitleştirme olarak, bu hızlı başlangıç, oteller dizininin kısaltılmış bir sürümünü kullanır. Daha ilginç sorgular denemek için tam sürümü oluşturabilirsiniz. Tam sürümü ve tüm 50 belgelerini almak için, yerleşik örnek veri kaynaklarından *oteller-Sample* ' ı seçerek **veri alma** Sihirbazı 'nı çalıştırın.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Azure portal dizin oluşturma](search-get-started-portal.md)
