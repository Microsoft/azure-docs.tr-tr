---
title: Eşleme Hizmeti iş ortağı kılavuzu
titleSuffix: Azure
description: Eşleme Hizmeti iş ortağı kılavuzu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 292c850f5352c80112e3f0bcc5c868f8128819be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592331"
---
# <a name="peering-service-partner-walkthrough"></a>Eşleme Hizmeti iş ortağı kılavuzu

Bu bölümde, eşleme hizmeti için doğrudan eşlemeyi etkinleştirmek üzere sağlayıcının izlenmesi gereken adımlar açıklanmaktadır.

## <a name="create-direct-peering-connection-for-peering-service"></a>Eşleme hizmeti için doğrudan eşleme bağlantısı oluşturma
Hizmet sağlayıcıları, eşleme hizmetini destekleyen yeni bir doğrudan eşleme oluşturarak coğrafi erişimini genişletebilir. Bunu gerçekleştirmek için
1. Henüz yoksa bir eşleme hizmeti ortağı olun
1. [Portalı kullanarak doğrudan eşleme oluşturmak veya değiştirmek](howto-direct-portal.md)için yönergeleri izleyin. Yüksek kullanılabilirlik gereksinimini karşıladığından emin olun.
1. Ardından, [portalı kullanarak doğrudan eşleme üzerinde eşleme hizmetini etkinleştirmek](howto-peering-service-portal.md)için adımları izleyin.

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Eşleme hizmeti için eski doğrudan eşleme bağlantısı kullan
Eşleme hizmetini desteklemek için kullanmak istediğiniz eski bir doğrudan eşlemeye sahipseniz,
1. Henüz yoksa bir eşleme hizmeti ortağı olun.
1. [Portalı kullanarak eski bir doğrudan eşlemeyi Azure kaynağına dönüştürmek](howto-legacy-direct-portal.md)için yönergeleri izleyin. Gerekirse, yüksek kullanılabilirlik gereksinimini karşılamak için ek devreler sıralayın.
1. Ardından, [portalı kullanarak doğrudan eşleme üzerinde eşleme hizmetini etkinleştirmek](howto-peering-service-portal.md)için adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Eşleme ilkesi](https://peering.azurewebsites.net/peering)hakkında bilgi edinin.
* Microsoft ile doğrudan eşleme ayarlama adımları hakkında bilgi edinmek için [doğrudan eşleme](walkthrough-direct-all.md)Kılavuzu ' nu izleyin.
* Exchange eşlemesini Microsoft ile ayarlamaya yönelik adımlar hakkında bilgi edinmek için [Exchange eşleme](walkthrough-exchange-all.md)Kılavuzu ' nu izleyin.