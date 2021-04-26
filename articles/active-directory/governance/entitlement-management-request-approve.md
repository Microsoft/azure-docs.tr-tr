---
title: Erişim isteklerini onaylama veya reddetme-Azure AD Yetkilendirme Yönetimi
description: Azure Active Directory yetkilendirme yönetimi 'nde bir erişim paketine yönelik istekleri onaylamak veya reddetmek için erişim portalını nasıl kullanacağınızı öğrenin.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: fddb3b171e5a26273cb2e0045f11e3a4dbb48c5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97655110"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde erişim isteklerini onaylama veya reddetme

Azure AD yetkilendirme yönetimi sayesinde, ilkeleri erişim paketleri için onay gerektirecek şekilde yapılandırabilir ve bir veya daha fazla onaylayan seçebilirsiniz. Bu makalede, belirlenen onaylayanlar erişim paketleri için istekleri onaylama veya reddetme işlemlerinin nasıl yapılacağı açıklanır.

## <a name="open-request"></a>Açık istek

Erişim isteklerini onaylamaya veya reddetmeye yönelik ilk adım, onay bekleyen erişim isteğini bulup açar. Erişim isteğini açmak için iki yol vardır.

**Önkoşul rolü:** Kişi

1. Bir isteği onaylamayı veya reddetmenizi isteyen Microsoft Azure bir e-posta bulun. Örnek bir e-posta aşağıda verilmiştir:

    ![Paket e-postasına erişim isteğini onayla](./media/entitlement-management-shared/approver-request-email.png)

1. Erişim isteğini açmak için **Onayla veya reddetme isteği** bağlantısına tıklayın.

1. Erişim Portalı ' nda oturum açın.

E-postanız yoksa, bu adımları izleyerek Onayınızı bekleyen erişim isteklerini bulabilirsiniz.

1. Konumundaki erişim portalında oturum açın [https://myaccess.microsoft.com](https://myaccess.microsoft.com) .  (ABD kamu için, erişim portalındaki etki alanı bağlantısı olacaktır `myaccess.microsoft.us` .)

1. Onay bekleyen erişim isteklerinin bir listesini görmek için sol taraftaki menüden **onaylar** ' a tıklayın.

1. **Bekleyen** sekmesinde, isteği bulun.

## <a name="view-requestors-answers-to-questions-preview"></a>İstek sahibinin soruların yanıtlarını görüntüleyin (Önizleme)

1. Erişimimde **onaylar** sekmesine gidin.

1. Onaylamak istediğiniz isteğe gidin ve **Ayrıntılar**' a tıklayın. Karar vermek için hazırsanız **Onayla** veya **Reddet** ' e de tıklayabilirsiniz.

1. **İstek ayrıntıları**' na tıklayın.

    ![Erişim Portalı-erişim isteği-tıklama isteği ayrıntıları](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. İstek sahibi tarafından belirtilen bilgiler panelin en altında olacaktır.

    ![Ekran görüntüsü, isteğin ayrıntılarını gösterir](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. Talep sahibinin verdiği bilgilere bağlı olarak, isteği onaylayabilir veya reddedebilirsiniz. Rehberlik için istekleri onaylama veya reddetme adımlarına bakın.

## <a name="approve-or-deny-request"></a>İsteği onayla veya Reddet

Onay bekleyen bir erişim isteği açtıktan sonra, bir onaylama veya reddetme kararı vermenize yardımcı olacak ayrıntıları görebilirsiniz.

**Önkoşul rolü:** Kişi

1. Erişim isteği bölmesini açmak için **Görünüm** bağlantısına tıklayın.

1. Erişim isteğiyle ilgili ayrıntıları görmek için **Ayrıntılar** ' a tıklayın.

    Ayrıntılar arasında kullanıcının adı, kuruluş, erişim başlangıç ve bitiş tarihi, iş gerekçe, isteğin gönderildiği zaman ve isteğin ne zaman dolacağı bulunur.

1. **Onayla** veya **Reddet**' e tıklayın.

1. Gerekirse bir neden girin.

    ![Ekran görüntüsünde, isteği kabul ettiğiniz veya reddetmeniz gereken sayfa gösterilir.](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Kararınızı göndermek için **Gönder** ' e tıklayın.

    Bir ilke birden çok onaylayıcı ile yapılandırıldıysa, yalnızca bir onaylayanın bekleyen onay ile ilgili bir karar vermesini sağlaması gerekir. Bir onaylayan, erişim isteğine kararını gönderdikten sonra, istek tamamlanır ve diğer onaylayanların isteği onaylaması veya reddetmesi için artık kullanılamaz. Diğer onaylayanlar, erişim Portalı ' nda istek kararlarını ve karar Oluşturucu 'yu görebilir. Şu anda yalnızca tek aşamalı onay desteklenir.

    Yapılandırılmış onaylayanlardan hiçbiri erişim isteğini onaylayabilir veya reddedeiyorsa, istek süresi yapılandırılan istek süresinden sonra dolar. Kullanıcı, erişim isteğinin bittiğini ve erişim isteğini yeniden gönderebilmeleri gerektiğini bildirdi.

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim paketine erişim isteme](entitlement-management-request-access.md)
- [İşlem ve e-posta bildirimleri iste](entitlement-management-process.md)
