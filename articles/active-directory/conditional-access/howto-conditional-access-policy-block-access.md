---
title: Koşullu erişim-erişimi engelle-Azure Active Directory
description: İçin özel bir koşullu erişim ilkesi oluşturun
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e0801daa5bf83889be87987d440e377287b5ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366199"
---
# <a name="conditional-access-block-access"></a>Koşullu erişim: erişimi engelle

Koruyucu bir bulut geçiş yaklaşımına sahip kuruluşlar için, tüm ilkeyi engelle seçeneği kullanılabilir. 

> [!CAUTION]
> Bir blok ilkesinin yanlış yapılandırılması, kuruluşların Azure portal kilitlenmelerine neden olabilir.

Bunlara benzer ilkeler istenmeyen yan etkilere sahip olabilir. Doğru test ve Doğrulama etkinleştirilmeden önce hayati önem taşır. Yöneticiler, değişiklik yaparken koşullu erişim [yalnızca rapor modu](concept-conditional-access-report-only.md) ve [What If aracı](what-if-tool.md) gibi araçları kullanmalıdır.

## <a name="user-exclusions"></a>Kullanıcı dışlamaları

Koşullu erişim ilkeleri güçlü araçlardır ve ilkenizde aşağıdaki hesapların dışlanmasını öneririz:

* Kiracı genelindeki hesap kilitlenmesini engellemek için **acil durum erişimi** veya **kesme camı** hesapları. Olası olmayan senaryoda tüm yöneticiler kiracınızın dışında kilitlendiğinden, acil durum erişimi yönetim hesabınız kiracıya oturum açmak için kullanılabilir ve erişimi kurtarmak için gerekli adımları uygulayın.
   * Daha fazla bilgi için [Azure AD 'de acil durum erişim hesaplarını yönetme](../roles/security-emergency-access.md)makalesinde bulabilirsiniz.
* Azure AD Connect eşitleme hesabı gibi **hizmet hesapları** ve **hizmet sorumluları**. Hizmet hesapları, belirli bir kullanıcıya bağlı olmayan etkileşimli olmayan hesaplardır. Bunlar normalde, uygulamalar için programlı erişime izin veren, ancak aynı zamanda yönetim amaçlarıyla sistemlerde oturum açmak için kullanılan arka uç hizmetleri tarafından kullanılır. MFA programlı olarak tamamlanmadığı için bunlar gibi hizmet hesapları dışlanmalıdır. Hizmet sorumluları tarafından yapılan çağrılar koşullu erişim tarafından engellenmiyor.
   * Kuruluşunuzun komut dosyalarında veya kodda kullanımda olan bu hesapları varsa, bunları [yönetilen kimliklerle](../managed-identities-azure-resources/overview.md)değiştirmeyi göz önünde bulundurun. Geçici bir çözüm olarak, bu belirli hesapları temel ilkeden hariç bırakabilirsiniz.

## <a name="create-a-conditional-access-policy"></a>Koşullu erişim ilkesi oluşturma

Aşağıdaki adımlar, kullanıcılar güvenilir bir ağda değilse [Office 365](concept-conditional-access-cloud-apps.md#office-365) hariç tüm uygulamalara erişimi engellemek Için koşullu erişim ilkeleri oluşturmanıza yardımcı olur. Bu ilkeler, yöneticilerin mevcut kullanıcılar üzerinde sahip olacağı etkiyi belirleyebilmeleri için, [yalnızca rapor modunda](howto-conditional-access-insights-reporting.md) başlatılır. Yöneticiler ilkelerin sizin için uygun olduğundan emin olduğunda, bunları **Açık** olarak değiştirebilir.

İlk ilke, güvenilir bir konumda değilse Microsoft 365 uygulamalar haricinde tüm uygulamalara erişimi engeller.

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar** altında **Kullanıcılar ve gruplar**’ı seçin.
   1. **Ekle**' nin altında **tüm kullanıcılar**' ı seçin.
   1. **Dışla** altında, **Kullanıcılar ve gruplar** ' ı seçin ve kuruluşunuzun acil erişim veya kesme camı hesaplarını seçin. 
   1. **Bitti** seçeneğini belirleyin.
1. **Bulut uygulamaları veya eylemler** altında aşağıdaki seçenekleri belirleyin:
   1. **Ekle**' nin altında **tüm bulut uygulamaları**' nı seçin.
   1. **Dışla** altında **Office 365**' i seçin, **Seç**' i seçin ve **bitti**' yi seçin.
1. **Koşullar** altında:
   1. **Koşulların** bulunduğu  >  **konum**.
      1. **Yapılandır** 'ı **Evet** olarak ayarla
      1. **Dahil et** altında **herhangi bir konum** seçin.
      1. **Hariç tut** altında **tüm güvenilen konumlar**' ı seçin.
      1. **Bitti** seçeneğini belirleyin.
   1. **İstemci uygulamaları (Önizleme)** altında **Yapılandır** ' ı **Evet** olarak ayarlayın ve **bitti**' yi ve **bitti ' yi seçin.**
1. **Erişim denetimleri**  >  **izni** altında, **erişimi engelle**' yi seçin ve ardından **Seç**' i seçin.
1. Ayarlarınızı onaylayın ve **ilkeyi** **yalnızca rapor** olarak ayarlayın.
1. İlkenizi etkinleştirmek için oluşturmak **için Oluştur ' u seçin.**

Microsoft 365 kullanıcılarına çok faktörlü kimlik doğrulaması veya uyumlu bir cihaz istemek için aşağıda ikinci bir ilke oluşturulur.

1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar** altında **Kullanıcılar ve gruplar**’ı seçin.
   1. **Ekle**' nin altında **tüm kullanıcılar**' ı seçin.
   1. **Dışla** altında, **Kullanıcılar ve gruplar** ' ı seçin ve kuruluşunuzun acil erişim veya kesme camı hesaplarını seçin. 
   1. **Bitti** seçeneğini belirleyin.
1. **Bulut uygulamaları veya eylemler**  >  altında, **Uygulama Seç**' i seçin, **Office 365**' i seçin ve sonra da **Tamam**' ı seçin.
1. **Erişim denetimleri**  >  **izni** altında, **erişim ver**' i seçin.
   1. **Multi-Factor Authentication gerektir** ' i seçin ve **cihazın uyumlu olarak işaretlenmesini gerektir** ' **i seçin.**
   1. **Seçili denetimlerin tümünün** seçili olduğundan emin olun.
   1. **Seç**’i seçin.
1. Ayarlarınızı onaylayın ve **ilkeyi** **yalnızca rapor** olarak ayarlayın.
1. İlkenizi etkinleştirmek için oluşturmak **için Oluştur ' u seçin.**

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

[Koşullu erişim yalnızca rapor modunu kullanarak etkiyi belirleme](howto-conditional-access-insights-reporting.md)

[Koşullu erişim What If aracını kullanarak oturum açma davranışının benzetimini yapma](troubleshoot-conditional-access-what-if.md)
