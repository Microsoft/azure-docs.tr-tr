---
title: Azure IoT Edge modül önkoşulları | Azure Marketi
description: IoT Edge modülünü yayımlama önkoşulları.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 610f9bc8d87ac75e2868e247fe1332574480b498
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813870"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge modülü yayımlama önkoşulları

Bu makalede bir IoT Edge modülü teklifini yayımlama önkoşulları açıklanmaktadır.  Daha önce yapmadıysanız, [IoT Edge modülleri Yayımlama Kılavuzu](../..//iot-edge-module.md)' nu gözden geçirin.


## <a name="publishing-prerequisites"></a>Ön koşullar yayımlanıyor

Azure Marketi 'Nde bir IoT Edge modülünü yayımlamak için aşağıdaki önkoşulları karşılamanız gerekir:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)erişim. Daha fazla bilgi için bkz. [Azure Marketi ve AppSource Yayımlama Kılavuzu](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- [Azure Marketi koşullarına](https://azure.microsoft.com/support/legal/marketplace-terms/) yönelik anlaşma
- IoT Edge modülü teknik varlığınızı bir Azure Container Registry barındırın.  Daha fazla bilgi için bkz. [IoT Edge modülü teknik varlığınızı hazırlama](./cpp-create-technical-assets.md)
- IoT Edge modülünüzün meta verilerini kullanıma hazırlayın. Örneğin, aşağıdaki varlıkları hazırlayın:
    - Bir başlık
    - Bir açıklama (HTML biçiminde)
    - Logo resmi (PNG biçimi ve 40x40px, 90x90px, 115x115px, 255x115px dahil)
    - Kullanım Koşulları ve Gizlilik ilkesi
    - Şunu içeren bir varsayılan modül yapılandırması: rotalar, ikizi istenen özellikler, createOptions ve ortam değişkenleri.
    - Modül belgeleri
    - Destek kişileri


## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge modülü teknik varlığınızı hazırladıktan](./cpp-create-technical-assets.md)sonra, [IoT Edge modül teklifinizi oluşturmaya](./cpp-create-offer.md)hazır olursunuz. 
