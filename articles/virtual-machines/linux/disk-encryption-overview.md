---
title: Linux sanal makineleri için Azure disk şifrelemesini etkinleştirme
description: Bu makale, Linux sanal makineleri için Microsoft Azure disk şifrelemeyi etkinleştirme hakkında yönergeler sağlar.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a3d48d53c2d4d0c859b58a94b12ffa94590b18a5
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989626"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Linux sanal makineleri için Azure disk şifrelemesi 

Azure disk şifrelemesi, kuruluşunuzun güvenlik ve uyumluluk taahhütlerinizi karşılamak için verilerinizi korumanıza ve korumaya yardımcı olur. Azure sanal makinelerinin (VM 'Ler) işletim sistemi ve veri diskleri için birim şifrelemesi sağlamak üzere Linux 'un [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) özelliğini kullanır ve disk şifreleme anahtarlarını ve gizli dizileri denetlemenize ve yönetmenize yardımcı olmak için [Azure Key Vault](../../key-vault/index.yml) ile tümleşiktir. 

[Azure Güvenlik Merkezi](../../security-center/index.yml)'ni kullanırsanız, şifrelenmeyen VM 'ler varsa uyarılırsınız. Uyarılar yüksek önem derecesine sahiptir ve bu VM 'Leri şifrelemeniz önerilir.

![Azure Güvenlik Merkezi disk şifreleme uyarısı](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Bir VM 'yi şifrelemek için Azure AD ile Azure disk şifrelemesi 'ni daha önce kullandıysanız, VM 'nizi şifrelemek için bu seçeneği kullanmaya devam etmeniz gerekir. Ayrıntılar için bkz. [Azure AD ile Azure disk şifrelemesi (önceki sürüm)](disk-encryption-overview-aad.md) . 
> - Bazı öneriler veri, ağ veya işlem kaynak kullanımını artırabilir, bu da ek lisans veya abonelik maliyetlerine neden olur. Desteklenen bölgelerde Azure 'da kaynak oluşturmak için geçerli bir etkin Azure aboneliğiniz olması gerekir.

Linux için Azure disk şifrelemesi temellerini, [Azure CLI hızlı başlangıç Ile LINUX VM oluşturma ve şifreleme](disk-encryption-cli-quickstart.md) ile yalnızca birkaç dakika Içinde ve [Azure PowerShell hızlı BAŞLANGıCı Ile Linux VM oluşturma ve şifreleme](disk-encryption-powershell-quickstart.md)hakkında bilgi edinebilirsiniz.

## <a name="supported-vms-and-operating-systems"></a>Desteklenen VM 'Ler ve işletim sistemleri

### <a name="supported-vm-sizes"></a>Desteklenen VM boyutları

Linux VM 'Leri [çeşitli boyutlarda](sizes.md)kullanılabilir. Azure disk şifrelemesi, [temel, A serisi VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/)'lerde veya bu minimum bellek gereksinimlerini karşılamayan sanal makinelerde kullanılamaz:

| Sanal makine | Minimum bellek gereksinimi |
|--|--|
| Yalnızca veri birimlerini şifrelerken Linux VM 'Leri| 2 GB |
| Hem veri hem de işletim sistemi birimlerini şifrelerken Linux VM 'Leri ve kök (/) dosya sistemi kullanımının 4 GB veya daha az olduğu durumlar | 8 GB |
| Hem veri hem de işletim sistemi birimlerini şifrelerken Linux VM 'Leri ve kök (/) dosya sistemi kullanımının 4GB'A kadar büyük olması | Kök dosya sistemi kullanımı * 2. Örneğin, 16 GB 'lik bir kök dosya sistemi kullanımı, en az 32 GB RAM gerektirir |

Linux sanal makinelerde işletim sistemi disk şifreleme işlemi tamamlandıktan sonra, VM daha az bellekle çalışacak şekilde yapılandırılabilir. 

Azure disk şifrelemesi, Premium depolama özellikli VM 'Ler için de kullanılabilir. 

### <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Azure disk şifrelemesi, [Azure tarafından onaylanan Linux dağıtımların](endorsed-distros.md)bir alt kümesinde desteklenir ve bu, tüm Linux Server olası dağıtımların bir alt kümesidir.

![Azure disk şifrelemesini destekleyen Linux sunucu dağıtımlarından oluşan Venn diyagramı](./media/disk-encryption/ade-supported-distros.png)

Azure tarafından onaylanan Linux sunucu dağıtımları, Azure disk şifrelemesini desteklemez; onaylama işlemleri için, yalnızca aşağıdaki dağıtımlar ve sürümler Azure disk şifrelemesini destekler:

| Linux dağıtımı | Sürüm | Şifreleme için desteklenen birim türü|
| --- | --- |--- |
| Ubuntu | 18,04| İşletim sistemi ve veri diski |
| Ubuntu | 16,04| İşletim sistemi ve veri diski |
| Ubuntu | 14.04.5</br>[Azure 'da ayarlanmış çekirdek, 4,15 veya üzeri bir sürüme güncelleştirildi](disk-encryption-troubleshooting.md) | İşletim sistemi ve veri diski |
| RHEL | 7,7 | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RHEL | 7,6 | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RHEL | 7,5 | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RHEL | 7,4 | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RHEL | 7.3 | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RHEL | 7.2 | İşletim sistemi ve veri diski (aşağıdaki nota bakın) |
| RHEL | 6.8 | Veri diski (aşağıdaki nota bakın) |
| RHEL | 6.7 | Veri diski (aşağıdaki nota bakın) |
| CentOS | 7,7 | İşletim sistemi ve veri diski |
| CentOS | 7,6 | İşletim sistemi ve veri diski |
| CentOS | 7,5 | İşletim sistemi ve veri diski |
| CentOS | 7,4 | İşletim sistemi ve veri diski |
| CentOS | 7.3 | İşletim sistemi ve veri diski |
| CentOS | 7.2 n | İşletim sistemi ve veri diski |
| CentOS | 6.8 | Veri diski |
| openSUSE | 42,3 | Veri diski |
| SLES | 12-SP4 | Veri diski |
| SLES | 12-SP3 | Veri diski |

> [!NOTE]
> Yeni ADE uygulama, RHEL7 Kullandıkça Öde görüntüleri için RHEL OS ve veri diski için desteklenir. ADE Şu anda RHEL kendi abonelik (KCG) görüntüleri için desteklenmiyor. 

## <a name="additional-vm-requirements"></a>Ek VM gereksinimleri

Azure disk şifrelemesi, sistemde dm-crypt ve VFAT modüllerinin bulunmasını gerektirir. VFAT 'i varsayılan görüntüden kaldırmak veya devre dışı bırakmak, sistemin anahtar birimini okumasını ve sonraki yeniden başlatmalarda disklerin kilidini açmak için gereken anahtarı almasını engeller. VFAT modülünü sistemden kaldırmak için sistem sağlamlaştırma adımları Azure disk şifrelemesi ile uyumlu değildir. 

Şifrelemeyi etkinleştirmeden önce, şifrelenecek veri disklerinin/etc/fstabnda doğru şekilde listelenmesi gerekir. "/Dev/sdX" biçimindeki cihaz adları, özellikle şifreleme uygulandıktan sonra, yeniden başlatmalar genelinde aynı diskle ilişkilendirilemediğinden, bu giriş için kalıcı bir blok cihaz adı kullanın. Bu davranış hakkında daha fazla bilgi için bkz. [LINUX VM cihaz adı değişikliklerinde sorun giderme](troubleshoot-device-names-problems.md)

/Etc/fstab ayarlarının bağlama için doğru yapılandırıldığından emin olun. Bu ayarları yapılandırmak için, Mount-a komutunu çalıştırın veya VM 'yi yeniden başlatın ve bu şekilde uzak yeniden bağlama 'yı tetikleyin. Bu tamamlandıktan sonra, sürücünün hala bağlı olduğunu doğrulamak için lsblk komutunun çıkışını kontrol edin. 
- /Etc/fstab dosyası Şifrelemeyi etkinleştirmeden önce sürücüyü doğru bağmazsa, Azure disk şifrelemesi onu düzgün bir şekilde bağlanamaz.
- Azure Disk Şifrelemesi işlemi, bağlama bilgilerini şifreleme sürecinin bir parçası olarak/etc/fstab ' dan ve kendi yapılandırma dosyasına taşıyacaktır. Veri sürücüsü şifrelemesi tamamlandıktan sonra/etc/fstab içinde eksik girişi görmek için uyarıda yok.
- Şifrelemeyi başlatmadan önce, bağlı veri disklerine yazmak ve devre dışı bırakmak için yeniden başlatmadan sonra otomatik olarak yeniden başlatabilmeleri için tüm hizmetleri ve süreçlerini durdurmayı unutmayın. Bunlar, dosyaları bu bölümlerde açık tutabilir, böylece şifreleme yordamının yeniden bağlanmasını önler ve bu da şifrelemenin başarısız olmasına neden olur. 
- Yeniden başlatmadan sonra, Azure disk şifrelemesi işleminin yeni şifrelenmiş diskleri bağlaması zaman alır. Yeniden başlatmadan sonra hemen kullanılamayacak. İşlem, diğer işlemlerin erişebilmesi için önce şifrelenmiş sürücüleri açmak, kilitlerini açmak ve ardından bağlamak için zaman gerektirir. Bu işlem, sistem özelliklerine bağlı olarak yeniden başlatma sonrasında bir dakikadan uzun sürebilir.

Veri disklerini bağlamak ve gerekli/etc/fstab girdilerini oluşturmak için kullanılabilen komutların bir örneği, [Azure disk şifrelemesi ÖNKOŞULLARı CLI betiği](https://github.com/ejarvi/ade-cli-getting-started) (satırlar 244-248) ve [Azure disk şifrelemesi önkoşulları PowerShell 'de bulunabilir. betiği](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Ağ gereksinimleri

Azure Disk Şifrelemesi özelliğini etkinleştirmek için Linux VM 'lerinin aşağıdaki ağ uç noktası yapılandırma gereksinimlerini karşılaması gerekir:
  - Anahtar kasanıza bağlanma belirteci almak için, Linux VM 'nin bir Azure Active Directory uç noktasına bağlanabilmesi gerekir \[login.microsoftonline.com\].
  - Şifreleme anahtarlarını anahtar kasanıza yazmak için, Linux VM 'nin Anahtar Kasası uç noktasına bağlanabiliyor olması gerekir.
  - Linux VM, Azure uzantı deposunu barındıran bir Azure depolama uç noktasına ve VHD dosyalarını barındıran bir Azure depolama hesabına bağlanabilmelidir.
  -  Güvenlik ilkeniz, Azure VM 'lerinden Internet 'e erişimi sınırlayıp, önceki URI 'yi çözümleyebilir ve IP 'lere giden bağlantılara izin vermek için belirli bir kuralı yapılandırabilirsin. Daha fazla bilgi için bkz. [Azure Key Vault bir güvenlik duvarı arkasında](../../key-vault/key-vault-access-behind-firewall.md).  

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
| PowerShell cmdlet'leri | Daha fazla bilgi için bkz. [Azure PowerShell cmdlet 'leri](/powershell/azure/overview). |


## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç-Azure CLı ile Linux VM oluşturma ve şifreleme](disk-encryption-cli-quickstart.md)
- [Hızlı başlangıç-Azure PowerShell ile Linux VM oluşturma ve şifreleme](disk-encryption-powershell-quickstart.md)
- [Linux VM 'lerinde Azure disk şifrelemesi senaryoları](disk-encryption-linux.md)
- [Azure disk şifrelemesi önkoşulları CLı betiği](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure disk şifrelemesi önkoşulları PowerShell betiği](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md)


