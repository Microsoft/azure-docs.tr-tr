---
title: Sanal ağ hizmeti uç noktaları-Azure Event Hubs | Microsoft Docs
description: Bu makalede bir sanal ağa Microsoft. EventHub hizmet uç noktası ekleme hakkında bilgi sağlanır.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f7f0f3ff480018c9bfc5d9c6f34cf7e2935f8d6a
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959968"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-virtual-networks"></a>Belirli sanal ağlardan Azure Event Hubs ad alanlarına erişime izin ver 

[Sanal ağ (VNet) hizmet uç noktaları][vnet-sep] ile Event Hubs tümleştirmesi, sanal ağlara bağlı sanal makineler gibi iş yüklerinden, her iki uçta da güvenli hale getirilen ağ trafiği yolu ile güvenli erişim sağlar. 

En az bir sanal ağ alt ağ hizmeti uç noktasına bağlanacak şekilde yapılandırıldıktan sonra, ilgili Event Hubs ad alanı artık sanal ağlardaki her yerden trafiği kabul etmez. Sanal ağ perspektifinden bir Event Hubs ad alanını bir hizmet uç noktasına bağlamak, sanal ağ alt ağından mesajlaşma hizmetine yalıtılmış bir ağ tüneli yapılandırır. 

Sonuç olarak, alt ağa ve ilgili Event Hubs ad alanıyla ilişkili olan iş yükleri arasında özel ve yalıtılmış bir ilişki vardır. Bu, bir genel IP aralığında yer alan mesajlaşma hizmeti uç noktasının observable ağ adresi artma. Bu davranış için bir özel durum vardır. Hizmet uç noktasının etkinleştirilmesi, varsayılan olarak `denyall` sanal ağla Ilişkili [IP güvenlik duvarında](event-hubs-ip-filtering.md) kuralı etkinleştirir. Olay Hub 'ı genel uç noktasına erişimi etkinleştirmek için IP güvenlik duvarında belirli IP adresleri ekleyebilirsiniz. 

