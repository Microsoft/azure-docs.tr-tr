---
title: Azure Kullanıcı asıl adı (UPN) değişikliklerini planlayın ve sorun giderin
description: UPN değişiklikleri için bilinen sorunları ve azaltmaları anlayın
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19e40d135316c1c7cd270d2804fff1f487937685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96858544"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Azure Active Directory Kullanıcı asıl adı değişikliklerini planlayın ve sorun giderin

Kullanıcı asıl adı (UPN), Kullanıcı hesapları için internet iletişim standardı olan bir özniteliktir. UPN, bir UPN öneki (Kullanıcı hesabı adı) ve bir UPN soneki (bir DNS etki alanı adı) içerir. Önek, "" sembolünü kullanarak soneki birleştirir \@ . Örneğin, someone@example.com. Bir UPN, bir Dizin ormanı içindeki tüm güvenlik sorumlusu nesneleri arasında benzersiz olmalıdır. 

**Bu makale, Kullanıcı tanımlayıcısı olarak UPN kullandığınızı varsayar. Bu, UPN değişikliklerinin planlanmasına ve UPN değişikliklerinden kaynaklanan sorunlardan kurtarılmasını ele alınmaktadır.**

> [!NOTE]
> Geliştiriciler için, Kullanıcı ObjectID 'yi, değerlerinin değiştirebildiği UPN veya e-posta adresleri yerine sabit tanımlayıcı olarak kullanmanızı öneririz.


## <a name="learn-about-upns-and-upn-changes"></a>UPN 'ler ve UPN değişiklikleri hakkında bilgi edinin
Oturum açma sayfaları genellikle, gerekli değer aslında UPN 'si olduğunda kullanıcılardan kendi e-posta adreslerini girmesini ister. Bu nedenle, kullanıcıların UPN 'sini her zaman birincil e-posta adresi değişikliklerine göre değiştirdiğinizden emin olmalısınız.

Kullanıcıların birincil e-posta adresleri birçok nedenden dolayı değişebilir:

* Şirket yeniden markalama

* farklı şirket bölümleri 'ne taşınan çalışanlar 

* Birleşmeler ve alımlar

* çalışan adı değişiklikleri

### <a name="types-of-upn-changes"></a>UPN değişikliği türleri

Ön eki, soneki veya her ikisini değiştirerek bir UPN 'yi değiştirebilirsiniz.

* **Ön ek değiştiriliyor**.

   *  Örneğin, bir kişinin adı değiştiyse, hesap adını değiştirebilirsiniz:  
BSimon@contoso.comiçinBJohnson@contoso.com

   * Ayrıca, ön ekler için Kurumsal standarda de değişiklik yapabilirsiniz:  
Bsimon@contoso.comiçinBritta.Simon@contoso.com

* **Son ek değiştiriliyor**. <br>

    Örneğin, bir kişi bölümleri değiştirdiğine göre, etki alanını değiştirebilirsiniz: 

   * Britta.Simon@contoso.com Hedef Britta.Simon@contosolabs.com <br>
     Veya<br>
    * Britta.Simon@corp.contoso.com Hedef Britta.Simon@labs.contoso.com 

Birincil e-posta adresleri her güncelleştirildiği sırada kullanıcıların UPN 'sini değiştirmenizi öneririz.

Active Directory 'den Azure AD 'ye ilk eşitleme sırasında, kullanıcıların e-postalarının UPN 'lerine özdeş olduğundan emin olun.

### <a name="upns-in-active-directory"></a>Active Directory UPN 'leri

Active Directory, varsayılan UPN soneki, Kullanıcı hesabını oluşturduğunuz etki alanının DNS adıdır. Çoğu durumda bu, Internet 'teki kuruluş etki alanı olarak kaydolmanızı sağlayan etki alanı adıdır. Kullanıcı hesabını contoso.com etki alanında oluşturursanız, varsayılan UPN

username@contoso.com

 Ancak, Active Directory etki alanları ve güvenleri kullanarak [daha fazla UPN soneki ekleyebilirsiniz](../fundamentals/add-custom-domain.md) . 

Örneğin, labs.contoso.com eklemek ve kullanıcıların UPN 'lerini ve e-postasını göstermek isteyebilirsiniz. Bunlar daha sonra olur

username@labs.contoso.com.

