---
title: Azure Web uygulaması güvenlik duvarı (WAF) için bot korumasını yapılandırma
description: Azure Application Gateway 'de Web uygulaması güvenlik duvarı (WAF) için bot korumasını yapılandırma hakkında bilgi edinin.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516870"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Azure Application Gateway Web uygulaması güvenlik duvarı için bot korumasını yapılandırma (Önizleme)

Bu makalede, Azure portal kullanarak Application Gateway için Azure Web uygulaması güvenlik duvarı 'nda (WAF) bir bot koruma kuralının nasıl yapılandırılacağı gösterilmektedir. 

WAF 'niz için yönetilen bir bot koruma kuralı kümesini, bilinen kötü amaçlı IP adreslerinden gelen istekleri engellemek veya günlüğe kaydetmek için etkinleştirebilirsiniz. IP adresleri Microsoft Threat Intelligence akışından kaynaklıdır. Microsoft tehdit bilgileri, Azure Güvenlik Merkezi dahil olmak üzere çeşitli hizmetlerin kullandığı Intelligent Security Graph tarafından desteklenir.

> [!NOTE]
> Bot koruma kuralı kümesi şu anda genel önizleme aşamasındadır ve bir önizleme hizmet düzeyi sözleşmesi ile sunulmaktadır. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Ayrıntılar için [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ' na bakın.

## <a name="prerequisites"></a>Önkoşullar

 [Application Gateway Için Web uygulaması güvenlik duvarı Ilkeleri oluşturma](create-waf-policy-ag.md)bölümünde açıklanan yönergeleri izleyerek Application Gateway için temel bir WAF ilkesi oluşturun.

## <a name="enable-bot-protection-rule-set"></a>Bot koruma kuralı kümesini etkinleştir

1. Daha önce oluşturduğunuz **temel** ilke sayfasında, **Ayarlar**' ın altında **kurallar**' ı seçin.  

2. Ayrıntılar sayfasında, **kuralları yönet** bölümünde, açılan menüden, bot koruma kuralının onay kutusunu seçin ve ardından **Kaydet**' i seçin.

> [!div class="mx-imgBorder"]
> ![Bot koruması](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Sonraki adımlar

Özel kurallar hakkında daha fazla bilgi için bkz. [Azure Application Gateway Web uygulaması güvenlik duvarı v2 Için özel kurallar](custom-waf-rules-overview.md).