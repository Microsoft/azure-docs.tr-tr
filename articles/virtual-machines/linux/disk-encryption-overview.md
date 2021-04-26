---
title: Linux VM'leri için Azure Disk Şifrelemesi'ni etkinleştirme
description: Bu makale, Linux sanal makineleri için Microsoft Azure disk şifrelemeyi etkinleştirme hakkında yönergeler sağlar.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a1ea83dc62c2752e34584b89de2cdb6dbde3dfa0
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443927"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Linux VM'leri için Azure Disk Şifrelemesi 

Azure Disk Şifrelemesi verilerinizi koruyarak kurumsal güvenlik ve uyumluluk taahhütlerinizi yerine getirmenize yardımcı olur. Azure sanal makinelerinin (VM 'Ler) işletim sistemi ve veri diskleri için birim şifrelemesi sağlamak üzere Linux 'un [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) özelliğini kullanır ve disk şifreleme anahtarlarını ve gizli dizileri denetlemenize ve yönetmenize yardımcı olmak için [Azure Key Vault](../../key-vault/index.yml) ile tümleşiktir.

Azure disk şifrelemesi, sanal makinelerle aynı şekilde bölge esnektir. Ayrıntılar için bkz. [kullanılabilirlik alanları destekleyen Azure hizmetleri](../../availability-zones/az-region.md).

[Azure Güvenlik Merkezi](../../security-center/index.yml)'ni kullanırsanız, şifrelenmeyen VM 'ler varsa uyarılırsınız. Uyarılar yüksek önem derecesine sahiptir ve bu VM 'Leri şifrelemeniz önerilir.

![Azure Güvenlik Merkezi disk şifreleme uyarısı](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Bir VM 'yi şifrelemek için Azure AD ile Azure disk şifrelemesi 'ni daha önce kullandıysanız, VM 'nizi şifrelemek için bu seçeneği kullanmaya devam etmeniz gerekir. Ayrıntılar için bkz. [Azure AD ile Azure disk şifrelemesi (önceki sürüm)](disk-encryption-overview-aad.md) . 
> - Bazı öneriler veri, ağ veya işlem kaynak kullanımını artırabilir, bu da ek lisans veya abonelik maliyetlerine neden olur. Desteklenen bölgelerde Azure 'da kaynak oluşturmak için geçerli bir etkin Azure aboneliğiniz olması gerekir.

Linux için Azure disk şifrelemesi temellerini yalnızca birkaç dakika içinde [Azure CLI hızlı başlangıç Ile LINUX VM oluşturma ve şifreleme](disk-encryption-cli-quickstart.md) veya [Azure PowerShell hızlı başlangıç Ile Linux VM oluşturma ve şifreleme](disk-encryption-powershell-quickstart.md)hakkında bilgi edinebilirsiniz.

## <a name="supported-vms-and-operating-systems"></a>Desteklenen VM 'Ler ve işletim sistemleri

### <a name="supported-vms"></a>Desteklenen VM 'Ler

Linux VM 'Leri [çeşitli boyutlarda](../sizes.md)kullanılabilir. Azure disk şifrelemesi, 1. nesil ve 2. nesil VM 'lerde desteklenir. Azure disk şifrelemesi, Premium depolama özellikli VM 'Ler için de kullanılabilir.

Bkz. [Yerel geçici disk olmadan Azure VM boyutları](../azure-vms-no-temp-disk.md).

Azure disk şifrelemesi Ayrıca [temel, A serisi VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/)'lerde veya bu minimum bellek gereksinimlerini karşılamayan sanal makinelerde kullanılamaz:

| Sanal makine | Minimum bellek gereksinimi |
|--|--|
| Yalnızca veri birimlerini şifrelerken Linux VM 'Leri| 2 GB |
| Hem veri hem de işletim sistemi birimlerini şifrelerken Linux VM 'Leri ve kök (/) dosya sistemi kullanımının 4 GB veya daha az olduğu durumlar | 8 GB |
| Hem veri hem de işletim sistemi birimlerini şifrelerken Linux VM 'Leri ve kök (/) dosya sistemi kullanımının 4GB'A kadar büyük olması | Kök dosya sistemi kullanımı * 2. Örneğin, 16 GB 'lik bir kök dosya sistemi kullanımı, en az 32 GB RAM gerektirir |

Linux sanal makinelerde işletim sistemi disk şifreleme işlemi tamamlandıktan sonra, VM daha az bellekle çalışacak şekilde yapılandırılabilir.

Daha fazla özel durum için bkz. [Azure disk şifrelemesi: desteklenmeyen senaryolar](disk-encryption-linux.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Azure disk şifrelemesi, [Azure tarafından onaylanan Linux dağıtımların](endorsed-distros.md)bir alt kümesinde desteklenir ve bu, tüm Linux Server olası dağıtımların bir alt kümesidir.

![Azure disk şifrelemesini destekleyen Linux sunucu dağıtımlarından oluşan Venn diyagramı](./media/disk-encryption/ade-supported-distros.png)

Azure tarafından onaylanan Linux sunucu dağıtımları, Azure disk şifrelemesini desteklemez; onaylama işlemleri için, yalnızca aşağıdaki dağıtımlar ve sürümler Azure disk şifrelemesini destekler:


| Publisher | Sunduğu | SKU | URN | Şifreleme için desteklenen birim türü |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18,04-LTS | Kurallı: UbuntuServer: 18.04-LTS: latest | İşletim sistemi ve veri diski |
| Canonical | Ubuntu 18.04 | 18,04-GÜNLÜK-LTS | Kurallı: UbuntuServer: 18.04-DAILY-LTS: latest | İşletim sistemi ve veri diski |
| Canonical | Ubuntu 16.04 | 16,04-GÜNLÜK-LTS | Kurallı: UbuntuServer: 16.04-DAILY-LTS: latest | İşletim sistemi ve veri diski |
| Canonical | Ubuntu 14.04.5</br>[Azure 'da ayarlanmış çekirdek, 4,15 veya üzeri bir sürüme güncelleştirildi](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Kurallı: UbuntuServer: 14.04.5-LTS: latest | İşletim sistemi ve veri diski |
| Canonical | Ubuntu 14.04.5</br>[Azure 'da ayarlanmış çekirdek, 4,15 veya üzeri bir sürüme güncelleştirildi](disk-encryption-troubleshooting.md) | 14.04.5-GÜNLÜK-LTS | Kurallı: UbuntuServer: 14.04.5-DAILY-LTS: latest | İşletim sistemi ve veri diski |
| RedHat | RHEL 8-LVM | 8-LVM | RedHat: RHEL: 8-LVM: latest | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 8,2 | 8.2 | RedHat: RHEL: 8.2: en son | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 8.1 | 8.1 | RedHat: RHEL: 8.1: latest | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 7-LVM | 7-LVM | RedHat: RHEL: 7-LVM: 7.9.2020111202 | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 7,9 | 7_9 | RedHat: RHEL: 7_9: en son | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 7,8 | 7.8 | RedHat: RHEL: 7,8: latest | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 7,7 | 7.7 | RedHat: RHEL: 7.7: latest | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 7,6 | 7.6 | RedHat: RHEL: 7.6: latest | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 7.5 | 7,5 | RedHat: RHEL: 7.5: en son | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 7,4 | 7.4 | RedHat: RHEL: 7.4: latest | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 7,3 | 7.3 | RedHat: RHEL: 7.3: latest | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 7,2 | 7.2 | RedHat: RHEL: 7.2: latest | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 6,8 | 6.8 | RedHat: RHEL: 6.8: latest | Veri diski (aşağıdaki nota bakın) |
| RedHat | RHEL 6,7 | 6.7 | RedHat: RHEL: 6.7: latest | Veri diski (aşağıdaki nota bakın) |
| OpenLogic | CentOS 8-LVM | 8-LVM | OpenLogic: CentOS-LVM: 8-LVM: en son | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 8,2 | 8_2 | OpenLogic: CentOS: 8_2: latest | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 8,1 | 8_1 | OpenLogic: CentOS: 8_1: en son | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 7-LVM | 7-LVM | OpenLogic: CentOS-LVM: 7-LVM: 7.9.2021020400 | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 7,9 | 7_9 | OpenLogic: CentOS: 7_9: en son | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 7,8 | 7_8 | OpenLogic: CentOS: 7_8: en son | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 7,7 | 7.7 | OpenLogic: CentOS: 7.7: latest | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 7,6 | 7.6 | OpenLogic: CentOS: 7.6: latest | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 7.5 | 7,5 | OpenLogic: CentOS: 7.5: en son | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic: CentOS: 7.4: latest | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 7,3 | 7.3 | OpenLogic: CentOS: 7.3: en son | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 7.2 n | 7.2 n | OpenLogic: CentOS: 7.2 n: en son | İşletim sistemi ve veri diski |
| OpenLogic | CentOS 7,1 | 7.1 | OpenLogic: CentOS: 7.1: en son | Yalnızca veri diski |
| OpenLogic | CentOS 7,0 | 7.0 | OpenLogic: CentOS: 7.0: en son | Yalnızca veri diski |
| OpenLogic | CentOS 6,8 | 6.8 | OpenLogic: CentOS: 6.8: latest | Yalnızca veri diski |
| SUSE | openSUSE 42,3 | 42,3 | SUSE: openSUSE-artık: 42.3: latest | Yalnızca veri diski |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE: SLES: 12-SP4: en son | Yalnızca veri diski |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE: SLES-HPC: 12-SP3: en son | Yalnızca veri diski |

> [!NOTE]
> Yeni Azure disk şifrelemesi uygulama, RHEL7 Kullandıkça Öde görüntüleri için RHEL OS ve veri diski için desteklenir.  
>
> Ayrıca, yalnızca abonelik **kaydedildikten sonra** da RHEL-kendi aboneliği getir Gold GÖRÜNTÜLERI için Ade desteklenir. Daha fazla bilgi için bkz. [Azure 'Da kendi aboneliğinizi getir Gold görüntülerini Red Hat Enterprise Linux](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Ek VM gereksinimleri

Azure disk şifrelemesi, sistemde dm-crypt ve VFAT modüllerinin bulunmasını gerektirir. VFAT 'i varsayılan görüntüden kaldırmak veya devre dışı bırakmak, sistemin anahtar birimini okumasını ve sonraki yeniden başlatmalarda disklerin kilidini açmak için gereken anahtarı almasını engeller. VFAT modülünü sistemden kaldırmak veya veri sürücülerindeki işletim sistemi bağlama noktalarını/klasörlerini genişletmeyi zorlamak için sistem sağlamlaştırma adımları Azure disk şifrelemesi ile uyumlu değildir. 

Şifrelemeyi etkinleştirmeden önce, şifrelenecek veri disklerinin/etc/fstabnda doğru şekilde listelenmesi gerekir. Giriş oluştururken "NOFAIL" seçeneğini kullanın ve kalıcı bir cihaz adı seçin ("/dev/sdX" biçimindeki cihaz adları yeniden başlatmalar genelinde aynı disk ile ilişkilendirilemeyebilir, özellikle şifrelemeden sonra, bu davranış hakkında daha fazla bilgi için bkz.: [LINUX VM cihaz adı değişiklikleri sorunlarını giderme](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)).

/Etc/fstab ayarlarının bağlama için doğru yapılandırıldığından emin olun. Bu ayarları yapılandırmak için, Mount-a komutunu çalıştırın veya VM 'yi yeniden başlatın ve bu şekilde uzak yeniden bağlama 'yı tetikleyin. Bu tamamlandıktan sonra, sürücünün hala bağlı olduğunu doğrulamak için lsblk komutunun çıkışını kontrol edin. 

- /Etc/fstab dosyası Şifrelemeyi etkinleştirmeden önce sürücüyü doğru bağmazsa, Azure disk şifrelemesi onu düzgün bir şekilde bağlanamaz.
- Azure Disk Şifrelemesi işlemi, bağlama bilgilerini şifreleme sürecinin bir parçası olarak/etc/fstab ' dan ve kendi yapılandırma dosyasına taşıyacaktır. Veri sürücüsü şifrelemesi tamamlandıktan sonra/etc/fstab içinde eksik girişi görmek için uyarıda yok.
- Şifrelemeyi başlatmadan önce, bağlı veri disklerine yazmak ve devre dışı bırakmak için yeniden başlatmadan sonra otomatik olarak yeniden başlatabilmeleri için tüm hizmetleri ve süreçlerini durdurmayı unutmayın. Bunlar, dosyaları bu bölümlerde açık tutabilir, böylece şifreleme yordamının yeniden bağlanmasını önler ve bu da şifrelemenin başarısız olmasına neden olur. 
- Yeniden başlatmadan sonra, Azure disk şifrelemesi işleminin yeni şifrelenmiş diskleri bağlaması zaman alır. Yeniden başlatmadan sonra hemen kullanılamayacak. İşlem, diğer işlemlerin erişebilmesi için önce şifrelenmiş sürücüleri açmak, kilitlerini açmak ve ardından bağlamak için zaman gerektirir. Bu işlem, sistem özelliklerine bağlı olarak yeniden başlatma sonrasında bir dakikadan uzun sürebilir.

Veri disklerini bağlamak ve gerekli/etc/fstab girdilerini oluşturmak için kullanılan komutlara bir örnek aşağıda verilmiştir:

```bash
UUID0="$(blkid -s UUID -o value /dev/sda1)"
UUID1="$(blkid -s UUID -o value /dev/sda2)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>Ağ gereksinimleri

Azure Disk Şifrelemesi özelliğini etkinleştirmek için Linux VM 'lerinin aşağıdaki ağ uç noktası yapılandırma gereksinimlerini karşılaması gerekir:
  - Anahtar kasanıza bağlanma belirteci almak için, Linux VM 'nin bir Azure Active Directory uç noktasına bağlanabilmesi gerekir, \[ login.microsoftonline.com \] .
  - Şifreleme anahtarlarını anahtar kasanıza yazmak için, Linux VM 'nin Anahtar Kasası uç noktasına bağlanabiliyor olması gerekir.
  - Linux VM, Azure uzantı deposunu barındıran bir Azure depolama uç noktasına ve VHD dosyalarını barındıran bir Azure depolama hesabına bağlanabilmelidir.
  -  Güvenlik ilkeniz, Azure VM 'lerinden Internet 'e erişimi sınırlayıp, önceki URI 'yi çözümleyebilir ve IP 'lere giden bağlantılara izin vermek için belirli bir kuralı yapılandırabilirsin. Daha fazla bilgi için bkz. [Azure Key Vault bir güvenlik duvarı arkasında](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Şifreleme anahtarı depolama gereksinimleri  

Azure disk şifrelemesi, disk şifreleme anahtarlarını ve gizli dizileri denetlemek ve yönetmek için bir Azure Key Vault gerektirir. Anahtar kasanızın ve sanal makinelerinizin aynı Azure bölgesinde ve abonelikte bulunması gerekir.

Ayrıntılar için bkz. [Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminoloji

Aşağıdaki tabloda, Azure disk şifrelemesi belgelerinde kullanılan bazı yaygın terimler tanımlanmaktadır:

| Terminoloji | Tanım |
| --- | --- |
| Azure Key Vault | Key Vault, Federal bilgi Işleme standartları (FIPS) tarafından doğrulanan donanım güvenlik modüllerini temel alan bir şifreleme, anahtar yönetim hizmetidir. Bu standartlar, şifreleme anahtarlarınızı ve hassas gizli dizileri korumaya yardımcı olur. Daha fazla bilgi için bkz. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) belgeleri ve [Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI,](/cli/azure/install-azure-cli) Azure kaynaklarını komut satırından yönetmek ve yönetmek için iyileştirilmiştir.|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) , Linux sanal makinelerinde disk şifrelemeyi etkinleştirmek Için kullanılan Linux tabanlı, saydam disk şifreleme alt sistemidir. |
| Anahtar şifreleme anahtarı (KEK) | Gizli anahtarı korumak veya kaydırmak için kullanabileceğiniz asimetrik anahtar (RSA 2048). Donanım güvenlik modülü (HSM) korumalı bir anahtar veya yazılımla korunan anahtar sağlayabilirsiniz. Daha fazla bilgi için bkz. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) belgeleri ve [Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md). |
| PowerShell cmdlet'leri | Daha fazla bilgi için bkz. [Azure PowerShell cmdlet 'leri](/powershell/azure/). |


## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç-Azure CLı ile Linux VM oluşturma ve şifreleme ](disk-encryption-cli-quickstart.md)
- [Hızlı başlangıç-Azure PowerShell bir Linux sanal makinesi oluşturma ve şifreleme](disk-encryption-powershell-quickstart.md) 
- [Linux VM’lerde Azure Disk Şifrelemesi senaryoları](disk-encryption-linux.md)
- [Azure disk şifrelemesi önkoşulları CLı betiği](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure disk şifrelemesi önkoşulları PowerShell betiği](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Şifrelemesi için anahtar kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md)