---
title: Birincil yenileme belirteci (PRT) ve Azure AD-Azure Active Directory
description: Rolü nedir ve Azure Active Directory birincil yenileme belirtecini (PRT) nasıl yönetebiliriz?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46cc8ef1158c02190f905cbe8eb1d12ea7be50a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644944"
---
# <a name="what-is-a-primary-refresh-token"></a>Birincil Yenileme Belirteci nedir?

Birincil yenileme belirteci (PRT), Windows 10, Windows Server 2016 ve sonraki sürümlerde, iOS ve Android cihazlarda Azure AD kimlik doğrulamasının temel yapıtıdır. Bu cihazlarda kullanılan uygulamalarda çoklu oturum açma (SSO) özelliğini etkinleştirmek için, Microsoft ilk taraf belirteç aracılarına özel olarak verilen bir JSON Web Token (JWT). Bu makalede, Windows 10 cihazlarında bir PRT 'nin nasıl verildiği, kullanıldığı ve korunduğu hakkında ayrıntılı bilgi sağlıyoruz.

Bu makalede, Azure AD 'de mevcut olan farklı cihaz durumlarını ve çoklu oturum açma 'nın Windows 10 ' da nasıl çalıştığını anladığınızı varsaymaktadır. Azure AD 'deki cihazlar hakkında daha fazla bilgi için [Azure Active Directory cihaz yönetimi](overview.md) nedir makalesine bakın.

## <a name="key-terminology-and-components"></a>Anahtar terminolojisi ve bileşenleri

Aşağıdaki Windows bileşenleri, bir PRT isteğinde ve kullanarak bir anahtar rol oynar:

* **Bulut kimlik doğrulama sağlayıcısı** (cloudap): cloudap, Windows oturum açma için modern kimlik doğrulama sağlayıcısıdır ve kullanıcıların bir Windows 10 cihazına oturum açmasını doğrular. CloudAP, kimlik sağlayıcılarının kimlik bilgilerini kullanarak Windows için kimlik doğrulamasını etkinleştirmek üzere kimlik sağlayıcılarının üzerinde oluşturabileceğiniz bir eklenti çerçevesi sağlar.
* **Web hesabı Yöneticisi** (WAM): WAM, Windows 10 cihazlarında varsayılan belirteç aracısıdır. WAM ayrıca kimlik sağlayıcılarının bu kimlik sağlayıcısına bağlı olan uygulamalarına yönelik SSO 'yu oluşturup etkinleştirebilmeleri için bir eklenti çerçevesi de sağlar.
* **Azure AD cloudap eklentisi**: Windows oturum açma SıRASıNDA Azure AD ile Kullanıcı kimlik bilgilerini doğrulayan, cloudap çerçevesi üzerine Inşa EDILEN Azure AD 'ye özgü bir eklenti.
* **Azure AD WAM eklentisi**:, kimlik doğrulaması IÇIN Azure AD 'yi kullanan uygulamalar için SSO 'yu sağlayan, WAM çerçevesi üzerinde oluşturulmuş BIR Azure AD özel eklentisi.
* **Dsreg**: Windows 10 ' da, tüm cihaz durumları için cihaz kayıt işlemini Işleyen BIR Azure AD 'ye özgü bileşen.
* **Güvenilir Platform Modülü** (TPM): TPM, Kullanıcı ve cihaz gizli dizileri için donanım tabanlı güvenlik işlevleri sağlayan bir cihaza yerleşik bir donanım bileşenidir. [Güvenilir Platform Modülü teknolojisine genel bakış](/windows/security/information-protection/tpm/trusted-platform-module-overview)makalesinde daha fazla ayrıntı bulabilirsiniz.

## <a name="what-does-the-prt-contain"></a>PRT neleri içerir?

Bir PRT, genellikle herhangi bir Azure AD yenileme belirtecinde bulunan talepleri içerir. Ayrıca, PRT 'ye dahil edilen cihaza özgü bazı talepler vardır. Bunlar şu şekildedir:

* **CIHAZ kimliği**: belirli bir cihazdaki bir kullanıcıya bir prt verilir. Cihaz KIMLIĞI talebi, `deviceID` PRT 'nin kullanıcıya verildiği aygıtı belirler. Bu talep daha sonra PRT aracılığıyla elde edilen belirteçlere verilir. Cihaz KIMLIĞI talebi, cihaz durumu veya uyumluluğuna göre koşullu erişim için Yetkilendirmeyi belirlemede kullanılır.
* **Oturum anahtarı**: oturum anahtarı, prt 'nin bir parçası olarak VERILEN Azure AD kimlik doğrulama hizmeti tarafından oluşturulan şifreli bir simetrik anahtardır. Oturum anahtarı, diğer uygulamalar için belirteçleri elde etmek üzere bir PRT kullanıldığında, sahip olma kanıtı olarak davranır.

### <a name="can-i-see-whats-in-a-prt"></a>Bir PRT 'de ne olduğunu görebilir miyim?

