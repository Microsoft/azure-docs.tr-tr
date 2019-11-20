---
title: Azure Active Directory B2C Multi-Factor Authentication | Microsoft Docs
description: Azure Active Directory B2C tarafından sağlanan tüketiciye yönelik uygulamalarda Multi-Factor Authentication etkinleştirme.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0b872fcfbc2ead2cebdd32ff718b582c13af314e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065572"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'de Multi-Factor Authentication 'ı etkinleştirme

Azure Active Directory B2C (Azure AD B2C), uygulamalarınızda kayıt ve oturum açma deneyimlerine ikinci bir güvenlik katmanı ekleyebilmeniz için doğrudan [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) ile tümleşir. Multi-Factor Authentication 'ı tek bir kod satırı yazmadan etkinleştirirsiniz. Zaten kaydolma ve oturum açma Kullanıcı akışları oluşturduysanız, çok faktörlü kimlik doğrulamasını yine de etkinleştirebilirsiniz.

Bu özellik, uygulamaların aşağıdakiler gibi senaryoları işlemesine yardımcı olur:

- Bir uygulamaya erişmek için Multi-Factor Authentication gerekmez, ancak başka bir uygulamaya erişmesi gerekir. Örneğin, müşteri, sosyal veya yerel hesapla otomatik bir sigorta uygulamasında oturum açabilir, ancak aynı dizinde kayıtlı olan ev sigortası uygulamasına erişmeden önce telefon numarasını doğrulamalıdır.
- Genel olarak bir uygulamaya erişmek için Multi-Factor Authentication gerekmez, ancak bunun içindeki hassas bölümlere erişmesi gerekir. Örneğin, müşteri, sosyal veya yerel hesap içeren bir bankacılık uygulamasında oturum açabilir ve hesap bakiyesini denetleyebilir, ancak bir hat aktarımını denemeden önce telefon numarasını doğrulamanız gerekir.

## <a name="set-multi-factor-authentication"></a>Multi-Factor Authentication 'ı ayarlama

Bir Kullanıcı akışı oluşturduğunuzda, çok faktörlü kimlik doğrulamasını etkinleştirme seçeneğiniz vardır.

![Multi-Factor Authentication 'ı ayarlama](./media/active-directory-b2c-reference-mfa/add-policy.png)

**Çok faktörlü kimlik doğrulamasını** **etkin**olarak ayarlayın.

Deneyimi doğrulamak için **Kullanıcı akışını Çalıştır** ' i kullanabilirsiniz. Aşağıdaki senaryoyu onaylayın:

Multi-Factor Authentication adımı gerçekleşmeden önce kiracınızda bir müşteri hesabı oluşturulur. Adım sırasında müşterinin bir telefon numarası sağlaması ve doğrulanması istenir. Doğrulama başarılı olursa telefon numarası daha sonra kullanılmak üzere hesaba iliştirilir. Müşteri iptal ediyor veya devre dışı olsa bile, çok faktörlü kimlik doğrulaması etkin olan bir sonraki oturum açma sırasında müşterinin bir telefon numarasını doğrulaması istenebilir.

## <a name="add-multi-factor-authentication"></a>Multi-Factor Authentication ekleme

Multi-Factor Authentication 'ı daha önce oluşturduğunuz bir Kullanıcı akışında etkinleştirmek mümkündür.

Multi-Factor Authentication 'ı etkinleştirmek için:

1. Kullanıcı akışını açın ve ardından **Özellikler**' i seçin.
2. **Çok faktörlü kimlik doğrulaması**' nın yanındaki **etkin**' i seçin.
3. Sayfanın üst kısmından **Kaydet**'e tıklayın.


