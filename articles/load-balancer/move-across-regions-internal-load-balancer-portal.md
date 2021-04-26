---
title: Azure iç Load Balancer Azure portal kullanarak başka bir Azure bölgesine taşıma
description: Azure portal kullanarak Azure iç Load Balancer bir Azure bölgesinden diğerine taşımak için Azure Resource Manager şablonu kullanın
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 68a2cb6926cb41956711a9e3c15d21c250d27f0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94698506"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Azure iç Load Balancer Azure portal kullanarak başka bir bölgeye taşıma

Mevcut iç yük dengeleyicinizi bir bölgeden diğerine taşımak istediğiniz çeşitli senaryolar vardır. Örneğin, test için aynı yapılandırmaya sahip bir iç yük dengeleyici oluşturmak isteyebilirsiniz. Ayrıca olağanüstü durum kurtarma planlamasının bir parçası olarak bir iç yük dengeleyiciyi başka bir bölgeye taşımak isteyebilirsiniz.

Azure iç yük dengeleyiciler bir bölgeden diğerine taşınamaz. Ancak, bir iç yük dengeleyicinin mevcut yapılandırmasını ve sanal ağını dışarı aktarmak için bir Azure Resource Manager şablonu kullanabilirsiniz.  Daha sonra, yük dengeleyiciyi ve sanal ağı bir şablona dışarı aktararak, parametreleri hedef bölgeyle eşleşecek şekilde değiştirerek ve sonra şablonları yeni bölgeye dağıtabilmeniz için kaynağı başka bir bölgede da oluşturabilirsiniz.  Kaynak Yöneticisi ve şablonlar hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Resource Manager şablonları oluşturma ve dağıtma](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Önkoşullar

- Azure iç yük dengeleyicinin, taşımak istediğiniz Azure bölgesinde olduğundan emin olun.

- Azure iç yük dengeleyiciler bölgeler arasında taşınamaz.  Yeni Yük dengeleyiciyi hedef bölgedeki kaynaklarla ilişkilendirmeniz gerekir.

- Bir iç yük dengeleyici yapılandırmasını dışarı aktarmak ve başka bir bölgede iç yük dengeleyici oluşturmak için bir şablon dağıtmak için, ağ katılımcısı rolü veya daha yüksek bir sürümü gerekir.

- Kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belirler. Bu düzen, yük dengeleyiciler, ağ güvenlik grupları, sanal makineler ve sanal ağlar dahil değildir ancak bunlarla sınırlı değildir.

- Azure aboneliğinizin, kullanılan hedef bölgede iç yük dengeleyiciler oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı sağlamak için desteğe başvurun.

- Aboneliğinizin bu işleme yönelik yük dengeleyiciler eklenmesini desteklemek için yeterli kaynağa sahip olduğundan emin olun.  Bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Hazırlama ve taşıma
Aşağıdaki adımlarda, bir Kaynak Yöneticisi şablonu kullanarak taşıma için iç yük dengeleyiciyi hazırlama ve Azure portal kullanarak iç yük dengeleyici yapılandırmasını hedef bölgeye taşıma işlemleri gösterilmektedir.  Bu işlemin bir parçası olarak, iç yük dengeleyicinin sanal ağ yapılandırması dahil olmalıdır ve iç yük dengeleyici taşınmadan önce önce yapılmalıdır.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Sanal ağ şablonunu dışarı aktarın ve Azure portal dağıtın

1. [Azure Portal](https://portal.azure.com)  >  **kaynak gruplarında** oturum açın.
2. Kaynak sanal ağı içeren kaynak grubunu bulun ve üzerine tıklayın.
3. > **ayarları**  >  **dışarı aktarma şablonu**' nu seçin.
4. **Şablonu dışarı aktar** dikey penceresinde **Dağıt** ' ı seçin.
5.   >  Çevrimiçi düzenleyicide dosya **parameters.js** açmak için şablon **düzenleme parametreleri** ' ne tıklayın.
6. Sanal ağ adının parametresini düzenlemek için, **Parametreler** altındaki **değer** özelliğini değiştirin:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Düzenleyicideki kaynak sanal ağ adı değerini hedef VNET için seçtiğiniz bir adla değiştirin. Adı tırnak içine aldığınızdan emin olun.

8. Düzenleyicide **Kaydet** ' e tıklayın.

9.   >  Çevrimiçi düzenleyicide dosya **template.js** açmak için şablon **düzenleme** şablonu ' na tıklayın.

10. VNET 'in taşınacağı hedef bölgeyi düzenlemek için kaynaklar altındaki **Location** özelliğini değiştirin:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

11. Bölge konum kodlarını almak için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).  Bir bölgenin kodu, alanı olmayan bölge adıdır, **Orta ABD**  =  **merkezileştirme**.

12. Ayrıca, varsa dosya **template.js** diğer parametreleri de değiştirebilir ve gereksinimlerinize bağlı olarak isteğe bağlıdır:

    * **Adres alanı** : **kaynak**  >  **AddressSpace** bölümü değiştirilerek ve dosyadaki **template.js** **addresspredüzeltmelerinin** özelliği değiştirilerek kaydedilmeden önce VNET 'in adres alanı değiştirilebilir:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Alt ağ** -alt ağ adı ve alt ağ adres alanı, **template.js** dosyadaki **alt ağlar** bölümü değiştirilerek değiştirilebilir veya öğesine eklenebilir. Alt ağın adı, **ad** özelliğini değiştirerek değiştirilebilir. Alt ağ adres alanı, dosyadaki **template.js** **addresspredüzeltmesini** özelliğini değiştirerek değiştirilebilir:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         Dosyadaki **template.js** , adres ön ekini değiştirmek için, yukarıda listelenen bölüm ve aşağıda listelenen **tür** bölümü olmak üzere iki yerde düzenlenmelidir.  **Addresspredüzeltmesini** özelliğini, yukarıdaki bir ile eşleşecek şekilde değiştirin:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

13. Çevrimiçi düzenleyicide **Kaydet** ' e tıklayın.

14.   >  Hedef VNET 'in dağıtılacağı aboneliği seçmek için temel bilgiler **aboneliği** ' ne tıklayın.

15.   >  Hedef VNET 'in dağıtılacağı kaynak grubunu seçmek için temel bilgiler **kaynak grubu** ' na tıklayın.  Hedef sanal ağ için yeni bir kaynak grubu oluşturmak için **Yeni oluştur** ' a tıklayabilirsiniz.  Adın mevcut VNET 'in kaynak kaynak grubuyla aynı olmadığından emin olun.

16. **Temel bilgilerin**  >  **,** sanal ağın dağıtılmasını istediğiniz hedef konuma ayarlandığını doğrulayın.

17. **Ayarların** , yukarıdaki parametreler düzenleyicisinde girdiğiniz adla eşleştiğini doğrulayın.

18. **Hüküm ve koşullar** altındaki kutuyu işaretleyin.

19. Hedef sanal ağı dağıtmak için **satın al** düğmesine tıklayın.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>İç yük dengeleyici şablonunu dışarı aktarma ve Azure PowerShell dağıtma

1. [Azure Portal](https://portal.azure.com)  >  **kaynak gruplarında** oturum açın.
2. Kaynak iç yük dengeleyiciyi içeren kaynak grubunu bulun ve üzerine tıklayın.
3. > **ayarları**  >  **dışarı aktarma şablonu**' nu seçin.
4. **Şablonu dışarı aktar** dikey penceresinde **Dağıt** ' ı seçin.
5.   >  Çevrimiçi düzenleyicide dosya **parameters.js** açmak için şablon **düzenleme parametreleri** ' ne tıklayın.

6. İç yük dengeleyici adının parametresini düzenlemek için, kaynak iç yük dengeleyici adının **DefaultValue** özelliğini hedef iç yük dengeleyicinizin adına değiştirin, adın tırnak içinde olduğundan emin olun:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Yukarıda taşınan hedef sanal ağın değerini düzenlemek için öncelikle kaynak KIMLIĞINI edinmeniz ve sonra dosyayı kopyalayıp **parameters.js** dosyaya yapıştırmanız gerekir. KIMLIĞI almak için:

    1. [](https://portal.azure.com)  >  Başka bir tarayıcı sekmesi veya penceresinde Azure Portal **kaynak gruplarında** oturum açın.
    2. Yukarıdaki adımlardan taşınan sanal ağı içeren hedef kaynak grubunu bulun ve üzerine tıklayın.
    3. > **ayarları**  >  **özelliklerini** seçin.
    4. Sağ taraftaki dikey pencerede, **kaynak kimliğini** vurgulayın ve panoya kopyalayın.  Alternatif olarak, **kaynak kimliği** yolunun sağ tarafındaki **Panoya Kopyala** düğmesine tıklayabilirsiniz.
    5. Kaynak KIMLIĞI ' ni, diğer tarayıcı penceresinde veya sekmesinde bulunan **parametreleri Düzenle** Düzenleyicisi ' ne **DefaultValue** özelliğine yapıştırın:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Çevrimiçi düzenleyicide **Kaydet** ' e tıklayın.

7.   >  Çevrimiçi düzenleyicide dosya **template.js** açmak için şablon **düzenleme** şablonu ' na tıklayın.
8. İç yük dengeleyici yapılandırmasının taşınacağı hedef bölgeyi düzenlemek için, dosyadaki **template.js** içindeki **kaynaklar** altında **Location** özelliğini değiştirin:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  Bölge konum kodlarını almak için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).  Bir bölgenin kodu, alanı olmayan bölge adıdır, **Orta ABD**  =  **merkezileştirme**.

10. Ayrıca, isterseniz şablondaki diğer parametreleri değiştirebilir ve gereksinimlerinize bağlı olarak isteğe bağlıdır:

    * **SKU** -   >  dosyadaki **template.js** SKU **adı** özelliğini değiştirerek, yapılandırmadaki iç yük dengeleyicinin SKU 'sunu standart iken temel veya temel olarak değiştirebilirsiniz:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Temel ve standart SKU yük dengeleyiciler arasındaki farklar hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer genel bakış](./load-balancer-overview.md)

    * **Yük Dengeleme kuralları** -dosyadaki **template.js** **loadBalancingRules** bölümüne girdi ekleyerek veya kaldırarak, yapılandırmaya Yük Dengeleme kuralları ekleyebilir veya kaldırabilirsiniz:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Yük Dengeleme kuralları hakkında daha fazla bilgi için bkz. [Azure Load Balancer nedir?](./load-balancer-overview.md)

    * **Yoklamalar** -dosyadaki **template.js** **yoklamalar** bölümüne girdi ekleyerek veya kaldırarak, yapılandırmadaki yük dengeleyici için bir araştırma ekleyebilir veya kaldırabilirsiniz:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Azure Load Balancer sistem durumu araştırmaları hakkında daha fazla bilgi için bkz. [Load Balancer sistem durumu araştırmaları](./load-balancer-custom-probe-overview.md)

    * **Gelen NAT kuralları** -dosyadaki **template.js** **ınboundnatrules** bölümüne girdi ekleyerek veya kaldırarak yük dengeleyici için gelen NAT kuralları ekleyebilir veya kaldırabilirsiniz:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Bir gelen NAT kuralı ekleme veya kaldırma işlemini gerçekleştirmek için, kuralın mevcut olması veya **template.js** özellik olarak bir **tür** özelliği olarak kaldırılması gerekir:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Gelen NAT kuralları hakkında daha fazla bilgi için bkz. [Azure Load Balancer nedir?](./load-balancer-overview.md)

12. Çevrimiçi düzenleyicide **Kaydet** ' e tıklayın.

13.   >  Hedef iç yük dengeleyicinin dağıtılacağı aboneliği seçmek için temel bilgiler **aboneliği** ' ne tıklayın.

15.   >  Hedef yük dengeleyicinin dağıtılacağı kaynak grubunu seçmek için temel bilgiler **kaynak grubu** ' na tıklayın.  **Yeni oluştur** ' a tıklayarak hedef iç yük dengeleyici için yeni bir kaynak grubu oluşturabilir veya sanal ağ için yukarıda oluşturulan mevcut kaynak grubunu seçebilirsiniz.  Adın mevcut kaynak iç yük dengeleyicinin kaynak kaynak grubuyla aynı olmadığından emin olun.

16. **Temel**  >  aldığı **konumun** , iç yük dengeleyicinin dağıtılması için istediğiniz hedef konuma ayarlandığını doğrulayın.

17. **Ayarların** , yukarıdaki parametreler düzenleyicisinde girdiğiniz adla eşleştiğini doğrulayın.  Yapılandırma içindeki tüm sanal ağlar için kaynak kimliklerinin doldurulduğunu doğrulayın.

18. **Hüküm ve koşullar** altındaki kutuyu işaretleyin.

19. Hedef sanal ağı dağıtmak için **satın al** düğmesine tıklayın.

## <a name="discard"></a>Vazgeç

Hedef sanal ağı ve iç yük dengeleyiciyi atmak istiyorsanız hedef sanal ağı ve iç yük dengeleyiciyi içeren kaynak grubunu silin.  Bunu yapmak için, portalda panodaki kaynak grubunu seçin ve genel bakış sayfasının en üstünde **Sil** ' i seçin.

## <a name="clean-up"></a>Temizleme

Değişiklikleri uygulamak ve sanal ağ ile iç yük dengeleyiciyi tamamlamak için kaynak sanal ağı ve iç yük dengeleyiciyi veya kaynak grubunu silin. Bunu yapmak için, portalda panodaki sanal ağı ve iç yük dengeleyiciyi veya kaynak grubunu seçin ve her sayfanın en üstünde **Sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure iç yük dengeleyiciyi bir bölgeden diğerine taşımış ve kaynak kaynakları temizledi.  Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz:


- [Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM’lerini başka bir bölgeye taşıma](../site-recovery/azure-to-azure-tutorial-migrate.md)