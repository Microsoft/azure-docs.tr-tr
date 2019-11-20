---
title: Azure Active Directory B2C Müşteri kaydı sırasında e-posta doğrulamayı devre dışı bırakma
description: Azure Active Directory B2C ' de müşteri kaydı sırasında e-posta doğrulamayı devre dışı bırakmayı öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9a7adc04991dd4a472bdaf1aa47aacaf6cdeb190
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256924"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C Müşteri kaydı sırasında e-posta doğrulamayı devre dışı bırakma

Varsayılan olarak, Azure Active Directory B2C (Azure AD B2C), müşterinin e-posta adresini yerel hesaplar için doğrular (e-posta adresi veya Kullanıcı adı ile kaydolan kullanıcılar için hesaplar). Azure AD B2C, müşterilerin kaydolma işlemi sırasında bunları doğrulamasını zorunlu kılarak geçerli e-posta adreslerini sağlar. Ayrıca, kötü amaçlı aktörlerin uygulamalarınızda sahte hesaplar oluşturmak için otomatik süreçler kullanmasını önler.

Bazı uygulama geliştiricileri, kaydolma işlemi sırasında e-posta doğrulamasını atlamayı tercih eder ve bunun yerine müşterilerin e-posta adreslerini daha sonra doğrular. Bunu desteklemek için Azure AD B2C, e-posta doğrulamasını devre dışı bırakacak şekilde yapılandırılabilir. Bunun yapılması daha sorunsuz bir kaydolma işlemi oluşturur ve geliştiricilere, e-posta adreslerini doğrulayan müşterileri, kendilerine ait olmayan müşterilerin ayırt edilmesine yönelik esneklik sağlar.

E-posta doğrulamayı devre dışı bırakmak için şu adımları izleyin:

1. [Azure portalda](https://portal.azure.com) oturum açma
1. Azure AD B2C kiracınızı içeren dizini seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. **Kullanıcı akışları ' nı**seçin.
1. E-posta doğrulamasını devre dışı bırakmak istediğiniz kullanıcı akışını seçin. Örneğin, *B2C_1_signinsignup*.
1. **Sayfa düzenlerini**seçin.
1. **Yerel hesap kaydolma sayfası**' nı seçin.
1. **Kullanıcı öznitelikleri**altında **e-posta adresi**' ni seçin.
1. DOĞRULAMASı için **doğrulama** açılır penceresinde **Hayır**' ı seçin.
1. **Kaydet**’i seçin. E-posta doğrulaması artık bu kullanıcı akışı için devre dışı bırakıldı.

> [!WARNING]
> Kaydolma işleminde e-posta doğrulamasını devre dışı bırakmak istenmeyen postalara neden olabilir. Varsayılan Azure AD B2C tarafından belirtilen e-posta doğrulamasını devre dışı bırakırsanız, bir değiştirme doğrulama sistemi uygulamanızı öneririz.
