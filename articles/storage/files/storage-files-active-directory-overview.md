---
title: Azure dosyaları için SMB üzerinden Azure Active Directory kimlik doğrulamasına genel bakış-Azure depolama
description: Azure dosyaları, Azure Active Directory (Azure AD) etki alanı Hizmetleri aracılığıyla SMB (sunucu Ileti bloğu) üzerinden kimlik tabanlı kimlik doğrulamasını destekler. Etki alanına katılmış Windows sanal makineleriniz (VM), Azure AD kimlik bilgilerini kullanarak Azure dosya paylaşımlarına erişebilir.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 08/07/2019
ms.author: rogarana
ms.openlocfilehash: 6cdee8f1ad59962822e9e0394547c395c13e4bd8
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611783"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-azure-ad-ds-authentication-support-for-smb-access"></a>SMB erişimi için Azure dosyalarına Azure Active Directory etki alanı hizmeti (Azure AD DS) kimlik doğrulama desteği 'ne genel bakış
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure dosyaları için Azure AD DS kimlik doğrulamasını etkinleştirme hakkında bilgi edinmek için bkz. [Azure dosyaları IÇIN SMB üzerinden Azure Active Directory etki alanı hizmeti kimlik doğrulamasını etkinleştirme](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Sözlük 
Azure dosyaları için SMB üzerinden Azure AD etki alanı hizmet kimlik doğrulamasıyla ilgili bazı önemli koşulları anlamak yararlı olabilir:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD), Microsoft 'un çok kiracılı bulut tabanlı dizin ve kimlik yönetimi hizmetidir. Azure AD temel Dizin Hizmetleri, uygulama erişim yönetimi ve kimlik korumasını tek bir çözümde birleştirir. Daha fazla bilgi için bkz. [Azure Active Directory nedir?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    Azure AD Domain Services etki alanına katılması, Grup ilkeleri, LDAP ve Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar. Bu hizmetler Windows Server Active Directory ile tamamen uyumludur. Daha fazla bilgi için bkz. [Azure Active Directory (ad) etki alanı Hizmetleri](../../active-directory-domain-services/overview.md).

