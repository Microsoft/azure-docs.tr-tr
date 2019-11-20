---
title: Linux VM 'lerinde Azure disk şifrelemesi senaryoları
description: Bu makale çeşitli senaryolar için Linux VM 'Leri için Microsoft Azure disk şifrelemeyi etkinleştirme hakkında yönergeler sağlar
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 2e7646d2f84696d0b04183d8d06b96405909de87
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750050"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Linux VM 'lerinde Azure disk şifrelemesi senaryoları

Azure disk şifrelemesi, Azure sanal makinelerinin (VM 'Ler) işletim sistemi ve veri diskleri için birim şifrelemesi sağlamak üzere Linux 'un DM-Crypt özelliğini kullanır ve disk şifreleme anahtarlarını ve gizli dizileri denetlemenize ve yönetmenize yardımcı olmak için Azure Key Vault ile tümleşiktir. Hizmete genel bakış için bkz. [Linux VM 'leri Için Azure disk şifrelemesi](disk-encryption-overview.md).

Birçok disk şifreleme senaryosu vardır ve adımlar senaryoya göre farklılık gösterebilir. Aşağıdaki bölümlerde Linux sanal makinelerinde daha ayrıntılı senaryolar ele alınmaktadır.

Yalnızca [desteklenen VM boyutlarının ve işletim sistemlerinin](disk-encryption-overview.md#supported-vms-and-operating-systems)sanal makinelerine disk şifrelemesi uygulayabilirsiniz. Ayrıca aşağıdaki önkoşulları karşılamanız gerekir:

- [VM 'Ler için ek gereksinimler](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ağ gereksinimleri](disk-encryption-overview.md#networking-requirements)
- [Şifreleme anahtarı depolama gereksinimleri](disk-encryption-overview.md#encryption-key-storage-requirements)

Her durumda, [bir anlık görüntü](snapshot-copy-managed-disk.md) almalısınız ve/veya diskler şifrelenmeden önce bir yedek oluşturmalısınız. Yedeklemeler, şifreleme sırasında beklenmeyen bir hata oluşursa, kurtarma seçeneğinin mümkün olmasını güvence altına almanıza olanak tanır. Yönetilen disklere sahip VM 'Ler şifreleme gerçekleşmeden önce bir yedekleme gerektirir. Bir yedekleme yapıldıktan sonra,-skipVmBackup parametresini belirterek yönetilen diskleri şifrelemek için [set-AzVMDiskEncryptionExtension cmdlet 'ini](/powershell/module/az.compute/set-azvmdiskencryptionextension) kullanabilirsiniz. Şifrelenmiş VM 'Leri yedekleme ve geri yükleme hakkında daha fazla bilgi için [Azure Backup](../../backup/backup-azure-vms-encryption.md) makalesine bakın. 

>[!WARNING]
> - Bir VM 'yi şifrelemek için Azure AD ile Azure disk şifrelemesi 'ni daha önce kullandıysanız, VM 'nizi şifrelemek için bu seçeneği kullanmaya devam etmeniz gerekir. Ayrıntılar için bkz. [Azure AD ile Azure disk şifrelemesi (önceki sürüm)](disk-encryption-overview-aad.md) . 
>
> - Linux işletim sistemi birimlerini şifrelerken, sanal makinenin kullanılamaz kabul edilmesi gerekir. Şifreleme işlemi sırasında erişilmesi gereken açık dosyaları engelleyen sorunlardan kaçınmak için şifreleme sürerken SSH oturum açma işlemlerini kesinlikle öneririz. İlerlemeyi denetlemek için [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell cmdlet 'ini veya [VM şifrelemesi Show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI komutunu kullanın. Bu işlemin, bir 30 GB işletim sistemi birimi için birkaç saat, ayrıca veri birimlerini şifrelemek için de ek süre gelmesi beklenmelidir. Şifreleme biçimi ALL seçeneği kullanılmamışsa, veri hacmi şifreleme süresi veri birimlerinin boyutuyla ve miktarıyla orantılıdır. 
> - Linux VM 'lerinde şifrelemeyi devre dışı bırakmak yalnızca veri birimlerinde desteklenir. İşletim sistemi birimi şifrelendiyse veri veya işletim sistemi birimlerinde desteklenmez.  

## <a name="install-tools-and-connect-to-azure"></a>Araçları yükleyip Azure 'a bağlanın

Azure disk şifrelemesi, [Azure CLI](/cli/azure) ve [Azure PowerShell](/powershell/azure/new-azureps-module-az)aracılığıyla etkinleştirilebilir ve yönetilebilir. Bunu yapmak için araçları yerel olarak yüklemeli ve Azure aboneliğinize bağlamanız gerekir.

### <a name="azure-cli"></a>Azure CLI

[Azure clı 2,0](/cli/azure) , Azure kaynaklarını yönetmeye yönelik bir komut satırı aracıdır. CLı, verileri esnek bir şekilde sorgulamak, engelleyici olmayan işlemler olarak uzun süreli işlemleri desteklemek ve komut dosyasını kolay hale getirmek için tasarlanmıştır. [Azure CLI 'Yı yüklemeye](/cli/azure/install-azure-cli?view=azure-cli-latest)ilişkin adımları izleyerek yerel olarak yükleyebilirsiniz.

 

Azure [CLI Ile Azure hesabınızda oturum açmak](/cli/azure/authenticate-azure-cli)için [az Login](/cli/azure/reference-index?view=azure-cli-latest#az-login) komutunu kullanın.

```azurecli
az login
```

Oturum açmak için bir kiracı seçmek istiyorsanız şunu kullanın:
    
```azurecli
az login --tenant <tenant>
```

Birden çok aboneliğiniz varsa ve belirli bir tane belirtmek istiyorsanız, [az Account List](/cli/azure/account#az-account-list) komutuyla abonelik listenizi alın ve [az Account set](/cli/azure/account#az-account-set)ile belirtin.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Daha fazla bilgi için bkz. [Azure CLI ile çalışmaya başlama 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az Module](/powershell/azure/new-azureps-module-az) , Azure kaynaklarınızı yönetmek için [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) modelini kullanan bir cmdlet kümesi sağlar. Bunu tarayıcınızda [Azure Cloud Shell](../../cloud-shell/overview.md)ile kullanabilir veya [Azure PowerShell modülünü yüklemeye](/powershell/azure/install-az-ps)ilişkin yönergeleri kullanarak yerel makinenize yükleyebilirsiniz. 

Yerel olarak zaten yüklüyse, Azure disk şifrelemesini yapılandırmak için Azure PowerShell SDK sürümünün en son sürümünü kullandığınızdan emin olun. [Azure PowerShell sürümünün](https://github.com/Azure/azure-powershell/releases)en son sürümünü indirin.

[Azure hesabınızda Azure PowerShell oturum açmak](/powershell/azure/authenticate-azureps?view=azps-2.5.0)için [Connect-azaccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet 'ini kullanın.

```powershell
Connect-AzAccount
```

Birden çok aboneliğiniz varsa ve bir tane belirtmek istiyorsanız, [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) cmdlet 'ini kullanarak bunları listeleyin ve ardından [set-azcontext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet 'ini kullanın:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

[Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) cmdlet 'ini çalıştırmak, doğru aboneliğin seçildiğini doğrular.

Azure disk şifrelemesi cmdlet 'lerinin yüklendiğini doğrulamak için [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) cmdlet 'ini kullanın:
     
```powershell
Get-command *diskencryption*
```
Daha fazla bilgi için bkz. [Azure PowerShell kullanmaya](/powershell/azure/get-started-azureps)başlama. 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Mevcut veya çalışan bir Linux VM 'de şifrelemeyi etkinleştirme
Bu senaryoda, Kaynak Yöneticisi şablonu, PowerShell cmdlet 'leri veya CLı komutlarını kullanarak şifrelemeyi etkinleştirebilirsiniz. Sanal makine uzantısı için şema bilgilerine ihtiyacınız varsa, [Linux uzantısı Için Azure disk şifrelemesi](../extensions/azure-disk-enc-linux.md) makalesine bakın.

>[!IMPORTANT]
 >Yönetilen disk tabanlı VM örneğinin, Azure disk şifrelemesi etkinleştirilmeden önce ve/veya dışında bir şekilde anlık görüntü ve/veya yedeklenmesi zorunludur. Yönetilen diskin bir anlık görüntüsü portaldan veya [Azure Backup](../../backup/backup-azure-vms-encryption.md)aracılığıyla alınabilir. Yedeklemeler, şifreleme sırasında beklenmeyen bir hata olması durumunda kurtarma seçeneğinin yapılabilmesini sağlamaktır. Yedekleme yapıldıktan sonra,-skipVmBackup parametresini belirterek yönetilen diskleri şifrelemek için set-AzVMDiskEncryptionExtension cmdlet 'i kullanılabilir. Set-AzVMDiskEncryptionExtension komutu, bir yedekleme yapılıncaya ve bu parametre belirtilene kadar yönetilen disk tabanlı VM 'Lere karşı başarısız olur. 
>
>Şifrelemeyi şifrelemek veya devre dışı bırakmak VM 'nin yeniden başlatılmasına neden olabilir. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Azure CLı kullanarak mevcut veya çalışan bir Linux VM 'de şifrelemeyi etkinleştirme 

[Azure CLI](/cli/azure/?view=azure-cli-latest) komut satırı aracını yükleyip kullanarak, şifrelenmiş VHD 'niz üzerinde disk şifrelemeyi etkinleştirebilirsiniz. [Azure Cloud Shell](../../cloud-shell/overview.md) ile tarayıcınızda kullanabilir veya yerel makinenize yükleyip herhangi bir PowerShell oturumunda kullanabilirsiniz. Azure 'da mevcut veya çalışan Linux VM 'lerinde şifrelemeyi etkinleştirmek için aşağıdaki CLı komutlarını kullanın:

Azure 'da çalışan bir sanal makinede şifrelemeyi etkinleştirmek için [az VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) komutunu kullanın.

- **Çalışan bir VM 'yi şifreleyin:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Çalışan bir VM 'yi KEK kullanarak şifreleyin:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Disk-Encryption-keykasası parametresinin değeri için sözdizimi tam tanımlayıcı dizesidir:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[keykasaadı]</br>
Anahtar şifreleme-anahtar parametresinin değeri için sözdizimi, KEK: https://[keykasaadı]. kasa. Azure. net/Keys/[kekname]/[kek-Unique-ID] ile 

- **Disklerin şifrelendiğini doğrulayın:** Bir sanal makinenin şifreleme durumunu denetlemek için [az VM Encryption Show](/cli/azure/vm/encryption#az-vm-encryption-show) komutunu kullanın. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Şifrelemeyi devre dışı bırak:** Şifrelemeyi devre dışı bırakmak için [az VM ENCRYPTION Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) komutunu kullanın. Şifrelemeyi devre dışı bırakmak yalnızca Linux sanal makineleri için veri birimlerinde kullanılabilir.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>PowerShell kullanarak mevcut veya çalışan bir Linux VM 'de şifrelemeyi etkinleştirme
Azure 'da çalışan bir sanal makinede şifrelemeyi etkinleştirmek için [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet 'ini kullanın. Diskler şifrelenmeden önce bir [anlık görüntü](snapshot-copy-managed-disk.md) alın ve/veya VM 'yi [Azure Backup](../../backup/backup-azure-vms-encryption.md) yedekleyin. -SkipVmBackup parametresi, çalışan bir Linux sanal makinesini şifrelemek için PowerShell betiklerine zaten belirtildi.

-  **Çalışan bir VM 'Yi şifreleyin:** Aşağıdaki komut dosyası değişkenlerinizi başlatır ve set-AzVMDiskEncryptionExtension cmdlet 'ini çalıştırır. Kaynak grubu, VM ve Anahtar Kasası, ön koşullar olarak oluşturulmuştur. MyVirtualMachineResourceGroup, MySecureVM ve Mysecurekasasını değerlerinizle değiştirin. Hangi diskleri şifrelediğiniz belirlemek için-VolumeType parametresini değiştirin.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Çalışan bir VM 'YI kek kullanarak şifreleyin:** İşletim sistemi diskini değil, veri disklerini şifreliyorsanız-VolumeType parametresini eklemeniz gerekebilir. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Disk-Encryption-keykasası parametresinin değeri için sözdizimi tam tanımlayıcı dizesidir:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[keykasaadı]</br> Anahtar şifreleme-anahtar parametresinin değeri için sözdizimi, KEK: https://[keykasaadı]. kasa. Azure. net/Keys/[kekname]/[kek-Unique-ID] ile 
    
- **Disklerin şifrelendiğini doğrulayın:** Bir VM 'nin şifreleme durumunu denetlemek için [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet 'ini kullanın. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Disk şifrelemesini devre dışı bırak:** Şifrelemeyi devre dışı bırakmak için [Disable-Azvmdiskencryptıon](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet 'ini kullanın. Şifrelemeyi devre dışı bırakmak yalnızca Linux sanal makineleri için veri birimlerinde kullanılabilir.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Mevcut veya çalışan bir Linux sanal makinesinde şablonla şifrelemeyi etkinleştirme

[Kaynak Yöneticisi şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)kullanarak Azure 'da var olan veya çalışan BIR Linux sanal makinesinde disk şifrelemeyi etkinleştirebilirsiniz.

1. Azure hızlı başlangıç şablonunda **Azure 'A dağıt** ' a tıklayın.

2. Abonelik, kaynak grubu, kaynak grubu konumu, parametreler, yasal koşullar ve Sözleşme ' yi seçin. Mevcut veya çalışan VM 'de şifrelemeyi etkinleştirmek için **Oluştur** ' a tıklayın.

Aşağıdaki tabloda mevcut veya çalışan VM 'Ler için Kaynak Yöneticisi şablon parametreleri listelenmektedir:

| Parametre | Açıklama |
| --- | --- |
| vmName | Şifreleme işleminin çalıştırılacağı sanal makinenin adı. |
| keyVaultName | Şifreleme anahtarının karşıya yüklenmesi gereken anahtar kasasının adı. Bunu, cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` veya Azure CLı komutu `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`kullanarak alabilirsiniz.|
| keyVaultResourceGroup | Anahtar kasasını içeren kaynak grubunun adı. |
|  keyEncryptionKeyURL 'Si | Şifreleme anahtarını şifrelemek için kullanılan anahtar şifreleme anahtarının URL 'SI. UseExistingKek açılır listesinde **nokek** ' ı seçerseniz bu parametre isteğe bağlıdır. UseExistingKek açılır listesinde **kek** ' yi seçerseniz _Keyencryptionkeyurl_ değerini girmeniz gerekir. |
| Birimtürü | Şifreleme işleminin gerçekleştirildiği birimin türü. Geçerli değerler _Işletim sistemi_, _veri_ve _hepsi_. 
| forceUpdateTag | İşlemin zorla çalışması gereken her seferinde GUID gibi benzersiz bir değer geçirin. |
| resizeOSDisk | Sistem birimini bölmeden önce IŞLETIM sistemi bölümünün tam işletim sistemi VHD 'SI kaplamaya yeniden boyutlandırılması gerekir. |
| location | Tüm kaynakların konumu. |


## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Linux VM 'lerinde veri diskleri için EncryptFormatAll özelliğini kullanın

**Encryptformatall** parametresi, Linux veri disklerinin şifrelenme süresini azaltır. Belirli ölçütlere uyan bölümler biçimlendirilecek (geçerli dosya sistemi ile) ve ardından komut yürütmeden önce olduğu yere yeniden takılacaktır. Ölçütlere uyan bir veri diskini dışlamak istiyorsanız, komutu çalıştırmadan önce onu kaldırabilirsiniz.

 Bu komutu çalıştırdıktan sonra, daha önce bağlanan tüm sürücüler biçimlendirilir ve şifreleme katmanı artık boş sürücünün üstünde başlatılır. Bu seçenek belirlendiğinde, VM 'ye bağlı olan kısa ömürlü kaynak disk de şifrelenir. Kısa ömürlü sürücü sıfırlandığında, sonraki fırsatta Azure disk şifrelemesi çözümü tarafından sanal makine için yeniden biçimlendirilir ve yeniden şifrelenir. Kaynak disk şifrelendikten sonra, [Microsoft Azure Linux Aracısı](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) kaynak diski yönetemez ve takas dosyasını etkinleştiremeyecektir, ancak takas dosyasını el ile yapılandırabilirsiniz.

>[!WARNING]
> Bir VM 'nin veri birimlerinde gerekli veriler olduğunda, EncryptFormatAll kullanılmamalıdır. Diskleri şifrelemeden hariç tutabilir. Öncelikle bir test sanal makinesinde ilk olarak Encryptformatı denemeniz, özellik parametresini ve uygulamayı üretim VM 'sinde denemeden önce öğrenmelisiniz. EncryptFormatAll seçeneği, veri diskini ve üzerindeki tüm verileri biçimlendirir. Devam etmeden önce, dışlamak istediğiniz disklerin düzgün şekilde takılmamış olduğunu doğrulayın. </br></br>
 >Şifreleme ayarlarını güncelleştirirken bu parametreyi ayarlarsanız, gerçek şifrelemeden önce bir yeniden başlatmaya neden olabilir. Bu durumda, fstab dosyasından biçimlendirilmesini istemediğiniz diski de kaldırmak isteyeceksiniz. Benzer şekilde, şifreleme işlemini başlatmadan önce fstab dosyasına şifrelemek istediğiniz bölümü eklemeniz gerekir. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll ölçütü
Parametresi tüm bölümlere geçer ve aşağıdaki ölçütlerin **Tümünü** karşılayan sürece bunları şifreler: 
- Bir kök/işletim sistemi/önyükleme bölümü değil
- Zaten şifreli değil
- Bir BEK birimi değil
- Bir RAID birimi değil
- Bir LVM birimi değil
- Bağlı

RAID veya LVM birimi yerine RAID veya LVM birimini oluşturan diskleri şifreleyin.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Azure CLı ile EncryptFormatAll parametresini kullanma
Azure 'da çalışan bir sanal makinede şifrelemeyi etkinleştirmek için [az VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) komutunu kullanın.

-  **Çalışan bir VM 'yi EncryptFormatAll kullanarak şifreleyin:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Bir PowerShell cmdlet 'i ile EncryptFormatAll parametresini kullanma
EncryptFormatAll parametresiyle [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet 'ini kullanın. 

**Çalışan bir VM 'Yi EncryptFormatAll kullanarak şifreleyin:** Örnek olarak, aşağıdaki komut dosyası değişkenlerinizi başlatır ve EncryptFormatAll parametresiyle set-AzVMDiskEncryptionExtension cmdlet 'ini çalıştırır. Kaynak grubu, VM ve Anahtar Kasası, ön koşullar olarak oluşturulmuştur. MyVirtualMachineResourceGroup, MySecureVM ve Mysecurekasasını değerlerinizle değiştirin.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Mantıksal birim Yöneticisi (LVM) ile EncryptFormatAll parametresini kullanın 
Bir LVM-şifreli kurulum önerilir. Aşağıdaki tüm örneklerde, cihaz yolu ve bağlama noktalarını, kullanım örneklerinize uygun olacak şekilde değiştirin. Bu kurulum şu şekilde yapılabilir:

- VM 'yi oluşturacak veri disklerini ekleyin.
- Bu diskleri biçimlendirin, bağlayın ve fstab dosyasına ekleyin.

    1. Yeni eklenen diski biçimlendirin. Azure tarafından oluşturulan symbağlantıları burada kullanırız. Çözümlemeyin 'in kullanılması, cihaz adları değiştirme ile ilgili sorunları önler. Daha fazla bilgi için bkz. [cihaz adları sorunlarını giderme](troubleshoot-device-names-problems.md) makalesi.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    1. Diskleri bağlayın.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    1. Fstab öğesine ekleyin.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    1. Bu diskleri şifrelemek için set-AzVMDiskEncryptionExtension PowerShell cmdlet 'ini-EncryptFormatAll ile çalıştırın.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Bu yeni disklerin üstünde LVM 'yi ayarlayın. VM 'nin önyüklemesi bittikten sonra şifrelenmiş sürücülerin kilidinin açılacağı unutulmamalıdır. Bu nedenle, LVM bağlama de daha sonra gecikecek.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Müşteri tarafından şifrelenen VHD ve şifreleme anahtarlarından oluşturulan yeni VM 'Ler
Bu senaryoda, PowerShell cmdlet 'lerini veya CLı komutlarını kullanarak şifrelemeyi etkinleştirebilirsiniz. 

Azure 'da kullanılabilen önceden şifrelenmiş görüntüleri hazırlamak için Azure disk şifrelemesi ile aynı betiklerdeki yönergeleri kullanın. Görüntü oluşturulduktan sonra, şifrelenmiş bir Azure VM oluşturmak için sonraki bölümdeki adımları kullanabilirsiniz.

* [Önceden şifrelenen bir Linux VHD hazırlama](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Yönetilen disk tabanlı VM örneğinin, Azure disk şifrelemesi etkinleştirilmeden önce ve/veya dışında bir şekilde anlık görüntü ve/veya yedeklenmesi zorunludur. Yönetilen diskin bir anlık görüntüsü portaldan alınabilir veya [Azure Backup](../../backup/backup-azure-vms-encryption.md) kullanılabilir. Yedeklemeler, şifreleme sırasında beklenmeyen bir hata olması durumunda kurtarma seçeneğinin yapılabilmesini sağlamaktır. Yedekleme yapıldıktan sonra,-skipVmBackup parametresini belirterek yönetilen diskleri şifrelemek için set-AzVMDiskEncryptionExtension cmdlet 'i kullanılabilir. Set-AzVMDiskEncryptionExtension komutu, bir yedekleme yapılıncaya ve bu parametre belirtilene kadar yönetilen disk tabanlı VM 'Lere karşı başarısız olur. 
>
> Şifrelemeyi şifrelemek veya devre dışı bırakmak VM 'nin yeniden başlatılmasına neden olabilir. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>VM 'Leri önceden şifrelenen VHD 'lerle şifrelemek için Azure PowerShell kullanma 
[Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples)PowerShell cmdlet 'ini kullanarak, şifrelenen VHD 'niz üzerinde disk şifrelemeyi etkinleştirebilirsiniz. Aşağıdaki örnek size bazı ortak parametreler vermektedir. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Yeni eklenen bir veri diskinde şifrelemeyi etkinleştir

[Az VM disk Attach](add-disk.md)veya [Azure Portal aracılığıyla](attach-disk-portal.md)yeni bir veri diski ekleyebilirsiniz. Şifrelemebilmeniz için önce yeni eklenen veri diskini bağlamanız gerekir. Şifreleme sürerken sürücü kullanılamaz hale gelediğinden, veri sürücüsünün şifrelemesini istemeniz gerekir. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLı ile yeni eklenen bir diskte şifrelemeyi etkinleştirme

 VM daha önce "All" ile şifrelendiyse,--Volume-Type parametresi "All" olarak kalmalıdır. Tümü hem işletim sistemi hem de veri disklerini içerir. VM daha önce "OS" birim türüyle şifrelendiyse, hem işletim sistemi hem de yeni veri diskinin dahil edilmesini sağlamak için--Volume-Type parametresi "All" olarak değiştirilmelidir. VM yalnızca "Data" birim türüyle şifrelendiyse, aşağıda gösterildiği gibi "veri" kalabilir. Yeni bir veri diski eklemek ve bir VM 'ye eklemek, şifreleme için yeterli hazırlık yapmamalıdır. Şifreleme etkinleştirilmeden önce, yeni eklenen diskin de biçimlendirilmesi ve sanal makine içinde düzgün şekilde bağlanması gerekir. Linux 'ta, diskin [kalıcı bir blok cihaz adıyla](troubleshoot-device-names-problems.md)/etc/fstab 'e bağlanması gerekir.  

PowerShell sözdiziminin aksine, CLı, kullanıcının şifrelemeyi etkinleştirirken benzersiz bir sıra sürümü sağlamasını gerektirmez. CLı otomatik olarak kendi benzersiz dizi sürümü değerini oluşturur ve kullanır.

-  **Çalışan bir VM 'nin veri birimlerini şifreleyin:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Çalışan bir VM 'nin veri birimlerini KEK kullanarak şifreleyin:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell ile yeni eklenen bir diskte şifrelemeyi etkinleştir
 Linux için yeni bir disk şifrelemek üzere PowerShell kullanırken yeni bir sıra sürümünün belirtilmesi gerekir. Sıra sürümü benzersiz olmalıdır. Aşağıdaki komut dosyası, dizi sürümü için bir GUID oluşturur. Diskler şifrelenmeden önce bir [anlık görüntü](snapshot-copy-managed-disk.md) alın ve/veya VM 'yi [Azure Backup](../../backup/backup-azure-vms-encryption.md) yedekleyin. Yeni eklenen bir veri diskini şifrelemek için PowerShell betiklerine-skipVmBackup parametresi zaten belirtilmiş.
 

-  **Çalışan BIR VM 'nin veri birimlerini şifreleyin:** Aşağıdaki komut dosyası değişkenlerinizi başlatır ve set-AzVMDiskEncryptionExtension cmdlet 'ini çalıştırır. Kaynak grubu, VM ve Anahtar Kasası zaten ön koşullar olarak oluşturulmuş olmalıdır. MyVirtualMachineResourceGroup, MySecureVM ve Mysecurekasasını değerlerinizle değiştirin. -VolumeType parametresi için kabul edilebilir değerler tümü, işletim sistemi ve veri. VM daha önce "OS" veya "All" birim türüyle şifrelendiyse, hem işletim sistemi hem de yeni veri diskinin dahil edilmesini sağlamak için-VolumeType parametresi "All" olarak değiştirilmelidir.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Çalışan BIR VM 'nin veri BIRIMLERINI kek kullanarak şifreleyin:** -VolumeType parametresi için kabul edilebilir değerler tümü, işletim sistemi ve veri. VM daha önce "OS" veya "All" birim türüyle şifrelendiyse, hem işletim sistemi hem de yeni veri diskinin dahil edilmesini sağlamak için-VolumeType parametresinin tümü olarak değiştirilmesi gerekir.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Disk-Encryption-keykasası parametresinin değeri için sözdizimi tam tanımlayıcı dizesidir:/Subscriptions/[Subscription-ID-Guid]/resourceGroups/[KVresource-Group-Name]/providers/Microsoft.KeyVault/vaults/[keykasaadı]</br> Anahtar şifreleme-anahtar parametresinin değeri için sözdizimi, KEK: https://[keykasaadı]. kasa. Azure. net/Keys/[kekname]/[kek-Unique-ID] ile 


## <a name="disable-encryption-for-linux-vms"></a>Linux sanal makineleri için şifrelemeyi devre dışı bırakma
Azure PowerShell, Azure CLı veya Kaynak Yöneticisi şablonuyla şifrelemeyi devre dışı bırakabilirsiniz. 

>[!IMPORTANT]
>Linux VM 'lerde Azure disk şifrelemesi ile şifrelemeyi devre dışı bırakmak yalnızca veri birimlerinde desteklenir. İşletim sistemi birimi şifrelendiyse veri veya işletim sistemi birimlerinde desteklenmez.  

- **Azure PowerShell ile disk şifrelemeyi devre dışı bırak:** Şifrelemeyi devre dışı bırakmak için [Disable-Azvmdiskencryptıon](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet 'ini kullanın. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Azure CLI ile şifrelemeyi devre dışı bırakın:** Şifrelemeyi devre dışı bırakmak için [az VM ENCRYPTION Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) komutunu kullanın. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Kaynak Yöneticisi şablonuyla şifrelemeyi devre dışı bırak:** Şifrelemeyi devre dışı bırakmak için [çalışan bir LINUX VM şablonunda şifrelemeyi devre dışı bırak '](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) i kullanın.
     1. **Azure’a dağıt**’a tıklayın.
     2. Abonelik, kaynak grubu, konum, VM, yasal koşullar ve Sözleşme ' yi seçin.

## <a name="unsupported-scenarios"></a>Desteklenmeyen senaryolar

Azure disk şifrelemesi, aşağıdaki Linux senaryoları, özellikleri ve teknolojileri için çalışmaz:

- Klasik VM oluşturma yöntemiyle oluşturulan temel katman VM veya VM 'Leri şifreleme.
- IŞLETIM sistemi sürücüsü şifrelendiğinde bir işletim sistemi sürücüsünde veya Linux sanal makinesinin veri sürücüsünde şifrelemeyi devre dışı bırakma.
- Linux sanal makine ölçek kümeleri için işletim sistemi sürücüsü şifreleme.
- Linux VM 'lerde özel görüntüleri şifreleme.
- Şirket içi anahtar yönetim sistemiyle tümleştirme.
- Azure dosyaları (paylaşılan dosya sistemi).
- Ağ dosya sistemi (NFS).
- Dinamik birimler.
- Kısa ömürlü işletim sistemi diskleri.
- Paylaşılan/dağıtılmış dosya sistemlerinin şifrelenmesi (ancak bunlarla sınırlı olmamak üzere): DFS, GFS, DRDB ve CephFS gibi.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure disk şifrelemeye genel bakış](disk-encryption-overview.md)
- [Azure disk şifrelemesi örnek betikleri](disk-encryption-sample-scripts.md)
- [Azure disk şifrelemesi sorunlarını giderme](disk-encryption-troubleshooting.md)
