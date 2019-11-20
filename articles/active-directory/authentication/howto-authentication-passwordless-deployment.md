---
title: Azure AD ile passwordless kimlik doğrulama dağıtımını tamamlandırma
description: Azure Active Directory parolasız kimlik doğrulama dağıtımını tamamlandırma
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b6f07e1dd8e9252d2b6e00b85a47ba2e19f8bd8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603463"
---
# <a name="complete-a-passwordless-authentication-deployment"></a>Parolasız kimlik doğrulama dağıtımını tamamlandırma

Büyük bir kişi Kullanıcı adı ve parola ile birlikte bir kuruluştan başlar. 

Kuruluşlar, kullanıcıların kullanmalarını isteyerek tehdit sayacını denemeye çalışır:

- Uzun parolalar
- Karmaşık parolalar
- Sık kullanılan parola değişiklikleri
- Çok faktörlü kimlik doğrulaması (MFA)

Microsoft 'un [araştırması](https://aka.ms/passwordguidance) , bu çabaları kullanıcılara açıklama ve destek maliyetlerini artırma konusunda göstermektedir. Daha fazla bilgi için [PA $ $Word](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)'e bakın.

Passwordless kimlik doğrulamasının dağıtımı aşağıdaki avantajları sağlar:

- Artırılmış güvenlik. Parolaları saldırı yüzeyi olarak kaldırarak kimlik avı ve parola püskürtme saldırılarına karşı risk azaltma.
- Daha iyi kullanıcı deneyimi. Kullanıcılara her yerden veri erişimi için uygun bir yol sağlayın ve mobil sırada Outlook, OneDrive, Office ve daha fazlasına kolay erişim sağlar.
- Sağlam Öngörüler. Güçlü günlük kaydı ve denetimle kullanıcıların parolasız etkinlik hakkında öngörüler elde edin.

Parola, sizin veya bildiğiniz bir şeyi içeren bir şeyle değiştirilmiştir. Örneğin, Iş için Windows Hello, yüz veya parmak izi gibi bir biyometri hareketi ya da bir ağ üzerinden aktarılamayan cihaza özgü PIN.

Microsoft birçok senaryoyu kapsayan üç adet passwordless kimlik doğrulama seçeneği sunar. Bu yöntemler, birlikte kullanılabilir. 

- [İş Için Windows Hello](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) , kullanıcıların adanmış Windows bilgisayarlarına en iyi şekilde yöneliktir.
- [FIDO2 güvenlik anahtarlarıyla](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) güvenlik anahtarı oturum açma özelliği, özellikle de kiosks gibi paylaşılan makinelerde oturum açan kullanıcılar, telefon kullanımı sınırlı olduğu ve yüksek ayrıcalıklı kimlikler için yararlıdır.
- [Microsoft Authenticator uygulamayla](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) telefon oturumu açmak, mobil cihazlara sahip kullanıcılar için passwordless bir seçenek sağlamak için yararlıdır. Her türlü iOS veya Android telefonunu, kullanıcıların herhangi bir platformda veya tarayıcıda oturum açmasına izin vererek güçlü, passworddaha az bir kimlik bilgisine dönüştürür. Kullanıcılar telefonlarına bir bildirim alarak oturum açıp ekranda görünen bir sayıyı telefondaki bir sayıyla eşleştirir, ardından biyometrik verilerini veya PIN 'ini kullanarak bunları doğrulamak için kullanın.

## <a name="compare-passwordless-authentication-methods"></a>Passwordless kimlik doğrulama yöntemlerini karşılaştırın

Microsoft 'un parolasız kimlik doğrulama yöntemleri farklı senaryolara olanak tanır. Her kimlik doğrulama yönteminin kurumsal ihtiyaçlarını, önkoşullarını ve tüm yeteneklerini, passwordless kimlik doğrulama stratejinizi seçmek için göz önünde bulundurun. Windows 10 cihazları kullanan her kuruluşun Iş için Windows Hello 'Yu kullanmasını öneririz. Daha sonra, ek senaryolar için telefon oturum açma (Microsoft Authenticator uygulamayla) veya güvenlik anahtarları ekleyin.

