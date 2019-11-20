---
title: Erişim gözden geçirmeleriyle gruplara veya uygulamalara erişimi gözden geçirme-Azure Active Directory | Microsoft Docs
description: Azure Active Directory erişim incelemeleriyle grup üyelerinin veya uygulama erişiminin erişimini incelemeyi öğrenin.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e51ad8faecd935ea999b1287e542e4b14d55290a
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948235"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmeleriyle gruplara veya uygulamalara erişimi gözden geçirme

Azure Active Directory (Azure AD), kuruluşların Azure AD 'de bulunan gruplara ve uygulamalara ve Azure AD erişim gözden geçirmeleri adlı bir özellik ile diğer Microsoft çevrimiçi hizmetlerine erişimi nasıl yöneteceğini basitleştirir.

Bu makalede, belirlenen bir gözden geçirenin, bir grubun üyeleri veya bir uygulamaya erişimi olan kullanıcılar için erişim incelemesi nasıl gerçekleştirdiği açıklanır.

## <a name="open-the-access-review"></a>Erişim gözden geçirmesini açın

Erişim gözden geçirmesi gerçekleştirmeye yönelik ilk adım, erişim incelemesini bulup açmak.

1. Microsoft 'un, erişimi incelemenizi isteyen bir e-posta bulun. Bir grubun erişimini gözden geçirmek için örnek bir e-posta aşağıda verilmiştir.

    ![Bir gruba erişimi gözden geçirmek için Microsoft 'tan örnek e-posta](./media/perform-access-review/access-review-email.png)

1. Erişim gözden geçirmesini açmak için **Incelemeye başla** bağlantısına tıklayın.

E-postanız yoksa, bekleyen erişim incelemelerinizi aşağıdaki adımları izleyerek bulabilirsiniz.

1. [@No__t-1](https://myapps.microsoft.com)' de uygps portalında oturum açın.

    ![Uygulamaps portalı izinleriniz olan uygulamaları listeleme](./media/perform-access-review/myapps-access-panel.png)

1. Sayfanın sağ üst köşesinde, adınızı ve varsayılan kuruluşunuzu görüntüleyen Kullanıcı simgesine tıklayın. Birden fazla kuruluş listeleniyorsa, erişim incelemesi isteyen organizasyonu seçin.

1. Bekleyen erişim incelemelerinin listesini görmek için **erişim İncelemeleri** kutucuğuna tıklayın.

    Kutucuk görünür değilse, bu kuruluş için gerçekleştirilecek hiçbir erişim incelemesi yoktur ve şu anda hiçbir işlem yapmanız gerekmez.

    ![Uygulamalar ve gruplar için bekleyen erişim İncelemeleri listesi](./media/perform-access-review/access-reviews-list.png)

1. Gerçekleştirmek istediğiniz erişim incelemesi için **gözden geçirmeyi Başlat** bağlantısına tıklayın.

## <a name="perform-the-access-review"></a>Erişim gözden geçirmesini gerçekleştir

Erişim gözden geçirmesini açtıktan sonra, incelenmesi gereken kullanıcıların adlarını görürsünüz.

İstek kendi erişiminizi gözden geçirmek ise, sayfa farklı görünür. Daha fazla bilgi için bkz. [gruplar veya uygulamalar için kendinize erişimi gözden geçirme](review-your-access.md).

![Gözden geçirilmesi gereken kullanıcıları listelemek için açık erişim gözden geçirme](./media/perform-access-review/perform-access-review.png)

Erişimi onaylamak veya reddetmek için kullanabileceğiniz iki yol vardır:

- Bir veya daha fazla kullanıcı için erişimi onaylayabilir veya reddedebilirsiniz veya
- En kolay ve en hızlı şekilde sistem önerilerini kabul edebilirsiniz.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Bir veya daha fazla kullanıcı için erişimi onaylama veya reddetme

1. Devam eden erişimlerini onaylama veya reddetme seçeneklerinden birini belirlemek için Kullanıcı listesini gözden geçirin.

1. Tek bir kullanıcıya erişimi onaylamak veya reddetmek için, yapılacak eylemi belirtmek üzere bir pencere açmak için satıra tıklayın. Birden çok kullanıcının erişimini onaylamak veya reddetmek için, kullanıcıların yanındaki onay işaretlerini ekleyin ve sonra gerçekleştirilecek eylemi belirtmek üzere bir pencere açmak için **X kullanıcıları gözden geçir** düğmesine tıklayın.

1. **Onayla** veya **Reddet**' e tıklayın. Emin değilseniz, **bilinmiyor**' a tıklayabilirsiniz. Bunun yapılması kullanıcının erişimini sürdürmasına neden olur, ancak seçim denetim günlüklerine yansıtılır.

    ![Onaylama, reddetme ve bilmiyorum seçeneklerini içeren eylem penceresi](./media/perform-access-review/approve-deny.png)

1. Gerekirse, **neden** kutusunda bir neden girin.

    Erişim incelemesinin Yöneticisi, devam eden erişimi veya grup üyeliğini onaylamak için bir neden belirtmeniz gerekebilir.

1. Gerçekleştirilecek eylemi belirledikten sonra **Kaydet**' e tıklayın.

    Yanıtınızı değiştirmek istiyorsanız, satırı seçin ve yanıtı güncelleştirin. Örneğin, daha önce reddedilen bir kullanıcıyı onaylayabilir veya daha önce onaylanmış bir kullanıcıyı reddedebilirsiniz. Erişim incelemesi sonlanana kadar yanıtınızı dilediğiniz zaman değiştirebilirsiniz.

    Birden çok gözden geçiren varsa, son gönderilen yanıt kaydedilir. Yöneticinin iki gözden geçiren – Gamze ve Bob 'u aldığı bir örnek düşünün. Çiğdem önce erişim gözden geçirmeyi açar ve erişimi onaylar. İnceleme bitmeden önce, Bob erişim incelemesini açar ve erişimi reddeder. Son reddetme yanıtı kaydedilir.

    > [!NOTE]
    > Bir kullanıcının erişimi reddedildiyse, bu kişiler hemen kaldırılmaz. İnceleme sona erdiğinde veya bir yönetici incelemeyi durdurduklarında bunlar kaldırılır.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Önerilere göre erişimi onaylayın veya reddedin

Erişim incelemelerini sizin için daha kolay ve hızlı hale getirmek için, tek bir tıklama ile kabul etmenizi sağlayacak öneriler de sağlarız. Öneriler, kullanıcının oturum açma etkinliğine göre oluşturulur.

1. Sayfanın altındaki mavi çubukta **öneriyi kabul et**' e tıklayın.

    ![Önerileri kabul et düğmesini gösteren erişim gözden geçirme listesini aç](./media/perform-access-review/accept-recommendations.png)

    Önerilen eylemlerin bir özetini görürsünüz.

    ![Önerilen eylemlerin özetini görüntüleyen pencere](./media/perform-access-review/accept-recommendations-summary.png)

1. Önerileri kabul etmek için **Tamam** ' ı tıklatın.

## <a name="next-steps"></a>Sonraki adımlar

- [Grupların veya uygulamaların erişim incelemesini tamamlar](complete-access-review.md)
