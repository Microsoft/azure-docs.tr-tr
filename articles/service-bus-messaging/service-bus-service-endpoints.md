---
title: Azure Service Bus için sanal ağ hizmet uç noktalarını yapılandırma
description: Bu makalede bir sanal ağa Microsoft. ServiceBus hizmet uç noktası ekleme hakkında bilgi sağlanır.
ms.topic: article
ms.date: 03/29/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 9b5aba6c5ad4e1e6d0c90b99ebcdf441bb35cc39
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960461"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>Belirli sanal ağlardan Azure Service Bus ad alanına erişime izin ver
[Sanal ağ (VNet) hizmet uç noktaları][vnet-sep] ile Service Bus tümleştirmesi, sanal ağlara bağlı sanal makineler gibi iş yüklerinden, her iki uçta da güvenli hale getirilen ağ trafiği yolu ile güvenli erişim sağlar.

En az bir sanal ağ alt ağ hizmeti uç noktasına bağlanacak şekilde yapılandırıldıktan sonra ilgili Service Bus ad alanı artık her yerden trafiği kabul etmez, ancak isteğe bağlı olarak belirli internet IP adreslerinden gelen trafiği kabul etmez. Sanal ağ perspektifinden bir Service Bus ad alanını bir hizmet uç noktasına bağlamak, sanal ağ alt ağından mesajlaşma hizmetine yalıtılmış bir ağ tüneli yapılandırır.

Sonuç olarak, alt ağa ve ilgili Service Bus ad alanıyla ilişkili olan iş yükleri arasında özel ve yalıtılmış bir ilişki vardır. Bu, bir genel IP aralığında yer alan mesajlaşma hizmeti uç noktasının observable ağ adresi artma.

