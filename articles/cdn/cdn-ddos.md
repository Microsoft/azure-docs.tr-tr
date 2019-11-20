---
title: Azure CDN DDoS koruma özellikleri | Microsoft Docs
description: Microsoft Azure CDN'den DDoS koruması için ek ücret ödemeden temel korunur
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: magattus
ms.openlocfilehash: 9cd688de861015cc12d1f98ed71e5376e5f574db
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593880"
---
# <a name="azure-cdn-ddos-protection"></a>Azure CDN DDoS koruması

Content delivery network, tasarımı gereği DDoS koruması sağlar. Hayır, aşağıda belirtildiği gibi Azure CDN volumetric saldırılarına karşı koruma sağlar genel kapasiteye ek olarak, ek DDoS koruması sahip ek bir maliyet.

## <a name="azure-cdn-from-microsoft"></a>Microsoft Azure CDN

Microsoft Azure CDN tarafından korunan [temel Azure DDoS](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview). Microsoft Platformu Azure CDN'den halinde varsayılan ve hiçbir ek ücret ödemeden tümleşiktir. Azure CDN kapasiteden Microsoft'un küresel çapta dağıtılan ağ ve tam ölçekli her zaman açık trafik izleme ve gerçek zamanlı azaltma aracılığıyla ortak ağ katmanı saldırılarına karşı koruma sağlar. Temel bir DDoS koruma de en sık kullanılan karşı katman 7 DNS sorgu sel ve Katman 3 sık gerçekleşen felaketlerden koruyor ve bu hedef CDN uç noktası 4 volumetric saldırıları. Bu hizmet ayrıca koruma Microsoft'un Kurumsal ve tüketici Hizmetleri büyük ölçekli saldırılardan kendini kanıtlamış bir parça kayıtta sahiptir.

## <a name="azure-cdn-from-verizon"></a>Verizon'dan Azure CDN

Verizon'dan Azure CDN Verzion'ın tescilli DDoS riskini azaltma platformu tarafından korunur. Bunu içine verizon'dan Azure CDN varsayılan ve hiçbir ek ücret ödemeden tümleştirilmiştir. En yaygın karşı temel koruma, katman 7 DNS sorgu sel ve Katman 3 ve 4 volumetric saldırıları, hedef CDN uç sık gerçekleşmesini sağlar.

## <a name="azure-cdn-from-akamai"></a>Akamai'den Azure CDN

Akamai'den Azure CDN, Akamai'nın tescilli DDoS riskini azaltma platformu tarafından korunur. Bunu içine akamai'den Azure CDN varsayılan ve hiçbir ek ücret ödemeden tümleştirilmiştir. En yaygın karşı temel koruma, katman 7 DNS sorgu sel ve Katman 3 ve 4 volumetric saldırıları, hedef CDN uç sık gerçekleşmesini sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinin [Azure DDoS](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview). 
