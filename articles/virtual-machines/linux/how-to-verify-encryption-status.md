---
title: Linux için şifreleme durumunu doğrulama-Azure disk şifrelemesi
description: Bu makale, platform ve işletim sistemi düzeylerinden şifreleme durumunu doğrulamaya ilişkin yönergeler sağlar.
author: kailashmsft
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 04654672cc5806465ec9f75b695772dcb2037eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564144"
---
# <a name="verify-encryption-status-for-linux"></a>Linux için şifreleme durumunu doğrulama 

Bu makalenin kapsamı, farklı yöntemler kullanarak bir sanal makinenin şifreleme durumunu doğrulamak: Azure portal, PowerShell, Azure CLı veya sanal makinenin (VM) işletim sistemi. 

Şifreleme durumunu şifreleme sırasında veya sonrasında, aşağıdakilerden birini yaparak doğrulayabilirsiniz:

- Belirli bir VM 'ye bağlı diskler denetleniyor. 
- Diskin bağlı veya bağlı olup olmadığı her bir diskte şifreleme ayarlarını sorgulama.

Bu senaryo, Azure disk şifrelemesi çift yönlü ve tek geçiş uzantıları için geçerlidir. Linux dağıtımları, bu senaryonun tek ortamıdır.

>[!NOTE] 
>Makale genelinde değişkenler kullanıyoruz. Değerleri uygun şekilde değiştirin.

## <a name="portal"></a>Portal

Azure portal **Uzantılar** bölümünün içinde, listeden Azure disk şifrelemesi uzantısını seçin. **Durum iletisi** bilgileri geçerli şifreleme durumunu gösterir:

