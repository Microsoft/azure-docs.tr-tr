---
title: SSS-Linux sanal makineleri için Azure disk şifrelemesi
description: Bu makalede, Linux IaaS VM 'Leri için Microsoft Azure disk şifrelemesi hakkında sık sorulan sorulara yanıtlar verilmektedir.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 77b092920bff75c806ec78a933d23df546bf636a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036363"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>IaaS VM 'Leri için Azure disk şifrelemesi hakkında SSS

Bu makalede, Linux VM 'Leri için Azure disk şifrelemesi hakkında sık sorulan soruların (SSS) yanıtları sağlanmaktadır. Bu hizmet hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi 'ne genel bakış](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Azure Disk şifrelemesi genel kullanıma (GA) nerede?

Linux sanal makineleri için Azure disk şifrelemesi, tüm Azure genel bölgelerinde genel kullanıma açıktır.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Hangi kullanıcı deneyimleri Azure Disk şifrelemesi ile kullanılabilir?

Azure Resource Manager şablonları, Azure PowerShell ve Azure CLI, Azure Disk şifrelemesi GA destekler. Farklı kullanıcı deneyimleri esneklik sağlar. Sanal makinelerinize disk şifrelemeyi etkinleştirmek için üç farklı seçeneğiniz vardır. Kullanıcı deneyimi ve Azure disk şifrelemesi 'nde sunulan adım adım yönergeler hakkında daha fazla bilgi için bkz. [Linux Için Azure disk şifrelemesi senaryoları](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure Disk şifrelemesi nin ücreti ne kadardır?

Azure disk şifrelemesi ile VM disklerini şifrelemek için ücret alınmaz, ancak Azure Key Vault kullanımıyla ilişkili ücretler vardır. Azure Key Vault maliyetleri hakkında daha fazla bilgi için bkz. [anahtar kasası fiyatlandırma](https://azure.microsoft.com/pricing/details/key-vault/) sayfası.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Azure Disk şifrelemesi kullanılarak nasıl başlayabilirsiniz?

Başlamak için okuma [Azure Disk Şifrelemesi'ne genel bakış](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Azure disk şifrelemesini hangi VM boyutları ve işletim sistemleri destekler?

[Azure disk şifrelemesi genel bakış](disk-encryption-overview.md) makalesi, Azure disk şifrelemesini destekleyen [VM boyutlarını](disk-encryption-overview.md#supported-vm-sizes) ve [VM işletim sistemlerini](disk-encryption-overview.md#supported-operating-systems) listeler.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Ben Azure Disk şifrelemesi ile hem önyükleme hem de veri birimleri şifreleyebilir mi?

Evet, hem önyükleme hem de veri birimlerini şifreleyebilir veya önce işletim sistemi birimini şifrelemek zorunda kalmadan veri birimini şifreleyebilirsiniz. 

İşletim sistemi birimini şifreledikten sonra, işletim sistemi biriminde şifrelemeyi devre dışı bırakmak desteklenmez. Ölçek kümesindeki Linux VM 'Ler için yalnızca veri hacmi şifrelenebilir.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Takılmamış bir birimi Azure disk şifrelemesi ile şifreleyebilir miyim?

Hayır, Azure disk şifrelemesi yalnızca bağlı birimleri şifreler.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Gizli dizileri veya şifreleme anahtarlarını Nasıl yaparım? mı döndürün?

Gizli dizileri döndürmek için, disk şifrelemeyi etkinleştirmek üzere yalnızca ilk kullandığınız komutu çağırın, farklı bir Key Vault belirtin. Anahtar şifreleme anahtarını döndürmek için, yeni anahtar şifrelemesini belirterek disk şifrelemeyi etkinleştirmek üzere başlangıçta kullandığınız komutu çağırın. 

>[!WARNING]
> - Bu VM 'yi şifrelemek için Azure AD kimlik bilgilerini belirterek daha önce Azure [ad uygulaması Ile Azure disk şifrelemesi](disk-encryption-linux-aad.md) KULLANDıYSANıZ, VM 'nizi şifrelemek için bu seçeneği kullanmaya devam etmeniz gerekir. Bu desteklenen bir senaryo olmadığından, bu şifrelenmiş VM 'de Azure disk şifrelemesi 'ni kullanamazsınız. Bu, bu şifrelenmiş VM için AAD uygulamasından uzağa geçiş henüz desteklenmiyor.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>İlk olarak bir tane kullanmadıysa, anahtar şifreleme anahtarı ekleme veya kaldırma Nasıl yaparım??

Anahtar şifreleme anahtarı eklemek için, anahtar şifreleme anahtar parametresini geçirerek Etkinleştir komutunu yeniden çağırın. Anahtar şifreleme anahtarını kaldırmak için anahtar şifreleme anahtarı parametresi olmadan Etkinleştir komutunu yeniden çağırın.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure Disk şifrelemesi kendi anahtarını getir (BYOK) izin veriyor mu?

Evet, kendi anahtar şifreleme anahtarlarınızı sağlayabilirsiniz. Bu anahtarlar Azure anahtar Kasası'nda, Azure Disk şifrelemesi için anahtar deposu olduğu korunur. Anahtar şifreleme anahtarları destek senaryoları hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Azure tarafından oluşturulan anahtar şifreleme anahtarı kullanabilir miyim?

Evet, Azure Key Vault, Azure disk şifrelemesi kullanmak için bir anahtar şifreleme anahtarı oluşturmak için kullanabilirsiniz. Bu anahtarlar Azure anahtar Kasası'nda, Azure Disk şifrelemesi için anahtar deposu olduğu korunur. Anahtar şifreleme anahtarı hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Şifreleme anahtarlarını korumak için bir şirket içi anahtar yönetimi hizmeti veya HSM kullanabilir miyim?

Azure Disk şifreleme ile şifreleme anahtarlarını korumak için şirket içi anahtar yönetimi hizmeti veya HSM kullanamazsınız. Azure Key Vault hizmeti, şifreleme anahtarlarını korumak için yalnızca kullanabilirsiniz. Anahtar şifreleme anahtarı destek senaryoları hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Azure Disk şifrelemesini yapılandırmak için Önkoşullar nelerdir?

Azure Disk şifrelemesi önkoşulları vardır. Yeni bir Anahtar Kasası oluşturmak için [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md) makalesine bakın veya şifrelemeyi etkinleştirmek için disk şifreleme erişimi için mevcut bir Anahtar Kasası kurun ve gizli dizileri ve anahtarları koruyun. Anahtar şifreleme anahtarı destek senaryoları hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Bir Azure AD uygulamasını (önceki sürüm) ile Azure Disk şifrelemesini yapılandırmak için Önkoşullar nelerdir?

Azure Disk şifrelemesi önkoşulları vardır. Azure Active Directory bir uygulama oluşturmak, yeni bir Anahtar Kasası oluşturmak veya şifrelemeyi etkinleştirmek için disk şifreleme erişimi için mevcut bir Anahtar Kasası ayarlamak ve gizli dizileri ve anahtarları korumak için Azure AD içeriğiyle Azure [disk şifrelemesi](disk-encryption-linux-aad.md) ' ne bakın. Anahtar şifreleme anahtarı destek senaryoları hakkında daha fazla bilgi için bkz. Azure [ad Ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure Disk şifrelemesi, yine de desteklenen bir Azure AD uygulamasını (önceki sürüm) kullanıyor mu?
Evet. Disk şifrelemesi kullanarak bir Azure AD uygulamasını hala desteklenmektedir. Ancak, yeni VM'ler şifrelerken, şifreleme ile bir Azure AD uygulama yerine yeni bir yöntem kullanmak önerilir. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Şifreleme olmadan bir Azure AD uygulaması için bir Azure AD uygulama ile şifrelenmiş VM'ler geçişini sağlayabilir miyim?
  Şu anda, şifreleme olmadan bir Azure AD uygulaması için bir Azure AD uygulama ile şifrelenmiş olan makineler için bir doğrudan geçiş yolu yoktur. Ayrıca, bir AD uygulamasını şifrelemeyle şifreleme olmadan bir Azure AD uygulaması'ndan doğrudan bir yolu yoktur. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure PowerShell'in hangi sürümünü Azure Disk şifrelemesi destekliyor mu?

Azure Disk şifrelemesini yapılandırmak için en son Azure PowerShell SDK'sı sürümünü kullanın. En son sürümünü indirin [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk şifrelemesi *değil* Azure SDK sürüm 1.1.0 tarafından desteklenir.

> [!NOTE]
> Linux Azure disk şifrelemesi önizleme uzantısı "Microsoft. OSTCExtension. AzureDiskEncryptionForLinux" kullanım dışıdır. Bu uzantı Azure disk şifrelemesi önizleme sürümü için yayımlandı. Test veya üretim dağıtımınızdaki uzantının önizleme sürümünü kullanmamalısınız.

> Linux IaaS VM 'niz üzerinde şifrelemeyi etkinleştirmek için Linux VM için Azure disk şifrelemesi uzantısını dağıtmanız gereken Azure Resource Manager (ARM) gibi dağıtım senaryolarında, Azure disk şifrelemesi üretimi desteklenen uzantısını kullanmanız gerekir " Microsoft. Azure. Security. AzureDiskEncryptionForLinux ".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Azure Disk şifrelemesi my özel bir Linux görüntüsü üzerinde uygulayabilir miyim?

Azure Disk şifrelemesi, özel bir Linux görüntüsü üzerinde uygulayamazsınız. Galeri Linux görüntüleri yalnızca daha önce bahsedilen desteklenen dağıtımları için desteklenir. Özel Linux görüntüleri şu anda desteklenmemektedir.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Bir Linux Red Hat yum güncelleştirme kullanan VM için güncelleştirmeleri uygulayabilir miyim?

Evet, Red Hat Linux sanal makinesi üzerinde bir yum güncelleştirmesi yapabilirsiniz.  Daha fazla bilgi için bkz. [bir güvenlik duvarının arkasındaki Linux paket yönetimi](disk-encryption-troubleshooting.md#linux-package-management-behind-a-firewall).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Linux için önerilen Azure disk şifrelemesi iş akışı nedir?

Aşağıdaki iş akışı, Linux üzerinde en iyi sonuçlar için önerilir:
* Gerekli işletim sistemi distro sürümü ve için karşılık gelen değiştirilmemiş stok galeri görüntüsü başlayın
* Şifrelenir, tüm bağlı sürücülerin yedekleyin.  Bir hata varsa, örneğin geri en fazla kurtarma için şifreleme tamamlanmadan önce VM yeniden başlatılırsa, böylece.
* Şifreleme (birkaç saat veya gün bile VM özelliklerine ve bağlı veri diskleri boyutuna bağlı olarak sürebilir)
* Özelleştirebilir ve yazılım görüntüyü gerektiği gibi ekleyin.

Bu iş akışını mümkün değilse, bağlı [depolama hizmeti şifrelemesi](../../storage/common/storage-service-encryption.md) (SSE) platform depolama hesabı katmanı dm-crypt kullanan tam disk şifrelemesi için bir alternatif olabilir.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Disk "Bek birimi" veya "/ mnt/azure_bek_disk" nedir?

"Bek Volume", şifrelenmiş Azure VM 'Ler için şifreleme anahtarlarını güvenli bir şekilde depolayan yerel bir veri birimidir.
> [!NOTE]
> Silmeyin ve bu diskteki tüm içeriği düzenleyebilir. Iaas VM üzerindeki tüm şifreleme işlemleri için şifreleme anahtar bulunması gerekli olmadığından, diski çıkarın değil.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure Disk şifrelemesi hangi şifreleme yöntemini kullanıyor?

Azure disk şifrelemesi, 256 bitlik bir birim ana anahtarıyla AES-XTS-plain64 şifre çözme varsayılanını kullanır.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>EncryptFormatAll kullanın ve tüm birim türlerini belirtmek, bu verileri zaten şifrelenmiş veri sürücülerinde silecek?
Hayır, veri zaten Azure Disk şifrelemesi kullanılarak şifrelenmiş veri sürücülerden silinmesi gerekmez. Benzer şekilde nasıl EncryptFormatAll işletim sistemi sürücüsünü yeniden şifrele siz, bunu zaten şifrelenmiş veri sürücüsü yeniden şifrele olmaz. Daha fazla bilgi için [EncryptFormatAll ölçütleri](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>XFS FileSystem destekleniyor mu?
XFS birimleri yalnızca EncryptFormatAll ile veri diski şifrelemesi için desteklenir. Bu işlem, birimi yeniden biçimlendirir, daha önce orada tüm verileri siliyor. Daha fazla bilgi için [EncryptFormatAll ölçütleri](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Şifrelenmiş bir VM 'yi yedeklebilirim ve geri yükleyebilir miyim? 

Azure Backup, şifrelenmiş VM 'nin aynı abonelik ve bölge içinde yedeklenmesi ve geri yüklenmesi için bir mekanizma sağlar.  Yönergeler için lütfen [Azure Backup ile şifrelenmiş sanal makineleri yedekleme ve geri yükleme](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)bölümüne bakın.  Şifrelenmiş bir sanal makinenin farklı bir bölgeye geri yüklenmesi Şu anda desteklenmiyor.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Soru sormak veya geri bildirim sağlamak için nereye miyim?

Soru sormak veya geri bildirim sağlayın [Azure Disk şifrelemesi Forumu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Azure Disk şifrelemesi için en sık kullanılan sorular hakkında daha fazla öğrendiniz. Bu hizmet hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure Disk Şifrelemesine Genel Bakış](disk-encryption-overview.md)
- [Güvenlik Merkezi'nde Azure disk şifrelemesi Uygula](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure veri bekleme sırasında şifreleme](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
