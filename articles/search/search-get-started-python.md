---
title: "Hızlı başlangıç: Python 'da arama dizini oluşturma"
titleSuffix: Azure Cognitive Search
description: Bir arama dizini oluşturmayı, verileri yüklemeyi ve Python, Jupyter Notebook ve Azure.Documtaları kullanarak sorguları çalıştırmayı öğrenin. Python için istemci kitaplığını ara.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: 8b9c4792fa6dbdc70f657ce3c5f1757473a22fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225226"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebook"></a>Hızlı başlangıç: Jupyter Notebook kullanarak Python 'da Azure Bilişsel Arama dizini oluşturma

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell (REST)](search-get-started-powershell.md)
> * [C#](search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Portal](search-get-started-portal.md)
>

Python ile Azure Bilişsel Arama dizini oluşturan, yükleyen ve sorgulayan bir not defteri oluşturun ve Python için Azure SDK 'daki [Azure-Search-Belgeler kitaplığını](/python/api/overview/azure/search-documents-readme) kullanın. Bu makalede, adım adım bir not defteri adım oluşturma açıklanır. Alternatif olarak, [tamamlanmış bir Jupyter Python Not defteri indirebilir ve çalıştırabilirsiniz](https://github.com/Azure-Samples/azure-search-python-samples).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç için aşağıdaki hizmetler ve araçlar gereklidir.

* Python 3. x ve Jupyter Notebook sağlayan [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section).

* [Azure-Search-Belgeler paketi](https://pypi.org/project/azure-search-documents/)

* Geçerli aboneliğinizde [bir arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz katmanı kullanabilirsiniz. 

## <a name="copy-a-key-and-url"></a>Anahtar ve URL 'YI kopyalama

REST çağrıları için her istekte hizmet URL'sinin ve bir erişim anahtarının iletilmesi gerekir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

1. **Ayarlar**  >  **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

   ![HTTP uç noktası ve erişim anahtarı al](media/search-get-started-rest/get-url-key.png "HTTP uç noktası ve erişim anahtarı al")

Tüm istekler hizmetinize gönderilen her istekte bir API anahtarı gerektirir. İstek başına geçerli bir anahtara sahip olmak, isteği gönderen uygulama ve bunu işleyen hizmet arasında güven oluşturur.

## <a name="connect-to-azure-cognitive-search"></a>Azure Bilişsel Arama bağlanma

Bu görevde Jupyter Notebook başlatın ve Azure Bilişsel Arama bağlanabildiğinizi doğrulayın. Bunu, hizmetinizdeki dizinlerin bir listesini isteyerek gerçekleştirirsiniz. Anaconda3 ile Windows 'da Anaconda gezginini kullanarak bir not defteri başlatabilirsiniz.

1. Yeni bir Python3 Not defteri oluşturun.

1. İlk hücrede, [Azure Arama belgeleri](/python/api/azure-search-documents)de dahil olmak üzere Python IÇIN Azure SDK 'dan kitaplıkları yükleyin.

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents
    
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. İkinci hücrede, her istekte sabitler olacak istek öğelerini girin. Önceki bir adımda kopyaladığınız arama hizmeti adınızı, yönetici API anahtarınızı ve sorgu API anahtarınızı girin. Bu hücre Ayrıca belirli işlemler için kullanacağınız istemcileri ayarlar: bir dizin oluşturmak için [Searchındexclient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) ve bir dizini sorgulamak Için [searchclient](/python/api/azure-search-documents/azure.search.documents.searchclient) .

   ```python
    service_name = "YOUR-SEARCH-SERIVCE-NAME"
    admin_key = "YOUR-SEARCH-SERVICE-ADMIN-API-KEY"
    
    index_name = "hotels-quickstart"
    
    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
    
    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. Üçüncü hücrede, mevcut *oteller-hızlı başlangıç* dizinlerinin hizmetinizi temizlemek için bir delete_index işlemi çalıştırın. Dizinin silinmesi, aynı ada sahip başka bir *oteller-hızlı başlangıç* dizini oluşturmanıza olanak sağlar.

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. Her adımı çalıştırın.

## <a name="1---create-an-index"></a>1 - Dizin oluşturma

Bir dizinin gerekli öğeleri bir ad, alan koleksiyonu ve bir anahtar içerir. Alanlar koleksiyonu, hem yükleme verileri hem de sonuçlar döndürme için kullanılan bir mantıksal *arama belgesinin* yapısını tanımlar. 

Her alan, alanın nasıl kullanıldığını tanımlayan bir ad, tür ve özniteliklere sahiptir (örneğin, tam metin aranabilir, filtrelenebilir veya arama sonuçlarında alınabilir mi olduğunu belirtir). Bir dizin içinde, türündeki alanlardan biri `Edm.String` belge kimliği için *anahtar* olarak atanmalıdır.

Bu dizin "oteller-QuickStart" olarak adlandırılmıştır ve aşağıda gördüğünüz alan tanımlarına sahiptir. Diğer izlenecek yollarda kullanılan daha büyük bir [oteller dizininin](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) bir alt kümesidir. Bu hızlı başlangıçta breçekimi için kırpıyoruz.

1. Sonraki hücrede, şemayı sağlamak için aşağıdaki örneği bir hücreye yapıştırın.

    ```python
    # Specify the index schema
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
        
            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),
    
            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),
    
            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. Başka bir hücrede, isteği formüle yazın. Bu create_index isteği, arama hizmetinizin dizinler koleksiyonunu hedefler ve önceki hücrede belirttiğiniz dizin şemasını temel alan bir [Searchındex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) oluşturur.

    ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)
    
    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
    ```

1. Her adımı çalıştırın.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2-belge yükleme

Belge yüklemek için, işlem türü için bir [Dizin eylemi](/python/api/azure-search-documents/azure.search.documents.models.indexaction) kullanarak bir belge koleksiyonu oluşturun (karşıya yükleme, birleştirme ve karşıya yükleme, vb.). Belgeler GitHub 'daki [Hotelsdata](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) 'dan geliyor.

1. Yeni bir hücrede, dizin şemasına uygun dört belge sağlayın. Her belge için bir karşıya yükleme eylemi belirtin.

    ```python
    documents = [
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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
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
    ```  

1. Başka bir hücrede, isteği formüle yazın. Bu upload_documents isteği, oteller-Hızlı Başlangıç dizininin docs koleksiyonunu hedefler ve önceki adımda belirtilen belgeleri Bilişsel Arama dizinine iter.

    ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
    ```

1. Belgeleri arama hizmetinizde bir dizine göndermek için her adımı çalıştırın.

## <a name="3---search-an-index"></a>3 - Dizin arama

Bu adım, [Search. Client sınıfının](/python/api/azure-search-documents/azure.search.documents.searchclient) **Search** metodunu kullanarak bir dizinin nasıl sorgulanalınacağını gösterir.

1. Aşağıdaki adım, bir boş arama ( `search=*` ) yürüterek rastgele belgelerin derecelendirildi bir liste (arama puanı = 1,0) döndürüyor. Hiçbir ölçüt olmadığından, tüm belgeler sonuçlara dahildir. Bu sorgu her belgedeki alanların yalnızca ikisini yazdırır. Ayrıca `include_total_count=True` , sonuçlarda tüm belgelerin (4) sayısını almak için de ekler.

    ```python
    results =  search_client.search(search_text="*", include_total_count=True)
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Sonraki sorgu, arama ifadesine ("WiFi") tüm terimleri ekler. Bu sorgu, sonuçların yalnızca deyimdeki alanları içerdiğini belirtir `select` . Geri gelen alanları sınırlamak, kablo üzerinden geri gönderilen veri miktarını en aza indirir ve arama gecikmesini azaltır.

    ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
    ```

1. Ardından, bir filtre ifadesi uygulayarak yalnızca 4 ' ten büyük bir derecelendirme olan oteller azalan düzende sıralanır.

    ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')
    
    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
    ```

1. `search_fields`Tek bir alanla eşleşen kapsam sorgusuna ekleyin.

    ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')
    
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Modeller, model gezinti yapısını oluşturmak için kullanılabilecek etiketlerdir. Bu sorgu, kategori için modelleri ve sayıları döndürür.

    ```python
    results =  search_client.search(search_text="*", facets=["Category"])
    
    facets = results.get_facets()
    
    for facet in facets["Category"]:
        print("    {}".format(facet))
    ```

1. Bu örnekte, belirli bir belgeyi anahtarını temel alarak arayabilirsiniz. Genellikle bir Kullanıcı arama sonucunda bir belgeyi tıklattığında bir belge döndürmek istersiniz.

    ```python
    result = search_client.get_document(key="3")
    
    print("Details for hotel '3' are:")
    print("Name: {}".format(result["HotelName"]))
    print("Rating: {}".format(result["Rating"]))
    print("Category: {}".format(result["Category"]))
    ```

1. Bu örnekte, AutoComplete işlevini kullanacağız. Bu, genellikle arama kutusuna kullanıcı yazarken olası eşleşmelerin otomatik olarak tamamlanmasını sağlamak için bir arama kutusunda kullanılır.

   Dizin oluşturulduğunda, isteğin bir parçası olarak "SG" adlı bir öneri aracı de oluşturulmuştur. Öneri aracı tanımı, öneri aracı istekleriyle olabilecek olası eşleşmeleri bulmak için hangi alanların kullanılabileceğini belirtir. Bu örnekte, bu alanlar ' Etiketler ', ' Adres/Şehir ', ' adres/ülke '. Otomatik tamamlamayı benzetmek için, "sa" harflerini kısmi bir dize olarak geçirin. [Searchclient](/python/api/azure-search-documents/azure.search.documents.searchclient) 'ın AutoComplete yöntemi, geri dönüş olasılığı ile eşleşen geri gönderilir.

    ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')
    
    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
    ```

## <a name="clean-up"></a>Temizleme

Kendi aboneliğinizde çalışırken, projenin sonunda oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Basitleştirme olarak, bu hızlı başlangıç, oteller dizininin kısaltılmış bir sürümünü kullanır. Daha ilginç sorgular denemek için tam sürümü oluşturabilirsiniz. Tam sürümü ve tüm 50 belgelerini almak için, yerleşik örnek veri kaynaklarından *oteller-Sample* ' ı seçerek **veri alma** Sihirbazı 'nı çalıştırın.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Azure portal dizin oluşturma](search-get-started-portal.md)