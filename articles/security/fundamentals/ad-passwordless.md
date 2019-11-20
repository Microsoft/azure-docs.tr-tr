---
title: Azure Active Directory ile bir dünya 'yi parola olmadan anlama | Microsoft Docs
description: Bu kılavuz CEOs, Cıos, Cıos, kimlik mimarları, kuruluş mimarları ve güvenlik ve BT karar mekanizmalarının, Azure Active Directory uygulamaları için bir passwordless kimlik doğrulama yöntemi seçmekten sorumlu olur.
keywords: parolasız, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 78f8b7097060f1091bfed30722c9ea456feed384
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007272"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Azure Active Directory ile parolalar olmadan bir dünya

Parolalarınızla ilişkinizi bölmek zaman alabilir. Parolalar geçmişte bizim için iyi, ancak bugünün dijital çalışma alanında, saldırganlar için görece kolay bir saldırı vektörü haline gelmiştir. Saldırganlar, parolaları beğendiğinde ve Azure Active Directory (Azure AD) ' deki en yaygın olarak reddedilen parolaların, yıl, ay, mevsimi veya yerel bir spor ekibi gibi terimleri dahil etmenizin nedenini öğrenmek zor değildir. Ayrıca, [araştırma](https://aka.ms/passwordguidance) , parola yönetimi için uzunluk gereksinimleri, karmaşıklık gereksinimleri ve değişiklik frekansları gibi geleneksel önerilerin, insan doğası ile ilgili çeşitli nedenlerle onay açısından üretken olduğunu göstermiştir.

Kullanıcı hesaplarının güvenliğini sağlamak için yaygın olarak kullanılan üç tür saldırı, parola spreyi, kimlik avı ve ihlalin yeniden yürütme. [Akıllı kilitleme](../../active-directory/authentication/howto-password-smart-lockout.md), [yasaklanmış parolalar](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)ve [parola koruması](../../active-directory/authentication/concept-password-ban-bad-on-premises.md) gibi Azure AD özellikleri, bu tür saldırılara karşı korumaya yardımcı olabilir. Benzer şekilde, [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) veya iki adımlı doğrulama uygulamak, ikinci bir kimlik doğrulama biçimi gerektirerek ek güvenlik sağlar. Ancak uzun çalıştırmada, parolasız bir çözüm, en güvenli kimlik doğrulama yöntemini sağlamaya yönelik en iyi çözümdür.

Bu makale, Microsoft 'un parolasız çözümlerini anlamanıza ve uygulamanıza yardımcı olan ve aşağıdaki seçeneklerden bir veya daha fazlası arasından seçim yapmanıza yardımcı olan yolculuğun başlangıcıdır:

* **İş Için Windows Hello**. Windows 10 ' da, Iş için Windows Hello, bilgisayarlarda ve mobil cihazlarda güçlü iki öğeli kimlik doğrulama ile parolaları değiştirir. Bu kimlik doğrulaması, bir cihaza bağlı yeni bir kullanıcı kimlik bilgisi türü içerir ve bir biyometrik tanıma veya PIN kullanır.

* **Parolasız oturum açma Microsoft Authenticator**. Microsoft Authenticator uygulaması, bir Azure AD hesabında parola kullanılmadan oturum açmak için kullanılabilir. Iş için Windows Hello teknolojisine benzer şekilde, Microsoft Authenticator bir cihaza bağlı olan ve biyometrik ya da PIN kullanan kullanıcı kimlik bilgilerini etkinleştirmek için anahtar tabanlı kimlik doğrulaması kullanır.

* **FIDO2 güvenlik anahtarları**. FIDO2, her Web sitesinde benzersiz olan ve Windows Hello veya dış güvenlik anahtarları gibi yerel bir cihazda depolanan şifreli oturum açma kimlik bilgileri sağlar. Bu güvenlik anahtarları, kimlik avlama, parola hırsızlığı ve yeniden yürütme saldırılarının riskleri açısından dayanıklı değildir. Biyometri veya PIN aracılığıyla kullanıcı doğrulaması ile birlikte kullanıldığında, modern güvenlik ihtiyaçlarını karşılayan iki öğeli doğrulama çözümü vardır.

## <a name="the-future-of-passwordless-authentication"></a>Parolasız kimlik doğrulamasının geleceği

Bu günler, bankalar, kredi kartı şirketleri ve diğer kuruluşlar ve çevrimiçi hizmetler, kimliğinizi iki kez doğrulamanızı isteyerek hesabınızı korur: parolanızı kullanarak bir kez, daha sonra telefon, metin veya bir uygulama. Multi-Factor Authentication, paylaşılan, çalınan veya tahmin edilen parolaların güvenlik sorununu ele alırken, bunları hatırlama denemelerine yönelik zorluk etmenini gidermez. Günümüzün bulut dönemi içinde hangi kullanıcıların ve kuruluşların, son derece güvenli *ve* kullanışlı olan parolasız kimlik doğrulama yöntemleri vardır.

![Kolaylık ve güvenlik karşılaştırması](./media/ad-passwordless/azure-ad-pwdless-image1.png)

Iş için Windows Hello, Microsoft Authenticator ile parolasız oturum açma ve FIDO2 güvenlik anahtarları, kullanıcılara hem yüksek düzeyde güvenli hem de kullanımı kolay bir kimlik doğrulama yöntemi sağlayan basit, ortak bir mimari paylaşır. Her üçü ortak/özel anahtar teknolojisine dayalıdır, Biyometri veya PIN gibi yerel bir hareket ve tek bir cihaza bağlı ve güvenli bir şekilde depolanmış ve hiç paylaşılmayan özel anahtarlar gerektirir.

