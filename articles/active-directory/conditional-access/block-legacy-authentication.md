---
title: Koşullu erişim ile Azure Active Directory (Azure AD) için eski kimlik doğrulamasını engelleme | Microsoft Docs
description: Azure AD koşullu erişim kullanarak eski kimlik doğrulamasını engelleyerek güvenlik durunuzu geliştirmeyi öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4e4dc33d670c5f6c5ebefa21ccf1a1ff941e913
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024584"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Nasıl yapılır: koşullu erişimle Azure AD 'de eski kimlik doğrulamasını engelleme   

Kullanıcılarınıza bulut uygulamalarınıza kolay erişim sağlamak için Azure Active Directory (Azure AD) eski kimlik doğrulaması dahil olmak üzere çok çeşitli kimlik doğrulama protokollerini destekler. Ancak, eski protokoller Multi-Factor Authentication 'ı (MFA) desteklemez. MFA birçok ortamda, kimlik hırsızlığına yönelik ortak bir gereksinimdir. 

Ortamınız, kiracınızın korumasını geliştirmek için eski kimlik doğrulamasını engellemeye hazırsanız, bu hedefi koşullu erişimle gerçekleştirebilirsiniz. Bu makalede, kiracınız için eski kimlik doğrulamasını engelleyen koşullu erişim ilkelerini nasıl yapılandırabileceğiniz açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, hakkında bilgi sahibi olduğunuz varsayılmaktadır: 

- Azure AD koşullu erişim 'in [temel kavramları](overview.md) 
- Azure portal koşullu erişim ilkelerini yapılandırmaya yönelik [en iyi yöntemler](best-practices.md)

## <a name="scenario-description"></a>Senaryo açıklaması

Azure AD, eski kimlik doğrulama dahil olmak üzere en yaygın olarak kullanılan kimlik doğrulama ve yetkilendirme protokollerini destekler. Eski kimlik doğrulaması, temel kimlik doğrulaması kullanan protokollerin anlamına gelir. Genellikle, bu protokoller herhangi bir tür ikinci faktör kimlik doğrulamasını zorunlu kılamaz. Eski kimlik doğrulamasına dayalı uygulamalar için örnekler şunlardır:

- Daha eski Microsoft Office uygulamalar
- POP, IMAP ve SMTP gibi posta protokollerini kullanan uygulamalar

Tek faktörlü kimlik doğrulaması (örneğin, Kullanıcı adı ve parola) bu günlerde yeterli değil. Parolaların tahmin edilmesi kolay olduğu ve bizim (insanların) iyi parola seçerken kötü olduğu için parolalar hatalı. Parolalar ayrıca kimlik avı ve parola spreyi gibi çeşitli saldırılara karşı savunmasız kalır. Parola tehditlerine karşı korumak için yapabileceğiniz en kolay şeylerden biri MFA uygulamasıdır. MFA ile, bir saldırgan kullanıcının parolasını elinde bıraksa bile, parola kimlik doğrulaması ve verilere erişmek için tek başına yeterli değildir.

Eski kimlik doğrulaması kullanan uygulamaların kiracının kaynaklarına erişmesini nasıl önleyebilirim? Öneri yalnızca bir koşullu erişim ilkesiyle engellenmeniz önerilir. Gerekirse, yalnızca belirli kullanıcıların ve belirli ağ konumlarının eski kimlik doğrulamasına dayalı uygulamalar kullanmasına izin verebilirsiniz.

Koşullu erişim ilkeleri, ilk faktör kimlik doğrulaması tamamlandıktan sonra zorlanır. Bu nedenle, koşullu erişim hizmet reddi (DoS) saldırıları gibi senaryolar için birinci hat savunma olarak tasarlanmamıştır, ancak erişimi anlamak için bu olaylardaki sinyalleri (örneğin, oturum açma risk düzeyi, isteğin konumu vb.) kullanabilir.

## <a name="implementation"></a>Uygulama

Bu bölümde, eski kimlik doğrulamasını engellemek için bir koşullu erişim ilkesinin nasıl yapılandırılacağı açıklanmaktadır. 

### <a name="identify-legacy-authentication-use"></a>Eski kimlik doğrulama kullanımını tanımla

Dizininizde eski kimlik doğrulamasını engelleyebilmeniz için önce, kullanıcılarınızın eski kimlik doğrulaması kullanan uygulamalar olup olmadığını ve bunun genel dizininizi nasıl etkileyeceğini anlamanız gerekir. Azure AD oturum açma günlükleri, eski kimlik doğrulaması kullanıp kullandığınızı anlamak için kullanılabilir.

1. @No__t 3**oturum açma** **Azure Active Directory** **Azure Portal** >  ' e gidin.
1. @No__t-1**Istemci uygulaması** **sütunlarına**tıklanarak gösterilmezse istemci uygulaması sütununu ekleyin.
1. @No__t-1**Istemci uygulaması** Için **filtre ekleyin**> **diğer Istemciler** için tüm seçenekleri belirleyip **Uygula**' ya tıklayın.

Filtreleme yalnızca eski kimlik doğrulama protokolleri tarafından yapılan oturum açma girişimlerini gösterir. Her bir bireysel oturum açma girişimine tıkladığınızda ek ayrıntılar gösterilecektir. **Temel bilgi** sekmesindeki **istemci uygulaması** alanı, hangi eski kimlik doğrulama protokolünün kullanıldığını gösterir.

