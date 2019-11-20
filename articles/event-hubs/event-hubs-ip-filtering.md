---
title: Azure Event Hubs güvenlik duvarı kuralları | Microsoft Docs
description: Belirli IP adreslerinden Azure Event Hubs bağlantılara izin vermek için güvenlik duvarı kurallarını kullanın.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: f96c25dbb85ed92141636487f10d861a8c5e5f28
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468431"
---
# <a name="use-firewall-rules"></a>Güvenlik duvarı kurallarını kullanma

Azure Event Hubs yalnızca belirli bir bilinen sitelerden erişilebilir olması gereken senaryolarda güvenlik duvarı kuralları, belirli IPv4 adreslerinden kaynaklanan trafiği kabul etmek için kuralları yapılandırmanızı sağlar. Örneğin, bu adresler bir kurumsal NAT ağ geçidi olabilir.

## <a name="when-to-use"></a>Kullanılması gereken durumlar

Event Hubs ad alanınızı, yalnızca belirli bir IP adresi aralığından trafik alması ve diğer her şeyi reddedecek şekilde ayarlamak istiyorsanız, diğer IP adreslerinden Olay Hub 'ı uç noktalarını engellemek için bir *güvenlik duvarı kuralından* yararlanabilirsiniz. Örneğin, [Azure Express Route][express-route]ile Event Hubs kullanıyorsanız, şirket ıçı altyapı IP adreslerinizin trafiğini kısıtlamak Için bir *güvenlik duvarı kuralı* oluşturabilirsiniz.

## <a name="how-filter-rules-are-applied"></a>Filtre kuralları nasıl uygulanır

IP filtresi kuralları Event Hubs ad alanı düzeyinde uygulanır. Bu nedenle, kurallar desteklenen herhangi bir protokolü kullanarak istemcilerden gelen tüm bağlantılara uygulanır.

Event Hubs ad alanındaki izin verilen bir IP kuralıyla eşleşmeyen bir IP adresinden gelen bağlantı girişimleri yetkisiz olarak reddedilir. Yanıt, IP kuralından bahsetmiyor.

## <a name="default-setting"></a>Varsayılan ayar

Varsayılan olarak, Event Hubs Portal 'daki **IP filtresi** Kılavuzu boştur. Bu varsayılan ayar, Olay Hub 'ınızın herhangi bir IP adresinden bağlantıları kabul ettiği anlamına gelir. Bu varsayılan ayar 0.0.0.0/0 IP adresi aralığını kabul eden bir kuralla eşdeğerdir.

## <a name="ip-filter-rule-evaluation"></a>IP filtresi kuralı değerlendirmesi

IP filtresi kuralları sırasıyla uygulanır ve IP adresiyle eşleşen ilk kural kabul etme veya reddetme eylemini belirler.

>[!WARNING]
> Güvenlik duvarlarını uygulamak, diğer Azure hizmetlerinin Event Hubs etkileşimde olmasını engelleyebilir.
>
> IP filtrelemesi (güvenlik duvarları) uygulandığında güvenilir Microsoft Hizmetleri desteklenmez ve yakında kullanıma sunulacaktır.
>
> IP filtrelemesi ile çalışmayan yaygın Azure senaryoları ( **listenin ayrıntılı olmadığına** unutmayın)-
> - Azure İzleyici
> - Azure Stream Analytics
> - Azure Event Grid ile tümleştirme
> - Azure IoT Hub yolları
> - Azure IoT Device Explorer
>
> Aşağıdaki Microsoft hizmetlerinin bir sanal ağda olması gerekir
> - Azure Web Apps
> - Azure İşlevleri

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarıyla bir güvenlik duvarı kuralı oluşturma

> [!IMPORTANT]
> Güvenlik duvarı kuralları, Event Hubs **Standart** ve **adanmış** katmanlarında desteklenir. Temel katmanda desteklenmez.

Aşağıdaki Kaynak Yöneticisi şablonu, var olan bir Event Hubs ad alanına bir IP filtre kuralı eklenmesine olanak sağlar.

Şablon parametreleri:

- **IPMask** , CIDR gösteriminde tek bir IPv4 ADRESIDIR veya IP adresi bloğudur. Örneğin, CıDR gösteriminde 70.37.104.0/24, Aralık için önemli olan önek bit sayısını belirten, 70.37.104.0 ile 70.37.104.255 arasındaki 256 IPv4 adresini temsil eder.

> [!NOTE]
> Mümkün olan reddetme kuralları olmadığı sürece, Azure Resource Manager şablonu, bağlantıları kısıtlayameyen **"Izin ver"** olarak ayarlanmış varsayılan eylemi içerir.
> Sanal ağ veya güvenlik duvarları kuralları yaparken, ***"DefaultAction"*** öğesini değiştirmemiz gerekir
> 
> Kaynak
> ```json
> "defaultAction": "Allow"
> ```
> -
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
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
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
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
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Şablonu dağıtmak için [Azure Resource Manager][lnk-deploy]talimatlarını izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure sanal ağlarına Event Hubs erişimi kısıtlayan için şu bağlantıya bakın:

- [Event Hubs için sanal ağ hizmeti uç noktaları][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
