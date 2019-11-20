---
title: Sanal ağ hizmeti uç noktaları-Azure Service Bus
description: Bir sanal ağa Microsoft. ServiceBus hizmet uç noktası ekleyin.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: aschhab
ms.openlocfilehash: 7d31dd004c879fd3e689f4ba7a8ae58cb223ae70
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484930"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Azure Service Bus ile sanal ağ hizmeti uç noktalarını kullanma

[Sanal ağ (VNet) hizmet uç noktaları][vnet-sep] ile Service Bus tümleştirmesi, sanal ağlara bağlı sanal makineler gibi iş yüklerinden, her ikisinde de güvenli hale getirilen ağ trafiği yolu ile güvenli erişim sağlar ucundaki.

En az bir sanal ağ alt ağ hizmeti uç noktasına bağlanacak şekilde yapılandırıldıktan sonra, ilgili Service Bus ad alanı artık herhangi bir yerden trafiği kabul etmez, ancak yetkilendirilmiş sanal ağ (ler) i. Sanal ağ perspektifinden bir Service Bus ad alanını bir hizmet uç noktasına bağlamak, sanal ağ alt ağından mesajlaşma hizmetine yalıtılmış bir ağ tüneli yapılandırır.

Sonuç olarak, alt ağa ve ilgili Service Bus ad alanıyla ilişkili olan iş yükleri arasında özel ve yalıtılmış bir ilişki vardır. Bu, bir genel IP aralığında yer alan mesajlaşma hizmeti uç noktasının observable ağ adresi artma.

>[!WARNING]
> Sanal ağlar tümleştirmesini uygulamak, diğer Azure hizmetlerinin Service Bus etkileşimde olmasını engelleyebilir.
>
> Sanal ağlar uygulandığında güvenilen Microsoft Hizmetleri desteklenmez.
>
> Sanal ağlarla çalışmayan yaygın Azure senaryoları ( **listenin ayrıntılı olmadığına** unutmayın)-
> - Azure İzleyici
> - Azure Stream Analytics
> - Azure Event Grid ile tümleştirme
> - Azure IoT Hub yolları
> - Azure IoT Device Explorer
>
> Aşağıdaki Microsoft hizmetlerinin bir sanal ağda olması gerekir
> - Azure App Service
> - Azure İşlevleri

> [!IMPORTANT]
> Sanal ağlar yalnızca [Premium katmanda](service-bus-premium-messaging.md) Service Bus ad alanlarında desteklenir.

## <a name="enable-service-endpoints-with-service-bus"></a>Service Bus ile hizmet uç noktalarını etkinleştirme

VNet hizmet uç noktalarını Service Bus kullandığınızda önemli bir göz önünde bulundurun. bu uç noktaları, standart ve Premium katman Service Bus ad alanlarını karışan uygulamalarda etkinleştirmemelidir. Standart katman VNET 'leri desteklemediğinden, uç nokta yalnızca Premium katman ad alanları ile kısıtlıdır.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet tümleştirmesi tarafından etkinleştirilen gelişmiş güvenlik senaryoları 

Sıkı ve compartmenbir güvenlik gerektiren ve sanal ağ alt ağlarının, compartmenhizmeti arasında segmentleme sağladığı çözümler, genellikle bu bölmeleri bulunan hizmetler arasında iletişim yollarına gerek duyar.

TCP/IP üzerinden HTTPS 'yi yürüten bu dahil olmak üzere, bölmeleri arasındaki tüm anında IP rotası, üzerindeki ağ katmanından güvenlik açıklarına karşı yararlanma riskini taşır. Mesajlaşma Hizmetleri, iletiler, taraflar arasında geçiş yaparken iletilerin diske yazıldığı, tamamen yalıtılmış iletişim yolları sağlar. Aynı Service Bus örneğine bağlanan iki ayrı sanal ağdaki iş yükleri iletiler aracılığıyla verimli ve güvenilir bir şekilde iletişim kurabilir, ancak ilgili ağ yalıtımı sınır bütünlüğü korunur.
 
Bu, güvenlik duyarlı bulut çözümlerinizin yalnızca Azure sektör lideri güvenilir ve ölçeklenebilir zaman uyumsuz mesajlaşma özelliklerine erişim elde edemeyeceği anlamına gelir, ancak artık güvenli çözüm bölmeleri arasında iletişim yolları oluşturmak için mesajlaşma kullanabilir , HTTPS ve diğer TLS güvenlikli yuva protokolleri de dahil olmak üzere eşler arası iletişim moduyla ulaşılabilir, doğal olarak daha güvenlidir.

## <a name="binding-service-bus-to-virtual-networks"></a>Service Bus sanal ağlara bağlama

*Sanal ağ kuralları* , Azure Service Bus sunucunuzun belirli bir sanal ağ alt ağından gelen bağlantıları kabul edip etmediğini denetleyen güvenlik duvarı güvenlik özelliğidir.

Bir Service Bus ad alanını bir sanal ağa bağlamak iki adımlı bir işlemdir. Önce bir sanal ağ alt ağında bir **sanal ağ hizmeti uç noktası** oluşturmanız ve bunu [hizmet uç noktasına genel bakış][vnet-sep]bölümünde açıklandığı gibi "Microsoft. ServiceBus" için etkinleştirmeniz gerekir. Hizmet uç noktasını ekledikten sonra, Service Bus ad alanını bir *sanal ağ kuralıyla*bağlayın.

Sanal ağ kuralı, bir sanal ağ alt ağıyla Service Bus ad alanının bir ilişkidir. Kural var olsa da, alt ağa erişen tüm iş yükleri Service Bus ad alanına erişim izni verilir. Service Bus kendisi hiçbir şekilde giden bağlantı oluşturmaz, erişim elde etmek zorunda değildir ve bu nedenle bu kuralı etkinleştirerek alt ağınız için hiçbir şekilde erişim izni verilmez.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarıyla bir sanal ağ kuralı oluşturma

Aşağıdaki Kaynak Yöneticisi şablonu, var olan bir Service Bus ad alanına bir sanal ağ kuralı eklenmesini sağlar.

Şablon parametreleri:

* **NamespaceName**: Service Bus ad alanı.
* **Virtualnetworkingsubnetıd**: sanal ağ alt ağı için tam olarak nitelenmiş Kaynak Yöneticisi yolu; Örneğin, bir sanal ağın varsayılan alt ağı için `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default`.

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

Şablon:

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Şablonu dağıtmak için [Azure Resource Manager][lnk-deploy]talimatlarını izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Sanal ağlar hakkında daha fazla bilgi için aşağıdaki bağlantılara bakın:

- [Azure sanal ağ hizmet uç noktaları][vnet-sep]
- [Azure Service Bus IP filtreleme][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md