## <a name="windows-hello-for-business"></a>İş İçin Windows Hello

Windows 10 ' da, Iş için Windows Hello, bilgisayarlarda ve cihazlarda güçlü iki öğeli kimlik doğrulaması ile parolaları değiştirir. Kimlik doğrulaması, bir cihaza bağlı yeni bir kullanıcı kimlik bilgisi türü içerir ve kullanıcıların Azure AD 'de ve şirket içi Active Directory kimlik doğrulamasını sağlayan bir biyometri hareketi veya PIN kullanır. Yalnızca Iş için Windows Hello 'Yu kullanarak bir cihazda oturum açmanız kolaydır. Bir PIN veya bir parmak izi ya da yüz tanıma gibi bir biyometri kullanın.

### <a name="windows-hello-for-business-scenarios"></a>Iş için Windows Hello senaryoları

Iş için Windows Hello, kendilerine ait Windows bılgısayar olan bilgi çalışanları için idealdir. Biyometrik ve kimlik bilgileri, kullanıcının BILGISAYARıNA doğrudan bağlanır, bu da sahip dışında bir kişiye erişimi engeller. PKI tümleştirmesi ve çoklu oturum açma (SSO) için yerleşik destek sayesinde Iş için Windows Hello, şirket içi ve buluttaki kurumsal kaynaklara sorunsuz bir şekilde erişmek için basit ve kullanışlı bir yöntem sağlar.

### <a name="windows-hello-for-business-deployment-considerations"></a>Iş için Windows Hello dağıtım konuları

Iş için Windows Hello, cihaz kaydı, sağlama ve kimlik doğrulamayı gerçekleştirmek için çeşitli bileşenleri kullanan dağıtılmış bir sistemdir. Bu nedenle, dağıtım kuruluştaki birden fazla ekip üzerinde doğru planlamayı gerektirir. İş için Windows Hello [Planlama Kılavuzu](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) , Iş Için Windows Hello dağıtımı ve göz önünde bulundurmanız gereken seçenekler hakkında kararlar almanıza yardımcı olmak için kullanılabilir.

Iş için Windows Hello 'yu dağıttığınızda seçebileceğiniz birçok seçenek vardır. Birden çok seçenek sağlamak, neredeyse her kuruluşun Iş için Windows Hello 'Yu dağıtmasına olanak sağlar. Desteklenen aşağıdaki dağıtım türlerini göz önünde bulundurun:

