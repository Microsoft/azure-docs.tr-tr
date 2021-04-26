---
title: Oturum açma etkinliklerim sayfasından son oturum açma etkinliğinizi görüntüleyin ve arayın-Azure Active Directory | Microsoft Docs
description: Son oturum açma etkinliğinizi görüntüleme ve arama konusundaki ayrıntılar, Hesabım portalının oturum açma görevlerim sayfasından yapılır.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.openlocfilehash: 1062c8dfe416af2d39063bf71f39d52e151e28d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096081"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>Oturum açma etkinliklerim sayfasından son oturum açma etkinliğinizi görüntüleyin ve arayın

En son iş veya okul hesabı oturum açma etkinliğinizi, **Hesabım** portalının My **Sign-ins** sayfasından görüntüleyebilirsiniz. Oturum açma geçmişinizi gözden geçirmek, şunları görmenizi sağlayarak olağan dışı etkinlikleri denetlemenize yardımcı olur:

- Parolanızı tahmin etmeye çalışıyorsa.
- Bir saldırgan hesabınızda başarıyla oturum açmışsa ve bu konumdan.
- Saldırganın erişmeye çalıştığı uygulamalar.

>[!Note]
> Kişisel Microsoft hesabı oturum açarken bir hata görürseniz, kuruluşunuzun etki alanı adını (örneğin contoso.com) veya aşağıdaki URL 'Lerden birindeki kuruluşunuzun **KIRACı kimliğini** kullanarak oturum açabilirsiniz:
>
>   - https://myaccount.microsoft.com?tenantId=*your_domain_name*
>   - https://myaccount.microsoft.com?tenant=*your_tenant_ID*

## <a name="view-your-recent-sign-in-activity"></a>Son oturum açma etkinliğinizi görüntüleme

1. İş veya okul hesabınızda oturum açın ve ardından https://myaccount.microsoft.com/ sayfanıza gidin.

2. Sol gezinti bölmesinden **oturum açma** işlemlerini seçin veya **oturum kapatma** bloğundan **son etkinliği gözden geçir** bağlantısını seçin.

    ![Son kullanılan etkinlik bağlantılarını gösteren Hesabım sayfası](media/my-account-portal/my-account-portal-sign-ins.png)

3. Her bir oturum açma öğesini genişletip gözden geçirerek her birini tanıdığınızdan emin olun. Tanıdık olmayan bir oturum açma öğesi bulursanız, tehlikede olduğu durumlarda hesabı korumak için parolanızı değiştirin.

    ![Genişletilmiş oturum açma ayrıntılarının bulunduğu son etkinlik sayfası](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>Başarılı bir oturum açma görürseniz

Bazen kendi normal oturum açma etkinliğinizi gözden geçirirken, bilmediğiniz bir konumdan, tarayıcıdan veya işletim sisteminden başarılı bir oturum açma işlemi görebilirsiniz. Bilmediğiniz oturum açma işlemleri bir saldırganın hesabınıza erişim kazandığını ifade edebilir. Yetkilendirmediğiniz etkinlikleri görürseniz, parolanızı hemen değiştirmenizi ve ardından güvenlik bilgilerini güncelleştirmek için [güvenlik bilgileri](https://mysignins.microsoft.com/security-info) ' ne gitmeniz önerilir.

Bir şeyin yanlış olduğunu belirlemediğinizden önce, yanlış pozitif bir değer görmediğinden emin olun (öğenin şüpheli olduğu, ancak sorun devam ettiği). Örneğin, IP adresinizi temel alarak yaklaşık konum ve eşlemenizi tespit ediyoruz. Mobil ağlar, bazen trafiği uzak konumlara yönlendirdiklerinden, özellikle de sabit bir şekilde kullanılır. Mobil cihazınızı Washington durumunda oturum açsanız bile, konum California 'dan gelen oturum açma durumunu gösterebilir. Yalnızca konumun ötesinde ayrıntıları denetlemeniz önemle tavsiye ederiz. Aynı işletim sisteminin, tarayıcının ve uygulamanın anlamlı olduğundan emin olun.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Başarısız bir oturum açma görürseniz

Başarısız bir oturum açma görürseniz, kimlik bilgilerinizi yanlış yazmış olursunuz. Bu, bir saldırganın parolanızı tahmin etmeye çalıştığı anlamına da gelebilir. Bu riske yanıt vermek için parolanızı değiştirmeniz gerekmez, ancak Azure AD Multi-Factor Authentication (MFA) için kaydolmanızı öneririz. Multi-Factor Authentication sayesinde, korsanın parolanızı tahmin etse bile, hesaba erişmek için yeterli olmaz.

![Başarısız oturum açma kutucuğu](media/my-account-portal-sign-ins-page/unsuccessful.png)

**Oturum etkinlik** ' in altında bir notla başarısız bir oturum açma görürseniz, `Additional verification failed, invalid code` Bu, birincil kimlik doğrulama kimlik bilgilerinizin başarılı olduğu ancak Multi-Factor Authentication başarısız olduğu anlamına gelir. Bu durum, bir saldırganın parolanızı doğru tahmin ettiğinde ancak Multi-Factor Authentication sınamasını geçemedik anlamına gelebilir. Saldırgan zaten sahip olabileceğinden ve güvenlik ayarlarınızı güncelleştirmek için [güvenlik bilgileri](https://mysignins.microsoft.com/security-info) sayfasına giderek parolanızı değiştirmenize devam etmenizi öneririz.

## <a name="search-for-specific-sign-in-activity"></a>Belirli bir oturum açma etkinliğini arayın

En son oturum açma etkinliğinizi kullanılabilir bilgilerden herhangi birini izleyerek arayabilirsiniz. Örneğin, işletim sistemi, konum, uygulama ve benzeri en son oturum açma etkinliğinizi arayabilirsiniz.

1. **Son etkinliği gözden geçir** sayfasında, **arama çubuğuna aramak** istediğiniz bilgileri yazın. Örneğin, `Unsuccessful` Hesabım uygulaması tarafından toplanan tüm başarısız oturum açma etkinliklerini aramak için yazın.

2. Aramaya başlamak için **Ara** düğmesini seçin.

    ![Son etkinlik sayfası, vurgulanan arama çubuğu, arama düğmesi ve sonuçları gösterme](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>Olağan dışı etkinliği Onayla

Olağan dışı etkinlik olarak işaretlenen oturum açma işlemleri, **oturum açma 'lerim** sayfasında söz konusu etkinliğin kutucuğunda onaylanır.

![İçin olağan dışı oturum açma kutucuğu, oturum açma girişiminde bulunduğunu onaylar veya denemeyin](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>Sonraki adımlar

Son oturum açma etkinliğinizi görüntüledikten sonra şunları yapabilirsiniz:

- [Güvenlik bilgilerinizi](./security-info-setup-signin.md)görüntüleyin veya yönetin.

- Bağlı [cihazlarınızı](my-account-portal-devices-page.md)görüntüleyin veya yönetin.

- [Kuruluşları](my-account-portal-organizations-page.md)görüntüleyin veya yönetin.

- Kuruluşunuzun [gizlilikle ilgili verilerinizi nasıl kullandığını](my-account-portal-privacy-page.md)görüntüleyin.

- [Hesap Portalı ayarlarınızı](my-account-portal-settings.md) değiştirme