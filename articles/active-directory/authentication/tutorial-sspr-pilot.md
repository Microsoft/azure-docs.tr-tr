---
title: Azure AD SSPR pilotunu etkinleştirme
description: Bu öğreticide bir pilot kullanıcı grubu için Azure AD self servis parola sıfırlama özelliğini etkinleştireceksiniz
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 685c928a20e9bfb47365b5ffa769f1a223d2c563
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69561508"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Öğretici: Bir Azure AD self servis parola sıfırlama pilot kullanıma alma

Bu öğreticide kuruluşunuzda Azure AD self servis parola sıfırlama (SSPR) pilotu dağıtımını tamamlayacak ve yönetici olmayan bir hesabı kullanarak test edeceksiniz.

Self servis parola sıfırlamanın herhangi bir testinin yönetici olmayan hesaplarla yapılması önemlidir. Yönetici hesaplarının parola sıfırlama ilkesi Microsoft tarafından yönetilmektedir ve bunun için daha güçlü kimlik doğrulama yöntemleri kullanılmaktadır. Bu ilke, güvenlik sorularının ve yanıtlarının kullanılmasına izin vermez ve sıfırlama için iki yöntemin kullanılmasını gerektirir.

> [!div class="checklist"]
> * Kendi kendine parola sıfırlamayı etkinleştirme
> * Self servis parola sıfırlamayı kullanıcı olarak test etme

## <a name="prerequisites"></a>Önkoşullar

* Genel Yönetici hesabı

## <a name="enable-self-service-password-reset"></a>Kendi kendine parola sıfırlamayı etkinleştirme

1. Genel Yönetici hesabını kullanarak [Azure portal](https://portal.azure.com) oturumu açın.
1. **Azure Active Directory**'ye göz atın ve **Parola sıfırlama**'yı seçin.
1. Bir pilot grupla başlayarak self servis parola sıfırlama özelliği kuruluşunuzdaki kullanıcıların yalnızca bir bölümü için etkinleştirin.
   * **Özellikler** sayfasında, **self servis parola sıfırlama etkin**seçeneğinin altında, **Seçili**' i seçin ve bir pilot grup seçin.
      * SSPR işlevini yalnızca sizin seçtiğiniz belirli bir Azure AD grubunun üyeleri kullanabilir. Bu işlevin dağıtımını yaparken kavram kanıtı için bir kullanıcı grubu tanımlamanız ve bu ayarı kullanmanız önerilir. Güvenlik gruplarının iç içe geçmesi burada desteklenmiyor.
      * Seçtiğiniz gruptaki kullanıcıların gerekli lisanslara sahip olduğundan emin olun.
   * **Kaydet**'e tıklayın.
1. **Kimlik doğrulama yöntemleri** sayfasında
   * **1** ' e **sıfırlamak Için gereken yöntem sayısını** ayarlayın
   * **Kullanıcıların yararlanabileceği yöntemler** bölümünde kuruluşunuzun izin vermek istediği yöntemleri seçin. Bu öğretici için, **e-posta**, **cep telefonu**, **ofis telefonu**, **mobil uygulama bildirimi**ve **mobil uygulama kodunu**etkinleştirmek üzere kutulara göz atın.
   * **Kaydet**'e tıklayın.
1. **Kayıt** sayfasında
   * **Kullanıcılardan oturum açarken kaydolmalarını iste** için **Evet**'i seçin.
   * **Kullanıcıların kimlik doğrulaması bilgilerini yeniden onaylamasını istemeden önce geçen gün sayısı** ayarını **180** olarak belirleyin.
   * **Kaydet**'e tıklayın.
1. **Bildirimler** sayfasında
   * **Parola sıfırlamayı kullanıcılara bildirme** seçeneğini **Evet** olarak ayarlayın.
   * **Diğer yöneticiler parolalarını sıfırladığında tüm yöneticilere bildirme** seçeneğini **Evet** olarak ayarlayın.
1. **Özelleştirme** sayfasında
   * Microsoft, **Yardım masası bağlantısını özelleştir** ayarını **Evet** olarak değiştirmenizi ve **Özel yardım masası e-postası veya URL'si** alanına kullanıcılarınızın kuruluş içinde ek yardım almak için kullanabileceği bir e-posta adresi veya web sayfası URL'si yazmanızı önerir.
   * Bu öğretici için **Özelleştirme yardım masası bağlantısını** **Hayır**olarak ayarlayacağız.

Self servis parola sıfırlamayı pilot grubunuzdaki bulut kullanıcıları için yapılandırdık.

## <a name="test-sspr-as-a-user"></a>Self servis parola sıfırlamayı kullanıcı olarak test etme

Self servis parola sıfırlamayı pilot grubunuza üye olan ve yönetici olmayan bir kullanıcıyla deneyin. **Kendisine atanmış yönetici rollerine sahip bir hesap kullanıyorsanız, kimlik doğrulama yöntemleri ve sayı, Microsoft yönetici ilkesini yönettiği için seçtiğinizden farklı olabilir.**

1. Yeni bir InPrivate veya gizli tarayıcı penceresi açın.
1. Test kullanıcısıyla [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) adresindeki kayıt portalından self servis parola sıfırlama hizmetine kaydolun.
1. Aynı test kullanıcısıyla [https://aka.ms/sspr](https://aka.ms/sspr) adresindeki self servis parola sıfırlama portalına gidin ve önceki adımda girdiğiniz bilgileri kullanarak parolanızı sıfırlamayı deneyin.
1. Parolanızın başarıyla sıfırlanması gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide yapılandırdığınız işlevi kullanmak istemediğinize karar verirseniz aşağıdaki değişikliği yapın.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. **Azure Active Directory**'ye göz atın ve **Parola sıfırlama**'yı seçin.
1. **Özellikler** sayfasında, **Self servis parola sıfırlama etkinleştirildi** seçeneğinin altında **Yok**'u seçin.
1. **Kaydet**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide Azure AD'de self servis parola sıfırlama özelliğini etkinleştirdiniz. Self servis parola sıfırlama deneyimini şirket içi Active Directory Domain Services altyapısıyla nasıl tümleştirebileceğinizi görmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [SSPR şirket içi geri yazma tümleştirmesini etkinleştirme](tutorial-enable-writeback.md)
