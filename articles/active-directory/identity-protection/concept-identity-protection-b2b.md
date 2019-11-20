---
title: Kimlik koruması ve B2B kullanıcıları-Azure Active Directory
description: B2B kullanıcılarına nasıl çalıştığını ve bilinen kısıtlamaları kullanarak kimlik koruması kullanma
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95ea7eb470a5880bc88b3df903d33854f363e974
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881321"
---
# <a name="identity-protection-and-b2b-users"></a>Kimlik Koruması ve B2B kullanıcıları

Azure AD B2B işbirliğiyle kuruluşlar, kimlik koruması kullanan B2B kullanıcıları için risk tabanlı ilkeler uygulayabilir. Bu ilkeler iki şekilde yapılandırılır:

- Yöneticiler, Konuk kullanıcıları dahil tüm uygulamalar için uygulanan yerleşik kimlik koruması risk tabanlı ilkeleri yapılandırabilir.
- Yöneticiler, Konuk kullanıcılar da dahil olmak üzere, koşullu erişim ilkelerini, bir koşul olarak oturum açma riskini kullanarak yapılandırabilir.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>B2B işbirliği kullanıcıları için risk nasıl değerlendirilir

B2B işbirliği kullanıcıları için Kullanıcı riski, kendi giriş dizininde değerlendirilir. Bu kullanıcılar için gerçek zamanlı oturum açma riski, kaynağa erişmeyi denediğinde kaynak dizininde değerlendirilir.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B işbirliği kullanıcıları için kimlik korumasıyla ilgili sınırlamalar

Bir kaynak dizinindeki B2B işbirliği kullanıcıları için kimlik koruması uygulamada, kimlikleri kendi giriş dizininde mevcut olduğundan sınırlamalar vardır. Ana sınırlamalar aşağıdaki gibidir:

- Bir Konuk Kullanıcı, parola sıfırlamayı zorlamak için kimlik koruması Kullanıcı risk ilkesini tetiklerse, **engellenir**. Bu blok, kaynak dizinindeki parolaların sıfırlanmamasından kaynaklanır.
- **Konuk kullanıcılar riskli kullanıcılar raporunda görünmez**. Bu görünürlük kaybı, B2B kullanıcısının giriş dizininde gerçekleşen risk değerlendirmesinden kaynaklanır.
- Yöneticiler, kaynak dizinlerinde **riskli BIR B2B işbirliği kullanıcısını yok sayamaz veya düzeltemiyor** . Bu işlevsellik kaybı, kaynak dizinindeki yöneticilerin B2B kullanıcısının ana dizinine erişim sahibi olmaması nedeniyle oluşur.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Neden riskli B2B işbirliği kullanıcılarını dizinimde düzelmiyorum?

B2B kullanıcıları için risk değerlendirmesi ve düzeltme, kendi giriş dizininde meydana gelir. Bu olgu nedeniyle, Konuk kullanıcılar kaynak dizinindeki riskli kullanıcılar raporunda görünmez ve kaynak dizinindeki Yöneticiler bu kullanıcılar için güvenli bir parola sıfırlamayı zorlamaz.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Kuruluşumdaki risk tabanlı bir ilke nedeniyle B2B işbirliği kullanıcısı engellenirse ne yapmalıyım?

Dizininizde riskli bir B2B kullanıcısı risk tabanlı ilkeniz tarafından engellenirse, kullanıcının bu riski kendi giriş dizininde düzeltilmesi gerekecektir. Kullanıcılar kendi giriş dizininde güvenli bir parola sıfırlama işlemi gerçekleştirerek riskini düzeltebilir. Kendi giriş dizininde self servis parola sıfırlaması etkinleştirilmemişse, bir yöneticinin riskini el ile kapatması veya parolalarını sıfırlaması için kendi kuruluşlarının BT personeline başvurmaları gerekir.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>B2B işbirliği kullanıcılarının risk tabanlı ilkelerden etkilenmemesini Nasıl yaparım?.

Kuruluşunuzun risk tabanlı koşullu erişim ilkelerinin B2B kullanıcılarını dışlamak, B2B kullanıcılarının risk değerlendirmesine karşı etkilenmelerini veya engellenmesini engeller. Bu B2B kullanıcılarını dışlamak için, Azure AD 'de kuruluşunuzun tüm konuk kullanıcılarını içeren bir grup oluşturun. Daha sonra, bu grubu yerleşik kimlik koruması Kullanıcı risk ve oturum açma risk ilkeleriniz için bir dışlama olarak ekleyin ve bir koşul olarak oturum açma riskini kullanan tüm koşullu erişim ilkelerini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD B2B işbirliği ile ilgili aşağıdaki makalelere bakın:

- [Azure AD B2B işbirliği nedir?](../b2b/what-is-b2b.md)
