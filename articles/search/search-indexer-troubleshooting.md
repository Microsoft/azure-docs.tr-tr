---
title: Ortak arama Dizin Oluşturucu sorunlarını giderme
titleSuffix: Azure Cognitive Search
description: Veri kaynağı bağlantısı, güvenlik duvarı ve eksik belgeler dahil olmak üzere Azure Bilişsel Arama 'daki Dizin oluşturucularla ilgili hataları ve yaygın sorunları giderin.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efdd9666c8876ddaf12b9555fa66beb62c56e93e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740074"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Azure Bilişsel Arama ortak Dizin Oluşturucu sorunlarını giderme

Dizin oluşturucular, verileri Azure Bilişsel Arama 'da dizin oluştururken çeşitli sorunlar halinde çalışabilir. Hatanın ana kategorileri şunlardır:

* [Bir veri kaynağına veya diğer kaynaklara bağlanma](#connection-errors)
* [Belge işleme](#document-processing-errors)
* [Bir dizine belge alımı](#index-errors)

## <a name="connection-errors"></a>Bağlantı hataları

> [!NOTE]
> Dizin oluşturucular, Azure ağ güvenlik mekanizmaları tarafından güvenliği sağlanan veri kaynaklarına ve diğer kaynaklara erişmek için sınırlı desteğe sahiptir. Şu anda, Dizin oluşturucular yalnızca ilgili IP adres aralığı kısıtlama mekanizmaları veya varsa NSG kuralları aracılığıyla veri kaynaklarına erişebilir. Desteklenen her veri kaynağına erişme ayrıntıları aşağıda bulunabilir.
>
> Tam etki alanı adının (örn.) ping işlemi gerçekleştirerek arama hizmetinizin IP adresini bulabilirsiniz `<your-search-service-name>.search.windows.net` .
>
> Hizmet etiketinin IP adresi aralığını `AzureCognitiveSearch` [](../virtual-network/service-tags-overview.md#available-service-tags) [Indirilebilir JSON dosyalarını](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) veya [hizmet etiketi bulma API 'si](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview)aracılığıyla bulabilirsiniz. IP adresi aralığı haftalık olarak güncelleştirilir.

### <a name="configure-firewall-rules"></a>Güvenlik duvarı kurallarını yapılandırma

Azure Storage, CosmosDB ve Azure SQL yapılandırılabilir bir güvenlik duvarı sağlar. Güvenlik Duvarı etkinleştirildiğinde belirli bir hata iletisi yoktur. Genellikle, güvenlik duvarı hataları geneldir ve veya gibi `The remote server returned an error: (403) Forbidden` görünür `Credentials provided in the connection string are invalid or have expired` .

Dizin oluşturucularının böyle bir örnekteki bu kaynaklara erişmesine izin vermek için 2 seçenek vardır:

* **Tüm ağlardan** erişime izin vererek (uygulanabiliyorsa) güvenlik duvarını devre dışı bırakın.
* Alternatif olarak, `AzureCognitiveSearch` kaynağınızın güvenlik duvarı kurallarında (IP adresi aralığı kısıtlaması), arama HIZMETINIZIN IP adresi ve [hizmet](../virtual-network/service-tags-overview.md#available-service-tags) IP adresi aralığı için erişime izin verebilirsiniz.

Her veri kaynağı türü için IP adresi aralığı kısıtlamalarını yapılandırmaya ilişkin ayrıntılar aşağıdaki bağlantılardan bulunabilir:

* [Azure Depolama](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Kısıtlama**: Azure depolama için yukarıdaki belgelerde belirtildiği gıbı, IP adres aralığı kısıtlamaları yalnızca arama hizmetiniz ve depolama hesabınız farklı bölgelerde olduğunda çalışır.

Azure işlevleri ( [özel bir Web API 'si](cognitive-search-custom-skill-web-api.md)olarak kullanılabilir), [IP adresi kısıtlamalarını](../azure-functions/ip-addresses.md#ip-address-restrictions)da destekler. Yapılandırılacak IP adresleri listesi, arama hizmetinizin IP adresi ve hizmet etiketinin IP adresi aralığıdır `AzureCognitiveSearch` .

Azure VM 'de SQL Server 'daki verilere erişim ayrıntıları [burada](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) özetlenmiştir

### <a name="configure-network-security-group-nsg-rules"></a>Ağ güvenlik grubu (NSG) kurallarını yapılandırma

Bir SQL yönetilen örneğindeki verilere erişirken veya bir Azure VM, [özel Web API 'si yeteneği](cognitive-search-custom-skill-web-api.md)için Web hizmeti URI 'si olarak kullanıldığında, MÜŞTERILERIN belirli IP adresleriyle ilgilenmemelidir.

Bu gibi durumlarda, Azure VM veya SQL yönetilen örneği bir sanal ağ içinde bulunacak şekilde yapılandırılabilir. Daha sonra bir ağ güvenlik grubu, sanal ağ alt ağlarının ve ağ arabirimlerinin içinde ve dışında akabilir ağ trafiği türünü filtrelemek için yapılandırılabilir.

`AzureCognitiveSearch`Hizmet etiketi, gelen [NSG KURALLARıNDA](../virtual-network/manage-network-security-group.md#work-with-security-rules) , IP adresi aralığını aramak gerekmeden doğrudan kullanılabilir.

SQL yönetilen örneğindeki verilere erişim hakkında daha fazla ayrıntı [burada](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md) özetlenmiştir

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "Dizin oluşturma" etkin değil

Azure Bilişsel Arama Cosmos DB Dizin oluşturma üzerinde örtük bir bağımlılığa sahiptir. Cosmos DB Otomatik Dizin oluşturmayı kapatırsanız Azure Bilişsel Arama başarılı bir durum döndürür, ancak kapsayıcı içerikleri dizinlemeye başarısız olur. Ayarları denetleme ve Dizin oluşturmayı açma hakkında yönergeler için, bkz. [Azure Cosmos DB Dizin oluşturmayı yönetme](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

### <a name="sharepoint-online-conditional-access-policies"></a>SharePoint Online koşullu erişim ilkeleri

Bir SharePoint Online Indexer oluştururken, bir cihaz kodu sağladıktan sonra AAD uygulamanızda oturum açmanızı gerektiren bir adımla devam edersiniz. "Oturum açma işlemi başarılı oldu, ancak yöneticiniz, cihazın yönetilmek üzere erişim isteğinde bulunmasını istiyor" iletisini alırsanız, bir [koşullu erişim](https://review.docs.microsoft.com/azure/active-directory/conditional-access/overview) ilkesi nedeniyle dizin oluşturucunun SharePoint Online belge kitaplığına erişmesi olasıdır.

İlkeyi, dizin oluşturucunun belge kitaplığına erişmesine izin verecek şekilde güncelleştirmek için aşağıdaki adımları izleyin:

1. Azure portal açın ve **Azure AD koşullu erişim** araması yapın ve ardından sol menüdeki **ilkeler** ' i seçin. Bu sayfayı görüntülemek için erişiminiz yoksa, erişimi olan veya erişimi olan birini bulmanız gerekir.

1. Hangi ilkenin SharePoint Online Indexer 'ın belge kitaplığına erişimini engellediğini belirleme. Dizin oluşturucuyu engelliyor olabilecek ilke, **Kullanıcılar ve gruplar** bölümünde Dizin Oluşturucu oluşturma adımı sırasında kimlik doğrulaması için kullandığınız kullanıcı hesabını içerir. İlke şu **koşullara** da sahip olabilir:
    * **Windows** platformlarını kısıtlayın.
    * **Mobil uygulamaları ve Masaüstü istemcilerini** kısıtlayın.
    * **Cihaz durumunun** **Evet** olarak yapılandırıldığını.

1. Dizin oluşturucuyu engelleyen bir ilke olduğunu doğruladıktan sonra, Dizin Oluşturucu için bir istisna yapmanız gerekir. Arama hizmeti IP adresini alın.

    1. Arama hizmetinizin tam etki alanı adını (FQDN) alın. Bu, şöyle görünecektir `<search-service-name>.search.windows.net` . Azure portal arama hizmetinize arayarak FQDN 'yi bulabilirsiniz.

   ![Hizmet FQDN 'SI al](media\search-indexer-howto-secure-access\search-service-portal.png "Hizmet FQDN 'SI al")

    Arama hizmetinin IP adresi, `nslookup` FQDN 'nin bir (veya a) gerçekleştirerek elde edilebilir `ping` . Aşağıdaki örnekte, Azure Storage güvenlik duvarında gelen bir kurala "150.0.0.1" ekleyeceksiniz. Azure depolama hesabına erişebilmek için, arama hizmeti dizin oluşturucunun güvenlik duvarı ayarları güncelleştirildikten sonra 15 dakika kadar sürebilir.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. Bölgeniz için Dizin Oluşturucu yürütme ortamı için IP adresi aralıklarını alın.

    Ek IP adresleri, dizin oluşturucunun [çok kiracılı yürütme ortamından](search-indexer-securing-resources.md#indexer-execution-environment)kaynaklanan istekler için kullanılır. Bu IP adresi aralığını hizmet etiketinden alabilirsiniz.

    Hizmet etiketi için IP adresi aralıkları `AzureCognitiveSearch` [bulma API 'si (Önizleme)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) veya [indirilebilir JSON dosyası](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)aracılığıyla elde edilebilir.

    Bu kılavuzda, arama hizmetinin Azure genel bulutu olduğu varsayıldığında, [Azure genel JSON dosyası](https://www.microsoft.com/download/details.aspx?id=56519) indirilmelidir.

   ![JSON dosyasını indir](media\search-indexer-troubleshooting\service-tag.png "JSON dosyasını indir")

    Arama hizmetinin Orta Batı ABD olduğu kabul edildiğinde JSON dosyasından, çok kiracılı Dizin Oluşturucu yürütme ortamı için IP adreslerinin listesi aşağıda listelenmiştir.

    ```json
        {
          "name": "AzureCognitiveSearch.WestCentralUS",
          "id": "AzureCognitiveSearch.WestCentralUS",
          "properties": {
            "changeNumber": 1,
            "region": "westcentralus",
            "platform": "Azure",
            "systemService": "AzureCognitiveSearch",
            "addressPrefixes": [
              "52.150.139.0/26",
              "52.253.133.74/32"
            ]
          }
        }
    ```

1. Azure portal koşullu erişim sayfasına dönün, sol taraftaki menüden **adlandırılmış konumlar** ' ı seçin ve **+ IP aralıkları konumu**' nu seçin. Yeni adlandırılmış konumunuza bir ad verin ve en son iki adımda topladığınız arama hizmetiniz ve Dizin Oluşturucu yürütme ortamlarınız için IP aralıklarını ekleyin.
    * Yalnızca geçerli IP aralıklarını kabul ettiğinden, arama hizmeti IP adresiniz için IP adresinin sonuna "/32" eklemeniz gerekebilir.
    * Dizin Oluşturucu yürütme ortamı IP aralıkları için, yalnızca arama hizmetinizin bulunduğu bölgenin IP aralıklarını eklemeniz gerektiğini unutmayın.

1. Yeni adlandırılmış konumu ilkeden dışlayın. 
    1. Sol taraftaki menüden **ilkeler** ' i seçin. 
    1. Dizin oluşturucuyu engelleyen ilkeyi seçin.
    1. **Koşulları** seçin.
    1. **Konum** seçin.
    1. **Dışla** ' yı seçin ve ardından yeni adlandırılmış konumu ekleyin.
    1. Değişiklikleri **kaydedin** .

1. İlkenin yeni ilke kurallarını güncelleştirmesi ve zorlaması için birkaç dakika bekleyin.

1. Dizin oluşturucuyu yeniden oluşturma girişimi
    1. Oluşturduğunuz veri kaynağı nesnesi için bir güncelleştirme isteği gönderin.
    1. Dizin Oluşturucu oluşturma isteğini yeniden gönderin. Oturum açmak için yeni kodu kullanın, sonra başarılı oturum açma işleminden sonra başka bir Dizin Oluşturucu oluşturma isteği gönderin.

## <a name="document-processing-errors"></a>Belge işleme hataları

### <a name="unprocessable-or-unsupported-documents"></a>Proceslabilen veya desteklenmeyen belgeler

Blob Dizin Oluşturucu, [hangi belge biçimlerinin açıkça desteklendiğini belgeler.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) Bazen bir BLOB depolama kapsayıcısı desteklenmeyen belgeler içeriyor. Diğer zamanlarda sorunlu belgeler olabilir. [Yapılandırma seçeneklerini değiştirerek](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)bu belgelerde Dizin oluşturucuyu durdurmayı önleyebilirsiniz:

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Belge içeriği eksik

Blob Indexer [bir kapsayıcıdaki bloblardan metin bulur ve ayıklar](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Metin ayıklama ile ilgili bazı sorunlar şunlardır:

* Belge yalnızca taranmış görüntüleri içerir. Taranmış görüntüler (JPGs) gibi metin olmayan içeriğe sahip PDF blob 'ları, standart bir blob dizin oluşturma işlem hattında sonuç üretmez. Metin öğeleriyle görüntü içeriğiniz varsa, bilişsel [arama](cognitive-search-concept-image-scenarios.md) 'yı kullanarak metni bulabilir ve ayıklayabilir.
* Blob Indexer yalnızca dizin meta verileri için yapılandırılmış. İçeriği ayıklamak için, blob dizin oluşturucunun [hem içeriği hem de meta verileri ayıklamak](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)üzere yapılandırılması gerekir:

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Dizin hataları

### <a name="missing-documents"></a>Eksik belgeler

Dizin oluşturucular bir [veri kaynağından](/rest/api/searchservice/create-data-source)belge bulur. Bazen veri kaynağından dizinlenen bir belge, dizinde eksik gibi görünüyor. Bu hataların birçok yaygın nedeni olabilir:

* Belge Dizinlenmemiş. Başarılı bir Dizin Oluşturucu çalıştırması için portalı denetleyin.
* [Değişiklik izleme](/rest/api/searchservice/create-data-source#data-change-detection-policies) değerini denetleyin. Yüksek filigran değeri gelecekteki bir zamana ayarlandıysa, bu değerden küçük bir tarihi olan tüm belgeler Dizin Oluşturucu tarafından atlanır. Dizin oluşturucunun değişiklik izleme durumunu, [Dizin Oluşturucu durumundaki](/rest/api/searchservice/get-indexer-status#indexer-execution-result)' ınitialtrackingstate ' ve ' finaltrackingstate ' alanlarını kullanarak anlayabilirsiniz.
* Dizin Oluşturucu çalıştırıldıktan sonra belge güncelleştirildi. Dizinleyicinin bir [zamanlamaya](/rest/api/searchservice/create-indexer#indexer-schedule)göre olması, sonunda yeniden çalıştırılır ve belgeyi seçer.
* Veri kaynağında belirtilen [sorgu](/rest/api/searchservice/create-data-source) , belgeyi dışlar. Dizin oluşturucular, veri kaynağının parçası olmayan belgeleri dizinlemez.
* [Alan eşlemeleri](/rest/api/searchservice/create-indexer#fieldmappings) veya [AI zenginleştirme](./cognitive-search-concept-intro.md) belgeyi değiştirdi ve beklediğinizden farklı görünüyor.
* Belgenizi bulmak için [arama BELGESI API](/rest/api/searchservice/lookup-document) 'sini kullanın.