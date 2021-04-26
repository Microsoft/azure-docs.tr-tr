---
title: include dosyası
description: include dosyası
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009694"
---
> [!NOTE]
> Bir Web uygulaması 20 dakikadan sonra zaman aşımına uğrar. yalnızca gerçek Web uygulamasına yönelik istekler zamanlayıcıyı sıfırlayabilir. Uygulamanın yapılandırmasını Azure portal veya gelişmiş araçlar sitesine () yönelik isteklerin yapılması `https://<app_name>.scm.azurewebsites.net` süreölçeri sıfırlayamaz. Web uygulamanızı sürekli veya zamanlanmış (Zamanlayıcı-tetikleyici) WebJobs çalıştıracak şekilde ayarlarsanız, Web Işlerinin güvenilir bir şekilde çalışmasını sağlamak için Web uygulamanızın Azure **yapılandırma** sayfasında **her zaman açık** ayarını etkinleştirin. Bu özellik yalnızca temel, standart ve Premium [fiyatlandırma katmanlarında](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)kullanılabilir.
