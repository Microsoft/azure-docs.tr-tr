---
title: Azure Relay ad alanı için IP güvenlik duvarını yapılandırma
description: Bu makalede, belirli IP adreslerinden Azure Relay ad alanlarına bağlantılara izin vermek için Güvenlik Duvarı kurallarının nasıl kullanılacağı açıklanır.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ad8feed5df49dcc4503226a5fae50195bb9d48aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91999510"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Azure Relay ad alanı için IP güvenlik duvarını yapılandırma
Varsayılan olarak, istek geçerli kimlik doğrulaması ve yetkilendirmeyle geldiği sürece, geçiş ad alanlarına internet 'ten erişilebilir. IP güvenlik duvarı ile bunu, [CIDR (sınıfsız Inter-Domain yönlendirme)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) gösteriminde yalnızca bir IPv4 adresi veya IPv4 adres aralıkları kümesiyle sınırlayabilirsiniz.

Bu özellik, Azure Relay yalnızca belirli bilinen sitelerden erişilebilir olması gereken senaryolarda yararlıdır. Güvenlik duvarı kuralları, belirli IPv4 adreslerinden kaynaklanan trafiği kabul etmek için kuralları yapılandırmanızı sağlar. Örneğin, [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)ile geçiş kullanıyorsanız, yalnızca şirket ıçı altyapı IP adreslerinden gelen trafiğe izin vermek için bir **güvenlik duvarı kuralı** oluşturabilirsiniz. 


> [!IMPORTANT]
> Bu özellik şu anda önizleme sürümündedir. 


## <a name="enable-ip-firewall-rules"></a>IP güvenlik duvarı kurallarını etkinleştir
IP güvenlik duvarı kuralları ad alanı düzeyinde uygulanır. Bu nedenle, kurallar desteklenen herhangi bir protokolü kullanarak istemcilerden gelen tüm bağlantılara uygulanır. Ad alanındaki izin verilen bir IP kuralıyla eşleşmeyen bir IP adresinden gelen bağlantı girişimleri yetkisiz olarak reddedilir. Yanıt, IP kuralından bahsetmiyor. IP filtresi kuralları sırasıyla uygulanır ve IP adresiyle eşleşen ilk kural kabul etme veya reddetme eylemini belirler.

### <a name="use-azure-portal"></a>Azure portalı kullanma
Bu bölümde, bir ad alanı için IP güvenlik duvarı kuralları oluşturmak üzere Azure portal nasıl kullanılacağı gösterilmektedir. 

1. [Azure Portal](https://portal.azure.com) **geçiş ad alanına** gidin.
2. Sol taraftaki menüden **ağ** seçeneği ' ni seçin. **Erişime Izin ver** bölümünde **tüm ağlar** seçeneğini belirlerseniz, geçiş ad alanı herhangi bir IP adresinden gelen bağlantıları kabul eder. Bu ayar 0.0.0.0/0 IP adresi aralığını kabul eden bir kuralla eşdeğerdir. 

    ![Ekran görüntüsü ağ sayfasını tüm ağlar seçeneğinin seçili olduğunu gösterir.](./media/ip-firewall/all-networks-selected.png)
1. Belirli ağlara ve IP adreslerine erişimi kısıtlamak için **Seçili ağlar** seçeneğini belirleyin. **Güvenlik duvarı** bölümünde şu adımları izleyin:
    1. Geçerli istemci IP 'nize ad alanına erişim sağlamak için **ISTEMCI IP adresi ekle** seçeneğini belirleyin. 
    2. **Adres aralığı** IÇIN, CIDR gösteriminde belirli bir IPv4 adresi veya bir IPv4 adresi aralığı girin. 

        ![Güvenlik Duvarı-tüm ağlar seçeneği seçildi](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. Ayarları kaydetmek için araç çubuğunda **Kaydet** ' i seçin. Onayın Portal bildirimlerinde gösterilmesi için birkaç dakika bekleyin.


### <a name="use-resource-manager-template"></a>Resource Manager şablonu kullanma
Aşağıdaki Kaynak Yöneticisi şablonu, var olan bir geçiş ad alanına bir IP filtre kuralı eklenmesine olanak sağlar.

Şablon bir parametre alır: **IPMask**, tek bir IPv4 adresı veya CIDR GÖSTERIMINDE bir IP adresleri bloğu. Örneğin, CıDR gösteriminde 70.37.104.0/24, Aralık için önemli olan önek bit sayısını belirten, 70.37.104.0 ile 70.37.104.255 arasındaki 256 IPv4 adresini temsil eder.

> [!NOTE]
> Mümkün olan reddetme kuralları olmadığı sürece, Azure Resource Manager şablonu, bağlantıları kısıtlayameyen **"Izin ver"** olarak ayarlanmış varsayılan eylemi içerir.
> Sanal ağ veya güvenlik duvarları kuralları yaparken, ***"DefaultAction"*** öğesini değiştirmemiz gerekir
> 
> Kaynak
> ```json
> "defaultAction": "Allow"
> ```
> kullanıcısı
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.Relay/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "virtualNetworkRules": [],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Şablonu dağıtmak için [Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)talimatlarını izleyin.



## <a name="next-steps"></a>Sonraki adımlar
Ağ güvenliği ile ilgili diğer özellikler hakkında bilgi edinmek için bkz. [ağ güvenliği](network-security.md).


<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