PRT, içeriği hiçbir istemci bileşeni tarafından bilinen Azure AD 'den gönderilen donuk bir BLOB ' tur. Bir PRT içinde nelerin olduğunu göremezsiniz.

## <a name="how-is-a-prt-issued"></a>PRT nasıl verilir?

Cihaz kaydı, Azure AD 'de cihaz tabanlı kimlik doğrulaması için bir önkoşuldur. Yalnızca kayıtlı cihazlarda kullanıcılara bir PRT verilir. Cihaz kaydı hakkında daha ayrıntılı bilgi için, [iş Için Windows Hello ve cihaz kaydı](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)makalesine bakın. Cihaz kaydı sırasında dsreg bileşeni iki şifreleme anahtarı çifti kümesi oluşturur:

* Cihaz anahtarı (dkpub/dkprıv)
* Aktarım anahtarı (tkpub/tkprıv)

Cihazın geçerli ve çalışır durumda bir TPM 'si varsa özel anahtarlar cihazın TPM 'ye bağlanır, ancak ortak anahtarlar cihaz kayıt işlemi sırasında Azure AD 'ye gönderilir. Bu anahtarlar, PRT istekleri sırasında cihaz durumunu doğrulamak için kullanılır.

PRT, bir Windows 10 cihazında Kullanıcı kimlik doğrulaması sırasında iki senaryoda verilir:

* **Azure AD 'ye katılmış** veya **karma Azure AD 'ye katılmış**: bir Kullanıcı, kuruluş kimlik bilgileriyle oturum açtığında Windows oturum açma sırasında bir prt verilir. Windows 10 tarafından desteklenen tüm kimlik bilgileriyle (örneğin, parola ve Iş için Windows Hello) bir PRT verilir. Bu senaryoda, PRT için birincil yetkiliniz olan Azure AD CloudAP eklentisi.
* **Azure AD kayıtlı cihazı**: bir Kullanıcı Windows 10 cihazına ikincil iş hesabı eklediğinde bir prt verilir. Kullanıcılar, Windows 10 ' a bir hesabı iki farklı şekilde ekleyebilir.  
   * Bir uygulamada oturum açtıktan sonra bu **cihaz isteminde bu hesabı her yerde kullan** hesabı ekleme (örneğin, Outlook)
   * Ayarlar hesaplarından hesap ekleme   >    >  **iş veya okul**  >  **Connect 'e** erişin

Azure AD kayıtlı cihaz senaryolarında, Azure AD WAM eklentisi, bu Azure AD hesabında Windows oturum açma işlemi gerçekleşmediği için PRT 'in birincil yetkilisindir.

> [!NOTE]
> 3. taraf kimlik sağlayıcılarının Windows 10 cihazlarında PRT verilmesini etkinleştirmek için WS-Trust protokolünü desteklemesi gerekir. WS-Trust olmadan, karma Azure AD 'ye katılmış veya Azure AD 'ye katılmış cihazlarda kullanıcılara PRT verilemez. Yalnızca ADFS 'de usernamemixed uç noktalar gereklidir. ADFS/Services/Trust/2005/windowstransport ve ADFS/Services/Trust/13/windowstransport, yalnızca intranet 'e yönelik uç noktalar olarak etkinleştirilmelidir ve Web uygulaması ara sunucusu aracılığıyla extranet 'e yönelik uç noktalar olarak **gösterilmemelidir** .

## <a name="what-is-the-lifetime-of-a-prt"></a>Bir PRT 'in yaşam süresi nedir?

Verildikten sonra, bir PRT 14 gün boyunca geçerlidir ve Kullanıcı cihazı etkin bir şekilde kullandığı sürece sürekli olarak yenilenir.  

## <a name="how-is-a-prt-used"></a>Bir PRT nasıl kullanılır?

Bir PRT, Windows 'da iki anahtar bileşeni tarafından kullanılır:

* **Azure AD CloudAP eklentisi**: Windows oturum açma sırasında Azure AD cloudap eklentisi, Kullanıcı tarafından belirtilen kimlik bilgilerini kullanarak Azure AD 'den bir prt ister. Ayrıca, kullanıcının bir internet bağlantısına erişimi olmadığında önbelleğe alınmış oturum açma özelliğini etkinleştirmek için PRT 'yi önbelleğe alır.
* **Azure AD WAM eklentisi**: kullanıcılar uygulamalara erişmeye çalıştığında, Azure AD WAM eklentisi Windows 10 ' da SSO 'yu etkinleştirmek için prt 'yi kullanır. Azure AD WAM eklentisi, belirteç istekleri için WAM 'yi kullanan uygulamalar için yenileme ve erişim belirteçleri istemek üzere PRT 'yi kullanır. Ayrıca, tarayıcılarda SSO 'yu tarayıcı isteklerine ekleme tarafından da sunar. Windows 10 ' da tarayıcı SSO 'SU, Microsoft Edge (yerel olarak) ve Chrome ( [Windows 10 hesapları](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en) veya [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en) uzantıları aracılığıyla) üzerinde desteklenir.

