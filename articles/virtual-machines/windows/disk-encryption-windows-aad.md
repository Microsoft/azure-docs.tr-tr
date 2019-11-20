---
title: Windows VM 'Leri için Azure AD ile Azure disk şifrelemesi (önceki sürüm)
description: Bu makalede, Windows IaaS VM 'Leri için Microsoft Azure disk şifrelemeyi etkinleştirme yönergeleri sunulmaktadır.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: d06be85e4d18bc0867835a307b44ec8813c79d7d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245012"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Windows VM 'Leri için Azure AD ile Azure disk şifrelemesi (önceki sürüm)

**Azure disk şifrelemesi 'nin yeni sürümü, VM disk şifrelemeyi etkinleştirmek için bir Azure AD uygulama parametresi sağlama gereksinimini ortadan kaldırır. Yeni sürümde, artık şifrelemeyi Etkinleştir adımını kullanarak Azure AD kimlik bilgilerini sağlamanız gerekmez. Yeni sürüm kullanılarak Azure AD uygulama parametreleri olmadan tüm yeni VM 'Ler şifrelenmelidir. Yeni sürümü kullanarak VM disk şifrelemesini etkinleştirme yönergelerini görüntülemek için bkz. [Windows VM 'leri Için Azure disk şifrelemesi](disk-encryption-windows.md). Azure AD uygulama parametreleriyle zaten şifrelenmiş VM 'Ler hala desteklenmektedir ve AAD sözdizimi ile sürdürülmeye devam etmelidir.**


Birçok disk şifreleme senaryosunu etkinleştirebilirsiniz ve adımlar senaryoya göre farklılık gösterebilir. Aşağıdaki bölümlerde Windows IaaS VM 'Leri için daha ayrıntılı senaryolar ele alınmaktadır. Disk şifrelemeyi kullanabilmeniz için önce [Azure disk şifrelemesi önkoşullarının](disk-encryption-overview-aad.md) tamamlanması gerekir. 


>[!IMPORTANT]
> - Diskler şifrelenmeden önce [bir anlık görüntü alıp](snapshot-copy-managed-disk.md) /veya bir yedek oluşturmalısınız. Yedeklemeler, şifreleme sırasında beklenmeyen bir hata oluşursa, kurtarma seçeneğinin mümkün olmasını güvence altına almanıza olanak tanır. Yönetilen disklere sahip VM 'Ler şifreleme gerçekleşmeden önce bir yedekleme gerektirir. Bir yedekleme yapıldıktan sonra,-skipVmBackup parametresini belirterek yönetilen diskleri şifrelemek için [set-AzVMDiskEncryptionExtension cmdlet 'ini](/powershell/module/az.compute/set-azvmdiskencryptionextension) kullanabilirsiniz. Şifrelenmiş VM 'Leri yedekleme ve geri yükleme hakkında daha fazla bilgi için bkz. [şifrelenmiş Azure VM 'Yi yedekleme ve geri yükleme](../../backup/backup-azure-vms-encryption.md). 
>
> - Şifrelemeyi şifrelemek veya devre dışı bırakmak bir sanal makinenin yeniden başlatılmasına neden olabilir.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Market 'ten oluşturulan yeni IaaS sanal makinelerinde şifrelemeyi etkinleştirme
Kaynak Yöneticisi şablonu kullanarak Azure 'daki Market 'ten yeni IaaS Windows VM 'de disk şifrelemeyi etkinleştirebilirsiniz. Şablon, Windows Server 2012 Galeri görüntüsünü kullanarak yeni bir şifrelenmiş Windows VM oluşturur.

1. [Kaynak Yöneticisi şablonunda](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) **Azure 'a dağıt**' a tıklayın.

2. Abonelik, kaynak grubu, kaynak grubu konumu, parametreler, yasal koşullar ve Sözleşme ' yi seçin. Şifrelemenin etkinleştirildiği yeni bir IaaS VM 'si dağıtmak için **satın al** ' a tıklayın.

