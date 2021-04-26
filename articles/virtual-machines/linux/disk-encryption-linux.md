---
title: Linux VM’lerde Azure Disk Şifrelemesi senaryoları
description: Bu makale çeşitli senaryolar için Linux VM 'Leri için Microsoft Azure disk şifrelemeyi etkinleştirme hakkında yönergeler sağlar
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f014c07a319cbb07497cba01699b93d092255b93
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771518"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Linux VM’lerde Azure Disk Şifrelemesi senaryoları

Linux sanal makineleri (VM 'Ler) için Azure disk şifrelemesi, işletim sistemi diski ve veri disklerinin tam disk şifrelemesini sağlamak için Linux 'un DM-Crypt özelliğini kullanır. Ayrıca, EncryptFormatAll özelliği kullanılırken geçici disk şifrelemesi de sağlar.

Azure disk şifrelemesi, disk şifreleme anahtarlarını ve gizli dizileri denetlemenize ve yönetmenize yardımcı olmak için [Azure Key Vault ile tümleşiktir](disk-encryption-key-vault.md) . Hizmete genel bakış için bkz. [Linux VM 'leri Için Azure disk şifrelemesi](disk-encryption-overview.md).

Yalnızca [desteklenen VM boyutlarının ve işletim sistemlerinin](disk-encryption-overview.md#supported-vms-and-operating-systems)sanal makinelerine disk şifrelemesi uygulayabilirsiniz. Ayrıca aşağıdaki önkoşulları karşılamanız gerekir:

- [VM 'Ler için ek gereksinimler](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ağ gereksinimleri](disk-encryption-overview.md#networking-requirements)
- [Şifreleme anahtarı depolama gereksinimleri](disk-encryption-overview.md#encryption-key-storage-requirements)

Her durumda, [bir anlık görüntü](snapshot-copy-managed-disk.md) almalısınız ve/veya diskler şifrelenmeden önce bir yedek oluşturmalısınız. Yedeklemeler, şifreleme sırasında beklenmeyen bir hata oluşursa, kurtarma seçeneğinin mümkün olmasını güvence altına almanıza olanak tanır. Yönetilen disklere sahip VM 'Ler şifreleme gerçekleşmeden önce bir yedekleme gerektirir. Bir yedekleme yapıldıktan sonra,-skipVmBackup parametresini belirterek yönetilen diskleri şifrelemek için [set-AzVMDiskEncryptionExtension cmdlet 'ini](/powershell/module/az.compute/set-azvmdiskencryptionextension) kullanabilirsiniz. Şifrelenmiş VM 'Leri yedekleme ve geri yükleme hakkında daha fazla bilgi için [Azure Backup](../../backup/backup-azure-vms-encryption.md) makalesine bakın. 

>[!WARNING]
> - Bir VM 'yi şifrelemek için Azure AD ile Azure disk şifrelemesi 'ni daha önce kullandıysanız, VM 'nizi şifrelemek için bu seçeneği kullanmaya devam etmeniz gerekir. Ayrıntılar için bkz. [Azure AD ile Azure disk şifrelemesi (önceki sürüm)](disk-encryption-overview-aad.md) . 
>
> - Linux işletim sistemi birimlerini şifrelerken, sanal makinenin kullanılamaz kabul edilmesi gerekir. Şifreleme işlemi sırasında erişilmesi gereken açık dosyaları engelleyen sorunlardan kaçınmak için şifreleme sürerken SSH oturum açma işlemlerini kesinlikle öneririz. İlerlemeyi denetlemek için [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell cmdlet 'ini veya [VM şifrelemesi Show](/cli/azure/vm/encryption#az_vm_encryption_show) CLI komutunu kullanın. Bu işlemin, bir 30 GB işletim sistemi birimi için birkaç saat, ayrıca veri birimlerini şifrelemek için de ek süre gelmesi beklenmelidir. Şifreleme biçimi ALL seçeneği kullanılmamışsa, veri hacmi şifreleme süresi veri birimlerinin boyutuyla ve miktarıyla orantılıdır. 
> - Linux VM 'lerinde şifrelemeyi devre dışı bırakmak yalnızca veri birimlerinde desteklenir. İşletim sistemi birimi şifrelendiyse veri veya işletim sistemi birimlerinde desteklenmez.  

## <a name="install-tools-and-connect-to-azure"></a>Araçları yükleyip Azure 'a bağlanın

Azure disk şifrelemesi, [Azure CLI](/cli/azure) ve [Azure PowerShell](/powershell/azure/new-azureps-module-az)aracılığıyla etkinleştirilebilir ve yönetilebilir. Bunu yapmak için, araçları yerel olarak yüklemeli ve Azure aboneliğinize bağlamanız gerekir.

### <a name="azure-cli"></a>Azure CLI

[Azure clı 2,0](/cli/azure) , Azure kaynaklarını yönetmeye yönelik bir komut satırı aracıdır. CLı, verileri esnek bir şekilde sorgulamak, engelleyici olmayan işlemler olarak uzun süreli işlemleri desteklemek ve komut dosyasını kolay hale getirmek için tasarlanmıştır. [Azure CLI 'Yı yüklemeye](/cli/azure/install-azure-cli)ilişkin adımları izleyerek yerel olarak yükleyebilirsiniz.

 

Azure [CLI Ile Azure hesabınızda oturum açmak](/cli/azure/authenticate-azure-cli)için [az Login](/cli/azure/reference-index#az_login) komutunu kullanın.

```azurecli
az login
```

Oturum açmak için bir kiracı seçmek istiyorsanız şunu kullanın:
    
```azurecli
az login --tenant <tenant>
```

Birden çok aboneliğiniz varsa ve belirli bir tane belirtmek istiyorsanız, [az Account List](/cli/azure/account#az_account_list) komutuyla abonelik listenizi alın ve [az Account set](/cli/azure/account#az_account_set)ile belirtin.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Daha fazla bilgi için bkz. [Azure CLI ile çalışmaya başlama 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az Module](/powershell/azure/new-azureps-module-az) , Azure kaynaklarınızı yönetmek için [Azure Resource Manager](../../azure-resource-manager/management/overview.md) modelini kullanan bir cmdlet kümesi sağlar. Bunu tarayıcınızda [Azure Cloud Shell](../../cloud-shell/overview.md)ile kullanabilir veya [Azure PowerShell modülünü yüklemeye](/powershell/azure/install-az-ps)ilişkin yönergeleri kullanarak yerel makinenize yükleyebilirsiniz. 

Yerel olarak zaten yüklüyse, Azure disk şifrelemesini yapılandırmak için Azure PowerShell SDK sürümünün en son sürümünü kullandığınızdan emin olun. [Azure PowerShell sürümünün](https://github.com/Azure/azure-powershell/releases)en son sürümünü indirin.

[Azure hesabınızda Azure PowerShell oturum açmak](/powershell/azure/authenticate-azureps)için [Connect-azaccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'ini kullanın.

```powershell
Connect-AzAccount
```

Birden çok aboneliğiniz varsa ve bir tane belirtmek istiyorsanız, [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) cmdlet 'ini kullanarak bunları listeleyin ve ardından [set-azcontext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanın:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

[Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) cmdlet 'ini çalıştırmak, doğru aboneliğin seçildiğini doğrular.

Azure disk şifrelemesi cmdlet 'lerinin yüklendiğini doğrulamak için [Get-Command](/powershell/module/microsoft.powershell.core/get-command) cmdlet 'ini kullanın:
     
```powershell
Get-command *diskencryption*
```
Daha fazla bilgi için bkz. [Azure PowerShell kullanmaya](/powershell/azure/get-started-azureps)başlama. 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Mevcut veya çalışan bir Linux VM 'de şifrelemeyi etkinleştirme
Bu senaryoda, Kaynak Yöneticisi şablonu, PowerShell cmdlet 'leri veya CLı komutlarını kullanarak şifrelemeyi etkinleştirebilirsiniz. Sanal makine uzantısı için şema bilgilerine ihtiyacınız varsa, [Linux uzantısı Için Azure disk şifrelemesi](../extensions/azure-disk-enc-linux.md) makalesine bakın.

>[!IMPORTANT]
 >Yönetilen disk tabanlı VM örneğinin, Azure disk şifrelemesi etkinleştirilmeden önce ve/veya dışında bir şekilde anlık görüntü ve/veya yedeklenmesi zorunludur. Yönetilen diskin bir anlık görüntüsü portaldan veya [Azure Backup](../../backup/backup-azure-vms-encryption.md)aracılığıyla alınabilir. Yedeklemeler, şifreleme sırasında beklenmeyen bir hata olması durumunda kurtarma seçeneğinin yapılabilmesini sağlamaktır. Yedekleme yapıldıktan sonra, Set-AzVMDiskEncryptionExtension cmdlet 'i-skipVmBackup parametresini belirterek yönetilen diskleri şifrelemek için kullanılabilir. Yedekleme yapıldıktan ve bu parametre belirtilene kadar Set-AzVMDiskEncryptionExtension komutu yönetilen disk tabanlı VM 'Lere karşı başarısız olur. 
>
>Şifrelemeyi şifrelemek veya devre dışı bırakmak VM 'nin yeniden başlatılmasına neden olabilir. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Azure CLı kullanarak mevcut veya çalışan bir Linux VM 'de şifrelemeyi etkinleştirme 

[Azure CLI](/cli/azure/) komut satırı aracını yükleyip kullanarak, şifrelenmiş VHD 'niz üzerinde disk şifrelemeyi etkinleştirebilirsiniz. [Azure Cloud Shell](../../cloud-shell/overview.md) ile tarayıcınızda kullanabilir veya yerel makinenize yükleyip herhangi bir PowerShell oturumunda kullanabilirsiniz. Azure 'da mevcut veya çalışan Linux VM 'lerinde şifrelemeyi etkinleştirmek için aşağıdaki CLı komutlarını kullanın:

Azure 'da çalışan bir sanal makinede şifrelemeyi etkinleştirmek için [az VM Encryption Enable](/cli/azure/vm/encryption#az_vm_encryption_show) komutunu kullanın.

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

- **Disklerin şifrelendiğini doğrulayın:** Bir sanal makinenin şifreleme durumunu denetlemek için [az VM Encryption Show](/cli/azure/vm/encryption#az_vm_encryption_show) komutunu kullanın. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Şifrelemeyi devre dışı bırak:** Şifrelemeyi devre dışı bırakmak için [az VM ENCRYPTION Disable](/cli/azure/vm/encryption#az_vm_encryption_disable) komutunu kullanın. Şifrelemeyi devre dışı bırakmak yalnızca Linux sanal makineleri için veri birimlerinde kullanılabilir.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>PowerShell kullanarak mevcut veya çalışan bir Linux VM 'de şifrelemeyi etkinleştirme
Azure 'da çalışan bir sanal makinede şifrelemeyi etkinleştirmek için [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet 'ini kullanın. Diskler şifrelenmeden önce bir [anlık görüntü](snapshot-copy-managed-disk.md) alın ve/veya VM 'yi [Azure Backup](../../backup/backup-azure-vms-encryption.md) yedekleyin. -SkipVmBackup parametresi, çalışan bir Linux sanal makinesini şifrelemek için PowerShell betiklerine zaten belirtildi.

-  **Çalışan bir VM 'Yi şifreleyin:** Aşağıdaki komut dosyası değişkenlerinizi başlatır ve Set-AzVMDiskEncryptionExtension cmdlet 'ini çalıştırır. Kaynak grubu, VM ve Anahtar Kasası, ön koşullar olarak oluşturulmuştur. MyVirtualMachineResourceGroup, MySecureVM ve Mysecurekasasını değerlerinizle değiştirin. Hangi diskleri şifrelediğiniz belirlemek için-VolumeType parametresini değiştirin.

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
| keyVaultName | Şifreleme anahtarının karşıya yüklenmesi gereken anahtar kasasının adı. Bunu, cmdlet 'ini `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` veya Azure CLI komutunu kullanarak edinebilirsiniz `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` .|
| keyVaultResourceGroup | Anahtar kasasını içeren kaynak grubunun adı. |
|  keyEncryptionKeyURL 'Si | Şifreleme anahtarını şifrelemek için kullanılan anahtar şifreleme anahtarının URL 'SI. UseExistingKek açılır listesinde **nokek** ' ı seçerseniz bu parametre isteğe bağlıdır. UseExistingKek açılır listesinde **kek** ' yi seçerseniz _Keyencryptionkeyurl_ değerini girmeniz gerekir. |
| Birimtürü | Şifreleme işleminin gerçekleştirildiği birimin türü. Geçerli değerler _Işletim sistemi_, _veri_ ve _hepsi_. 
| forceUpdateTag | İşlemin zorla çalışması gereken her seferinde GUID gibi benzersiz bir değer geçirin. |
| location | Tüm kaynakların konumu. |

Linux VM disk şifrelemesi şablonunu yapılandırma hakkında daha fazla bilgi için bkz. [Linux Için Azure disk şifrelemesi](../extensions/azure-disk-enc-linux.md).

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Linux VM 'lerinde veri diskleri için EncryptFormatAll özelliğini kullanın

**Encryptformatall** parametresi, Linux veri disklerinin şifrelenme süresini azaltır. Belirli ölçütlere uyan bölümler, geçerli dosya sistemleriyle birlikte biçimlendirilir ve ardından komut yürütmeden önce oldukları yere yeniden takılacaktır. Ölçütlere uyan bir veri diskini dışlamak istiyorsanız, komutu çalıştırmadan önce onu kaldırabilirsiniz.

 Bu komutu çalıştırdıktan sonra, daha önce bağlanan tüm sürücüler biçimlendirilir ve şifreleme katmanı artık boş sürücünün üstünde başlatılır. Bu seçenek belirlendiğinde, VM 'ye bağlı geçici disk da şifrelenir. Geçici disk sıfırlandığında, sonraki fırsatta Azure disk şifrelemesi çözümü tarafından sanal makine için yeniden biçimlendirilir ve yeniden şifrelenir. Kaynak disk şifrelendikten sonra, [Microsoft Azure Linux Aracısı](../extensions/agent-linux.md) kaynak diski yönetemez ve takas dosyasını etkinleştiremeyecektir, ancak takas dosyasını el ile yapılandırabilirsiniz.

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
Azure 'da çalışan bir sanal makinede şifrelemeyi etkinleştirmek için [az VM Encryption Enable](/cli/azure/vm/encryption#az_vm_encryption_enable) komutunu kullanın.

-  **Çalışan bir VM 'yi EncryptFormatAll kullanarak şifreleyin:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Bir PowerShell cmdlet 'i ile EncryptFormatAll parametresini kullanma
EncryptFormatAll parametresiyle [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet 'ini kullanın. 

**Çalışan bir VM 'Yi EncryptFormatAll kullanarak şifreleyin:** Örnek olarak, aşağıdaki komut dosyası değişkenlerinizi başlatır ve Set-AzVMDiskEncryptionExtension cmdlet 'ini EncryptFormatAll parametresiyle çalıştırır. Kaynak grubu, VM ve Anahtar Kasası, ön koşullar olarak oluşturulmuştur. MyVirtualMachineResourceGroup, MySecureVM ve Mysecurekasasını değerlerinizle değiştirin.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Mantıksal birim Yöneticisi (LVM) ile EncryptFormatAll parametresini kullanın 
Bir LVM-şifreli kurulum önerilir. Aşağıdaki tüm örneklerde, cihaz yolu ve bağlama noktalarını, kullanım örneklerinize uygun olacak şekilde değiştirin. Bu kurulum şu şekilde yapılabilir:

1.  VM 'yi oluşturacak veri disklerini ekleyin.

1. Bu diskleri biçimlendirin, bağlayın ve fstab dosyasına ekleyin.

1. Bölüm standardı seçin, tüm sürücüyü kapsayan bir bölüm oluşturun ve sonra bölümü biçimlendirin. Azure tarafından oluşturulan symbağlantıları burada kullanırız. Çözümlemeyin 'in kullanılması, cihaz adları değiştirme ile ilgili sorunları önler. Daha fazla bilgi için bkz. [cihaz adları sorunlarını giderme](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems) makalesi.
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. Diskleri bağlama:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    Fstab dosyasına Ekle:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Bu diskleri şifrelemek için-EncryptFormatAll ile Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet 'ini çalıştırın.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Anahtar şifreleme anahtarı (KEK) kullanmak istiyorsanız, KEK 'nin URI 'sini ve anahtar kasanızın RESOURCEID değerini sırasıyla-KeyEncryptionKeyUrl ve-Keyencryptionkeyvaultıd parametrelerine geçirin:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Bu yeni disklerin üstünde LVM 'yi ayarlayın. VM 'nin önyüklemesi bittikten sonra şifrelenmiş sürücülerin kilidinin açılacağı unutulmamalıdır. Bu nedenle, LVM bağlama de daha sonra gecikecek.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Müşteri tarafından şifrelenen VHD ve şifreleme anahtarlarından oluşturulan yeni VM 'Ler
Bu senaryoda, PowerShell cmdlet 'lerini veya CLı komutlarını kullanarak şifrelemeyi etkinleştirebilirsiniz. 

Azure 'da kullanılabilen önceden şifrelenmiş görüntüleri hazırlamak için Azure disk şifrelemesi ile aynı betiklerdeki yönergeleri kullanın. Görüntü oluşturulduktan sonra, şifrelenmiş bir Azure VM oluşturmak için sonraki bölümdeki adımları kullanabilirsiniz.

* [Önceden şifrelenen bir Linux VHD hazırlama](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Yönetilen disk tabanlı VM örneğinin, Azure disk şifrelemesi etkinleştirilmeden önce ve/veya dışında bir şekilde anlık görüntü ve/veya yedeklenmesi zorunludur. Yönetilen diskin bir anlık görüntüsü portaldan alınabilir veya [Azure Backup](../../backup/backup-azure-vms-encryption.md) kullanılabilir. Yedeklemeler, şifreleme sırasında beklenmeyen bir hata olması durumunda kurtarma seçeneğinin yapılabilmesini sağlamaktır. Yedekleme yapıldıktan sonra, Set-AzVMDiskEncryptionExtension cmdlet 'i-skipVmBackup parametresini belirterek yönetilen diskleri şifrelemek için kullanılabilir. Yedekleme yapıldıktan ve bu parametre belirtilene kadar Set-AzVMDiskEncryptionExtension komutu yönetilen disk tabanlı VM 'Lere karşı başarısız olur. 
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

 VM daha önce "All" ile şifrelendiyse,--Volume-Type parametresi "All" olarak kalmalıdır. Tümü hem işletim sistemi hem de veri disklerini içerir. VM daha önce "OS" birim türüyle şifrelendiyse, hem işletim sistemi hem de yeni veri diskinin dahil edilmesini sağlamak için--Volume-Type parametresi "All" olarak değiştirilmelidir. VM yalnızca "Data" birim türüyle şifrelendiyse, aşağıda gösterildiği gibi "veri" kalabilir. Yeni bir veri diski eklemek ve bir VM 'ye eklemek, şifreleme için yeterli hazırlık yapmamalıdır. Şifreleme etkinleştirilmeden önce, yeni eklenen diskin de biçimlendirilmesi ve sanal makine içinde düzgün şekilde bağlanması gerekir. Linux 'ta, diskin [kalıcı bir blok cihaz adıyla](../troubleshooting/troubleshoot-device-names-problems.md)/etc/fstab 'e bağlanması gerekir.  

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
 

-  **Çalışan BIR VM 'nin veri birimlerini şifreleyin:** Aşağıdaki komut dosyası değişkenlerinizi başlatır ve Set-AzVMDiskEncryptionExtension cmdlet 'ini çalıştırır. Kaynak grubu, VM ve Anahtar Kasası zaten ön koşullar olarak oluşturulmuş olmalıdır. MyVirtualMachineResourceGroup, MySecureVM ve Mysecurekasasını değerlerinizle değiştirin. -VolumeType parametresi için kabul edilebilir değerler tümü, işletim sistemi ve veri. VM daha önce "OS" veya "All" birim türüyle şifrelendiyse, hem işletim sistemi hem de yeni veri diskinin dahil edilmesini sağlamak için-VolumeType parametresi "All" olarak değiştirilmelidir.

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
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Desteklenmeyen senaryolar

Azure disk şifrelemesi, aşağıdaki Linux senaryoları, özellikleri ve teknolojileri için çalışmaz:

- Klasik VM oluşturma yöntemiyle oluşturulan temel katman VM veya VM 'Leri şifreleme.
- IŞLETIM sistemi sürücüsü şifrelendiğinde bir işletim sistemi sürücüsünde veya Linux sanal makinesinin veri sürücüsünde şifrelemeyi devre dışı bırakma.
- Linux sanal makine ölçek kümeleri için işletim sistemi sürücüsü şifreleniyor.
- Linux VM 'lerde özel görüntüleri şifreleme.
- Şirket içi anahtar yönetim sistemiyle tümleştirme.
- Azure dosyaları (paylaşılan dosya sistemi).
- Ağ dosya sistemi (NFS).
- Dinamik birimler.
- Kısa ömürlü işletim sistemi diskleri.
- Paylaşılan/dağıtılmış dosya sistemlerinin şifrelenmesi (ancak bunlarla sınırlı olmamak üzere): DFS, GFS, DRDB ve CephFS gibi.
- Şifrelenmiş bir VM 'yi başka bir aboneliğe veya bölgeye taşıma.
- Şifrelenmiş bir sanal makinenin görüntüsünü veya anlık görüntüsünü oluşturma ve ek VM 'Leri dağıtmak için kullanma.
- Çekirdek kilitlenme dökümü (kdump).
- Oracle ACFS (ASM küme dosyası sistemi).
- Lsv2 serisi VM 'lerinin NVMe diskleri (bkz: [Lsv2-Series](../lsv2-series.md)).
- "İç içe bağlama noktaları" olan bir VM. diğer bir deyişle, tek bir yolda birden çok bağlama noktası ("/1stmountpoint/Data/2stbağlamanoktası" gibi).
- Bir işletim sistemi klasörünün üzerine bağlanmış bir veri sürücüsüne sahip bir VM.
- Bir kök (işletim sistemi diski) mantıksal biriminin bir veri diski kullanılarak genişletilmiş olduğu VM.
- Yazma Hızlandırıcısı disklere sahip, d serisi VM 'Ler.
- Müşteri tarafından yönetilen anahtarlarla (SSE + CMK) [sunucu tarafı şifrelemesiyle](../disk-encryption.md) şifrelenmiş diskler IÇEREN bir VM 'ye Ade uygulama. ADE ile şifrelenen bir VM üzerindeki bir veri diskine SSE + CMK uygulamak, desteklenmeyen bir senaryodur.
- ADE ile şifrelenen veya **herhangi** bır zaman Ade ile ŞIFRELENEN bir VM 'yi, [müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifrelemeye](../disk-encryption.md)geçirme.
- Yük devretme kümelerinde VM 'Leri şifreleme.
- [Azure Ultra disklerin](../disks-enable-ultra-ssd.md)şifrelenmesi.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure disk şifrelemeye genel bakış](disk-encryption-overview.md)
- [Azure Disk Şifrelemesi örnek betikleri](disk-encryption-sample-scripts.md)
- [Azure Disk Şifrelemesi sorunlarını giderme](disk-encryption-troubleshooting.md)