Bu Günlükler, hangi kullanıcıların eski kimlik doğrulamasına bağlı olduğunu ve hangi uygulamaların kimlik doğrulama isteklerini yapmak için eski protokolleri kullandığını gösterir. Bu günlüklerde görünmeyen ve eski kimlik doğrulaması kullanmayan kullanıcılar için, yalnızca bu kullanıcılar için bir koşullu erişim ilkesi uygulayın.

### <a name="block-legacy-authentication"></a>Eski kimlik doğrulamasını engelleme 

Koşullu erişim ilkesinde, kaynaklarınıza erişmek için kullanılan istemci uygulamalarına bağlı bir koşul belirleyebilirsiniz. İstemci uygulamaları koşulu, **mobil uygulamalar ve Masaüstü istemcileri**için **diğer istemcileri** seçerek eski kimlik doğrulamasını kullanan uygulamalar için kapsamı daraltmanızı sağlar.

![Diğer istemciler](./media/block-legacy-authentication/01.png)

Bu uygulamalara erişimi engellemek için **erişimi engelle**' yi seçmeniz gerekir.

![Erişimi engelle](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Kullanıcıları ve bulut uygulamalarını seçin

Kuruluşunuz için eski kimlik doğrulamasını engellemek istiyorsanız, şunu seçerek bunu deneyebilirsiniz:

- Tüm kullanıcılar
- Tüm bulut uygulamaları
- Erişimi engelle

![Atamalar](./media/block-legacy-authentication/03.png)

Bu yapılandırma koşullu erişim ilkelerine yönelik [en iyi uygulamaları](best-practices.md) ihlal ettiğinden, Azure bu şekilde bir ilke oluşturmanızı önleyen bir güvenlik özelliğine sahiptir.
 
![İlke yapılandırması desteklenmiyor](./media/block-legacy-authentication/04.png)

*Tüm kullanıcıların ve tüm bulut uygulamalarının* kiracınızda oturum açmasını engellemek mümkün olduğundan, güvenlik özelliği gereklidir. En düşük uygulama gereksinimini karşılamak için en az bir kullanıcıyı dışarıda bırakmanız gerekir. Ayrıca bir dizin rolü de dışlayabilirsiniz.

![İlke yapılandırması desteklenmiyor](./media/block-legacy-authentication/05.png)

İlkenize bir Kullanıcı dışlayarak bu güvenlik özelliğini karşılamanız gerekir. İdeal olarak, [Azure AD 'de birkaç acil erişim yönetim hesabı](../users-groups-roles/directory-emergency-access.md) tanımlamanız ve bunları ilkenize dışlayamazsınız.

## <a name="policy-deployment"></a>İlke dağıtımı

İlkenizi üretime eklemeden önce şunları yapın:
 
- **Hizmet hesapları** -konferans odası telefonları gibi hizmet hesapları veya cihazlar tarafından kullanılan Kullanıcı hesaplarını belirler. Bu hesapların güçlü parolalara sahip olduğundan emin olun ve bunları dışlanan bir gruba ekleyin.
- **Oturum açma raporları** -oturum açma raporunu gözden geçirin ve **diğer istemci** trafiğini arayın. En iyi kullanımı belirler ve neden kullanımda olduğunu araştırın. Genellikle trafik, modern kimlik doğrulaması kullanmayan eski Ofis istemcileri veya bazı üçüncü taraf posta uygulamaları tarafından oluşturulur. Kullanım kullanımını Bu uygulamalardan uzağa taşımaya yönelik bir plan yapın veya etki düşükse, kullanıcılarınıza bu uygulamaları artık kullanamazlar.
 
Daha fazla bilgi için bkz. [Yeni bir Ilkeyi nasıl dağıtmanız gerekir?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Bilmeniz gerekenler

**Diğer istemcileri** kullanarak erişimin engellenmesi, Exchange Online PowerShell ve Dynamics 365 ' i temel kimlik doğrulaması kullanarak engeller.

**Diğer istemciler** için bir ilkeyi yapılandırmak, kuruluşun tamamını spconnect gibi belirli istemcilerden engeller. Eski istemciler beklenmeyen yollarla kimlik doğrulaması yaptığından bu blok oluşur. Sorun, eski Office istemcileri gibi başlıca Office uygulamalarına uygulanmaz.

İlkenin etkili olması 24 saate kadar sürebilir.

**Diğer istemciler** koşulu için kullanılabilir tüm izin denetimlerini seçebilirsiniz; Ancak, son kullanıcı deneyimi her zaman aynı engellenen erişimdir.

**Diğer istemciler** koşulunu kullanarak eski kimlik doğrulamasını engellerseniz, cihaz platformunu ve konum koşulunu da ayarlayabilirsiniz. Örneğin, mobil cihazlar için yalnızca eski kimlik doğrulamasını engellemek istiyorsanız, şu seçeneği belirleyerek **cihaz platformları** koşulunu ayarlayın:

- Android
- iOS
- Windows Phone

![İlke yapılandırması desteklenmiyor](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Sonraki adımlar

- Koşullu erişim ilkelerini henüz yapılandırmaya alışmıyorsanız, bir örnek için [Azure Active Directory Koşullu erişimi olan belirli uygulamalar IÇIN MFA isteme](app-based-mfa.md) konusuna bakın.
- Modern kimlik doğrulama desteği hakkında daha fazla bilgi için bkz. [modern kimlik doğrulama office 2013 ve office 2016 istemci uygulamaları Için nasıl kullanılır](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