## <a name="how-is-a-prt-renewed"></a>PRT nasıl yenilenir?

Bir PRT iki farklı yöntemde yenilenir:

* **Her 4 saatte bir Azure AD CloudAP eklentisi**: cloudap eklentisi, Windows oturum açma sırasında her 4 saatte BIR, prt 'yi yeniler. Bu süre boyunca kullanıcının internet bağlantısı yoksa, CloudAP eklentisi cihaz internet 'e bağlandıktan sonra PRT 'yi yeniler.
* **Uygulama belirteci istekleri sırasında Azure AD WAM eklentisi**: WAM eklentisi, uygulamalar için sessiz belirteç Isteklerini etkinleştirerek Windows 10 cihazlarında SSO 'yu etkinleştirir. WAM eklentisi, bu belirteç istekleri sırasında PRT 'yi iki farklı şekilde yenileyebilir:
   * Uygulama, erişim belirteci için bir WAM sessizce ister ancak bu uygulama için kullanılabilir yenileme belirteci yok. Bu durumda, WAM, uygulama için bir belirteç istemek üzere PRT 'yi kullanır ve yanıtta yeni bir PRT 'yi geri alır.
   * Uygulama, erişim belirteci için WAM ister ancak PRT geçersiz ya da Azure AD ek yetkilendirme gerektirir (örneğin, Azure AD Multi-Factor Authentication). Bu senaryoda, WAM kullanıcının yeniden kimlik doğrulamasını veya ek doğrulama sağlamasını gerektiren etkileşimli bir oturum açma işlemini başlatır ve başarılı bir kimlik doğrulamasında yeni bir PRT verilir.

Bir ADFS ortamında, PRT 'yi yenilemek için etki alanı denetleyicisine doğrudan görüş satırı gerekli değildir. PRT yenilemesi, WS-Trust protokolü kullanılarak proxy üzerinde yalnızca/ADFS/Services/Trust/2005/usernamemixed ve/ADFS/Services/Trust/13/usernamemixed endpoints gerektirir.

Windows Aktarım uç noktaları, yalnızca parola değiştirildiğinde, PRT yenileme için değil, parola kimlik doğrulaması için gereklidir.

### <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular

* Bir PRT yalnızca yerel uygulama kimlik doğrulaması sırasında verilir ve yenilenir. Bir tarayıcı oturumu sırasında bir PRT yenilenmez veya verilmez.
* Azure AD 'ye katılmış ve karma Azure AD 'ye katılmış cihazlarda, CloudAP eklentisi bir PRT için birincil yetkildir. Bir PRT, WAM tabanlı bir belirteç isteği sırasında yenilenirse, PRT, kabul edilmeden önce Azure AD ile PRT 'in geçerliliğini doğrulayan CloudAP eklentisine geri gönderilir.

## <a name="how-is-the-prt-protected"></a>PRT nasıl korunuyor?

Bir PRT, kullanıcının oturum açladığı cihaza bağlayarak korunur. Azure AD ve Windows 10 aşağıdaki yöntemlerle PRT korumasını etkinleştirir:

* **İlk oturum açma sırasında**: ilk oturum açma sırasında, istekler cihaz kaydı sırasında oluşturulan şifreleme cihaz anahtarı kullanılarak imzalanarak BIR prt verilir. Geçerli ve çalışır bir TPM 'ye sahip bir cihazda, cihaz anahtarı, herhangi bir kötü amaçlı erişimi öngören TPM tarafından korunur. Karşılık gelen cihaz anahtarı imzası onaylanamamışsa, bir PRT verilmez.
* **Belirteç istekleri ve yenileme sırasında**: BIR prt verildiğinde, Azure AD Ayrıca cihaza şifreli bir oturum anahtarı da yayınlar. Oluşturulan ve Azure AD 'ye gönderilen ortak Aktarım anahtarı (tkpub) ile birlikte, cihaz kaydının bir parçası olarak şifrelenir. Bu oturum anahtarı yalnızca TPM tarafından güvenli hale getirilmiş özel Aktarım anahtarı (tkprprıv) tarafından şifresi çözülür. Oturum anahtarı, Azure AD 'ye gönderilen tüm istekler için sahip olma (POP) anahtarıdır.  Oturum anahtarı da TPM tarafından korunur ve başka bir işletim sistemi bileşeni erişemez. Belirteç istekleri veya PRT yenileme istekleri bu oturum anahtarı tarafından TPM aracılığıyla güvenli bir şekilde imzalanır ve bu nedenle üzerinde değişiklik yapılamaz. Azure AD, cihazdaki tüm istekleri, karşılık gelen oturum anahtarı tarafından imzalanmamış olan cihazdan geçersiz kılar.

