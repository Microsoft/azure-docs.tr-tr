---
title: Azure Data Factory doğrulama etkinliği
description: Doğrulama etkinliği, eklenen veri kümesini kullanıcının belirttiği ölçütlere göre doğrulayıp işlem hattının yürütülmesine devam etmez.
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
ms.openlocfilehash: f63c78c59d7d6be3c66ea0785389eff73e3bff60
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678360"
---
# <a name="validation-activity-in-azure-data-factory"></a>Azure Data Factory doğrulama etkinliği
İşlem hattının yalnızca yürütmeye devam etmesini sağlamak için bir işlem hattında bir doğrulama kullanabilirsiniz, bu işlem, eklenen veri kümesi başvurusunun var olduğunu doğrulandıktan sonra, belirtilen ölçütlere uyduğunda veya zaman aşımına ulaşılmıştır.


## <a name="syntax"></a>Sözdizimi

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
ad | ' Doğrulama ' etkinliğinin adı | Dize | Evet |
type | **Doğrulama**olarak ayarlanmalıdır. | Dize | Evet |
Veri kümesi | Etkinlik, bu veri kümesi başvurusunun var olduğunu ve belirtilen ölçütlere uyup uymadığını ya da zaman aşımına ulaşılmasına kadar yürütmeyi engeller. Belirtilen veri kümesi "MinimumSize" veya "ChildItems" özelliğini desteklemelidir. | Veri kümesi başvurusu | Evet |
timeout | Çalıştırılacak etkinliğinin zaman aşımını belirtir. Değer belirtilmemişse, varsayılan değer 7 gündür ("7.00:00:00"). Biçim d. hh: mm: ss şeklindedir | Dize | Hayır |
kullanılmadığında | Doğrulama denemeleri arasındaki saniye cinsinden gecikme. Değer belirtilmemişse, varsayılan değer 10 saniyedir. | Tamsayı | Hayır |
childItems | Klasörde alt öğeler olup olmadığını denetler. -True olarak ayarlanabilir: klasörün var olduğunu ve öğe olduğunu doğrulayın. Klasörde en az bir öğe mevcut olana kadar veya zaman aşımı değerine ulaşılana kadar engeller.-false: klasörün var olduğunu ve boş olduğunu doğrulayın. Klasör boş olana veya zaman aşımı değerine ulaşılana kadar engeller. Hiçbir değer belirtilmemişse, etkinlik, klasör mevcut olana kadar veya zaman aşımına ulaşılıncaya kadar engeller. | Boole | Hayır |
minimumSize | Bir dosyanın bayt cinsinden en küçük boyutu. Değer belirtilmemişse, varsayılan değer 0 bayttır | Tamsayı | Hayır |


## <a name="next-steps"></a>Sonraki adımlar
Data Factory tarafından desteklenen diğer denetim akışı etkinliklerini görün:

- [If Koşulu Etkinliği](control-flow-if-condition-activity.md)
- [İşlem Hattı Yürütme Etkinliği](control-flow-execute-pipeline-activity.md)
- [Her etkinlik için](control-flow-for-each-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
- [Web etkinliği](control-flow-web-activity.md)
- [Bitiş Etkinliği](control-flow-until-activity.md)
