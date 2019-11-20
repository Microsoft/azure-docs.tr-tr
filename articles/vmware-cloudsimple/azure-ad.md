---
title: CloudSimple tarafından Azure VMware çözümü-özel bulutta kimlik kaynağı olarak Azure AD kullanın
description: Azure 'da CloudSimple 'a erişen kullanıcıların kimliğini doğrulamak için CloudSimple özel bulutunuzda bir kimlik sağlayıcısı olarak Azure AD 'nin nasıl ekleneceğini açıklar.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a5871a052998e9dd32d698c5a89f57064cc7d6b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987577"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>CloudSimple özel bulutu 'nda vCenter için kimlik sağlayıcısı olarak Azure AD kullanma

VMware yöneticilerinin vCenter 'a erişmesi için Azure Active Directory (Azure AD) ile kimlik doğrulaması yapmak üzere CloudSimple özel bulut vCenter ' yi ayarlayabilirsiniz. Çoklu oturum açma kimlik kaynağı kurulduktan sonra, **cloudowner** kullanıcısı kimlik kaynağından vCenter 'a kullanıcı ekleyebilir.  

Active Directory etki alanınızı ve etki alanı denetleyicilerinizi aşağıdaki yollarla ayarlayabilirsiniz:

* Şirket içinde çalışan etki alanı ve etki alanı denetleyicileri Active Directory
* Azure aboneliğinizde sanal makineler olarak çalışan etki alanı ve etki alanı denetleyicileri Active Directory
* CloudSimple özel bulutunuzda çalışan yeni Active Directory etki alanı ve etki alanı denetleyicileri
* Azure Active Directory hizmeti

Bu kılavuzda, Azure AD 'yi kimlik kaynağı olarak ayarlamak için gereken görevler açıklanmaktadır.  Şirket içi Active Directory veya Azure 'da çalışan Active Directory kullanma hakkında bilgi için, kimlik kaynağını ayarlamayla ilgili ayrıntılı yönergeler için [Active Directory kullanmak üzere vCenter Identity kaynaklarını ayarlama](set-vcenter-identity.md) bölümüne bakın.

## <a name="about-azure-ad"></a>Azure AD hakkında

Azure AD, Microsoft çok kiracılı, bulut tabanlı dizin ve kimlik yönetimi hizmetidir.  Azure AD, kullanıcıların kimliğini doğrulamak ve Azure 'da farklı hizmetlere erişmek için ölçeklenebilir, tutarlı ve güvenilir bir kimlik doğrulama mekanizması sağlar.  Ayrıca, Azure AD 'yi bir kimlik doğrulama/kimlik kaynağı olarak kullanmak üzere herhangi bir üçüncü taraf hizmeti için Güvenli LDAP hizmetleri sağlar.  Azure AD, özel bulutu yöneten kullanıcılar için özel bulutunuzun erişimine izin vermek üzere kullanılabilen temel dizin hizmetlerini, Gelişmiş kimlik idare ve uygulama erişim yönetimini birleştirir.

Azure AD 'yi vCenter ile bir kimlik kaynağı olarak kullanmak için Azure AD ve Azure AD etki alanı Hizmetleri 'ni ayarlamanız gerekir. Aşağıdaki yönergeleri izleyin:

