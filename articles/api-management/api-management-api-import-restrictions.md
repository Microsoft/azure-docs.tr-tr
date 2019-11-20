---
title: Azure API Management API içeri aktarma 'da kısıtlamalar ve bilinen sorunlar | Microsoft Docs
description: Open API, WSDL veya WADL biçimlerini kullanarak Azure API Management içeri aktarma ile ilgili bilinen sorunların ve kısıtlamaların ayrıntıları.
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
ms.date: 11/06/2019
ms.author: apimpm
ms.openlocfilehash: 88ef235d47a548ce426eaa2e8a8a56fb9dcb01d2
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796028"
---
# <a name="api-import-restrictions-and-known-issues"></a>API içeri aktarma kısıtlamaları ve bilinen sorunlar

## <a name="about-this-list"></a>Bu liste hakkında

Bir API 'yi içeri aktarırken, bazı kısıtlamalardan gelebilir veya başarıyla içeri aktarabilmek için düzeltilmesi gereken sorunları belirleyebilirsiniz. Bu makale, API 'nin içe aktarma biçimine göre düzenlenmiş şekilde bunları belgeler.

## <a name="open-api"> </a>Openapı/Swagger

Openapı belgenizi içeri aktarırken hata alıyorsanız, önceden doğruladığınızdan emin olun. Tasarımcı 'yı Azure portal (Design-Front End-Openapı belirtim Editor) veya <a href="https://editor.swagger.io">Swagger Düzenleyicisi</a>gibi bir üçüncü taraf aracıyla kullanabilirsiniz.

### <a name="open-api-general"> </a>Genel

-   Hem yol hem de sorgu üzerinde gerekli parametreler benzersiz adlara sahip olmalıdır. (Openapı 'de bir parametre adının yalnızca bir konum içinde benzersiz olması gerekir, örneğin yol, sorgu, üstbilgi. Ancak API Management ' de, işlemlerin hem yol hem de sorgu parametrelerine göre ayırt edilebilir (Openapı 'Yi desteklemez). Bu nedenle, tüm URL şablonu içinde parametre adlarının benzersiz olması gerekir.)
-   **\$başvuru** işaretçileri dış dosyalara başvuramaz.
-   **x-MS-Paths** ve **x-Servers** desteklenen tek uzantılardır.
-   Özel uzantılar İçeri aktarmada yok sayılır ve dışarı aktarma için kaydedilmez veya korunmaz.
-   **Özyineleme** -API Management özyinelemeli olarak tanımlanan tanımları desteklemez (örneğin, kendilerine başvuran şemalar).
-   Kaynak dosya URL 'SI (varsa) göreli sunucu URL 'Lerine uygulanır.
-   Güvenlik tanımları yok sayılır.
-   API işlemleri için satır içi şema tanımları desteklenmez. Şema tanımları API kapsamında tanımlanır ve API işlemleri isteği veya Yanıt kapsamları içinde başvurulabilir.
-   Tanımlı URL parametresinin URL şablonunun bir parçası olması gerekir.
-   Bir API tarafından döndürülen MIME türlerini **açıklayan anahtar sözcük** , desteklenmez. 

### <a name="open-api-v2"> </a>Openapı sürüm 2

-   Yalnızca JSON biçimi destekleniyor.

### <a name="open-api-v3"> </a>Openapı sürüm 3

-   Çok sayıda **sunucu** belirtilmişse, API Management ilk https URL 'sini seçmeyi deneyecek. Herhangi bir HTTPs URL 'si yoksa, ilk HTTP URL 'si. HTTP URL 'Leri yoksa, sunucu URL 'SI boş olur.
-   **Örnekler** desteklenmez, ancak **örnek** .
-   **Multipart/form verileri** desteklenmez.

## <a name="wsdl"> </a>WSDL

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

## <a name="wadl"> </a>Wadl

Şu anda bilinen bir WADL içeri aktarma sorunu yok.
