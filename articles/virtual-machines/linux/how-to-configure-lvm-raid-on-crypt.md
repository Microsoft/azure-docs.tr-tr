---
title: Şifrelenmiş cihazlarda LVM ve RAID Yapılandırma-Azure disk şifrelemesi
description: Bu makalede, Linux VM 'Leri için şifrelenmiş cihazlarda LVM ve RAID yapılandırmasına yönelik yönergeler sağlanmaktadır.
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: bdd897e76df941130e3acdf9c30ea8edd41147e9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601933"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Şifrelenmiş cihazlarda LVM ve RAID yapılandırma

Bu makale, şifrelenmiş cihazlarda mantıksal birim yönetimi (LVM) ve RAID 'yi gerçekleştirmeye yönelik adım adım bir işlemdir. İşlem aşağıdaki ortamlar için geçerlidir:

- Linux dağıtımları
    - RHEL 7.6 +
    - Ubuntu 18.04 +
    - SUSE 12 +
- Azure disk şifrelemesi tek Pass uzantısı
- Azure disk şifrelemesi ikili geçiş uzantısı


## <a name="scenarios"></a>Senaryolar

Bu makaledeki yordamlar aşağıdaki senaryoları destekler:  

- Şifrelenmiş cihazların en üstünde LVM 'yi yapılandırma (LVM-Crypt)
- Şifrelenmiş cihazların üzerine RAID yapılandırma (RAID-on-Crypt)

Temeldeki cihaz veya cihazlar şifrelendikten sonra, bu şifrelenmiş katmanın üstünde LVM veya RAID yapıları oluşturabilirsiniz. 

Fiziksel birimler (PVs), şifrelenen katmanın üstünde oluşturulur. Fiziksel birimler birim grubunu oluşturmak için kullanılır. Birimleri oluşturur ve gerekli girdileri/etc/fstaba 'a eklersiniz. 