>[!IMPORTANT]
> [Active Directory soneki değiştiriyorsanız](../fundamentals/add-custom-domain.md), Azure AD 'de eşleşen bir özel etki alanı adının [eklendiğinden ve doğrulandığından](../fundamentals/add-custom-domain.md)emin olmanız gerekir. 

![Doğrulanan etki alanlarının ekran görüntüsü](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Azure Active Directory UPN 'leri

Kullanıcılar, Azure AD 'de userPrincipalName özniteliğinde bulunan değerle oturum açabilirler. 

Azure AD 'yi şirket içi Active Directory birlikte kullandığınızda, Kullanıcı hesapları Azure AD Connect hizmeti kullanılarak eşitlenir. Varsayılan olarak Azure AD Connect Sihirbazı, Azure AD 'de UPN olarak şirket içi Active Directory userPrincipalName özniteliğini kullanır. Özel yüklemede farklı bir özniteliğe değiştirebilirsiniz.

Tek bir kullanıcının Kullanıcı asıl adını (UPN) veya kuruluşunuzun tamamına güncelleştirdiğinizde tanımlı bir işleminiz olması önemlidir. 

Bu belgedeki bilinen sorunları ve geçici çözümleri görüntüleyin.

Kullanıcı hesaplarını Active Directory 'den Azure AD 'ye eşitlerken, Active Directory içindeki UPN 'leri Azure AD 'de doğrulanmış etki alanlarına eşlemekte olduğunuzdan emin olun.

![Azure A etki alanlarını doğrulayan, eşleşen UPN örneklerini gösteren ekran görüntüsü.](./media/howto-troubleshoot-upn-changes/verified-domains.png)

UserPrincipalName özniteliğinin değeri Azure AD 'de doğrulanmış bir etki alanına karşılık gelmiyorsa, eşitleme işlemi soneki varsayılan bir. onmicrosoft.com değeri ile değiştirir.


### <a name="roll-out-bulk-upn-changes"></a>Toplu UPN değişiklikleri toplama

Toplu UPN değişikliklerinde [pilot için en iyi uygulamaları](../fundamentals/active-directory-deployment-plans.md) izleyin. Ayrıca, hızlı bir şekilde çözümlenemeyen sorunlar bulursanız, UPN 'leri geri almaya yönelik test alma planı da vardır. Pilot çalışmaya başladıktan sonra, çeşitli kuruluş rollerine ve bunların belirli uygulama veya cihaz kümelerine sahip küçük Kullanıcı gruplarını hedeflemeye başlayabilirsiniz.

Kullanıcıların bu ilk alt kümesini kullanarak, değişikliğin bir parçası olarak hangi kullanıcıların beklemesi gerektiği konusunda iyi bir fikir verecektir. Bu bilgileri Kullanıcı İletişimlerine ekleyin.

Normal işlemlerin parçası olarak bireysel kullanıcılara ait UPN 'leri değiştirmek için tanımlı bir yordam oluşturun. Bilinen sorunlar ve geçici çözümler hakkında belge içeren, test edilmiş bir yordamın olması önerilir.

Aşağıdaki bölümler, UPN 'ler değiştirildiğinde olası bilinen sorunları ve geçici çözümleri ayrıntılandırır.

## <a name="apps-known-issues-and-workarounds"></a>Uygulamalar, bilinen sorunlar ve geçici çözümler

[Hizmet olarak yazılım (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) ve iş kolu (LOB) uygulamaları genellikle kullanıcıları bulmak ve roller de dahil olmak üzere Kullanıcı profili bilgilerini depolamak için UPN 'leri kullanır. Kullanıcılar uygulamada ilk kez oturum açtığında, UPN değişikliklerinden etkilenebilecek bir kullanıcı profili oluşturmak için [tam zamanında sağlama](../app-provisioning/user-provisioning.md) kullanan uygulamalar.

**Bilinen sorun**<br>
Bir kullanıcının UPN 'sini değiştirmek, Azure AD kullanıcısı ve uygulamada oluşturulan kullanıcı profili arasındaki ilişkiyi bozabilir. Uygulama  [tam zamanında sağlama](../app-provisioning/user-provisioning.md)kullanıyorsa, yeni bir kullanıcı profili oluşturabilir. Bu, uygulama yöneticisinin bu ilişkiyi onarmak için el ile değişiklikler yapmasını gerektirir.

**Geçici çözüm**<br>
[Azure AD otomatik Kullanıcı sağlama](../app-provisioning/user-provisioning.md) , desteklenen bulut uygulamalarında kullanıcı kimliklerinizi otomatik olarak oluşturmanıza, korumanıza ve kaldırmanıza olanak sağlar. Uygulamalarınızda otomatik Kullanıcı sağlamayı yapılandırmak, uygulamalardaki UPN 'leri otomatik olarak güncelleştirir. , UPN değişikliklerinden etkilenmediğinden emin olmak için uygulamaları aşamalı dağıtım kapsamında test edin.
Geliştiricisiyseniz, Azure Active Directory otomatik Kullanıcı sağlamayı etkinleştirmek için [uygulamanıza SCIM desteği eklemeyi](../app-provisioning/use-scim-to-provision-users-and-groups.md) göz önünde bulundurun. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Yönetilen cihazlar bilinen sorunlar ve geçici çözümler

[Cihazlarınızı Azure AD 'ye](../devices/overview.md)getirerek, kullanıcılarınızın bulut ve şirket içi kaynaklarınız genelinde çoklu oturum açma (SSO) ile üretkenliğini en üst düzeye çıkarmış olursunuz.

### <a name="azure-ad-joined-devices"></a>Azure AD’ye katılmış cihazlar

[Azure AD 'ye katılmış](../devices/concept-azure-ad-join.md) cihazlar doğrudan Azure AD 'ye katılır ve kullanıcıların kuruluşun kimliğini kullanarak cihazda oturum açmalarına olanak tanır.

**Bilinen sorunlar** <br>
Kullanıcılar, kimlik doğrulaması için Azure AD 'ye bağlı olan uygulamalarla çoklu oturum açma sorunları yaşayabilir.

**Çözünürlük** <br>
Bu bölümde bahsedilen sorunlar Windows 10 Mayıs 2020 Güncelleştirmesi (2004) üzerinde düzeltildi.

**Geçici çözüm** <br>
UPN değişikliğinin Azure AD ile eşitlenmesi için yeterli zaman bekleyin. Yeni UPN 'nin Azure AD portalında yansıtıldığını doğruladıktan sonra, kullanıcıdan yeni UPN 'si ile oturum açmak için "diğer Kullanıcı" kutucuğunu seçmesini isteyin. [PowerShell](/powershell/module/azuread/get-azureaduser)aracılığıyla da doğrulayabilirsiniz. Yeni UPN 'si ile oturum açtıktan sonra eski UPN başvuruları "iş veya okul erişimi" Windows ayarında görünmeye devam edebilir.

![Doğrulanan etki alanlarının ekran görüntüsü](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>Hibrit Azure AD’ye katılmış cihazlar

[Karma Azure AD 'ye katılmış](../devices/concept-azure-ad-join-hybrid.md) cihazlar Active Directory ve Azure AD 'ye katılır. Ortamınızda şirket içi Active Directory bir parmak izi varsa ve Azure AD tarafından sunulan yeteneklerden yararlanmak istiyorsanız karma Azure AD 'ye katılmayı uygulayabilirsiniz.

**Bilinen sorunlar** 

Windows 10 karma Azure AD 'ye katılmış cihazların beklenmedik şekilde yeniden başlatmalar ve erişim sorunlarıyla karşılaşmaları olasıdır.

Yeni UPN Azure AD 'ye eşitlenmeden önce Windows 'da oturum açtıklarında veya var olan bir Windows oturumunu kullanmaya devam ederseniz, koşullu erişim, kaynaklara erişmek için karma birleştirilmiş cihazların kullanımını zorlamak üzere yapılandırılmışsa, kimlik doğrulaması için Azure AD kullanan uygulamalarla çoklu oturum açma sorunları yaşayabilirler. 

Ayrıca, bir dakika sonra yeniden başlatmaya zorlamak için aşağıdaki ileti görünür. 

"Bilgisayarınız bir dakika içinde otomatik olarak yeniden başlatılacak. Windows bir sorunla karşılaştı ve yeniden başlatılması gerekiyor. Bu iletiyi şimdi kapatmanız ve çalışmalarınızı kaydetmeniz gerekir.

**Çözünürlük** <br>
Bu bölümde bahsedilen sorunlar Windows 10 Mayıs 2020 Güncelleştirmesi (2004) üzerinde düzeltildi.

**Geçici çözüm** 

Cihazın Azure AD 'den katılmamış ve yeniden başlatılması gerekiyor. Yeniden başlattıktan sonra cihaz otomatik olarak Azure AD 'ye katılır ve kullanıcının "diğer Kullanıcı" kutucuğunu seçerek yeni UPN 'yi kullanarak oturum açması gerekir. Bir cihazın Azure AD 'den katılmayı kaldırmak için komut isteminde aşağıdaki komutu çalıştırın:

**dsregcmd/Leave**

Kullanıcının kullanılıyorsa Iş için Windows Hello 'ya [yeniden kaydolması](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) gerekecektir. Windows 7 ve 8,1 cihazları, UPN değişikliklerinden sonra bu sorundan etkilenmez.


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Bilinen sorunları ve geçici çözümleri Microsoft Authenticator

Kuruluşunuz, oturum açmak ve kurumsal uygulamalara ve verilere erişmek için [Microsoft Authenticator uygulamasının](../user-help/user-help-auth-app-overview.md) kullanımını gerektirebilir. Bir Kullanıcı adı uygulamada görünebilse de, Kullanıcı kayıt işlemini tamamlayana kadar hesap doğrulama yöntemi olarak çalışacak şekilde ayarlanamaz.

[Microsoft Authenticator uygulamasının](../user-help/user-help-auth-app-overview.md) dört ana işlevi vardır:

* Bir anında iletme bildirimi veya doğrulama kodu aracılığıyla çok faktörlü kimlik doğrulaması

* [Aracılı kimlik doğrulaması](../develop/msal-android-single-sign-on.md) kullanan uygulamalar için çoklu oturum açma sağlamak üzere IOS ve Android cihazlarda kimlik doğrulama Aracısı işlevi görür

* Intune Uygulama Koruması ve cihaz kaydı/yönetimi gibi diğer özellikler için bir gereksinim olan Azure AD 'de cihaz kaydı (Workplace Join olarak da bilinir)

* MFA ve cihaz kaydı gerektiren telefon oturum açma.

### <a name="multi-factor-authentication-with-android-devices"></a>Android cihazlarla Multi-Factor Authentication

Microsoft Authenticator uygulama bant dışı doğrulama seçeneği sunar. Oturum açma sırasında kullanıcıya otomatik telefon çağrısı veya SMS koymak yerine [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) kullanıcının akıllı telefonundaki veya tabletindeki Microsoft Authenticator uygulamasına bir bildirim gönderir. Kullanıcı, oturum açma işleminin tamamlanabilmesi için Onayla ' ya dokunduktan sonra (veya bir PIN veya biyometri ve "kimlik doğrulaması" ' na dokunduğunda).

**Bilinen sorunlar** 

Bir kullanıcının UPN 'sini değiştirdiğinizde, eski UPN hala Kullanıcı hesabında görüntülenir ve bir bildirim alınmayabilir. [Doğrulama kodları](../user-help/user-help-auth-app-faq.md) çalışmaya devam eder.

**Geçici çözüm**

Bir bildirim alındığında, kullanıcıdan bildirimi yok saymasını isteyin, Authenticator uygulamasını açın, "bildirimleri denetle" seçeneğine dokunun ve MFA isteğini onaylayın. Bundan sonra hesapta görüntülenecek UPN güncelleştirilir. Güncelleştirilmiş UPN 'nin yeni bir hesap olarak görüntülenebileceğini, bunun nedeni kullanılan diğer kimlik doğrulayıcı işlevlerinden kaynaklanıyor olduğunu aklınızda olun. Daha fazla bilgi için bu makaledeki diğer bilinen sorunlara bakın.

### <a name="brokered-authentication"></a>Aracılı kimlik doğrulaması

Android ve Microsoft Authenticator etkinleştir gibi iOS aracılarından:

* Çoklu oturum açma (SSO)-kullanıcılarınızın her uygulamada oturum açması gerekmez.

* Cihaz kimliği-Aracı, çalışma alanına katılmış olduğunda cihazda oluşturulan cihaz sertifikasına erişir.

* Uygulama tanımlama doğrulaması-bir uygulama aracıyı çağırdığında, yeniden yönlendirme URL 'sini geçirir ve aracı tarafından doğrulanır.

Ayrıca, uygulamaların [koşullu erişim](../conditional-access/index.yml)gibi daha gelişmiş özelliklere katılmasına ve [Microsoft Intune senaryoları](../develop/msal-net-use-brokers-with-xamarin-apps.md)desteklediğinden de olanak tanır.

**Bilinen sorunlar**<br>
Kullanıcıya, uygulama tarafından geçirilen login_hint ile aracıda depolanan UPN arasında uyuşmazlık olduğundan, aracı destekli oturum açma kullanan yeni uygulamalarda daha etkileşimli kimlik doğrulama istemleri sunulur.

**Geçici çözüm** <br> Kullanıcının hesabı Microsoft Authenticator ' dan el ile kaldırması ve aracı yardımlı bir uygulamadan yeni bir oturum açma başlatması gerekir. Hesap ilk kimlik doğrulamasından sonra otomatik olarak eklenir.

### <a name="device-registration"></a>Cihaz kaydı

Microsoft Authenticator uygulaması, cihazın Azure AD 'ye kaydedilmesinden sorumludur. Cihaz kaydı, cihazın Azure AD 'de kimlik doğrulamasına izin verir ve aşağıdaki senaryolar için bir gereksinimdir:

* Intune Uygulama Koruması

* Intune cihaz kaydı

* Telefonla oturum açma

**Bilinen sorunlar**<br>
UPN 'yi değiştirdiğinizde Microsoft Authenticator uygulamasında yeni UPN 'ye sahip yeni bir hesap görüntülenir, eski UPN 'ye sahip hesap hala listelenmeye devam eder. Ayrıca, eski UPN, uygulama ayarlarındaki cihaz kaydı bölümünde görüntülenir. Cihaz kaydı veya bağımlı senaryolar normal işlevselliğinde değişiklik yapılmaz.

**Geçici çözüm** <br> Microsoft Authenticator uygulamasındaki eski UPN 'nin tüm başvurularını kaldırmak için, kullanıcıya Microsoft Authenticator hem eski hem de yeni hesapları el ile kaldırmasını isteyin, MFA için yeniden kaydolun ve cihaza yeniden katılın.

### <a name="phone-sign-in"></a>Telefonla oturum açma

Telefonla oturum açma, kullanıcıların Azure AD 'de parola olmadan oturum açmasına olanak tanır. Telefonla oturum açmayı etkinleştirmek için, kullanıcının kimlik doğrulayıcı uygulamasını kullanarak MFA 'ya kaydolması ve sonra doğrudan kimlik doğrulayıcı üzerinde telefon oturum açmasını etkinleştirmesi gerekir. Yapılandırma kapsamında cihaz Azure AD 'ye kaydolur.

**Bilinen sorunlar** <br>
Kullanıcılar, herhangi bir bildirim almadıkları için telefonla oturum açmayı kullanamaz. Kullanıcı bildirimlerin denetimine dokunursa bir hata alırlar.

**Geçici çözüm**<br>
Kullanıcının telefon oturumu açmak için etkinleştirilen hesapta açılan menüyü seçmesini ve telefonla oturum açmayı devre dışı bırakması gerekir. İsterseniz, telefonla oturum açma yeniden etkinleştirilebilir.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Güvenlik anahtarı (FIDO2) bilinen sorunlar ve geçici çözümler

**Bilinen sorunlar** <br>
Aynı anahtara birden çok Kullanıcı kaydedildiğinde, oturum açma ekranında eski UPN 'nin görüntülendiği bir hesap seçim sayfası gösterilir. Güvenlik anahtarlarını kullanan oturum açma işlemleri, UPN değişikliklerinden etkilenmez.  

**Geçici çözüm**<br>
Eski UPN başvurularını kaldırmak için, kullanıcıların [güvenlik anahtarını sıfırlaması ve yeniden kaydedilmesi](../authentication/howto-authentication-passwordless-security-key.md#known-issues)gerekir.

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive bilinen sorunlar ve geçici çözümler

OneDrive kullanıcılarının, UPN değişikliklerinden sonra sorunlarla karşılaşması bilinmektedir. Daha fazla bilgi için bkz. [UPN değişikliklerinin ONEDRIVE URL 'sini ve OneDrive özelliklerini nasıl etkilediği](/onedrive/upn-changes).

## <a name="next-steps"></a>Sonraki adımlar

Şu kaynaklara bakın:
* [Azure AD Connect: tasarım kavramları](./plan-connect-design-concepts.md)

* [Azure AD UserPrincipalName popülasyonu](./plan-connect-userprincipalname.md)

* [Microsoft Identity platform KIMLIĞI belirteçleri](../develop/id-tokens.md)
