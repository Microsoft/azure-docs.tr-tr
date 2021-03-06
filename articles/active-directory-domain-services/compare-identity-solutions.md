---
title: Azure 'da Active Directory tabanlı Hizmetleri karşılaştırın | Microsoft Docs
description: Bu genel bakışta, farklı kimlik tekliflerini Active Directory Domain Services, Azure Active Directory ve Azure Active Directory Domain Services karşılaştırırsınız.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: justinha
ms.openlocfilehash: 48ce982a6d6d7a3ed12d6e3b4711d111dec4d6f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174640"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Kendi kendini yönetilen Active Directory Domain Services, Azure Active Directory ve yönetilen Azure Active Directory Domain Services karşılaştırın

Uygulamaları, Hizmetleri veya cihazları merkezi bir kimliğe erişim sağlamak için Azure 'da Active Directory tabanlı hizmetler kullanmanın üç yaygın yolu vardır. Kimlik çözümlerinde bu seçenek, kuruluşunuzun ihtiyaçları için en uygun dizini kullanma esnekliği sağlar. Örneğin, çoğunlukla mobil cihazları çalıştıran yalnızca bulutta bulunan kullanıcıları yönetiyorsanız, kendi Active Directory Domain Services (AD DS) kimlik çözümünüzü oluşturmak ve çalıştırmak mantıklı olmayabilir. Bunun yerine yalnızca Azure Active Directory kullanabilirsiniz.

Üç Active Directory tabanlı kimlik çözümü ortak bir ad ve teknolojiyi paylaşıyorsa, ancak farklı müşteri taleplerini karşılayan hizmetler sağlamak üzere tasarlanmıştır. Yüksek düzeyde, bu kimlik çözümleri ve özellik kümeleri şunlardır:

* **Active Directory Domain Services (AD DS)** -kimlik ve kimlik doğrulama, bilgisayar nesne yönetimi, Grup İlkesi ve güvenler gibi önemli özellikler sağlayan, kurumsal kullanıma hazır Basit Dizin Erişim Protokolü (LDAP) sunucusu.
    * AD DS, şirket içi BT ortamıyla birçok kuruluşta merkezi bir bileşendir ve çekirdek Kullanıcı hesabı kimlik doğrulaması ve bilgisayar yönetimi özellikleri sağlar.
    * Daha fazla bilgi için [Windows Server belgelerindeki Active Directory Domain Services genel bakış][overview-adds]bölümüne bakın.
* **Azure Active Directory (Azure AD)** -Microsoft 365, Azure Portal veya SaaS uygulamaları gibi kaynaklar için Kullanıcı hesabı ve kimlik doğrulama hizmetleri sağlayan bulut tabanlı kimlik ve mobil cihaz yönetimi.
    * Azure AD, bulutta yerel olarak çalışabilen kullanıcılara tek bir kimlik sağlamak için şirket içi AD DS ortamıyla eşitlenebilir.
    * Azure AD hakkında daha fazla bilgi için bkz. [Azure Active Directory nedir?][whatis-azuread]
* **Azure Active Directory Domain Services (Azure AD DS)** -etki alanına katılması, Grup ILKESI, LDAP ve Kerberos/NTLM kimlik doğrulaması gibi tamamen uyumlu geleneksel AD DS özelliklerinin bir alt kümesiyle yönetilen etki alanı Hizmetleri sağlar.
    * Azure AD DS, kendi şirket içi AD DS ortamıyla eşitlenebilir Azure AD ile tümleşir. Bu özellik merkezi kimlik kullanım örneklerini, bir yükseltme ve kaydırma stratejisinin parçası olarak Azure 'da çalışan geleneksel Web uygulamalarına genişletir.
    * Azure AD ve şirket içi eşitleme hakkında daha fazla bilgi edinmek için bkz. [nesnelerin ve kimlik bilgilerinin yönetilen bir etki alanında nasıl eşitleneceğini][synchronization]öğrenin.

Bu genel bakış makalesinde, bu kimlik çözümlerinin birlikte nasıl çalıştığı veya kuruluşunuzun ihtiyaçlarına bağlı olarak bağımsız olarak nasıl kullanılabileceği karşılaştırılmaktadır.

