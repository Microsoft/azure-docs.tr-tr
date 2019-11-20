---
title: include dosyası
description: include dosyası
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836792"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Dağıtım sırasında uygulamama ne?

Resmi olarak desteklenen dağıtım yöntemlerini tüm dosyalarda değişiklik `/home/site/wwwroot` uygulamanızın klasör. Bu dosyalar, üretimde çalışan aynıdır. Bu nedenle, dağıtımı kilitli dosyalar nedeniyle başarısız olabilir. Tüm dosyalar aynı anda güncelleştirilmediğinden üretim uygulamasında da dağıtım sırasında davranmasına neden olabilecek. Bu sorunlarla karşılaşmamak için birkaç farklı yolu vardır:

- Uygulamanızı durdurma veya dağıtım sırasında uygulamanız için çevrimdışı modunu etkinleştirin. Daha fazla bilgi için [dağıtım sırasında kilitli dosyalarla başa](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Dağıtım bir [hazırlama yuvasındaki](../articles/app-service/deploy-staging-slots.md) ile [otomatik takas](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) etkin. 
- Kullanım [paketi çalıştırmak](https://github.com/Azure/app-service-announcements/issues/84) sürekli dağıtım yerine.
