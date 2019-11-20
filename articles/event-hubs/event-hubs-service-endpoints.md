---
title: Sanal ağ hizmeti uç noktaları-Azure Event Hubs | Microsoft Docs
description: Bu makalede bir sanal ağa Microsoft. EventHub hizmet uç noktası ekleme hakkında bilgi sağlanır.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 5a1b293d4d7f652c0cdd95226113ec3ce8f8222c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466130"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Azure Event Hubs ile sanal ağ hizmet uç noktalarını kullanma

[Sanal ağ (VNet) hizmet uç noktaları][vnet-sep] ile Event Hubs tümleştirmesi, sanal ağlara bağlı sanal makineler gibi iş yüklerinden, her ikisinde de güvenli hale getirilen ağ trafiği yolu ile güvenli erişim sağlar ucundaki.

En az bir sanal ağ alt ağ hizmeti uç noktasına bağlanacak şekilde yapılandırıldıktan sonra, ilgili Event Hubs ad alanı artık sanal ağlardaki her yerden trafiği kabul etmez. Sanal ağ perspektifinden bir Event Hubs ad alanını bir hizmet uç noktasına bağlamak, sanal ağ alt ağından mesajlaşma hizmetine yalıtılmış bir ağ tüneli yapılandırır. 

Sonuç olarak, alt ağa ve ilgili Event Hubs ad alanıyla ilişkili olan iş yükleri arasında özel ve yalıtılmış bir ilişki vardır. Bu, bir genel IP aralığında yer alan mesajlaşma hizmeti uç noktasının observable ağ adresi artma. Bu davranış için bir özel durum vardır. Hizmet uç noktasının etkinleştirilmesi, varsayılan olarak, sanal ağla ilişkili IP güvenlik duvarında denyall kuralını etkinleştirir. Olay Hub 'ı genel uç noktasına erişimi etkinleştirmek için IP güvenlik duvarında belirli IP adresleri ekleyebilirsiniz. 


>[!WARNING]
> Sanal ağlar tümleştirmesini uygulamak, diğer Azure hizmetlerinin Event Hubs etkileşimde olmasını engelleyebilir.
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
> - Azure Web Apps
> - Azure İşlevleri

> [!IMPORTANT]
> Sanal ağlar, Event Hubs **Standart** ve **adanmış** katmanlarında desteklenir. Temel katmanda desteklenmez.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet tümleştirmesi tarafından etkinleştirilen gelişmiş güvenlik senaryoları 

Sıkı ve compartmenbir güvenlik gerektiren ve sanal ağ alt ağlarının, compartmençalıştıran hizmetler arasında segmentleme sağladığı çözümler, yine de bu bölmeleri bulunan hizmetler arasında iletişim yollarına gerek duyar.

TCP/IP üzerinden HTTPS 'yi yürüten bu dahil olmak üzere, bölmeleri arasındaki tüm anında IP rotası, üzerindeki ağ katmanından güvenlik açıklarına karşı yararlanma riskini taşır. Mesajlaşma Hizmetleri, iletiler, taraflar arasında geçiş yaparken iletilerin diske yazıldığı, tamamen yalıtılmış iletişim yolları sağlar. Aynı Event Hubs örneğine bağlanan iki ayrı sanal ağdaki iş yükleri iletiler aracılığıyla verimli ve güvenilir bir şekilde iletişim kurabilir, ancak ilgili ağ yalıtımı sınır bütünlüğü korunur.
 
Bu, güvenlik duyarlı bulut çözümlerinizin yalnızca Azure sektör lideri güvenilir ve ölçeklenebilir zaman uyumsuz mesajlaşma özelliklerine erişim elde edemeyeceği anlamına gelir, ancak artık güvenli çözüm bölmeleri arasında iletişim yolları oluşturmak için mesajlaşma kullanabilir , HTTPS ve diğer TLS güvenlikli yuva protokolleri de dahil olmak üzere eşler arası iletişim moduyla ulaşılabilir, doğal olarak daha güvenlidir.

## <a name="bind-event-hubs-to-virtual-networks"></a>Event Hubs sanal ağlara bağlama

*Sanal ağ kuralları* , Azure Event Hubs ad uzayının belirli bir sanal ağ alt ağından gelen bağlantıları kabul edip etmediğini denetleyen güvenlik duvarı güvenlik özelliğidir.

Bir Event Hubs ad alanını bir sanal ağa bağlamak iki adımlı bir işlemdir. Önce bir sanal ağ alt ağında bir **sanal ağ hizmeti uç noktası** oluşturmanız ve bunu [hizmet uç noktasına genel bakış][vnet-sep]bölümünde açıklandığı gibi "Microsoft. EventHub" için etkinleştirmeniz gerekir. Hizmet uç noktasını ekledikten sonra, Event Hubs ad alanını bir *sanal ağ kuralıyla*bağlayın.

Sanal ağ kuralı, bir sanal ağ alt ağıyla Event Hubs ad alanının bir ilişkidir. Kural var olsa da, alt ağa erişen tüm iş yükleri Event Hubs ad alanına erişim izni verilir. Event Hubs kendisi hiçbir şekilde giden bağlantı oluşturmaz, erişim elde etmek zorunda değildir ve bu nedenle bu kuralı etkinleştirerek alt ağınız için hiçbir şekilde erişim izni verilmez.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarıyla bir sanal ağ kuralı oluşturma

Aşağıdaki Kaynak Yöneticisi şablonu, var olan bir Event Hubs ad alanına bir sanal ağ kuralı eklenmesini sağlar.

Şablon parametreleri:

* **NamespaceName**: Event Hubs ad alanı.
* **Vnetrutaname**: oluşturulacak sanal ağ kuralının adı.
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
                    "service": "Microsoft.EventHub"
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
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
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
- [Azure Event Hubs IP filtreleme][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
