---
title: Eski kimlik doğrulamasını engelle-Azure Active Directory
description: Azure AD koşullu erişim kullanarak eski kimlik doğrulamasını engelleyerek güvenlik durunuzu geliştirmeyi öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84c8b82219f2b2aea39bbcd23f030243d9ea8635
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861814"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Nasıl yapılır: Koşullu Erişimle Azure AD'de eski kimlik doğrulama girişimlerini engelleme   

Kullanıcılarınıza bulut uygulamalarınıza kolay erişim sağlamak için Azure Active Directory (Azure AD) eski kimlik doğrulaması dahil olmak üzere çok çeşitli kimlik doğrulama protokollerini destekler. Ancak, eski protokoller Multi-Factor Authentication 'ı (MFA) desteklemez. MFA birçok ortamda, kimlik hırsızlığına yönelik ortak bir gereksinimdir. 

Alex Weinert, Microsoft 'taki kimlik Güvenliği Direktörü, 12 Mart 2020 ' de, [kuruluşunuzda eski kimlik doğrulamasını engellemek Için yeni araçlar](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) , kuruluşların eski kimlik doğrulamasını nasıl engelleyebileceklerini ve Microsoft 'un bu görevi gerçekleştirmek için sağladığı diğer araçların ne olduğunu vurgular:

> MFA 'nın etkili olabilmesi için eski kimlik doğrulamasını da engellemeniz gerekir. Bunun nedeni, POP, SMTP, IMAP ve MAPI gibi eski kimlik doğrulama protokollerinin MFA 'ya zorlayamadığı için, bu nedenle bunları, duyuru işlemleri için tercih edilen giriş noktaları kuruluşunuza saldırıyor...
> 
>... Azure Active Directory (Azure AD) trafiği analizinden gelen eski kimlik doğrulama numaraları Stark:
> 
> - Parola spreyi saldırılarının yüzde 99 ' sinden fazlası eski kimlik doğrulama protokollerini kullanır
> - Credential stuffing saldırılarının yüzde 97 ' sinden fazlası eski kimlik doğrulamasını kullanır
> - Eski kimlik doğrulama 67 deneyimini devre dışı bırakmış olan kuruluşların Azure AD hesapları, eski kimlik doğrulamasının etkin olduğu kadardan daha az sayıda kombinasyon
>

Ortamınız, kiracınızın korumasını geliştirmek için eski kimlik doğrulamasını engellemeye hazırsanız, bu hedefi koşullu erişimle gerçekleştirebilirsiniz. Bu makalede, kiracınız için eski kimlik doğrulamasını engelleyen koşullu erişim ilkelerini nasıl yapılandırabileceğiniz açıklanmaktadır. Koşullu erişimi içeren lisansları olmayan müşteriler, eski kimlik doğrulamasını engellemek için [güvenlik varsayılanlarını](../fundamentals/concept-fundamentals-security-defaults.md)kullanabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, Azure AD koşullu erişim 'in [temel kavramlarıyla](overview.md) ilgili bilgi sahibi olduğunuz varsayılır.

## <a name="scenario-description"></a>Senaryo açıklaması

Azure AD, eski kimlik doğrulama dahil olmak üzere en yaygın olarak kullanılan kimlik doğrulama ve yetkilendirme protokollerini destekler. Eski kimlik doğrulaması, temel kimlik doğrulaması kullanan protokollerin anlamına gelir. Genellikle, bu protokoller herhangi bir tür ikinci faktör kimlik doğrulamasını zorunlu kılamaz. Eski kimlik doğrulamasına dayalı uygulamalar için örnekler şunlardır:

- Daha eski Microsoft Office uygulamalar
- POP, IMAP ve SMTP gibi posta protokollerini kullanan uygulamalar

Tek faktörlü kimlik doğrulaması (örneğin, Kullanıcı adı ve parola) bu günlerde yeterli değil. Parolaların tahmin edilmesi kolay olduğu ve bizim (insanların) iyi parola seçerken kötü olduğu için parolalar hatalı. Parolalar ayrıca kimlik avı ve parola spreyi gibi çeşitli saldırılara karşı savunmasızdır. Parola tehditlerine karşı korumak için yapabileceğiniz en kolay şeylerden biri Multi-Factor Authentication (MFA) uygulamasıdır. MFA ile, bir saldırgan kullanıcının parolasını elinde bıraksa bile, parola kimlik doğrulaması ve verilere erişmek için tek başına yeterli değildir.

Eski kimlik doğrulaması kullanan uygulamaların kiracının kaynaklarına erişmesini nasıl önleyebilirim? Öneri yalnızca bir koşullu erişim ilkesiyle engellenmeniz önerilir. Gerekirse, yalnızca belirli kullanıcıların ve belirli ağ konumlarının eski kimlik doğrulamasına dayalı uygulamalar kullanmasına izin verebilirsiniz.

