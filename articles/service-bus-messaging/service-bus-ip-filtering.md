---
title: Azure Service Bus için IP güvenlik duvarı kurallarını yapılandırma
description: Belirli IP adreslerinden Azure Service Bus bağlantılara izin vermek için güvenlik duvarı kuralları kullanma.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: 747e15e912033c5bc6a1f64ab389f1ab03f40c0b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962331"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Belirli IP adreslerinden veya aralıklardan Azure Service Bus ad alanına erişime izin ver
Varsayılan olarak, istek geçerli kimlik doğrulaması ve yetkilendirmeyle geldiği sürece, Service Bus ad alanlarına internet 'ten erişilebilir. IP güvenlik duvarı ile bunu, [CIDR (sınıfsız Inter-Domain yönlendirme)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) gösteriminde yalnızca bir IPv4 adresi veya IPv4 adres aralıkları kümesiyle sınırlayabilirsiniz.

Bu özellik, Azure Service Bus yalnızca belirli bilinen sitelerden erişilebilir olması gereken senaryolarda yararlıdır. Güvenlik duvarı kuralları, belirli IPv4 adreslerinden kaynaklanan trafiği kabul etmek için kuralları yapılandırmanızı sağlar. Örneğin, [Azure Express Route][express-route]ile Service Bus kullanıyorsanız, yalnızca şirket ıçı altyapı IP adreslerinizin veya BIR kurumsal NAT ağ geçidinin adreslerinden gelen trafiğe izin vermek için bir **güvenlik duvarı kuralı** oluşturabilirsiniz. 

## <a name="ip-firewall-rules"></a>IP güvenlik duvarı kuralları
IP güvenlik duvarı kuralları Service Bus ad alanı düzeyinde uygulanır. Bu nedenle, kurallar desteklenen herhangi bir protokolü kullanarak istemcilerden gelen tüm bağlantılara uygulanır. Service Bus ad alanındaki izin verilen bir IP kuralıyla eşleşmeyen bir IP adresinden gelen bağlantı girişimleri yetkisiz olarak reddedilir. Yanıt, IP kuralından bahsetmiyor. IP filtresi kuralları sırasıyla uygulanır ve IP adresiyle eşleşen ilk kural kabul etme veya reddetme eylemini belirler.

