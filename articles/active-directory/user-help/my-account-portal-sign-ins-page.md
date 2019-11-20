---
title: Oturum açma (Önizleme) sayfasından son oturum açma etkinliğinizi görüntüleme ve arama-Azure Active Directory | Microsoft Docs
description: Son oturum açma etkinliğinizi görüntüleme ve arama konusundaki ayrıntılar, Hesabım portalının oturum açma görevlerim sayfasından yapılır.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9d134e522575f30fd1f1be8176e0e1589faa7ac
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587634"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Son oturum açma etkinliğinizi, oturum açma (Önizleme) sayfasından görüntüleme ve arama

En son iş veya okul hesabı oturum açma etkinliğinizi, **Hesabım** portalının My **Sign-ins** sayfasından görüntüleyebilirsiniz. Oturum açma geçmişinizi gözden geçirmek, şunları görmenizi sağlayarak olağan dışı etkinlikleri denetlemenize yardımcı olur:

- Parolanızı tahmin etmeye çalışıyorsa.

- Bir saldırgan hesabınızda başarıyla oturum açmışsa ve bu konumdan.

- Saldırganın erişmeye çalıştığı uygulamalar.

## <a name="view-your-recent-sign-in-activity"></a>Son oturum açma etkinliğinizi görüntüleyin

1. İş veya okul hesabınızda oturum açın ve ardından https://myprofile.microsoft.com/ sayfanıza gidin.

2. Sol gezinti bölmesinden **oturum açma işlemlerini (Önizleme)** seçin veya **oturum açma (Önizleme)** bloğundan **son etkinliği gözden geçir** bağlantısını seçin.

    ![Son kullanılan etkinlik bağlantılarını gösteren Hesabım sayfası](media/my-account-portal/my-account-portal-sign-ins.png)

3. Her bir oturum açma öğesini genişletip gözden geçirerek her birini tanıdığınızdan emin olun. Tanıdık görünmeyen bir oturum açma öğesi bulursanız, tehlikede olduğu takdirde hesabınızı korumanıza yardımcı olması için parolanızı değiştirmenizi önemle tavsiye ederiz.

    ![Genişletilmiş oturum açma ayrıntılarının bulunduğu son etkinlik sayfası](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Başarılı bir oturum açma görürseniz

Kendi etkinliğinizi normal olarak tanıyabilmelisiniz. Ancak, garip bir konumdan, tarayıcıdan veya işletim sisteminden başarılı bir oturum açma bildirimi yaptıysanız, bir saldırganın hesabınıza erişim kazandığı anlamına gelebilir. Bu durumda, parolanızı hemen değiştirmenizi ve güvenlik ayarlarınızı güncelleştirmek için [güvenlik bilgisi](https://mysignins.microsoft.com/security-info) sayfasına gitmeniz önerilir.

Bir şeyin yanlış olduğunu belirlemediğinizden önce, yanlış pozitif bir değer görmediğinden emin olun (öğenin şüpheli olduğu, ancak sorun devam ettiği). Örneğin, IP adresinizi temel alarak yaklaşık konum ve eşlemenizi tespit ediyoruz. Mobil ağlar, bazen trafiği uzak konumlara yönlendirdiklerinden, özellikle de sabit bir şekilde kullanılır. Bu nedenle, mobil cihazınızı Washington durumunda kullanarak açtıysanız, konum California 'dan gelen oturum açma durumunu gösterebilir. Bu nedenle, yalnızca konumun ötesinde daha fazla ayrıntı denetlemeniz önemle tavsiye ederiz. Ayrıca, aynı zamanda işletim sisteminin, tarayıcının ve uygulamanın anlamlı olduğundan emin olmanız gerekir.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Başarısız bir oturum açma görürseniz

Oturum etkinliği olmadan başarısız bir oturum açma işlemi, birincil kimlik doğrulama yönteminizin (Kullanıcı adı/parola) başarısız olduğu anlamına gelir. Bu, Kullanıcı adınızı veya parolanızı yanlış yazmış olduğunuz anlamına gelir, ancak bir saldırganın parolanızı tahmin etmeye çalıştığı anlamına da gelebilir. Bir saldırganın parolanızı tahmin edemeyeceğini düşünüyorsanız parolanızı değiştirmeniz gerekmez, ancak Azure Multi-Factor Authentication (MFA) için kaydolmanızı önemle öneririz. MFA ile, korsanın parolanızı tahmin etse bile hesabınıza erişmek için yeterli olmayacaktır.

Başarısız bir oturum açma görürseniz, oturum etkinliği altında, **ek doğrulama başarısız oldu, geçersiz kod**ile, birincil kimlik doğrulamanın (Kullanıcı adı/parola) başarılı olduğu ancak MFA 'nın başarısız olduğu anlamına gelir. Bu bir saldırgan ise parolanızı doğru tahmin eder ancak MFA Challenge 'ı yine de geçemeyebilir. Bu durumda, saldırgan söz konusu bölüm hakkına sahip olduğundan ve ardından güvenlik ayarlarınızı güncelleştirmek için [güvenlik bilgisi](https://mysignins.microsoft.com/security-info) sayfasına giderek parolanızı değiştirmenize devam etmenizi öneririz.

## <a name="search-for-specific-sign-in-activity"></a>Belirli bir oturum açma etkinliğini arayın

En son oturum açma etkinliğinizi kullanılabilir bilgilerden herhangi birini izleyerek arayabilirsiniz. Örneğin, işletim sistemi, konum, uygulama ve benzeri en son oturum açma etkinliğinizi arayabilirsiniz.

1. **Son etkinliği gözden geçir** sayfasında, **arama çubuğuna aramak** istediğiniz bilgileri yazın. Örneğin, Hesabım uygulaması tarafından toplanan tüm etkinlikleri aramak için `My Account` yazın.

2. Aramaya başlamak için **Ara** düğmesini seçin.

    ![Son etkinlik sayfası, vurgulanan arama çubuğu, arama düğmesi ve sonuçları gösterme](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Sonraki adımlar

Son oturum açma etkinliğinizi görüntüledikten sonra şunları yapabilirsiniz:

- [Güvenlik bilgilerinizi](user-help-security-info-overview.md)görüntüleyin veya yönetin.

- Bağlı [cihazlarınızı](my-account-portal-devices-page.md)görüntüleyin veya yönetin.

- [Kuruluşları](my-account-portal-organizations-page.md)görüntüleyin veya yönetin.

- Kuruluşunuzun [gizlilikle ilgili verilerinizi nasıl kullandığını](my-account-portal-privacy-page.md)görüntüleyin.
