---
title: Azure AD Yetkilendirme Yönetimi 'nde bir erişim paketinin erişimini gözden geçirme
description: Azure Active Directory erişim gözden geçirmeleri (Önizleme) içinde, Yetkilendirme Yönetimi erişim paketlerinin erişim incelemesini tamamlamayı öğrenin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a78a69301ab2ae637531f8643d9d57a8b44b563f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608936"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde bir erişim paketinin erişimini gözden geçirme

Azure AD Yetkilendirme Yönetimi, kuruluşların gruplara, uygulamalara ve SharePoint sitelerine erişimi nasıl yöneteceğini basitleştirir. Bu makalede, belirli bir gözden geçiren olarak bir erişim paketine atanan diğer kullanıcılar için erişim incelemelerinin nasıl gerçekleştirileceği açıklanır.

## <a name="prerequisites"></a>Ön koşullar

Kullanıcıların etkin erişim paketi atamalarını gözden geçirmek için erişim gözden geçirmesi yapmak üzere önkoşulları karşılamanız gerekir:
- Azure AD Premium P2
- Genel yönetici
- Belirlenen Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

Daha fazla bilgi için bkz. [Lisans gereksinimleri](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>Erişim gözden geçirmesini açın

Erişim gözden geçirmesini bulmak ve açmak için aşağıdaki adımları kullanın:

1. Microsoft 'tan erişimi incelemenizi isteyen bir e-posta alabilirsiniz. Erişim gözden geçirmesini açmak için e-postayı bulun. Erişimi gözden geçirmek için örnek bir e-posta aşağıda verilmiştir:
    
    ![Access Review gözden geçiren e-postası](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Erişim gözden geçirmesini açmak için **Kullanıcı erişimini gözden geçir** bağlantısına tıklayın. 

1. E-postanız yoksa, bekleyen erişim incelemelerinizi doğrudan https://myaccess.microsoft.comgiderek bulabilirsiniz.

1. Size atanan bekleyen erişim incelemelerinin listesini görmek için sol gezinti çubuğundaki **erişim İncelemeleri** ' ne tıklayın.
    
    ![Erişimimde erişim gözden geçirmeleri seçin](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Başlamak istediğiniz gözden geçirmeyi tıklatın.
    
    ![Erişim gözden geçirmesini seçin](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Erişim gözden geçirmesini gerçekleştir

Erişim gözden geçirmesini açtığınızda, gözden geçirmeniz gereken kullanıcıların adlarını görürsünüz. Erişimi onaylamak veya reddetmek için kullanabileceğiniz iki yol vardır:
- Bir veya daha fazla kullanıcının erişimini el ile onaylayabilir veya reddedebilirsiniz
- Sistem önerilerini kabul edebilirsiniz

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Bir veya daha fazla kullanıcının erişimini el ile onaylama veya reddetme
1. Kullanıcıların listesini gözden geçirin ve erişime sahip olmaya devam etmesi gereken kullanıcıları saptayın.

    ![Gözden geçirilecek kullanıcıların listesi](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Erişimi onaylamak veya reddetmek için, kullanıcının adının solundaki radyo düğmesini seçin.

1. Kullanıcı adlarının üzerindeki çubukta **Onayla** veya **Reddet** ' i seçin.

    ![Kullanıcıyı seçin](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Emin değilseniz, **bilinmiyor** düğmesine tıklayabilirsiniz.

    Bu seçimi yaparsanız, Kullanıcı erişimi korur ve bu seçim denetim günlüklerine gider. Günlükte gözden geçirmeyi tamamladığınız diğer gözden geçirenler gösterilir.

1. Kararınız için bir neden belirtmeniz gerekebilir. Bir neden yazın ve **Gönder**' e tıklayın.

    ![Erişimi onayla veya Reddet](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Kararlarınızı İnceleme sonundan önce istediğiniz zaman değiştirebilirsiniz. Bunu yapmak için listeden kullanıcıyı seçin ve kararı değiştirin. Örneğin, daha önce reddetmiş olduğunuz bir kullanıcı için erişimi onaylayabilirsiniz.

Birden çok gözden geçiren varsa, son gönderilen yanıt kaydedilir. Yöneticinin iki gözden geçiren – Gamze ve Bob 'u aldığı bir örnek düşünün. Çiğdem önce incelemeyi açar ve erişimi onaylar. İnceleme bitmeden önce, Bob gözden geçirmeyi açıp erişimi reddeder. Bu durumda, son reddetme erişimi kararı kaydedilir.

>[!NOTE]
>Bir kullanıcının erişimi reddedildiyse, bu kişiler erişim paketinden hemen kaldırılmaz. Gözden geçirme sona erdiğinde Kullanıcı erişim paketinden kaldırılır veya bir yönetici incelemeyi sonlandırır.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Sistem tarafından oluşturulan önerileri kullanarak erişimi onaylama veya reddetme

Birden çok kullanıcının erişimini daha hızlı bir şekilde gözden geçirmek için, tek bir tıklama ile önerileri kabul ederek sistem tarafından oluşturulan önerileri kullanabilirsiniz. Öneriler, kullanıcının oturum açma etkinliğine göre oluşturulur.

1.  Sayfanın üst kısmındaki çubukta **öneriyi kabul et**' e tıklayın.
    
    ![Önerileri kabul et ' i seçin](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Önerilen eylemlerin bir özetini görürsünüz.

1.  Önerileri kabul etmek için **Gönder** ' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim paketlerini kendi kendine gözden geçirme](entitlement-management-access-reviews-self-review.md)