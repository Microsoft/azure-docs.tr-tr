---
title: Azure Resource Manager şablonu kullanarak bir Azure kurtarma hizmetleri Kasası oluşturmak için hızlı başlangıç.
description: Bu hızlı başlangıçta, bir Azure Resource Manager şablonu kullanarak Azure kurtarma hizmetleri Kasası oluşturmayı öğreneceksiniz (ARM şablonu).
ms.date: 04/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 11a88b5485ad970802a65af31daccdb30a1c86df
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533261"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak bir kurtarma hizmetleri Kasası oluşturma

Bu hızlı başlangıçta, bir Azure Resource Manager şablonu (ARM şablonu) kullanarak bir kurtarma hizmetleri kasasının nasıl ayarlanacağı açıklanmaktadır. [Azure Site Recovery](site-recovery-overview.md) hizmeti, iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejinize katkıda bulunur. böylece, planlı ve plansız kesintiler sırasında iş uygulamalarınızın çevrimiçi kalması sağlanır. Site Recovery, şirket içi makinelerin ve Azure sanal makinelerinin (VM) çoğaltma, yük devretme ve kurtarma gibi olağanüstü durum kurtarma işlemlerini yönetir.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Etkin bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturabilirsiniz.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

Şablonda iki Azure kaynağı tanımlanmıştır:

- [Microsoft. RecoveryServices kasaları](/azure/templates/microsoft.recoveryservices/vaults): kasayı oluşturur.
- [Microsoft. RecoveryServices/Vaults/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): kasanın yedek artıklığı ayarlarını yapılandırır.

Şablon, kasanın yedekleme yapılandırması için isteğe bağlı parametreler içerir. Depolama artıklığı ayarları yerel olarak yedekli depolama (LRS) veya coğrafi olarak yedekli depolama (GRS). Daha fazla bilgi için bkz. [depolama yedekliliği ayarlama](../backup/backup-create-rs-vault.md#set-storage-redundancy).

Daha fazla Azure Kurtarma Hizmetleri şablonu için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Şablonu dağıtma

Şablonu dağıtmak için **abonelik**, **kaynak grubu** ve **kasa adı** gereklidir.

1. Azure 'da oturum açmak ve şablonu açmak için **Azure 'A dağıt** görüntüsünü seçin.

   [![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Aşağıdaki değerleri seçin veya girin:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Bir kurtarma hizmetleri Kasası oluşturmak için şablon.":::

   - **Abonelik**: Azure aboneliğinizi seçin.
   - **Kaynak grubu**: var olan bir grubu seçin veya grup eklemek Için **Yeni oluştur** ' u seçin.
   - **Konum**: kaynak grubunun konumunu varsayılan olarak belirler ve bir kaynak grubu seçildikten sonra kullanılamaz hale gelir.
   - **Kasa adı**: kasa için bir ad sağlayın.
   - **Depolama türünü değiştir**: varsayılan değer **false**'dur. Yalnızca kasanın depolama türünü değiştirmeniz gerekiyorsa **true** ' ı seçin.
   - **Kasa depolama türü**: varsayılan: **GloballyRedundant**. Depolama türü **true** olarak ayarlandıysa, **locallyyedekli**' i seçin.
   - **Konum**: işlev, `[resourceGroup().location]` kaynak grubunun konumunu varsayılan olarak belirler. Konumu değiştirmek için **westus** gibi bir değer girin.
   - **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum** onay kutusunu seçin.

1. Kasanın dağıtımına başlamak için, **satın al** düğmesini seçin. Başarılı bir dağıtımdan sonra bir bildirim görüntülenir.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="Kasa dağıtımı başarılı oldu.":::

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Kasanın oluşturulduğunu doğrulamak için Azure CLı veya Azure PowerShell kullanın.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

Aşağıdaki çıktı, kasadaki bilgilerin bir alıntısıdır:

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yeni kaynakları kullanmayı planlıyorsanız, hiçbir işlem yapmanız gerekmez. Aksi takdirde, bu hızlı başlangıçta oluşturulan kaynak grubunu ve kasayı kaldırabilirsiniz. Kaynak grubunu ve kaynaklarını silmek için Azure CLı veya Azure PowerShell kullanın.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir kurtarma hizmetleri Kasası oluşturdunuz. Olağanüstü durum kurtarma hakkında daha fazla bilgi edinmek için sonraki hızlı başlangıç makalesine ilerleyin.

> [!div class="nextstepaction"]
> [Olağanüstü durum kurtarma işlemini ayarlama](azure-to-azure-quickstart.md)
