---
title: B2B işbirliği kullanıcıları için koşullu erişim-Azure AD
description: Azure Active Directory B2B kullanıcıları için Multi-Factor Authentication ilkelerini nasıl zorlayacağınızı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22a5388d15b18180539eb95990a29f7ddf4f1951
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739557"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B işbirliği kullanıcıları için koşullu erişim

Bu makalede, kuruluşların, B2B Konuk kullanıcılarına kaynaklarına erişmesi için koşullu erişim (CA) ilkelerini nasıl kapsam altında olduğu açıklanır.
>[!NOTE]
>Bu kimlik doğrulama veya yetkilendirme akışı, Konuk kullanıcılar için bu kimlik sağlayıcısının (IDP) var olan kullanıcılarından farklı bir bit.

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>B2B Konuk kullanıcıları için bir dış dizinden kimlik doğrulama akışı

Aşağıdaki diyagramda akış gösterilmektedir: görüntü, ![ B2B Konuk kullanıcıları için bir dış dizinden kimlik doğrulama akışını gösterir](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Adım | Description |
|--------------|-----------------------|
| 1. | B2B Konuk Kullanıcı, bir kaynağa erişim ister. Kaynak, kullanıcıyı güvenilir bir IDP olan kaynak kiracıya yönlendirir.|
| 2. | Kaynak kiracı kullanıcıyı dış olarak tanımlar ve kullanıcıyı B2B Konuk kullanıcısının IDP 'ye yönlendirir. Kullanıcı IDP 'de birincil kimlik doğrulaması gerçekleştirir.
| 3. | B2B Konuk kullanıcının IDP 'si kullanıcıya bir belirteç yayınlar. Kullanıcı, belirteç ile kaynak kiracıya yeniden yönlendirilir. Kaynak kiracı belirteci doğrular ve ardından kullanıcıyı CA ilkelerine göre değerlendirir. Örneğin, kaynak kiracısı kullanıcının Azure Active Directory (AD) Multi-Factor Authentication gerçekleştirmesini gerektirebilir.
| 4. | Tüm kaynak kiracı CA ilkeleri karşılandıktan sonra kaynak kiracının kendi belirtecini yayınlar ve kullanıcıyı kaynağına yeniden yönlendirir.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Bir kerelik geçiş kodu ile B2B Konuk kullanıcıları için kimlik doğrulama akışı

Aşağıdaki diyagramda akış gösterilmektedir: ![ görüntüde bir kerelik geçiş kodu bulunan B2B Konuk kullanıcıları Için kimlik doğrulama akışı gösterilmektedir](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Adım | Description |
|--------------|-----------------------|
| 1. |Kullanıcı başka bir Kiracıdaki bir kaynağa erişim istiyor. Kaynak, kullanıcıyı güvenilir bir IDP olan kaynak kiracıya yönlendirir.|
| 2. | Kaynak kiracısı, kullanıcıyı [dış e-posta bir kerelik geçiş kodu (OTP) kullanıcısı](./one-time-passcode.md) olarak tanımlar ve kullanıcıya OTP ile bir e-posta gönderir.|
| 3. | Kullanıcı OTP 'yi alır ve kodu gönderir. Kaynak kiracısı, kullanıcıyı CA ilkelerine göre değerlendirir.
| 4. | Tüm CA ilkeleri karşılandıktan sonra, kaynak kiracı bir belirteç yayınlar ve kullanıcıyı kaynağına yönlendirir. |

>[!NOTE]
>Kullanıcı bir dış kaynak kiracısından ise, B2B Konuk kullanıcısının IDP CA ilkelerinin de değerlendirilmesi mümkün değildir. Bugün itibariyle, yalnızca kaynak kiracının CA ilkeleri konukları için geçerlidir.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>B2B kullanıcıları için Azure AD Multi-Factor Authentication

Kuruluşlar, B2B Konuk kullanıcıları için birden çok Azure AD Multi-Factor Authentication ilkesi uygulayabilir. Bu ilkeler, kiracı, uygulama veya bireysel kullanıcı düzeyinde, kuruluşun tam zamanlı çalışanları ve üyeleri için etkinleştirildikleri şekilde zorlanabilir.
Konuk kullanıcının kuruluşunda Multi-Factor Authentication özellikleri olsa bile, kullanıcılar için Azure AD Multi-Factor Authentication kaynak kiracıya her zaman sorumludur. Örnek olarak

1. Fabrikam adlı bir şirketteki yönetici veya bilgi çalışanı, Application Woodgrove uygulamasını kullanmak için Contoso adlı başka bir şirketten Kullanıcı davet eder.

2. Fabrikam 'daki Woodgrove uygulaması, erişim için Azure AD Multi-Factor Authentication gerektirecek şekilde yapılandırılmıştır.

3. Contoso 'dan B2B Konuk kullanıcısı fabrikam kiracısında Woodgrove 'a erişmeyi denediğinde, Azure AD Multi-Factor Authentication Challenge ' ı tamamlamaları istenir.

4. Konuk Kullanıcı daha sonra Azure AD Multi-Factor Authentication 'yi fabrikam ile ayarlayabilir ve seçenekleri seçebilir.

5. Bu senaryo her türlü kimlik için (Azure AD veya kişisel Microsoft hesabı (MSA) geçerlidir. Örneğin, contoso 'daki Kullanıcı sosyal KIMLIK kullanarak kimlik doğrulaması gerçekleştiriyorsa.

6. Fabrikam, Azure AD Multi-Factor Authentication destekleyen, yeterli Premium Azure AD lisanslarına sahip olmalıdır. Contoso kullanıcısı bundan sonra Fabrikam ' dan bu lisansı kullanır. B2B lisanslaması hakkında bilgi için bkz. [Azure AD dış kimlikleri için faturalandırma modeli](./external-identities-pricing.md) .

>[!NOTE]
>Azure AD Multi-Factor Authentication, öngörülebilirlik sağlamak için kaynak kiralamanın üzerinde yapılır. Konuk Kullanıcı oturum açtığında, arka planda görünen kaynak kiracı oturum açma sayfasını ve ön planda kendi giriş kiracı oturum açma sayfasını ve Şirket logosunu görür.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>B2B kullanıcıları için Azure AD Multi-Factor Authentication ayarlama

B2B işbirliği kullanıcıları için Azure AD Multi-Factor Authentication ayarlamak için şu videoyu izleyin:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>B2B kullanıcıları Azure AD Multi-Factor Authentication teklif teklifi için

Azure AD Multi-Factor Authentication kullanım deneyimi hakkında daha fazla bilgi edinmek için şu videoyu izleyin:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>B2B kullanıcıları için Azure AD Multi-Factor Authentication sıfırlama

Şimdi, B2B Konuk kullanıcıları için aşağıdaki PowerShell cmdlet 'leri kullanıma sunulmuştur:

1. Azure AD'ye Bağlanma

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Tüm kullanıcıları kanıtlama yöntemlerine al

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Aşağıda bir örnek verilmiştir:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Belirli bir kullanıcı için Azure AD Multi-Factor Authentication yöntemini sıfırlayın, B2B işbirliği kullanıcısının, düzeltme yöntemlerini yeniden ayarlaması gerekir. 
   Aşağıda bir örnek verilmiştir:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>B2B kullanıcıları için koşullu erişim

B2B Konuk kullanıcıları için CA ilkelerini etkileyen çeşitli faktörler vardır.

### <a name="device-based-conditional-access"></a>Cihaz tabanlı Koşullu Erişim

CA 'da, kullanıcının [cihazının uyumlu olmasını veya karma Azure AD 'ye katılmış](../conditional-access/concept-conditional-access-conditions.md#device-state-preview)olmasını gerektirme seçeneği vardır. B2B Konuk kullanıcıları yalnızca kaynak kiracının cihazlarını yönetebilmesi durumunda uyumluluk sağlayabilir. Cihazlar aynı anda birden fazla kuruluş tarafından yönetilemez. B2B Konuk kullanıcıları, şirket içi bir AD hesabı olmadığından karma Azure AD JOIN 'i karşılayamaz. Yalnızca Konuk kullanıcının cihazı yönetilmiyorsa, cihazlarını kaynak kiracısına kaydedebilir veya kaydedebilir ve sonra cihazı uyumlu hale getirebilirsiniz. Kullanıcı daha sonra izin denetimini karşılayamaz.

>[!Note]
>Dış kullanıcılar için yönetilen bir cihazın kullanılması önerilmez.

### <a name="mobile-application-management-policies"></a>Mobil uygulama yönetim ilkeleri

CA, **onaylanan istemci uygulamaları iste** gibi denetimler verir ve **Uygulama koruma ilkelerinin** cihazın kiracıya kaydedilmesini gerektirir. Bu denetimler yalnızca [iOS ve Android cihazlarına](../conditional-access/concept-conditional-access-conditions.md#device-platforms)uygulanabilir. Ancak, kullanıcının cihazı zaten başka bir kuruluş tarafından yönetilmiyorsa, bu denetimlerden hiçbiri B2B Konuk kullanıcılarına uygulanamaz. Bir mobil cihaz, bir seferde birden fazla kiracıya kaydedilemez. Mobil cihaz başka bir kuruluş tarafından yönetilmiyorsa, Kullanıcı engellenecektir. Yalnızca Konuk kullanıcının cihazı yönetilmiyorsa, cihazlarını kaynak kiracısına kaydedebilirler. Kullanıcı daha sonra izin denetimini karşılayamaz.  

>[!NOTE]
>Dış kullanıcılar için bir uygulama koruma ilkesi kullanılması önerilmez.

### <a name="location-based-conditional-access"></a>Konum tabanlı koşullu erişim

Davet edilen kuruluş, iş ortağı kuruluşlarını tanımlayan güvenilir bir IP adresi aralığı oluşturabileceğini IP aralıklarına dayalı [konum tabanlı ilke](../conditional-access/concept-conditional-access-conditions.md#locations) uygulanabilir.

İlkeler, **coğrafi konumlara** göre de zorlanabilir.

### <a name="risk-based-conditional-access"></a>Risk tabanlı Koşullu Erişim

B2B Konuk Kullanıcı izin denetimini karşılıyorsa, [oturum açma risk ilkesi](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) zorlanır. Örneğin, bir kuruluş, orta veya yüksek oturum açma riski için Azure AD Multi-Factor Authentication gerektirebilir. Ancak, bir Kullanıcı, kaynak kiracısında daha önce Azure AD Multi-Factor Authentication kaydolmamışsa, Kullanıcı engellenecektir. Bu, kötü amaçlı kullanıcıların, meşru bir kullanıcının parolasının güvenliğini tehlikeye adıkları olayda kendi Azure AD Multi-Factor Authentication kimlik bilgilerini kaydetmesini engellemek için yapılır.

Ancak, [Kullanıcı risk ilkesi](../conditional-access/concept-conditional-access-conditions.md#user-risk) , kaynak kiracısında çözümlenemez. Örneğin, yüksek riskli Konuk kullanıcılar için parola değişikliği yapmanız gerekiyorsa, kaynak dizinindeki parolaları sıfırlayamamasından dolayı bunlar engellenir.

### <a name="conditional-access-client-apps-condition"></a>Koşullu erişim istemci uygulamaları koşulu

[İstemci uygulamaları koşulları](../conditional-access/concept-conditional-access-conditions.md#client-apps) , diğer kullanıcı türleri için YAPTıKLARı gibi B2B Konuk kullanıcıları için de aynı şekilde davranır. Örneğin, konuk kullanıcıların eski kimlik doğrulama protokollerini kullanmasını engelleyebilirsiniz.

### <a name="conditional-access-session-controls"></a>Koşullu erişim oturum denetimleri

[Oturum denetimleri](../conditional-access/concept-conditional-access-session.md) , B2B Konuk kullanıcıları için diğer kullanıcı türleri için aynı şekilde davranır.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için Azure AD B2B işbirliği ile ilgili aşağıdaki makalelere bakın:

- [Azure AD B2B işbirliği nedir?](./what-is-b2b.md)
- [Kimlik Koruması ve B2B kullanıcıları](../identity-protection/concept-identity-protection-b2b.md)
- [Dış Kimlikler fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/)
- [Sık sorulan sorular (SSS)](./faq.md)
