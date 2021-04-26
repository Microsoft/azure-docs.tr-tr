---
title: Azure PowerShell kullanarak bir Azure sanal ağını başka bir Azure bölgesine taşıma
description: Bir Azure sanal ağını, bir Azure bölgesinden diğerine, Kaynak Yöneticisi şablonu kullanarak ve Azure PowerShell taşıyın.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fa89a458b23d18bc6fddfca9cf6d9f2a16f91669
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934924"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Azure PowerShell kullanarak bir Azure sanal ağını başka bir bölgeye taşıma

Mevcut bir Azure sanal ağını bir bölgeden diğerine taşımaya yönelik çeşitli senaryolar vardır. Örneğin, var olan sanal ağınız ile test ve kullanılabilirlik için aynı yapılandırmaya sahip bir sanal ağ oluşturmak isteyebilirsiniz. Ya da bir üretim sanal ağını olağanüstü durum kurtarma planlamanızın bir parçası olarak başka bir bölgeye taşımak isteyebilirsiniz.

Sanal ağı başka bir bölgeye taşımayı gerçekleştirmek için bir Azure Resource Manager şablonu kullanabilirsiniz. Bunu, sanal ağı bir şablona vererek, parametreleri hedef bölgeyle eşleşecek şekilde değiştirerek ve sonra şablonu yeni bölgeye dağıtarak yapabilirsiniz. Kaynak Yöneticisi şablonları hakkında daha fazla bilgi için bkz. [kaynak gruplarını şablonlara dışarı aktarma](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Önkoşullar

- Sanal ağınızın, taşımak istediğiniz Azure bölgesinde olduğundan emin olun.

- Bir sanal ağı dışa aktarmak ve başka bir bölgede bir sanal ağ oluşturmak üzere bir şablon dağıtmak için, ağ katılımcısı rolüne veya daha üst bir sürüme sahip olmanız gerekir.

- Sanal ağ eşlemeleri yeniden oluşturulmayacak ve şablonda hala mevcutsa başarısız olur. Şablonu dışarı aktarmadan önce tüm sanal ağ eşlerini kaldırmanız gerekir. Daha sonra sanal ağ taşıdıktan sonra yeniden kurabilirsiniz.
    
- Kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belirler. Bu düzen, yük dengeleyiciler, ağ güvenlik grupları (NSG 'Ler) ve genel IP 'Leri içerir ancak bunlarla sınırlı değildir.

- Azure aboneliğinizin hedef bölgede sanal ağlar oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı etkinleştirmek için desteğe başvurun.

- Aboneliğinizin bu işleme yönelik sanal ağların eklenmesini desteklemek için yeterli kaynağa sahip olduğundan emin olun. Daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-for-the-move"></a>Taşıma için hazırlanma
Bu bölümde, Kaynak Yöneticisi şablonu kullanarak taşıma için sanal ağı hazırlarsınız. Daha sonra Azure PowerShell komutlarını kullanarak sanal ağı hedef bölgeye taşıyabilirsiniz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sanal ağı dışa aktarmak ve PowerShell 'i kullanarak hedef sanal ağı dağıtmak için aşağıdakileri yapın:

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Hedef bölgeye taşımak istediğiniz sanal ağın kaynak KIMLIĞINI alın ve sonra [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)kullanarak bir değişkene yerleştirin:

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Kaynak sanal ağı, [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)komutunu çalıştırdığınız dizindeki bir. JSON dosyasına dışarı aktarın:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. İndirilen dosya, kaynağın verildiği kaynak grubuyla aynı ada sahiptir. Komutuyla verdiğiniz *\<resource-group-name> . JSON* dosyasını bulun ve ardından düzenleyicide açın:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Sanal ağ adının parametresini düzenlemek için, kaynak sanal ağ adının **DefaultValue** özelliğini hedef sanal ağınızın adıyla değiştirin. Adı tırnak işaretleri içine aldığınızdan emin olun.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Sanal ağın taşınacağı hedef bölgeyi düzenlemek için kaynaklar altındaki **Location** özelliğini değiştirin:

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
  
1. Bölge konum kodlarını almak için aşağıdaki komutu çalıştırarak [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet 'ini Azure PowerShell kullanabilirsiniz:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. Seçim Gereksinimlerinize bağlı olarak, *\<resource-group-name> . JSON* dosyasındaki diğer parametreleri de değiştirebilirsiniz:

    * **Adres alanı**: dosyayı kaydetmeden önce, **Resources**  >  **AddressSpace** bölümünü değiştirerek ve **addresspredüzeltmelerinin** özelliğini değiştirerek sanal ağın adres alanını değiştirebilirsiniz:

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

    * **Alt ağ**: dosyanın **alt ağlar** bölümünü değiştirerek alt ağ adını ve alt ağ adres alanını değiştirebilir veya ekleyebilirsiniz. **Ad** özelliğini değiştirerek alt ağın adını değiştirebilirsiniz. Ve **Addresspredüzeltmesini** özelliğini değiştirerek alt ağ adres alanını değiştirebilirsiniz:

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

        Adres ön ekini değiştirmek için, dosyayı iki yerde düzenleyin: önceki bölümdeki kodda ve aşağıdaki kodun **tür** bölümünde. Aşağıdaki kodda **Addresspredüzeltmesini** özelliğini, önceki bölümdeki koddaki **addresspredüzeltmesini** özelliğiyle eşleşecek şekilde değiştirin.

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

1. *\<resource-group-name> . JSON* dosyasını kaydedin.

1. [Yeni-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)kullanılarak dağıtılacak hedef sanal ağın hedef bölgede bir kaynak grubu oluşturun:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Düzenlenecek *\<resource-group-name> . JSON* dosyasını, önceki adımda oluşturduğunuz kaynak grubuna [New-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)kullanarak dağıtın:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Kaynakların hedef bölgede oluşturulduğunu doğrulamak için [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) ve [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)kullanın:
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Sanal ağı veya kaynak grubunu sil 

Sanal ağı dağıttıktan sonra, hedef bölgedeki sanal ağı baştan başlatmak veya atmak için, hedef bölgede oluşturduğunuz kaynak grubunu silin ve taşınan sanal ağ silinir. 

Kaynak grubunu kaldırmak için [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)komutunu kullanın:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Temizleme

Değişikliklerinizi yürütmek ve sanal ağ taşımayı tamamlamak için aşağıdakilerden birini yapın:

* [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)kullanarak kaynak grubunu silin:

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)kullanarak kaynak sanal ağı silme:  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, PowerShell kullanarak bir sanal ağı bir bölgeden diğerine taşımış sonra gereksiz kaynak kaynaklarını temizlülüsiniz. Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz.:

- [Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure sanal makinelerini başka bir bölgeye taşıma](../site-recovery/azure-to-azure-tutorial-migrate.md)