3. Şablonu dağıttıktan sonra, tercih ettiğiniz yöntemi kullanarak VM şifreleme durumunu doğrulayın:
     - [Az VM Encryption Show](/cli/azure/vm/encryption#az-vm-encryption-show) komutunu kullanarak Azure CLI ile doğrulayın. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet 'ini kullanarak Azure PowerShell ile doğrulayın. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  VM 'yi seçin ve ardından portalda şifreleme durumunu doğrulamak için **Ayarlar** başlığı altında **diskler** ' e tıklayın. **Şifreleme**altındaki grafikte etkinleştirilip etkinleştirilmediğini görürsünüz. 
           ![Azure portalı-disk şifrelemesi etkin @ no__t-1

Aşağıdaki tabloda Azure AD istemci KIMLIĞI kullanılarak Market senaryosundan yeni VM 'Ler için Kaynak Yöneticisi şablon parametreleri listelenmektedir:

| Parametre | Açıklama | 
| --- | --- |
| adminUserName | Sanal makine için Yönetici Kullanıcı adı. |
| adminPassword | Sanal makine için Yönetici Kullanıcı parolası. |
| newStorageAccountName | İşletim sisteminin ve veri VHD 'lerinin depolandığı depolama hesabının adı. |
| vmSize | VM 'nin boyutu. Şu anda yalnızca standart A, D ve G serisi destekleniyor. |
| virtualNetworkName | VM NIC 'sinin ait olacağı VNet 'in adı. |
| subnetName | VM NIC 'sinin ait olması gereken VNet 'teki alt ağın adı. |
| Aadclientıd | Anahtar kasanıza gizli diziler yazma izinleri olan Azure AD uygulamasının istemci KIMLIĞI. |
| AADClientSecret | Anahtar kasanıza gizli diziler yazma izinleri olan Azure AD uygulamasının istemci gizli anahtarı. |
| keyVaultURL | BitLocker anahtarının yüklenmesi gereken anahtar kasasının URL 'SI. Bunu, `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` veya Azure CLı `az keyvault show --name "MySecureVault" --query properties.vaultUri` cmdlet 'ini kullanarak alabilirsiniz. |
| keyEncryptionKeyURL 'Si | Oluşturulan BitLocker anahtarını şifrelemek için kullanılan anahtar şifreleme anahtarının URL 'SI (isteğe bağlı). </br> </br>KeyEncryptionKeyURL isteğe bağlı bir parametredir. Anahtar Kasanızda veri şifreleme anahtarını (parola gizli dizisi) daha fazla korumak için kendi KEK getirebilirsiniz. |
| keyVaultResourceGroup | Anahtar kasasının kaynak grubu. |
| vmName | Şifreleme işleminin gerçekleştirileceği sanal makinenin adı. |


## <a name="bkmk_RunningWinVM"></a>Mevcut veya çalışan IaaS Windows VM 'lerinde şifrelemeyi etkinleştirme
Bu senaryoda, bir şablon, PowerShell cmdlet 'leri veya CLı komutları kullanarak şifrelemeyi etkinleştirebilirsiniz. Aşağıdaki bölümlerde Azure disk şifrelemesini etkinleştirme hakkında daha ayrıntılı bilgi verilmektedir. 


### <a name="bkmk_RunningWinVMPSH"></a>Azure PowerShell ile mevcut veya çalışan VM 'lerde şifrelemeyi etkinleştirme 
Azure 'da çalışan bir IaaS sanal makinesinde şifrelemeyi etkinleştirmek için [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet 'ini kullanın. PowerShell cmdlet 'lerini kullanarak Azure disk şifrelemesi ile şifrelemeyi etkinleştirme hakkında bilgi için, bkz. blog gönderileri [Azure PowerShell-Bölüm 1 Ile Azure disk şifrelemesini keşfedebilir](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) ve [Azure PowerShell-Bölüm 2 Ile Azure disk şifrelemesini keşfedebilir](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Çalışan bir VM 'yi bir istemci parolası kullanarak şifreleyin:** Aşağıdaki komut dosyası değişkenlerinizi başlatır ve set-AzVMDiskEncryptionExtension cmdlet 'ini çalıştırır. Kaynak grubu, VM, Anahtar Kasası, AAD uygulaması ve istemci parolası zaten ön koşullar olarak oluşturulmuş olmalıdır. MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, Mysecurekasası, My-AAD-Client-ID ve My-AAD-Client-Secret değerlerini değerlerinizle değiştirin.
     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **İstemci gizliliğini kaydırmak IÇIN kek kullanarak çalışan bir VM 'Yi şifreleyin:** Azure disk şifrelemesi, şifreleme etkinleştirilirken oluşturulan disk şifreleme gizli dizilerini kaydırmak için Anahtar Kasanızda mevcut bir anahtar belirtmenize olanak tanır. Anahtar şifreleme anahtarı belirtildiğinde Azure disk şifrelemesi, Key Vault yazmadan önce şifreleme gizli dizilerini kaydırmak için bu anahtarı kullanır. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Disk-Encryption-keykasası parametresinin değeri için sözdizimi tam tanımlayıcı dizesidir:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[keykasaadı]</br> Anahtar şifreleme-anahtar parametresinin değeri için sözdizimi, KEK: https://[keykasaadı]. kasa. Azure. net/Keys/[kekname]/[kek-Unique-ID] ile 

- **Disklerin şifrelendiğini doğrulayın:** IaaS VM 'sinin şifreleme durumunu denetlemek için [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet 'ini kullanın. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Disk şifrelemesini devre dışı bırak:** Şifrelemeyi devre dışı bırakmak için [Disable-Azurermvmdiskencryptıon](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet 'ini kullanın. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Azure CLı ile mevcut veya çalışan VM 'lerde şifrelemeyi etkinleştirme
Azure 'da çalışan bir IaaS sanal makinesinde şifrelemeyi etkinleştirmek için [az VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) komutunu kullanın.

- **Çalışan bir VM 'yi bir istemci parolası kullanarak şifreleyin:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **İstemci gizliliğini kaydırmak için KEK kullanarak çalışan bir VM 'yi şifreleyin:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Disk-Encryption-keykasası parametresinin değeri için sözdizimi tam tanımlayıcı dizesidir:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[keykasaadı] </br> Anahtar şifreleme-anahtar parametresinin değeri için sözdizimi, KEK: https://[keykasaadı]. kasa. Azure. net/Keys/[kekname]/[kek-Unique-ID] ile 

- **Disklerin şifrelendiğini doğrulayın:** IaaS VM 'sinin şifreleme durumunu denetlemek için [az VM Encryption Show](/cli/azure/vm/encryption#az-vm-encryption-show) komutunu kullanın. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Şifrelemeyi devre dışı bırak:** Şifrelemeyi devre dışı bırakmak için [az VM ENCRYPTION Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) komutunu kullanın. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="bkmk_RunningWinVMwRM"> </a>Kaynak Yöneticisi şablonunu kullanma
[Çalışan bir WINDOWS VM 'yi şifrelemek için Kaynak Yöneticisi şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)kullanarak Azure 'da var olan veya çalışan IaaS Windows VM 'lerinde disk şifrelemeyi etkinleştirebilirsiniz.


1. Azure hızlı başlangıç şablonunda **Azure 'A dağıt**' a tıklayın.

2. Abonelik, kaynak grubu, kaynak grubu konumu, parametreler, yasal koşullar ve Sözleşme ' yi seçin. Mevcut veya çalışan IaaS VM üzerinde şifrelemeyi etkinleştirmek için **satın al** ' a tıklayın.

Aşağıdaki tabloda, bir Azure AD istemci KIMLIĞI kullanan mevcut veya çalışan VM 'Ler için Kaynak Yöneticisi şablon parametreleri listelenmektedir:

| Parametre | Açıklama |
| --- | --- |
| Aadclientıd | Anahtar kasasına gizli diziler yazma izinleri olan Azure AD uygulamasının istemci KIMLIĞI. |
| AADClientSecret | Anahtar kasasına gizli diziler yazma izinleri olan Azure AD uygulamasının istemci gizli anahtarı. |
| keyVaultName | BitLocker anahtarının yüklenmesi gereken anahtar kasasının adı. Bunu, `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` veya Azure CLı komutu olan `az keyvault list --resource-group "MySecureGroup"` ' i kullanarak alabilirsiniz.|
|  keyEncryptionKeyURL 'Si | Oluşturulan BitLocker anahtarını şifrelemek için kullanılan anahtar şifreleme anahtarının URL 'SI. UseExistingKek açılır listesinde **nokek** ' ı seçerseniz bu parametre isteğe bağlıdır. UseExistingKek açılır listesinde **kek** ' yi seçerseniz _Keyencryptionkeyurl_ değerini girmeniz gerekir. |
| Birimtürü | Şifreleme işleminin gerçekleştirildiği birimin türü. Geçerli değerler _Işletim sistemi_, _veri_ve _hepsi_. |
| sequenceVersion | BitLocker işleminin sıra sürümü. Aynı VM 'de her disk şifreleme işlemi gerçekleştirildiğinde bu sürüm numarasını artırın. |
| vmName | Şifreleme işleminin gerçekleştirileceği sanal makinenin adı. |


## <a name="bkmk_VHDpre"> </a>Müşteri tarafından şifrelenen VHD ve şifreleme anahtarlarından oluşturulan yeni IaaS VM 'leri
Bu senaryoda, Kaynak Yöneticisi şablonu, PowerShell cmdlet 'leri veya CLı komutlarını kullanarak şifrelemeyi etkinleştirebilirsiniz. Aşağıdaki bölümlerde Kaynak Yöneticisi şablonu ve CLı komutlarının daha ayrıntılı bir şekilde açıklanmaktadır. 

Azure 'da kullanılabilen önceden şifrelenmiş görüntüleri hazırlama ekinde yer alan yönergeleri kullanın. Görüntü oluşturulduktan sonra, şifrelenmiş bir Azure VM oluşturmak için sonraki bölümdeki adımları kullanabilirsiniz.

* [Önceden şifrelenmiş bir Windows VHD hazırlama](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="bkmk_VHDprePSH"></a> Azure PowerShell ile önceden şifrelenen VHD 'Ler Ile VM 'leri şifreleme
[Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)PowerShell cmdlet 'ini kullanarak, şifrelenen VHD 'niz üzerinde disk şifrelemeyi etkinleştirebilirsiniz. Aşağıdaki örnek size bazı ortak parametreler vermektedir. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Yeni eklenen bir veri diskinde şifrelemeyi etkinleştir
PowerShell kullanarak veya [Azure Portal aracılığıyla](attach-managed-disk-portal.md) [bir Windows sanal makinesine yeni bir disk ekleyebilirsiniz](attach-disk-ps.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell ile yeni eklenen bir diskte şifrelemeyi etkinleştir
 Windows VM 'Leri için yeni bir disk şifrelemek üzere PowerShell kullanırken yeni bir sıra sürümü belirtilmelidir. Sıra sürümü benzersiz olmalıdır. Aşağıdaki komut dosyası, dizi sürümü için bir GUID oluşturur. Bazı durumlarda, yeni eklenen bir veri diski Azure disk şifrelemesi uzantısı tarafından otomatik olarak şifrelenmiş olabilir. Otomatik şifreleme, genellikle yeni disk çevrimiçi olduktan sonra sanal makine yeniden başlatıldığında meydana gelir. Bu, genellikle disk şifrelemesi VM 'de daha önce çalıştırıldığında birim türü için "All" belirtildiğinde oluşur. Yeni eklenen bir veri diskinde Otomatik şifreleme gerçekleşirse, set-AzVmDiskEncryptionExtension cmdlet 'ini yeni bir sıra sürümü ile yeniden çalıştırmayı öneririz. Yeni veri diskiniz otomatik olarak şifrelenirse ve şifrelenmek istemiyorsanız, tüm sürücülerin şifresini çözün, ardından birim türü için işletim sistemini belirten yeni bir sıra sürümü ile yeniden şifreleyin. 
 

-  **Çalışan bir VM 'yi bir istemci parolası kullanarak şifreleyin:** Aşağıdaki komut dosyası değişkenlerinizi başlatır ve set-AzVMDiskEncryptionExtension cmdlet 'ini çalıştırır. Kaynak grubu, VM, Anahtar Kasası, AAD uygulaması ve istemci parolası zaten ön koşullar olarak oluşturulmuş olmalıdır. MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, Mysecurekasası, My-AAD-Client-ID ve My-AAD-Client-Secret değerlerini değerlerinizle değiştirin. Bu örnek, hem işletim sistemi hem de veri birimlerini içeren-VolumeType parametresi için "All" kullanır. Yalnızca işletim sistemi birimini şifrelemek istiyorsanız,-VolumeType parametresi için "OS" kullanın. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **İstemci gizliliğini kaydırmak IÇIN kek kullanarak çalışan bir VM 'Yi şifreleyin:** Azure disk şifrelemesi, şifreleme etkinleştirilirken oluşturulan disk şifreleme gizli dizilerini kaydırmak için Anahtar Kasanızda mevcut bir anahtar belirtmenize olanak tanır. Anahtar şifreleme anahtarı belirtildiğinde Azure disk şifrelemesi, Key Vault yazmadan önce şifreleme gizli dizilerini kaydırmak için bu anahtarı kullanır. Bu örnek, hem işletim sistemi hem de veri birimlerini içeren-VolumeType parametresi için "All" kullanır. Yalnızca işletim sistemi birimini şifrelemek istiyorsanız,-VolumeType parametresi için "OS" kullanın. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Disk-Encryption-keykasası parametresinin değeri için sözdizimi tam tanımlayıcı dizesidir:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[keykasaadı]</br> Anahtar şifreleme-anahtar parametresinin değeri için sözdizimi, KEK: https://[keykasaadı]. kasa. Azure. net/Keys/[kekname]/[kek-Unique-ID] ile 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLı ile yeni eklenen bir diskte şifrelemeyi etkinleştirme
  Şifrelemeyi etkinleştirmek için komutunu çalıştırdığınızda Azure CLı komutu sizin için otomatik olarak yeni bir dizi sürümü sağlar. Birim-türü parametresi için kabul edilebilir değerler tümü, işletim sistemi ve veri. VM için yalnızca bir disk türünü şifreliyorsanız, birim türü parametresini işletim sistemi veya veri olarak değiştirmeniz gerekebilir. Örnekler, birim türü parametresi için "All" kullanır. 

-  **Çalışan bir VM 'yi bir istemci parolası kullanarak şifreleyin:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **İstemci gizliliğini kaydırmak için KEK kullanarak çalışan bir VM 'yi şifreleyin:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Azure AD İstemci sertifikası tabanlı kimlik doğrulaması kullanarak şifrelemeyi etkinleştirin.
KEK olmadan veya olmadan istemci sertifikası kimlik doğrulaması kullanabilirsiniz. PowerShell betikleri kullanılmadan önce, sertifikaya anahtar kasasına yüklenmiş ve VM 'ye dağıtılan bir sertifika zaten olmalıdır. KEK de kullanıyorsanız KEK zaten var olmalıdır. Daha fazla bilgi için, Önkoşullar makalesinin [Azure AD Için sertifika tabanlı kimlik doğrulaması](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) bölümüne bakın.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Azure PowerShell ile sertifika tabanlı kimlik doğrulaması kullanarak şifrelemeyi etkinleştirme

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Sertifika tabanlı kimlik doğrulaması ve Azure PowerShell ile bir KEK kullanarak şifrelemeyi etkinleştirme

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Şifrelemeyi devre dışı bırak
Azure PowerShell, Azure CLı veya Kaynak Yöneticisi şablonuyla şifrelemeyi devre dışı bırakabilirsiniz. 

- **Azure PowerShell ile disk şifrelemeyi devre dışı bırak:** Şifrelemeyi devre dışı bırakmak için [Disable-Azurermvmdiskencryptıon](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet 'ini kullanın. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Azure CLI ile şifrelemeyi devre dışı bırakın:** Şifrelemeyi devre dışı bırakmak için [az VM ENCRYPTION Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) komutunu kullanın. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Kaynak Yöneticisi şablonuyla şifrelemeyi devre dışı bırak:** 

    1. [WINDOWS VM 'leri çalıştırırken disk şifrelemesini devre dışı bırak](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) ' dan **Azure 'a dağıt** ' a tıklayın.
    2. Abonelik, kaynak grubu, konum, VM, yasal koşullar ve Sözleşme ' yi seçin.
    3.  Çalışan bir Windows VM 'de disk şifrelemeyi devre dışı bırakmak için **satın al** ' a tıklayın 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure disk şifrelemeye genel bakış](disk-encryption-overview.md)
