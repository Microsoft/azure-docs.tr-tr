---
title: Genel Bakış-Azure dosyaları kimlik tabanlı yetkilendirme
description: Azure dosyaları, SMB (sunucu Ileti bloğu) üzerinden Azure Active Directory Domain Services (AD DS) ve Active Directory arasında kimlik tabanlı kimlik doğrulamasını destekler. Etki alanına katılmış Windows sanal makineleriniz (VM), Azure AD kimlik bilgilerini kullanarak Azure dosya paylaşımlarına erişebilir.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 8887243f953a7bb000033a2e56b4655b93cd7ca8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519801"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>SMB erişimi için Azure dosyalarına yönelik kimlik tabanlı kimlik doğrulama seçeneklerine genel bakış
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure dosya paylaşımları için şirket içi Active Directory Domain Services kimlik doğrulamasını nasıl etkinleştireceğinizi öğrenmek için bkz. [Azure dosya paylaşımları IÇIN SMB üzerinden şirket içi Active Directory Domain Services kimlik doğrulamasını etkinleştirme](storage-files-identity-auth-active-directory-enable.md).

Azure dosya paylaşımları için Azure AD DS kimlik doğrulamasını etkinleştirme hakkında bilgi edinmek için bkz. [Azure dosyalarında Azure Active Directory Domain Services kimlik doğrulamasını etkinleştirme](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Sözlük 
Azure dosya paylaşımları için SMB üzerinden Azure AD etki alanı hizmet kimlik doğrulamasıyla ilgili bazı önemli koşulları anlamak yararlı olabilir:

-   **Kerberos kimlik doğrulaması**

    Kerberos, bir kullanıcının veya ana bilgisayarın kimliğini doğrulamak için kullanılan bir kimlik doğrulama protokolüdür. Kerberos hakkında daha fazla bilgi için bkz. [Kerberos kimlik doğrulamasına genel bakış](/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Sunucu Ileti bloğu (SMB) protokolü**

    SMB, sektör standardı bir ağ dosya paylaşım protokolüdür. SMB, ortak Internet dosya sistemi veya CIFS olarak da bilinir. SMB hakkında daha fazla bilgi için bkz. [MICROSOFT SMB protokolü ve CIFS protokolüne genel bakış](/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD), Microsoft 'un çok kiracılı bulut tabanlı dizin ve kimlik yönetimi hizmetidir. Azure AD temel Dizin Hizmetleri, uygulama erişim yönetimi ve kimlik korumasını tek bir çözümde birleştirir. Azure AD 'ye katılmış Windows sanal makineleri (VM 'Ler) Azure AD kimlik bilgilerinizle Azure dosya paylaşımlarına erişemez. Daha fazla bilgi için bkz. [Azure Active Directory nedir?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS, etki alanına katılması, Grup ilkeleri, LDAP ve Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar. Bu hizmetler Active Directory Domain Services ile tamamen uyumludur. Daha fazla bilgi için bkz. [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Şirket içi Active Directory Domain Services (AD DS)**

    Azure dosyaları ile şirket içi Active Directory Domain Services (AD DS) tümleştirmesi, ağ kullanıcıları ve yöneticiler tarafından kullanılabilir hale getirilmesi sırasında dizin verilerinin depolanması için yöntemler sağlar. Güvenlik, oturum açma kimlik doğrulaması ve dizindeki nesnelere erişim denetimi aracılığıyla AD DS tümleşiktir. Tek bir ağ oturumu açma ile yöneticiler, ağ genelinde dizin verilerini ve organizasyonu yönetebilir ve yetkili ağ kullanıcıları ağ üzerinde herhangi bir yerde kaynaklara erişebilir. AD DS genellikle şirket içi ortamlarda kuruluşlar tarafından benimsenmiştir ve erişim denetimi kimliği olarak AD DS kimlik bilgileri kullanılır. Daha fazla bilgi için bkz. [Active Directory Domain Services genel bakış](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Azure rol tabanlı erişim denetimi (Azure RBAC)**

    Azure rol tabanlı erişim denetimi (Azure RBAC), Azure için ayrıntılı erişim yönetimine izin vermez. Azure RBAC kullanarak, kullanıcılara işlerini gerçekleştirmek için gereken en az izni vererek kaynaklara erişimi yönetebilirsiniz. Azure RBAC hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Genel kullanım örnekleri

Azure dosyalarında Windows ACL 'Leri için kimlik tabanlı kimlik doğrulaması ve destek aşağıdaki kullanım durumları için en iyi yararlanılabilir:

### <a name="replace-on-premises-file-servers"></a>Şirket içi dosya sunucularını değiştirme

Dağınık şirket içi dosya sunucularının kullanım dışı bırakılması ve değiştirilmesi, her kuruluşun BT çalışırken yolculuğunda karşılaştığı yaygın bir sorundur. Şirket içi AD DS kimlik doğrulamasıyla Azure dosya paylaşımları, verileri Azure dosyalarına geçirebileceğiniz en iyi şekilde buraya uygundur. Tam geçiş, yüksek oranda kullanılabilirlik ve ölçeklenebilirlik avantajlarından yararlanarak istemci tarafı değişiklikleri de en aza indirmenizi sağlar. Son kullanıcılara sorunsuz bir geçiş deneyimi sağlar. bu sayede, mevcut etki alanına katılmış makineleri kullanarak verilerine aynı kimlik bilgileriyle erişmeye devam edebilirler.

### <a name="lift-and-shift-applications-to-azure"></a>Uygulamaları Azure 'a yükselt ve Kaydır

Uygulamaları buluta kaldırdığınızda ve kaydırdığınızda verileriniz için aynı kimlik doğrulama modelini tutmak istersiniz. Kimlik tabanlı erişim denetimi deneyimini Azure dosya paylaşımlarına genişlettiğimiz için, uygulamanızı modern kimlik doğrulama yöntemlerine değiştirme ve bulut benimseme sürecini ortadan kaldırır. Azure dosya paylaşımları, kimlik doğrulaması için Azure AD DS veya şirket içi AD DS tümleştirme seçeneği sunar. Planınız %100 bulut Native olarak kullanılıyorsa ve bulut altyapılarını yönetme çabalarını en aza indiriyorsa Azure AD DS tam olarak yönetilen bir etki alanı hizmeti olarak daha iyi bir şekilde uyum sağlar. AD DS özellikleri ile tam uyumlulukla karşılaşırsanız, VM 'lerde Self-hosting etki alanı denetleyicileri tarafından AD DS ortamınızı buluta genişletmeyi düşünmek isteyebilirsiniz. Her iki durumda da, iş gereksinimlerinize uygun etki alanı hizmetlerini seçme esnekliği sağlıyoruz.

### <a name="backup-and-disaster-recovery-dr"></a>Yedekleme ve olağanüstü durum kurtarma (DR)

Birincil dosya depolama alanınızı şirket içinde saklarken Azure dosya paylaşımları, iş devamlılığını artırmak için yedekleme veya DR için ideal bir depolama alanı olarak görev yapabilir. Azure dosya paylaşımlarını, verilerinizi mevcut dosya sunucularından yedeklemek için kullanabilirsiniz, ancak Windows DACL 'Lerini korurken. DR senaryolarında, yük devretme sırasında uygun erişim denetimi zorlamayı desteklemek için bir kimlik doğrulama seçeneği yapılandırabilirsiniz.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

Aşağıdaki tabloda Azure AD DS için desteklenen Azure dosya paylaşımları kimlik doğrulama senaryoları ve şirket içi AD DS özetlenmektedir. Azure dosyaları ile tümleştirme için, istemci ortamınız için benimsediğiniz etki alanı hizmetini seçmenizi öneririz. Şirket içinde veya Azure 'da cihazlarınızın etki alanına katıldığı AD DS zaten varsa, Azure dosya paylaşımları kimlik doğrulaması için AD DS özelliğinden faydalanmayı seçmeniz gerekir. Benzer şekilde, Azure AD DS zaten benimsediyseniz Azure dosya paylaşımlarında kimlik doğrulaması için bunu kullanmanız gerekir.


|Azure AD DS kimlik doğrulaması  | Şirket içi AD DS kimlik doğrulaması  |
|---------|---------|
|Azure AD DS katılmış Windows makineleri, SMB üzerinden Azure AD kimlik bilgileriyle Azure dosya paylaşımlarına erişebilir.     |Şirket içi AD DS katılmış veya Azure AD DS 'a katılmış Windows makineleri, SMB üzerinden Azure AD 'ye eşitleniyor olan şirket içi Active Directory kimlik bilgileriyle Azure dosya paylaşımlarına erişebilir. İstemciniz AD DS bir görüş satırı içermelidir.        |

### <a name="restrictions"></a>Kısıtlamalar

- Azure AD DS ve şirket içi AD DS kimlik doğrulaması, bilgisayar hesaplarında kimlik doğrulamasını desteklemez. Bunun yerine bir hizmet oturum açma hesabı kullanmayı düşünebilirsiniz.
- Azure AD 'ye katılmış cihazlarda veya Azure AD 'ye kayıtlı cihazlarda Azure AD DS kimlik doğrulaması ve şirket içi AD DS kimlik doğrulaması desteklenmez.
- Azure dosya paylaşımları yalnızca aşağıdaki etki alanı hizmetlerinden birine yönelik kimlik tabanlı kimlik doğrulamasını destekler [Azure Active Directory Domain Services (Azure AD DS)](#azure-ad-ds) ya da [Şirket içi Active Directory Domain Services (AD DS)](#ad-ds).
- Önizleme aşamasında olan ağ dosya sistemi (NFS) ile kimlik tabanlı kimlik doğrulama yöntemi desteklenmez.

## <a name="advantages-of-identity-based-authentication"></a>Kimlik tabanlı kimlik doğrulamanın avantajları
Azure dosyaları için kimlik tabanlı kimlik doğrulaması, paylaşılan anahtar kimlik doğrulamasını kullanarak çeşitli avantajlar sunar:

-   **Şirket içi AD DS ve Azure AD DS ile geleneksel kimlik tabanlı dosya paylaşma erişim deneyimini buluta genişletin**  
    Uygulamanızı kaldırın ve buluta Ötele, geleneksel dosya sunucularını Azure dosya paylaşımlarıyla değiştirerek, uygulamanızın dosya verilerine erişmek için şirket içi AD DS veya Azure AD DS kimlik bilgileriyle kimlik doğrulaması yapmasını isteyebilirsiniz. Azure dosyaları, şirket içi AD DS ya da Azure AD DS etki alanına katılmış VM 'lerden SMB üzerinden Azure dosya paylaşımlarına erişmek için hem şirket içi AD DS, hem de Azure AD DS kimlik bilgilerinin kullanılmasını destekler.

-   **Azure dosya paylaşımlarında ayrıntılı erişim denetimi uygulayın**  
    Paylaşıma, dizine veya dosya düzeyinde belirli bir kimliğe izin verebilirsiniz. Örneğin, proje işbirliği için tek bir Azure dosya paylaşımının kullanıldığı birkaç ekibin olduğunu varsayalım. Tüm takımlara hassas olmayan dizinlere erişim izni verebilirsiniz, ancak gizli finansal verileri içeren dizinlere erişimi yalnızca finans ekibinize kısıtlar. 

-   **Windows ACL 'Lerini (NTFS olarak da bilinir) verilerinizin yanı sıra yedekleyin**  
    Azure dosya paylaşımlarını, mevcut şirket içi dosya paylaşımlarınızı yedeklemek için kullanabilirsiniz. Azure dosyaları, SMB üzerinden Azure dosya paylaşımlarına bir dosya paylaşımı yedeklediğiniz zaman, ACL 'lerinizi verilerle birlikte korur.

## <a name="how-it-works"></a>Nasıl çalışır?

Azure dosya paylaşımları, şirket içi AD DS veya Azure AD DS kimlik doğrulaması için Kerberos protokolünü kullanır. İstemci üzerinde çalışan bir kullanıcı veya uygulamayla ilişkili bir kimlik, Azure dosya paylaşımlarında verilere erişmeye çalıştığında, kimlik doğrulaması için AD DS ya da Azure AD DS etki alanı hizmetine gönderilir. Kimlik doğrulaması başarılı olursa, Kerberos belirteci döndürür. İstemci, Kerberos belirtecini içeren bir istek gönderir ve Azure dosya paylaşımları, isteği yetkilendirmek için bu belirteci kullanır. Azure dosya paylaşımları, erişim kimlik bilgileri değil yalnızca Kerberos belirtecini alır.

Azure dosya paylaşımlarında kimlik tabanlı kimlik doğrulamasını etkinleştirebilmeniz için önce etki alanı ortamınızı ayarlamanız gerekir.

### <a name="ad-ds"></a>AD DS

Şirket içi AD DS kimlik doğrulaması için AD etki alanı denetleyicilerinizi ve etki alanınıza, makinelerinize veya VM 'Lere katılmanız gerekir. Etki alanı denetleyicilerinizi Azure VM 'lerinde veya şirket içinde barındırabilirsiniz. Her iki durumda da, etki alanı katılmış istemcilerinizin etki alanı hizmetine bir görüş satırı olması gerekir, bu nedenle etki alanı hizmetinizin kurumsal ağı veya sanal ağı (VNET) içinde olmaları gerekir.

Aşağıdaki diyagramda SMB üzerinden Azure dosya paylaşımlarına yönelik şirket içi AD DS kimlik doğrulaması gösterilmektedir. Şirket içi AD DS Azure AD Connect eşitleme kullanılarak Azure AD ile eşitlenmelidir. Yalnızca şirket içi AD DS ve Azure AD 'de bulunan karma kullanıcıların kimliği doğrulanabilir ve Azure dosya paylaşımının erişimine izin verebilir. Bunun nedeni, paylaşma düzeyi izninin Azure AD 'de temsil edilen kimliğe göre yapılandırılması, dizin/dosya düzeyi izninin AD DS ' de bu şekilde zorlandığı yerdir. İzinleri aynı karma kullanıcıya göre doğru şekilde yapılandırdığınızdan emin olun.

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="SMB üzerinden Azure dosya paylaşımlarına yönelik şirket içi AD DS kimlik doğrulamasını gösteren diyagram.":::

### <a name="azure-ad-ds"></a>Azure AD DS

Azure AD DS kimlik doğrulaması için Azure AD Domain Services ve etki alanına, dosya verilerine erişmeyi planladığınız sanal makinelere katılmayı etkinleştirmeniz gerekir. Etki alanına katılmış VM 'niz Azure AD DS aynı sanal ağda (VNET) bulunmalıdır. 

Aşağıdaki diyagram, Azure AD DS kimlik doğrulaması için SMB üzerinden Azure dosya paylaşımlarına yönelik iş akışını temsil eder. Azure dosya paylaşımlarında, şirket içi AD DS kimlik doğrulaması için benzer bir düzeni izler. İki önemli fark vardır:

- İlk olarak, depolama hesabını göstermek için Azure AD DS 'da kimlik oluşturmanız gerekmez. Bu işlem, arka planda etkinleştirme işlemi tarafından gerçekleştirilir.

- İkinci olarak, Azure AD 'de mevcut olan tüm kullanıcıların kimliği doğrulanabilir ve yetkilendirilenebilir. Kullanıcı yalnızca bulutta veya hibrit olabilir. Azure AD 'den Azure AD DS eşitleme, Kullanıcı yapılandırmasına gerek duymadan platform tarafından yönetilir. Ancak, istemcinin Azure AD DS etki alanına katılmış olması gerekir; Azure AD 'ye katılmış veya kayıtlı olamaz. 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="Diyagram":::

### <a name="enable-identity-based-authentication"></a>Kimlik tabanlı kimlik doğrulamasını etkinleştir

Yeni ve mevcut depolama hesaplarınızdaki Azure dosya paylaşımları için Azure AD DS ya da şirket içi AD DS kimlik tabanlı kimlik doğrulamasını etkinleştirebilirsiniz. Yalnızca bir etki alanı hizmeti, hesaptaki tüm dosya paylaşımları için geçerli olan depolama hesabında dosya erişimi kimlik doğrulaması için kullanılabilir. Makalelerimizde Azure AD DS kimlik doğrulaması için dosya paylaşımlarınızı ayarlama hakkında ayrıntılı yönergeler ve diğer makalelerimizde şirket içi AD DS için Azure dosya ve yönergeler [üzerinde Azure Active Directory Domain Services kimlik doğrulamasını etkinleştirme](storage-files-identity-auth-active-directory-domain-service-enable.md) , [Azure dosya paylaşımları için SMB üzerinden şirket içi Active Directory Domain Services kimlik doğrulamasını etkinleştirme](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Azure dosyaları için paylaşma düzeyi izinleri yapılandırma

Azure AD DS veya şirket içi AD DS kimlik doğrulaması etkinleştirildikten sonra Azure yerleşik rollerini kullanabilir ya da Azure AD kimlikleri için özel rolleri yapılandırabilir ve depolama hesaplarınızdaki herhangi bir dosya paylaşımına erişim hakları atayabilirsiniz. Atanan izin, izin verilen kimliğin, kök dizin bile değil, yalnızca paylaşıma erişim almasına izin verir. Azure dosya paylaşımları için dizin veya dosya düzeyi izinlerini yine de yapılandırmanız gerekir.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Azure dosyaları için dizin veya dosya düzeyi izinlerini yapılandırma

Azure dosya paylaşımları, kök dizin dahil olmak üzere hem dizin hem de dosya düzeyinde standart Windows dosya izinlerini uygular. Dizin veya dosya düzeyi izinlerinin yapılandırılması hem SMB hem de REST üzerinde desteklenir. Hedef dosya paylaşımından sanal makineden bağlama yapın ve Windows Dosya Gezgini, Windows [ıacl 'leri](/windows-server/administration/windows-commands/icacls)veya [set-ACL](/powershell/module/microsoft.powershell.security/get-acl) komutunu kullanarak izinleri yapılandırın.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Süper Kullanıcı izinleri için depolama hesabı anahtarını kullan

Depolama hesabı anahtarına sahip bir Kullanıcı, Azure dosya paylaşımlarına Süper Kullanıcı izinleriyle erişebilir. Süper Kullanıcı izinleri tüm erişim denetimi kısıtlamalarını atlar.

> [!IMPORTANT]
> Önerilen en iyi güvenlik uygulaması, depolama hesabı anahtarlarınızı paylaşmayı ve mümkün olan her durumda kimlik tabanlı kimlik doğrulaması kullanmaktan kaçınmaktır.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Azure dosya paylaşımlarına veri aktarırken dizin ve dosya ACL 'Lerini koruma

Azure dosyaları, verileri Azure dosya paylaşımlarına kopyalarken dizin veya dosya düzeyi ACL 'Lerinin korunması destekler. Bir dizin veya dosyadaki ACL 'Leri, Azure Dosya Eşitleme veya ortak dosya taşıma araç kümelerinden birini kullanarak Azure dosya paylaşımlarına kopyalayabilirsiniz. Örneğin, [](/windows-server/administration/windows-commands/robocopy) `/copy:s` bir Azure dosya paylaşımındaki ACL 'leri ve verileri kopyalamak için, Robocopy 'yi bayrağıyla birlikte kullanabilirsiniz. ACL 'ler varsayılan olarak korunur, ACL 'Leri korumak için depolama hesabınızda kimlik tabanlı kimlik doğrulamasını etkinleştirmeniz gerekmez.

## <a name="pricing"></a>Fiyatlandırma
Depolama hesabınızda SMB üzerinden kimlik tabanlı kimlik doğrulamasını etkinleştirmek için başka bir hizmet ücreti yoktur. Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure dosyaları fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/files/) ve [Azure AD Domain Services fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Sonraki adımlar
SMB üzerinden Azure dosyaları ve kimlik tabanlı kimlik doğrulaması hakkında daha fazla bilgi için şu kaynaklara bakın:

- [Azure Dosyalar dağıtımını planlama](storage-files-planning.md)
- [Azure dosya paylaşımları için SMB üzerinden şirket içi Active Directory Domain Services kimlik doğrulamasını etkinleştirme](storage-files-identity-auth-active-directory-enable.md)
- [Azure dosyalarında Azure Active Directory Domain Services kimlik doğrulamasını etkinleştirme](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [SSS](storage-files-faq.md)