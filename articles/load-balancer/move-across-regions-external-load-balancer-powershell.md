---
title: Azure dış Load Balancer Azure PowerShell kullanarak başka bir Azure bölgesine taşıma
description: Azure PowerShell kullanarak Azure dış Load Balancer bir Azure bölgesinden diğerine taşımak için Azure Resource Manager şablonu kullanın.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 94cb46536bcf029a9e71a7238772ccc7b186b1dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500283"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Azure dış Load Balancer Azure PowerShell kullanarak başka bir bölgeye taşıma

Mevcut dış yük dengeleyicinizi bir bölgeden diğerine taşımak istediğiniz çeşitli senaryolar vardır. Örneğin, test için aynı yapılandırmaya sahip bir dış yük dengeleyici oluşturmak isteyebilirsiniz. Ayrıca, olağanüstü durum kurtarma planlamasının bir parçası olarak bir dış yük dengeleyiciyi başka bir bölgeye taşımak isteyebilirsiniz.

Azure dış yük dengeleyiciler bir bölgeden diğerine taşınamaz. Ancak, bir dış yük dengeleyicinin mevcut yapılandırmasını ve genel IP 'sini dışarı aktarmak için bir Azure Resource Manager şablonu kullanabilirsiniz.  Daha sonra, yük dengeleyiciyi ve genel IP 'yi bir şablona dışarı aktararak ve parametreleri hedef bölgeyle eşleşecek şekilde değiştirerek ve sonra şablonları yeni bölgeye dağıtabilmeniz için kaynağı başka bir bölgede da oluşturabilirsiniz.  Kaynak Yöneticisi ve şablonlar hakkında daha fazla bilgi için bkz. [kaynak gruplarını şablonlara dışarı aktarma](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Önkoşullar

- Azure dış yük dengeleyicinin, taşımak istediğiniz Azure bölgesinde olduğundan emin olun.

- Azure dış yük dengeleyiciler bölgeler arasında taşınamaz.  Yeni Yük dengeleyiciyi hedef bölgedeki kaynaklarla ilişkilendirmeniz gerekir.

- Bir dış yük dengeleyici yapılandırmasını dışarı aktarmak ve başka bir bölgede dış yük dengeleyici oluşturmak üzere bir şablon dağıtmak için, ağ katılımcısı rolü veya daha yüksek bir sürüme sahip olmanız gerekir.
   
- Kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belirler. Bu düzen, yük dengeleyiciler, ağ güvenlik grupları, genel IP 'Ler ve sanal ağlar dahil değildir ancak bunlarla sınırlı değildir.

- Azure aboneliğinizin, kullanılan hedef bölgede dış yük dengeleyiciler oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı sağlamak için desteğe başvurun.

- Aboneliğinizin bu işleme yönelik yük dengeleyiciler eklenmesini desteklemek için yeterli kaynağa sahip olduğundan emin olun.  Bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Hazırlama ve taşıma
Aşağıdaki adımlarda, bir Kaynak Yöneticisi şablonu kullanarak taşıma için dış yük dengeleyiciyi hazırlama ve Azure PowerShell kullanarak dış yük dengeleyici yapılandırmasını hedef bölgeye taşıma işlemleri gösterilmektedir.  Bu işlemin bir parçası olarak dış yük dengeleyicinin genel IP yapılandırmasının dahil olması gerekir ve dış yük dengeleyiciyi taşımadan önce önce yapılması gerekir.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Genel IP şablonunu dışarı aktarma ve Azure PowerShell dağıtma

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Hedef bölgeye taşımak istediğiniz genel IP 'nin kaynak KIMLIĞINI alın ve [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress)kullanarak bir değişkene yerleştirin:

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Kaynak ortak IP 'yi bir. JSON dosyasına dışarı aktarma [-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)komutunu çalıştırdığınız dizine aktarın:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. İndirilen dosya, kaynağın öğesinden verildikten sonra adı alınacaktır.  **\<resource-group-name> . JSON** adlı komuttan aktarılmış dosyayı bulun ve seçtiğiniz bir düzenleyicide açın:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Genel IP adının parametresini düzenlemek için kaynak genel IP adının **DefaultValue** özelliğini hedef genel IP 'nizin adı olarak değiştirin, adın tırnak içinde olduğundan emin olun:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Genel IP 'nin taşınacağı hedef bölgeyi düzenlemek için kaynaklar altındaki **Location** özelliğini değiştirin:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Bölge konum kodlarını almak için aşağıdaki komutu çalıştırarak [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet 'ini Azure PowerShell kullanabilirsiniz:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Ayrıca, isterseniz şablondaki diğer parametreleri değiştirebilir ve gereksinimlerinize bağlı olarak isteğe bağlıdır:

    * **SKU** -   >  **\<resource-group-name> . JSON** dosyasındaki SKU **adı** özelliğini değiştirerek, yapılandırmadaki genel IP 'yi standart iken Basic veya Basic 'e dönüştürebilirsiniz:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Temel ve standart SKU genel IP 'leri arasındaki farklılıklar hakkında daha fazla bilgi için bkz. [genel IP adresi oluşturma, değiştirme veya silme](../virtual-network/virtual-network-public-ip-address.md).

    * **Ortak IP ayırma yöntemi** ve **boşta kalma zaman aşımı** - **publicıpallocationmethod** özelliğini **dinamik** veya **statik** olarak **dinamik** olarak değiştirerek şablonda bu seçeneklerin her ikisini de değiştirebilirsiniz  . Boşta **kalma zaman aşımı özelliği,** istediğiniz tutara göre değiştirilebilir.  Varsayılan değer **4**' dir:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Ayırma yöntemleri ve boşta kalma zaman aşımı değerleri hakkında daha fazla bilgi için bkz. [genel IP adresi oluşturma, değiştirme veya silme](../virtual-network/virtual-network-public-ip-address.md).


9. **\<resource-group-name> . JSON** dosyasını kaydedin.

10. Hedef bölgede, hedef genel IP 'nin [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)kullanılarak dağıtılması için bir kaynak grubu oluşturun.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Düzenlenen **\<resource-group-name> . JSON** dosyasını önceki adımda oluşturulan kaynak grubuna [New-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)kullanarak dağıtın:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Hedef bölgede kaynakların oluşturulduğunu doğrulamak için [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) ve [Get-azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress)komutunu kullanın:
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Dış yük dengeleyici şablonunu dışarı aktarma ve Azure PowerShell dağıtma

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hedef bölgeye taşımak istediğiniz dış yük dengeleyicinin kaynak KIMLIĞINI alın ve [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer)kullanarak bir değişkene yerleştirin:

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Kaynak dış yük dengeleyici yapılandırmasını bir. JSON dosyasına dışarı aktarma [-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)komutunu çalıştırdığınız dizine aktarın:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. İndirilen dosya, kaynağın öğesinden verildikten sonra adı alınacaktır.  **\<resource-group-name> . JSON** adlı komuttan aktarılmış dosyayı bulun ve seçtiğiniz bir düzenleyicide açın:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Dış yük dengeleyici adının parametresini düzenlemek için, kaynak dış yük dengeleyici adının **DefaultValue** özelliğini hedef dış yük dengeleyicinizin adına değiştirin, adın tırnak içinde olduğundan emin olun:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  Yukarıda taşınan hedef genel IP 'nin değerini düzenlemek için, önce kaynak KIMLIĞINI edinmeniz ve sonra **\<resource-group-name> . JSON** dosyasına kopyalamanız ve yapıştırmanız gerekir.  KIMLIĞI almak için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress)komutunu kullanın:

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Kaynak KIMLIĞINI göstermek için değişkeni yazın ve ENTER tuşuna basın.  KIMLIK yolunu vurgulayın ve panoya kopyalayın:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  **\<resource-group-name> . JSON** dosyasında, genel IP dış kimliği Için ikinci parametreye **DEFAULTVALUE** yerine değişkenden **kaynak kimliği** ' ni yapıştırın, yolu tırnak içine aldığınızdan emin olun:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  Yük Dengeleyici için giden NAT ve giden kurallarını yapılandırdıysanız, giden genel IP 'nin dış KIMLIĞI için bu dosyada üçüncü bir giriş bulunur.  Giden genel IP 'nin KIMLIĞINI edinmek ve bu girişi **\<resource-group-name> . JSON** dosyasına yapıştırmak için **hedef bölgede** yukarıdaki adımları yineleyin:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. Dış yük dengeleyici yapılandırmasının taşınacağı hedef bölgeyi düzenlemek için, **\<resource-group-name> . JSON** dosyasındaki **kaynaklar** altındaki **Location** özelliğini değiştirin:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Bölge konum kodlarını almak için aşağıdaki komutu çalıştırarak [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet 'ini Azure PowerShell kullanabilirsiniz:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Ayrıca, isterseniz şablondaki diğer parametreleri değiştirebilir ve gereksinimlerinize bağlı olarak isteğe bağlıdır:
    
    * **SKU** -   >  **\<resource-group-name> . JSON** dosyasındaki SKU **adı** özelliğini değiştirerek, yapılandırmadaki dış yük dengeleyicinin SKU 'sunu standart iken temel veya temel olarak değiştirebilirsiniz:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Temel ve standart SKU yük dengeleyiciler arasındaki farklar hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer genel bakış](./load-balancer-overview.md)

    * **Yük Dengeleme kuralları** - **\<resource-group-name> . JSON** dosyasının **loadBalancingRules** bölümüne girdi ekleyerek veya kaldırarak, yapılandırmaya Yük Dengeleme kuralları ekleyebilir veya kaldırabilirsiniz:

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

    * **Yoklamalar** : **\<resource-group-name> . JSON** dosyasının **yoklamalar** bölümüne girdi ekleyerek veya kaldırarak, yapılandırmadaki yük dengeleyici için bir araştırma ekleyebilir veya kaldırabilirsiniz:

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

    * **Gelen NAT kuralları** - **\<resource-group-name> . JSON** dosyasının **ınboundnatrules** bölümüne girdi ekleyerek veya kaldırarak yük dengeleyici için gelen NAT kuralları ekleyebilir veya kaldırabilirsiniz:

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
        Bir gelen NAT kuralı ekleme veya kaldırma işlemini gerçekleştirmek için, kural var olmalıdır veya **\<resource-group-name> . JSON** dosyasının sonunda bir **tür** özelliği olarak kaldırılmalıdır:

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

    * **Giden kuralları** - **\<resource-group-name> . JSON** dosyasındaki **outboundrules** özelliğini düzenleyerek, yapılandırmada giden kuralları ekleyebilir veya kaldırabilirsiniz:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Giden kuralları hakkında daha fazla bilgi için bkz. [Load Balancer giden kuralları](./load-balancer-outbound-connections.md#outboundrules)

13. **\<resource-group-name> . JSON** dosyasını kaydedin.
    
10. Hedef dış yük dengeleyicinin [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)kullanılarak dağıtılması için hedef bölgede bir kaynak grubu oluşturun veya bir kaynak grubu oluşturun. Yukarıdaki mevcut kaynak grubu, bu işlemin bir parçası olarak da kullanılabilir:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Düzenlenen **\<resource-group-name> . JSON** dosyasını önceki adımda oluşturulan kaynak grubuna [New-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)kullanarak dağıtın:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Hedef bölgede kaynakların oluşturulduğunu doğrulamak için [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) ve [Get-azloadbalancer](/powershell/module/az.network/get-azloadbalancer)komutunu kullanın:
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Vazgeç 

Dağıtımdan sonra, hedefteki genel IP ve yük dengeleyiciyi baştan başlatmak veya atmak istiyorsanız hedefte oluşturulan kaynak grubunu silin ve taşınan ortak IP ve yük dengeleyici silinir.  Kaynak grubunu kaldırmak için [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)komutunu kullanın:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Temizleme

Değişiklikleri uygulamak ve NSG 'nin taşınmasını tamamlamak için, kaynak NSG veya kaynak grubunu silin, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) veya [Remove-azpublicıpaddress](/powershell/module/az.network/remove-azpublicipaddress) ve [Remove-azloadbalancer](/powershell/module/az.network/remove-azloadbalancer) komutunu kullanın

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure ağ güvenlik grubunu bir bölgeden diğerine taşıdı ve kaynak kaynakları temizledi.  Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz:


- [Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM’lerini başka bir bölgeye taşıma](../site-recovery/azure-to-azure-tutorial-migrate.md)