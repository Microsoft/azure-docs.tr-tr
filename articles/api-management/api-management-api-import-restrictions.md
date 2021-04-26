---
title: API biçimleri için kısıtlamalar ve Ayrıntılar desteği
titleSuffix: Azure API Management
description: Azure API Management 'de açık API, WSDL ve WADL biçimleri üzerinde bilinen sorunların ve kısıtlamaların ayrıntıları.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 6a53cc2b2ec6d46b4bde54af58b4e5542ff6cf79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91932643"
---
# <a name="api-import-restrictions-and-known-issues"></a>API içeri aktarma kısıtlamaları ve bilinen sorunlar

## <a name="about-this-list"></a>Bu liste hakkında

Bir API 'yi içeri aktarırken, bazı kısıtlamalardan gelebilir veya içeri aktarma işlemini başarıyla gerçekleştirmeden önce düzeltilmesi gereken sorunları belirleyebilirsiniz. Bu makale, API 'nin içe aktarma biçimine göre düzenlenmiş bu sınırlamaları belgeler. Openapı dışa aktarmanın nasıl çalıştığını da açıklar.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Openapı/Swagger içeri aktarma sınırlamaları

Openapı belgenizi içeri aktarırken hata alıyorsanız, önceden doğruladığınızdan emin olun. Tasarımcı 'yı Azure portal (Design-Front End-Openapı belirtim Editor) veya <a href="https://editor.swagger.io">Swagger Düzenleyicisi</a>gibi bir üçüncü taraf aracıyla kullanabilirsiniz.

### <a name="general"></a><a name="open-api-general"> </a>Genel

