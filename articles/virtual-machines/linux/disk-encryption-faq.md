---
title: SSS-Linux sanal makineleri için Azure disk şifrelemesi
description: Bu makalede, Linux IaaS VM 'Leri için Microsoft Azure disk şifrelemesi hakkında sık sorulan sorulara yanıtlar verilmektedir.
author: msmbaldwin
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 6a85af1a5e0603d78dc9bc233fef56417e19c50e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553094"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>Linux sanal makineleri için Azure disk şifrelemesi hakkında SSS

Bu makalede, Linux sanal makineleri (VM 'Ler) için Azure disk şifrelemesi hakkında sık sorulan soruların (SSS) yanıtları sağlanmaktadır. Bu hizmet hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi 'ne genel bakış](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>Linux VM 'Leri için Azure disk şifrelemesi nedir?

Linux sanal makineleri için Azure disk şifrelemesi, Linux 'un dm-crypt özelliğini kullanarak işletim sistemi diski * ve veri disklerinin tam disk şifrelemesini sağlar. Ayrıca, [Encryptformatall özelliği](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)kullanılırken geçici disk şifrelemesi de sağlar. İçerik, VM 'den depolama arka uca şifrelenmiş olarak akar. Bu nedenle, müşteri tarafından yönetilen bir anahtarla uçtan uca şifreleme sağlar.
 
Bkz. [desteklenen VM 'ler ve işletim sistemleri](disk-encryption-overview.md#supported-vms-and-operating-systems).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Azure disk şifrelemesi genel kullanıma sunuldu (GA)?

Linux sanal makineleri için Azure disk şifrelemesi, tüm Azure genel bölgelerinde genel kullanıma açıktır.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure disk şifrelemesi ile hangi kullanıcı deneyimleri mevcuttur?

Azure disk şifrelemesi GA, Azure Resource Manager şablonları, Azure PowerShell ve Azure CLı 'yi destekler. Farklı kullanıcı deneyimleri size esneklik sağlar. Sanal makinelerinize disk şifrelemeyi etkinleştirmek için üç farklı seçeneğiniz vardır. Kullanıcı deneyimi ve Azure disk şifrelemesi 'nde sunulan adım adım yönergeler hakkında daha fazla bilgi için bkz. [Linux Için Azure disk şifrelemesi senaryoları](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure disk şifreleme maliyeti ne kadar sürer?

Azure disk şifrelemesi ile VM disklerini şifrelemek için ücret alınmaz, ancak Azure Key Vault kullanımıyla ilişkili ücretler vardır. Azure Key Vault maliyetler hakkında daha fazla bilgi için [Key Vault fiyatlandırma](https://azure.microsoft.com/pricing/details/key-vault/) sayfasına bakın.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Azure disk şifrelemesi 'ni kullanmaya nasıl başlayabilirim?

Başlamak için [Azure disk şifrelemesi 'ne genel bakış](disk-encryption-overview.md)makalesini okuyun.

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Azure disk şifrelemesini hangi VM boyutları ve işletim sistemleri destekler?

[Azure disk şifrelemesi genel bakış](disk-encryption-overview.md) makalesi, Azure disk şifrelemesini destekleyen [VM boyutlarını](disk-encryption-overview.md#supported-vms) ve [VM işletim sistemlerini](disk-encryption-overview.md#supported-operating-systems) listeler.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Hem önyükleme hem de veri hacimlerini Azure disk şifrelemesi ile şifreleyebilir miyim?

Evet, hem önyükleme hem de veri birimlerini şifreleyebilir veya önce işletim sistemi birimini şifrelemek zorunda kalmadan veri birimini şifreleyebilirsiniz. 

İşletim sistemi birimini şifreledikten sonra, işletim sistemi biriminde şifrelemeyi devre dışı bırakmak desteklenmez. Ölçek kümesindeki Linux VM 'Ler için yalnızca veri hacmi şifrelenebilir.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Takılmamış bir birimi Azure disk şifrelemesi ile şifreleyebilir miyim?

Hayır, Azure disk şifrelemesi yalnızca bağlı birimleri şifreler.

## <a name="what-is-storage-server-side-encryption"></a>Depolama sunucu tarafı şifrelemesi nedir?

Depolama sunucu tarafı şifrelemesi, Azure depolama 'da Azure yönetilen disklerini şifreler. Yönetilen diskler, platform tarafından yönetilen bir anahtarla sunucu tarafı şifreleme (10 Haziran 2017 itibariyle) ile varsayılan olarak şifrelenir. Yönetilen disklerin şifrelemesini, kendi anahtarlarınız ile, müşteri tarafından yönetilen bir anahtar belirterek yönetebilirsiniz. Daha fazla bilgi için bkz. [Azure yönetilen disklerin sunucu tarafı şifrelemesi](../disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Azure disk şifrelemesi, müşteri tarafından yönetilen anahtarla depolama sunucusu tarafı şifrelemeden farklı ve her çözümü ne zaman kullanmalıyım?

Azure disk şifrelemesi, müşteri tarafından yönetilen anahtar kullanarak işletim sistemi diski, veri diskleri ve geçici disk için uçtan uca şifreleme sağlar.
- Gereksinimleriniz yukarıdaki ve uçtan uca şifrelemeyi şifrelemeyi içeriyorsa, Azure disk şifrelemesi ' ni kullanın. 
- Gereksinimleriniz, müşteri tarafından yönetilen anahtarla yalnızca bekleyen verileri şifrelemeyi içeriyorsa, [müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifrelemeyi](../disk-encryption.md)kullanın. Müşteri tarafından yönetilen anahtarlarla hem Azure disk şifrelemesi hem de depolama sunucu tarafı şifrelemesi ile bir disk şifrelenemez. 
- Linux oturumunuz [Azure disk şifrelemesi için desteklenen işletim sistemleri](disk-encryption-overview.md#supported-operating-systems) altında listelenmiyorsa veya [Windows için desteklenmeyen senaryolarda](disk-encryption-linux.md#unsupported-scenarios)adlı bir senaryo kullanıyorsanız, [müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifrelemeyi](../disk-encryption.md)göz önünde bulundurun.
- Kuruluşunuzun ilkesi, Azure tarafından yönetilen bir anahtarla Rest içeriğini şifrelemenize izin veriyorsa, hiçbir eylemde bulunmanız gerekmez; içerik varsayılan olarak şifrelenir. Yönetilen diskler için, depolama içindeki içerikler, platform tarafından yönetilen anahtarla sunucu tarafı şifreleme ile varsayılan olarak şifrelenir. Anahtar, Azure depolama hizmeti tarafından yönetilir. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Gizli dizileri veya şifreleme anahtarlarını Nasıl yaparım? mı döndürün?

Gizli dizileri döndürmek için, disk şifrelemeyi etkinleştirmek üzere yalnızca ilk kullandığınız komutu çağırın, farklı bir Key Vault belirtin. Anahtar şifreleme anahtarını döndürmek için, yeni anahtar şifrelemesini belirterek disk şifrelemeyi etkinleştirmek üzere başlangıçta kullandığınız komutu çağırın. 

>[!WARNING]
> - Bu VM 'yi şifrelemek için Azure AD kimlik bilgilerini belirterek daha önce Azure [ad uygulaması Ile Azure disk şifrelemesi](disk-encryption-linux-aad.md) KULLANDıYSANıZ, VM 'nizi şifrelemek için bu seçeneği kullanmaya devam etmeniz gerekir. Bu desteklenen bir senaryo olmadığından, bu şifrelenmiş VM 'de Azure disk şifrelemesi 'ni kullanamazsınız. Bu, bu şifrelenmiş VM için AAD uygulamasından uzağa geçiş henüz desteklenmiyor.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>İlk olarak bir tane kullanmadıysa, anahtar şifreleme anahtarı ekleme veya kaldırma Nasıl yaparım??

Anahtar şifreleme anahtarı eklemek için, anahtar şifreleme anahtar parametresini geçirerek Etkinleştir komutunu yeniden çağırın. Anahtar şifreleme anahtarını kaldırmak için anahtar şifreleme anahtarı parametresi olmadan Etkinleştir komutunu yeniden çağırın.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure disk şifrelemesi kendi anahtarınızı (BYOK) getirmenize izin veriyor mu?

Evet, kendi anahtar şifreleme anahtarlarınızı sağlayabilirsiniz. Bu anahtarlar, Azure disk şifrelemesi için anahtar deposu olan Azure Key Vault karşı korunmuş bir koruma. Anahtar şifreleme anahtarları destek senaryoları hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Azure tarafından oluşturulan bir anahtar şifreleme anahtarı kullanabilir miyim?

Evet, Azure disk şifrelemesi kullanımı için anahtar şifreleme anahtarı oluşturmak üzere Azure Key Vault kullanabilirsiniz. Bu anahtarlar, Azure disk şifrelemesi için anahtar deposu olan Azure Key Vault karşı korunmuş bir koruma. Anahtar şifreleme anahtarı hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Şifreleme anahtarlarını korumak için bir şirket içi anahtar yönetimi hizmeti veya HSM kullanabilir miyim?

Azure disk şifrelemesi ile şifreleme anahtarlarını korumak için şirket içi anahtar yönetimi hizmetini veya HSM 'yi kullanamazsınız. Şifreleme anahtarlarını korumak için yalnızca Azure Key Vault hizmetini kullanabilirsiniz. Anahtar şifreleme anahtarı destek senaryoları hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Azure disk şifrelemesi 'ni yapılandırma önkoşulları nelerdir?

Azure disk şifrelemesi için Önkoşullar vardır. Yeni bir Anahtar Kasası oluşturmak için [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md) makalesine bakın veya şifrelemeyi etkinleştirmek için disk şifreleme erişimi için mevcut bir Anahtar Kasası kurun ve gizli dizileri ve anahtarları koruyun. Anahtar şifreleme anahtarı destek senaryoları hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Azure disk şifrelemesini bir Azure AD uygulamasıyla (önceki sürüm) yapılandırma önkoşulları nelerdir?

Azure disk şifrelemesi için Önkoşullar vardır. Azure Active Directory bir uygulama oluşturmak, yeni bir Anahtar Kasası oluşturmak veya şifrelemeyi etkinleştirmek için disk şifreleme erişimi için mevcut bir Anahtar Kasası ayarlamak ve gizli dizileri ve anahtarları korumak için Azure AD içeriğiyle Azure [disk şifrelemesi](disk-encryption-linux-aad.md) ' ne bakın. Anahtar şifreleme anahtarı destek senaryoları hakkında daha fazla bilgi için bkz. Azure [ad Ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure AD uygulaması (önceki sürüm) kullanılarak Azure disk şifrelemesi hala destekleniyor mu?
Evet. Azure AD uygulaması kullanan disk şifrelemesi hala desteklenmektedir. Ancak, yeni VM 'Leri şifrelerken bir Azure AD uygulamasıyla şifreleme yerine yeni yöntemi kullanmanız önerilir. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Bir Azure AD uygulamasıyla şifrelenen VM 'Leri bir Azure AD uygulaması olmadan şifrelemeye geçirebilir miyim?
  Şu anda, bir Azure AD uygulamasıyla şifrelenmiş olan makineler için bir Azure AD uygulaması olmadan bir doğrudan geçiş yolu yoktur. Ayrıca, bir Azure AD uygulaması olmadan şifrelemeden bir AD uygulamasıyla şifrelemeye doğrudan yol yoktur. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure disk şifrelemesi hangi Azure PowerShell sürümünde desteklenir?

Azure disk şifrelemesini yapılandırmak için Azure PowerShell SDK 'sının en son sürümünü kullanın. En son [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)sürümünü indirin. Azure disk *şifrelemesi, Azure* SDK sürüm 1.1.0 tarafından desteklenmez.

> [!NOTE]
> Linux Azure disk şifrelemesi önizleme uzantısı "Microsoft. OSTCExtension. AzureDiskEncryptionForLinux" kullanım dışıdır. Bu uzantı Azure disk şifrelemesi önizleme sürümü için yayımlandı. Test veya üretim dağıtımınızdaki uzantının önizleme sürümünü kullanmamalısınız.

> Linux IaaS VM 'niz üzerinde şifrelemeyi etkinleştirmek için Linux VM için Azure disk şifrelemesi uzantısını dağıtmanız gereken Azure Resource Manager (ARM) gibi dağıtım senaryolarında, Azure disk şifrelemesi üretimi desteklenen "Microsoft. Azure. Security. AzureDiskEncryptionForLinux" uzantısını kullanmanız gerekir.

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Özel Linux görüntüme Azure disk şifrelemesi uygulayabilir miyim?

Özel Linux yansımanıza Azure disk şifrelemesi uygulayamazsınız. Yalnızca daha önce çağrılan desteklenen dağıtımlar için Galeri Linux görüntüleri desteklenir. Özel Linux görüntüleri şu anda desteklenmiyor.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Bir Linux Red Hat VM 'sine, bu güncelleştirmeyi kullanan güncelleştirmeleri uygulayabilir miyim?

Evet, Red Hat Linux sanal makinesi üzerinde bir yum güncelleştirmesi yapabilirsiniz.  Daha fazla bilgi için bkz. [yalıtılmış bir ağda Azure disk şifrelemesi](disk-encryption-isolated-network.md).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Linux için önerilen Azure disk şifrelemesi iş akışı nedir?

Linux üzerinde en iyi sonuçları elde etmek için aşağıdaki iş akışının kullanılması önerilir:
* Gerekli işletim sistemi ve sürüme karşılık gelen değiştirilmemiş hisse senedi Galerisi görüntüsünden başlayın
* Şifrelenecek tüm bağlı sürücüleri yedekleyin.  Bu yedekleme, bir hata oluşursa kurtarma için izin verir, örneğin, şifreleme tamamlanmadan önce sanal makine yeniden başlatılır.
* Şifreleme (VM özelliklerine ve bağlı veri disklerinin boyutuna bağlı olarak birkaç saat, hatta gün sürebilir)
* Gerektiğinde görüntüye yazılım ekleyin ve ekleyin.

Bu iş akışı mümkün değilse, platform depolama hesabı katmanında [depolama hizmeti şifrelemesi](../../storage/common/storage-service-encryption.md) (SSE) bağlı olarak, dm-crypt kullanılarak tam disk şifrelemesi için bir alternatif olabilir.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>"Bek Volume" veya "/mnt/azure_bek_disk" diski nedir?

"Bek Volume", şifrelenmiş Azure VM 'Ler için şifreleme anahtarlarını güvenli bir şekilde depolayan yerel bir veri birimidir.
> [!NOTE]
> Bu diskteki içeriği silmeyin veya düzenleyemezsiniz. IaaS VM 'deki herhangi bir şifreleme işlemi için şifreleme anahtarı varlığı gerektiğinden diski çıkarın.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure disk şifrelemesi hangi şifreleme yöntemini kullanıyor?

Azure disk şifrelemesi, 256 bitlik bir birim ana anahtarıyla AES-XTS-plain64 şifre çözme varsayılanını kullanır.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>EncryptFormatAll kullanıyorum ve tüm birim türlerini belirtirseniz, zaten Şifrelediğimiz veri sürücülerindeki verileri silecek mı?
Hayır, Azure disk şifrelemesi kullanılarak önceden şifrelenmiş veri sürücülerinden veri silinmeyecektir. EncryptFormatAll 'ın işletim sistemi sürücüsünü yeniden şifrelemeine benzer şekilde, zaten şifrelenmiş veri sürücüsünü yeniden şifrelemez. Daha fazla bilgi için bkz. [Encryptformatall ölçütleri](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>XFS FileSystem destekleniyor mu?
XFS işletim sistemi disklerinin şifrelenmesi desteklenir.

XFS veri disklerinin şifrelenmesi yalnızca EncryptFormatAll parametresi kullanıldığında desteklenir. Bu işlem, birimi yeniden biçimlendirir, daha önce orada tüm verileri siliyor. Daha fazla bilgi için bkz. [Encryptformatall ölçütleri](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Şifrelenmiş bir VM 'yi yedeklebilirim ve geri yükleyebilir miyim? 

Azure Backup, şifrelenmiş VM 'nin aynı abonelik ve bölge içinde yedeklenmesi ve geri yüklenmesi için bir mekanizma sağlar.  Yönergeler için lütfen [Azure Backup ile şifrelenmiş sanal makineleri yedekleme ve geri yükleme](../../backup/backup-azure-vms-encryption.md)bölümüne bakın.  Şifrelenmiş bir sanal makinenin farklı bir bölgeye geri yüklenmesi Şu anda desteklenmiyor.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Soru sormak veya geri bildirimde bulunmak için nereye gidebilirim?

[Azure disk şifrelemesi Için Microsoft Q&soru sayfasında](/answers/topics/azure-disk-encryption.html)sorular sorabilir veya geribildirim sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Azure disk şifrelemesi ile ilgili en sık sorulan sorular hakkında daha fazla bilgi edindiniz. Bu hizmet hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure Disk Şifrelemesine Genel Bakış](disk-encryption-overview.md)
- [Azure Güvenlik Merkezi 'nde disk şifrelemeyi uygulama](../../security-center/asset-inventory.md)
- [Azure bekleyen verileri şifreleme](../../security/fundamentals/encryption-atrest.md)