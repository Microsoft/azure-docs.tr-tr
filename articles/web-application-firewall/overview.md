---
title: Azure Web uygulaması güvenlik duvarı 'na giriş
description: Bu makalede, Azure Web uygulaması güvenlik duvarı 'na (WAF) genel bir bakış sunulmaktadır
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "79475796"
---
# <a name="what-is-azure-web-application-firewall"></a>Azure Web Uygulaması Güvenlik Duvarı nedir?

Web Uygulaması Güvenlik Duvarı (WAF) web uygulamalarınız için yaygın güvenlik açıklarına ve açıklardan yararlanma işlemlerine karşı merkezi bir koruma sağlar. Web uygulamaları, yaygın olarak bilinen güvenlik açıklarından yararlanan kötü amaçlı saldırılara karşı giderek daha da yöneliktir. SQL ekleme ve siteler arası komut dosyası oluşturma en yaygın saldırı arasındadır.

![WAF 'ye Genel Bakış](media/overview/wafoverview.png)

Uygulama kodundaki saldırıların önlenmesi zor olur. Uygulama topolojisinin birden çok katmanında kapsamlı bakım, düzeltme eki uygulama ve izleme gerektirebilir. Merkezi bir Web uygulaması güvenlik duvarı, güvenlik yönetiminin daha basit olmasına yardımcı olur. Ayrıca, bir WAF, uygulama yöneticilerine tehditler ve yetkisiz erişimlere karşı daha iyi koruma güvence altına almanızı sağlar.

Bir WAF çözümü, her bir Web uygulamasının güvenliğini sağlamak yerine, bilinen bir güvenlik açığına merkezi olarak yama yaparak bir güvenlik tehdidi üzerinde daha hızlı yanıt verebilir.

## <a name="supported-service"></a>Desteklenen hizmet

WAF, Microsoft 'un Azure Application Gateway, Azure ön kapısı ve Azure Content Delivery Network (CDN) hizmeti ile dağıtılabilir. Azure CDN WAF Şu anda genel önizleme aşamasındadır.  WAF, her belirli hizmet için özelleştirilmiş özelliklere sahiptir. Her hizmet için WAF özellikleri hakkında daha fazla bilgi için bkz. her hizmet için genel bakış.

## <a name="next-steps"></a>Sonraki adımlar

- Application Gateway Web uygulaması güvenlik duvarı hakkında daha fazla bilgi için bkz. [Azure Application Gateway Web uygulaması güvenlik duvarı](./ag/ag-overview.md).
- Azure ön kapısının Web uygulaması güvenlik duvarı hakkında daha fazla bilgi için bkz. [Azure ön kapısı hizmetinde Web uygulaması güvenlik duvarı](./afds/afds-overview.md).
- Azure CDN Service üzerinde Web uygulaması güvenlik duvarı hakkında daha fazla bilgi için bkz. [Azure CDN hizmetinde Web uygulaması güvenlik duvarı](./cdn/cdn-overview.md)