### <a name="passwordless-authentication-scenarios"></a>Passwordless kimlik doğrulama senaryoları

| Senaryo | Telefon kimlik doğrulaması | Güvenlik anahtarları | İş İçin Windows Hello |
| --- | --- | --- | --- |
| **Bilgisayar oturum açma**: <br> Atanan Windows 10 cihazından | **Hayır** | **Evet** <br> Biyometrik, PIN ile | **Evet**<br>Biyometrik tanıma ve veya PIN ile |
| **Bilgisayar oturum açma**: <br> Paylaşılan Windows 10 cihazından | **Hayır** | **Evet** <br> Biyometrik, PIN ile  | **Hayır** |
| **Web uygulaması oturum açma**: <br>Kullanıcı tarafından ayrılmış bir bilgisayardan | **Evet** | **Evet** <br> Uygulamalarda çoklu oturum açma, bilgisayar oturum açma tarafından etkinleştirildi | **Evet**<br> Uygulamalarda çoklu oturum açma, bilgisayar oturum açma tarafından etkinleştirildi |
| **Web uygulaması oturum açma**: <br> Mobil veya Windows dışı bir cihazdan | **Evet** | **Hayır** | **Hayır** |
| **Bilgisayar oturum açma**: <br> Windows dışı bilgisayar | **Hayır** | **Hayır** | **Hayır** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulaması için teknik konular

**AD FS tümleştirme** -kullanıcı, Microsoft Authenticator paroladışı kimlik bilgisini etkinleştirmesidir, bu kullanıcı için kimlik doğrulaması, onay için bir bildirim gönderir. Karma Kiracıdaki kullanıcıların, "parolanızı kullan" seçeneğini seçmedikçe oturum açmak için ADFS 'ye yönlendirilmesi engellenir. Bu işlem, şirket içi koşullu erişim ilkelerinin yanı sıra geçişli kimlik doğrulama akışlarını da atlar. Ancak, bir login_hint belirtilmişse, kullanıcı ADFS 'ye iletilir ve passwordless kimlik bilgisini kullanma seçeneğini atlar.

Bir kuruluşun şirket içi Azure MFA sunucusu aracılığıyla MFA için etkinleştirilen **Azure MFA sunucusu** son kullanıcıları, hala bir passworddaha az telefon oturum açma kimlik bilgisi oluşturabilir ve kullanabilir. Kullanıcı, kimlik bilgileriyle Microsoft Authenticator birden çok yüklemeyi (5 +) yükseltmeyi denerse, bu değişiklik hataya neden olabilir.

**Cihaz kaydı** -kimlik doğrulayıcı uygulamasını passwordless kimlik doğrulaması için kullanmak Için CIHAZıN Azure AD kiracısında kayıtlı olması ve paylaşılan bir cihaz olması gerekir. Bir cihaz yalnızca tek bir kiracıda kaydedilebilir. Bu sınır, kimlik doğrulayıcı uygulamasını kullanarak telefon oturum açma için yalnızca bir iş veya okul hesabının desteklendiği anlamına gelir.

## <a name="prerequisites"></a>Ön koşullar

Kuruluşların, passwordless dağıtımına başlamadan önce aşağıdaki önkoşulları karşılaması gerekir.

