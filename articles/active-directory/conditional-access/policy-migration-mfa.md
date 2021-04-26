---
title: Çok faktörlü kimlik doğrulamasıyla koşullu erişim ilkelerini geçirme-Azure Active Directory
description: Bu makalede, Azure portal çok faktörlü kimlik doğrulaması gerektiren klasik bir ilkenin nasıl geçirileceği gösterilmektedir.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc99241f5fa7c09c69db13dcb3f6c3aaa75e376
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87027402"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Azure portal klasik bir ilkeyi geçirme

Bu makalede, bir bulut uygulaması için **çok faktörlü kimlik doğrulaması** gerektiren klasik bir ilkenin nasıl geçirileceği gösterilmektedir. Bir önkoşul olmamasına karşın, klasik ilkelerinizi geçirmeye başlamadan önce [Azure Portal klasik Ilkeleri geçirmeyi](policy-migration.md) okumanız önerilir.

![Salesforce uygulaması için MFA gerektiren klasik ilke ayrıntıları](./media/policy-migration/33.png)

Geçiş işlemi aşağıdaki adımlardan oluşur:

1. Yapılandırma ayarlarını almak için [Klasik Ilkeyi açın](#open-a-classic-policy) .
1. Klasik ilkenizi değiştirmek için yeni bir Azure AD koşullu erişim ilkesi oluşturun. 
1. Klasik ilkeyi devre dışı bırakın.

## <a name="open-a-classic-policy"></a>Klasik bir ilke açın

1. [Azure Portal](https://portal.azure.com) **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**' ne gidin.
1. **Klasik ilkeler**' i seçin.

   ![Klasik ilkeler görünümü](./media/policy-migration-mfa/12.png)

1. Klasik ilkeler listesinde, geçirmek istediğiniz ilkeyi seçin. Yeni bir koşullu erişim ilkesiyle yeniden oluşturabilmeniz için yapılandırma ayarlarını belgeleyin.

## <a name="create-a-new-conditional-access-policy"></a>Yeni bir koşullu erişim ilkesi oluşturma

1. [Azure Portal](https://portal.azure.com) **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**' ne gidin.
1. Yeni bir koşullu erişim ilkesi oluşturmak için **Yeni ilke**' yi seçin.
1. **Yeni** sayfada, **ad** metin kutusuna ilkeniz için bir ad yazın.
1. **Atamalar** bölümünde **Kullanıcılar ve gruplar**' a tıklayın.
   1. Klasik ilkenizde tüm kullanıcılar ' ı seçtiyseniz, **tüm kullanıcılar**' a tıklayın. 
   1. Klasik ilkenizde seçili gruplarınız varsa, **Kullanıcı ve Grup Seç**' e tıklayın ve ardından gerekli kullanıcılar ve gruplar ' ı seçin.
   1. Dışlanan gruplarınız varsa, **Dışla** sekmesine tıklayın ve ardından gerekli kullanıcıları ve grupları seçin. 
   1. **Bitti** 'yi seçin
1. **Atama** bölümünde **bulut uygulamaları veya eylemleri**' ne tıklayın.
1. **Bulut uygulamaları veya eylemler** sayfasında, aşağıdaki adımları uygulayın:
   1. **Uygulama Seç**' e tıklayın.
   1. **Seç**’e tıklayın.
   1. **Seç** sayfasında, bulut uygulamanızı seçin ve ardından **Seç**' e tıklayın.
   1. **Bulut uygulamaları** sayfasında **bitti**' ye tıklayın.
1. **Multi-Factor Authentication** ' ın seçili olmasını istiyorsanız:
   1. **Erişim denetimleri** bölümünde, **izin ver**' e tıklayın.
   1. **İzin** sayfasında, **erişim ver**' e tıklayın ve ardından **çok faktörlü kimlik doğrulaması iste**' ye tıklayın.
   1. **Seç**’e tıklayın.
1. İlkenizi **etkinleştirmek için tıklayın** ve ardından **Kaydet**' i seçin.

   ![Koşullu erişim ilkesi oluşturma](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Klasik ilkeyi devre dışı bırak

Klasik ilkenizi devre dışı bırakmak için **Ayrıntılar** görünümünde **devre dışı bırak** ' a tıklayın.

![Klasik ilkeleri devre dışı bırak](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Sonraki adımlar

- Klasik ilke geçişi hakkında daha fazla bilgi için, [Azure Portal klasik Ilkeleri geçirme](policy-migration.md)bölümüne bakın.
- [Yeni ilke kararlarının etkisini öğrenmek için koşullu erişim için yalnızca rapor modunu kullanın.](concept-conditional-access-report-only.md)