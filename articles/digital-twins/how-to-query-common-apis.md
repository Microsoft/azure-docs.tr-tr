---
title: Ortak sorgu desenleri-Azure dijital TWINS | Microsoft Docs
description: Azure Digital TWINS yönetim API 'Leri için birkaç ortak API sorgulama deseni öğrenin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/27/2019
ms.openlocfilehash: c821524b344c8b741047ba415127cffe59a74ca2
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005907"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Ortak görevler için Azure dijital TWINS API 'Lerini sorgulama

Bu makalede, Azure dijital TWINS örneğiniz için genel senaryoları yürütmeniz için sorgu desenleri gösterilmektedir. Bu, dijital TWINS örneğinizin zaten çalıştığını varsayar. Postman gibi herhangi bir REST istemcisini kullanabilirsiniz. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Boşluklar ve türler için sorgular

Bu bölümde, sağlanan boşluklarınız hakkında daha fazla bilgi almak için örnek sorgular gösterilmektedir. Kimlik doğrulamalarının, örnek sorgularla birlikte HTTP isteklerini ALMASıNı sağlayın ve yer tutucuları kurulumdaki değerlerle değiştirin. 

- Kök düğümleri olan boşlukları al.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Ada göre bir boşluk alın ve cihazları, algılayıcıları, hesaplanan değerleri ve algılayıcı değerlerini ekleyin. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Üst öğe belirtilen alan KIMLIĞI olan ve [belirtilen alana göre](how-to-navigate-apis.md#api-navigation)iki ila beş seviyedeki, bir boşluk ve kendi cihaz/algılayıcı bilgilerini alın. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Verilen KIMLIĞE sahip alanı alın ve hesaplanan ve algılayıcı değerlerini dahil edin.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Belirli bir alan için özellik anahtarlarını alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- *Areaınsqmeters* adlı Özellik anahtarı ile boşluk alın ve değeri 30 ' dur. Ayrıca dize işlemleri yapabilirsiniz, örneğin, `name = X contains Y`Özellik anahtarını içeren boşluklar alabilirsiniz.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Ad *sıcaklığını* ve ilişkili bağımlılıkları ve ontoloon 'un tüm adlarını alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Roller ve rol atamaları için sorgular

Bu bölümde roller ve atamaları hakkında daha fazla bilgi almak için bazı sorgular gösterilmektedir. 

- Azure dijital TWINS tarafından desteklenen tüm rolleri alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Dijital TWINS örneğiniz içindeki tüm rol atamalarını alın. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Belirli bir yoldaki rol atamalarını alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Cihazlar için sorgular

Bu bölümde, cihazlarınızla ilgili belirli bilgileri almak için yönetim API 'Lerini nasıl kullanabileceğinizi gösteren bazı örnekler gösterilmektedir. Tüm API çağrılarının kimliği doğrulanmış HTTP istekleri olması gerekir.

- Tüm cihazları al.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Tüm cihaz durumlarını bul.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Belirli bir cihazı alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Kök alana bağlı tüm cihazları alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- 2 ile 4 arasındaki düzeyler 'e eklenmiş tüm cihazları alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Belirli bir alan KIMLIĞINE doğrudan bağlı tüm cihazları alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Belirli bir alana ve alt öğelere bağlı tüm cihazları alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Bir alanın alt öğesine ekli tüm cihazları al, bu alan hariç.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Bir alanın doğrudan alt öğelerine bağlı tüm cihazları alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Bir alanın üst öğelerinden birine bağlı tüm cihazları alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Düzeyi 5 ' ten küçük veya buna eşit olan bir alanın alt öğesine bağlı tüm cihazları alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- KIMLIĞI *YOUR_SPACE_ID*olan alanla aynı düzeyde olan alanlara eklenmiş tüm cihazları alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Cihazınız için IoT Hub cihaz bağlantı dizesini alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Verilen algılayıcı dahil olmak üzere, belirtilen donanım KIMLIĞINE sahip cihazı alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Bu örnekte *hareket* ve *sıcaklık*gibi belirli veri türleri için algılayıcılar alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Eşleştiriciler ve Kullanıcı tanımlı işlevler için sorgular 

- Tüm sağlanan eşleştiriciler ve kimliklerini alın.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Alanlarla ilişkili boşluklar ve Kullanıcı tanımlı işlev dahil olmak üzere belirli bir eşleştirici hakkındaki ayrıntıları alın.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Bir algılayıcı için bir eşleştirici değerlendirin ve hata ayıklama amacıyla günlüğe kaydetmeyi etkinleştirin. Bu HTTP GET iletisini döndürmek, eşleştirici ve sensörin veri türüne ait olup olmadığını söyler. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Kullanıcı tanımlı işlevlerin KIMLIĞINI alın. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Kullanıcı tanımlı belirli bir işlevin içeriğini al 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Kullanıcılar için sorgular

Bu bölümde, Azure dijital TWINS 'te kullanıcıları yönetmeye yönelik bazı örnek API sorguları gösterilmektedir. Yer tutucuları kurulumdaki değerlerle değiştirerek bir HTTP GET isteği yapın. 

- Tüm kullanıcıları al. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Belirli bir kullanıcıyı al.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Sonraki adımlar

Yönetim API 'niz ile kimlik doğrulaması yapmayı öğrenmek için, [API 'Lerle kimlik doğrulamasını](./security-authenticating-apis.md)okuyun.

API uç noktalarınız hakkında daha fazla bilgi edinmek için [dijital TWINS Swagger nasıl kullanacağınızı](./how-to-use-swagger.md)okuyun.
