---
title: Sorun giderme-Linux sanal makineleri için Azure disk şifrelemesi
description: Bu makalede, Linux VM 'Leri için Microsoft Azure disk şifrelemesi için sorun giderme ipuçları sunulmaktadır.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: c1e96a3acf2a576e0656afb3abea9dd787bf989a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750061"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure disk şifrelemesi sorun giderme kılavuzu

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

Azure disk şifrelemesi işlemleri, Azure sanal makine aracısının desteklenmeyen sürümlerini kullanan sanal makine görüntülerinde başarısız olabilir. 2\.2.38 'den önceki aracı sürümlerine sahip Linux görüntülerinin, Şifrelemeyi etkinleştirmeden önce güncelleştirilmeleri gerekir. Daha fazla bilgi için bkz. Azure 'da [Azure Linux aracısını güncelleştirme](../extensions/update-linux-agent.md) ve [sanal makine aracıları için en düşük sürüm desteği](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

Microsoft. Azure. Security. AzureDiskEncryption veya Microsoft. Azure. Security. AzureDiskEncryptionForLinux Guest aracı uzantısının doğru sürümü de gereklidir. Uzantı sürümleri, Azure sanal makine Aracısı önkoşulları karşılanacağı ve sanal makine aracısının desteklenen bir sürümü kullanıldığında platform tarafından otomatik olarak güncelleştirilir ve güncelleştirilir.

Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux uzantısı kullanım dışı bırakılmış ve artık desteklenmiyor.  

## <a name="unable-to-encrypt-linux-disks"></a>Linux diskleri şifrelenemiyor

Bazı durumlarda, Linux disk şifrelemesi "işletim sistemi disk şifrelemesi başlatıldı" saatinde takılmış ve SSH devre dışı bırakılmış gibi görünüyor. Şifreleme işlemi, bir hisse senedi Galerisi görüntüsünde 3-16 saat sürebilir. Multi-terabaytlık ölçekli veri diskleri eklenirse, işlem gün sürebilir.

Linux işletim sistemi disk şifreleme sırası, işletim sistemi sürücüsünü geçici olarak kaldırır. Daha sonra, tüm işletim sistemi diskinin, şifreli durumunda yeniden bağlanmadan önce blok blok şifrelemesini gerçekleştirir. Linux disk şifrelemesi, şifreleme sürerken VM 'nin eşzamanlı kullanımına izin vermez. VM 'nin performans özellikleri, şifrelemeyi tamamlaması için gereken sürede önemli bir farklılık yapabilir. Bu özellikler, diskin boyutunu ve depolama hesabının standart veya Premium (SSD) depolama alanı olup olmadığını içerir.

Şifreleme durumunu denetlemek için [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) komutundan döndürülen **ilerlemedurumuiletisi** alanını yoklayın. İşletim sistemi sürücüsü şifrelenirken, sanal makine bir bakım durumuna girer ve devam eden işlemde kesintiye uğramasını önlemek için SSH 'yi devre dışı bırakır. **Encryptionınprogress** iletisi, şifrelemenin devam ettiği sürenin büyük bölümü için rapor bildiriyor. Birkaç saat sonra, bir **VMRestartPending** iletisi VM 'yi yeniden başlatmanızı ister. Örneğin:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

VM 'yi yeniden başlattıktan sonra ve VM yeniden başlatıldıktan sonra, yeniden başlatma için 2-3 dakika beklemeniz ve hedef üzerinde son adımların gerçekleştirilmesi gerekir. Şifreleme son tamamlandığında durum iletisi değişir. Bu ileti kullanılabilir olduktan sonra, şifrelenmiş işletim sistemi sürücüsünün kullanıma hazır olması beklenir ve VM yeniden kullanılabilir hale gelir.

Aşağıdaki durumlarda, VM 'yi şifrelemeden hemen önce geri görüntüye veya yedeklemeye geri yüklemeniz önerilir:
   - Daha önce açıklanan yeniden başlatma sırası gerçekleşmezse.
   - Önyükleme bilgileri, ilerleme iletisi veya diğer hata göstergeleri bu işlemin ortasında işletim sistemi şifrelemesini bildirir. Bu kılavuzda açıklanan "çıkaramadı" hatası, bir ileti örneğidir.

Bir sonraki denemeden önce, VM 'nin özelliklerini yeniden değerlendirin ve tüm önkoşulların karşılanmasını sağlayın.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Güvenlik duvarının arkasında Azure disk şifrelemesi sorunlarını giderme

Bağlantı bir güvenlik duvarı, proxy gereksinimi veya ağ güvenlik grubu (NSG) ayarları tarafından kısıtlanmışsa, gerekli görevleri gerçekleştirme uzantısının kesintiye uğramayabilir. Bu kesinti, "sanal makine üzerinde uzantı durumu kullanılamıyor" gibi durum iletileri oluşmasına neden olabilir. Beklenen senaryolarda, şifreleme tamamlanamamaktadır. Aşağıdaki bölümlerde araştırmanız gerekebilecek bazı yaygın güvenlik duvarı sorunları bulunmaktadır.

### <a name="network-security-groups"></a>Ağ güvenlik grupları
Uygulanan tüm ağ güvenlik grubu ayarları, uç noktanın disk şifrelemesi için belgelenen ağ yapılandırması [önkoşullarını](disk-encryption-overview.md#networking-requirements) karşıladığından hala izin vermelidir.

### <a name="azure-key-vault-behind-a-firewall"></a>Bir güvenlik duvarının arkasında Azure Key Vault

Şifreleme, [Azure AD kimlik bilgileriyle](disk-encryption-linux-aad.md#)etkinleştirildiğinde, hedef VM hem Azure Active Directory uç noktalarına hem de Key Vault uç noktalarına bağlantı kurulmasına izin vermelidir. Geçerli Azure Active Directory kimlik doğrulama uç noktaları, [Office 365 URL 'leri ve IP adresi aralıkları](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) belgelerinin 56 ve 59 bölümlerinde saklanır. Key Vault yönergeler, [bir güvenlik duvarının arkasındaki Azure Key Vault erişme](../../key-vault/key-vault-access-behind-firewall.md)hakkındaki belgelerde sunulmaktadır.

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
VM, yalnızca VM içinden erişilebilen, iyi bilinen yönlendirilemeyen IP adresini (`169.254.169.254`) kullanan [Azure örnek meta veri hizmeti](../windows/instance-metadata-service.md) uç noktasına erişebilmelidir.  Yerel HTTP trafiğini bu adrese dönüştüren ara sunucu (örneğin, X-Iletilmiş-for üstbilgisi ekleme) desteklenmez.

### <a name="linux-package-management-behind-a-firewall"></a>Bir güvenlik duvarının arkasındaki Linux paket yönetimi

Çalışma zamanında, Linux için Azure disk şifrelemesi, Şifrelemeyi etkinleştirmeden önce gerekli Önkoşul bileşenlerini yüklemek için hedef dağıtımın paket yönetim sistemi bağımlıdır. Güvenlik Duvarı ayarları VM 'nin bu bileşenleri indirip yükleyebilmesini engelliyorsa, sonraki sorunlar beklenir. Bu paket yönetim sistemi yapılandırma adımları dağıtıma göre farklılık gösterebilir. Red hat üzerinde bir ara sunucu gerektiğinde, abonelik Yöneticisi 'nin ve en fazla 'nin düzgün kurulduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [abonelik-yönetici ve sorun giderme sorunları nasıl giderilir](https://access.redhat.com/solutions/189533).  

## <a name="troubleshooting-encryption-status"></a>Şifreleme durumu sorunlarını giderme 

Portal, sanal makine içinde şifrelenmemiş olduktan sonra bile bir disk şifreli olarak görüntülenebilir.  Bu durum, daha yüksek düzeyde Azure disk şifrelemesi yönetim komutları kullanmak yerine, diskin VM içinden doğrudan şifresini kaldırmak için düşük düzey komutlar kullanıldığında meydana gelebilir.  Üst düzey komutlar yalnızca VM 'nin içinden diskin şifresini kaldıramaz, ancak VM 'nin dışında, önemli platform düzeyi şifreleme ayarlarını ve VM ile ilişkili uzantı ayarlarını da güncelleştirir.  Bunlar hizalamayla tutulmazsa, Platform şifreleme durumunu bildiremez veya VM 'yi düzgün şekilde sağlayamaz.   

Azure disk şifrelemesini PowerShell ile devre dışı bırakmak için [Disable-azvmdiskencryption](/powershell/module/az.compute/disable-azvmdiskencryption) ' ı ve ardından [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension)' i kullanın. Şifreleme devre dışı olmadan önce Remove-AzVMDiskEncryptionExtension çalıştırma başarısız olur.

CLı ile Azure disk şifrelemesini devre dışı bırakmak için [az VM Encryption Disable](/cli/azure/vm/encryption)seçeneğini kullanın. 

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure disk şifrelemesi 'nde bazı yaygın sorunlar ve bu sorunları nasıl giderebileceğiniz hakkında daha fazla bilgi edindiniz. Bu hizmet ve özellikleri hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure Güvenlik Merkezi 'nde disk şifrelemeyi uygulama](../../security-center/security-center-apply-disk-encryption.md)
- [Bekleyen Azure veri şifrelemesi](../../security/fundamentals/encryption-atrest.md)
