---
title: Azure Web uygulaması güvenlik duvarı 'na giriş
description: Bu makalede, Azure Web uygulaması güvenlik duvarı 'na (WAF) genel bir bakış sunulmaktadır
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 7b43a6bdac254493da8693b55158e15746e76dc3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502047"
---
# <a name="what-is-azure-web-application-firewall"></a>Azure Web Uygulaması Güvenlik Duvarı nedir?

Web uygulaması güvenlik duvarı (WAF), Web uygulamalarınızın yaygın olarak karşılaşılan ve güvenlik açıklarından merkezi bir şekilde korunmasını sağlar. Web uygulamaları, yaygın olarak bilinen güvenlik açıklarından yararlanan kötü amaçlı saldırılara karşı giderek daha da yöneliktir. SQL ekleme ve siteler arası komut dosyası oluşturma en yaygın saldırı arasındadır.

![WAF 'ye Genel Bakış](media/overview/wafoverview.png)

Uygulama kodundaki saldırıların önlenmesi zor olur. Uygulama topolojisinin birden çok katmanında kapsamlı bakım, düzeltme eki uygulama ve izleme gerektirebilir. Merkezi bir Web uygulaması güvenlik duvarı, güvenlik yönetiminin daha basit olmasına yardımcı olur. Ayrıca, bir WAF, uygulama yöneticilerine tehditler ve yetkisiz erişimlere karşı daha iyi koruma güvence altına almanızı sağlar.

Bir WAF çözümü, her bir Web uygulamasının güvenliğini sağlamak yerine, bilinen bir güvenlik açığına merkezi olarak yama yaparak bir güvenlik tehdidi üzerinde daha hızlı yanıt verebilir.

WAF, Azure Application Gateway ve Azure ön kapılı hizmeti ile dağıtılabilir. Şu anda WAF, her bir hizmet için özelleştirilmiş özellikler içeriyor. Her hizmet için WAF özellikleri hakkında daha fazla bilgi için bkz. her hizmet için genel bakış.

## <a name="next-steps"></a>Sonraki adımlar

- Application Gateway Web uygulaması güvenlik duvarı hakkında daha fazla bilgi için bkz. [Azure Application Gateway Web uygulaması güvenlik duvarı](./ag/ag-overview.md).
- Azure ön kapısının Web uygulaması güvenlik duvarı hakkında daha fazla bilgi için bkz. [Azure ön kapısı hizmetinde Web uygulaması güvenlik duvarı](./afds/afds-overview.md).