-   **Azure rol tabanlı Access Control (RBAC)**  
    Azure Rol Tabanlı Erişim Denetimi (RBAC), Azure için ayrıntılı erişim yönetimi sağlar. RBAC kullanarak, kullanıcılara işlerini gerçekleştirmek için gereken en az izni vererek kaynaklara erişimi yönetebilirsiniz. RBAC hakkında daha fazla bilgi için bkz. [Azure 'da rol tabanlı erişim denetimi (RBAC) nedir?](../../role-based-access-control/overview.md)

-   **Kerberos kimlik doğrulaması**

    Kerberos, bir kullanıcının veya ana bilgisayarın kimliğini doğrulamak için kullanılan bir kimlik doğrulama protokolüdür. Kerberos hakkında daha fazla bilgi için bkz. [Kerberos kimlik doğrulamasına genel bakış](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Sunucu Ileti bloğu (SMB) protokolü**  
    SMB, sektör standardı bir ağ dosya paylaşım protokolüdür. SMB, ortak Internet dosya sistemi veya CIFS olarak da bilinir. SMB hakkında daha fazla bilgi için bkz. [MICROSOFT SMB protokolü ve CIFS protokolüne genel bakış](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Azure AD etki alanı hizmeti kimlik doğrulamasının avantajları
Azure dosyaları için Azure AD etki alanı hizmeti kimlik doğrulaması, paylaşılan anahtar kimlik doğrulamasını kullanarak çeşitli avantajlar sunar:

-   **Azure AD ve Azure AD etki alanı hizmeti ile geleneksel kimlik tabanlı dosya paylaşma erişim deneyimini buluta genişletin**  
    Uygulamanızı buluta "kaldırma ve kaydırma" planlıyorsanız, geleneksel dosya sunucularını Azure dosyaları ile değiştirerek, uygulamanızın dosya verilerine erişmek için Azure AD kimlik bilgileriyle kimlik doğrulaması yapmasını isteyebilirsiniz. Azure dosyaları, Azure AD DS etki alanına katılmış Windows VM 'lerinden SMB üzerinden Azure dosyalarına erişmek için Azure AD kimlik bilgilerinin kullanılmasını destekler. Ayrıca, Kullanıcı adlarını, parolaları ve diğer grup atamalarını korumak için tüm şirket içi Active Directory nesnelerinizi Azure AD 'ye eşitlemeyi tercih edebilirsiniz.

-   **Azure dosya paylaşımlarında ayrıntılı erişim denetimi uygulayın**  
    Paylaşıma, dizine veya dosya düzeyinde belirli bir kimliğe izin verebilirsiniz. Örneğin, proje işbirliği için tek bir Azure dosya paylaşımının kullanıldığı birkaç ekibin olduğunu varsayalım. Tüm takımlara hassas olmayan dizinlere erişim izni verebilirsiniz, ancak gizli finansal verileri içeren dizinlere erişimi yalnızca finans ekibinize kısıtlar. 

-   **ACL 'Leri verilerle birlikte yedekleyin**  
    Azure dosyalarını, mevcut şirket içi dosya paylaşımlarınızı yedeklemek için kullanabilirsiniz. Azure dosyaları, Azure dosyalarına SMB üzerinden bir dosya paylaşımının yedeklendiği zaman, ACL 'larınızı verilerle birlikte korur.

## <a name="how-it-works"></a>Nasıl çalışır?
Azure dosyaları, etki alanına katılmış VM 'lerden Azure AD kimlik bilgileriyle Kerberos kimlik doğrulamasını desteklemek için Azure AD Domain Services kullanır. Azure AD 'yi Azure dosyaları ile kullanabilmeniz için öncelikle Azure AD Domain Services etkinleştirmeniz ve dosya verilerine erişmeyi planladığınız VM 'lerden etki alanına katılmanız gerekir. Etki alanına katılmış VM 'niz Azure AD Domain Services aynı sanal ağda (VNET) bulunmalıdır. 

Bir VM üzerinde çalışan bir uygulamayla ilişkili bir kimlik, Azure dosyalarındaki verilere erişmeye çalıştığında, kimlik doğrulaması için Azure AD Domain Services gönderilen istek gönderilir. Kimlik doğrulaması başarılı olursa Azure AD Domain Services, Kerberos belirteci döndürür. Uygulama, Kerberos belirtecini içeren bir istek gönderir ve Azure dosyaları, isteği yetkilendirmek için bu belirteci kullanır. Azure dosyaları yalnızca belirteci alır ve Azure AD kimlik bilgilerini kalıcı hale almaz.

![SMB üzerinden Azure AD kimlik doğrulaması diyagramını gösteren ekran görüntüsü](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>SMB erişimi için Azure AD etki alanı hizmeti kimlik doğrulamasını etkinleştirme
24 Eylül 2018 ' den sonra oluşturulan yeni ve mevcut depolama hesaplarınızdaki Azure dosyaları için Azure AD etki alanı hizmet kimlik doğrulamasını etkinleştirebilirsiniz. 

Bu özelliği etkinleştirmeden önce, depolama hesabınızın ilişkilendirildiği birincil Azure AD kiracısı için Azure AD Domain Services dağıtıldığını doğrulayın. Azure AD Domain Services henüz ayarlanmamışsa, [Azure Portal kullanarak Azure Active Directory Domain Services etkinleştir](../../active-directory-domain-services/tutorial-create-instance.md)bölümünde sunulan adım adım yönergeleri izleyin.

Azure AD Domain Services dağıtımı genellikle 10 ila 15 dakika sürer. Azure AD Domain Services dağıtıldıktan sonra, Azure dosyaları için SMB üzerinden Azure AD kimlik doğrulamasını etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [Azure dosyaları IÇIN SMB üzerinden Azure Active Directory etki alanı hizmeti kimlik doğrulamasını etkinleştirme](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Azure dosyaları için paylaşma düzeyi izinleri yapılandırma
Azure AD etki alanı hizmeti kimlik doğrulaması etkinleştirildikten sonra, Azure AD kimlikleri için özel RBAC rolleri yapılandırabilir ve depolama hesabındaki herhangi bir dosya paylaşımına erişim hakları atayabilirsiniz.

Etki alanına katılmış bir VM üzerinde çalışan bir uygulama bir Azure dosya paylaşımının bağlamaya veya bir dizin ya da dosyaya erişmeye çalıştığında, doğru bir paylaşımın düzeyi izinlerinin ve NTFS izinlerinin olduğundan emin olmak için uygulamanın Azure AD kimlik bilgileri doğrulanır. Paylaşma düzeyi izinlerini yapılandırma hakkında daha fazla bilgi için bkz. [SMB üzerinden Azure Active Directory etki alanı hizmeti kimlik doğrulamasını etkinleştirme](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Azure dosyaları için dizin veya dosya düzeyi izinlerini yapılandırma 
Azure dosyaları, kök dizine dahil olmak üzere dizin ve dosya düzeyinde standart NTFS dosya izinlerini zorlar. Dizin veya dosya düzeyi izinlerinin yapılandırılması yalnızca SMB üzerinden desteklenir. Hedef dosya paylaşımının VM 'nizden bağlama ve Windows Dosya Gezgini, Windows [ıccacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) veya [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) komutunu kullanarak izinleri yapılandırma. 

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Süper Kullanıcı izinleri için depolama hesabı anahtarını kullan 
Depolama hesabı anahtarını taşıyan bir Kullanıcı, Azure dosyalarına Süper Kullanıcı izinleriyle erişebilir. Süper Kullanıcı izinleri, paylaşılan düzeyinde yapılandırılmış tüm erişim denetimi kısıtlamalarını RBAC ve Azure AD tarafından zorlanan şekilde iletir. Azure dosya paylaşımının bağlanması için süper kullanıcı izinleri gerekir. 

> [!IMPORTANT]
> En iyi güvenlik yöntemlerinin bir parçası olarak, depolama hesabı anahtarlarınızı paylaşmayı önleyin ve mümkün olduğunda Azure AD izinlerinden yararlanın.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Azure dosya paylaşımlarına veri aktarma için dizin ve dosya ACL 'Lerini koruma
Azure dosyaları artık Azure dosya paylaşımlarına veri kopyaladığınızda dizin veya dosya ACL 'Lerinin kullanılmasını destekler. Bir dizin veya dosyadaki ACL 'Leri Azure dosyalarına kopyalayabilirsiniz. Örneğin, hem verileri hem de [](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) ACL 'leri bir `/copy:s` Azure dosya paylaşımında kopyalamak için Robocopy 'yi bayrağıyla birlikte kullanabilirsiniz. ACL koruması varsayılan olarak açık ve depolama hesabınızda Azure AD etki alanı hizmeti kimlik doğrulama özelliğini açıkça etkinleştirmeniz gerekmez. 

## <a name="pricing"></a>Fiyatlandırma
Depolama hesabınızda SMB üzerinden Azure AD kimlik doğrulamasını etkinleştirmek için başka bir hizmet ücreti yoktur. Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure dosyaları fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/files/) ve [Azure AD Domain Services fiyatlandırma](https://azure.microsoft.com/pricing/details/active-directory-ds/) sayfaları.

## <a name="next-steps"></a>Sonraki adımlar
SMB üzerinden Azure dosyaları ve Azure AD kimlik doğrulaması hakkında daha fazla bilgi için şu kaynaklara bakın:

- [Azure dosyaları 'na giriş](storage-files-introduction.md)
- [Azure dosyaları için SMB üzerinden Azure Active Directory kimlik doğrulamasını etkinleştirme](storage-files-active-directory-enable.md)
- [SSS](storage-files-faq.md)
