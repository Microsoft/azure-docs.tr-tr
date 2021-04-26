---
title: Azure CDN ile Çin içerik teslimi | Microsoft Docs
description: Çin kullanıcılarına içerik sunmak için Azure Content Delivery Network (CDN) kullanma hakkında bilgi edinin.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 599ec041837460c30b4655531b822eab5f0eafa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778923"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Azure CDN ile Çin içerik teslimi

Azure Content Delivery Network (CDN) Global, Çin 'deki mevcut nokta (POP) konumları olan Çin kullanıcılarına veya Çin 'den kaynaklanan isteklere en iyi performansı sağlayan herhangi bir POP 'a içerik sunabilir. Ancak Çin, müşterileriniz için önemli bir Pazar ise ve hızlı performansa ihtiyaç duyduklarında bunun yerine Azure CDN Çin kullanmayı göz önünde bulundurun.

Azure CDN Çin, bir dizi yerel sağlayıcı ile ortaklık yaparak Çin 'nin içindeki pop 'Ların içeriğini sundığından Azure CDN küresel ' ten farklıdır. Çince uyumluluk ve düzenleme nedeniyle, Azure CDN Çin kullanmak üzere ayrı bir abonelik kaydetmeniz gerekir ve Web sitelerinizin bir ıCP lisansına sahip olması gerekir. İçerik teslimini etkinleştirmek ve yönetmek için Portal ve API deneyimi Azure CDN Global ve Azure CDN Çin arasında aynıdır.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Azure CDN genel ve Azure CDN Çin karşılaştırması

Azure CDN Global ve Azure CDN Çin aşağıdaki özelliklere sahiptir:

- Azure CDN Genel:

     - Portal https://portal.azure.com  

     - Çin dışında içerik teslimi gerçekleştirir

     - Dört fiyatlandırma katmanı: Microsoft Standard, Verizon Standard, Verizon Premium ve Akamai standart

     - [Belgeler](./index.yml)

- Azure CDN Çin:

     - Portal https://portal.azure.cn

     - Çin içinde içerik teslimi gerçekleştirir

     - İki fiyatlandırma katmanı: Standart ve Premium

     - [Belgeler](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Sonraki adımlar

Azure CDN Çin hakkında daha fazla bilgi edinmek için bkz.:

- [Content Delivery Network özellikleri](https://www.azure.cn/en-us/home/features/cdn/)

- [Azure Content Delivery Network 'ye Genel Bakış](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Azure Content Delivery Network kullanma](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Çin 'de Azure hizmeti kullanılabilirliği](/azure/china/concepts-service-availability)