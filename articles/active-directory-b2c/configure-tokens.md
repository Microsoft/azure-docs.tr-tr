---
title: Belirteçleri Yapılandırma-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C ' de belirteç ömrünü ve uyumluluk ayarlarını yapılandırmayı öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83f8051fa31b6431d4a8515e2c0912cc1872a402
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064384"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C belirteçleri yapılandırma

Bu makalede, Azure Active Directory B2C (Azure AD B2C) ' de [bir belirtecin yaşam süresini ve uyumluluğunu](active-directory-b2c-reference-tokens.md) yapılandırmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Kullanıcıların uygulamanızda kaydolup oturum açmasını sağlamak için [bir Kullanıcı akışı oluşturun](tutorial-create-user-flows.md) .

## <a name="configure-token-lifetime"></a>Belirteç ömrünü yapılandırma

Belirteç ömrünü herhangi bir Kullanıcı akışında yapılandırabilirsiniz.

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure AD B2C kiracınızı içeren dizini seçin.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. **Kullanıcı akışları (ilkeler)** seçeneğini belirleyin.
5. Daha önce oluşturduğunuz kullanıcı akışını açın.
6. Seçin **özellikleri**.
7. **Belirteç ömrü**altında, uygulamanızın gereksinimlerine uyacak şekilde aşağıdaki özellikleri ayarlayın:

    ![Azure portal belirteç ömrü özelliği ayarları](./media/configure-tokens/token-lifetime.png)

8. **Kaydet**’e tıklayın.

## <a name="configure-token-compatibility"></a>Belirteç uyumluluğunu yapılandırma

1. **Kullanıcı akışları (ilkeler)** seçeneğini belirleyin.
2. Daha önce oluşturduğunuz kullanıcı akışını açın.
3. Seçin **özellikleri**.
4. **Belirteç uyumluluk ayarları**altında, uygulamanızın gereksinimlerine uyacak şekilde aşağıdaki özellikleri ayarlayın:

    ![Azure portal belirteç uyumluluk özelliği ayarları](./media/configure-tokens/token-compatibility.png)

5. **Kaydet**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

[Erişim belirteçlerini kullanma](active-directory-b2c-access-tokens.md)hakkında daha fazla bilgi edinin.



