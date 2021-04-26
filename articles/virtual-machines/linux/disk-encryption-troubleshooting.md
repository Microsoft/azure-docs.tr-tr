---
title: Linux sanal makineleri için Azure disk şifrelemesi sorunlarını giderme
description: Bu makalede, Linux VM 'Leri için Microsoft Azure disk şifrelemesi için sorun giderme ipuçları sunulmaktadır.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 58ea788d6959b039ff5de8a4a889c531b987d826
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563379"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Linux VM 'Leri için Azure disk şifrelemesi sorun giderme kılavuzu

Bu kılavuz BT uzmanları, bilgi güvenliği analistleri ve kuruluşları tarafından Azure disk şifrelemesi kullanan bulut yöneticileri içindir. Bu makale, disk şifrelemesiyle ilgili sorunları gidermenize yardımcı olur.

Aşağıdaki adımlardan herhangi birini gerçekleştirmeden önce, şifrelemeye çalıştığınız VM 'Lerin [desteklenen VM boyutları ve işletim sistemleri](disk-encryption-overview.md#supported-vms-and-operating-systems)arasında olduğundan ve tüm önkoşulları karşıladığınızı doğrulayın:

- [VM 'Ler için ek gereksinimler](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ağ gereksinimleri](disk-encryption-overview.md#networking-requirements)
- [Şifreleme anahtarı depolama gereksinimleri](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux işletim sistemi disk şifrelemesi sorunlarını giderme

Linux işletim sistemi (OS) disk şifrelemesi, tam disk şifreleme işlemi aracılığıyla çalıştırmadan önce IŞLETIM sistemi sürücüsünü çıkarmalıdır. Sürücünün bağlantısını çıkarırsam, "bir hata iletisi" kaldırılamadı... " oluşma olasılığı yüksektir.

Bu hata, desteklenen hisse senedi Galerisi görüntüsünden değiştirilen bir ortama sahip bir VM 'de işletim sistemi disk şifrelemesi denendiğinde ortaya çıkabilir. Desteklenen görüntünün sapmaları, uzantının işletim sistemi sürücüsünü çıkarma özelliğine engel olabilir. Sapmalarla örnek olarak aşağıdaki öğeler bulunabilir:
- Özelleştirilmiş görüntüler artık desteklenen bir dosya sistemi veya bölümleme şeması ile eşleşmiyor.
- SAP, MongoDB, Apache Cassandra ve Docker gibi büyük uygulamalar, şifrelemeden önce işletim sisteminde yüklenip çalıştırıldığında desteklenmez. Azure disk şifrelemesi, disk şifrelemesi için işletim sistemi sürücüsünün hazırlanmasından itibaren bu işlemlerin güvenli bir şekilde kapatılmasını başaramadı. İşletim sistemi sürücüsüne açık dosya tutamaçlarını tutan etkin süreçler varsa, işletim sistemi sürücüsü kaldırılamaz, bu da işletim sistemi sürücüsünü şifreleme hatasına neden olur. 
- Etkin duruma yakın zamanda çalışan özel betikler veya şifreleme işlemi sırasında VM üzerinde başka herhangi bir değişiklik yapılmakta. Bu çakışma, bir Azure Resource Manager şablonu aynı anda yürütülecek birden çok uzantı tanımlıyorsa veya özel bir betik uzantısı ya da başka bir eylem aynı anda disk şifrelemesi için çalıştığında gerçekleşebilir. Bu tür adımları serileştirmek ve yalıtmak sorunu çözebilir.
- Şifreleme etkinleştirilmeden önce güvenliği genişletilmiş Linux (SELinux) devre dışı bırakılmamıştır, bu nedenle çıkarma adımı başarısız olur. Şifreleme tamamlandıktan sonra SELinux yeniden etkinleştirilebilir.
- İşletim sistemi diski bir mantıksal birim Yöneticisi (LVM) şeması kullanır. Sınırlı LVM veri diski desteği kullanılabilir olsa da, LVM işletim sistemi diski değildir.
- Minimum bellek gereksinimleri karşılanmıyor (işletim sistemi disk şifrelemesi için 7 GB önerilir).
- Veri sürücüleri, her ikisi de/mnt/Directory ya da diğeri altına bağlanır (örneğin,/mnt/DATA1,/mnt/DATA2,/DATA3 +/DATA3/DATA4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Ubuntu 14,04 LTS için varsayılan çekirdeği güncelleştirme

Ubuntu 14,04 LTS görüntüsü, varsayılan bir 4,4 çekirdek sürümü ile birlikte gelir. Bu çekirdek sürümü, işletim sistemi şifreleme işlemi sırasında, bellek yetersiz olduğundan, dd komutunu yanlış bir şekilde sonlandırdığı bilinen bir sorunla karşılaştı. Bu hata, en son Azure ayarlanmış Linux çekirdekte düzeltildi. Bu hatadan kaçınmak için, görüntüde Şifrelemeyi etkinleştirmeden önce, aşağıdaki komutları kullanarak [Azure ayarlanmış çekirdek 4,15](https://packages.ubuntu.com/trusty/linux-azure) veya sonraki bir sürüme güncelleştirin:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

VM yeni çekirdekte yeniden başlatıldıktan sonra yeni çekirdek sürümü şu kullanılarak onaylanabilir:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Azure sanal makine Aracısı ve uzantı sürümlerini güncelleştirme

Azure disk şifrelemesi işlemleri, Azure sanal makine aracısının desteklenmeyen sürümlerini kullanan sanal makine görüntülerinde başarısız olabilir. 2.2.38 'den önceki aracı sürümlerine sahip Linux görüntülerinin, Şifrelemeyi etkinleştirmeden önce güncelleştirilmeleri gerekir. Daha fazla bilgi için bkz. Azure 'da [Azure Linux aracısını güncelleştirme](../extensions/update-linux-agent.md) ve [sanal makine aracıları için en düşük sürüm desteği](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

Microsoft. Azure. Security. AzureDiskEncryption veya Microsoft. Azure. Security. AzureDiskEncryptionForLinux Guest aracı uzantısının doğru sürümü de gereklidir. Uzantı sürümleri, Azure sanal makine Aracısı önkoşulları karşılanacağı ve sanal makine aracısının desteklenen bir sürümü kullanıldığında platform tarafından otomatik olarak güncelleştirilir ve güncelleştirilir.

Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux uzantısı kullanım dışı bırakılmış ve artık desteklenmiyor.  

## <a name="unable-to-encrypt-linux-disks"></a>Linux diskleri şifrelenemiyor

Bazı durumlarda, Linux disk şifrelemesi "işletim sistemi disk şifrelemesi başlatıldı" saatinde takılmış ve SSH devre dışı bırakılmış gibi görünüyor. Şifreleme işlemi, bir hisse senedi Galerisi görüntüsünde 3-16 saat sürebilir. Multi-terabaytlık ölçekli veri diskleri eklenirse, işlem gün sürebilir.

Linux işletim sistemi disk şifreleme sırası, işletim sistemi sürücüsünü geçici olarak kaldırır. Daha sonra, tüm işletim sistemi diskinin, şifreli durumunda yeniden bağlanmadan önce blok blok şifrelemesini gerçekleştirir. Linux disk şifrelemesi, şifreleme sürerken VM 'nin eşzamanlı kullanımına izin vermez. VM 'nin performans özellikleri, şifrelemeyi tamamlaması için gereken sürede önemli bir farklılık yapabilir. Bu özellikler, diskin boyutunu ve depolama hesabının standart veya Premium (SSD) depolama alanı olup olmadığını içerir.

İşletim sistemi sürücüsü şifrelenirken, sanal makine bir bakım durumuna girer ve devam eden işlemde kesintiye uğramasını önlemek için SSH 'yi devre dışı bırakır.  Şifreleme durumunu denetlemek için Azure PowerShell [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) komutunu kullanın ve **ilerlemedurumuiletisi** alanını denetleyin. **İlerlemedurumuiletisi** , veri ve işletim sistemi diskleri şifreli olarak bir dizi durum bildirir:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings :
ProgressMessage            : Transitioning

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for data volumes

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

**İlerlemedurumuiletisi** , şifreleme Işlemi Için **işletim sistemi disk şifrelemesi başlatıldığında** kalacaktır.  Şifreleme tamamlandığında ve başarılı olduğunda, **ilerlemedurumuiletisi** döndürülür:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for all volumes
```

Bu ileti kullanılabilir olduktan sonra, şifrelenmiş işletim sistemi sürücüsünün kullanıma hazır olması beklenir ve VM yeniden kullanılabilir hale gelir.

Önyükleme bilgileri, ilerleme iletisi veya bir hata, işletim sistemi şifrelemesini bu işlemin ortasında başarısız bildirirse, VM 'yi şifrelemeden önce anlık görüntüye veya yedeklemeye geri yükleyin. Bu kılavuzda açıklanan "çıkaramadı" hatası, bir ileti örneğidir.

Şifrelemeyi yeniden denemeden önce, sanal makinenin özelliklerini yeniden değerlendirerek, tüm önkoşulların karşılanmasını sağlayın.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Güvenlik duvarının arkasında Azure disk şifrelemesi sorunlarını giderme

[Yalıtılmış bir ağda disk şifrelemeyi](disk-encryption-isolated-network.md) görme

## <a name="troubleshooting-encryption-status"></a>Şifreleme durumu sorunlarını giderme 

Portal, sanal makine içinde şifrelenmemiş olduktan sonra bile bir disk şifreli olarak görüntülenebilir.  Bu durum, daha yüksek düzeyde Azure disk şifrelemesi yönetim komutları kullanmak yerine, diskin VM içinden doğrudan şifresini kaldırmak için düşük düzey komutlar kullanıldığında meydana gelebilir.  Üst düzey komutlar yalnızca VM 'nin içinden diskin şifresini kaldıramaz, ancak VM 'nin dışında, önemli platform düzeyi şifreleme ayarlarını ve VM ile ilişkili uzantı ayarlarını da güncelleştirir.  Bunlar hizalamayla tutulmazsa, Platform şifreleme durumunu bildiremez veya VM 'yi düzgün şekilde sağlayamaz.

Azure disk şifrelemesini PowerShell ile devre dışı bırakmak için [Disable-azvmdiskencryption](/powershell/module/az.compute/disable-azvmdiskencryption) ' ı ve ardından [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension)' i kullanın. Şifreleme devre dışı olmadan önce Remove-AzVMDiskEncryptionExtension çalıştırmak başarısız olur.

CLı ile Azure disk şifrelemesini devre dışı bırakmak için [az VM Encryption Disable](/cli/azure/vm/encryption)seçeneğini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure disk şifrelemesi 'nde bazı yaygın sorunlar ve bu sorunları nasıl giderebileceğiniz hakkında daha fazla bilgi edindiniz. Bu hizmet ve özellikleri hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure Güvenlik Merkezi 'nde disk şifrelemeyi uygulama](../../security-center/asset-inventory.md)
- [Azure bekleyen verileri şifreleme](../../security/fundamentals/encryption-atrest.md)