-   Hem yol hem de sorgu üzerinde gerekli parametreler benzersiz adlara sahip olmalıdır. (Openapı 'de bir parametre adının yalnızca bir konum içinde benzersiz olması gerekir, örneğin yol, sorgu, üstbilgi. Ancak API Management ' de, işlemlerin hem yol hem de sorgu parametrelerine göre ayırt edilebilir (Openapı 'Yi desteklemez). Bu nedenle, tüm URL şablonu içinde parametre adlarının benzersiz olması gerekir.)
-   `\$ref` işaretçiler dış dosyalara başvuramaz.
-   `x-ms-paths` ve `x-servers` desteklenen tek uzantılardır.
-   Özel uzantılar İçeri aktarmada yok sayılır ve dışarı aktarma için kaydedilmez veya korunmaz.
-   `Recursion` -API Management özyinelemeli olarak tanımlanan tanımları desteklemez (örneğin, kendilerine başvuran şemalar).
-   Kaynak dosya URL 'SI (varsa) göreli sunucu URL 'Lerine uygulanır.
-   Güvenlik tanımları yok sayılır.
-   API işlemleri için satır içi şema tanımları desteklenmez. Şema tanımları API kapsamında tanımlanır ve API işlemleri isteği veya Yanıt kapsamları içinde başvurulabilir.
-   Tanımlı URL parametresinin URL şablonunun bir parçası olması gerekir.
-   `server` nesne, API işlem düzeyinde desteklenmiyor.
-   `Produces` bir API tarafından döndürülen MIME türlerini açıklayan anahtar sözcük desteklenmez. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>Openapı sürüm 2

-   Yalnızca JSON biçimi destekleniyor.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>Openapı sürüm 3

-   Eğer çok `servers` belirtilmişse, API Management Ilk https URL 'sini seçmeyi deneyecek. Herhangi bir HTTPs URL 'si yoksa, ilk HTTP URL 'si. HTTP URL 'Leri yoksa, sunucu URL 'SI boş olur.
-   `Examples` desteklenmez, ancak `example` .

## <a name="openapi-import-update-and-export-mechanisms"></a>Openapı içeri aktarma, güncelleştirme ve dışarı aktarma mekanizmaları

### <a name="general"></a><a name="open-import-export-general"> </a>Genel

-   API Management hizmetinden dışarıya aktarılmış API tanımları, birincil olarak API Management hizmetinde barındırılan API 'yi çağırması gereken API Management hizmeti dışındaki uygulamalara yöneliktir. Aktarılan API tanımlarının aynı veya farklı API Management hizmetine yeniden aktarılması amaçlanmamıştır. Farklı seri hizmet/envionments genelinde API 'leri yapılandırma yönetimi için lütfen git ile API Management hizmeti kullanma ile ilgili belgelere başvurun. 

### <a name="add-new-api-via-openapi-import"></a>Openapı içeri aktarma aracılığıyla yeni API ekleme

Openapı belgesinde bulunan her işlem için, Azure Kaynak adı ve görünen adı ile ve sırasıyla ayarlanan yeni bir işlem oluşturulur `operationId` `summary` . `operationId` değer aşağıda açıklanan kuralların ardından normalleştirilir. `summary` değer olduğu gibi içeri aktarılır ve uzunluğu 300 karakterle sınırlıdır.

`operationId`Belirtilmemişse (yani, yoksa `null` veya boş), Azure Kaynak adı değeri http yöntemi ve yol şablonu birleştirilerek oluşturulur. Örneğin, `get-foo` .

Belirtilmemişse (yani,, `summary` `null` veya boş), `display name` değer olarak ayarlanır `operationId` . `operationId`Belirtilmezse, görünen ad DEĞERI http yöntemi ve yol şablonu birleştirilerek oluşturulacaktır, örneğin, `Get - /foo` .

### <a name="update-an-existing-api-via-openapi-import"></a>Openapı içeri aktarma aracılığıyla var olan bir API 'YI güncelleştirme

İçeri aktarma sırasında mevcut API, Openapı belgesinde açıklanan API ile eşleşecek şekilde değiştirilir. Openapı belgesindeki her işlem, `operationId` değeri mevcut Işlemin Azure Kaynak adı ile karşılaştırılarak mevcut işlemle eşleştirilir.

Bir eşleşme bulunursa, mevcut işlemin özellikleri "yerinde" güncelleştirilir.

Bir eşleşme bulunamazsa, yukarıdaki bölümde açıklanan kurallar kullanılarak yeni bir işlem oluşturulur. Her yeni işlem için, içeri aktarma işlemi aynı HTTP yöntemi ve yol şablonuyla mevcut bir işlemden ilke kopyalamaya çalışır.

Tüm mevcut eşleşmeyen işlemler silinecektir.

İçeri aktarmayı daha öngörülebilir hale getirmek için lütfen şu yönergeleri izleyin:

- `operationId`Her işlem için özelliği belirttiğinizden emin olun.
- `operationId`İlk içeri aktarma işleminden sonra değiştirilminden kaçının.
- Aynı anda hiçbir zaman değiştirmeyin `operationId` ve http yöntemi veya yol şablonu.

### <a name="export-api-as-openapi"></a>API 'YI Openapı olarak dışarı aktar

Her işlem için, Azure Kaynak adı bir olarak verilecek `operationId` ve görünen ad bir olarak verilecek `summary` .
OperationId için normalleştirme kuralları

- Küçük harfe Dönüştür.
- Alfasayısal olmayan karakterlerin her dizisini tek bir çizgiyle değiştirin, örneğin, `GET-/foo/{bar}?buzz={quix}` içine dönüştürülür `get-foo-bar-buzz-quix-` .
- Örneğin, her iki tarafta da kesik çizgiler `get-foo-bar-buzz-quix-` kırpılacak `get-foo-bar-buzz-quix`
- Bir kaynak adı için en fazla dört karakter olan 76 karakter uzunluğunda olacak şekilde kısaltın.
- Gerekirse, yinelenenleri kaldırma soneki için kalan dört karakteri kullanın `-1, -2, ..., -999` .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL dosyaları SOAP geçişli ve SOAP--REST API 'Leri oluşturmak için kullanılır.

-   **SOAP bağlamaları** -yalnızca "Document" ve "literal" kodlamasının SOAP bağlamaları desteklenir. "RPC" stili veya SOAP kodlaması desteği yoktur.
-   **Wsdl: Import** -bu öznitelik desteklenmiyor. Müşteriler içeri aktarmaları tek bir belgede birleştirmelidir.
-   **Birden çok parçaya sahip iletiler** -bu tür iletiler desteklenmez.
-   **WCF WSHttpBinding** -Windows Communication Foundation ile oluşturulan soap hizmetleri, BasicHttpBinding-WSHttpBinding kullanmalıdır.
-   **MTOM** -MTOM kullanan hizmetler işe <em>başlayabilir</em> . Resmi destek şu anda sunulmamaktadır.
-   Yinelemeli olarak tanımlanan **özyineleme** türleri (örneğin, kendi dizisine başvuru) APIM tarafından desteklenmez.
-   **Birden çok ad** alanı-bir şemada birden fazla ad alanı kullanılabilir, ancak ileti parçalarını tanımlamak için yalnızca hedef ad alanı kullanılabilir. Diğer giriş veya çıkış öğelerini tanımlamak için kullanılan hedeften farklı ad alanları korunmaz. Bu tür bir WSDL belgesi içeri aktarılabilse de, tüm ileti bölümlerinin dışarı aktarılması için WSDL 'nin hedef ad alanı olacaktır.
-   **Diziler** -SOAP-Rest dönüşümü, yalnızca aşağıdaki örnekte gösterilen sarmalanmış dizileri destekler:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>Wadl

Şu anda bilinen bir WADL içeri aktarma sorunu yok.
