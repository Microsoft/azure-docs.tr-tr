---
title: Azure Active Directory B2C içinde self servis parola sıfırlama | Microsoft Docs
description: Azure Active Directory B2C ' de müşterileriniz için self servis parola sıfırlamanın nasıl ayarlanacağını gösterir
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7903ec669772c3a3858a9c1d514ab3e6de6a2bd4
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936826"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Müşterileriniz için self servis parola sıfırlamayı ayarlama

Self servis parola sıfırlama özelliği sayesinde, yerel hesaplara kaydolan müşterilerimiz parolalarını kendi kendilerine sıfırlayabilir. Bu, özellikle uygulamanızın milyonlarca müşteriyi düzenli olarak kullanıyorsa, destek personelinizdeki yükü önemli ölçüde azaltır. Şu anda, doğrulanmış bir e-posta adresi kullanmak desteklenen tek kurtarma yöntemidir.

> [!NOTE]
> Bu makale, kimlik sağlayıcısı olarak **yerel hesap oturumu açma** kullanan v1 **oturum açma** Kullanıcı akışı bağlamında kullanılan self servis parola sıfırlama için geçerlidir. Uygulamanızdan çağrılan, tamamen özelleştirilebilir parola sıfırlama Kullanıcı akışları gerekiyorsa, [Bu makaleye](active-directory-b2c-reference-policies.md)bakın.
> 
> 

Varsayılan olarak, dizininizde self servis parola sıfırlama özelliği açık değildir. Açmak için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com/) abonelik Yöneticisi olarak oturum açın. Bu, dizininizi oluştururken kullandığınız iş veya okul hesabı ya da aynı Microsoft hesabı.
2. **Azure Active Directory** açın (sol taraftaki Gezinti çubuğunda).
3. Seçenekler dikey penceresinde aşağı kaydırın ve **parola sıfırlama**' yı seçin.
4. **Self servis parola sıfırlamayı etkin** **olarak ayarlayın**. 
5. Sayfanın en üstündeki **Kaydet** ' e tıklayın. İşiniz bitti!

Test etmek için, bir kimlik sağlayıcısı olarak yerel hesapları olan oturum açma Kullanıcı akışındaki "Şimdi Çalıştır" özelliğini kullanın. Yerel hesap oturum açma sayfasında (bir e-posta adresi ve parola girin veya Kullanıcı adı ve parola girdiğinizde), müşteri deneyimini doğrulamak için **hesabınıza erişemiyor musunuz?** seçeneğine tıklayın.

> [!NOTE]
> Self servis parola sıfırlama sayfaları, [Şirket markası özelliği](../active-directory/fundamentals/customize-branding.md)kullanılarak özelleştirilebilir.
> 
> 