## <a name="important-points"></a>Önemli nokta
- Bu özellik hem **Standart** hem de **adanmış** katmanlar için desteklenir. **Temel** katmanda desteklenmez.
- Event Hubs ad alanınız için sanal ağların etkinleştirilmesi, istekler izin verilen sanal ağlardan çalışan bir hizmetten kaynaklanmadığı takdirde varsayılan olarak gelen istekleri engeller. Engellenen istekler diğer Azure hizmetlerinden, Azure portal, günlük ve ölçüm hizmetlerinden ve bu şekilde devam eder. Bir özel durum olarak, sanal ağlar etkinleştirildiğinde bile belirli **Güvenilen hizmetlerden** Event Hubs kaynaklara erişime izin verebilirsiniz. Güvenilen hizmetler listesi için bkz. [Güvenilen hizmetler](#trusted-microsoft-services).
- Yalnızca belirtilen IP adreslerinden veya bir sanal ağın alt ağından gelen trafiğe izin vermek için ad alanı için **en az BIR IP kuralı veya sanal ağ kuralı** belirtin. IP ve sanal ağ kuralları yoksa, ad alanına genel İnternet üzerinden erişilebilir (erişim anahtarı kullanılarak).  

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet tümleştirmesi tarafından etkinleştirilen gelişmiş güvenlik senaryoları 

Sıkı ve compartmenbir güvenlik gerektiren ve sanal ağ alt ağlarının, compartmençalıştıran hizmetler arasında segmentleme sağladığı çözümler, yine de bu bölmeleri bulunan hizmetler arasında iletişim yollarına gerek duyar.

TCP/IP üzerinden HTTPS 'yi yürüten bu dahil olmak üzere, bölmeleri arasındaki tüm anında IP rotası, üzerindeki ağ katmanından güvenlik açıklarına karşı yararlanma riskini taşır. Mesajlaşma Hizmetleri, iletilerin taraflar arasında geçiş yaparken bile diske yazıldığı, yalıtılmış iletişim yolları sağlar. Aynı Event Hubs örneğine bağlanan iki ayrı sanal ağdaki iş yükleri iletiler aracılığıyla verimli ve güvenilir bir şekilde iletişim kurabilir, ancak ilgili ağ yalıtımı sınır bütünlüğü korunur.
 
Bu, güvenlik duyarlı bulut çözümlerinizin yalnızca Azure sektör lideri güvenilir ve ölçeklenebilir zaman uyumsuz mesajlaşma özelliklerine erişim elde edemeyeceği anlamına gelir, ancak artık HTTPS ve diğer TLS güvenlikli yuva protokolleri de dahil olmak üzere herhangi bir eşler arası iletişim moduyla ulaşılabilir 'dan daha güvenli olan güvenli çözüm bölmeleri arasında iletişim yolları oluşturmak için mesajlaşma kullanabilir.

## <a name="bind-event-hubs-to-virtual-networks"></a>Olay Hub 'larını sanal ağlara bağlama

**Sanal ağ kuralları** , Azure Event Hubs ad uzayının belirli bir sanal ağ alt ağından gelen bağlantıları kabul edip etmediğini denetleyen güvenlik duvarı güvenlik özelliğidir.

Bir Event Hubs ad alanını bir sanal ağa bağlamak iki adımlı bir işlemdir. Önce bir sanal ağın alt ağında bir **sanal ağ hizmeti uç noktası** oluşturmanız ve bunu [hizmet uç noktası genel bakış][vnet-sep] makalesinde açıklandığı gibi **Microsoft. EventHub** için etkinleştirmeniz gerekir. Hizmet uç noktasını ekledikten sonra, Event Hubs ad alanını bir **sanal ağ kuralıyla** bağlayın.

Sanal ağ kuralı, bir sanal ağ alt ağıyla Event Hubs ad alanının bir ilişkidir. Kural var olsa da, alt ağa erişen tüm iş yükleri Event Hubs ad alanına erişim izni verilir. Event Hubs kendisi hiçbir şekilde giden bağlantı oluşturmaz, erişim elde etmek zorunda kalmaz ve bu nedenle bu kuralı etkinleştirerek alt ağınız için hiçbir şekilde erişim izni verilmemiş olur.

## <a name="use-azure-portal"></a>Azure portalı kullanma
Bu bölümde, bir sanal ağ hizmeti uç noktası eklemek için Azure portal nasıl kullanılacağı gösterilmektedir. Erişimi sınırlandırmak için, bu Event Hubs ad alanı için sanal ağ hizmet uç noktasını tümleştirmeniz gerekir.

1. [Azure portal](https://portal.azure.com) **Event Hubs ad alanına** gidin.
4. Sol menüdeki **Ayarlar** altında **ağ** ' ı seçin. **Ağ** sekmesini yalnızca **Standart** veya **adanmış** ad alanları için görürsünüz. 

    > [!WARNING]
    > **Seçili ağlar** seçeneğini belirleyin ve bu sayfada en az bir IP güvenlik duvarı kuralı veya bir sanal ağ eklememeniz durumunda, ad alanına **genel İnternet** üzerinden erişilebilir (erişim anahtarı kullanılarak). 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Ağlar sekmesi-seçili ağlar seçeneği" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    **Tüm ağlar** seçeneğini belirlerseniz, Olay Hub 'ı HERHANGI bir IP adresinden (erişim anahtarı kullanılarak) gelen bağlantıları kabul eder. Bu ayar 0.0.0.0/0 IP adresi aralığını kabul eden bir kuralla eşdeğerdir. 

    ![Güvenlik Duvarı-tüm ağlar seçeneği seçildi](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Belirli ağlara erişimi kısıtlamak için, henüz seçili değilse sayfanın en üstündeki **Seçili ağlar** seçeneğini belirleyin.
2. Sayfanın **sanal ağ** bölümünde **+ var olan sanal ağı ekle** _ ' yi seçin. Yeni bir VNet oluşturmak istiyorsanız _ *+ Yeni sanal ağ oluştur*' u seçin. 

    ![var olan sanal ağı ekle](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)

    >[!WARNING]
    > **Seçili ağlar** seçeneğini belirleyin ve bu sayfada en az bir IP güvenlik duvarı kuralı veya bir sanal ağ eklememeniz durumunda, ad alanına genel İnternet üzerinden erişilebilir (erişim anahtarı kullanılarak).
3. Sanal ağlar listesinden sanal ağı seçin ve ardından **alt ağı** seçin. Sanal ağı listeye eklemeden önce hizmet uç noktasını etkinleştirmeniz gerekir. Hizmet uç noktası etkinleştirilmemişse, Portal bunu etkinleştirmenizi ister.
   
   ![alt ağ seçin](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Alt ağ için hizmet uç noktası **Microsoft. EventHub** için etkinleştirildikten sonra aşağıdaki başarılı iletiyi görmeniz gerekir. Ağı eklemek için sayfanın alt kısmındaki **Ekle** ' yi seçin. 

    ![alt ağ seçin ve uç noktayı etkinleştirin](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Hizmet uç noktasını etkinleştiremeyebilirsiniz, Kaynak Yöneticisi şablonunu kullanarak eksik sanal ağ hizmeti uç noktasını yoksayabilirsiniz. Portalda bu işlev kullanılamaz.
5. **Güvenilen Microsoft hizmetlerinin bu güvenlik duvarını atlamasına izin** vermek isteyip istemediğinizi belirtin. Ayrıntılar için bkz. [Güvenilen Microsoft Hizmetleri](#trusted-microsoft-services) . 
6. Ayarları kaydetmek için araç çubuğunda **Kaydet** ' i seçin. Onayın Portal bildirimlerinde gösterilmesi için birkaç dakika bekleyin.

    ![Ağı kaydet](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)

    > [!NOTE]
    > Belirli IP adresleri veya aralıklarına erişimi kısıtlamak için bkz. [belırlı IP adreslerinden veya aralıklardan erişime Izin ver](event-hubs-ip-filtering.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Resource Manager şablonu kullanma
Aşağıdaki örnek Kaynak Yöneticisi şablonu, var olan bir Event Hubs ad alanına bir sanal ağ kuralı ekler. Ağ kuralı için bir sanal ağdaki alt ağın KIMLIĞINI belirtir. 

KIMLIK, sanal ağ alt ağı için tam olarak nitelenmiş bir Kaynak Yöneticisi yoludur. Örneğin, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` bir sanal ağın varsayılan alt ağı için.

Sanal ağ veya güvenlik duvarları kuralları eklerken, değerini `defaultAction` olarak ayarlayın `Deny` .


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
- [Azure Event Hubs IP filtreleme][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