> [!div class="nextstepaction"]
> [Başlamak için Azure portal kullanarak Azure AD DS yönetilen bir etki alanı oluşturun][tutorial-create]

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS ve otomatik olarak yönetilen AD DS

Kerberos veya NTLM gibi geleneksel kimlik doğrulama mekanizmalarına erişmesi gereken uygulama ve hizmetlerinize sahipseniz, bulutta Active Directory Domain Services sağlamanın iki yolu vardır:

* Azure Active Directory Domain Services kullanarak oluşturduğunuz *yönetilen bir etki alanı* (Azure AD DS). Microsoft gerekli kaynakları oluşturur ve yönetir.
* Sanal makineler (VM 'Ler), Windows Server Konuk işletim sistemi ve Active Directory Domain Services (AD DS) gibi geleneksel kaynakları kullanarak oluşturduğunuz ve yapılandırdığınız, *otomatik olarak yönetilen* bir etki alanı. Daha sonra bu kaynakları yönetmeye devam edersiniz.

Azure AD DS ile çekirdek hizmet bileşenleri, Microsoft tarafından *yönetilen* etki alanı deneyimi olarak dağıtılır ve korunur. VM 'Ler, Windows Server işletim sistemi veya etki alanı denetleyicileri (DC 'Ler) gibi bileşenler için AD DS altyapısını dağıtmayın, yönetmeyin, yamazsınız ve güvende kalmazsınız.

Azure AD DS, bazı tasarım ve yönetim karmaşıklığını azaltan geleneksel, otomatik olarak yönetilen AD DS ortamına yönelik daha küçük bir özellik alt kümesi sağlar. Örneğin, tasarlamak ve sürdürmek üzere AD Ormanı, etki alanı, siteler ve çoğaltma bağlantıları yoktur. [Azure AD DS ve şirket içi ortamlar arasında hala orman güvenleri oluşturabilirsiniz][create-forest-trust].

Bulutta çalışan ve Kerberos veya NTLM gibi geleneksel kimlik doğrulama mekanizmalarına erişmesi gereken uygulamalar ve hizmetler için Azure AD DS, en düşük düzeyde yönetim yüküne sahip bir yönetilen etki alanı deneyimi sağlar. Daha fazla bilgi için bkz. [Azure AD DS kullanıcı hesapları, parolalar ve yönetim Için yönetim kavramları][administration-concepts].

Kendi kendine yönetilen bir AD DS ortamını dağıtıp çalıştırdığınızda, ilişkili tüm altyapı ve Dizin bileşenlerini korumanız gerekir. Kendi kendine yönetilen bir AD DS ortamıyla ek bakım yükü vardır, ancak daha sonra Şemayı genişletme veya orman güvenleri oluşturma gibi ek görevler gerçekleştirebilirsiniz.

Bulutta uygulamalara ve hizmetlere kimlik sağlayan, kendi kendine yönetilen bir AD DS ortamı için ortak dağıtım modelleri şunlardır:

* **Tek başına bulut AD DS** -Azure VM 'leri etki alanı denetleyicileri olarak ve ayrı bir yalnızca bulut AD DS ortamı oluşturulur. Bu AD DS ortamı, şirket içi AD DS ortamıyla tümleştirilemiyor. Buluttaki VM 'Leri açmak ve yönetmek için farklı bir kimlik bilgileri kümesi kullanılır.
* **Kaynak ormanı dağıtımı** -Azure VM 'leri etki alanı denetleyicileri olarak yapılandırılır ve var olan bir ormanın parçası olan bir AD DS etki alanı oluşturulur. Bir güven ilişkisi daha sonra şirket içi AD DS ortamına yapılandırılır. Diğer Azure VM 'Leri, bulutta bu kaynak ormanına etki alanına katılabilir. Kullanıcı kimlik doğrulaması, şirket içi AD DS ortamına bir VPN/ExpressRoute bağlantısı üzerinden çalışır.
* Şirket **içi etki alanını Azure 'A genişletme** -bir Azure sanal ağı, bir VPN/ExpressRoute bağlantısı kullanarak şirket içi ağa bağlanır. Azure VM 'Leri, bu Azure sanal ağına bağlanarak şirket içi AD DS ortamına katılmasına olanak tanır.
    * Diğer bir seçenek de Azure VM 'Leri oluşturmak ve bunları şirket içi AD DS etki alanından çoğaltma etki alanı denetleyicileri olarak yükseltir. Bu etki alanı denetleyicileri, şirket içi AD DS ortamına bir VPN/ExpressRoute bağlantısı üzerinden çoğaltılır. Şirket içi AD DS etki alanı, Azure 'da etkin bir şekilde genişletilir.

