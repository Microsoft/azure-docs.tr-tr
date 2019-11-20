---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70737500"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Tarayıcıdan Azure dosyaları kullanan bir Web uygulamasından hata ConditionHeadersNotSupported

Azure dosyalarında barındırılan içeriğe, Web tarayıcısı gibi koşullu üstbilgiler kullanan bir uygulama aracılığıyla erişim başarısız olduğunda ConditionHeadersNotSupported hatası oluşur. Hata, koşul başlıklarının desteklenmediğini belirtir.

![Azure dosyaları koşullu üstbilgileri hatası](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Nedeni

Koşullu üstbilgiler henüz desteklenmiyor. Dosyaya her erişildiğinde uygulamanın tam dosyayı istemesi gerekecektir.

### <a name="workaround"></a>Geçici Çözüm

Yeni bir dosya yüklendiğinde, Cache-Control özelliği varsayılan olarak "No-Cache" olur. Uygulamayı her seferinde dosyayı isteyecek şekilde zorlamak için, dosyanın Cache-Control özelliğinin "No-Cache" (No-Cache, No-Store,-yeniden Validate "olarak güncelleştirilmesi gerekir. Bu, [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)kullanılarak elde edilebilir.

![Azure dosyaları için Depolama Gezgini içerik önbelleği değişikliği koşullu üstbilgileri](media/storage-files-condition-headers/storage-explorer-cache.png)