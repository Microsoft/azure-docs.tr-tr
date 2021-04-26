---
title: Azure Güvenlik Duvarı tehdit zekası yapılandırması
description: Azure Güvenlik Duvarı ilkenize yönelik tehdit zekası tabanlı filtreleme işlemlerini, ve bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen trafiği uyaracak ve reddedecek şekilde nasıl yapılandıracağınızı öğrenin.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7ede1c917bb44dd31aa59855a0b7c83eb478700a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651735"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Azure Güvenlik Duvarı tehdit zekası yapılandırması

Tehdit zekası tabanlı filtreleme, Azure Güvenlik Duvarı ilkenizin ve bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen trafiği uyarmak ve reddetmek için yapılandırılabilir. IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) , Microsoft Threat Intelligence 'ı güçlendirir ve Azure Güvenlik Merkezi dahil birden çok hizmet tarafından kullanılır.<br>

Tehdit zekası tabanlı filtreleme 'yi yapılandırdıysanız, ilişkili kurallar NAT kurallarından, ağ kurallarından veya uygulama kurallarından önce işlenir.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Tehdit bilgileri ilkesi":::

## <a name="threat-intelligence-mode"></a>Tehdit bilgileri modu

Tehdit zekasını, aşağıdaki tabloda açıklanan üç moddan birinde yapılandırabilirsiniz. Varsayılan olarak, tehdit zekası tabanlı filtreleme, uyarı modunda etkindir.

|Mod |Description  |
|---------|---------|
|`Off`     | Tehdit bilgileri özelliği güvenlik duvarınız için etkinleştirilmemiş. |
|`Alert only`     | Güvenlik duvarınızdan veya bilinen kötü amaçlı IP adreslerinden ve etki alanlarına giden trafik için yüksek güvenilirlikli uyarılar alacaksınız. |
|`Alert and deny`     | Trafik engellenir ve trafik algılandığında, bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen güvenlik duvarınızdan veya bu bilgisayarlardan geçiş yapmaya çalışırken yüksek güvenilirlikli uyarılar alırsınız. |

> [!NOTE]
> Tehdit bilgileri modu üst ilkelerden alt ilkelere devralınır. Alt ilke, üst ilkeden aynı veya daha sıkı bir mod ile yapılandırılmalıdır.

## <a name="allowlist-addresses"></a>Allowlist adresleri

Tehdit bilgileri hatalı pozitif sonuçlar tetikleyebilir ve gerçekten geçerli olan trafiği engeller. Tehdit bilgileri 'nin belirttiğiniz adreslerin, aralıkların veya alt ağların hiçbirini filtrelemesine olanak sağlamak için izin verilen IP adreslerinin bir listesini yapılandırabilirsiniz.  

![Allowlist adresleri](media/threat-intelligence-settings/allow-list.png)

Bir CSV dosyasını karşıya yükleyerek izin öğesini aynı anda birden çok girdi ile güncelleştirebilirsiniz. CSV dosyası yalnızca IP adreslerini ve aralıklarını içerebilir. Dosya başlık içeremez.

> [!NOTE]
> Tehdit bilgileri izin adresleri üst ilkelerden alt ilkelere devralınır. Bir üst ilkeye eklenen herhangi bir IP adresi veya aralığı, tüm alt ilkeler için de geçerlidir.

## <a name="logs"></a>Günlükler

Aşağıdaki günlük alıntısı, bir kötü amaçlı siteye giden trafik için tetiklenen bir kural gösterir:

```json
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Test Etme

- **Giden sınama** -giden trafik uyarıları nadir bir oluşum olmalıdır, çünkü ortamınız tehlikede olduğu anlamına gelir. Giden uyarıların test sağlanmasına yardımcı olmak için bir uyarıyı tetikleyen bir test FQDN 'SI oluşturulmuştur. Giden testleriniz için **testmaliciousdomain.eastus.cloudapp.Azure.com** kullanın.

- **Gelen sınama** -güvenlik duvarında DNAT kuralları yapılandırılırsa, gelen trafikte uyarıları görmeyi bekleyebilir. Bu, DNAT kuralında yalnızca belirli kaynaklara izin verildiğinden ve trafik başka bir şekilde reddedilse bile geçerlidir. Azure Güvenlik Duvarı tüm bilinen bağlantı noktası tarayıcıları üzerinde uyarı vermez; yalnızca kötü amaçlı etkinlikler için de bilinen tarayıcılarda.

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft Güvenlik Zekası raporunu](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) gözden geçirin