Aşağıdaki tabloda, kuruluşunuz için ihtiyacınız olabilecek özelliklerden bazıları ve yönetilen bir Azure AD DS etki alanı veya otomatik olarak yönetilen bir AD DS etki alanı arasındaki farklar özetlenmektedir:

| **Özellik** | **Azure AD DS** | **Kendi kendine yönetilen AD DS** |
| ----------- |:---------------:|:----------------------:|
| **Yönetilen hizmet**                               | **&#x2713;** | **&#x2715;** |
| **Güvenli dağıtımlar**                            | **&#x2713;** | Yönetici, dağıtımın güvenliğini sağlar |
| **DNS sunucusu**                                    | **&#x2713;** (yönetilen hizmet) |**&#x2713;** |
| **Etki alanı veya kuruluş yöneticisi ayrıcalıkları** | **&#x2715;** | **&#x2713;** |
| **Etki alanına katılma**                                   | **&#x2713;** | **&#x2713;** |
| **NTLM ve Kerberos kullanarak etki alanı kimlik doğrulaması** | **&#x2713;** | **&#x2713;** |
| **Kerberos kısıtlanmış temsili**               | Kaynak tabanlı | Kaynak tabanlı & hesap tabanlı|
| **Özel OU yapısı**                           | **&#x2713;** | **&#x2713;** |
| **grup ilkesi**                                  | **&#x2713;** | **&#x2713;** |
| **Şema uzantıları**                             | **&#x2715;** | **&#x2713;** |
| **AD etki alanı/orman güvenleri**                     | **&#x2713;** (yalnızca tek yönlü giden orman güvenleri) | **&#x2713;** |
| **Güvenli LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP okuma**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP yazma**                                    | **&#x2713;** (yönetilen etki alanı içinde) | **&#x2713;** |
| **Coğrafi olarak dağıtılan dağıtımlar**                   | **&#x2713;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS ve Azure AD

Azure AD, kuruluş tarafından kullanılan cihazların kimliğini yönetmenizi ve bu cihazlardan şirket kaynaklarına erişimi kontrol etmenizi sağlar. Kullanıcılar, bir kimlik ile cihaz sağlayan kişisel cihazlarını (kendi kendine getir (BYO) modeli) Azure AD ile de kaydedebilir. Daha sonra Azure AD, bir Kullanıcı Azure AD 'ye oturum açtığında ve güvenli kaynaklara erişmek için cihazı kullandığında cihazın kimliğini doğrular. Cihaz, Microsoft Intune gibi mobil cihaz yönetimi (MDM) yazılımı kullanılarak yönetilebilir. Bu yönetim özelliği, gizli kaynaklara erişimi yönetilen ve ilkeyle uyumlu cihazlarla kısıtlamanızı sağlar.

Geleneksel bilgisayarlar ve dizüstü bilgisayarlar da Azure AD 'ye katılabilir. Bu mekanizma, kullanıcıların şirket kimlik bilgilerini kullanarak cihazda oturum açmalarına izin vermek gibi, kişisel bir cihazı Azure AD 'ye kaydetmenin sunduğu avantajlardan de yararlanır.

Azure AD 'ye katılmış cihazlar aşağıdaki avantajları sağlar:

* Azure AD tarafından güvenliği sağlanmış uygulamalara çoklu oturum açma (SSO).
* Kurumsal ilkeyle uyumlu Kullanıcı ayarlarının cihazlar arasında dolaşımını.
* Kurumsal kimlik bilgileri ile Iş için Windows Mağazası 'na erişin.
* Iş için Windows Hello.
* Şirket ilkesiyle uyumlu cihazlardan uygulamalara ve kaynaklara kısıtlı erişim.

