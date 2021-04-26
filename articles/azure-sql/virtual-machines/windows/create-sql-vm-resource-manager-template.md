---
title: ARM şablonu kullanarak SQL Server VM oluşturma
description: Bir Azure Resource Manager şablonu (ARM şablonu) kullanarak Azure sanal makinesi (VM) üzerinde SQL Server oluşturma hakkında bilgi edinin.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.openlocfilehash: a20cb27ac91a0b01ed9cc3a5ac4c5c57f90ceda1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359651"
---
# <a name="quickstart-create-sql-server-vm-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak SQL Server VM oluşturma

Azure sanal makinesine (VM) bir SQL Server dağıtmak için bu Azure Resource Manager şablonu (ARM şablonu) kullanın. 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

SQL Server VM ARM şablonu şunları gerektirir:

- [Azure CLI](/cli/azure/install-azure-cli) ve/veya [PowerShell](/powershell/scripting/install/installing-powershell)'in en son sürümü. 
- Hazırlanmış bir [sanal ağ](../../../virtual-network/quick-create-portal.md) ve [alt ağı](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)olan önceden yapılandırılmış bir [kaynak grubu](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) .
- Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.


## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json":::

Şablonda beş Azure kaynağı tanımlanmıştır: 

- [Microsoft. Network/Publicıpaddresses](/azure/templates/microsoft.network/publicipaddresses): genel bir IP adresi oluşturur. 
- [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): bir ağ güvenlik grubu oluşturur. 
- [Microsoft. Network/NetworkInterfaces](/azure/templates/microsoft.network/networkinterfaces): ağ arabirimini yapılandırır. 
- [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Azure 'da bir sanal makine oluşturur. 
- [Microsoft. SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): sanal makineyi SQL IaaS Aracısı uzantısına kaydeder. 

Azure VM şablonlarındaki daha fazla SQL Server [hızlı başlangıç şablonu galerisinde](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine&pageNumber=1&sort=Popular)bulabilirsiniz.


## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon, tasarlanan SQL Server sürümünün yüklü olduğu bir sanal makine oluşturur ve SQL IaaS Aracısı uzantısına kaydedilir. 

   [![Azure’a dağıtma](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Aşağıdaki değerleri seçin veya girin.

    * **Abonelik**: bir Azure aboneliği seçin.
    * **Kaynak grubu**: SQL Server VM hazırlanan kaynak grubu. 
    * **Bölge**: bir bölge seçin.  Örneğin **Orta ABD**.
    * **Sanal makine adı**: SQL Server sanal makine için bir ad girin. 
    * **Sanal makine boyutu**: açılan kutudan sanal makineniz için uygun boyutu seçin.
    * **Var olan sanal ağ adı**: SQL Server VM hazırlanan sanal ağın adını girin. 
    * **Mevcut VNET kaynak grubu**: sanal ağınızın hazırlandığı kaynak grubunu girin. 
    * **Mevcut alt ağ adı**: hazırlanmış alt ağınızın adı. 
    * **Görüntü teklifi**: iş gereksinimlerinize en uygun SQL Server ve Windows Server görüntüsünü seçin. 
    * **SQL SKU 'su**: iş gereksinimlerinize en uygun SQL Server SKU sürümünü seçin. 
    * **Yönetici Kullanıcı adı**: sanal makinenin yöneticisinin Kullanıcı adı. 
    * **Yönetici parolası**: VM yönetici hesabı tarafından kullanılan parola. 
    * **Depolama Iş yükü türü**: işinizle en iyi şekilde eşleşen iş yükünün depolama türü. 
    * **SQL veri diskleri sayısı**: veri dosyaları için kullanılan SQL Server disk sayısı.  
    * **Veri yolu**: SQL Server veri dosyalarının yolu. 
    * **SQL günlük diskleri sayısı**: günlük dosyaları için kullanılan SQL Server disk sayısı. 
    * **Günlük yolu**: SQL Server günlük dosyalarının yolu. 
    * **Konum**: tüm kaynakların konumu, bu değer varsayılan olarak kalmalıdır `[resourceGroup().location]` . 

3. **Gözden geçir ve oluştur**’u seçin. SQL Server VM başarıyla dağıtıldıktan sonra bir bildirim alırsınız.

Şablonu dağıtmak için Azure portalı kullanılır. Azure portal ek olarak, Azure PowerShell, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Dağıtılan kaynakları denetlemek için Azure CLı ' yı kullanabilirsiniz. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, Azure CLı veya Azure PowerShell kullanarak kaynak grubunu silin:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Sonraki adımlar

Şablon oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için, bkz.:

> [!div class="nextstepaction"]
> [ Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

SQL Server VM dağıtmanın diğer yolları için bkz.: 
- [Azure portalı](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

Daha fazla bilgi edinmek için bkz. [Azure VM 'lerinde SQL Server genel bakış](sql-server-on-azure-vm-iaas-what-is-overview.md).