---
title: Azure Data Factory Web kancası etkinliği
description: Web kancası etkinliği, eklenen veri kümesini kullanıcının belirttiği ölçütlere göre doğrulayıp işlem hattının yürütülmesine devam etmez.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: b2f7c35e6ddb3c6ed0a3032d7ea6d4c53043c17b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122899"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory Web kancası etkinliği
Özel kodunuzla işlem hattı yürütülmesini denetlemek için bir Web kancası etkinliği kullanabilirsiniz. Müşteriler, Web kancası etkinliğini kullanarak bir uç nokta çağırabilir ve geri çağırma URL 'SI geçirebilir. İşlem hattı çalıştırması, bir sonraki etkinliğe devam etmeden önce geri aramanın çağrılmasını bekler.

## <a name="syntax"></a>Sözdizimi

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Tür özellikleri



Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
ad | Web kancası etkinliğinin adı | Dize | Yes |
type | **Web kancası**olarak ayarlanmalıdır. | Dize | Yes |
method | Hedef uç nokta için REST API yöntemi. | dizisinde. Desteklenen türler: ' POST ' | Yes |
url | Hedef uç nokta ve yol | Dize (veya dize resultType 'ı olan ifade). | Yes |
bilgisinde | İsteğe gönderilen üst bilgiler. Örneğin, bir istek için dili ve türü ayarlamak için: "üstbilgiler": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"}. | Dize (veya dize resultType 'ı olan ifade) | Evet, Content-Type üst bilgisi gereklidir. "üstbilgiler": {"Content-Type": "Application/JSON"} |
body | Uç noktaya gönderilen yükü temsil eder. | Geçerli JSON (veya JSON resultType 'ı olan ifade). İstek [yükü şeması](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) 'nda istek yükü şeması bölümüne bakın. | Yes |
kimlik doğrulaması | Uç noktayı çağırmak için kullanılan kimlik doğrulama yöntemi. Desteklenen türler "temel" veya "ClientCertificate." Daha fazla bilgi için bkz. [kimlik doğrulama](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) bölümü. Kimlik doğrulaması gerekmiyorsa, bu özelliği dışlayın. | Dize (veya dize resultType 'ı olan ifade) | Hayır |
timeout | Etkinliğin &#39;callbackurı&#39; 'nin çağrılması için bekleyeceği süre. Etkinliğin ' callBackUri ' 'nin çağrılması ne kadar bekleyecektir. Varsayılan değer 10 dakikadır ("00:10:00"). Biçim zaman aralığı, d. hh: mm: ss | Dize | Hayır |
Geri aramada durum bildir | Kullanıcının etkinliği başarısız olarak işaretleyecek Web kancası etkinliğinin başarısız durumunu rapor etmesine izin verir | Boole | Hayır |

## <a name="additional-notes"></a>Ek notlar

Azure Data Factory, gövdedeki "callBackUri" adlı ek bir özelliği URL uç noktasına geçirecek ve bu URI 'nin zaman aşımı değeri belirtilenden önce çağrılmasını beklecektir. URI çağrılamaz, etkinlik ' Timeerme ' durumuyla başarısız olur.

Özel uç noktaya yapılan çağrı başarısız olursa Web kancası etkinliğinin kendisi başarısız olur. Herhangi bir hata iletisi geri çağırma gövdesine eklenebilir ve sonraki bir etkinlikte kullanılabilir.

Geri çağırma URI 'sine geri geçirilen gövde geçerli bir JSON olmalıdır. Content-Type üst bilgisini `application/json`olarak ayarlamanız gerekir.

"Geri arama sırasında durumu raporla" seçeneğini kullandığınızda, geri çağırma yaparken aşağıdaki kod parçacığını gövdeye eklemeniz gerekir:

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>Sonraki adımlar

Data Factory tarafından desteklenen diğer denetim akışı etkinliklerini görün:

- [If Koşulu Etkinliği](control-flow-if-condition-activity.md)
- [İşlem Hattı Yürütme Etkinliği](control-flow-execute-pipeline-activity.md)
- [Her etkinlik için](control-flow-for-each-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
- [Web etkinliği](control-flow-web-activity.md)
- [Bitiş Etkinliği](control-flow-until-activity.md)