![Durum, sürüm ve durum iletisi vurgulanmış şekilde Portal denetimi](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Uzantılar listesinde, karşılık gelen Azure disk şifrelemesi uzantısı sürümünü görürsünüz. Sürüm 0. x, Azure disk şifrelemesi ikili geçişine karşılık gelir ve sürüm 1. x, Azure disk şifrelemesi tek geçişine karşılık gelir.

Uzantıyı seçip **ayrıntılı durumu görüntüle**' yi seçerek daha fazla bilgi edinebilirsiniz. Şifreleme işleminin ayrıntılı durumu JSON biçiminde görüntülenir.

!["Ayrıntılı durumu görüntüle" bağlantısı vurgulanmış şekilde Portal denetimi](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![JSON biçiminde ayrıntılı durum](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Şifreleme durumunu doğrulamak için bir diğer yol ise **disk ayarları** bölümüne bakar.

![İşletim sistemi diski ve veri diskleri için şifreleme durumu](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Bu durum, disklerin, aslında işletim sistemi düzeyinde şifrelendikleri için şifreleme ayarlarına sahip olduğu anlamına gelir.
>
> Tasarım yaparak diskler önce damgalı ve daha sonra şifrelenir. Şifreleme işlemi başarısız olursa, diskler kesilebilir ancak şifrelenmez. 
>
> Disklerin gerçekten şifrelenip şifrelenmediğini onaylamak için, işletim sistemi düzeyinde her diskin şifrelemesini iki kez kontrol edebilirsiniz.

## <a name="powershell"></a>PowerShell

Aşağıdaki PowerShell komutlarını kullanarak, şifrelenmiş bir sanal makinenin *genel* şifreleme durumunu doğrulayabilirsiniz:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![PowerShell 'de genel şifreleme durumu](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Aşağıdaki PowerShell komutlarını kullanarak her bir diskten şifreleme ayarlarını yakalayabilirsiniz.

### <a name="single-pass"></a>Tek pass
Tek bir geçişte, şifreleme ayarları her bir disk (işletim sistemi ve veri) üzerinde damgalanır. Bir işletim sistemi diskinin şifreleme ayarlarını, tek bir geçişte şu şekilde yakalayabilirsiniz:

```powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Bir işletim sistemi diski için şifreleme ayarları](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Diskte şifreleme ayarları damgalı yoksa, çıkış boş olur:

![Boş çıkış](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Veri disklerinin şifreleme ayarlarını yakalamak için aşağıdaki komutları kullanın:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Veri diskleri için şifreleme ayarları](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Çift geçiş
İkili geçişte, şifreleme ayarları her bir diskte değil, VM modeline damgalanır.

Şifreleme ayarlarının ikili bir geçişte damgalı olduğunu doğrulamak için aşağıdaki komutları kullanın:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![İkili geçişte şifreleme ayarları](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Eklenmemiş diskler

Bir VM 'ye bağlı olmayan diskler için şifreleme ayarlarını kontrol edin.

### <a name="managed-disks"></a>Yönetilen diskler
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>Azure CLI’si

Aşağıdaki Azure CLı komutlarını kullanarak, şifrelenmiş bir sanal makinenin *genel* şifreleme durumunu doğrulayabilirsiniz:

```azurecli
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Azure CLı 'dan genel şifreleme durumu ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Tek pass
Aşağıdaki Azure CLı komutlarını kullanarak her bir disk için şifreleme ayarlarını doğrulayabilirsiniz:

```azurecli
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Veri şifreleme ayarları](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Diskte şifreleme ayarları atıyamazsa, metin **diskinin şifrelenmediğini** görürsünüz.

Ayrıntılı durum ve şifreleme ayarları almak için aşağıdaki komutları kullanın.

İşletim sistemi diski:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![İşletim sistemi diski için ayrıntılı durum ve şifreleme ayarları](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Veri diskleri:

```azurecli
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Veri diskleri için ayrıntılı durum ve şifreleme ayarları](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Çift geçiş

```azurecli
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Azure CLı aracılığıyla ikili geçiş için genel şifreleme ayarları](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

Ayrıca, işletim sistemi diskinin VM modeli depolama profilindeki şifreleme ayarlarını da denetleyebilirsiniz:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Azure CLı aracılığıyla çift geçiş için VM profili](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Eklenmemiş diskler

Bir VM 'ye bağlı olmayan diskler için şifreleme ayarlarını kontrol edin.

### <a name="managed-disks"></a>Yönetilen diskler

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Yönetilmeyen diskler

Yönetilmeyen diskler, Azure depolama hesaplarında sayfa Blobları olarak depolanan VHD dosyalarıdır.

Belirli bir diskin ayrıntılarını almak için şunları sağlamanız gerekir:

- Diski içeren depolama hesabının KIMLIĞI.
- Söz konusu depolama hesabı için bir bağlantı dizesi.
- Diski depolayan kapsayıcının adı.
- Disk adı.

Bu komut tüm depolama hesaplarınızın tüm kimliklerini listeler:

```azurecli
az storage account list --query [].[id] -o tsv
```
Depolama hesabı kimlikleri aşağıdaki biçimde listelenir:

/Subscriptions/ \<subscription id> /ResourceGroups/ \<resource group name> /providers/Microsoft.Storage/storageAccounts/\<storage account name>

Uygun KIMLIĞI seçin ve bir değişkende depolayın:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

Bu komut, belirli bir depolama hesabı için bağlantı dizesini alır ve bir değişkende depolar:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Aşağıdaki komut, bir depolama hesabı altındaki tüm kapsayıcıları listeler:
```azurecli
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Diskler için kullanılan kapsayıcı normalde "VHD 'ler" olarak adlandırılır.

Kapsayıcı adını bir değişkende depolayın: 
```bash
ContainerName="name of the container"
```

Belirli bir kapsayıcıdaki tüm Blobları listelemek için bu komutu kullanın:
```azurecli 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Sorgulamak istediğiniz diski seçin ve adını bir değişkende depolayın:
```bash
DiskName="diskname.vhd"
```
Disk şifreleme ayarlarını sorgulayın:
```azurecli
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>İşletim sistemi
Veri diski bölümlerinin şifrelendiğini (ve işletim sistemi diskinin olmadığını) doğrulayın.

Bir bölüm veya disk şifrelendiğinde **, şifreli bir tür olarak** görüntülenir. Şifrelenmediğinde, **bölüm/disk** türü olarak gösterilir.

```bash
lsblk
```

![Bölüm için işletim sistemi Crypt katmanı](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Aşağıdaki **lsblk** türevini kullanarak daha fazla bilgi edinebilirsiniz. 

Uzantı tarafından bağlanan bir **Crypt** türü katmanı görürsünüz. Aşağıdaki örnek, mantıksal birimleri ve **şifre \_ Luks fsType** olan normal diskleri gösterir.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Mantıksal birimler ve normal diskler için işletim sistemi Crypt katmanı](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Ek bir adım olarak, veri diskinde yüklenmiş bir anahtar olup olmadığını doğrulayabilirsiniz:

```bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

```bash
cryptsetup luksDump /dev/sdd1
```

Ve hangi **DM** cihazlarının **şifreli** olarak listelendiğini kontrol edebilirsiniz:

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Disk Şifrelemesi sorunlarını giderme](disk-encryption-troubleshooting.md)
