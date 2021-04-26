---
title: Azure Active Directory B2C yaş ' i etkinleştirme | Microsoft Docs
description: Uygulamanızı kullanarak sömürmeyi amaçlama 'yi nasıl tanımlayacağınızı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522467"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Azure Active Directory B2C yaş ve ömrü etkinleştir

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) yaş aşımları, uygulamanızı, ile veya ebeveyn izni olmadan kullanmak istediğiniz sömürmeyi amaçlama tanımlamanızı sağlar. Uygulamada, küçük bir oturum açma işleminden daha fazlasını engellemeyi seçebilirsiniz. Ya da, oturum açma işleminin tamamlanmasına ve uygulamanın küçük bir durum sağlamasına izin verir. 

>[!IMPORTANT]
>Bu özellik genel önizleme aşamasındadır. Üretim uygulamaları için özelliği kullanmayın.
>

Bir Kullanıcı akışı için yaş geçişi etkinleştirildiğinde, kullanıcılardan Doğum tarihi ve ikamet ülkesi sorulur. Bir Kullanıcı daha önce bilgileri girmeyen bir oturum açarsa, bir sonraki oturum açışlarında bu uygulamayı girmesi gerekir. Kurallar, bir kullanıcının her oturum açışında uygulanır.

![Yaş aşımları bilgi toplama akışı ekran görüntüsü](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C, kullanıcının küçük olup olmadığını belirlemek için girdiği bilgileri kullanır. Daha sonra, **Agegroup** alanı hesabında güncellenir. Değer,,, `null` `Undefined` ve olabilir `Minor` `Adult` `NotAdult` .  **Agegroup** ve **consentProvidedForMinor** alanları, daha sonra **ligalagegroupclassification** değerini hesaplamak için kullanılır.


## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>Kiracınızı yaş için ayarlama

Bir Kullanıcı akışında yaş geçişi kullanmak için kiracınızı ek özelliklere sahip olacak şekilde yapılandırmanız gerekir.

1. Üst menüdeki **Dizin + abonelik** filtresini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin, arama yapın ve **Azure AD B2C** seçin.
1. Sol taraftaki menüden kiracınız için **Özellikler** ' i seçin.
1. **Yaş geçişi** altında **Yapılandır**' ı seçin.
1. İşlemin tamamlanmasını bekleyin ve kiracınız yaş için ayarlanır.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Kullanıcı akışınızda yaş kullanım süresini etkinleştirme

Kiracınız yaş kullanımını kullanacak şekilde ayarlandıktan sonra bu özelliği, etkin olduğu [Kullanıcı akışlarında](user-flow-versions.md) kullanabilirsiniz. Ömrü aşağıdaki adımlarla etkinleştirilir:

1. Yaş geçişi etkin olan bir Kullanıcı akışı oluşturun.
1. Kullanıcı akışını oluşturduktan sonra menüdeki **Özellikler** ' i seçin.
1. **Yaş** aşımları bölümünde, **etkin**' i seçin.
1. **Kaydolma veya oturum açma** için, kullanıcıları nasıl yönetmek istediğinizi seçin:
    - Minörlerin uygulamanıza erişmesine izin verin.
    - Uygulamanıza erişmek için yalnızca izin yaşın altındaki azları engelle.
    - Tüm minörlerin uygulamanıza erişmesini engelleyin.
1. For **bloğunda**, aşağıdaki seçeneklerden birini seçin:
    - **BIR JSON 'ı uygulamaya geri gönder** -Bu seçenek, küçük bir blok uygulamanın uygulamaya geri yanıt gönderir.
    - **Bir hata sayfası göster** -kullanıcıya, uygulamaya erişeemiyorum bildiren bir sayfa gösterilir.

## <a name="test-your-user-flow"></a>Kullanıcı akışınızı test etme

1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** düğmesini seçin.
1. Yerel veya sosyal hesapla oturum açın. Daha sonra, ikametinizi ve küçük bir alt benzetimi yapan Doğum tarihini seçin. 
1. Testi yineleyin ve yetişkinin benzetimini yapan bir Doğum tarihi seçin.  

Küçük olarak oturum açtığınızda şu hata iletisini görmeniz gerekir: *ne yazık ki, oturum açma işlemi engellendi. Ülkenizdeki Gizlilik ve çevrimiçi güvenlik yasaları, alt öğelere ait hesaplara erişimi önler.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Özel ilkenizde yaş ve kullanım süresini etkinleştirme

1. [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/age-gating)'da yaş aşımları ilkesinin örneğini alın.
1. Her dosyada, dizeyi `yourtenant` Azure AD B2C kiracınızın adıyla değiştirin. Örneğin, B2C kiracınızın adı *contosob2c* ise, tüm örnekleri `yourtenant.onmicrosoft.com` olur `contosob2c.onmicrosoft.com` .
1. İlke dosyalarını karşıya yükleyin.

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C 'de Kullanıcı erişimini yönetmeyi](manage-user-access.md)öğrenin.