Bu anahtarların TPM ile güvenli hale getirilmesi sayesinde, anahtarları çalmaya çalışan kötü amaçlı aktörlerden PRT güvenliğini geliştirdik ve PRT 'yi yeniden oynadık.  Bu nedenle, TPM kullanmak Azure AD 'ye katılmış, karma Azure AD 'ye katılmış ve Azure AD 'ye kayıtlı cihazların kimlik bilgileri hırsızlığına karşı güvenliğini büyük ölçüde geliştirir. Performans ve güvenilirlik için TPM 2,0, Windows 10 ' da tüm Azure AD cihaz kaydı senaryoları için önerilen sürümdür. Windows 10, 1903 güncelleştirmesi başlatılıyor, Azure AD, güvenilirlik sorunları nedeniyle yukarıdaki anahtarların herhangi biri için TPM 1,2 kullanmaz. 

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Uygulama belirteçleri ve tarayıcı tanımlama bilgileri nasıl korunur?

**Uygulama belirteçleri**: BIR uygulama WAM aracılığıyla belirteç Istediğinde, Azure AD bir yenileme belirteci ve erişim belirteci yayınlar. Ancak, WAM yalnızca uygulamaya erişim belirtecini döndürür ve kullanıcının veri koruma uygulaması programlama arabirimi (DPAPI) anahtarıyla şifreleyerek yenileme belirtecinin önbelleğinde güvenliğini sağlar. WAM, daha fazla erişim belirteci vermek için istekleri oturum anahtarıyla imzalayarak yenileme belirtecini kullanır. DPAPI anahtarı, Azure AD 'de bir Azure AD tabanlı simetrik anahtarla korunmaktadır. Cihazın, Kullanıcı profilinin DPAPI anahtarıyla şifresinin çözülmesi gerektiğinde Azure AD, oturum anahtarı tarafından şifrelenen DPAPI anahtarını sağlar, bu, şifre çözme için TPM isteğinde bulunan CloudAP eklentisini ister. Bu işlevsellik yenileme belirteçlerinin güvenliğini sağlama ve kendi koruma mekanizmalarını uygulayan uygulamaların yapılmasını önler.  

**Tarayıcı tanımlama bilgileri**: Windows 10 ' da, Azure AD, Internet Explorer ve Microsoft Edge 'de tarayıcı SSO 'Yu, Windows 10 hesapları uzantısı aracılığıyla yerel olarak veya Google Chrome 'da destekler. Güvenlik yalnızca tanımlama bilgilerini korumak için değil, tanımlama bilgilerinin gönderildiği uç noktalar için de tasarlanmıştır. Tarayıcı tanımlama bilgileri, tanımlama bilgilerini imzalamak ve korumak için oturum anahtarından yararlanarak bir PRT ile aynı şekilde korunur.

Bir Kullanıcı bir tarayıcı etkileşimi başlattığında, tarayıcı (veya uzantısı) bir COM Native istemci konağını çağırır. Yerel istemci Konağı, sayfanın izin verilen etki alanlarından birinden olmasını sağlar. Tarayıcı, bir nonce dahil olmak üzere yerel istemci ana bilgisayarına başka parametreler gönderebilir, ancak yerel istemci ana bilgisayarı ana bilgisayar adının doğrulanmasını garanti eder. Yerel istemci ana bilgisayarı, CloudAP eklentisi 'nden bir PRT tanımlama bilgisi ister ve bunu TPM korumalı oturum anahtarı ile oluşturur ve imzalar. PRT-Cookie, oturum anahtarı tarafından imzalandığından, ' yi ile çalışmak çok zordur. Bu PRT tanımlama bilgisi, kaynak olduğunu doğrulamak üzere Azure AD 'nin istek başlığına dahildir. Chrome tarayıcısını kullanıyorsanız, yalnızca yerel istemci ana bilgisayarının bildiriminde açıkça tanımlanmış olan uzantı, bu istekleri yapmak için rastgele uzantıların yapılmasını engellemeyi çağırabilir. Azure AD, PRT tanımlama bilgisini doğruladıktan sonra tarayıcıya bir oturum tanımlama bilgisi yayınlar. Bu oturum tanımlama bilgisi Ayrıca, bir PRT ile verilen aynı oturum anahtarını içerir. Sonraki istekler sırasında oturum anahtarı, tanımlama bilgisini cihaza etkin bir şekilde bağlamaya ve başka bir yerden yeniden çalınmalarına izin vermez.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Bir dut bir MFA talebi alır mi?

Bir PRT, belirli senaryolarda Multi-Factor Authentication (MFA) talebi alabilir. MFA tabanlı bir PRT, uygulamalar için belirteçleri istemek üzere kullanıldığında, MFA talebi bu uygulama belirteçlerine aktarılır. Bu işlevsellik, kullanıcılara gereken her uygulama için MFA sınamasını önleyecek şekilde kullanıcılara sorunsuz bir deneyim sağlar. Bir PRT, MFA talebini aşağıdaki yollarla alabilir:

* **İş Için Windows Hello Ile oturum açın**: iş Için Windows Hello parolaları değiştirir ve güçlü iki öğeli kimlik doğrulama sağlamak için şifreleme anahtarları kullanır. Iş için Windows Hello, cihazdaki bir kullanıcıya özeldir ve kendisi MFA 'nın sağlamasını gerektirir. Bir Kullanıcı Iş için Windows Hello ile oturum açtığında, kullanıcının PRT 'si bir MFA talebi alır. Bu senaryo, akıllı kart kimlik doğrulaması ADFS 'den bir MFA talebi üretirse akıllı kartlar ile oturum açan kullanıcılar için de geçerlidir.
   * Iş için Windows Hello çok faktörlü kimlik doğrulaması olarak kabul edildiği için, PRT 'in kendisi yenilendiğinde MFA talebi güncelleştirilir, böylece kullanıcılar Iş için WIndows Hello ile oturum açtığında MFA süresi sürekli olarak genişletilir.
* **WAM etkileşimli oturum açma sırasında MFA**: WAM aracılığıyla bir belirteç isteği sırasında, bir kullanıcının uygulamaya erışmek için MFA yapması gerekiyorsa, bu etkileşim sırasında yenilenen prt bir MFA talebine göre belirlenir.
   * Bu durumda, MFA talebi sürekli olarak güncellenmez, bu nedenle MFA süresi dizindeki yaşam süresine göre belirlenir.
   * Bir uygulamaya erişim için önceki mevcut PRT ve RT kullanıldığında, PRT ve RT ilk kimlik doğrulaması kanıtı olarak kabul edilir. İkinci bir kanıt ve kesin bir MFA talebi ile birlikte yeni bir de gerekecektir. Bu, ayrıca yeni bir PRT ve RT de yayımlayacak.