1. [Azure AD ve Azure AD etki alanı Hizmetleri 'ni ayarlama](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Özel bulut vCenter 'unuzda bir kimlik kaynağı ayarlama](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Azure AD ve Azure AD etki alanı hizmetlerini ayarlama

Başlamadan önce, genel yönetici ayrıcalıklarıyla Azure aboneliğinize erişmeniz gerekir.  Aşağıdaki adımlar genel yönergeler sağlar. Ayrıntılar Azure belgelerinde bulunur.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Zaten Azure AD varsa, bu bölümü atlayabilirsiniz.

1. Azure AD [belgelerinde](../active-directory/fundamentals/get-started-azure-ad.md)açıklandığı gibi ABONELIĞINIZDE Azure AD 'yi ayarlayın.
2. [Azure Active Directory Premium kaydolma](../active-directory/fundamentals/active-directory-get-started-premium.md)konusunda açıklandığı gibi aboneliğinizde Azure Active Directory Premium etkinleştirin.
3. Özel bir etki alanı adı ayarlayın ve [Azure Active Directory için](../active-directory/fundamentals/add-custom-domain.md)özel etki alanı adı ekleme bölümünde açıklandığı gibi özel etki alanı adını doğrulayın.
    1. Azure 'da belirtilen bilgileri kullanarak etki alanı kayıt şirketinizde bir DNS kaydı ayarlayın.
    2. Özel etki alanı adını birincil etki alanı olarak ayarlayın.

İsterseniz diğer Azure AD özelliklerini yapılandırabilirsiniz.  Bunlar, Azure AD ile vCenter kimlik doğrulamasını etkinleştirmek için gerekli değildir.

### <a name="azure-ad-domain-services"></a>Azure AD etki alanı Hizmetleri

> [!NOTE]
> Bu, Azure AD 'yi vCenter için kimlik kaynağı olarak etkinleştirmeye yönelik önemli bir adımdır.  Herhangi bir sorunu önlemek için tüm adımların doğru gerçekleştirildiğinden emin olun.

1. [Azure Portal kullanarak Azure Active Directory etki alanı Hizmetleri 'Ni etkinleştirme](../active-directory-domain-services/active-directory-ds-getting-started.md)bölümünde açıklandığı gıbı Azure AD etki alanı Hizmetleri 'ni etkinleştirin.
2. Azure AD etki alanı Hizmetleri tarafından [, Azure Portal kullanarak Azure Active Directory Domain Services etkinleştirme](../active-directory-domain-services/active-directory-ds-getting-started-network.md)bölümünde açıklandığı gibi kullanılacak ağı ayarlayın.
3. [Azure Portal kullanarak Azure Active Directory Domain Services etkinleştirme](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md)bölümünde açıklandığı gibi Azure AD Domain Services yönetmek Için yönetici grubunu yapılandırın.
4. Azure AD Domain Services için DNS ayarlarını, [etkinleştirme Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-dns.md)' de açıklandığı gibi güncelleştirin.  Internet üzerinden AD 'ye bağlanmak istiyorsanız, Azure AD etki alanı Hizmetleri genel IP adresi için DNS kaydını etki alanı adına ayarlayın.
5. Kullanıcılar için parola karma eşitlemesini etkinleştirin.  Bu adım, NT LAN Manager (NTLM) ve Kerberos kimlik doğrulaması için gereken parola karmalarının Azure AD Domain Services için eşitlenmesini sağlar. Parola karma eşitlemesini ayarladıktan sonra kullanıcılar, şirket kimlik bilgileri ile yönetilen etki alanında oturum açabilir. Bkz. [Azure Active Directory Domain Services parola karma eşitlemesini etkinleştirme](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).
    1. Yalnızca bulutta bulunan kullanıcılar varsa, parola karmalarının NTLM veya Kerberos tarafından istenen biçimde depolanmasını sağlamak için <a href="http://myapps.microsoft.com/" target="_blank">Azure AD erişim paneli</a> ' ni kullanarak parolalarını değiştirmeleri gerekir.  [Yalnızca bulutta yer alan Kullanıcı hesapları için yönetilen etki alanınız için parola karma eşitlemesini etkinleştirme](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)konusundaki yönergeleri izleyin.  Bu adım, Azure AD dizininizde Azure portal veya Azure AD PowerShell cmdlet 'lerini kullanarak oluşturulan tek tek kullanıcılar ve tüm yeni kullanıcılar için yapılmalıdır. Azure AD etki alanı Hizmetleri 'ne erişmesi gereken kullanıcıların, <a href="http://myapps.microsoft.com/" target="_blank">Azure AD erişim panelini</a> kullanması ve parolayı değiştirmesi için profiline erişmesi gerekir.

        > [!NOTE]
        > Kuruluşunuzun yalnızca bulutta yer alan Kullanıcı hesapları varsa, Azure Active Directory Domain Services kullanması gereken tüm kullanıcılar parolalarını değiştirmeli. Yalnızca bulutta yer alan bir kullanıcı hesabı, Azure portal veya Azure AD PowerShell cmdlet’leri kullanılarak Azure AD dizininizde oluşturulmuş bir hesaptır. Bu tür kullanıcı hesapları şirket içi dizinden eşitlenmez.

    2. Parolaları şirket içi Active Directory 'nizden eşitliyorsanız, [Active Directory belgelerindeki](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)adımları izleyin.

6.  [Azure AD Domain Services yönetilen bir etki alanı için GÜVENLI LDAP (LDAPS) yapılandırma](../active-directory-domain-services/tutorial-configure-ldaps.md)başlığı altında açıklandığı gibi Azure ACTIVE DIRECTORY DOMAIN SERVICES Güvenli LDAP 'i yapılandırın.
    1. Güvenli LDAP [için bir sertifika edinin](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)ve Azure konu başlığı altında açıklandığı gibi Güvenli LDAP tarafından kullanılmak üzere bir sertifikayı karşıya yükleyin.  CloudSimple, vCenter 'un sertifikaya güvendiğinden emin olmak için bir sertifika yetkilisi tarafından verilen imzalı bir sertifikayı kullanmanızı önerir.
    2. [Azure AD Domain Services yönetilen bir etki alanı için Güvenli LDAP (LDAPS) etkinleştirme](../active-directory-domain-services/tutorial-configure-ldaps.md)bölümünde açıklandığı gıbı Güvenli LDAP 'yi etkinleştirin.
    3. Kimlik kaynağını yapılandırırken, sertifikanın genel bölümünü (özel anahtar olmadan). cer biçiminde, vCenter ile kullanmak üzere kaydedin.
    4. Azure AD etki alanı hizmetlerine Internet erişimi gerekliyse, ' internet üzerinden LDAP 'a güvenli erişime Izin ver ' seçeneğini etkinleştirin.
    5. TCP bağlantı noktası 636 için Azure AD etki alanı Hizmetleri NSG için gelen güvenlik kuralını ekleyin.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Özel bulut vCenter 'unuzda bir kimlik kaynağı ayarlama

1. Özel bulut vCenter için [ayrıcalıkları ilerletin](escalate-private-cloud-privileges.md) .
2. Kimlik kaynağının kurulması için gereken yapılandırma parametrelerini toplayın.

    | **Seçenek** | **Açıklama** |
    |------------|-----------------|
    | **Ad** | Kimlik kaynağının adı. |
    | **Kullanıcılar için temel DN** | Kullanıcılar için temel ayırt edici ad.  Azure AD için şunu kullanın: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` örnek: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`.|
    | **Etki alanı adı** | Etki alanının FDQN 'ı, örneğin, example.com. Bu metin kutusunda bir IP adresi sağlamaın. |
    | **Etki alanı diğer adı** | *(isteğe bağlı)* Etki alanı NetBIOS adı. SSPI kimlik doğrulamaları kullanıyorsanız, Active Directory etki alanının NetBIOS adını kimlik kaynağının diğer adı olarak ekleyin. |
    | **Gruplar için temel DN** | Gruplar için temel ayırt edici ad. Azure AD için şunu kullanın: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` örnek: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **Birincil sunucu URL 'SI** | Etki alanı için birincil etki alanı denetleyicisi LDAP sunucusu.<br><br> `ldaps://hostname:port`biçimini kullanın. Bağlantı noktası genellikle LDAPS bağlantıları için 636 ' dir. <br><br>Birincil veya ikincil LDAP URL 'sinde `ldaps://` kullandığınızda Active Directory sunucusunun LDAPS uç noktası için güven kuran bir sertifika gerekir. |
    | **İkincil sunucu URL 'SI** | Yük devretme için kullanılan ikincil etki alanı denetleyicisi LDAP sunucusunun adresi. |
    | **Sertifika Seç** | Active Directory LDAP sunucunuz veya OpenLDAP sunucu kimlik kaynağı ile LDAPS kullanmak istiyorsanız, URL metin kutusuna `ldaps://` yazdıktan sonra Sertifika Seç düğmesi görünür. İkincil bir URL gerekli değildir. |
    | **Kullanıcı Adı** | Etki alanındaki, kullanıcılar ve gruplar için temel DN 'ye yönelik en az salt okuma erişimi olan bir kullanıcının KIMLIĞI. |
    | **Parola** | Kullanıcı adı tarafından belirtilen kullanıcının parolası. |

3. Ayrıcalıkların ilerletiden sonra özel bulut vCenter 'unuzda oturum açın.
4. Azure Active Directory bir kimlik kaynağı olarak ayarlamak için önceki adımdaki değerleri kullanarak [vCenter 'da kimlik kaynağı ekleme](set-vcenter-identity.md#add-an-identity-source-on-vcenter) bölümündeki yönergeleri izleyin.
5. [Bir vCenter çoklu oturum açma grubuna üye ekleme](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)VMware konusunda açıklandığı gibi, Azure AD 'den vCenter gruplarına kullanıcı/grup ekleyin.

> [!CAUTION]
> Yeni kullanıcılar yalnızca *bulut sahibi grubu*, *bulut-genel-küme-yönetici-grubu*, *bulut-genel-depolama-yönetici-grubu*, bulut-genel- *Ağ-Yönetici-Grup* veya *bulut-genel-VM-yönetici grubu*için eklenmelidir.  *Yöneticiler* grubuna eklenen kullanıcılar otomatik olarak kaldırılacaktır.  Yalnızca hizmet hesaplarının *Yöneticiler* grubuna eklenmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* [Özel bulut izin modeli hakkında bilgi edinin](learn-private-cloud-permissions.md)
