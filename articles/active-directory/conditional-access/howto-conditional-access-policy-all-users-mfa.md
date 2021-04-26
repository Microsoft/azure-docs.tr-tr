---
title: Koşullu erişim-tüm kullanıcılar için MFA gerektir-Azure Active Directory
description: Tüm kullanıcıların Multi-Factor Authentication gerçekleştirmesini gerektirmek için özel bir koşullu erişim ilkesi oluşturun
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e6185c4bde71285fc163cae2af46f64ba052195
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994766"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Koşullu erişim: tüm kullanıcılar için MFA gerektir

Alex Weinert olarak, Microsoft 'taki kimlik güvenliği dizini, [PA $ $Word Web günlüğü Gönderinizde bahsetmez](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> Parolanız ne kadar önemlidir, ancak MFA bunu yapar! Önerilerimize bağlı olarak, MFA kullanıyorsanız, hesabınız% 99,9 ' den daha az olabilir.

Bu makaledeki kılavuz, kuruluşunuzun ortamınız için dengeli bir MFA ilkesi oluşturmasına yardımcı olur.

## <a name="user-exclusions"></a>Kullanıcı dışlamaları

Koşullu erişim ilkeleri güçlü araçlardır ve ilkenizde aşağıdaki hesapların dışlanmasını öneririz:

* Kiracı genelindeki hesap kilitlenmesini engellemek için **acil durum erişimi** veya **kesme camı** hesapları. Olası olmayan senaryoda tüm yöneticiler kiracınızın dışında kilitlendiğinden, acil durum erişimi yönetim hesabınız kiracıya oturum açmak için kullanılabilir ve erişimi kurtarmak için gerekli adımları uygulayın.
   * Daha fazla bilgi için [Azure AD 'de acil durum erişim hesaplarını yönetme](../roles/security-emergency-access.md)makalesinde bulabilirsiniz.
* Azure AD Connect eşitleme hesabı gibi **hizmet hesapları** ve **hizmet sorumluları**. Hizmet hesapları, belirli bir kullanıcıya bağlı olmayan etkileşimli olmayan hesaplardır. Bunlar normalde, uygulamalar için programlı erişime izin veren, ancak aynı zamanda yönetim amaçlarıyla sistemlerde oturum açmak için kullanılan arka uç hizmetleri tarafından kullanılır. MFA programlı olarak tamamlanmadığı için bunlar gibi hizmet hesapları dışlanmalıdır. Hizmet sorumluları tarafından yapılan çağrılar koşullu erişim tarafından engellenmiyor.
   * Kuruluşunuzun komut dosyalarında veya kodda kullanımda olan bu hesapları varsa, bunları [yönetilen kimliklerle](../managed-identities-azure-resources/overview.md)değiştirmeyi göz önünde bulundurun. Geçici bir çözüm olarak, bu belirli hesapları temel ilkeden hariç bırakabilirsiniz.

## <a name="application-exclusions"></a>Uygulama dışlamaları

Kuruluşların kullanımda olan çok sayıda bulut uygulaması olabilir. Bu uygulamaların hepsi eşit güvenlik gerektirmeyebilir. Örneğin, bordro ve katılımcı uygulamalar MFA gerektirebilir, ancak kafeterya olmayabilir. Yöneticiler, belirli uygulamaları ilkesinden dışlamalarını seçebilirler.

## <a name="create-a-conditional-access-policy"></a>Koşullu erişim ilkesi oluşturma

Aşağıdaki adımlar, tüm kullanıcıların Multi-Factor Authentication gerçekleştirmesini gerektirmek için bir koşullu erişim ilkesi oluşturmanıza yardımcı olur.

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar** altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. **Dahil et** altında **tüm kullanıcılar** ' ı seçin
   1. **Dışla** altında, **Kullanıcılar ve gruplar** ' ı seçin ve kuruluşunuzun acil erişim veya kesme camı hesaplarını seçin. 
   1. **Bitti** seçeneğini belirleyin.
1. **Bulut uygulamaları veya eylemleri**  >  **dahil**, **tüm bulut uygulamaları**' nı seçin.
   1. **Hariç tut**' un altında, Multi-Factor Authentication gerektirmeyen tüm uygulamaları seçin.
1. **Koşullar**  >  **istemci uygulamaları (Önizleme)** altında, bu ilke tüm varsayılanları seçili bırakmak **için geçerli olur** ve **bitti**' yi seçin.
1. **Erişim denetimleri**  >  **izni** altında **erişim ver**' i seçin, **Multi-Factor Authentication gerektir**' i seçin ve **Seç**' i seçin
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık** olarak ayarlayın.
1. İlkenizi etkinleştirmek için oluşturmak **için Oluştur ' u seçin.**

### <a name="named-locations"></a>Adlandırılmış konumlar

Kuruluşlar, **adlandırılmış konumlar** olarak bilinen bilinen ağ konumlarını, koşullu erişim ilkelerine dahil etmek için seçim gösterebilir. Bu adlandırılmış konumlar, ana ofis konumu gibi güvenilir IPv4 ağları içerebilir. Adlandırılmış konumları yapılandırma hakkında daha fazla bilgi için, [koşullu erişim Azure Active Directory konum koşulunun ne olduğuna](location-condition.md) ilişkin makaleye bakın.

Yukarıdaki örnek ilkede, bir kuruluş, kurumsal ağından bir bulut uygulamasına erişiyorsanız çok faktörlü kimlik doğrulaması gerektirmeyebilir. Bu durumda, şu yapılandırmayı ilkeye ekleyebilirler:

1. **Atamalar**' ın altında, **koşullar**  >  **konumlar**' ı seçin.
   1. **Evet 'i** yapılandırın.
   1. **Herhangi bir konum** ekleyin.
   1. **Tüm güvenilen konumları** hariç tutun.
   1. **Bitti** seçeneğini belirleyin.
1. **Bitti** seçeneğini belirleyin.
1. İlke değişikliklerinizi **kaydedin** .

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

[Koşullu erişim yalnızca rapor modunu kullanarak etkiyi belirleme](howto-conditional-access-insights-reporting.md)

[Koşullu erişim What If aracını kullanarak oturum açma davranışının benzetimini yapma](troubleshoot-conditional-access-what-if.md)