Koşullu erişim ilkeleri, ilk faktör kimlik doğrulaması tamamlandıktan sonra zorlanır. Bu nedenle, koşullu erişim hizmet reddi (DoS) saldırıları gibi senaryolar için birinci hat savunma olarak tasarlanmamıştır, ancak erişimi anlamak için bu olaylardaki sinyalleri (örneğin, oturum açma risk düzeyi, isteğin konumu vb.) kullanabilir.

## <a name="implementation"></a>Uygulama

Bu bölümde, eski kimlik doğrulamasını engellemek için bir koşullu erişim ilkesinin nasıl yapılandırılacağı açıklanmaktadır. 

### <a name="legacy-authentication-protocols"></a>Eski kimlik doğrulama protokolleri

Aşağıdaki seçenekler eski kimlik doğrulama protokolleri olarak kabul edilir

- Kimliği doğrulanmış SMTP-e-posta iletileri göndermek için POP ve IMAP istemcileri tarafından kullanılır.
- Otomatik bulma-Exchange Online 'da posta kutularını bulmak ve bağlamak için Outlook ve EAS istemcileri tarafından kullanılır.
- Exchange ActiveSync (EAS)-Exchange Online 'daki posta kutularına bağlanmak için kullanılır.
- Exchange Online PowerShell-uzak PowerShell ile Exchange Online 'a bağlanmak için kullanılır. Exchange Online PowerShell için temel kimlik doğrulamasını engellerseniz, bağlanmak için Exchange Online PowerShell modülünü kullanmanız gerekir. Yönergeler için bkz. [Multi-Factor Authentication kullanarak Exchange Online PowerShell 'e bağlanma](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Exchange Web Hizmetleri (EWS)-Outlook, Mac için Outlook ve üçüncü taraf uygulamalar tarafından kullanılan bir programlama arabirimidir.
- IMAP4-IMAP e-posta istemcileri tarafından kullanılır.
- HTTP üzerinden MAPI (MAPI/HTTP)-Outlook 2010 ve üzeri tarafından kullanılır.
- Çevrimdışı adres defteri (OAB)-Outlook tarafından indirilen ve kullanılan adres listesi koleksiyonlarının bir kopyası.
- Outlook her yerde (HTTP üzerinden RPC)-Outlook 2016 ve öncesi tarafından kullanılır.
- Outlook hizmeti-Windows 10 için posta ve takvim uygulaması tarafından kullanılır.
- POP3-POP e-posta istemcileri tarafından kullanılır.
- Raporlama Web Hizmetleri-Exchange Online 'daki rapor verilerini almak için kullanılır.
- Diğer istemciler-eski kimlik doğrulamasından yararlanarak tanımlanan diğer protokoller.

Bu kimlik doğrulama protokolleri ve hizmetleri hakkında daha fazla bilgi için bkz. [Azure Active Directory portalındaki oturum açma etkinlik raporları](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Eski kimlik doğrulama kullanımını tanımla

Dizininizde eski kimlik doğrulamasını engelleyebilmeniz için önce, kullanıcılarınızın eski kimlik doğrulaması kullanan uygulamalar olup olmadığını ve bunun genel dizininizi nasıl etkileyeceğini anlamanız gerekir. Azure AD oturum açma günlükleri, eski kimlik doğrulaması kullanıp kullandığınızı anlamak için kullanılabilir.

1. **Azure Portal**  >  **Azure Active Directory**  >  **oturum açma** işlemleri ' ne gidin.
1. **Sütunlar**  >  **istemci uygulaması**' na tıklanarak gösterilmezse, istemci uygulaması sütununu ekleyin.
1. **Filtre Ekle**  >  **Istemci uygulaması** > eski tüm kimlik doğrulama protokollerini seçin. Seçimlerinizi uygulamak ve iletişim kutusunu kapatmak için filtreleme iletişim kutusunun dışında ' yı seçin.
1. [Yeni oturum açma etkinliği raporları önizlemesini](../reports-monitoring/concept-all-sign-ins.md)etkinleştirdiyseniz, yukarıdaki adımları **Kullanıcı oturum açma işlemleri (etkileşimli olmayan)** sekmesinde da yineleyin.

Filtreleme yalnızca eski kimlik doğrulama protokolleri tarafından yapılan oturum açma girişimlerini gösterir. Her bir bireysel oturum açma girişimine tıkladığınızda daha fazla ayrıntı gösterilecektir. **Temel bilgi** sekmesindeki **istemci uygulaması** alanı, hangi eski kimlik doğrulama protokolünün kullanıldığını gösterir.

Bu Günlükler, hangi kullanıcıların eski kimlik doğrulamasına bağlı olduğunu ve hangi uygulamaların kimlik doğrulama isteklerini yapmak için eski protokolleri kullandığını gösterir. Bu günlüklerde görünmeyen ve eski kimlik doğrulaması kullanmayan kullanıcılar için, yalnızca bu kullanıcılar için bir koşullu erişim ilkesi uygulayın.

## <a name="block-legacy-authentication"></a>Eski kimlik doğrulamasını engelleme 

Eski kimlik doğrulamasını engellemek için koşullu erişim ilkelerini kullanmanın iki yolu vardır.

- [Eski kimlik doğrulamasını doğrudan engelleme](#directly-blocking-legacy-authentication)
- [Eski kimlik doğrulaması dolaylı olarak engelleniyor](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Eski kimlik doğrulamasını doğrudan engelleme

Tüm kuruluşunuz genelinde eski kimlik doğrulamasını engellemenin en kolay yolu, eski kimlik doğrulama istemcilerine özel olarak uygulanan bir koşullu erişim ilkesi yapılandırarak erişimi engeller. İlkeye Kullanıcı ve uygulama atarken, eski kimlik doğrulaması kullanarak oturum açması gereken kullanıcıları ve hizmet hesaplarını dışlayıp hariç tutdığınızdan emin olun. **Exchange ActiveSync istemcileri** ve **diğer istemciler**' i seçerek istemci uygulamaları koşulunu yapılandırın. Bu istemci uygulamalarına erişimi engellemek için erişim denetimlerini erişimi engelleyecek şekilde yapılandırın.

![Eski kimlik doğrulamasını engellemek için yapılandırılmış istemci uygulamaları koşulu](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Eski kimlik doğrulaması dolaylı olarak engelleniyor

Kuruluşunuz tüm kuruluş genelinde eski kimlik doğrulamasını engellemeye uygun olmasa bile, eski kimlik doğrulamasını kullanan oturum açma işlemlerinin, çok faktörlü kimlik doğrulaması veya uyumlu/karma Azure AD 'ye katılmış cihazları gerektirme gibi denetim izni gerektiren ilkeleri atmadığından emin olmanız gerekir. Kimlik doğrulama sırasında eski kimlik doğrulama istemcileri MFA, cihaz uyumluluğu veya Azure AD 'ye JOIN durum bilgilerini desteklemez. Bu nedenle, izin denetimlerini karşılayamayan eski kimlik doğrulama tabanlı oturum açma işlemleri engellenmeleri için tüm istemci uygulamalarına denetim izni veren ilkeler uygulayın. Ağustos 2020 ' deki istemci uygulamaları koşulunun genel kullanıma sunulduğuna göre, yeni oluşturulan koşullu erişim ilkeleri tüm istemci uygulamalarına varsayılan olarak uygulanır.

![İstemci uygulamaları koşulu varsayılan yapılandırması](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>Bilmeniz gerekenler

**Diğer istemcileri** kullanarak erişimin engellenmesi, Exchange Online PowerShell ve Dynamics 365 ' i temel kimlik doğrulaması kullanarak engeller.

**Diğer istemciler** için bir ilkeyi yapılandırmak, kuruluşun tamamını spconnect gibi belirli istemcilerden engeller. Eski istemciler beklenmeyen yollarla kimlik doğrulaması yaptığından bu blok oluşur. Sorun, eski Office istemcileri gibi başlıca Office uygulamalarına uygulanmaz.

İlkenin etkili olması 24 saate kadar sürebilir.

**Diğer istemciler** koşulu için kullanılabilir tüm izin denetimlerini seçebilirsiniz; Ancak, son kullanıcı deneyimi her zaman aynı engellenen erişimdir.

### <a name="sharepoint-online-and-b2b-guest-users"></a>SharePoint Online ve B2B Konuk kullanıcıları

SharePoint Online 'a eski kimlik doğrulama aracılığıyla B2B Kullanıcı erişimini engellemek için, kuruluşların PowerShell komutunu kullanarak SharePoint 'te eski kimlik doğrulamasını devre dışı bırakmasına `Set-SPOTenant` ve `-LegacyAuthProtocolsEnabled` parametresini olarak ayarına izin vermelidir `$false` . Bu parametreyi ayarlama hakkında daha fazla bilgi, [set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant) Ile Ilgili SharePoint PowerShell başvuru belgesinde bulunabilir

## <a name="next-steps"></a>Sonraki adımlar

- [Koşullu erişim yalnızca rapor modunu kullanarak etkiyi belirleme](howto-conditional-access-insights-reporting.md)
- Koşullu erişim ilkelerini henüz yapılandırmaya alışmıyorsanız, bir örnek için [Azure Active Directory Koşullu erişimi olan belirli uygulamalar IÇIN MFA isteme](../authentication/tutorial-enable-azure-mfa.md) konusuna bakın.
- Modern kimlik doğrulama desteği hakkında daha fazla bilgi için bkz. [modern kimlik doğrulama office 2013 ve office 2016 istemci uygulamaları Için nasıl kullanılır](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
- [Microsoft 365 kullanarak e-posta göndermek için çok işlevli bir cihaz veya uygulamayı ayarlama](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)