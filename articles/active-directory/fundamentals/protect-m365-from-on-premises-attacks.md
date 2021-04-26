---
title: Şirket içi saldırılardan Microsoft 365 koruma
description: Şirket içi bir saldırının Microsoft 365 etkilememesini sağlamaya yönelik yönergeler.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d548f4d792d8980e2aa5040b09530eaf7868c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609915"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Şirket içi saldırılardan Microsoft 365 koruma

Birçok müşteri, kullanıcılarını, cihazlarını ve uygulamalarını avantajlarına yönelik olarak kendi özel kurumsal ağlarını Microsoft 365. Ancak, bu özel ağların çok iyi belgelenmiş yollarla güvenliği tehlikeye girebilir. Microsoft 365 birçok kuruluş için bir nervous sistem sıralaması görevi gördüğü için, şirket içi altyapıda güvenliği aşılmış hale getirmemesini sağlamak önemlidir.

Bu makalede, sistemlerinizi şirket içi uzlaşmadan Microsoft 365 bulut ortamınızı koruyacak şekilde nasıl yapılandıracağınız gösterilir. Birincil olarak şu şekilde odaklanıyoruz: 

- Azure Active Directory (Azure AD) kiracı yapılandırma ayarları.
- Azure AD kiracıları, şirket içi sistemlere güvenle bağlanabilir.
- Uygulamalarınızı, bulut sistemlerinizi şirket içi uzlaşmadan koruyan yollarla çalıştırmak için gereken dengeler.

Microsoft 365 Bulut ortamınızın güvenliğini sağlamak için bu kılavuzun uygulanması önemle önerilir.
> [!NOTE]
> Bu makale başlangıçta bir blog gönderisi olarak yayımlandı. Bu, longeçekimi ve bakım için geçerli konumuna taşındı.
>
> Bu makalenin çevrimdışı sürümünü oluşturmak için, tarayıcınızın PDF 'yi Yazdır işlevini kullanın. Güncelleştirmeler için sık sık tekrar kontrol edin.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Güvenliği aşılmış şirket içi ortamlardan birincil tehdit vektörleri


Microsoft 365 bulut ortamınız, kapsamlı bir izleme ve güvenlik altyapısından faydalanır. Makine öğrenimi ve insan zekası ' nı kullanarak Microsoft 365 dünya genelindeki trafiğe bakar. Saldırıları hızla algılayabilir ve neredeyse gerçek zamanlı olarak yeniden yapılandırmanıza izin verebilir. 

Şirket içi altyapıyı Microsoft 365 'e bağlayan karma dağıtımlarda, birçok kuruluş, kritik kimlik doğrulama ve dizin nesnesi durum yönetimi kararları için şirket içi bileşenlere güven devredebilir.
Ne yazık ki, şirket içi ortamın güvenliği tehlikeye girerse, bu güven ilişkileri bir saldırganın Microsoft 365 ortamınızı tehlikeye atabilir.

İki birincil tehdit vektörü, *Federasyon güven ilişkileri* ve *hesap eşitleme* ' dir. Her iki vektör de bir saldırganın buluta yönetici erişimine izin verebilir.

* SAML kimlik doğrulaması gibi **Federasyon güven ilişkileri**, şirket içi kimlik altyapınızda Microsoft 365 kimlik doğrulaması yapmak için kullanılır. Bir SAML belirteç imzalama sertifikası tehlikeye atılırsa, Federasyon, bu sertifikaya sahip olan herkesin buluttaki herhangi bir kullanıcının kimliğine bürünmesini sağlar. *Mümkün olduğunda Microsoft 365 kimlik doğrulaması için Federasyon güven ilişkilerini devre dışı bırakmanızı öneririz.*

