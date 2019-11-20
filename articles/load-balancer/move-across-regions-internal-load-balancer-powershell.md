---
title: Azure iç Load Balancer Azure PowerShell kullanarak başka bir Azure bölgesine taşıma
description: Azure iç Load Balancer bir Azure bölgesinden diğerine taşımak için Azure Resource Manager şablonu kullanın Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 52a43dff5d2e740633675b71d5177d0df876d3cd
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092215"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Azure iç Load Balancer PowerShell kullanarak başka bir bölgeye taşıma

Mevcut iç yük dengeleyicinizi bir bölgeden diğerine taşımak istediğiniz çeşitli senaryolar vardır. Örneğin, test için aynı yapılandırmaya sahip bir iç yük dengeleyici oluşturmak isteyebilirsiniz. Ayrıca olağanüstü durum kurtarma planlamasının bir parçası olarak bir iç yük dengeleyiciyi başka bir bölgeye taşımak isteyebilirsiniz.

Azure iç yük dengeleyiciler bir bölgeden diğerine taşınamaz. Ancak, bir iç yük dengeleyicinin mevcut yapılandırmasını ve sanal ağını dışarı aktarmak için bir Azure Resource Manager şablonu kullanabilirsiniz.  Daha sonra, yük dengeleyiciyi ve sanal ağı bir şablona dışarı aktararak, parametreleri hedef bölgeyle eşleşecek şekilde değiştirerek ve sonra şablonları yeni bölgeye dağıtabilmeniz için kaynağı başka bir bölgede da oluşturabilirsiniz.  Kaynak Yöneticisi ve şablonlar hakkında daha fazla bilgi için bkz. [kaynak gruplarını şablonlara dışarı aktarma](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Önkoşullar

- Azure iç yük dengeleyicinin, taşımak istediğiniz Azure bölgesinde olduğundan emin olun.

- Azure iç yük dengeleyiciler bölgeler arasında taşınamaz.  Yeni Yük dengeleyiciyi hedef bölgedeki kaynaklarla ilişkilendirmeniz gerekir.

- Bir iç yük dengeleyici yapılandırmasını dışarı aktarmak ve başka bir bölgede iç yük dengeleyici oluşturmak için bir şablon dağıtmak için, ağ katılımcısı rolü veya daha yüksek bir sürümü gerekir.
   
- Kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belirler. Bu düzen, yük dengeleyiciler, ağ güvenlik grupları, sanal makineler ve sanal ağlar dahil değildir ancak bunlarla sınırlı değildir.

- Azure aboneliğinizin, kullanılan hedef bölgede iç yük dengeleyiciler oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı sağlamak için desteğe başvurun.

- Aboneliğinizin bu işleme yönelik yük dengeleyiciler eklenmesini desteklemek için yeterli kaynağa sahip olduğundan emin olun.  Bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Hazırlama ve taşıma
Aşağıdaki adımlarda, bir Kaynak Yöneticisi şablonu kullanarak taşıma için iç yük dengeleyiciyi hazırlama ve Azure PowerShell kullanarak iç yük dengeleyici yapılandırmasını hedef bölgeye taşıma işlemleri gösterilmektedir.  Bu işlemin bir parçası olarak, iç yük dengeleyicinin sanal ağ yapılandırması dahil olmalıdır ve iç yük dengeleyici taşınmadan önce önce yapılmalıdır.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Sanal ağ şablonunu dışarı aktarma ve Azure PowerShell dağıtma

1. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Hedef bölgeye taşımak istediğiniz sanal ağın kaynak KIMLIĞINI alın ve [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)kullanarak bir değişkene yerleştirin:

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Kaynak sanal ağı bir. JSON dosyasına dışarı aktarma [-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)komutunu yürütebileceğiniz dizine aktarın:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. İndirilen dosya, kaynağın öğesinden verildikten sonra adı alınacaktır.  **\<Resource-Group-name >. JSON** adlı komuttan aktarılmış dosyayı bulun ve seçtiğiniz bir düzenleyicide açın:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Sanal ağ adının parametresini düzenlemek için, kaynak sanal ağ adının **DefaultValue** özelliğini hedef sanal ağınızın adıyla değiştirin, adın tırnak içinde olduğundan emin olun:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  VNET 'in taşınacağı hedef bölgeyi düzenlemek için kaynaklar altındaki **Location** özelliğini değiştirin:

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
  
7. Bölge konum kodlarını almak için aşağıdaki komutu çalıştırarak [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) cmdlet 'ini Azure PowerShell kullanabilirsiniz:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Ayrıca, isterseniz  **\<kaynak-grup-adı >. JSON** dosyasındaki diğer parametreleri değiştirebilirsiniz ve gereksinimlerinize bağlı olarak isteğe bağlıdır:

    * **Adres alanı** -sanal ağın adres alanı, **Resources** > **AddressSpace** bölümü değiştirilerek ve içindeki  **\< addresspredüzeltmelerinin özelliği değiştirilerek kaydedilmeden önce değiştirilebilir. Resource-Group-name >. JSON** dosyası:

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

    * **Alt ağ** -alt ağ adı ve alt ağ adres alanı,  **\<Resource-Group-name >. JSON** dosyasının **alt ağlar** bölümü değiştirilerek değiştirilebilir veya öğesine eklenebilir. Alt ağın adı, **ad** özelliğini değiştirerek değiştirilebilir. Alt ağ adres alanı,  **\<Resource-Group-name >. JSON** dosyasındaki **addresspredüzeltmesini** özelliğini değiştirerek değiştirilebilir:

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

         **Resource-Group-name >. json dosyasında, adres ön ekini değiştirmek için, yukarıda listelenen bölüm ve aşağıda listelenen tür bölümü olmak üzere iki yerde düzenlenmelidir. \<**  **Addresspredüzeltmesini** özelliğini, yukarıdaki bir ile eşleşecek şekilde değiştirin:

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

9.  **Resource-Group-name >. json dosyasını kaydedin. \<**

10. Hedef sanal ağın [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) kullanılarak dağıtılması için hedef bölgede bir kaynak grubu oluşturun
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. **Düzenlenmiş\<kaynak-grup adı >. JSON** dosyasını, önceki adımda oluşturulan kaynak grubuna [New-azresourcegroupdeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)kullanarak dağıtın:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Hedef bölgede kaynakların oluşturulduğunu doğrulamak için [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) ve [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)kullanın:
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>İç yük dengeleyici şablonunu dışarı aktarma ve Azure PowerShell dağıtma

1. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hedef bölgeye taşımak istediğiniz iç yük dengeleyicinin kaynak KIMLIĞINI alın ve [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)kullanarak bir değişkene yerleştirin:

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Kaynak iç yük dengeleyici yapılandırmasını bir. JSON dosyasına dışarı aktarma [-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)komutunu çalıştırdığınız dizine aktarın:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. İndirilen dosya, kaynağın öğesinden verildikten sonra adı alınacaktır.  **\<Resource-Group-name >. JSON** adlı komuttan aktarılmış dosyayı bulun ve seçtiğiniz bir düzenleyicide açın:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. İç yük dengeleyici adının parametresini düzenlemek için, kaynak iç yük dengeleyici adının **DefaultValue** özelliğini hedef iç yük dengeleyicinizin adına değiştirin, adın tırnak içinde olduğundan emin olun:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Yukarıda taşınan hedef sanal ağın değerini düzenlemek için öncelikle kaynak kimliğini edinmeniz ve sonra  **\<kaynak-grup-adı >. JSON** dosyasına kopyalamanız ve yapıştırmanız gerekir.  KIMLIĞI almak için [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)kullanın:
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Kaynak KIMLIĞINI göstermek için değişkeni yazın ve ENTER tuşuna basın.  KIMLIK yolunu vurgulayın ve panoya kopyalayın:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  Resource-Group **-Name >. json dosyasında, kaynak kimliği ' ni hedef sanal ağ kimliği için ikinci parametreye DefaultValue 'ın yerine, değişkenin tırnak içine \<** yapıştırın:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. İç yük dengeleyici yapılandırmasının taşınacağı hedef bölgeyi düzenlemek için,  **\<Resource-Group-name >. JSON** dosyasındaki **Resources** altındaki **Location** özelliğini değiştirin:

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

11. Bölge konum kodlarını almak için aşağıdaki komutu çalıştırarak [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) cmdlet 'ini Azure PowerShell kullanabilirsiniz:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Ayrıca, isterseniz şablondaki diğer parametreleri değiştirebilir ve gereksinimlerinize bağlı olarak isteğe bağlıdır:
    
    * **SKU** - >  **kaynak-grup adı >. json ' daki SKU adı özelliğini değiştirerek, yapılandırmadaki iç yük dengeleyicinin SKU 'sunu temel veya temel olarak standart olarak değiştirebilirsiniz. \<** dosya:

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
      Temel ve standart SKU yük dengeleyiciler arasındaki farklar hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer genel bakış](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Yük Dengeleme kuralları** -  **\<kaynak-grup-adı >. JSON** dosyasının **loadBalancingRules** bölümüne girdi ekleyerek veya kaldırarak yapılandırmaya Yük Dengeleme kuralları ekleyebilir veya kaldırabilirsiniz:

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
       Yük Dengeleme kuralları hakkında daha fazla bilgi için bkz. [Azure Load Balancer nedir?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Yoklamalar** -  **\<Resource-Group-name >. JSON** dosyasının **yoklamalar** bölümüne girdi ekleyerek veya kaldırarak, yapılandırmadaki yük dengeleyici için bir araştırma ekleyebilir veya kaldırabilirsiniz:

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
       Azure Load Balancer sistem durumu araştırmaları hakkında daha fazla bilgi için bkz. [Load Balancer sistem durumu araştırmaları](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Gelen NAT kuralları** -  **\<Resource-Group-name >. JSON** dosyasının **ınboundnatrules** bölümüne girdi ekleyerek veya kaldırarak yük dengeleyici için gelen NAT kuralları ekleyebilir veya kaldırabilirsiniz:

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
        Bir gelen NAT kuralı ekleme veya kaldırma işlemini gerçekleştirmek için, kural var olmalıdır veya  **\<kaynak-grup-adı >. JSON** dosyasının sonunda bir **tür** özelliği olarak kaldırılmalıdır:

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
        Gelen NAT kuralları hakkında daha fazla bilgi için bkz. [Azure Load Balancer nedir?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
    
13. **Resource-Group-name >. json dosyasını kaydedin. \<**
    
10. Hedef bölgede, [Yeni-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)kullanılarak dağıtılacak hedef iç yük dengeleyicinin dağıtılacağı bir kaynak grubu oluşturun. Yukarıdaki mevcut kaynak grubu, bu işlemin bir parçası olarak da kullanılabilir:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. **Düzenlenmiş\<kaynak-grup adı >. JSON** dosyasını, önceki adımda oluşturulan kaynak grubuna [New-azresourcegroupdeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)kullanarak dağıtın:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Hedef bölgede kaynakların oluşturulduğunu doğrulamak için [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) ve [Get-azloadbalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)komutunu kullanın:
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>At 

Dağıtımdan sonra, hedefteki sanal ağ ve yük dengeleyiciyi baştan başlatmak veya atmak istiyorsanız hedefte oluşturulan kaynak grubunu silin ve taşınan sanal ağ ile yük dengeleyici silinir.  Kaynak grubunu kaldırmak için [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)komutunu kullanın:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Temizleme

Değişiklikleri uygulamak ve NSG 'nin taşınmasını tamamlamak için, kaynak NSG veya kaynak grubunu silin, [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) veya [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) ve [Remove-azloadbalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0) komutunu kullanın

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure iç yük dengeleyiciyi bir bölgeden diğerine taşımış ve kaynak kaynakları temizledi.  Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz:


- [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure VM’lerini başka bir bölgeye taşıma](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
