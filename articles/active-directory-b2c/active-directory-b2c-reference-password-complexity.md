---
title: Parola karmaşıklığı-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C ' de tüketiciler tarafından sağlanan parolalar için karmaşıklık gereksinimlerini yapılandırma.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 642621e2e04d3e96dc6886b1bdb28c161560b28e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065450"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Azure Active Directory B2C parolalar için karmaşıklık gereksinimlerini yapılandırın

Azure Active Directory B2C (Azure AD B2C) bir hesap oluştururken Son Kullanıcı tarafından sağlanan parolaların karmaşıklık gereksinimlerinin değiştirilmesini destekler. Varsayılan olarak, Azure AD B2C parolaları `Strong` kullanır. Azure AD B2C Ayrıca, müşterilerin kullanabileceği parolaların karmaşıklığını denetlemek için yapılandırma seçeneklerini destekler.

## <a name="password-rule-enforcement"></a>Parola kuralı zorlaması

Kaydolma veya parola sıfırlama sırasında, son kullanıcının karmaşıklık kurallarını karşılayan bir parola sağlaması gerekir. Parola karmaşıklığı kuralları Kullanıcı akışı başına zorlanır. Kayıt sırasında bir Kullanıcı akışının dört basamaklı PIN olmasını gerektirirken, kayıt sırasında sekiz karakterlik bir dize olması gerekir. Örneğin, çocuklar için alt öğeler için farklı parola karmaşıklığı olan bir Kullanıcı akışı kullanabilirsiniz.

Oturum açma sırasında parola karmaşıklığı hiçbir şekilde zorlanmaz. Kullanıcılar, geçerli karmaşıklık gereksinimini karşılamadığından parolalarını değiştirmek için oturum açma sırasında hiçbir şekilde istenmez.

Parola karmaşıklığı aşağıdaki Kullanıcı akışları türlerinde yapılandırılabilir:

- Kaydolma veya oturum açma Kullanıcı akışı
- Parola sıfırlama Kullanıcı akışı

Özel ilkeler kullanıyorsanız, ([bir özel ilkede parola karmaşıklığı yapılandırabilirsiniz](active-directory-b2c-reference-password-complexity-custom.md)) yapabilirsiniz.

## <a name="configure-password-complexity"></a>Parola karmaşıklığını yapılandırma

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. **Kullanıcı akışları ' nı**seçin.
2. Bir Kullanıcı akışı seçin ve **Özellikler**' e tıklayın.
3. **Parola karmaşıklığı**bölümünde, bu kullanıcı akışının parola karmaşıklığını **basit**, **güçlü**veya **özel**olarak değiştirin.

### <a name="comparison-chart"></a>Karşılaştırma grafiği

| Karmaşıklık | Açıklama |
| --- | --- |
| Basit | En az 8 ile 64 karakter uzunluğunda bir parola. |
| Güçlü | En az 8 ile 64 karakter uzunluğunda bir parola. 3/4 küçük harf, büyük harf, sayı veya sembol gerektirir. |
| Özel | Bu seçenek, parola karmaşıklığı kuralları üzerinde en çok denetim sağlar.  Özel bir uzunluk yapılandırmasına izin verir.  Ayrıca yalnızca sayı parolalarının kabul edilmesini sağlar (PIN 'ler). |

## <a name="custom-options"></a>Özel seçenekler

### <a name="character-set"></a>Karakter kümesi

Yalnızca rakamları (PIN) veya tam karakter kümesini kabul etmenizi sağlar.

- **Sayılar yalnızca sayılara** izin verir (0-9) ve bir parola girildiğinde.
- **Tümü** , herhangi bir harf, sayı veya simgeye izin verir.

### <a name="length"></a>Uzunluk

Parolanın uzunluk gereksinimlerini denetlemenize olanak tanır.

- **Minimum uzunluk** en az 4 olmalıdır.
- **Maksimum uzunluk** en düşük uzunluğa eşit veya daha büyük olmalı ve en fazla 64 karakter olabilir.

### <a name="character-classes"></a>Karakter sınıfları

Parolada kullanılan farklı karakter türlerini denetlemenizi sağlar.

- **2/4: Küçük harfli karakter, büyük harf, sayı (0-9),** simge parolanın en az iki karakter türü içermesini sağlar. Örneğin, bir sayı ve küçük harfli bir karakter.
- **3/4: Küçük harfli karakter, büyük harf, sayı (0-9),** simge parolanın en az iki karakter türü içermesini sağlar. Örneğin, bir sayı, küçük harf ve bir büyük harf karakteri.
- **4/4: Küçük harfli karakter, büyük harf, sayı (0-9),** simge, parola tüm karakter türlerini içerir.

    > [!NOTE]
    > **4/4** gereksinimi, son kullanıcı rahatsız edilmesine yol açabilir. Bazı çalışmalar bu gereksinimin parola entropi ' i geliştirmediğinden gösterilmemiştir. [NIST parola yönergelerine](https://pages.nist.gov/800-63-3/sp800-63b.html#appA) bakın