* **Cihaz kaydı sırasında MFA**: bir yönetici, [CIHAZLARı kaydetmek için MFA 'Yı GEREKTIRMEK](device-management-azure-portal.md#configure-device-settings)üzere Azure AD 'de cihaz ayarlarını yapılandırdıysa, kullanıcının kaydı tamamlaması için MFA yapması gerekir. Bu işlem sırasında, kullanıcıya verilen PRT 'nin kayıt sırasında elde edilen MFA talebi vardır. Bu özellik yalnızca, bu cihazda oturum açan diğer kullanıcılara değil, yalnızca JOIN işlemini yapan kullanıcı için geçerlidir.
   * WAM etkileşimli oturum açma işlemine benzer şekilde, MFA talebi sürekli olarak güncellenmez, bu nedenle MFA süresi dizindeki belirlenen yaşam süresine göre belirlenir.

Windows 10, her kimlik bilgisi için bölümlenmiş bir PRTs listesini tutar. Bu nedenle, her Iş için Windows Hello, parola veya akıllı kart için bir PRT vardır. Bu bölümlendirme, MFA taleplerinin, kullanılan kimlik bilgileri temel alınarak yalıtılmasını sağlar ve belirteç istekleri sırasında karıştırılır.

## <a name="how-is-a-prt-invalidated"></a>PRT nasıl geçersiz kılınır?

Aşağıdaki senaryolarda bir PRT geçersiz kılınır:

* **Geçersiz Kullanıcı**: bir Kullanıcı Azure AD 'de silinirse veya devre dışı bırakılmışsa, prt özellikleri geçersiz kılınır ve uygulamalar için belirteçleri almak üzere kullanılamaz. Silinen veya devre dışı bırakılmış bir Kullanıcı önceden bir cihazda daha önce oturum açmışsa, bu oturum açma işlemi, CloudAP 'in geçersiz durumlarını bilene kadar günlüğe kaydedilir. CloudAP kullanıcının geçersiz olduğunu belirlerse, sonraki oturum açmaları engeller. Geçersiz bir kullanıcının kimlik bilgileri önbelleğe alınmamış yeni cihazlarda oturum açması otomatik olarak engellenir.
* **Geçersiz cihaz**: Azure AD 'de bir cihaz silinirse veya devre dışı bırakılırsa, bu cihazda elde edilen prt geçersiz kılınır ve diğer uygulamalar için belirteçleri elde etmek için kullanılamaz. Bir kullanıcı zaten geçersiz bir cihazda oturum açmışsa, bu işlemi yapmaya devam edebilirler. Ancak cihazdaki tüm belirteçler geçersiz kılınır ve kullanıcının bu cihazdaki herhangi bir kaynak için SSO 'SU yoktur.
* **Parola değiştirme**: bir Kullanıcı parolasını değiştirdikten sonra, önceki parolayla elde edilen PRT, Azure AD tarafından geçersiz kılınır. Parola değişikliği, kullanıcının yeni bir Pry alma sonucu elde ediyor. Bu geçersiz kılma iki farklı şekilde olabilir:
   * Kullanıcı Windows 'da yeni parolasıyla oturum açarsa, CloudAP eski PRT 'yi atar ve yeni parolalarıyla yeni bir PRT vermek üzere Azure AD istekleri ister. Kullanıcının internet bağlantısı yoksa, yeni parola doğrulanamaz, Windows kullanıcının eski parolasını girmesini gerektirebilir.
   * Bir Kullanıcı eski parolasıyla oturum açtıysa veya Windows 'da oturum açtıktan sonra parolalarını değiştirdiyseniz, WAM tabanlı tüm Belirteç istekleri için eski PRT kullanılır. Bu senaryoda, kullanıcıdan WAM belirteci isteği sırasında yeniden kimlik doğrulaması yapması istenir ve yeni bir PRT verilir.
* **TPM sorunları**: bazen bır cihazın TPM 'SI, TPM tarafından güvenliği sağlanan anahtarların ınerişilebilirliğine göre lider veya başarısız olabilir. Bu durumda, cihaz, şifreleme anahtarlarını kanıtlayaamadığı için, mevcut bir PRT 'yi kullanarak bir PRT veya belirteç isteme yeteneğine sahip değildir. Sonuç olarak, mevcut PRT 'ları Azure AD tarafından geçersiz kılınır. Windows 10 bir hata algıladığında, cihazı yeni şifreleme anahtarlarıyla yeniden kaydetmek için bir kurtarma akışı başlatır. Karma Azure AD JOIN ile tıpkı ilk kayıt gibi, kurtarma Kullanıcı girişi olmadan sessizce gerçekleşir. Azure AD 'ye katılmış veya Azure AD 'ye kayıtlı cihazlarda, kurtarmanın cihazda yönetici ayrıcalıklarına sahip bir kullanıcı tarafından gerçekleştirilmesi gerekir. Bu senaryoda, kurtarma akışı kullanıcının cihazı başarıyla kurtarmasını yönlendiren bir Windows istemi tarafından başlatılır.

## <a name="detailed-flows"></a>Ayrıntılı akışlar

Aşağıdaki diyagramlarda, bir uygulama için erişim belirteci istemek üzere bir PRT verme, yenileme ve kullanma konusunda temel alınan Ayrıntılar gösterilmektedir. Ayrıca, bu adımlar, bu etkileşimler sırasında belirtilen güvenlik mekanizmalarının nasıl uygulandığını de açıklamaktadır.

### <a name="prt-issuance-during-first-sign-in"></a>İlk oturum açma sırasında PRT verme

![İlk oturum açma sırasında ayrıntılı akışta PRT verme](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Azure AD 'ye katılmış cihazlarda bu Exchange, kullanıcının Windows 'da oturum açmasını sağlamak için zaman uyumlu olarak gerçekleştirilir. Karma Azure AD 'ye katılmış cihazlarda, şirket içi Active Directory birincil yetkilisdir. Bu nedenle, Kullanıcı oturum açmak için bir TGT elde edene kadar bekler, ancak PRT verme işlemi zaman uyumsuz olur. Bu senaryo, oturum açma Azure AD kimlik bilgilerini kullanmadığından Azure AD 'ye kayıtlı cihazlar için geçerlidir.

| Adım | Description |
| :---: | --- |
| A | Kullanıcı oturum açma kullanıcı arabirimindeki parolasını girer. LogonUI bir kimlik doğrulama arabelleğindeki kimlik bilgilerini LSA 'ya geçirir, bu da bunu sırayla CloudAP öğesine geçirir. CloudAP bu isteği CloudAP eklentisine iletir. |
| B | CloudAP eklentisi kullanıcının kimlik sağlayıcısını tanımlamak için bir bölge bulma isteği başlatır. Kullanıcının kiracısında bir Federasyon sağlayıcısı kurulumu varsa, Azure AD Federasyon sağlayıcısının meta veri değişimi uç noktası (MEX) uç noktasını döndürür. Aksi takdirde Azure AD, kullanıcının Azure AD ile kimlik doğrulaması yapabileceğini belirten bir kullanıcı tarafından yönetilmediğini döndürür. |
| C | Kullanıcı yönetilmiyorsa, CloudAP Azure AD 'den nonce alır. Kullanıcı federe ise, CloudAP eklentisi Federasyon sağlayıcısından kullanıcının kimlik bilgileriyle bir SAML belirteci ister. Bu, SAML belirtecini aldıktan sonra Azure AD 'den bir kerelik anahtar ister. |
| D | CloudAP eklentisi kullanıcının kimlik bilgileri, nonce ve aracı kapsamıyla kimlik doğrulama isteği oluşturur, isteği cihaz anahtarıyla imzalar (dkprıv) ve Azure AD 'ye gönderir. Federasyon ortamında, CloudAP eklentisi kullanıcının kimlik bilgileri yerine Federasyon sağlayıcısı tarafından döndürülen SAML belirtecini kullanır. |
| E | Azure AD Kullanıcı kimlik bilgilerini, nonce ve cihaz imzasını doğrular, cihazın kiracıda geçerli olduğunu doğrular ve şifreli PRT 'yi yayınlar. Ayrıca, Azure AD, Aktarım anahtarı (tkpub) kullanılarak Azure AD tarafından şifrelenen oturum anahtarı adlı bir simetrik anahtar de yayınlar. Ayrıca, oturum anahtarı PRT 'ye de katıştırılır. Bu oturum anahtarı, PRT ile sonraki istekler için birlikte bulunma (PoP) anahtarı olarak davranır. |
| F | CloudAP eklentisi şifreli PRT ve oturum anahtarını CloudAP 'e geçirir. CloudAP, aktarım anahtarını (tkprıv) kullanarak oturum anahtarının şifresini çözmek için TPM isteyin ve TPM 'nin kendi anahtarını kullanarak yeniden şifreleyin. CloudAP, şifreli oturum anahtarını önbelleğinde ve PRT ile birlikte depolar. |

### <a name="prt-renewal-in-subsequent-logons"></a>Sonraki oturumlarda PRT yenilemesi

![Sonraki oturumlarda PRT yenilemesi](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Adım | Description |
| :---: | --- |
| A | Kullanıcı oturum açma kullanıcı arabirimindeki parolasını girer. LogonUI bir kimlik doğrulama arabelleğindeki kimlik bilgilerini LSA 'ya geçirir, bu da bunu sırayla CloudAP öğesine geçirir. CloudAP bu isteği CloudAP eklentisine iletir. |
| B | Kullanıcı daha önce kullanıcıya oturum açtıysa, Windows önbelleğe alınmış oturum açma işlemini başlatır ve kullanıcının oturum açmasını sağlamak için kimlik bilgilerini doğrular. Her 4 saatte bir CloudAP eklentisi zaman uyumsuz olarak yenileme işlemini başlatır. |
| C | CloudAP eklentisi kullanıcının kimlik sağlayıcısını tanımlamak için bir bölge bulma isteği başlatır. Kullanıcının kiracısında bir Federasyon sağlayıcısı kurulumu varsa, Azure AD Federasyon sağlayıcısının meta veri değişimi uç noktası (MEX) uç noktasını döndürür. Aksi takdirde Azure AD, kullanıcının Azure AD ile kimlik doğrulaması yapabileceğini belirten bir kullanıcı tarafından yönetilmediğini döndürür. |
| D | Kullanıcı federe ise, CloudAP eklentisi Federasyon sağlayıcısından kullanıcının kimlik bilgileriyle bir SAML belirteci ister. Bu, SAML belirtecini aldıktan sonra Azure AD 'den bir kerelik anahtar ister. Kullanıcı yönetilmiyorsa, CloudAP doğrudan Azure AD 'de nonce 'yi alır. |
| E | CloudAP eklentisi, kimlik doğrulama isteğini kullanıcının kimlik bilgileri, nonce ve var olan PRT ile oluşturur, oturum anahtarını oturum anahtarıyla imzalar ve Azure AD 'ye gönderir. Federasyon ortamında, CloudAP eklentisi kullanıcının kimlik bilgileri yerine Federasyon sağlayıcısı tarafından döndürülen SAML belirtecini kullanır. |
| F | Azure AD, oturum anahtarı imzasını, PRT 'ye gömülü oturum anahtarıyla karşılaştırarak doğrular, nonce 'yi doğrular ve cihazın kiracıda geçerli olduğunu doğrular ve yeni bir PRT yayınlar. Daha önce görüldüğü gibi, PRT, Aktarım anahtarı (tkpub) tarafından şifrelenen oturum anahtarı ile birlikte gönderilir. |
| G | CloudAP eklentisi şifreli PRT ve oturum anahtarını CloudAP 'e geçirir. CloudAP, aktarım anahtarını (tkprıv) kullanarak oturum anahtarının şifresini çözmek ve TPM 'nin kendi anahtarını kullanarak yeniden şifrelemek için TPM 'YI ister. CloudAP, şifreli oturum anahtarını önbelleğinde ve PRT ile birlikte depolar. |

> [!NOTE]
> Usernamemixed uç noktalar dışarıdan etkinleştirildiğinde bir VPN bağlantısı gerekmeden dışarıdan yenilenebilir.

### <a name="prt-usage-during-app-token-requests"></a>Uygulama belirteci istekleri sırasında PRT kullanımı

![Uygulama belirteci istekleri sırasında PRT kullanımı](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Adım | Description |
| :---: | --- |
| A | Bir uygulama (örneğin, Outlook, OneNote vb.), WAM 'ye yönelik bir belirteç isteği başlatır. WAM, sırasıyla belirteç isteğine hizmet etmek için Azure AD WAM eklentisini ister. |
| B | Uygulama için yenileme belirteci zaten kullanılabiliyorsa, Azure AD WAM eklentisi onu bir erişim belirteci istemek için kullanır. Cihaz bağlama kanıtı sağlamak için, WAM eklentisi, isteği oturum anahtarıyla imzalar. Azure AD oturum anahtarını doğrular ve oturum anahtarı tarafından şifrelenen bir erişim belirteci ve uygulama için yeni bir yenileme belirteci yayınlar. WAM eklentisi, belirteçlerin şifresini çözmek için Cloud AP eklentisini ister. Bu, sırasıyla, oturum anahtarını kullanarak şifre çözme için TPM 'yi istediğinde, her iki belirteci de içeren WAM eklentisine neden olur. Sonra, WAM eklentisi uygulamaya yalnızca erişim belirtecini sağlar, ancak yenileme belirtecini DPAPI ile yeniden şifreler ve kendi önbelleğinde depolar  |
| C |  Uygulama için yenileme belirteci yoksa, Azure AD WAM eklentisi bir erişim belirteci istemek için PRT 'yi kullanır. Birlikte bulunan bir kanıt sağlamak için, WAM eklentisi oturum anahtarıyla PRT 'yi içeren isteği imzalar. Azure AD, oturum anahtarı imzasını, PRT 'ye gömülü oturum anahtarıyla karşılaştırarak doğrular, cihazın geçerli olduğunu doğrular ve uygulama için bir erişim belirteci ve yenileme belirteci yayınlar. Ayrıca, Azure AD, her biri oturum anahtarı tarafından şifrelenen yeni bir PRT (yenileme döngüsüne göre) verebilir. |
| D | WAM eklentisi, belirteçlerin şifresini çözmek için Cloud AP eklentisini ister. Bu, sırasıyla, oturum anahtarını kullanarak şifre çözme için TPM 'yi istediğinde, her iki belirteci de içeren WAM eklentisine neden olur. Sonra, WAM eklentisi uygulamaya yalnızca erişim belirtecini sağlar, ancak yenileme belirtecini DPAPI ile yeniden şifreler ve kendi önbelleğinde depolar. WAM eklentisi bu uygulama için iletme belirtecini kullanacak şekilde kullanacaktır. WAM eklentisi Ayrıca, yeni PRT 'yi bulut AP eklentisine geri verir ve bu da kendi önbelleğinde güncelleştirmeden önce Azure AD ile PRT 'yi doğrular. Cloud AP eklentisi ileri doğru giden yeni PRT 'yi kullanacaktır. |
| E | WAM, yeni verilen erişim belirtecini WAM 'ye sağlar ve bunu çağıran uygulamaya geri sağlar|

### <a name="browser-sso-using-prt"></a>PRT kullanarak tarayıcı SSO 'SU

![PRT kullanarak tarayıcı SSO 'SU](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Adım | Description |
| :---: | --- |
| A | Kullanıcı, bir PRT almak için kimlik bilgileriyle Windows 'da oturum açar. Kullanıcı tarayıcıyı açtıktan sonra tarayıcı (veya uzantısı), URL 'Leri kayıt defterinden yükler. |
| B | Bir Kullanıcı Azure AD oturum açma URL 'sini açtığında tarayıcı veya uzantı, kayıt defterinden elde edilen URL 'YI doğrular. Eşleşiyorsa tarayıcı, belirteç almak için yerel istemci konağını çağırır. |
| C | Yerel istemci Konağı, URL 'Lerin Microsoft Identity Providers 'a (Microsoft hesabı veya Azure AD) ait olduğunu doğrular, URL 'den gönderilen bir nonce ayıklar ve bir PRT tanımlama bilgisi almak için CloudAP eklentisine bir çağrı yapar. |
| D | CloudAP eklentisi PRT tanımlama bilgisini oluşturacak, TPM ile bağlantılı oturum anahtarıyla oturum açıp yerel istemci konağına geri göndermeyecektir. |
| E | Yerel istemci ana bilgisayarı, bu PRT tanımlama bilgisini tarayıcıya döndürecek ve bu, Azure AD 'den x-MS-RefreshTokenCredential ve istek belirteçleri olarak adlandırılan istek üst bilgisinin bir parçası olarak ekleyecek. |
| F | Azure AD, PRT tanımlama bilgisinde oturum anahtarı imzasını doğrular, nonce 'yi doğrular, cihazın kiracıda geçerli olduğunu doğrular ve Web sayfası için bir KIMLIK belirteci ve tarayıcının şifreli bir oturum tanımlama bilgisini yayınlar. |

> [!NOTE]
> Yukarıdaki adımlarda açıklanan tarayıcı SSO akışı, Microsoft Edge 'de InPrivate veya Google Chrome 'daki (Microsoft hesapları uzantısı kullanılırken) özel modlardaki oturumlar için uygulanmaz.

## <a name="next-steps"></a>Sonraki adımlar

PRT ile ilgili sorunları giderme hakkında daha fazla bilgi için bkz. [karma Azure Active Directory katılmış Windows 10 ve Windows Server 2016 cihazları sorunlarını giderme](troubleshoot-hybrid-join-windows-current.md).