## <a name="important-points"></a>Önemli nokta
- Sanal ağlar yalnızca [Premium katmanda](service-bus-premium-messaging.md) Service Bus ad alanlarında desteklenir. VNet hizmet uç noktalarını Service Bus kullanılırken, standart ve Premium katman Service Bus ad alanlarını karıştıran uygulamalarda bu uç noktaları etkinleştirmemelisiniz. Standart katmanı VNET 'leri desteklemediğinden. Uç nokta yalnızca Premium katman ad alanları ile kısıtlıdır.
- Sanal ağlar tümleştirmesini uygulamak, diğer Azure hizmetlerinin Service Bus etkileşimde olmasını engelleyebilir. Özel durum olarak, ağ hizmeti uç noktaları etkinleştirildiğinde bile belirli **Güvenilen hizmetlerden** Service Bus kaynaklara erişime izin verebilirsiniz. Güvenilen hizmetler listesi için bkz. [Güvenilen hizmetler](#trusted-microsoft-services).

    Aşağıdaki Microsoft hizmetlerinin bir sanal ağda olması gerekir
    - Azure App Service
    - Azure İşlevleri
- Yalnızca belirtilen IP adreslerinden veya bir sanal ağın alt ağından gelen trafiğe izin vermek için ad alanı için **en az BIR IP kuralı veya sanal ağ kuralı** belirtin. IP ve sanal ağ kuralları yoksa, ad alanına genel İnternet üzerinden erişilebilir (erişim anahtarı kullanılarak).  

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet tümleştirmesi tarafından etkinleştirilen gelişmiş güvenlik senaryoları 

Sıkı ve compartmenbir güvenlik gerektiren ve sanal ağ alt ağlarının, compartmenhizmeti arasında segmentleme sağladığı çözümler, genellikle bu bölmeleri bulunan hizmetler arasında iletişim yollarına gerek duyar.

TCP/IP üzerinden HTTPS 'yi yürüten bu dahil olmak üzere, bölmeleri arasındaki tüm anında IP rotası, üzerindeki ağ katmanından güvenlik açıklarına karşı yararlanma riskini taşır. Mesajlaşma Hizmetleri, iletiler, taraflar arasında geçiş yaparken iletilerin diske yazıldığı, tamamen yalıtılmış iletişim yolları sağlar. Aynı Service Bus örneğine bağlanan iki ayrı sanal ağdaki iş yükleri iletiler aracılığıyla verimli ve güvenilir bir şekilde iletişim kurabilir, ancak ilgili ağ yalıtımı sınır bütünlüğü korunur.
 
Bu, güvenlik duyarlı bulut çözümlerinizin yalnızca Azure sektör lideri güvenilir ve ölçeklenebilir zaman uyumsuz mesajlaşma özelliklerine erişim elde edemeyeceği anlamına gelir, ancak artık HTTPS ve diğer TLS güvenlikli yuva protokolleri de dahil olmak üzere herhangi bir eşler arası iletişim moduyla ulaşılabilir 'dan daha güvenli olan güvenli çözüm bölmeleri arasında iletişim yolları oluşturmak için mesajlaşma kullanabilir.

## <a name="binding-service-bus-to-virtual-networks"></a>Service Bus sanal ağlara bağlama

*Sanal ağ kuralları* , Azure Service Bus sunucunuzun belirli bir sanal ağ alt ağından gelen bağlantıları kabul edip etmediğini denetleyen güvenlik duvarı güvenlik özelliğidir.

Bir Service Bus ad alanını bir sanal ağa bağlamak iki adımlı bir işlemdir. Önce bir sanal ağ alt ağında bir **sanal ağ hizmeti uç noktası** oluşturmanız ve [hizmet uç noktasına genel bakış][vnet-sep]bölümünde açıklandığı gibi **Microsoft. ServiceBus** için etkinleştirmeniz gerekir. Hizmet uç noktasını ekledikten sonra, Service Bus ad alanını bir **sanal ağ kuralıyla** bağlayın.

Sanal ağ kuralı, bir sanal ağ alt ağıyla Service Bus ad alanının bir ilişkidir. Kural var olsa da, alt ağa erişen tüm iş yükleri Service Bus ad alanına erişim izni verilir. Service Bus kendisi hiçbir şekilde giden bağlantı oluşturmaz, erişim elde etmek zorunda değildir ve bu nedenle bu kuralı etkinleştirerek alt ağınız için hiçbir şekilde erişim izni verilmez.

> [!NOTE]
> Bir ağ hizmeti uç noktasının, sanal ağda çalışan uygulamaları Service Bus ad alanına erişimi sağladığını unutmayın. Sanal ağ, uç noktanın ulaşılabilirlik durumunu denetler, ancak Service Bus varlıklarda (kuyruklar, konular veya abonelikler) hangi işlemleri yapabileceğinize yönelik değildir. Uygulamaların ad alanında ve varlıklarda gerçekleştirebileceği işlemleri yetkilendirmek için Azure Active Directory (Azure AD) kullanın. Daha fazla bilgi için bkz. [Service Bus varlıklara erişmek Için Azure AD ile bir uygulamaya kimlik doğrulama ve yetkilendirme](authenticate-application.md).


## <a name="use-azure-portal"></a>Azure portalı kullanma
Bu bölümde, bir sanal ağ hizmeti uç noktası eklemek için Azure portal nasıl kullanılacağı gösterilmektedir. Erişimi sınırlandırmak için, bu Event Hubs ad alanı için sanal ağ hizmet uç noktasını tümleştirmeniz gerekir.

1. [Azure portal](https://portal.azure.com) **Service Bus ad alanına** gidin.
2. Sol taraftaki menüde, **Ayarlar** altında **ağ** seçeneği ' ni seçin.  

    > [!NOTE]
    > **Ağ** sekmesini yalnızca **Premium** ad alanları için görürsünüz.  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Ağ sayfası-varsayılan" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    **Tüm ağlar** seçeneğini belirlerseniz, Service Bus ad alanınız HERHANGI bir IP adresinden gelen bağlantıları kabul eder. Bu varsayılan ayar 0.0.0.0/0 IP adres aralığını kabul eden kuralla eşdeğerdir. 

    ![Güvenlik Duvarı-tüm ağlar seçeneği seçildi](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. Belirli sanal ağlara erişimi kısıtlamak için, henüz seçili değilse **Seçili ağlar** seçeneğini belirleyin.
1. Sayfanın **sanal ağ** bölümünde **+ var olan sanal ağı ekle**' yi seçin. 

    ![var olan sanal ağı ekle](./media/service-endpoints/add-vnet-menu.png)

    >[!WARNING]
    > **Seçili ağlar** seçeneğini belirleyin ve bu sayfada en az bir IP güvenlik duvarı kuralı veya bir sanal ağ eklememeniz durumunda, ad alanına genel İnternet üzerinden erişilebilir (erişim anahtarı kullanılarak).
3. Sanal ağlar listesinden sanal ağı seçin ve ardından **alt ağı** seçin. Sanal ağı listeye eklemeden önce hizmet uç noktasını etkinleştirmeniz gerekir. Hizmet uç noktası etkinleştirilmemişse, Portal bunu etkinleştirmenizi ister.
   
   ![alt ağ seçin](./media/service-endpoints/select-subnet.png)

4. Alt ağ için hizmet uç noktası **Microsoft. ServiceBus** için etkinleştirildikten sonra aşağıdaki başarılı iletiyi görmeniz gerekir. Ağı eklemek için sayfanın alt kısmındaki **Ekle** ' yi seçin. 

    ![alt ağ seçin ve uç noktayı etkinleştirin](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Hizmet uç noktasını etkinleştiremeyebilirsiniz, Kaynak Yöneticisi şablonunu kullanarak eksik sanal ağ hizmeti uç noktasını yoksayabilirsiniz. Portalda bu işlev kullanılamaz.
6. Ayarları kaydetmek için araç çubuğunda **Kaydet** ' i seçin. Onayın Portal bildirimlerinde gösterilmesi için birkaç dakika bekleyin. **Kaydet** düğmesi devre dışı bırakılmalıdır. 

    ![Ağı kaydet](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > Belirli IP adreslerinden veya aralıklardan erişime izin verme hakkında yönergeler için bkz. [belırlı IP adreslerinden veya aralıklardan erişime Izin verme](service-bus-ip-filtering.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Resource Manager şablonu kullanma
Aşağıdaki örnek Kaynak Yöneticisi şablonu, var olan bir Service Bus ad alanına bir sanal ağ kuralı ekler. Ağ kuralı için bir sanal ağdaki alt ağın KIMLIĞINI belirtir. 

KIMLIK, sanal ağ alt ağı için tam olarak nitelenmiş bir Kaynak Yöneticisi yoludur. Örneğin, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` bir sanal ağın varsayılan alt ağı için.

Sanal ağ veya güvenlik duvarları kuralları eklerken, değerini `defaultAction` olarak ayarlayın `Deny` .

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
> IP ve sanal ağ kuralları yoksa, olarak ayarlamış olsanız bile tüm trafik ad alanına akar `defaultAction` `deny` .  Ad alanına genel İnternet üzerinden erişilebilir (erişim anahtarı kullanılarak). Yalnızca belirtilen IP adreslerinden veya bir sanal ağın alt ağından gelen trafiğe izin vermek için ad alanı için en az bir IP kuralı veya sanal ağ kuralı belirtin.  

## <a name="next-steps"></a>Sonraki adımlar

Sanal ağlar hakkında daha fazla bilgi için aşağıdaki bağlantılara bakın:

- [Azure sanal ağ hizmet uç noktaları][vnet-sep]
- [Azure Service Bus IP filtreleme][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