Cihazlar, şirket içi AD DS ortamı içeren karma bir dağıtım olmadan veya Azure AD 'ye katılabilir. Aşağıdaki tabloda ortak cihaz sahipliği modelleri ve bunların genellikle bir etki alanına nasıl birleştirilebilecekleri özetlenmektedir:

| **Cihaz türü**                                        | **Cihaz platformları**             | **Mechanism**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Kişisel cihazlar                                          | Windows 10, iOS, Android, macOS | Azure AD kayıtlı    |
| Kuruluşa ait cihaz, şirket içi AD DS katılmadı | Windows 10                       | Azure AD'ye katılanlar        |
| Kuruluşa ait cihaz, şirket içi AD DS katıldı  | Windows 10                       | Hibrit Azure AD'ye katılmış |

Azure AD 'ye katılmış veya kayıtlı bir cihazda, Kullanıcı kimlik doğrulaması modern OAuth/OpenID Connect tabanlı protokoller kullanılarak yapılır. Bu protokoller Internet üzerinden çalışacak şekilde tasarlanmıştır. bu nedenle, kullanıcıların şirket kaynaklarına her yerden erişebileceği mobil senaryolar için harika bir seçenektir.

Azure AD DS katılmış cihazlarda, uygulamalar kimlik doğrulaması için Kerberos ve NTLM protokollerini kullanabilir, bu nedenle, yükseltme ve kaydırma stratejisinin bir parçası olarak Azure VM 'lerinde çalışacak şekilde geçirilen eski uygulamaları destekleyebilir. Aşağıdaki tabloda cihazların nasıl temsil edildiği ve dizinde kimlik doğrulayabileceği fark özetlenmektedir:

| **Görünüş**                      | **Azure AD 'ye katılmış**                                 | **Azure AD DS 'a katılmış**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Cihazı denetleyen            | Azure AD                                            | Azure AD DS yönetilen etki alanı                                                |
| Dizindeki temsili | Azure AD dizinindeki cihaz nesneleri            | Azure AD DS yönetilen etki alanındaki bilgisayar nesneleri                        |
| Kimlik Doğrulaması                  | OAuth/OpenID Connect tabanlı protokoller              | Kerberos ve NTLM protokolleri                                               |
| Yönetim                      | Intune gibi mobil cihaz yönetimi (MDM) yazılımı | Grup İlkesi                                                              |
| Ağ                      | Internet üzerinden çalışarak                             | Yönetilen etki alanının dağıtıldığı sanal ağa bağlı veya eşlenmiş olmalıdır |
| Harika...                    | Son Kullanıcı mobil veya masaüstü cihazları                  | Azure 'da dağıtılan sunucu VM 'Leri                                              |


Şirket içi AD DS ve Azure AD ADFS kullanılarak federal kimlik doğrulaması için yapılandırılırsa, Azure DS 'de kullanılabilir (geçerli/geçerli) Parola karması yoktur. Beslenen kimlik doğrulaması uygulanmadan önce oluşturulan Azure AD Kullanıcı hesaplarının eski bir parola karması olabilir, ancak bu durum büyük olasılıkla şirket içi parolasının bir karmasıyla eşleşmez. Bu nedenle Azure AD DS, kullanıcıların kimlik bilgilerini doğrulayamayacak

## <a name="next-steps"></a>Sonraki adımlar

Azure AD DS kullanmaya başlamak için [Azure Portal kullanarak bir azure AD DS yönetilen etki alanı oluşturun][tutorial-create].

Ayrıca, [Azure AD DS kullanıcı hesapları, parolaları ve yönetimi için yönetim kavramları][administration-concepts] ve [nesnelerin ve kimlik bilgilerinin yönetilen bir etki alanında nasıl eşitleneceğini][synchronization]öğrenin.

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
[whatis-azuread]: ../active-directory/fundamentals/active-directory-whatis.md
[overview-adds]: /windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview
[create-forest-trust]: tutorial-create-forest-trust.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