* **Hesap eşitleme** , ayrıcalıklı kullanıcıları (kimlik bilgileri dahil) veya Microsoft 365 yönetim ayrıcalıklarına sahip olan grupları değiştirmek için kullanılabilir. Eşitlenmiş nesnelerin, doğrudan veya güvenilir roller ya da gruplara dahil *olmak üzere Microsoft 365 içindeki bir kullanıcının ötesinde hiçbir ayrıcalık olmamasını öneririz* . Bu nesnelerin güvenilen bulut rollerinde veya gruplarında doğrudan veya iç içe atanması bulunmadığından emin olun.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Şirket içi uzlaşmadan Microsoft 365 koruma


Daha önce özetlenen tehdit vektörlerine yönelik olarak, aşağıdaki diyagramda gösterilen ilkelere bağlı kalmanızı öneririz:

![Microsoft 365 korumak için başvuru mimarisi.](media/protect-m365/protect-m365-principles.png)

1. **Microsoft 365 Yönetici hesaplarınızı tamamen yalıtın.** Şunları yapmanız gerekir:

    * Azure AD 'de ana kopyalı.

     * Çok faktörlü kimlik doğrulaması kullanılarak kimlik doğrulandı.

     *  Azure AD koşullu erişim tarafından güvenli hale getirilir.

     *  Yalnızca Azure tarafından yönetilen iş istasyonları kullanılarak erişilir.

    Bu yönetici hesapları kısıtlı-hesapları kullanır. *Şirket içi hesapların Microsoft 365 için yönetim ayrıcalıklarına sahip olması gerekir.* 

    Daha fazla bilgi için, [Microsoft 365 yönetici rollerine genel bakış](/microsoft-365/admin/add-users/about-admin-roles)bölümüne bakın. Ayrıca bkz. [Azure AD 'de Microsoft 365 rolleri](../roles/m365-workload-docs.md).

1. **Microsoft 365 cihazları yönetin.** Şirket içi cihaz yönetimi altyapınızın bağımlılıklarını ortadan kaldırmak için Azure AD JOIN ve bulut tabanlı mobil cihaz yönetimi (MDM) kullanın. Bu bağımlılıklar cihaz ve güvenlik denetimlerini tehlikeye atabilir.

1. **Şirket içi hesabın Microsoft 365 için yükseltilmiş ayrıcalıklara sahip olmadığından emin olun.**
    Bazı hesaplar, NTLM, LDAP veya Kerberos kimlik doğrulaması gerektiren şirket içi uygulamalara erişir. Bu hesapların kuruluşun şirket içi kimlik altyapısında olması gerekir. Hizmet hesapları da dahil olmak üzere bu hesapların ayrıcalıklı bulut rollerine veya gruplarına dahil olmadığından emin olun. Ayrıca, bu hesaplardaki değişikliklerin Bulut ortamınızın bütünlüğünü etkilememesini sağlayın. Ayrıcalıklı şirket içi yazılımlar Microsoft 365 ayrıcalıklı hesapları veya rolleri etkilememelidir.

1. Şirket içi kimlik bilgilerinizle bağımlılıkları ortadan kaldırmak için **Azure AD bulut kimlik doğrulamasını kullanın** . Her zaman Windows Hello, FIDO, Microsoft Authenticator veya Azure AD çok faktörlü kimlik doğrulaması gibi güçlü kimlik doğrulaması kullanın.

## <a name="specific-security-recommendations"></a>Belirli güvenlik önerileri


Aşağıdaki bölümlerde, daha önce açıklanan ilkelerin nasıl uygulanacağı hakkında özel yönergeler sağlanmaktadır.

### <a name="isolate-privileged-identities"></a>Ayrıcalıklı kimlikleri yalıtma


Azure AD 'de yöneticiler gibi ayrıcalıklı rollere sahip kullanıcılar, ortamın geri kalanını derlemek ve yönetmek için güvenin köküdür. Bir uzlaşmasının etkilerini en aza indirmek için aşağıdaki uygulamaları uygulayın.

* Azure AD ve Microsoft 365 ayrıcalıklı roller için yalnızca bulut hesapları kullanın.

* Microsoft 365 ve Azure AD 'yi yönetmek için ayrıcalıklı erişim [cihazları](/security/compass/privileged-access-devices#device-roles-and-profiles) dağıtın.

*  Ayrıcalıklı rollere sahip tüm insan hesaplarına tam zamanında (JıT) erişim için [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) dağıtın. Rolleri etkinleştirmek için güçlü kimlik doğrulaması gerektir.

* [Gerekli görevleri yapmak için gereken en düşük ayrıcalığa](../roles/delegate-by-task.md)izin veren yönetim rolleri sağlayın.

* Aynı anda temsilciyi ve birden çok rolü içeren zengin bir rol atama deneyimini etkinleştirmek için Azure AD güvenlik grupları 'nı veya Microsoft 365 gruplarını kullanmayı göz önünde bulundurun. Bu gruplar toplu olarak *bulut grupları* olarak adlandırılır. [Rol tabanlı erişim denetimini de etkinleştirin](../roles/groups-assign-role.md). Rol kapsamını kuruluşun bir bölümüyle kısıtlamak için [Yönetim birimlerini](../roles/administrative-units.md) kullanabilirsiniz.

* [Acil durum erişim hesapları](../roles/security-emergency-access.md)dağıtın. Kimlik bilgilerini depolamak için şirket içi parola *kasaları kullanmayın.*

Daha fazla bilgi için bkz. [ayrıcalıklı erişimin güvenliğini sağlama](/security/compass/overview). Ayrıca bkz. [Azure AD 'de Yöneticiler Için güvenli erişim uygulamaları](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Bulut kimlik doğrulaması kullan 

Kimlik bilgileri, birincil saldırı vektörüdür. Kimlik bilgilerini daha güvenli hale getirmek için aşağıdaki uygulamaları uygulayın:

* [Passwordless kimlik doğrulaması dağıtın](../authentication/howto-authentication-passwordless-deployment.md). Parolasız kimlik bilgilerini dağıtarak parola kullanımını mümkün olduğunca azaltabilirsiniz. Bu kimlik bilgileri, bulutta yerel olarak yönetilir ve onaylanır. Şu kimlik doğrulama yöntemlerinden birini seçin:

   * [İş için Windows Hello](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Microsoft Authenticator uygulaması](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 güvenlik anahtarları](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Çok faktörlü kimlik doğrulamasını dağıtın](../authentication/howto-mfa-getstarted.md). [Azure AD çok faktörlü kimlik doğrulamasını kullanarak birden çok güçlü kimlik bilgileri](../fundamentals/resilience-in-credentials.md)sağlayın. Bu şekilde, bulut kaynaklarına erişim, Azure AD 'de yönetilebilen ve bir şirket içi parolaya ek olarak yönetilen bir kimlik bilgisi gerektirir. Daha fazla bilgi için bkz. [Azure ad kullanarak dayanıklı erişim denetimi yönetim stratejisi oluşturma](./resilience-overview.md).

### <a name="limitations-and-tradeoffs"></a>Sınırlamalar ve avantajları

* Karma hesap parolası yönetimi, parola koruma aracıları ve parola geri yazma aracıları gibi karma bileşenleri gerektirir. Şirket içi altyapınız tehlikedeyse, saldırganlar bu aracıların bulunduğu makineleri denetleyebilir. Bu güvenlik açığı, bulut altyapınızı tehlikeye atabilir. Ancak bulut hesaplarınız bu bileşenleri şirket içi uzlaşmadan korumaz.

*  Active Directory 'ten eşitlenen şirket içi hesapların Azure AD 'de kullanım süreleri dolmayacak şekilde işaretlenir. Bu ayar genellikle şirket içi Active Directory parola ayarları tarafından azaltılmıştır. Ancak, Active Directory Şirket içi örneğinizin güvenliği tehlikeye atılırsa ve eşitleme devre dışıysa, parola değişikliklerini zorlamak için [Enforcechoparlör Passwordpolicyforpasswordsyncedusers](../hybrid/how-to-connect-password-hash-synchronization.md) seçeneğini ayarlamanız gerekir.

## <a name="provision-user-access-from-the-cloud"></a>Buluttan Kullanıcı erişimi sağlama

*Sağlama* , uygulama veya kimlik sağlayıcılarındaki Kullanıcı hesaplarının ve grupların oluşturulmasına başvurur.

![Sağlama mimarisinin diyagramı.](media/protect-m365/protect-m365-provision.png)

Aşağıdaki sağlama yöntemlerini öneririz:

* **Cloud İK uygulamalarından Azure AD 'ye sağlama**: Bu sağlama, bulut HR UYGULAMALARıNıZDAN Azure AD 'ye yönelik birleştirici-Mover-Leaver döngüsünü kesintiye uğratmadan, şirket içi güvenliğin yalıtılmasını sağlar.

* **Bulut uygulamaları**: mümkün olduğunda, şirket içi sağlama çözümlerinin aksıne [Azure AD uygulama sağlamayı](../app-provisioning/user-provisioning.md) dağıtın. Bu yöntem, hizmet olarak yazılım (SaaS) uygulamalarınızın bazılarının şirket içi ihlallerdeki kötü amaçlı korsan profillerinden etkilenmemesini önler. 

* **Dış kimlikler**: [Azure AD B2B işbirliğini](../external-identities/what-is-b2b.md)kullanın.
    Bu yöntem, iş ortakları, müşteriler ve tedarikçilerle harici işbirliği için şirket içi hesapların bağımlılığını azaltır. Diğer kimlik sağlayıcılarıyla doğrudan tüm Federasyonu dikkatle değerlendirin. B2B Konuk hesaplarını aşağıdaki yollarla kısıtlamanızı öneririz:

   *  Konuk erişimini gözatma gruplarına ve dizindeki diğer özelliklere sınırlayın. Konukların üye olmadıkları grupları okuyabilme yeteneğini kısıtlamak için dış işbirliği ayarlarını kullanın. 

    *   Azure portal erişimini engelleyin. Nadir olarak gereken özel durumları yapabilirsiniz.  Tüm konukları ve dış kullanıcıları içeren bir koşullu erişim ilkesi oluşturun. Sonra [erişimi engellemek için bir ilke uygulayın](../../role-based-access-control/conditional-access-azure-management.md). 

* **Bağlantısı kesilen ormanlar**: [Azure AD bulut sağlamasını](../cloud-sync/what-is-cloud-sync.md)kullanın. Bu yöntem, bağlantısı kesilen ormanlara bağlanmanızı, ormanlar arası bağlantı veya güvenler oluşturma gereğini ortadan kaldırarak şirket içi ihlalin etkisini genişletmenizi sağlar. 
 
### <a name="limitations-and-tradeoffs"></a>Sınırlamalar ve avantajları

Karma hesapları sağlamak için kullanıldığında, Azure-AD-Cloud-HR sistemi, Active Directory 'den Azure AD 'ye veri akışını tamamlamaya yönelik şirket içi eşitlemeyi kullanır. Eşitleme kesintiye uğrarsa, Azure AD 'de yeni çalışan kayıtları kullanılamayacaktır.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>İşbirliği ve erişim için bulut gruplarını kullanın

Bulut grupları, işbirliklerinizi ve şirket içi altyapınızdan erişiminizi kullanmanıza olanak sağlar.

* **İşbirliği**: modern işbirliği Için Microsoft 365 grupları ve Microsoft ekipleri kullanın. Şirket içi dağıtım listelerine yetki alın ve [dağıtım listelerini Outlook 'ta Microsoft 365 gruplara yükseltin](/office365/admin/manage/upgrade-distribution-lists).

* **Erişim**: Azure AD 'de uygulamalara erişim yetkisi vermek IÇIN Azure AD güvenlik gruplarını veya Microsoft 365 gruplarını kullanın.
* **Office 365 lisansı**: yalnızca bulut gruplarını kullanarak Office 365 ' ye sağlamak için grup tabanlı lisanslama kullanın. Bu yöntem, şirket içi altyapıdan grup üyeliği denetimini ayırır.

Erişim için kullanılan grupların sahipleri, bir şirket içi uzlaşmadan üyeliğe engel olmak için ayrıcalıklı kimlikler olarak düşünülmelidir.
Bir yük devri, grup üyeliğinin şirket içinde veya Microsoft 365 içindeki dinamik grup üyeliğini etkileyebilecek şirket içi özniteliklerin doğrudan işlemesini içerir.

## <a name="manage-devices-from-the-cloud"></a>Cihazları buluttan yönetme


Cihazları güvenli bir şekilde yönetmek için Azure AD yeteneklerini kullanın.

-   **Windows 10 iş Istasyonlarını kullanın**: MDM ILKELERIYLE [Azure AD 'ye katılmış cihazları dağıtın](../devices/azureadjoin-plan.md) . Tam otomatik sağlama deneyimi için [Windows Autopilot](/mem/autopilot/windows-autopilot) etkinleştirin.

    -   Windows 8.1 ve önceki sürümleri çalıştıran makineleri kullanımdan kaldırma.

    -   Sunucu işletim sistemi makinelerini iş istasyonları olarak dağıtmayın.

    -   Tüm cihaz yönetimi iş yükleri için yetki kaynağı olarak [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) kullanın.

-   [**Ayrıcalıklı erişim cihazları dağıtma**](/security/compass/privileged-access-devices#device-roles-and-profiles): Microsoft 365 ve Azure AD 'yi yönetmek için ayrıcalıklı erişim kullanın.

## <a name="workloads-applications-and-resources"></a>İş yükleri, uygulamalar ve kaynaklar 

-   **Şirket içi çoklu oturum açma (SSO) sistemleri** 

    Şirket içi Federasyon ve Web erişimi yönetim altyapısını kullanımdan kaldırma. Uygulamaları Azure AD kullanacak şekilde yapılandırın.  

-   **Modern kimlik doğrulama protokollerini destekleyen SaaS ve iş kolu (LOB) uygulamaları** 

    [SSO Için Azure AD 'Yi kullanın](../manage-apps/what-is-single-sign-on.md). Azure AD 'yi kimlik doğrulaması için kullanmak üzere yapılandırdığınız daha fazla uygulama, şirket içi güvenliğin aşılmasına karşı daha az risk taşır.


* **Eski uygulamalar** 

   * Modern kimlik doğrulamayı desteklemeyen eski uygulamalara kimlik doğrulaması, yetkilendirme ve uzaktan erişim olanağı sağlayabilirsiniz. [Azure AD uygulama ara sunucusu](../manage-apps/application-proxy.md)kullanın. Ayrıca, [güvenli karma erişim ortağı tümleştirmelerini](../manage-apps/secure-hybrid-access.md)kullanarak bunları bir ağ veya uygulama teslim denetleyicisi çözümüyle etkinleştirebilirsiniz.   

   * Modern kimlik doğrulamasını destekleyen bir VPN satıcısı seçin. Kimlik doğrulamasını Azure AD ile tümleştirin. Şirket içi bir uzlaşmaya, VPN 'yi devre dışı bırakarak erişimi devre dışı bırakmak veya engellemek için Azure AD ' yi kullanabilirsiniz.

*  **Uygulama ve iş yükü sunucuları**

   * Gerekli sunucuları hizmet olarak Azure altyapısına (IaaS) geçirilebilecek uygulamalar veya kaynaklar. Active Directory Şirket içi örneklerine güvenmek ve bağımlılığı ayırmak için [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) kullanın. Bu ayrılmayı başarmak için, Azure AD DS için kullanılan sanal ağların kurumsal ağlarla bağlantısı olmadığından emin olun.

   * [Kimlik bilgisi katmanlaması](/security/compass/privileged-access-access-model#ADATM_BM)için yönergeleri izleyin. Uygulama sunucuları genellikle katman 1 varlıkları olarak kabul edilir.

## <a name="conditional-access-policies"></a>Koşullu Erişim ilkeleri

Sinyalleri yorumlamak ve kimlik doğrulama kararları almak için bunları kullanmak için Azure AD koşullu erişimini kullanın. Daha fazla bilgi için bkz. [koşullu erişim dağıtım planı](../conditional-access/plan-conditional-access.md).

* Mümkün olduğunda [eski kimlik doğrulama protokollerini engellemek](../conditional-access/howto-conditional-access-policy-block-legacy.md) Için koşullu erişimi kullanın. Ayrıca, uygulamaya özgü bir yapılandırma kullanarak, uygulama düzeyinde eski kimlik doğrulama protokollerini devre dışı bırakın.

   Daha fazla bilgi için bkz. [eski kimlik doğrulama protokolleri](../fundamentals/auth-sync-overview.md). Veya [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) ve [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant)için belirli ayrıntılara bakın.

* Önerilen [kimlik ve cihaz erişim yapılandırmasını](/microsoft-365/security/office-365-security/identity-access-policies)uygulayın.

* Azure AD 'nin koşullu erişimi içermeyen bir sürümünü kullanıyorsanız [Azure AD güvenlik varsayılanlarını](../fundamentals/concept-fundamentals-security-defaults.md)kullandığınızdan emin olun.

   Azure AD Özellik lisanslama hakkında daha fazla bilgi için bkz. [Azure AD fiyatlandırma Kılavuzu](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitor"></a>İzleyici 

Ortamınızı şirket içi bir uzlaşmadan Microsoft 365 korumak üzere yapılandırdıktan sonra, ortamı proaktif olarak [izleyin](../reports-monitoring/overview-monitoring.md) .
### <a name="scenarios-to-monitor"></a>İzlenecek senaryolar

Kuruluşunuza özgü tüm senaryolara ek olarak aşağıdaki önemli senaryoları izleyin. Örneğin, iş açısından kritik uygulamalarınızın ve kaynaklarınızın erişimini önceden izlemeniz gerekir.

* **Şüpheli etkinlik** 

    Şüpheli etkinlik için tüm [Azure AD risk olaylarını](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) izleyin. [Azure AD kimlik koruması](../identity-protection/overview-identity-protection.md) , Azure Güvenlik Merkezi ile yerel olarak tümleşiktir.

    Konum tabanlı sinyallerin gürültülü algılamalarını önlemek için konum [adlı](../reports-monitoring/quickstart-configure-named-locations.md) ağı tanımlayın. 
*  **Kullanıcı ve varlık davranış analizi (UEBA) uyarıları** 

    Anomali algılama hakkında Öngörüler almak için UEBA 'yi kullanın.
    * Microsoft Cloud App Security (MCAS) [bulutta Ueba](/cloud-app-security/tutorial-ueba)sağlar.

    * Şirket [ıçı UEBA 'Yi Azure Gelişmiş tehdit koruması (ATP) ile tümleştirebilirsiniz](/defender-for-identity/install-step2). MCAS Azure AD Kimlik Koruması sinyalleri okur. 

* **Acil durum erişim hesapları etkinliği** 

    [Acil durum erişim hesapları](../roles/security-emergency-access.md)kullanan tüm erişimi izleyin. Araştırmalar için uyarılar oluşturun. Bu izleme şunları içermelidir: 

   * Oturum açma işlemleri. 

   * Kimlik bilgisi yönetimi. 

   * Grup üyeliklerinde tüm güncelleştirmeler. 

   * Uygulama atamaları. 
* **Ayrıcalıklı rol etkinliği**

    [Azure AD Privileged Identity Management (PIM) tarafından oluşturulan güvenlik uyarılarını](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts)yapılandırın ve gözden geçirin.
    Her kullanıcı doğrudan atandığında uyarı oluşturarak yalnızca, PıM dışında ayrıcalıklı rollerin doğrudan atamasını izleyin.

* **Azure AD kiracı genelinde yapılandırma**

    Kiracı genelindeki yapılandırmalarda yapılan herhangi bir değişiklik sistemde uyarılar üretmelidir. Bu değişiklikler şunları içerir ancak bunlarla sınırlı değildir:

  *  Özel etki alanları güncelleştirildi.  

  * Azure AD B2B, allowlists ve blocklists olarak değişir.

  * Azure AD B2B, izin verilen kimlik sağlayıcılarına (doğrudan Federasyon veya sosyal oturum açma işlemleri aracılığıyla SAML kimlik sağlayıcıları) dönüşür.  

  * Koşullu erişim veya risk ilkesi değişiklikleri. 

* **Uygulama ve hizmet sorumlusu nesneleri**
    
   * Koşullu erişim ilkeleri gerektirebilecek yeni uygulamalar veya hizmet sorumluları. 

   * Hizmet sorumlularına kimlik bilgileri eklendi.
   * Uygulama onayı etkinliği. 

* **Özel roller**
   * Özel rol tanımlarına yönelik güncelleştirmeler. 

   * Yeni oluşturulan özel roller. 

### <a name="log-management"></a>Günlük yönetimi

Tutarlı bir araç kümesini kolaylaştırmak için bir günlük depolama ve bekletme stratejisi, tasarım ve uygulama tanımlayın. Örneğin, Azure Sentinel, ortak sorgular ve araştırma ve adli PlayBook gibi güvenlik bilgilerini ve olay yönetimi (SIEM) sistemlerini düşünebilirsiniz.

* **Azure AD günlükleri**: Tanılama, günlük tutma ve SIEM alma gibi ayarlar için en iyi uygulamaları düzenli olarak takip eden, üretilen günlükleri ve sinyalleri elde edin. 

    Günlük stratejisi aşağıdaki Azure AD günlüklerini içermelidir:
   * Oturum açma etkinliği 

   * Denetim günlükleri 

   * Risk olayları 

    Azure AD, oturum açma etkinlik günlüğü ve denetim günlükleri için [Azure izleyici tümleştirmesi](../reports-monitoring/concept-activity-logs-azure-monitor.md) sağlar. Risk olayları [MICROSOFT Graph API](/graph/api/resources/identityriskevent)aracılığıyla alınabilir. Azure [ad günlüklerini Azure izleyici günlüklerine akışını](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)sağlayabilirsiniz.

* **Karma altyapı işletim sistemi güvenlik günlükleri**: yüzey alanı etkileri nedeniyle, tüm karma kimlik altyapısı işletim sistemi günlüklerinin arşivlenmesi ve Katman 0 bir sistem olarak izlenmesi gerekir. Aşağıdaki öğeleri ekleyin: 

   *  Azure AD Connect. Kimlik eşitlemesini izlemek için [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) dağıtılması gerekir.

   *  Uygulama proxy aracıları 


   * Parola geri yazma aracıları 

   * Parola koruma ağ geçidi makineleri  

   * Azure AD çok faktörlü kimlik doğrulaması RADIUS uzantısı olan ağ ilkesi sunucuları (NPSs) 

## <a name="next-steps"></a>Sonraki adımlar
* [Azure AD kullanarak kimlik ve erişim yönetimine esnekliği oluşturma](resilience-overview.md)

* [Kaynaklara dış erişimi güvenli hale getirme](secure-external-access-resources.md) 
* [Tüm uygulamalarınızı Azure AD ile tümleştirme](five-steps-to-full-application-integration-with-azure-ad.md)