![LVM yapılarının katmanlarının diyagramı](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Benzer şekilde, RAID cihazı, disklerdeki şifreli katmanın üzerine oluşturulur. RAID cihazının üzerine bir dosya sistemi oluşturulur ve normal bir cihaz olarak/etc/fstab 'e eklenir.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

LVM-on-Crypt ' i kullanmanızı öneririz. Belirli bir uygulama veya ortam sınırlamaları nedeniyle, LVM kullanılmıyorsa, RAID bir seçenektir.

**Encryptformatall** seçeneğini kullanacaksınız. Bu seçenek hakkında daha fazla bilgi için bkz. [Linux VM 'lerinde veri diskleri Için EncryptFormatAll özelliğini kullanma](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

Ayrıca, işletim sistemini şifrelerken bu yöntemi kullanabilirsiniz, ancak yalnızca veri sürücüleri şifreliyoruz.

Yordamlar, Linux VM 'lerinde [Azure disk şifrelemesi senaryolarında](./disk-encryption-linux.md) önkoşulları zaten gözden geçirdiğinizi ve [hızlı başlangıç: Azure CLI Ile bir Linux VM oluşturma ve şifreleme](./disk-encryption-cli-quickstart.md).

Azure disk şifrelemesi çift taramalı sürümü kullanımdan kalkmaya yol açık ve artık yeni şifrelemeleri üzerinde kullanılmamalıdır.

## <a name="general-steps"></a>Genel adımlar

"Şifreli" yapılandırmaların kullanıldığı zaman, aşağıdaki yordamlarda özetlenen işlemi kullanın.

>[!NOTE] 
>Makale genelinde değişkenler kullanıyoruz. Değerleri uygun şekilde değiştirin.

### <a name="deploy-a-vm"></a>VM'yi dağıtma 
Aşağıdaki komutlar isteğe bağlıdır, ancak bunları yeni dağıtılan bir sanal makineye (VM) uygulamanızı öneririz.

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLı:

```azurecli
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Diskleri VM 'ye iliştirme
`$N`VM 'ye iliştirmek istediğiniz yeni disk sayısı için aşağıdaki komutları tekrarlayın.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLı:

```azurecli
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Disklerin sanal makineye bağlı olduğunu doğrulama
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![PowerShell 'de eklenen disklerin listesi](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLı:

```azurecli
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Azure CLı 'da eklenen disklerin listesi](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portal:

![Portalda eklenen disklerin listesi](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

İşletim sistemi:

```bash
lsblk 
```
![İşletim sistemindeki eklenen disklerin listesi](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Şifrelenecek diskleri yapılandırma
Bu yapılandırma, işletim sistemi düzeyinde yapılır. Karşılık gelen diskler, Azure disk şifrelemesi aracılığıyla geleneksel bir şifreleme için yapılandırılır:

- Dosya sistemleri, disklerin üstünde oluşturulur.
- Dosya sistemlerini bağlamak için geçici bağlama noktaları oluşturulur.
- Dosya sistemleri,/etc/fstab üzerinde önyükleme zamanında takılacak şekilde yapılandırılır.

Yeni disklere atanan cihaz harfini kontrol edin. Bu örnekte, dört veri diski kullanıyoruz.

```bash
lsblk 
```
![İşletim sistemine eklenen veri diskleri](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Her diskin üstünde bir dosya sistemi oluşturma
Bu komut, "for" döngüsünün "içinde" bölümünde tanımlanan her bir diskte bir ext4 dosya sisteminin oluşturulmasını yineler.

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Ext4 dosya sistemi oluşturma](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

En son oluşturduğunuz dosya sistemlerinin evrensel benzersiz tanımlayıcısını (UUID) bulun, geçici bir klasör oluşturun,/etc/fstab 'ye karşılık gelen girdileri ekleyin ve tüm dosya sistemlerini bağlayın.

Bu komut ayrıca "for" döngüsünün "içinde" bölümünde tanımlanan her disk üzerinde de yinelenir:

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Disklerin düzgün şekilde bağlandığından emin olun
```bash
lsblk
```
![Bağlı geçici dosya sistemleri listesi](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Ayrıca, disklerin yapılandırıldığını doğrulayın:

```bash
cat /etc/fstab
```
![Fstab aracılığıyla yapılandırma bilgileri](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Veri disklerini şifreleyin
Anahtar şifreleme anahtarı kullanarak PowerShell (KEK):

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

KEK kullanarak Azure CLı:

```azurecli
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>Şifreleme durumunu doğrulama

Bir sonraki adıma yalnızca tüm diskler şifrelendiğinde devam edin.

PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![PowerShell 'de şifreleme durumu](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLı:

```azurecli
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Azure CLı 'de şifreleme durumu](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portal:

![Portalda şifreleme durumu](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

İşletim sistemi düzeyi:

```bash
lsblk
```
![İşletim sistemindeki şifreleme durumu](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Uzantı, dosya sistemlerini/var/lib/azure_disk_encryption_config/azure_crypt_mount (eski bir şifrelemeye) veya/etc/crypttab (yeni şifrelemeleri) öğesine ekler.

>[!NOTE] 
>Bu dosyaların hiçbirini değiştirmeyin.

Bu dosya, bu diskleri, LVM veya RAID 'in daha sonra kullanabilmesi için önyükleme işlemi sırasında etkinleştirecek olacaktır. 

Bu dosyadaki bağlama noktalarıyla uğraşmayın. Azure disk şifrelemesi, bu şifrelenmiş cihazların üzerinde bir fiziksel birim veya RAID cihazı oluşturduktan sonra normal bir dosya sistemi olarak takılı diskleri alma özelliğini kaybedecektir. (Bu, hazırlama işlemi sırasında kullandığımız dosya sistemi biçimini kaldırır.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Geçici klasörleri ve geçici fstab girdilerini kaldırma
LVM 'nin bir parçası olarak kullanılacak disklerdeki dosya sistemlerini çıkarın.

```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
Ve/etc/fstab girdilerini kaldırın:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Disklerin bağlı olmadığından ve/etc/fstab üzerindeki girdilerin kaldırıldığını doğrulayın

```bash
lsblk
```
![Geçici dosya sistemlerinin takılmamış olduğunu doğrulama](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

Ve disklerin yapılandırıldığını doğrulayın:
```bash
cat /etc/fstab
```
![Geçici fstab girişlerinin kaldırıldığını doğrulama](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>LVM-Crypt için adımlar
Artık temeldeki diskler şifrelendikten sonra, LVM yapılarını oluşturabilirsiniz.

Cihaz adını kullanmak yerine, bir fiziksel birim oluşturmak için disklerin her biri için/dev/mapper yollarını kullanın (diskin üzerinde değil, diskin üst kısmında bulunan Crypt katmanında).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>LVM 'yi şifreli katmanların üstünde yapılandırma
#### <a name="create-the-physical-volumes"></a>Fiziksel birimleri oluşturma
Dosya sistemi imzasını temizlemeyi isteyip istemediğinizi soran bir uyarı alırsınız. **Y** girerek devam edin veya gösterildiği gibi **echo "y"** kullanın:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Fiziksel bir birimin oluşturulduğunu doğrulama](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>Burada/dev/mapper/Device adlarının, **lsblk**'ın çıktısına göre gerçek değerlerinizle değiştirilmesini gerekir.

#### <a name="verify-the-information-for-physical-volumes"></a>Fiziksel birimler için bilgileri doğrulama
```bash
pvs
```

![Fiziksel birimlerle ilgili bilgiler](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Birim grubunu oluşturma
Zaten başlatılmış olan cihazları kullanarak birim grubunu oluşturun:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Birim grubu bilgilerini denetleme

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Birim grubu için bilgiler](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Mantıksal birimler oluşturma

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Oluşturulan mantıksal birimleri denetleyin

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Mantıksal birimler için bilgiler](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Mantıksal birimler için yapıların üstünde dosya sistemleri oluşturma

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Yeni dosya sistemleri için bağlama noktaları oluşturma

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Yeni dosya sistemlerini/etc/fstab 'e ekleme ve bunları bağlama

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Yeni dosya sistemlerinin takılı olduğundan emin olun

```bash
lsblk -fs
df -h
```
![Ekran görüntüsü, data0 ve Veri1 olarak bağlanmış dosya sistemlerine sahip bir konsol penceresi gösterir.](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

Bu **lsblk** çeşitlemesiyle, bağımlılıkları ters sırada gösteren cihazları listeliyoruz. Bu seçenek, özgün/dev/SD [Disk] cihaz adları yerine mantıksal birime göre gruplanmış cihazları belirlemesine yardımcı olur.

**NOFAIL** seçeneğinin Azure disk şifrelemesi ile şifrelenen bir cihazın üzerine oluşturulan LVM birimlerinin bağlama noktası seçeneklerine eklendiğinden emin olmak önemlidir. Önyükleme işlemi sırasında (veya bakım modunda) işletim sisteminin takılmasını önler.

**NOFAIL** seçeneğini kullanmazsanız:

- İşletim sistemi hiçbir şekilde Azure disk şifrelemesi 'nin başlatıldığı ve veri disklerinin kilidinin açılacağı ve bağlı olduğu aşamayı hiçbir şekilde almaz. 
- Şifrelenen Diskler, önyükleme işleminin sonunda açılacak. LVM birimleri ve dosya sistemleri, Azure Disk Şifrelemesi tarafından açılana kadar otomatik olarak bağlanır. 

Sanal makineyi yeniden başlatmayı test edebilir ve dosya sistemlerinin Ayrıca önyükleme zamanından sonra otomatik olarak takıldığını doğrulayabilirsiniz. Bu işlem, dosya sistemlerinin sayısına ve boyutlarına bağlı olarak birkaç dakika sürebilir.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>VM 'yi yeniden başlatma ve yeniden başlatmanın ardından doğrulama

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Şifreli RAID ile ilgili adımlar
Artık temeldeki diskler şifrelendikten sonra, RAID yapılarını oluşturmaya devam edebilirsiniz. İşlem, LVM için bir ile aynıdır, ancak cihaz adını kullanmak yerine her disk için/dev/mapper yollarını kullanın.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Disklerin şifrelenmiş katmanının üstünde RAID yapılandırma
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Mdaddm komutu aracılığıyla yapılandırılmış RAID bilgileri](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>Buradaki/dev/mapper/Device adlarının, **lsblk**'ın çıktısına göre gerçek değerlerinizle değiştirilmesini gerekir.

### <a name="checkmonitor-raid-creation"></a>RAID oluşturmayı denetle/izle
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![RAID durumu](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Yeni RAID cihazının üzerine bir dosya sistemi oluşturma
```bash
mkfs.ext4 /dev/md10
```

Dosya sistemi için yeni bir bağlama noktası oluşturun, yeni dosya sistemini/etc/fstab öğesine ekleyin ve bağlayın:

>[!NOTE] 
>Bu döngüye yalnızca bir cihazda yineleme yapılır, bu örnek, gerekirse birden çok MD cihazında kullanılması için bu şekilde oluşturulmuştur.

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Yeni dosya sisteminin takılı olduğundan emin olun:

```bash
lsblk -fs
df -h
```
![Ekran görüntüsü, kıddata olarak bağlanmış bir dosya sistemine sahip bir konsol penceresi gösterir.](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

**NOFAIL** seçeneğinin Azure disk şifrelemesi ile şifrelenen bir cihazın ÜZERINE oluşturulan RAID birimlerinin bağlama noktası seçeneklerine eklendiğinden emin olmak önemlidir. Önyükleme işlemi sırasında (veya bakım modunda) işletim sisteminin takılmasını önler.

**NOFAIL** seçeneğini kullanmazsanız:

- İşletim sistemi hiçbir şekilde Azure disk şifrelemesi 'nin başlatıldığı ve veri disklerinin kilidinin açılacağı ve bağlı olduğu aşamayı hiçbir şekilde almaz.
- Şifrelenen Diskler, önyükleme işleminin sonunda açılacak. RAID birimleri ve dosya sistemleri, Azure Disk Şifrelemesi tarafından açılana kadar otomatik olarak bağlanır.

Sanal makineyi yeniden başlatmayı test edebilir ve dosya sistemlerinin Ayrıca önyükleme zamanından sonra otomatik olarak takıldığını doğrulayabilirsiniz. Bu işlem, dosya sistemlerinin sayısına ve boyutlarına bağlı olarak birkaç dakika sürebilir.

```bash
shutdown -r now
```

Oturum açmak için:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Sonraki adımlar

- [Azure disk şifrelemesi ile şifrelenen mantıksal birim yönetimi cihazlarını yeniden boyutlandır](how-to-resize-encrypted-lvm.md)
- [Azure Disk Şifrelemesi sorunlarını giderme](disk-encryption-troubleshooting.md)
