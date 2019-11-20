---
title: Azure ön kapısına sahip Web uygulaması güvenlik duvarı için bot korumasını yapılandırma (Önizleme)
description: Web uygulaması güvenlik duvarını (WAF) öğrenin.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 9a8901e3763b10b7ee00c10f4eec08a8325d7dec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512476"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Web uygulaması güvenlik duvarı için bot korumasını yapılandırma (Önizleme)
Bu makalede, Azure CLı, Azure PowerShell veya Azure Resource Manager şablonu kullanarak ön kapıya yönelik Azure Web uygulaması güvenlik duvarı 'nda (WAF) bot koruma kuralını yapılandırma işlemi gösterilmektedir.

WAF 'nizin bilinen kötü amaçlı IP adreslerinden gelen isteklere özel eylemler kazanması için yönetilen bir bot koruma kuralı kümesi etkinleştirilebilir. IP adresleri Microsoft Threat Intelligence akışından kaynaklıdır. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) , Microsoft Threat Intelligence 'ı güçlendirir ve Azure Güvenlik Merkezi dahil birden çok hizmet tarafından kullanılır.

> [!IMPORTANT]
> Bot koruma kuralı kümesi şu anda genel önizleme aşamasındadır ve bir önizleme hizmet düzeyi sözleşmesi ile sunulmaktadır. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.  Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

[Azure Portal kullanarak Azure ön kapısına yönelik BIR WAF Ilkesi oluşturma](waf-front-door-create-portal.md)bölümünde açıklanan yönergeleri Izleyerek ön kapıya yönelik temel bir WAF ilkesi oluşturun.

## <a name="enable-bot-protection-rule-set"></a>Bot koruma kuralı kümesini etkinleştir

1. Önceki bölümde oluşturduğunuz temel ilke sayfasında, **Ayarlar**' ın altında **kurallar**' a tıklayın.
2. Ayrıntılar sayfasında, **Kuralları Yönet** bölümünün altında, aşağı açılan menüden **botprotection-Preview-0,1**kuralının önündeki onay kutusunu seçin ve ardından yukarıya **Kaydet** ' i seçin.
    
   ![Bot koruma kuralı](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Sonraki adımlar

- [WAF 'yi nasıl izleyeceğinizi](waf-front-door-monitor.md)öğrenin.