## <a name="important-points"></a>Önemli nokta
- Güvenlik duvarları ve sanal ağlar yalnızca Service Bus **Premium** katmanında desteklenir. **Premier** katmanına yükseltme bir seçenek değilse, paylaşılan erişim IMZASı (SAS) belirtecini güvenli halde tutmanızı ve yalnızca yetkili kullanıcılarla paylaşmanızı öneririz. SAS kimlik doğrulaması hakkında daha fazla bilgi için bkz. [kimlik doğrulama ve yetkilendirme](service-bus-authentication-and-authorization.md#shared-access-signature).
- Yalnızca belirtilen IP adreslerinden veya bir sanal ağın alt ağından gelen trafiğe izin vermek için ad alanı için **en az BIR IP güvenlik duvarı kuralı veya sanal ağ kuralı** belirtin. IP ve sanal ağ kuralları yoksa, ad alanına genel İnternet üzerinden erişilebilir (erişim anahtarı kullanılarak).  
- Güvenlik duvarı kuralları uygulamak, diğer Azure hizmetlerinin Service Bus etkileşimde olmasını engelleyebilir. Özel durum olarak, IP filtrelemesi etkinleştirildiğinde bile belirli **Güvenilen hizmetlerden** Service Bus kaynaklara erişime izin verebilirsiniz. Güvenilen hizmetler listesi için bkz. [Güvenilen hizmetler](#trusted-microsoft-services). 

    Aşağıdaki Microsoft hizmetlerinin bir sanal ağda olması gerekir
    - Azure App Service
    - Azure İşlevleri

## <a name="use-azure-portal"></a>Azure portalı kullanma
Bu bölümde, bir Service Bus ad alanı için IP güvenlik duvarı kuralları oluşturmak üzere Azure portal nasıl kullanılacağı gösterilmektedir. 

1. [Azure portal](https://portal.azure.com) **Service Bus ad alanına** gidin.
2. Sol taraftaki menüde, **Ayarlar** altında **ağ** seçeneği ' ni seçin.  

    > [!NOTE]
    > **Ağ** sekmesini yalnızca **Premium** ad alanları için görürsünüz.  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Ağ sayfası-varsayılan" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    **Tüm ağlar** seçeneğini belirlerseniz, Service Bus ad alanınız HERHANGI bir IP adresinden gelen bağlantıları kabul eder. Bu varsayılan ayar 0.0.0.0/0 IP adres aralığını kabul eden kuralla eşdeğerdir. 

    ![Azure portal ağ sayfasının ekran görüntüsü. Tüm ağlardan erişime izin verme seçeneği güvenlik duvarları ve sanal ağlar sekmesinde seçilir.](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Yalnızca belirtilen IP adresinden erişime izin vermek için, henüz seçili değilse **Seçili ağlar** seçeneğini belirleyin. **Güvenlik duvarı** bölümünde şu adımları izleyin:
    1. Geçerli istemci IP 'nize ad alanına erişim sağlamak için **ISTEMCI IP adresi ekle** seçeneğini belirleyin. 
    2. **Adres aralığı** IÇIN, CIDR gösteriminde belirli bir IPv4 adresi veya bir IPv4 adresi aralığı girin. 
    3. **Güvenilen Microsoft hizmetlerinin bu güvenlik duvarını atlamasına izin** vermek isteyip istemediğinizi belirtin. 

        >[!WARNING]
        > **Seçili ağlar** seçeneğini belirleyin ve bu sayfada en az bir IP güvenlik duvarı kuralı veya bir sanal ağ eklememeniz durumunda, ad alanına genel İnternet üzerinden erişilebilir (erişim anahtarı kullanılarak).    

        ![Azure portal ağ sayfasının ekran görüntüsü. Seçili ağlardan erişime izin verme seçeneği seçilidir ve güvenlik duvarı bölümü vurgulanır.](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Ayarları kaydetmek için araç çubuğunda **Kaydet** ' i seçin. Onayın Portal bildirimlerinde gösterilmesi için birkaç dakika bekleyin.

    > [!NOTE]
    > Belirli sanal ağlara erişimi kısıtlamak için bkz. [belirli ağlardan erişime Izin ver](service-bus-service-endpoints.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Resource Manager şablonu kullanma
Bu bölümde, var olan bir Service Bus ad alanına bir sanal ağ ve bir güvenlik duvarı kuralı ekleyen örnek bir Azure Resource Manager şablonu vardır.

**IPMask** , CIDR gösteriminde tek bir IPv4 ADRESIDIR veya IP adresi bloğudur. Örneğin, CıDR gösteriminde 70.37.104.0/24, Aralık için önemli olan önek bit sayısını belirten, 70.37.104.0 ile 70.37.104.255 arasındaki 256 IPv4 adresini temsil eder.

Sanal ağ veya güvenlik duvarları kuralları eklerken, değerini `defaultAction` olarak ayarlayın `Deny` .


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
        "type": "Microsoft.ServiceBus/namespaces/networkrulesets",
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
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Şablonu dağıtmak için [Azure Resource Manager][lnk-deploy]talimatlarını izleyin.

> [!IMPORTANT]
> IP ve sanal ağ kuralları yoksa, olarak ayarlamış olsanız bile tüm trafik ad alanına akar `defaultAction` `deny` . Ad alanına genel İnternet üzerinden erişilebilir (erişim anahtarı kullanılarak). Yalnızca belirtilen IP adreslerinden veya bir sanal ağın alt ağından gelen trafiğe izin vermek için ad alanı için en az bir IP kuralı veya sanal ağ kuralı belirtin.  


## <a name="next-steps"></a>Sonraki adımlar

Azure sanal ağlarına Service Bus erişimi kısıtlayan için şu bağlantıya bakın:

- [Service Bus için sanal ağ hizmeti uç noktaları][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services