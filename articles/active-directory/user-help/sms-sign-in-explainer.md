---
title: Telefon numarası için SMS oturum açma kullanıcı deneyimi-Azure AD
description: Yeni veya mevcut telefon numaraları için SMS oturum açma kullanıcı deneyimi hakkında daha fazla bilgi edinin
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/21/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 1a50f2032a978a552205d1bba602249f34f0478a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98661610"
---
# <a name="use-your-phone-number-as-a-user-name"></a>Telefon numaranızı Kullanıcı adı olarak kullanma

Bir cihazın kaydedilmesi, kuruluşunuzun hizmetlerine yönelik telefondan erişim sağlar ve kuruluşunuzun telefonunuza erişmesine izin vermez. Yönetici değilseniz, [SMS tabanlı kimlik doğrulaması için kullanıcıları yapılandırma ve etkinleştirme](../authentication/howto-authentication-sms-signin.md)bölümünde daha fazla bilgi bulabilirsiniz.

Kuruluşunuz kullanılabilir SMS oturum açma yapmadıysa, hesabınız ile telefon kaydedilirken bir seçenek görmezsiniz.  

## <a name="when-you-have-a-new-phone-number"></a>Yeni bir telefon numaranız olduğunda

Yeni bir telefon veya yeni numara alırsanız ve bunu SMS oturum açma 'nın kullanılabildiği bir kuruluşa kaydettiğinizde, normal telefon kayıt işlemine karşılaşırsınız:

1. **Add metodunu** seçin.
1. **Telefon** seçin.
1. Telefon numarası girin ve **bana bir kod** seçin.
1. Kodu girdikten sonra **İleri**' yi seçin.
1. "SMS doğrulandı" ifadesini bildiren bir istem görürsünüz. Telefonunuz başarıyla kaydedildi. "

> [!Important]
> Bilinen bir sorun nedeniyle telefon numarası eklenirken kısa bir süre için SMS oturum açma numarası kayıt etmez. Ek numara ile oturum açmanız ve sonra SMS oturum açma için numarayı kaydetmek üzere istemleri izlemeniz gerekir.

### <a name="when-the-phone-number-is-in-use"></a>Telefon numarası kullanımda olduğunda

Kuruluşunuzdaki başka birinin kullandığı bir telefon numarası kullanmayı denerseniz, aşağıdaki iletiyi görürsünüz:

![Telefon numaranız zaten kullanıldığında oluşan hata iletisi](media/sms-sign-in-explainer/sms-sign-in-error.png)

Sorunu gidermek için yöneticinize ulaşın.

## <a name="when-you-have-an-existing-number"></a>Mevcut bir numaranız varsa

Zaten bir kuruluşla telefon numarası kullanıyorsanız ve telefon numaranızı Kullanıcı adı olarak kullanıyorsanız, aşağıdaki adımlar oturum açmanıza yardımcı olabilir.

1. SMS oturum açma kullanılabilir olduğunda, SMS oturum açma için telefon numarasını etkinleştirmek isteyip istemediğinizi soran bir başlık görüntülenir:

    :::image type="content" source="media/sms-sign-in-explainer/sms-sign-in-banner.png" alt-text="' Etkinleştir ' eylemi seçili bir telefon numarası için SMS oturum açma özelliğini etkinleştiren başlığı gösteren ekran görüntüsü." lightbox="media/sms-sign-in-explainer/sms-sign-in-banner.png":::

1. Ayrıca, telefon yöntemi kutucuğunda giriş işaretini seçerseniz bir **Etkinleştir** düğmesi görünür:

    [![Telefon numarası için SMS oturum açma özelliğini etkinleştirmek için başlık.](media/sms-sign-in-explainer/sms-sign-in-phone-method.png)](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Yöntemi etkinleştirmek için **Etkinleştir**' i seçin. Eylemi onaylamanız istenir:

    ![Telefon numarası için SMS oturum açmayı etkinleştirmek üzere onay iletişim kutusu](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. **Etkinleştir**’i seçin.

## <a name="when-you-remove-your-phone-number"></a>Telefon numaranızı kaldırdığınızda

1. Telefon numarasını silmek için SMS oturum açma telefonu yöntemi kutucuğunda Sil düğmesini seçin.

    [![Telefon numarası için SMS oturum açma silmek için başlık.](media/sms-sign-in-explainer/sms-sign-in-delete-method.png)](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Eylemi onaylamanız istendiğinde **Tamam**' ı seçin.

Varsayılan oturum açma yöntemi olarak kullanımda olan bir telefon numarasını kaldıramazsınız. Sayıyı kaldırmak için, varsayılan oturum açma yöntemini değiştirmeniz ve sonra telefon numarasını yeniden kaldırmanız gerekir.
