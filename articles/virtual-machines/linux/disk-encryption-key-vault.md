---
title: Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma
description: Bu makalede, Azure disk şifrelemesi ile kullanmak üzere bir Anahtar Kasası oluşturma ve yapılandırma adımları sağlanır
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 90306c55d976670f432d146d94764c4d90b8667d
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828497"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma

Azure disk şifrelemesi, disk şifreleme anahtarlarını ve gizli dizileri denetlemek ve yönetmek için Azure Key Vault kullanır.  Anahtar kasaları hakkında daha fazla bilgi için bkz. [Azure Key Vault kullanmaya başlama](../../key-vault/key-vault-get-started.md) ve [anahtar kasanızın güvenliğini sağlama](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Bir VM 'yi şifrelemek için Azure AD ile Azure disk şifrelemesi 'ni daha önce kullandıysanız, VM 'nizi şifrelemek için bu seçeneği kullanmaya devam etmeniz gerekir. Ayrıntılar için Azure [ad (önceki sürüm) Ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault-aad.md) konusuna bakın.

Azure disk şifrelemesi ile kullanım için bir Anahtar Kasası oluşturmak ve yapılandırmak üç adımdan oluşur:

1. Gerekirse bir kaynak grubu oluşturma.
2. Anahtar Kasası oluşturma. 
3. Anahtar Kasası Gelişmiş erişim ilkeleri ayarlanıyor.

Bu adımlar aşağıdaki hızlı başlangıçlarda gösterilmiştir:

- [Azure CLı ile Linux VM oluşturma ve şifreleme](disk-encryption-cli-quickstart.md)
- [Azure PowerShell ile Linux VM oluşturma ve şifreleme](disk-encryption-cli-quickstart.md)

Ayrıca, anahtar şifreleme anahtarı (KEK) oluşturabilir veya içeri aktarabilirsiniz.

> [!Note]
> Bu makaledeki adımlar, [Azure disk şifrelemesi ÖNKOŞULLARı CLI betiği](https://github.com/ejarvi/ade-cli-getting-started) ve [Azure disk şifrelemesi önkoşulları PowerShell betiği](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)içinde otomatikleştirilir.

## <a name="install-tools-and-connect-to-azure"></a>Araçları yükleyip Azure 'a bağlanın

Bu makaledeki adımlar [Azure CLI](/cli/azure/), [Azure PowerShell Az Module](/powershell/azure/overview)veya [Azure Portal](https://portal.azure.com)ile tamamlanabilir. 

Portal, tarayıcınız aracılığıyla erişilebilir olsa da, Azure CLı ve Azure PowerShell yerel yükleme gerektirir; bkz. [Linux Için Azure disk şifrelemesi: Ayrıntılar için araçları yükler](disk-encryption-linux.md#install-tools-and-connect-to-azure) .

### <a name="connect-to-your-azure-account"></a>Azure hesabınıza bağlanma

Azure CLı veya Azure PowerShell kullanmadan önce, önce Azure aboneliğinize bağlanmanız gerekir. [Azure CLI Ile oturum](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)açarak, [Azure PowerShell ile oturum](/powershell/azure/authenticate-azureps?view=azps-2.5.0)açarak veya istendiğinde kimlik bilgilerinizi Azure Portal sağlayarak bunu yapabilirsiniz.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

*Zaten bir kaynak grubunuz varsa, [bir Anahtar Kasası oluşturmak](#create-a-key-vault)için atlayabilirsiniz.*

Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

[Az Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI komutunu, [New-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell komutunu veya [Azure Portal](https://portal.azure.com)kullanarak bir kaynak grubu oluşturun.

### <a name="azure-cli"></a>Azure CLı

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Anahtar Kasası oluşturma

*Zaten bir anahtar kasanız varsa, [Anahtar Kasası Gelişmiş erişim Ilkeleri ayarlamak](#set-key-vault-advanced-access-policies)için atlayabilirsiniz.*

[Az keykasa Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI komutunu, [New-Azkeykasası](/powershell/module/az.keyvault/new-azkeyvault) azure PowerShell komutunu, [Azure Portal](https://portal.azure.com)veya bir [Kaynak Yöneticisi şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)kullanarak bir Anahtar Kasası oluşturun.

>[!WARNING]
> Şifreleme gizli dizileri 'nin bölgesel sınırların dışına bulunmadığından emin olmak için, Azure disk şifrelemesi Key Vault ve VM 'Lerin aynı bölgede birlikte bulunmasını gerektirir. Şifrelenecek VM 'Lerle aynı bölgedeki bir Key Vault oluşturun ve kullanın. 

Her Key Vault benzersiz bir adı olmalıdır. -Unique-keykasa-adı > < Aşağıdaki örneklerde anahtar kasanızın adıyla değiştirin.

### <a name="azure-cli"></a>Azure CLı

Azure CLı kullanarak bir Anahtar Kasası oluştururken, "--Enabled-for-disk-Encryption" bayrağını ekleyin.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kullanarak bir Anahtar Kasası oluştururken "-EnabledForDiskEncryption" bayrağını ekleyin.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Kaynak Yöneticisi şablonu

Ayrıca, [Kaynak Yöneticisi şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)kullanarak bir anahtar kasası da oluşturabilirsiniz.

1. Azure hızlı başlangıç şablonunda **Azure 'A dağıt**' a tıklayın.
2. Abonelik, kaynak grubu, kaynak grubu konumu, Key Vault adı, nesne KIMLIĞI, yasal koşullar ve anlaşma ' ı seçin ve ardından **satın al**' a tıklayın. 


##  <a name="set-key-vault-advanced-access-policies"></a>Anahtar Kasası Gelişmiş erişim ilkelerini ayarlama

Azure platformunun, birimleri önyüklemek ve şifrelerini çözmek için sanal makine için kullanılabilir hale getirmek üzere anahtar kasasındaki şifreleme anahtarlarına veya gizli anahtarlara erişmesi gerekir. 

Anahtar kasanızı, oluşturma sırasında (önceki adımda gösterildiği gibi) disk şifrelemesi, dağıtım veya şablon dağıtımı için etkinleştirmezseniz, Gelişmiş erişim ilkelerini güncelleştirmeniz gerekir.  

### <a name="azure-cli"></a>Azure CLı

Anahtar Kasası için disk şifrelemeyi etkinleştirmek üzere [az keykasatıon Update](/cli/azure/keyvault#az-keyvault-update) kullanın. 

 - **Key Vault disk şifrelemesi Için etkinleştir:** Etkin-disk şifrelemesi gereklidir. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Gerekirse dağıtım için Key Vault etkinleştirin:** Bu Anahtar Kasası kaynak oluşturma bölümünde başvuruluyorsa, örneğin bir sanal makine oluştururken, Microsoft. COMPUTE kaynak sağlayıcısı 'nın bu anahtar kasasından gizli dizileri almasını sağlar.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Gerekirse, şablon dağıtımı için Key Vault etkinleştirin:** Kaynak Yöneticisi kasalardan gizli dizileri almasına izin verin.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Anahtar Kasası için disk şifrelemeyi etkinleştirmek üzere [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Anahtar Kasası PowerShell cmdlet 'ini kullanın.

  - **Key Vault disk şifrelemesi Için etkinleştir:** Azure disk şifrelemesi için EnabledForDiskEncryption gereklidir.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Gerekirse dağıtım için Key Vault etkinleştirin:** Bu Anahtar Kasası kaynak oluşturma bölümünde başvuruluyorsa, örneğin bir sanal makine oluştururken, Microsoft. COMPUTE kaynak sağlayıcısı 'nın bu anahtar kasasından gizli dizileri almasını sağlar.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Gerekirse, şablon dağıtımı için Key Vault etkinleştirin:** Bu anahtar kasası bir şablon dağıtımında başvuruluyorsa, Azure Resource Manager Bu anahtar kasasından gizli dizileri almasını sağlar.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure portal

1. Anahtar kasanızı seçin, **erişim ilkeleri**' ne gidin ve **Gelişmiş erişim Ilkelerini göstermek için tıklayın**.
2. **Birim şifrelemesi Için Azure disk şifrelemesi 'ne erişimi etkinleştir**etiketli kutuyu seçin.
3. **Dağıtım Için Azure sanal makinelerine erişimi etkinleştir** ' i seçin ve/veya gerekirse **şablon dağıtımı Için Azure Resource Manager erişimi etkinleştirin**. 
4. **Kaydet**'e tıklayın.

    ![Azure Anahtar Kasası Gelişmiş erişim ilkeleri](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Anahtar şifreleme anahtarı ayarlama (KEK)

Şifreleme anahtarları için ek bir güvenlik katmanı için anahtar şifreleme anahtarı (KEK) kullanmak istiyorsanız, anahtar kasanıza bir KEK ekleyin. Anahtar şifreleme anahtarı belirtildiğinde Azure disk şifrelemesi, Key Vault yazmadan önce şifreleme gizli dizilerini kaydırmak için bu anahtarı kullanır.

Azure CLı [az keykasa Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) komutunu, Azure PowerShell [Add-azkeyvaultkey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 'ini veya [Azure Portal](https://portal.azure.com/)kullanarak yeni bir kek oluşturabilirsiniz. Bir RSA anahtar türü oluşturmanız gerekir; Azure disk şifrelemesi henüz eliptik eğri anahtarlarını kullanmayı desteklemiyor.

Bunun yerine, şirket içi anahtar yönetimi HSM 'nizden bir KEK içeri aktarabilirsiniz. Daha fazla bilgi için bkz. [Key Vault belgeleri](../../key-vault/key-vault-hsm-protected-keys.md). 

Anahtar Kasası KEK URL 'Lerinin sürümü oluşturulmalıdır. Azure, sürüm oluşturma kısıtlaması uygular. Geçerli gizli ve KEK URL 'Leri için aşağıdaki örneklere bakın:

* Geçerli bir gizli dizi URL 'SI örneği: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Geçerli bir KEK URL örneği: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure disk şifrelemesi, Anahtar Kasası gizli dizileri ve KEK URL 'Lerinin bir parçası olarak bağlantı noktası numaraları belirtilmesini desteklemez. Desteklenmeyen ve desteklenen Anahtar Kasası URL 'Leri örnekleri için aşağıdaki örneklere bakın:

  * Kabul edilebilir Anahtar Kasası URL 'SI: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Kabul edilmeyen Anahtar Kasası URL 'SI: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLı

Yeni bir KEK oluşturmak ve bunu Anahtar Kasanızda depolamak için Azure CLı [az keykasa Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) komutunu kullanın.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Bunun yerine, Azure CLı [az keykasa Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) komutunu kullanarak bir özel anahtarı içeri aktarabilirsiniz:

Her iki durumda da, KEK adını Azure CLı [az VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --Key-encryption-key parametresi olarak sağlarsınız. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Yeni bir KEK oluşturmak ve bunu Anahtar Kasanızda depolamak için Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) cmdlet 'ini kullanın.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Bunun yerine, Azure PowerShell [az keykasa Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) komutunu kullanarak bir özel anahtarı içeri aktarabilirsiniz.

Her iki durumda da, KEK anahtar kasanızın KIMLIĞINI ve KEK [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -keyencryptionkeytultıd ve-KeyEncryptionKeyUrl PARAMETRELERINE Azure PowerShell URL 'sini sağlamanız gerekir. Bu örnekte, hem disk şifreleme anahtarı hem de KEK için aynı anahtar kasasını kullandığınızı varsaydığını unutmayın.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Sonraki adımlar

- [Azure disk şifrelemesi önkoşulları CLı betiği](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure disk şifrelemesi önkoşulları PowerShell betiği](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Linux VM 'Lerinde Azure disk şifrelemesi senaryoları](disk-encryption-linux.md) öğrenin
- [Azure disk şifrelemesi sorunlarını giderme](disk-encryption-troubleshooting.md) hakkında bilgi edinin
- [Azure disk şifrelemesi örnek betiklerini](disk-encryption-sample-scripts.md) okuyun
