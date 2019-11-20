---
title: Azure Service Bus güvenlik duvarı kuralları | Microsoft Docs
description: Belirli IP adreslerinden Azure Service Bus bağlantılara izin vermek için güvenlik duvarı kuralları kullanma.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 45415af479c9581ee04b97af4fb5297d09c5769d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496338"
---
# <a name="use-firewall-rules"></a>Güvenlik duvarı kurallarını kullanma

Azure Service Bus yalnızca belirli bir bilinen sitelerden erişilebilen senaryolarda güvenlik duvarı kuralları, belirli IPv4 adreslerinden kaynaklanan trafiği kabul etmek için kuralları yapılandırmanızı sağlar. Örneğin, bu adresler bir kurumsal NAT ağ geçidi olabilir.

## <a name="when-to-use"></a>Kullanılması gereken durumlar

Yalnızca belirli bir IP adresi aralığından trafik alması ve diğer her şeyi reddetmesi gibi Service Bus kurmak istiyorsanız, diğer IP adreslerinden Service Bus uç noktaları engellemek için bir *güvenlik duvarı* üzerinden yararlanabilirsiniz. Örneğin, şirket içi altyapınızla özel bağlantılar oluşturmak için [Azure Express Route][express-route] ile Service Bus kullanıyorsunuz. 

## <a name="how-filter-rules-are-applied"></a>Filtre kuralları nasıl uygulanır

IP filtresi kuralları Service Bus ad alanı düzeyinde uygulanır. Bu nedenle, kurallar desteklenen herhangi bir protokolü kullanarak istemcilerden gelen tüm bağlantılara uygulanır.

Service Bus ad alanındaki izin verilen bir IP kuralıyla eşleşmeyen bir IP adresinden gelen bağlantı girişimleri yetkisiz olarak reddedilir. Yanıt, IP kuralından bahsetmiyor.

## <a name="default-setting"></a>Varsayılan ayar

Varsayılan olarak, Service Bus Portal 'daki **IP filtresi** Kılavuzu boştur. Bu varsayılan ayar, ad alanınız herhangi bir IP adresine bağlantı kabul ettiği anlamına gelir. Bu varsayılan ayar 0.0.0.0/0 IP adresi aralığını kabul eden bir kuralla eşdeğerdir.

## <a name="ip-filter-rule-evaluation"></a>IP filtresi kuralı değerlendirmesi

IP filtresi kuralları sırasıyla uygulanır ve IP adresiyle eşleşen ilk kural kabul etme veya reddetme eylemini belirler.

>[!WARNING]
> Güvenlik duvarı kuralları uygulamak, diğer Azure hizmetlerinin Service Bus etkileşimde olmasını engelleyebilir.
>
> IP filtrelemesi (güvenlik duvarı kuralları) uygulandığında güvenilir Microsoft Hizmetleri desteklenmez ve yakında kullanıma sunulacaktır.
>
> IP filtrelemesi ile çalışmayan yaygın Azure senaryoları ( **listenin ayrıntılı olmadığına** unutmayın)-
> - Azure İzleyici
> - Azure Stream Analytics
> - Azure Event Grid ile tümleştirme
> - Azure IoT Hub yolları
> - Azure IoT Device Explorer
>
> Aşağıdaki Microsoft hizmetlerinin bir sanal ağda olması gerekir
> - Azure App Service
> - Azure İşlevleri

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarıyla bir sanal ağ ve güvenlik duvarı kuralı oluşturma

> [!IMPORTANT]
> Güvenlik duvarları ve sanal ağlar yalnızca Service Bus **Premium** katmanında desteklenir.

Aşağıdaki Kaynak Yöneticisi şablonu, var olan bir Service Bus ad alanına bir sanal ağ kuralı eklenmesini sağlar.

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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

Azure sanal ağlarına Service Bus erişimi kısıtlayan için şu bağlantıya bakın:

- [Service Bus için sanal ağ hizmeti uç noktaları][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
