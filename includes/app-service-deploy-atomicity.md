---
title: include dosyası
description: include dosyası
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004371"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Dağıtım sırasında uygulamama ne olur?

Resmi olarak desteklenen tüm dağıtım yöntemleri, uygulamanızın klasöründeki dosyalarda değişiklikler yapar `/home/site/wwwroot` . Bu dosyalar, uygulamanızı çalıştırmak için kullanılır. Bu nedenle, dağıtım kilitli dosyalar nedeniyle başarısız olabilir. Aynı zamanda tüm dosyalar aynı anda güncelleştirilemediğinden, uygulama dağıtım sırasında tahmin edilemeyecek şekilde davranabilir. Bu, müşteriye yönelik bir uygulama için istenmeyen bir uygulamadır. Bu sorunlardan kaçınmak için birkaç farklı yol vardır:

- [Uygulamanızı paketten çıkmadan doğrudan ZIP paketinden çalıştırın](../articles/app-service/deploy-run-package.md) .
- Dağıtım sırasında uygulamanızı durdurun veya uygulamanız için çevrimdışı modu etkinleştirin. Daha fazla bilgi için bkz. [dağıtım sırasında kilitli dosyalarla uğraşın](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- [Otomatik takas](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) özellikli bir [hazırlama yuvasına](../articles/app-service/deploy-staging-slots.md) dağıtın. 
