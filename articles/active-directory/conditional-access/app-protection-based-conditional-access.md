---
title: Koşullu erişim ile uygulama koruma ilkeleri-Azure Active Directory
description: Azure Active Directory ' de koşullu erişim ile Cloud App erişimi için uygulama koruma ilkesi yapmayı isteme hakkında bilgi edinin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 720cd46ac37a82f56aa37c0041ca8d92db177071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575780"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Nasıl yapılır: koşullu erişimle Cloud App erişimi için uygulama koruma ilkesi ve onaylanan istemci uygulaması gerektirme

Kullanıcılar mobil cihazlarını kişisel ve iş görevleri için düzenli olarak kullanır. Personelin üretken olduğundan emin olmaya devam ederken kuruluşlar, güvensiz olabilecek uygulamalardan veri kaybını da engellemek istiyor. Koşullu erişim sayesinde kuruluşlar, onaylanmış (modern kimlik doğrulama özellikli) istemci uygulamalarına erişimi, Intune uygulama koruma ilkeleri uygulanmış şekilde kısıtlayabilir.

Bu makalede Microsoft 365, Exchange Online ve SharePoint gibi kaynaklar için koşullu erişim ilkelerini yapılandırmaya yönelik üç senaryo sunulmaktadır.

- [Senaryo 1: Microsoft 365 uygulamalar uygulama koruma ilkeleriyle onaylanan uygulamalar gerektirir](#scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies)
- [Senaryo 2: tarayıcı uygulamaları, uygulama koruma ilkeleriyle onaylanan uygulamalar gerektirir](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [Senaryo 3: Exchange Online ve SharePoint onaylı bir istemci uygulaması ve uygulama koruma ilkesi gerektir](#scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy)

Koşullu erişim 'de, bu istemci uygulamalarının bir uygulama koruma ilkesiyle korunması bilinmektedir. Uygulama koruma ilkeleri hakkında daha fazla bilgi için, [Uygulama koruma ilkelerine genel bakış](/intune/apps/app-protection-policy)

> [!WARNING]
> Tüm uygulamalar onaylanan uygulamalar olarak desteklenmez veya uygulama koruma ilkelerini desteklemez. Uygun istemci uygulamalarının listesi için bkz. [Uygulama koruma ilkesi gereksinimi](concept-conditional-access-grant.md#require-app-protection-policy).

> [!NOTE]
> İzin verme denetimleri altında "seçili denetimlerden birini gerektir", OR yan tümcesi gibidir. Bu, kullanıcıların **Uygulama koruma Ilkesini iste** veya **onaylanmış istemci uygulaması** izin denetimleri gerektir uygulamalarını destekleyen uygulamaları kullanmalarını sağlamak için ilke içinde kullanılır. Uygulama, izin verme denetimini destekliyorsa **Uygulama koruma ilkesinin** zorunlu kılınmasını gerektirir. **Uygulama koruma Ilkesi gerektir** denetimini destekleyen uygulamalar hakkında daha fazla bilgi için bkz. [Uygulama koruma ilkesi gereksinimi](concept-conditional-access-grant.md#require-app-protection-policy).

## <a name="scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies"></a>Senaryo 1: Microsoft 365 uygulamalar uygulama koruma ilkeleriyle onaylanan uygulamalar gerektirir

Bu senaryoda, Contoso tüm mobil Microsoft 365 erişiminin Outlook Mobile ve OneDrive gibi, erişim almadan önce bir uygulama koruma ilkesiyle korunan onaylanmış istemci uygulamaları kullanması gerektiğine karar verdi. Tüm kullanıcıları Azure AD kimlik bilgileriyle oturum açın ve bunlara Azure AD Premium P1 veya P2 ve Microsoft Intune içeren lisanslar atanır.

Kuruluşlar, bir onaylanan istemci uygulamasının mobil cihazlarda kullanılmasını gerektirmek için aşağıdaki adımları tamamlamalıdır.

**1. Adım: Microsoft 365 için bir Azure AD koşullu erişim ilkesi yapılandırma**

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar** altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. **Ekle**' nin altında, **tüm kullanıcılar** ' ı veya bu Ilkeyi uygulamak istediğiniz belirli **kullanıcıları ve grupları** seçin. 
   1. **Bitti** seçeneğini belirleyin.
1. **Bulut uygulamaları veya eylemleri**  >  **dahil**, **Office 365**' i seçin.
1. **Koşullar**' ın altında **cihaz platformları**' nı seçin.
   1. **Yapılandır** 'ı **Evet** olarak ayarlayın.
   1. **Android** ve **iOS** dahil edin.
1. **Koşullar**' ın altında, **istemci uygulamaları**' nı seçin.
   1. **Yapılandır** 'ı **Evet** olarak ayarlayın.
   1. **Mobil uygulamalar ve Masaüstü istemcileri** ' ni seçin ve diğer her şeyin seçimini kaldırın.
1. **Erişim denetimleri**  >  **izni** altında, aşağıdaki seçenekleri belirleyin:
   - **Onaylanan istemci uygulaması gerektir**
   - **Uygulama koruma ilkesi gerektir**
   - **Seçili denetimlerden birini gerektir**
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık** olarak ayarlayın.
1. İlkenizi oluşturmak ve etkinleştirmek için **Oluştur** ' u seçin.

**2. Adım: ActiveSync (EAS) ile Exchange Online için bir Azure AD koşullu erişim ilkesi yapılandırma**

Bu adımdaki koşullu erişim ilkesi için aşağıdaki bileşenleri yapılandırın:

1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar** altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. **Ekle**' nin altında, **tüm kullanıcılar** ' ı veya bu Ilkeyi uygulamak istediğiniz belirli **kullanıcıları ve grupları** seçin. 
   1. **Bitti** seçeneğini belirleyin.
1. **Bulut uygulamaları veya eylemleri**  >  **dahil**, **Office 365 Exchange Online**' ı seçin.
1. **Koşullar** altında **istemci uygulamalar**' ı seçin:
   1. **Yapılandır** 'ı **Evet** olarak ayarlayın.
   1. **Exchange ActiveSync istemcileri** ' ni seçin ve diğer her şeyin seçimini kaldırın.
1. **Erişim denetimleri**  >  **izni** altında, **erişim ver**' i, **Uygulama koruma ilkesi gerektir**' i ve **Seç**' i seçin.
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık** olarak ayarlayın.
1. İlkenizi oluşturmak ve etkinleştirmek için **Oluştur** ' u seçin.

**3. Adım: iOS ve Android istemci uygulamaları için Intune uygulama koruma ilkesini yapılandırma**

Android ve iOS için uygulama koruma ilkeleri oluşturma adımları için [Uygulama koruma ilkeleri oluşturma ve atama](/intune/apps/app-protection-policies)makalesindeki makaleyi gözden geçirin. 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>Senaryo 2: tarayıcı uygulamaları, uygulama koruma ilkeleriyle onaylanan uygulamalar gerektirir

Bu senaryoda, contoso, erişim almadan önce bir uygulama koruma ilkesiyle korunan, iOS ve Android için uç gibi tüm mobil web tarama erişiminin bir onaylanan istemci uygulaması kullanması gerektiğini Microsoft 365. Tüm kullanıcıları Azure AD kimlik bilgileriyle oturum açın ve bunlara Azure AD Premium P1 veya P2 ve Microsoft Intune içeren lisanslar atanır.

Kuruluşlar, bir onaylanan istemci uygulamasının mobil cihazlarda kullanılmasını gerektirmek için aşağıdaki adımları tamamlamalıdır.

**1. Adım: Microsoft 365 için bir Azure AD koşullu erişim ilkesi yapılandırma**

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar** altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. **Ekle**' nin altında, **tüm kullanıcılar** ' ı veya bu Ilkeyi uygulamak istediğiniz belirli **kullanıcıları ve grupları** seçin. 
   1. **Bitti** seçeneğini belirleyin.
1. **Bulut uygulamaları veya eylemleri**  >  **dahil**, **Office 365**' i seçin.
1. **Koşullar**' ın altında **cihaz platformları**' nı seçin.
   1. **Yapılandır** 'ı **Evet** olarak ayarlayın.
   1. **Android** ve **iOS** dahil edin.
1. **Koşullar**' ın altında, **istemci uygulamaları**' nı seçin.
   1. **Yapılandır** 'ı **Evet** olarak ayarlayın.
   1. **Tarayıcı** ' yı seçin ve diğer her şeyin seçimini kaldırın.
1. **Erişim denetimleri**  >  **izni** altında, aşağıdaki seçenekleri belirleyin:
   - **Onaylanan istemci uygulaması gerektir**
   - **Uygulama koruma ilkesi gerektir**
   - **Seçili denetimlerden birini gerektir**
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık** olarak ayarlayın.
1. İlkenizi oluşturmak ve etkinleştirmek için **Oluştur** ' u seçin.

**2. Adım: iOS ve Android istemci uygulamaları için Intune uygulama koruma ilkesini yapılandırma**

Android ve iOS için uygulama koruma ilkeleri oluşturma adımları için [Uygulama koruma ilkeleri oluşturma ve atama](/intune/apps/app-protection-policies)makalesindeki makaleyi gözden geçirin. 

## <a name="scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy"></a>Senaryo 3: Exchange Online ve SharePoint onaylı bir istemci uygulaması ve uygulama koruma ilkesi gerektir

Bu senaryoda, contoso, kullanıcıların erişim almadan önce bir uygulama koruma ilkesi tarafından korunan onaylanan bir istemci uygulamasını kullandıkları sürece kullanıcılara yalnızca mobil cihazlarda e-posta ve SharePoint verilerine erişebilmeye karar verdi. Tüm kullanıcıları Azure AD kimlik bilgileriyle oturum açın ve bunlara Azure AD Premium P1 veya P2 ve Microsoft Intune içeren lisanslar atanır.

Kuruluşlar, bir onaylanan istemci uygulamasının mobil cihazlarda ve Exchange ActiveSync istemcilerinde kullanılmasını gerektirmek için aşağıdaki üç adımı tamamlamalıdır.

**1. Adım: Exchange Online ve SharePoint 'e erişirken onaylanan istemci uygulaması ve uygulama koruma İlkesi kullanımını gerektiren Android ve iOS tabanlı modern kimlik doğrulama istemcileri için Ilke.**

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar** altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. **Ekle**' nin altında, **tüm kullanıcılar** ' ı veya bu Ilkeyi uygulamak istediğiniz belirli **kullanıcıları ve grupları** seçin. 
   1. **Bitti** seçeneğini belirleyin.
1. **Bulut uygulamaları veya eylemleri**  >  **dahil**, **Office 365 Exchange Online** ve **Office 365 SharePoint Online**' ı seçin.
1. **Koşullar**' ın altında **cihaz platformları**' nı seçin.
   1. **Yapılandır** 'ı **Evet** olarak ayarlayın.
   1. **Android** ve **iOS** dahil edin.
1. **Koşullar**' ın altında, **istemci uygulamaları**' nı seçin.
   1. **Yapılandır** 'ı **Evet** olarak ayarlayın.
   1. **Mobil uygulamalar ve Masaüstü istemcileri** ' ni seçin ve diğer her şeyin seçimini kaldırın.
1. **Erişim denetimleri**  >  **izni** altında, aşağıdaki seçenekleri belirleyin:
   - **Onaylanan istemci uygulaması gerektir**
   - **Uygulama koruma ilkesi gerektir**
   - **Seçili denetimlerden birini gerektir**
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık** olarak ayarlayın.
1. İlkenizi oluşturmak ve etkinleştirmek için **Oluştur** ' u seçin.

**2. Adım: onaylanmış bir istemci uygulamasının kullanılmasını gerektiren Exchange ActiveSync istemcileri için Ilke.**

1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar** altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. **Ekle**' nin altında, **tüm kullanıcılar** ' ı veya bu Ilkeyi uygulamak istediğiniz belirli **kullanıcıları ve grupları** seçin. 
   1. **Bitti** seçeneğini belirleyin.
1. **Bulut uygulamaları veya eylemleri**  >  **dahil**, **Office 365 Exchange Online**' ı seçin.
1. **Koşullar** altında **istemci uygulamalar**' ı seçin:
   1. **Yapılandır** 'ı **Evet** olarak ayarlayın.
   1. **Exchange ActiveSync istemcileri** ' ni seçin ve diğer her şeyin seçimini kaldırın.
1. **Erişim denetimleri**  >  **izni** altında, **erişim ver**' i, **Uygulama koruma ilkesi gerektir**' i ve **Seç**' i seçin.
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık** olarak ayarlayın.
1. İlkenizi oluşturmak ve etkinleştirmek için **Oluştur** ' u seçin.

**3. Adım: iOS ve Android istemci uygulamaları için Intune uygulama koruma ilkesini yapılandırma.**

Android ve iOS için uygulama koruma ilkeleri oluşturma adımları için [Uygulama koruma ilkeleri oluşturma ve atama](/intune/apps/app-protection-policies)makalesindeki makaleyi gözden geçirin. 

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu Erişim nedir?](overview.md)

[Koşullu erişim bileşenleri](concept-conditional-access-policies.md)

[Ortak koşullu erişim ilkeleri](concept-conditional-access-policy-common.md)

