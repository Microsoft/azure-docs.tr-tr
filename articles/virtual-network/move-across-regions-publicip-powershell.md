---
title: Azure genel IP yapılandırmasını Azure PowerShell kullanarak başka bir Azure bölgesine taşıma
description: Azure genel IP yapılandırmasını bir Azure bölgesinden diğerine Azure PowerShell kullanarak taşımak için Azure Resource Manager şablonu kullanın.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: a21d088680855b74e7259028ed7ef55165707c56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938698"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>Azure genel IP yapılandırmasını Azure PowerShell kullanarak başka bir bölgeye taşıma

Mevcut Azure genel IP yapılandırmalarınızı bir bölgeden diğerine taşımak istediğiniz çeşitli senaryolar vardır. Örneğin, test için aynı yapılandırma ve SKU ile genel bir IP oluşturmak isteyebilirsiniz. Ayrıca, olağanüstü durum kurtarma planlamasının bir parçası olarak genel bir IP yapılandırmasını başka bir bölgeye taşımak isteyebilirsiniz.

**Azure genel IP 'Leri bölgeye özeldir ve bir bölgeden diğerine taşınamaz.** Bununla birlikte, genel bir IP 'nin var olan yapılandırmasını dışarı aktarmak için bir Azure Resource Manager şablonu kullanabilirsiniz.  Daha sonra, genel IP 'yi bir şablona dışarı aktararak, hedef bölgeyle eşleşecek parametreleri değiştirerek ve sonra şablonu yeni bölgeye dağıtabilmeniz için kaynağı başka bir bölgede da oluşturabilirsiniz.  Kaynak Yöneticisi ve şablonlar hakkında daha fazla bilgi için bkz. [kaynak gruplarını şablonlara dışarı aktarma](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Önkoşullar

- Azure genel IP 'nin taşımak istediğiniz Azure bölgesinde olduğundan emin olun.

- Azure genel IP 'Leri bölgeler arasında taşınamaz.  Yeni genel IP 'yi hedef bölgedeki kaynaklarla ilişkilendirmeniz gerekir.

- Genel bir IP yapılandırmasını dışarı aktarmak ve başka bir bölgede genel IP oluşturmak üzere şablon dağıtmak için, ağ katılımcısı rolü veya daha yüksek bir sürümü gerekir.
   
- Kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belirler. Bu düzen, yük dengeleyiciler, ağ güvenlik grupları (NSG 'ler) ve sanal ağlar dahil değildir ancak bunlarla sınırlı değildir.

- Azure aboneliğinizin, kullanılan hedef bölgede ortak IP 'Ler oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı sağlamak için desteğe başvurun.

- Aboneliğinizin bu işlem için genel IP 'lerin eklenmesini desteklemek için yeterli kaynağa sahip olduğundan emin olun.  Bkz. [Azure aboneliği ve hizmet sınırları, kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Hazırlama ve taşıma
Aşağıdaki adımlarda, bir Kaynak Yöneticisi şablonu kullanarak yapılandırma taşıma için genel IP 'nin nasıl hazırlanacağı ve Azure PowerShell kullanarak genel IP yapılandırmasını hedef bölgeye nasıl taşıyacağınız gösterilmektedir.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Şablonu dışarı aktarma ve bir betikten dağıtma

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hedef bölgeye taşımak istediğiniz genel IP 'nin kaynak KIMLIĞINI alın ve [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress)kullanarak bir değişkene yerleştirin:

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Kaynak sanal ağı bir. JSON dosyasına dışarı aktarma [-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)komutunu yürütebileceğiniz dizine aktarın:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. İndirilen dosya, kaynağın öğesinden verildikten sonra adı alınacaktır.  **\<resource-group-name> . JSON** adlı komuttan aktarılmış dosyayı bulun ve seçtiğiniz bir düzenleyicide açın:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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

         Temel ve standart SKU genel IP 'leri arasındaki farklılıklar hakkında daha fazla bilgi için bkz. [genel IP adresi oluşturma, değiştirme veya silme](./virtual-network-public-ip-address.md).

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

        Ayırma yöntemleri ve boşta kalma zaman aşımı değerleri hakkında daha fazla bilgi için bkz. [genel IP adresi oluşturma, değiştirme veya silme](./virtual-network-public-ip-address.md).


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
## <a name="discard"></a>Vazgeç 

Dağıtımdan sonra, hedefteki genel IP 'yi baştan başlatmak veya atmak istiyorsanız hedefte oluşturulan kaynak grubunu silin ve taşınan genel IP silinir.  Kaynak grubunu kaldırmak için [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)komutunu kullanın:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Temizleme

Değişiklikleri yürütmek ve sanal ağı taşımayı tamamlamak için, kaynak sanal ağını veya kaynak grubunu silin, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) veya [Remove-azpublicıpaddress](/powershell/module/az.network/remove-azpublicipaddress)komutunu kullanın:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure genel IP 'sini bir bölgeden diğerine taşımış ve kaynak kaynakları temizledi.  Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz:


- [Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM’lerini başka bir bölgeye taşıma](../site-recovery/azure-to-azure-tutorial-migrate.md)
