---
title: Azure dosya paylaşımlarına genel bakış-şirket içi AD DS kimlik doğrulaması
description: Azure dosya paylaşımlarına Active Directory Domain Services (AD DS) kimlik doğrulaması hakkında bilgi edinin. Bu makale, destek senaryolarını, kullanılabilirliği ve AD DS ile Azure Active Directory arasında izinlerin nasıl çalıştığını açıklar.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 03/15/2021
ms.author: rogarana
ms.openlocfilehash: 26932d05cd3d2ef7704b48463c895e05524d87f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103472146"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Azure dosya paylaşımları için SMB üzerinden genel bakış-şirket içi Active Directory Domain Services kimlik doğrulaması

[Azure dosyaları](storage-files-introduction.md)   iki tür etki alanı hizmeti arasında sunucu Ileti bloğu (SMB) üzerinden kimlik tabanlı kimlik doğrulamasını destekler: şirket içi Active Directory Domain Services (AD DS) ve Azure Active Directory Domain Services (Azure AD DS). Kimlik doğrulaması için doğru etki alanı hizmetini seçmek üzere [nasıl çalıştığını](./storage-files-active-directory-overview.md#how-it-works) incelemenizi önemle öneririz. Kurulum, seçtiğiniz etki alanı hizmetine göre farklılık açmış. Bu makale dizisi, Azure dosya paylaşımlarında kimlik doğrulaması için şirket içi AD DS etkinleştirmeye ve yapılandırmaya odaklanmaktadır.

Azure dosya paylaşımlarından yeni bir deneyiminiz varsa, aşağıdaki makale serisini okumadan önce [planlama kılavuzumuzu](storage-files-planning.md) okumanızı öneririz.

## <a name="supported-scenarios-and-restrictions"></a>Desteklenen senaryolar ve kısıtlamalar

- Azure dosyaları için kullanılan AD DS kimlikleri şirket içi AD DS kimlik doğrulaması Azure AD ile eşitlenmelidir. Parola karması eşitleme isteğe bağlıdır. 
- Azure Dosya Eşitleme tarafından yönetilen Azure dosya paylaşımlarını destekler.
- , RC4-HMAC ve [AES 256 şifrelemesi](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption)ile ad ile Kerberos kimlik doğrulamasını destekler. AES 256 şifreleme desteği şu anda ada <= 15 karakter uzunluğunda depolama hesaplarıyla sınırlıdır. AES 128 Kerberos şifrelemesi henüz desteklenmiyor.
- Çoklu oturum açma deneyimini destekler.
- Yalnızca Windows 7 veya Windows Server 2008 R2 'den daha yeni işletim sistemi sürümlerinde çalıştırılan istemcilerde desteklenir.
- Yalnızca depolama hesabının kaydedildiği AD ormanına göre desteklenir. Azure dosya paylaşımlarına yalnızca AD DS kimlik bilgileriyle, varsayılan olarak tek bir ormandaki erişebilirsiniz. Azure dosya paylaşımınıza farklı bir ormandan erişmeniz gerekiyorsa, uygun orman güveninin yapılandırıldığından emin olun, Ayrıntılar için [SSS](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) bölümüne bakın.
- AD DS ' de oluşturulan bilgisayar hesaplarına karşı kimlik doğrulamasını desteklemez.
- Ağ dosya sistemi (NFS) dosya paylaşımlarında kimlik doğrulamasını desteklemez.

SMB üzerinden Azure dosya paylaşımları için AD DS etkinleştirdiğinizde, AD DS katılmış makineleriniz, mevcut AD DS kimlik bilgilerinizi kullanarak Azure dosya paylaşımlarını bağlayabilir. Bu özellik, şirket içi makinelerde barındırılan veya Azure 'da barındırılan bir AD DS ortamıyla etkinleştirilebilir.

## <a name="videos"></a>Videolar

Bazı yaygın kullanım örnekleri için Azure dosyaları AD kimlik doğrulamasını ayarlamanıza yardımcı olması için, aşağıdaki senaryolar için adım adım yönergeler içeren iki video yayımladık:

| Şirket içi dosya sunucularını Azure dosyaları ile değiştirme (dosyalar ve AD kimlik doğrulaması için özel bağlantı kurulumu dahil) | Azure dosyalarını Windows sanal masaüstü için profil kapsayıcısı olarak kullanma (AD kimlik doğrulaması ve FsLogix yapılandırmasında kurulum dahil)  |
|-|-|
| [![Şirket içi dosya sunucularını değiştirme videosunu ekran görüntüsü-oynatmak için tıklayın.](./media/storage-files-identity-auth-active-directory-enable/replace-on-prem-server-thumbnail.png)](https://www.youtube.com/watch?v=jd49W33DxkQ) | [![Profil kapsayıcısı olarak Azure dosyalarını kullanmaya yönelik ekran görüntüsü-oynatmak için tıklayın.](./media/storage-files-identity-auth-active-directory-enable/files-ad-ds-fslogix-thumbnail.png)](https://www.youtube.com/watch?v=9S5A1IJqfOQ) |


## <a name="prerequisites"></a>Önkoşullar 

Azure dosya paylaşımları için AD DS kimlik doğrulamasını etkinleştirmeden önce, aşağıdaki önkoşulları tamamladığınızdan emin olun: 

- [AD DS ortamınızı](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) seçin veya oluşturun ve Azure AD Connect [Azure AD ile eşitleyin](../../active-directory/hybrid/how-to-connect-install-roadmap.md) . 

    Özelliği yeni veya mevcut bir şirket içi AD DS ortamında etkinleştirebilirsiniz. Erişim için kullanılan kimlikler Azure AD ile eşitlenmelidir. Azure AD kiracısı ve eriştiğiniz dosya paylaşımının aynı abonelikle ilişkilendirilmesi gerekir.

- Etki alanı-şirket içi bir makineye veya bir Azure VM 'ye şirket içi AD DS ekleyin. Etki alanına ekleme hakkında daha fazla bilgi için, bkz. [bir bilgisayarı etki alanına ekleme](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain).

    Makinenizin bir AD DS etki alanına katılmış olmaması durumunda, makinenizde AD etki alanı denetleyicisi varsa, kimlik doğrulama için AD kimlik bilgilerini kullanmaya devam edebilirsiniz.

- Bir Azure depolama hesabı seçin veya oluşturun.  En iyi performans için, depolama hesabını paylaşıma erişmeyi planladığınız istemciyle aynı bölgeye dağıtmanızı öneririz. Daha sonra, [Azure dosya paylaşımından](storage-how-to-use-files-windows.md) depolama hesabı anahtarınızı bağlayın. Depolama hesabı anahtarıyla bağlama, bağlantıyı doğrular.

    Dosya paylaşımlarınızı içeren depolama hesabının Azure AD DS kimlik doğrulaması için önceden yapılandırılmadığından emin olun. Azure dosyaları Azure AD DS kimlik doğrulaması, depolama hesabında etkinleştirilmişse, şirket içi AD DS kullanacak şekilde değiştirilmeden önce devre dışı bırakılmalıdır. Bu, Azure AD DS ortamında yapılandırılan mevcut ACL 'Lerin uygun izin zorlaması için yeniden yapılandırılması gerektiğini gösterir.


    Azure dosyalarına bağlanırken sorunlarla karşılaşırsanız, [Windows 'Da Azure dosyaları bağlama hataları için yayımladığımız sorun giderme aracına](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)bakın. Ayrıca, 445 numaralı bağlantı noktası engellendiğinde senaryolar etrafında çalışmak için [rehberlik](./storage-files-faq.md#on-premises-access) sunuyoruz. 


- Azure dosya paylaşımlarınız için AD DS kimlik doğrulamasını etkinleştirmeden ve yapılandırmadan önce ilgili ağ yapılandırmalarını yapın. Daha fazla bilgi için bkz. [Azure dosyaları ağ iletişimi konuları](storage-files-networking-overview.md) .

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik

AD DS ile Azure dosyaları kimlik doğrulaması, [tüm Azure genel, Çin ve gov bölgelerinde](https://azure.microsoft.com/global-infrastructure/locations/)kullanılabilir.

## <a name="overview"></a>Genel Bakış

Dosya paylaşımınızda herhangi bir ağ yapılandırmasını etkinleştirmeyi planlıyorsanız, [ağ dikkat edilecek noktalar](./storage-files-networking-overview.md) makalesini okuyup AD DS kimlik doğrulamasını etkinleştirmeden önce ilgili yapılandırmayı tamamlamanızı öneririz.

Azure dosya paylaşımlarınız için AD DS kimlik doğrulamasını etkinleştirmek, Azure dosya paylaşımlarınız üzerinde şirket içi AD DS kimlik bilgilerinizle kimlik doğrulaması yapmanıza olanak sağlar. Ayrıca, ayrıntılı erişim denetimine izin vermek için izinlerinizi daha iyi yönetmenizi sağlar. Bunun yapılması, şirket içi AD DS kimlik ve AD Connect ile Azure AD arasında eşitleme yapılmasını gerektirir. Şirket içi AD DS kimlik bilgileriyle dosya/paylaşma düzeyinde erişimi yönetirken Azure AD 'ye eşitlenen kimliklerle paylaşma düzeyi erişimini kontrol edersiniz.

Ardından, AD DS kimlik doğrulaması için Azure dosyalarını ayarlamak üzere aşağıdaki adımları izleyin: 

1. [Birinci kısım: depolama hesabınızda AD DS kimlik doğrulamasını etkinleştirme](storage-files-identity-ad-ds-enable.md)

1. [İkinci Bölüm: hedef AD kimliğiyle eşitlenmiş olan Azure AD Identity (bir Kullanıcı, Grup veya hizmet sorumlusu) için erişim izinleri atama](storage-files-identity-ad-ds-assign-permissions.md)

1. [Üçüncü kısım: dizinler ve dosyalar için SMB üzerinden Windows ACL 'Lerini yapılandırma](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [4. kısım: Azure dosya paylaşımından AD DS katılmış bir VM 'ye bağlama](storage-files-identity-ad-ds-mount-file-share.md)

1. [AD DS 'de depolama hesabı Kimliğinizin parolasını güncelleştirin](storage-files-identity-ad-ds-update-password.md)

Aşağıdaki diyagramda Azure dosya paylaşımları için SMB üzerinden Azure AD kimlik doğrulamasını etkinleştirmeye yönelik uçtan uca iş akışı gösterilmektedir. 

![Dosyalar AD iş akışı diyagramı](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Azure dosya paylaşımlarına erişmek için kullanılan kimlikler, Azure [rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) modeli aracılığıyla paylaşım düzeyi dosya izinlerini zorlamak IÇIN Azure AD ile eşitlenmelidir. Mevcut dosya sunucularından taşınan dosyalarda/dizinlerde bulunan [Windows stili DACL 'ler](/previous-versions/technet-magazine/cc161041(v=msdn.10)) korunur ve zorlanır. Bu, kurumsal AD DS ortamınızla sorunsuz tümleştirme sunar. Şirket içi dosya sunucularını Azure dosya paylaşımlarıyla değiştirirken, mevcut kullanıcılar, kullanımdaki kimlik bilgileriyle hiçbir değişiklik yapmadan mevcut istemcilerinden Azure dosya paylaşımlarına tek bir oturum açma deneyimiyle erişebilirler.  

## <a name="next-steps"></a>Sonraki adımlar

Azure dosya paylaşımınızda şirket içi AD DS kimlik doğrulamasını etkinleştirmek için sonraki makaleye geçin:

[Birinci kısım: hesabınız için AD DS kimlik doğrulamasını etkinleştirin](storage-files-identity-ad-ds-enable.md)