* [Karma Azure AD 'ye katılmış anahtar güven dağıtımı](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Karma Azure AD 'ye katılmış sertifika güven dağıtımı](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Azure AD JOIN çoklu oturum açma dağıtım kılavuzu](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Şirket Içi anahtar güven dağıtımı](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Şirket Içi sertifika güven dağıtımı](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Birçok seçenek sağlanması, dağıtımın karmaşık görünmesini sağlar. Ancak, çoğu kuruluş büyük olasılıkla Iş için Windows Hello dağıtımının bağımlı olduğu altyapının çoğunu zaten uyguladıklarından emin olur. Ne olursa olsun, Iş için Windows Hello 'nun dağıtılmış bir sistem olduğunu ve uygun planlamanın önerildiğini anlamak önemlidir.

Belirli bir kuruluşunuz için en uygun dağıtım modeline karar vermenize yardımcı olması için [bir iş Için Windows Hello dağıtımı planlamasını](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) okumanız önerilir. Ardından, yaptığınız planlamaya göre, mevcut ortamınızda Iş için Windows Hello 'nun başarılı bir şekilde dağıtılmasını sağlamaya yardımcı olmak üzere [iş Için Windows Hello dağıtım kılavuzu](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) ' na bakın.

### <a name="how-windows-hello-for-business-works"></a>Iş için Windows Hello nasıl kullanılır?

#### <a name="user-sets-up-windows-hello-for-business"></a>Kullanıcı Iş için Windows Hello 'Yu ayarlar

Kayıt sırasında kullanıcının ilk iki adımlı doğrulaması yapıldıktan sonra, kullanıcının cihazında Windows Hello ayarlanır ve Windows, kullanıcıdan parmak izi veya yüz tanıma ya da bir PIN gibi bir biyometri olabilecek bir hareket ayarlaması ister. Bir kez ayarlandıktan sonra, Kullanıcı kimliklerini doğrulama hareketini sağlar. Windows, kullanıcıların kimliğini doğrulamak için Windows Hello 'Yu kullanır.

Windows 10 cihazınızın özelliklerine bağlı olarak, donanım Güvenilir Platform Modülü (TPM) veya yazılım TPM 'si olarak bilinen yerleşik bir güvenli şifreme sahibi olursunuz. TPM, kilidini açmak için yüz, parmak izi veya PIN 'inizi gerektiren özel anahtarı depolar. Biyometrik veriler dolaşımda değildir ve dış cihazlara veya sunuculara hiçbir şekilde gönderilmez. Windows Hello yalnızca biyometrik kimlik verilerini cihazda depoladığından, bir saldırganın biyometrik verileri çalmak için tehlikeye atabilecek tek bir koleksiyon noktası yoktur.

> [!TIP]
> Yüzeyde, PIN bir parola gibi hissedir, ancak bu aslında daha güvenlidir. Parola ve PIN arasındaki önemli bir fark, PIN 'in ayarlandığı belirli bir cihaza bağlı olması olabilir. Parolanızı çeken birisi hesabınızda her yerden oturum açabilir. Ancak PIN 'inizi çaldıysanız fiziksel cihazınızı da çalmak zorunda kalmaz! Ayrıca, bir PIN, cihazda yerel olduğundan, bir sunucudan iletimde yakalanamayacak veya bir sunucudan çalınabilmesi için herhangi bir yerde aktarılmaz.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Oturum açmak için Iş için Windows Hello kullanan Kullanıcı

Iş için Windows Hello Biyometri ve PIN 'Ler kimlik doğrulaması için asimetrik (ortak/özel anahtar) şifrelemeyi kullanır. Kimlik doğrulama sırasında, şifreleme, kimlik doğrulama sürecini güvenlik altına alarak, bir ortadaki adam (Mitı) saldırısının ortaya çıkan güvenlik belirtecini veya yapıtları oynamaması ve diğer yerlerden yeniden oynamasını sağlamak üzere TLS oturum anahtarına bağlıdır.

Iş için Windows Hello, kullanıcının kimliğini Azure AD ve Active Directory kaynakları için kimlik doğrulaması yapan kullanışlı bir oturum açma deneyimi sağlar. Azure AD 'ye katılmış cihazlar oturum açma sırasında Azure 'da kimlik doğrular ve isteğe bağlı olarak Active Directory kimlik doğrulaması yapabilir. Karma Azure Active Directory katılmış cihazlar, oturum açma sırasında Active Directory kimlik doğrular ve arka planda Azure Active Directory kimlik doğrulaması yapılır.

![Kaynak resme bakın](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

Aşağıdaki adımlarda Azure Active Directory için oturum açma kimlik doğrulaması gösterilmektedir.

![Windows 10 kilit ekranı](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. Kullanıcı, Biyometri veya PIN hareketini kullanarak Windows 'da oturum açar. Hareket, Iş için Windows Hello özel anahtarının kilidini açar ve Cloud AP sağlayıcısı olarak adlandırılan bulut kimlik doğrulaması güvenlik desteği sağlayıcısına gönderilir.

2. Cloud AP sağlayıcısı Azure Active Directory bir nonce ister.

3. Azure AD, 5 dakika için geçerli bir kerelik anahtar döndürür.

4. Cloud AP sağlayıcısı, kullanıcının özel anahtarını kullanarak nonce 'yi imzalar ve imzalı nonce öğesini Azure Active Directory döndürür.

5. Azure Active Directory, kullanıcının güvenli şekilde kaydedilmiş ortak anahtarını nonce imzasına karşı kullanarak imzalı nonce 'yi doğrular. İmza doğrulandıktan sonra, Azure AD döndürülen imzalı nonce 'yi doğrular. Nonce doğrulandıktan sonra Azure AD, cihazın aktarım anahtarına şifrelenmiş ve bulut AP sağlayıcısına döndüren oturum anahtarı ile bir PRT oluşturur.

6. Cloud AP sağlayıcısı, oturum anahtarıyla şifrelenmiş PRT 'yi alır. Cihazın özel aktarım anahtarını kullanarak, Cloud AP sağlayıcısı oturum anahtarının şifresini çözer ve cihazın TPM 'sini kullanarak oturum anahtarını korur.

7. Cloud AP sağlayıcısı, yeniden kimlik doğrulaması gerekmeksizin (SSO), kullanıcının Windows 'a ve bulut ve şirket içi uygulamalara erişebilmesi için Windows 'a başarılı bir kimlik doğrulama yanıtı döndürür.

Iş için Windows Hello 'Yu içeren diğer senaryolarda kimlik doğrulama işlemine daha ayrıntılı bir bakış için bkz. [iş Için Windows Hello ve kimlik doğrulaması](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Kullanıcı Iş için Windows Hello kimlik bilgilerini yönetir

[MICROSOFT PIN sıfırlama Hizmetleri](/windows/security/identity-protection/hello-for-business/hello-feature-pin-reset) , Azure AD 'de kullanıcıların PIN kodlarını sıfırlamalarını sağlayan bir özelliktir. Yönetici, Grup ilkesi, Microsoft Intune veya uyumlu bir MDM 'yi kullanarak Windows 10 cihazlarını, kullanıcıların bu ayarları aracılığıyla veya kilit ekranı üzerinde istemeden PIN 'ı sıfırlamasına olanak sağlayan Microsoft PIN sıfırlama hizmetini güvenli bir şekilde kullanmasını sağlar. yeniden kayıt.

Bazen kullanıcıların parolaları kullanmaya geri dönmesi gerekir. [Self servis parola sıfırlama](../../active-directory/authentication/howto-sspr-deployment.md) (SSPR), kullanıcıların BT personeline başvurmanız gerekmeden parolalarını sıfırlamalarını sağlayan başka BIR Azure AD özelliğidir. Kullanıcılar, hizmet kullanılmadan önce self servis parola sıfırlama için kaydolmalıdır veya kaydolmalıdır. Kayıt sırasında Kullanıcı, kuruluşu tarafından etkinleştirilen bir veya daha fazla kimlik doğrulama yöntemi seçer. SSPR kullanıcıların engellerini hızlı bir şekilde kaldırmasını ve nerede olduğu veya günün saati olmadan çalışmaya devam etmesine olanak sağlar. Kuruluşunuz, kullanıcıların kendi kendilerine engellemeyi kaldırmaya izin vererek, en yaygın parolayla ilgili sorunlara yönelik üretken olmayan süreyi ve yüksek destek maliyetlerini azaltabilir.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Microsoft Authenticator ile passwordless oturum açma

Microsoft Authenticator ile passwordless oturum açma, telefonla oturum açma kullanılarak Azure AD hesaplarında oturum açmak için kullanılabilecek başka bir passwordless çözümüdür. Bildiğiniz bir şey ve sahip olduğunuz bir şeyi vererek kimliğinizi doğrulamanız gerekir, ancak telefon oturumu açma işlemini atlayıp parmak izinizi, yüzünüzü veya PIN 'inizi kullanarak mobil cihazınızda tüm kimlik doğrulamanızı gerçekleştirmenizi sağlar.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator parolasız senaryolar

Microsoft Authenticator uygulaması, kullanıcıların kimliklerini doğrulamalarını ve iş veya kişisel hesaplarına kimlik doğrulamasını sağlar. Bu, bir kerelik geçiş kodu veya anında iletme bildirimi içeren bir parolayı artırmak veya bir parola gereksinimini tamamen değiştirmek için de kullanılabilir. Kullanıcılar, parola kullanmak yerine, kendi cep telefonlarını parmak izi taraması, yüz veya Iris tanıma veya PIN aracılığıyla kullanarak kimliklerini onaylanır. Windows Hello 'nun kullandığı gibi güvenli teknolojide yerleşik olarak bulunan bu araç, mobil cihazlarda basit bir uygulamaya paketlenir ve bu da kullanıcılara kullanışlı bir seçenek sağlar. Microsoft Authenticator uygulaması Android ve iOS için kullanılabilir.

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator dağıtım konuları

Azure AD 'de passwordless oturum açmak için Microsoft Authenticator uygulamasını kullanmaya yönelik önkoşullar şunlardır:

* Son kullanıcılar Azure Multi-Factor Authentication etkinleştirildi

* Uygulamanın dağıtımını kolaylaştırmak için Microsoft Intune veya bir üçüncü taraf mobil cihaz yönetimi (MDM) çözümünü kullanarak cihazlarını kaydetmeleri önerilir.

Bu gereksinimlerin karşılandığı varsayıldığında Yöneticiler, [Windows PowerShell](../../active-directory/authentication/howto-authentication-passwordless-phone.md) kullanarak Kiracıdaki parolasız telefon oturum açma özelliğini etkinleştirir. Kiracıda telefonla oturum açma etkinleştirildikten sonra, son kullanıcılar uygulamanın **hesaplar** ekranında iş veya okul hesabı ' nı seçip **telefonla oturum açmayı etkinleştir**' i seçerek telefonu kullanarak oturum açmayı tercih edebilir.

Parolasız oturum açma 'nın yönetici tarafından etkinleştirildiği varsayıldığında, son kullanıcıların aşağıdaki gereksinimleri karşılaması gerekir:

* Azure Multi-Factor Authentication kayıtlı

* İOS 8,0 veya üzerini çalıştıran cihazlarda yüklü Microsoft Authenticator en son sürümü veya Android 6,0 veya üzeri

* Uygulamaya anında iletme bildirimleri eklenmiş iş veya okul hesabı

Hesabınızı kilitleyen veya yeni bir cihazda hesapları yeniden oluşturmak zorunda kalmaktan kaçınmak için [Hesap kimlik bilgilerinizi buluta yedeklemek](../../active-directory/user-help/user-help-auth-app-backup-recovery.md) üzere Microsoft Authenticator kullanmanız önerilir. Yedeklemeden sonra, verilerinizi yeni bir cihazda kurtarmak için de kullanabilirsiniz; bu da, kilitlenmelerden veya hesapların yeniden oluşturulması gereğini ortadan kaldırmanıza imkan sağlar.

Çoğu Kullanıcı, kimlik doğrulamak için yalnızca parolaları kullanmaya alışkın olduğundan, kuruluşunuzun bu işlemle ilgili olarak kullanıcıları eğitilbilmesi önemlidir. Tanıma, kullanıcıların Microsoft Authenticator uygulamasını kullanarak oturum açma ile ilgili herhangi bir [sorun](../../active-directory/authentication/howto-authentication-passwordless-phone.md) için yardım masasına çağrı olasılığını azaltabilir.

> [!NOTE]
> Bu çözüm için olası bir başarısızlık noktası, gezici bir kullanıcının Internet bağlantısı olmayan bir konumda olduğu yerdir. FIDO2 güvenlik anahtarları ve Iş için Windows Hello aynı sınırlamaya tabi değildir.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Microsoft Authenticator ile passwordless oturum açma nasıl işe yarar?

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>Kullanıcı, Microsoft Authenticator ile parolasız oturum açma ayarlıyor

Microsoft Authenticator uygulamasının bir Azure AD hesabında oturum açmak için parolasız bir çözüm olarak kullanılabilmesi için, adımların hem yönetici hem de son kullanıcılar tarafından gerçekleştirilmesi gerekir.

İlk olarak, bir yöneticinin Windows PowerShell kullanarak, Kiracıdaki [kimlik bilgileri olarak uygulamanın kullanımını etkinleştirmesi](../../active-directory/authentication/howto-authentication-passwordless-phone.md) gerekir. Ayrıca, yöneticinin Azure Multi-Factor Authentication (Azure MFA) için son kullanıcıları etkinleştirmesi ve Microsoft Authenticator uygulamasını [doğrulama yöntemlerinden](../../active-directory/authentication/howto-mfa-mfasettings.md#verification-methods)biri olarak yapılandırması gerekir.

Son kullanıcıların Microsoft Authenticator uygulamasını [indirmesi ve yüklemesi](../../active-directory/user-help/user-help-auth-app-download-install.md) ve Microsoft Authenticator uygulamasını doğrulama yöntemlerinden biri olarak kullanması için [hesabını ayarlaması](../../active-directory/user-help/security-info-setup-auth-app.md) gerekir.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Parolasız oturum açma için Microsoft Authenticator kullanan Kullanıcı

Microsoft Authenticator uygulaması, herhangi bir Azure AD hesabında parola kullanılmadan oturum açmak için kullanılabilir. Windows 10 kilit ekranı, oturum açma seçeneği olarak Microsoft Authenticator uygulamasını içermediği sürece, kullanıcılar Kullanıcı adını girmeye devam edebilir ve ardından mobil cihazlarında varlığı doğrulamak üzere bir anında iletme bildirimi alabilir. Kullanıcılar, oturum açma ekranında bir sayıyla eşleşen ve ardından özel anahtarın kilidini açmak için bir yüz taraması, parmak izi veya PIN sağlayarak ve kimlik doğrulamasını tamamlayarak kendi varlığını ortadan kaldırır. Bu Multi-Factor Authentication yöntemi bir paroladan daha güvenlidir ve bir parola ve kod girmekten daha uygundur.

![Authenticator oturum açma](./media/ad-passwordless/azure-ad-pwdless-image4.png)

Microsoft Authenticator kullanan passwordless kimlik doğrulaması, Iş için Windows Hello ile aynı temel kalıbı izler, ancak kullanıcının Azure AD 'nin Microsoft Authenticator uygulama sürümünü bulabileceği şekilde tanımlanması gerektiğinden bu yana biraz daha karmaşıktır. kullanılamıyor.

![Authenticator işlemi](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. Kullanıcı Kullanıcı adını girer.

2. Azure AD, kullanıcının güçlü bir kimlik bilgisine sahip olduğunu algılar ve güçlü kimlik bilgisi akışını başlatır.

3. Bildirim, iOS cihazlarında Apple Anında İletilen Bildirim Servisi (APNS) aracılığıyla uygulamaya veya Android cihazlarda Firebase Cloud Messaging (FCM) aracılığıyla gönderilir.

4. Kullanıcı anında iletme bildirimini alır ve uygulamayı açar.

5. Uygulama Azure AD 'yi çağırır ve bir varlık kanıtı ve nonce bilgisi alır.

6. Kullanıcı, özel anahtarın kilidini açmak için biyometrik veya PIN 'ı girerek sınamayı tamamlar.

7. Nonce özel anahtarla imzalanır ve Azure AD 'ye geri gönderilir.

8. Azure AD ortak/özel anahtar doğrulaması gerçekleştirir ve bir belirteç döndürür.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Kullanıcı, Microsoft Authenticator kimlik bilgileriyle parolasız oturum açmayı yönetir

[Birleşik kayıt](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)ile, kullanıcılar hem Azure Multi-Factor Authentication hem de Self servis parola sıfırlamasının avantajlarını kaydedebilir ve bunların avantajlarını alabilir. Kullanıcılar bu ayarları, [Profilim sayfamda](https://aka.ms/mysecurityinfo)gezinerek kaydeder ve yönetir. SSPR 'yi etkinleştirmenin yanı sıra, Birleşik kayıt birden çok kimlik doğrulama yöntemini ve eylemini destekler.

## <a name="fido2-security-keys"></a>FIDO2 güvenlik anahtarları

FIDO2, FIDO Alliance standardının en son sürümüdür ve iki bileşen-W3C's Web kimlik doğrulaması (WebAuthN) standardı ve ilgili FIDO Alliance Istemci-Authenticator Protokolü (CTAP2) içerir. FIDO2 standartları, mobil ve masaüstü ortamlarındaki birçok uygulama ve Web sitesi ile kolayca kimlik doğrulaması yapmak için kullanıcıların donanım, mobil ve Biyometri tabanlı kimlik doğrulamasından yararlanmasını sağlar.

Microsoft ve sektör iş ortakları, Paylaşılan cihazlarda kolay ve güvenli kimlik doğrulaması sağlamak üzere Windows Hello için FIDO2 güvenlik cihazlarında birlikte çalışıyor. FIDO2 güvenlik anahtarları, kimlik bilgilerinizi sizinle birlikte kullanmanıza ve kuruluşunuzun bir parçası olan [Azure AD](https://aka.ms/azuread418)'ye katılmış bir Windows 10 cihazına güvenli bir şekilde kimlik doğrulaması yapmanıza imkan tanır.

WebAuthN, Web uygulamaları ve Hizmetleri tarafından sağlam, passwordless kimlik doğrulamasının geliştirilmesine ve uygulanmasına izin veren bir API tanımlar. CTAP protokolü, FIDO uyumlu güvenlik anahtarlarının, WebAuthN ile çalışmasını ve kimlik doğrulayıcılar olarak kullanılmasını sağlar. Web kimlik doğrulamasıyla, kullanıcılar yüz, parmak izi, PIN veya taşınabilir FIDO2 güvenlik anahtarlarıyla çevrimiçi hizmetler oturum açarak, parolalar yerine güçlü ortak anahtar kimlik bilgilerini kullanabilir. Şu anda WebAuthN, Microsoft Edge 'de desteklenir ve Chrome ve Firefox desteği geliştirmektedir.

FIDO2, WebAuthN ve CTAP protokollerine bağlı olan cihazlar ve belirteçler, parolaların kullanılmadan güçlü kimlik doğrulaması için platformlar arası bir çözüm sağlar. Microsoft iş ortakları, USB güvenlik anahtarları ve NFC özellikli akıllı kartlar gibi çeşitli güvenlik anahtarı form faktörleri üzerinde çalışmaktadır.

### <a name="fido2-security-keys-scenarios"></a>FIDO2 güvenlik anahtarları senaryoları

FIDO2 güvenlik anahtarları, Windows 10 kilit ekranında kimlik bilgisi sağlayıcısı olarak güvenlik anahtarını seçerek Azure AD 'de oturum açmak için kullanılabilir. Birden çok kullanıcı arasında bilgisayarları paylaşan ilk satır çalışanları için ideal bir çözüm sunan bir Kullanıcı adı veya parola gerekli değildir. Ayrıca, şirket ilkeleri kullanıcının kimlik bilgilerinin cihazından fiziksel olarak ayrı olması gerektiğini belirten çok iyi bir kimlik doğrulama seçeneğidir. Kullanıcılar ayrıca, Windows 10 sürüm 1809 veya üzeri sürümlerde Microsoft Edge tarayıcısının içindeki FIDO2 güvenlik anahtarını kullanarak Web sitelerinde oturum açmayı tercih edebilir.

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 güvenlik anahtarları dağıtım konuları

Yöneticiler, Azure AD 'de FIDO2 desteğini etkinleştirebilir ve kullanıcılara veya gruplara özelliği atayabilir. İlkeler, anahtarların nasıl sağlanacağı ve belirli bir donanım güvenlik anahtarı kümesine izin verme veya engelleme gibi kısıtlamalar yapılandırma için de oluşturulabilir. Anahtarların son kullanıcılara fiziksel olarak dağıtılması gerekir.

**FIDO2 güvenlik anahtarları kullanılarak Azure AD ve web sitelerinde passwordless oturum açma özelliğinin etkinleştirilmesi için gerekenler şunlardır:**

* Azure AD

* Azure Multi-Factor Authentication

* Birleşik kayıt önizlemesi

* FIDO2 güvenlik anahtarı önizlemesi uyumlu bir FIDO2 güvenlik anahtarı gerektirir

* Web kimlik doğrulaması (WebAuthN), Windows 10 sürüm 1809 veya üzeri sürümlerde Microsoft Edge gerektirir

* FIDO2 tabanlı Windows oturum açma, Azure AD 'ye katılmış Windows 10 sürüm 1809 veya üstünü gerektirir (en iyi deneyim Windows 10 sürüm 1903 veya üzeri)

FIDO2 uyumlu form faktörleri USB, NFC ve Bluetooth cihazları içerir. Bazı platformların ve tarayıcıların henüz FIDO2 uyumlu olmadığı için özel ihtiyaçlarınızı karşılayan form faktörünü seçmenizi öneririz.

Ayrıca, her bir kuruluşun bir güvenlik anahtarının kaybedilmesi veya çalınması için bir protokol oluşturmasını öneririz. Kullanıcılar kayıp veya çalınmış anahtarı raporlayabilmelidir, böylece Yöneticiler veya Kullanıcı kendi güvenlik anahtarlarını Kullanıcı profilinden silebilir ve yeni bir tane sağlayamaz.

### <a name="how-fido2-security-keys-works"></a>FIDO2 güvenlik anahtarlarının nasıl çalıştığı

#### <a name="user-sets-up-fido2-security-key"></a>Kullanıcı FIDO2 güvenlik anahtarı ayarlıyor

Yöneticiler [anahtarları el ile](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless) sağlayabildiği ve bunları son kullanıcılara dağıtabilecek sırada, Windows 10 kılıt ekranında FIDO2 kimlik bilgisi sağlayıcısının sağlanması ve etkinleştirilmesi [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods)üzerinden desteklenecektir. Ayrıca, yöneticilerin, donanım belirteci cihazlarını passwordless kimlik doğrulama yöntemi olarak etkinleştirmek için [Azure Portal](https://portal.azure.com/) kullanmaları gerekir.

FIDO2 güvenlik anahtarlarının dağıtımı, kullanıcıların anahtarlarını [Birleşik kayıt](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)kullanarak kaydetmelerini de gerektirir. Birleşik kayıt ile kullanıcılar bir kez kaydolduktan sonra hem Azure Multi-Factor Authentication hem de çoklu oturum açma parolası sıfırlamasının (SSPR) avantajlarından yararlanır.

Donanım belirtecini varsayılan Multi-Factor Authentication yöntemi olarak seçmeye ek olarak, ek bir doğrulama seçeneği de seçmeniz önerilir.

* Microsoft Authenticator--bildirim

* Kimlik doğrulayıcı uygulaması veya donanım belirteci--kod

* Telefon araması

* Kısa mesaj

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Oturum açma için FIDO2 güvenlik anahtarını kullanan Kullanıcı

FIDO2, özel anahtara ve ortak anahtar sunmaya yönelik Windows Hello ve güvenlik anahtarları gibi yerleşik platform kimlik doğrulamasını etkinleştirmek için kimlik doğrulayıcı ve ortak/özel anahtar şifrelemesi olarak kullanılan form faktörü arasında bir soyutlama katmanı sağlar Bu, dış kaynaklara erişmek için bir tanımlayıcı olarak kullanılabilir. FIDO2 güvenlik anahtarları, özel anahtarı depolayan ve Biyometri veya PIN 'in kilidini açmak için kendi yerleşik güvenli şifrelemesinin bulunduğu bir şekilde donatılmıştır. Kimlik bilgileri, hizmetler arasında yeniden kullanılamaz, yeniden yürütülemez veya paylaşılamaz ve kimlik avına ve Mitı saldırılarına veya sunucu ihlallerine tabi değildir.

![FIDO2 oturum açma](./media/ad-passwordless/azure-ad-pwdless-image6.png)

FIDO2 güvenlik anahtarları, form etmenden bağımsız olarak güvenli kimlik doğrulaması sağlar. Güvenlik anahtarı kimlik bilgisini barındırır ve parmak izi (güvenlik anahtarıyla tümleşik) gibi ek bir ikinci faktörle veya Windows oturum açma sırasında girilecek bir PIN ile korunmalıdır. Microsoft iş ortakları, çeşitli güvenlik anahtarı form faktörleri üzerinde çalışmaktadır. Bazı örneklerde USB güvenlik anahtarları ve NFC etkinleştirilmiş akıllı kartlar sayılabilir.

> [!NOTE]
> Bir güvenlik anahtarı, [Microsoft uyumlu](https://aka.ms/fido2securitykeys)olması IÇIN FIDO2 CTAP protokolünden bazı özellikleri ve uzantıları uygulamalıdır. Microsoft bu çözümleri Windows 10 ve Azure Active Directory uyumluluğuyla test etti.

![FIDO2 oturum açma işlemi](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. Kullanıcı FIDO2 cihazını bilgisayara takar.

2. Windows, FIDO2 güvenlik anahtarını algılar.

3. Windows bir kimlik doğrulama isteği gönderir.

4. Azure AD bir nonce geri gönderir.

5. Kullanıcı, FIDO2 güvenlik anahtarının güvenli kuşsında depolanan özel anahtarın kilidini açma hareketini tamamlar.

6. FIDO2 güvenlik anahtarı, anahtar nonce 'i özel anahtarla imzalar.

7. İmzalı nonce ile PRT belirteç isteği Azure AD 'ye gönderilir.

8. Azure AD, FIDO2 ortak anahtarını kullanarak imzalı nonce 'yi doğrular.

9. Azure AD, şirket içi kaynaklara erişimi etkinleştirmek için PRT 'yi döndürür.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Kullanıcı FIDO2 güvenlik anahtarı kimlik bilgilerini yönetir

Microsoft Authenticator uygulamasına benzer şekilde, FIDO2 güvenlik anahtarları için kimlik bilgisi yönetimi, son kullanıcılar için Birleşik kayıt deneyimine dayanır.

## <a name="deciding-a-passwordless-method"></a>Passwordless yöntemine karar verme

Bu üç parolasız seçenek arasından seçim yapmak, şirketinizin güvenlik, platform ve uygulama gereksinimlerine bağlıdır.

Microsoft parola-Less teknolojisini seçerken göz önünde bulundurmanız gereken bazı etmenler şunlardır:

||**İş İçin Windows Hello**|**Microsoft Authenticator uygulamayla passwordless oturum açma**|**FIDO2 güvenlik anahtarları**|
|:-|:-|:-|:-|
|**Önkoşul**| Windows 10, sürüm 1809 veya üzeri<br>Azure Active Directory| Microsoft Authenticator uygulaması<br>Telefon (Android 6,0 veya üzeri çalıştıran iOS ve Android cihazları.)|Windows 10, sürüm 1809 veya üzeri<br>Azure Active Directory|
|**Modundaysa**|Platform|Yazılım|Donanım|
|**Sistemler ve cihazlar**|Yerleşik Güvenilir Platform Modülü bılgısayar (TPM)<br>PIN ve Biyometri tanıma |Telefonda PIN ve Biyometri tanıma|Microsoft uyumlu FIDO2 güvenlik cihazları|
|**Kullanıcı deneyimi**|Windows cihazlarıyla bir PIN veya biyometrik tanıma (yüz, Iris veya parmak izi) kullanarak oturum açın.<br>Windows Hello kimlik doğrulaması cihaza bağlıdır; kullanıcının şirket kaynaklarına erişmek için hem cihaza hem de PIN veya biyometri faktörü gibi bir oturum açma bileşenine ihtiyacı vardır.|Parmak izi taraması, yüz veya Iris tanıma veya PIN ile cep telefonu kullanarak oturum açın.<br>Kullanıcılar, PC veya cep telefonlarından iş veya kişisel hesap için oturum açabilirler.|FIDO2 güvenlik cihazını (biyometri, PIN ve NFC) kullanarak oturum açın<br>Kullanıcı, cihaza, USB güvenlik anahtarları ve NFC 'nin etkinleştirildiği akıllı kartlar, anahtarlar veya wearables gibi cihazları kullanarak kuruluş denetimlerini ve kimlik doğrulamasını temel alarak cihaz erişimi sağlayabilir.|
|**Etkin senaryolar**| Windows cihazındaki parola-daha az deneyim.<br>Cihaz ve uygulamalarda çoklu oturum açma yeteneğine sahip adanmış iş BILGISAYARı için geçerlidir.|Cep telefonu kullanan parola-daha az her yerde çözüm.<br>Herhangi bir cihazdan Web üzerinde iş veya kişisel uygulamalara erişmek için geçerlidir.|Biyometri, PIN ve NFC kullanan çalışanlar için parola açısından daha az deneyim.<br>Paylaşılan bilgisayarlar ve cep telefonunun uygun bir seçenek olmaması (örneğin, yardım masası personeli, genel bilgi noktası veya hastane ekibi gibi) için geçerlidir|

Gereksinimlerinizi ve kullanıcılarınızı hangi yöntemin destekleyeceği belirlemek için aşağıdaki tabloyu kullanın.

|Bilgisini|Senaryo|Ortam|Passwordless teknolojisi|
|:-|:-|:-|:-|
|**Yöneticileri**|Yönetim görevleri için bir cihaza güvenli erişim|Atanan Windows 10 cihazı|Iş için Windows Hello ve/veya FIDO2 güvenlik anahtarı|
|**Yöneticileri**|Windows olmayan cihazlarda yönetim görevleri| Mobil veya Windows dışı cihaz|Microsoft Authenticator uygulamayla passwordless oturum açma|
|**Bilgi çalışanı**|Üretkenlik çalışmaları|Atanan Windows 10 cihazı|Iş için Windows Hello ve/veya FIDO2 güvenlik anahtarı|
|**Bilgi çalışanı**|Üretkenlik çalışmaları| Mobil veya Windows dışı cihaz|Microsoft Authenticator uygulamayla passwordless oturum açma|
|**Frontline Worker**|Bir fabrika, Tesis, perakende veya veri girişinde kiosks|Paylaşılan Windows 10 cihazları|FIDO2 güvenlik anahtarları|

## <a name="getting-started"></a>Başlarken

Parolasız kimlik doğrulaması, gelecekteki ve daha güvenli bir ortam yolunun yoludur. Kuruluşların bu değişikliği planlamaya ve parolalarla ilgili bağımlılıklarını azaltmasına başlaması önerilir. Başlamak için aşağıdaki hedefleri göz önünde bulundurun:

* MFA + Microsoft Authenticator App + koşullu erişim için kullanıcıları etkinleştirin.

* Azure AD parola koruması + güncelleştirme ilkelerini piyasaya çıkarma.

* Birleştirilmiş kayıt ile SSPR için kullanıcıları etkinleştirin.

* Mobility için Microsoft Authenticator uygulaması dağıtın.

* Iş için Windows Hello 'Yu dağıtın (1903: güncel kalın).

* Telefonlarını kullanmayan kullanıcılar için FIDO2 cihazlarını dağıtın.

* Mümkün olduğunda, parola tabanlı kimlik doğrulamasını devre dışı bırakın.

Bu hedeflere ulaşmak için aşağıdaki yaklaşımı öneririz:

1. Azure MFA ve koşullu erişim gibi özelliklerden tam olarak yararlanmak için Azure Active Directory etkinleştirin.

2. Ek koruma sağlamak için Multi-Factor Authentication 'ı etkinleştirin.

3. Kullanıcıların bir parola kullanmaya geri dönmesi gereken olay için self servis parola sıfırlamayı etkinleştirin.

4. Microsoft Authenticator telefonla oturum açmayı ek taşınabilirlik için dağıtın.

5. Iş için Windows Hello 'Yu tüm Windows 10 cihazlarınıza dağıtın.

6. FIDO2 güvenlik anahtarlarına hazırlanın.

> [!NOTE]
> Parolasız yöntemler için lisans gereksinimleriyle ilgili ayrıntılar için Azure Active Directory [lisanslama sayfasına](https://azure.microsoft.com/pricing/details/active-directory/) bakın.

## <a name="conclusion"></a>Sonuç

Son birkaç yılda, Microsoft, parolasız bir dünya etkinleştirme taahhüdünü sürdürdü. Microsoft, Windows 10 ile, Azure Active Directory ve Active Directory çoklu oturum açma imkanı sağlayan güçlü, donanım korumalı iki öğeli kimlik bilgisi olan Iş için Windows Hello 'yu kullanıma sunmuştur. Iş için Windows Hello teknolojisine benzer şekilde, Microsoft Authenticator uygulaması, bir mobil cihaza bağlanan ve biyometrik ya da PIN kullanan kullanıcı kimlik bilgilerini etkinleştirmek için anahtar tabanlı kimlik doğrulaması kullanır. Artık FIDO2 güvenlik anahtarları, kimlik bilgilerinizi sizinle birlikte kullanmanıza ve Windows 10 kilit ekranında kimlik bilgisi sağlayıcısı olarak güvenlik anahtarını seçerek Azure AD 'de oturum açmanıza olanak tanır. Bu paroladan daha az sayıda çözüm, kimlik avlama, parola püskürtme ve yeniden yürütme saldırıları riskini azaltır ve kullanıcılara her yerden oturum açmak ve verilere erişmek için yüksek düzeyde güvenli ve uygun bir yol sağlar.

Yaygın olarak erişilebilen cihazlarda Biyometri ve ortak anahtar şifrelemesi gibi modern Multi-Factor Authentication teknolojilerinin benimsenmesi, şirketin kimlik riskini anlamlı bir şekilde azaltabilmenin en etkili adımlarından biridir. Parolasız hale getirmek, güvenli kimlik doğrulaması için uzun süreli bir yaklaşımdır ve gene de gelişiyor. Verilen gereksinimler verildiğinde kuruluşlar, bir planı passwordless teknolojilerine taşımaya başlamak için bir plan yaparak bunları hazırlayabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Passwordne olduğuna](../../active-directory/authentication/concept-authentication-passwordless.md) ilişkin genel bakış nedir?
* [Azure AD 'de passwordless 'yi etkinleştirme](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