| Önkoşul | Authenticator uygulaması | FIDO2 güvenlik anahtarları |
| --- | --- | --- |
| [Azure MFA ve self servis parola sıfırlama (SSPR) Için Birleşik kayıt](howto-registration-mfa-sspr-combined.md) etkin (Önizleme özelliği) | √ | √ |
| [Kullanıcılar Azure MFA gerçekleştirebiliyor](howto-mfa-getstarted.md) | √ | √ |
| [Azure MFA ve SSPR için kullanıcılar kaydoldu](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Kullanıcılar mobil cihazlarını Azure Active Directory 'e kaydetti](../devices/overview.md) | √ |   |
| Microsoft Edge veya Mozilla Firefox gibi desteklenen bir tarayıcı kullanarak Windows 10 sürüm 1809 veya üzeri <br> (sürüm 67 veya üzeri). <br> *Microsoft, yerel destek için sürüm 1903 veya üstünü önerir*. |   | √ |
| Uyumlu FIDO2 güvenlik anahtarları. [Microsoft tarafından sınanmış ve onaylanmış](howto-authentication-passwordless-enable.md) bir FIDO2 güvenlik cihazını veya başka BIR uyumlu FIDO2 güvenlik cihazını kullandığınızdan emin olun. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Iş için Windows Hello önkoşulları

Windows Hello önkoşulları, şirket içi, karma veya yalnızca bulut yapılandırmasında dağıtım yapıp etmemediklerine oldukça bağlıdır. Daha fazla bilgi için bkz. [iş Için Windows Hello önkoşulların tam listesi](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Kullanıcılar, Azure MFA kayıt akışının bir parçası olarak parolasız yöntemini kaydeder. Bir Kullanıcı adı ve parola ile çok faktörlü kimlik doğrulaması, başka bir kayıtlı yöntemle birlikte, telefon veya güvenlik anahtarını bazı senaryolarda kullanamamaları durumunda geri dönüş olarak kullanılabilir.

### <a name="security-key-lifecycle"></a>Güvenlik anahtarı yaşam döngüsü

Güvenlik anahtarları kaynaklarınıza erişimi etkinleştirir ve bu fiziksel cihazların yönetimini planlamanız gerekir.

1. Anahtar dağıtımı: kuruluşunuza nasıl anahtar sağlayacağınızı planlayın. Merkezi bir sağlama işleminiz olabilir veya son kullanıcıların FIDO 2,0 ile uyumlu anahtarlar satın almasını sağlayabilirsiniz.
1. Anahtar etkinleştirme: son kullanıcıların güvenlik anahtarını kendi kendine etkinleştirmeleri gerekir. Son kullanıcılar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) bir güvenlik anahtarını kaydeder ve ilk kullanımda ikinci faktörü (PIN veya biyometri) etkinleştirir.
1. Bir anahtarı devre dışı bırakma: güvenlik anahtarı işlevselliği önizleme aşamasında olduğunda, yöneticinin bir kullanıcı hesabından anahtar kaldırması için bir yol yoktur. Kullanıcının onu kaldırması gerekir. Bir anahtar kaybolur veya çalınırsa:
   1. Parolayı, passwordless kimlik doğrulaması için etkinleştirilen herhangi bir gruptan kaldırın.
   1. Anahtarı bir kimlik doğrulama yöntemi olarak kaldırdıklarından emin olun.
   1. Yeni bir anahtar verin.
1. Anahtar değişimi: kullanıcılar iki güvenlik anahtarını aynı anda etkinleştirebilir. Bir güvenlik anahtarını değiştirirken, kullanıcının değiştirilmekte olan anahtarı da kaldırdığına emin olun.

### <a name="enable-windows-10-support"></a>Windows 10 desteğini etkinleştir

FIDO2 güvenlik anahtarlarını kullanarak Windows 10 oturum açma özelliğinin etkinleştirilmesi, Windows 10 ' da kimlik bilgisi sağlayıcısı işlevinin etkinleştirilmesini gerektirir. İki yönden birini etkinleştirin:

- [Intune ile kimlik bilgisi sağlayıcısını etkinleştirme](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Intune dağıtımı önerilen seçenektir.
- [Sağlama paketiyle kimlik bilgisi sağlayıcısını etkinleştirin](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Intune dağıtımı mümkün değilse, yöneticilerin kimlik bilgisi sağlayıcısı işlevini etkinleştirmek için her makinede bir paket dağıtmaları gerekir. Paket yüklemesi aşağıdaki seçeneklerden biriyle gerçekleştirilebilir:
      - Grup ilkesi veya System Center Configuration Manager (SCCM)
      - Windows 10 makinesinde yerel yükleme

### <a name="register-security-keys"></a>Güvenlik anahtarlarını Kaydet

Kullanıcıların, Azure Active Directory katılmış Windows 10 makinelerinde her birine güvenlik anahtarını kaydetmesi gerekir.

Daha fazla bilgi için bkz. [FIDO2 güvenlik anahtarlarının Kullanıcı kaydı ve yönetimi](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).

### <a name="licensing-for-passwordless-authentication"></a>Passwordless kimlik doğrulaması için lisanslama

Passwordless kimlik doğrulaması için ek maliyet yoktur, ancak bazı Önkoşullar Premium bir abonelik gerektirebilir. [Azure Active Directory lisanslama sayfasında](https://azure.microsoft.com/pricing/details/active-directory/)ayrıntılı özellik ve lisanslama bilgileri bölümüne bakın.

## <a name="develop-a-plan"></a>Plan geliştirme

Her kimlik doğrulama yöntemi için iş gereksinimlerinizi ve kullanım örneklerini göz önünde bulundurun. Daha sonra gereksinimlerinize en uygun yöntemi seçin.

### <a name="use-cases"></a>Uygulama alanları

Aşağıdaki tabloda bu proje sırasında uygulanacak kullanım durumları özetlenmektedir.

| Alan | Açıklama |
| --- | --- |
| **Erişim** | Passwordless oturum açma, kurumsal ağ içinde veya dışında bir kurumsal veya kişisel cihazdan kullanılabilir. |
| **Denetim** | Kullanım verileri, yöneticilere neredeyse gerçek zamanlı olarak denetim sağlamak için kullanılabilir. <br> Kullanım verileri en az 29 günde bir veya SıEM aracı kullanıldığında şirket sistemlerine indirilir. |
| **#** | Uygun kimlik doğrulama yöntemine ve ilişkili gruplara yönelik Kullanıcı atamalarının yaşam döngüsü tanımlanmıştır ve izlenir. |
| **Güvenlik** | Uygun kimlik doğrulama yöntemine erişim, Kullanıcı ve Grup atamaları aracılığıyla denetlenir. <br> Yalnızca yetkili kullanıcılar parolasız oturum açma kullanabilir. |
| **Performans** | Erişim atama yayma zaman çizelgeleri belgelenmiştir ve izlenir. <br> Oturum açma süreleri kullanım kolaylığı açısından ölçülür. |
| **Kullanıcı deneyimi** | Kullanıcılar mobil uyumluluğun farkında değildir. <br> Kullanıcılar, kimlik doğrulayıcı uygulamasının parolasız oturum açmasını yapılandırabilir. |
| **Destek** | Kullanıcılar, parolasız oturum açma sorunları için nasıl destek bulacağınızı öğrenirsiniz. |

### <a name="engage-the-right-stakeholders"></a>Doğru paydaşlara katılın

Teknoloji projeleri başarısız olduğunda, genellikle etki, sonuç ve sorumlulukların eşleşmeyen beklentileri nedeniyle oluşur. Bu sınırları önlemek için, doğru proje katılımcıları ile ilgileniyorsanız ve projedeki paydaş rollerinin iyi anlaşıldığından [emin olun](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) .

### <a name="organization-communications"></a>Kuruluş iletişimleri

İletişim, her yeni hizmetin başarısı için önemlidir. Kullanıcıların deneyiminin nasıl değiştirileceği, ne zaman değiştirileceği ve sorunlar yaşandıklarında nasıl destek kazanabilecekleri hakkında daha etkin bir şekilde iletişim kurun.

Son kullanıcılara yönelik iletişimlerinizin şunları eklemesi gerekir:

- [Birleşik güvenlik kaydı deneyimini etkinleştirme](howto-authentication-passwordless-phone.md)
- [Microsoft Authenticator uygulamasını indirme](../user-help/user-help-auth-app-download-install.md)
- [Microsoft Authenticator uygulamasına kaydetme](howto-authentication-passwordless-phone.md)
- [Telefonunuzla oturum açma](../user-help/user-help-auth-app-sign-in.md)

Microsoft, iletişimlerinizin taslağını sağlamak için MFA [iletişim şablonları](https://aka.ms/mfatemplates), self servis parola sıfırlama (SSPR) [iletişim şablonları](https://www.microsoft.com/download/details.aspx?id=56768)ve [Son Kullanıcı belgeleri](../user-help/security-info-setup-signin.md) sağlar. Kullanıcıları, bu sayfadaki güvenlik bilgileri bağlantılarını seçerek doğrudan kaydettirmek üzere [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) gönderebilirsiniz.

### <a name="testing-passwordless"></a>Passwordless test ediliyor

Dağıtımınızın her aşamasında, sonuçların beklendiği gibi olduğunu test etdiğinizden emin olun.

#### <a name="testing-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulamasını test etme

Microsoft Authenticator uygulamayla, passwordless kimlik doğrulaması için örnek test çalışmaları aşağıda verilmiştir

| Senaryo | Beklenen sonuçlar |
| --- | --- |
| Kullanıcı Microsoft Authenticator uygulamayı kaydedebilir | Kullanıcı, uygulamayı aka.ms/mysecurityinfo 'tan kaydedebilir |
| Kullanıcı telefonla oturum açmayı etkinleştirebilir | İş için yapılandırılmış telefon oturumu açma hesabı |
| Kullanıcı, telefonla oturum açma ile bir uygulamaya erişebilir | Kullanıcı telefon oturum açma akışından geçer ve belirlenen uygulamaya ulaşır. |
| Azure Active Directory portalındaki kimlik doğrulama yöntemleri ekranında Microsoft Authenticator parolasız oturum açma 'yı kapatarak telefonla oturum açma kaydını test edin | Daha önce etkinleştirilen kullanıcılar Microsoft Authenticator 'ten parolasız oturum açma kullanamaz. |
| Microsoft Authenticator uygulamasından telefonla oturum açma kaldırılıyor | İş hesabı artık Microsoft Authenticator kullanılamıyor |

#### <a name="testing-security-keys"></a>Güvenlik anahtarlarını test etme

Aşağıda, güvenlik anahtarlarına sahip parolasız kimlik doğrulaması için örnek test çalışmaları verilmiştir.

**Windows 10 cihazlarına Azure Active Directory için passwordless FıDO oturum açma**

| Senaryo | Beklenen sonuçlar |
| --- | --- |
| Kullanıcı FIDO2 cihazını kaydedebilir (1809) | Kullanıcı, FIDO2 cihaz ayarlarını > Hesaplar > oturum açma seçenekleri > güvenlik anahtarı ' nı kullanarak kaydedebilir |
| Kullanıcı FIDO2 cihazını sıfırlayabilir (1809) | Kullanıcı üretici yazılımını kullanarak FIDO2 cihazını sıfırlayabilir |
| Kullanıcı FIDO2 cihazla oturum açabilir (1809) | Kullanıcı oturum açma penceresinde güvenlik anahtarı seçebilir ve başarıyla oturum açabilir. |
| Kullanıcı FIDO2 cihazını kaydedebilir (1903) | Kullanıcı, FIDO2 cihaz ayarlarını > Hesaplar > oturum açma seçenekleri > güvenlik anahtarı ' na kaydedebilir |
| Kullanıcı FIDO2 cihazını sıfırlayabilir (1903) | Kullanıcı, FIDO2 cihaz ayarlarını > Hesaplar > oturum açma seçenekleri > güvenlik anahtarı ' nda sıfırlayabilir |
| Kullanıcı FIDO2 cihazla oturum açabilir (1903) | Kullanıcı oturum açma penceresinde güvenlik anahtarı seçebilir ve başarıyla oturum açabilir. |

**Azure AD Web Apps 'te passwordless FIDO oturum açma**

| Senaryo | Beklenen sonuçlar |
| --- | --- |
| Kullanıcı Microsoft Edge kullanarak aka.ms/mysecurityinfo 'e FIDO2 cihazını kaydedebilir | Kayıt başarılı olmalıdır |
| Kullanıcı, FIDO2 cihazını Firefox kullanarak aka.ms/mysecurityinfo adresinden kaydedebilir | Kayıt başarılı olmalıdır |
| Kullanıcı Microsoft Edge kullanarak FIDO2 cihazını kullanarak OneDrive online 'da oturum açabilir | Oturum açma başarılı olmalıdır |
| Kullanıcı FIDO2 cihazını Firefox kullanarak OneDrive online 'da oturum açabilir | Oturum açma başarılı olmalıdır |
| Azure Active Directory portalındaki kimlik doğrulama yöntemleri dikey penceresinde FIDO2 güvenlik anahtarlarını kapatarak test FIDO2 cihaz kaydı geri alınıyor. | Kullanıcılardan güvenlik anahtarını kullanarak oturum açması istenir, bu, içinde başarıyla oturum açacaktır ve bir hata görüntülenir: "Şirket ilkeniz, oturum açmak için farklı bir yöntem kullanmanızı gerektirir". Daha sonra kullanıcıların farklı bir yöntem seçebilmeleri ve başarıyla oturum açması gerekir. Pencereyi kapatın ve aynı hata iletisini görmediğinden emin olmak için yeniden oturum açın. |

### <a name="auditing-passwordless"></a>Parolasız denetim

Azure AD, teknik ve iş öngörüleri sağlayan raporlar içerir. İşletmenizin ve teknik uygulama sahiplerinizin sahipliğini varsaymasını ve kuruluşunuzun gereksinimlerine göre bu raporları kullanmasını sağlayabilirsiniz.

Azure Active Directory portalındaki kimlik doğrulama yöntemleri bölümü, yöneticilerin parolasız kimlik bilgileri için ayarları etkinleştirebileceği ve yönetebilecekleri yerdir.

Azure AD, şu durumlarda denetim günlüklerine girdi ekler:

- Yönetici, kimlik doğrulama yöntemleri bölümünde değişiklik yapar.
- Kullanıcı Azure Active Directory içindeki kimlik bilgileriyle ilgili her türlü değişikliği yapar.

Aşağıdaki tabloda tipik raporlama senaryolarının bazı örnekleri verilmiştir.

|   | Riski yönetin | Üretkenliği artırın | İdare ve uyum |
| --- | --- | --- | --- |
| **Rapor türleri** | Kimlik doğrulama yöntemleri-Birleşik güvenlik kaydı için kaydedilen kullanıcılar | Kimlik doğrulama yöntemleri – uygulama bildirimi için kaydolan kullanıcılar | Oturum açma işlemleri: kiracıya kimlerin eriştiğini ve nasıl yapıldığını gözden geçirin |
| **Olası eylemler** | Hedef kullanıcılar henüz kaydedilmedi | Microsoft Authenticator uygulamasının veya güvenlik anahtarlarının sürücü benimseme | Yöneticiler için erişimi iptal etme veya ek güvenlik ilkeleri zorlama |

**Azure AD, en fazla 30 günlük denetim verilerini korur** ve verileri Azure Yönetim PORTALı veya API aracılığıyla analiz sistemlerinize indirmeniz için kullanılabilir hale getirir. Kuruluşunuz daha uzun bekletme gerektiriyorsa, günlüklerin [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), splunk veya Sumo Logic gıbı bır SIEM aracına aktarılması ve tüketilmesi gerekir. [Erişim ve kullanım raporlarınızı görüntüleme hakkında daha fazla bilgi edinin](../reports-monitoring/overview-reports.md).

Kullanıcılar, [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)giderek kimlik bilgilerini kaydedebilir ve yönetebilir. Bu bağlantı, kullanıcıları birleştirilmiş SSPR/MFA kayıt deneyimi aracılığıyla etkinleştirilen Son Kullanıcı kimlik bilgileri yönetim deneyimine yönlendirir. FIDO2 güvenlik cihazlarının herhangi bir kaydı veya bir kullanıcı tarafından kimlik doğrulama yöntemlerine yapılan değişiklikler Azure Active Directory Denetim günlüklerine kaydedilir.

Kullanıcılar bir güvenlik anahtarındaki hesabı etkinleştirir veya devre dışı bırakır veya Windows 10 makinelerinde güvenlik anahtarı için ikinci faktörü sıfırlarsa, güvenlik günlüğüne bir giriş eklenir ve aşağıdaki olay kimlikleri altında bulunur: 4670, 5382.

### <a name="plan-for-rollback"></a>Geri alma planı

Passwordless kimlik doğrulaması, son kullanıcılar üzerinde en az etkiyle hafif bir özelliktir ancak geri almak gerekebilir.

Geri dönme, yöneticinin Azure Active Directory portalında oturum açmasını, istenen güçlü kimlik doğrulama yöntemlerini seçmesini ve Etkinleştir seçeneğini ' Hayır ' olarak değiştirmesini gerektirir. Bu işlem, tüm kullanıcılar için parolasız işlevselliği devre dışı bırakır.

FIDO2 güvenlik cihazlarını zaten kaydetmiş olan kullanıcıların, bir sonraki oturum açma sırasında güvenlik cihazını kullanması istenir ve ardından aşağıdaki hatayı görür:

![oturum açmak için farklı bir yol seçin](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>Pilot planı

Passwordless kimlik doğrulaması dağıttığınızda, önce bir veya daha fazla pilot grubunu etkinleştirmeniz gerekir. Bu amaçla özel olarak [gruplar oluşturabilirsiniz](../fundamentals/active-directory-groups-create-azure-portal.md) . Pilot 'a katılacak kullanıcıları gruplara ekleyin. Ardından, Seçili gruplar için yeni parolasız kimlik doğrulama yöntemlerini etkinleştirin.

Gruplar, şirket içi bir dizinden veya Azure AD 'den eşitlenebilir. Pilot 'larınızın sonuçlarıyla memnun olduktan sonra, tüm kullanıcılar için passwordless kimlik doğrulaması üzerinde geçiş yapabilirsiniz.

Dağıtım planları sayfasında [bir pilot Için en iyi uygulamalar](https://aka.ms/deploymentplans) bölümüne bakın.

## <a name="deploy-passwordless-authentication"></a>Passwordless kimlik doğrulaması dağıt

Aşağıdaki seçtiğiniz metoda hizalanmış adımları izleyin.

### <a name="required-administrative-roles"></a>Gerekli yönetim rolleri

| Azure AD rolü | Açıklama |
| --- | --- |
| Kimlik doğrulama Yöneticisi | Kimlik doğrulama yöntemlerini uygulayabilir ve yönetebilecek en az ayrıcalıklı rol |
| Kullanıcı | Cihazda Authenticator uygulamasını yapılandırmak veya Web veya Windows 10 oturum açma için güvenlik anahtarı cihazını kaydetmek için en az ayrıcalıklı rol. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulamayla telefon oturumu açma

Microsoft Authenticator uygulamayı kuruluşunuzda bir passwordless kimlik doğrulama yöntemi olarak etkinleştirmek için, makaledeki adımları izleyin, [Microsoft Authenticator uygulamayla passwordless oturum açma özelliğini etkinleştirin](howto-authentication-passwordless-phone.md) .

### <a name="deploy-fido2-security-key-sign-in"></a>FIDO2 güvenlik anahtarını dağıtma oturum açma

Bu makaledeki adımları izleyerek, FIDO2 güvenlik anahtarlarını kuruluşunuzda passwordless kimlik doğrulama yöntemleri olarak etkinleştirmek için [Azure AD 'de passwordless güvenlik anahtarı oturum açma özelliğini etkinleştirin](howto-authentication-passwordless-security-key.md) .

### <a name="troubleshoot-phone-sign-in"></a>Telefonla oturum açma sorunlarını giderme

| Senaryo | Çözüm |
| --- | --- |
| Kullanıcı birleştirilmiş kayıt gerçekleştiremez | [Birleşik kaydın](concept-registration-mfa-sspr-combined.md) etkinleştirildiğinden emin olun. |
| Kullanıcı telefonla oturum açma kimlik doğrulayıcı uygulamasında etkinleştirilemiyor | Kullanıcının dağıtım kapsamında olduğundan emin olun |
| Kullanıcı, passwordless kimlik doğrulaması kapsamında DEĞIL, ancak tamamlanmayan parolasız oturum açma seçeneği ile sunulmaktadır. | Bu senaryo, Kullanıcı, oluşturulduktan sonra uygulamanın telefonda oturum açmasını etkinleştirmişse oluşur. <br> Oturum açmayı etkinleştirmek için: kullanıcıyı, parolasız oturum açma için etkinleştirilen kullanıcıların kapsamına ekleyin. <br> Oturum açmayı engellemek için: kullanıcının bu uygulamanın kimlik bilgilerini kaldırmasını sağlayabilirsiniz. |

### <a name="troubleshoot-security-key-sign-in"></a>Güvenlik anahtarı oturum açma sorunlarını giderme

| Senaryo | Çözüm |
| --- | --- |
| Kullanıcı birleştirilmiş kayıt gerçekleştiremez | [Birleşik kaydın](concept-registration-mfa-sspr-combined.md) etkinleştirildiğinden emin olun. |
| Kullanıcı [güvenlik ayarlarına](https://aka.ms/mysecurityinfo) bir güvenlik anahtarı ekleyemez | [Güvenlik anahtarlarının](howto-authentication-passwordless-security-key.md) etkinleştirildiğinden emin olun. |
| Kullanıcı Windows 10 oturum açma seçeneklerinde güvenlik anahtarı ekleyemiyor | [Windows oturum açma güvenlik anahtarlarının oturum açmasını sağlayın](howto-authentication-passwordless-enable.md) |
| **Hata iletisi**: bu tarayıcının veya IŞLETIM sisteminin FIDO2 güvenlik anahtarlarını desteklemediğini algıladık. | Passwordless FIDO2 güvenlik cihazları, Windows 10 sürüm 1809 veya üzeri sürümlerde yalnızca desteklenen tarayıcılarda (Microsoft Edge, Firefox sürüm 67) kaydedilebilir. |
| **Hata iletisi**: Şirket ilkeniz, oturum açmak için farklı bir yöntem kullanmanızı gerektirir. | Kiracıda güvenlik anahtarlarının etkin olmadığından emin değil. |
| Kullanıcı Windows 10 sürüm 1809 üzerinde güvenlik anahtarımı yönemedi | Sürüm 1809, FIDO2 anahtar satıcısı tarafından sağlanmış olan güvenlik anahtarı yönetim yazılımını kullanmanızı gerektirir. Destek için satıcıya başvurun. |
| FIDO2 güvenlik anahtarımın bozuk olabileceğini sanırım, nasıl test edebilirim? | [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)gidin, bir test hesabı için kimlik bilgilerini girin, şüpheli güvenlik anahtarını takın, ekranın sağ üst kısmındaki "+" düğmesine tıklayın, Oluştur ' a tıklayın ve oluşturma işlemi boyunca ilerleyin. Bu senaryo başarısız olursa, cihazınız bozulmuş olabilir. |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD 'de oturum açma için passwordless güvenlik anahtarlarını etkinleştirin](howto-authentication-passwordless-security-key.md)
- [Microsoft Authenticator uygulamayla passwordless oturum açmayı etkinleştirme](howto-authentication-passwordless-phone.md)
- [Kimlik doğrulama yöntemleri kullanımı & Öngörüler hakkında daha fazla bilgi edinin](howto-authentication-methods-usage-insights.md)
