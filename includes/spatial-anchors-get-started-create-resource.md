---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 2c85e26d5a9115b00621c4099e3ed36afb224e3f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881361"
---
## <a name="create-a-spatial-anchors-resource"></a>Uzamsal bağlayıcı kaynağı oluşturma

### <a name="portal"></a>[Portal](#tab/azure-portal)

<a href="https://portal.azure.com" target="_blank">Azure Portal</a> gidin.

Sol bölmede **kaynak oluştur**' u seçin.

**Uzamsal bağlantıları** aramak için arama kutusunu kullanın.

![Uzamsal Tutturucuların aramasının sonuçlarını gösteren ekran görüntüsü.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

**Uzamsal bağlayıcıları** seçin ve ardından **Oluştur**' u seçin.

**Uzamsal bağlayıcı hesabı** bölmesinde şunları yapın:

* Normal alfasayısal karakterleri kullanarak benzersiz bir kaynak adı girin.
* Kaynağı iliştirmek istediğiniz aboneliği seçin.
* **Yeni oluştur** seçeneğini belirleyerek bir kaynak grubu oluşturun. **Myresourcegroup** olarak adlandırın ve ardından **Tamam**' ı seçin.

  [!INCLUDE [resource group intro text](resource-group.md)]

* Kaynağın yerleştirileceği bir konum (bölge) seçin.
* Kaynağı oluşturmaya başlamak için **Yeni** ' yi seçin.

![Kaynak oluşturmak için uzamsal bağlayıcı bölmesinin ekran görüntüsü.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Kaynak oluşturulduktan sonra, Azure portal dağıtımınızın tamamlandığını gösterir.

![Kaynak dağıtımının tamamlandığını gösteren ekran görüntüsü.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

**Kaynağa git**’i seçin. Artık kaynak özelliklerini görüntüleyebilirsiniz.

Kaynağın **hesap kimliği** değerini daha sonra kullanmak üzere bir metin düzenleyicisine kopyalayın.

![Kaynak özellikleri bölmesinin ekran görüntüsü.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Ayrıca, kaynağın **hesap etki alanı** değerini daha sonra kullanmak üzere bir metin düzenleyicisine kopyalayın.

![Kaynağın hesap etki alanı değerini gösteren ekran görüntüsü.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

**Ayarlar** altında **anahtar**' ı seçin. **Birincil anahtar** değerini, **hesap anahtarını** daha sonra kullanmak üzere bir metin düzenleyicisine kopyalayın.

![Hesap için anahtarlar bölmesinin ekran görüntüsü.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Başlangıç olarak ortamınızı Azure CLI için hazırlayın:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. Oturum açtıktan sonra, uzamsal bağlayıcı hesabının ayarlanacağı aboneliği seçmek için [az Account set](/cli/azure/account#az_account_set) komutunu kullanın:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Bir kaynak grubu oluşturmak için [az Group Create](/cli/azure/group#az_group_create) komutunu çalıştırın veya var olan bir kaynak grubunu kullanın:

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   [Az uzamsal-bağlayıcı hesabı listesi](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_list) komutunu kullanarak bir kaynak grubu için geçerli uzamsal bağlayıcı hesaplarınızı görüntüleyebilirsiniz:

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   Aboneliğiniz için de uzamsal bağlayıcı hesaplarını görüntüleyebilirsiniz:

   ```azurecli
   az spatial-anchors-account list
   ```

1. Uzamsal bağlayıcı hesabınızı oluşturmak için [az uzamsal-bağlayıcı-hesap oluştur](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_create) komutunu çalıştırın:

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. [Az uzamsal-bağlayıcı-hesap göster](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_show) komutunu kullanarak kaynak özelliklerini görüntüleyin:

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Kaynak **hesap kimliği** değerini ve kaynak **hesabı etki alanı** değerini daha sonra kullanmak üzere bir metin düzenleyicisine kopyalayın.

1. Birincil ve ikincil anahtarlarınızı almak için [az uzamsal-bağlayıcı-hesap anahtarı göster](/cli/azure/spatial-anchors-account/key#az_spatial_anchors_account_key_show) komutunu çalıştırın:

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Anahtar değerlerini daha sonra kullanmak üzere bir metin düzenleyicisine kopyalayın.

   Anahtarları yeniden oluşturmanız gerekiyorsa, [az uzamsal-bağlayıcı-hesap anahtar yenileme](/cli/azure/spatial-anchors-account/key#az_spatial_anchors_account_key_renew) komutunu kullanın:

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

[Az uzamsal-bağlayıcı-Account Delete](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_delete) komutunu kullanarak bir hesabı silebilirsiniz:

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Başlangıç olarak ortamınızı Azure PowerShell için hazırlayın:

[!INCLUDE [azure-powershell-requirements-no-header.md](azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> **Az. MixedReality** PowerShell modülü önizlemedeyken, cmdlet 'ini kullanarak ayrı olarak yüklemelisiniz `Install-Module` . Bu PowerShell modülü genel kullanıma sunulduktan sonra, gelecekteki Az PowerShell modülü sürümlerinin bir parçası haline gelecek ve Azure Cloud Shell içinden varsayılan olarak sağlanacaktır.

```azurepowershell-interactive
Install-Module -Name Az.MixedReality
```

1. Oturum açtıktan sonra, uzamsal bağlayıcı hesabının ayarlanacağı aboneliği seçmek için [set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanın:

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. [Yeni-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini çalıştırarak bir kaynak grubu oluşturun veya var olan bir kaynak grubunu kullanın:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) cmdlet 'ini kullanarak, bir kaynak grubu için geçerli uzamsal bağlayıcı hesaplarınızı görüntüleyebilirsiniz:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup
   ```

   Aboneliğiniz için de uzamsal bağlayıcı hesaplarını görüntüleyebilirsiniz:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount
   ```

1. Uzamsal bağlayıcı hesabınızı oluşturmak için [New-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/new-azspatialanchorsaccount) cmdlet 'ini çalıştırın:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart -Location eastus2
   ```

1. [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) cmdlet 'ini kullanarak kaynak özelliklerini görüntüleyin:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Daha sonra kullanmak üzere **AccountID** değerini ve **accountdomain** değerini bir metin düzenleyicisine kopyalayın.

1. Birincil ve ikincil anahtarlarınızı almak için [Get-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/get-azspatialanchorsaccountkey) cmdlet 'ini çalıştırın:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccountKey -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Anahtar değerlerini daha sonra kullanmak üzere bir metin düzenleyicisine kopyalayın.

   Anahtarları yeniden oluşturmanız gerekirse, [New-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/new-azspatialanchorsaccountkey) cmdlet 'ini kullanın:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Primary
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Secondary
   ```

[Remove-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/remove-azspatialanchorsaccount) cmdlet 'ini kullanarak bir hesabı silebilirsiniz:

```azurepowershell-interactive
Remove-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
```

---
