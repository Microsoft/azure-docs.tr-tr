---
title: Erişim paketi isteme-Azure AD Yetkilendirme Yönetimi
description: Azure Active Directory yetkilendirme yönetimi 'nde bir erişim paketine erişim istemek için erişim portalını nasıl kullanacağınızı öğrenin.
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
ms.openlocfilehash: 726507fa6ea0651b23d46424bda669a2d8ad41e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996653"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde bir erişim paketine erişim isteme

Azure AD yetkilendirme yönetimi sayesinde bir erişim paketi, erişim paketinin ömrü boyunca erişimi otomatik olarak yöneten kaynak ve ilkelerin bir kerelik kurulumunu mümkün bir şekilde sunar. 

Erişim paketi Yöneticisi, kullanıcıların paketlere erişim sahibi olması için ilkeleri bir onay gerektirecek şekilde yapılandırabilir. Erişim paketine erişmesi gereken bir Kullanıcı, erişim sağlamak için bir istek gönderebilir. Bu makalede bir erişim isteğinin nasıl gönderileceği açıklanır.

## <a name="sign-in-to-the-my-access-portal"></a>Erişim Portalı 'nda oturum açın

İlk adım, erişim paketine erişim isteyebileceğiniz erişim portalı 'nda oturum açmak için kullanılır.

**Önkoşul rolü:** Sahibinin

1. Çalıştığınız proje veya iş Yöneticisi için bir e-posta veya bir ileti arayın. E-posta, erişmeniz gereken erişim paketine bir bağlantı içermelidir. Bağlantı, ile başlar `myaccess` , bir dizin ipucu içerir ve bir erişim PAKETI kimliğiyle biter.  (ABD kamu için etki alanı `https://myaccess.microsoft.us` bunun yerine olabilir.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Bağlantıyı açın.

1. Erişim Portalı ' nda oturum açın.

    Kuruluş (iş veya okul) hesabınızı kullandığınızdan emin olun. Emin değilseniz, projeniz veya Business Manager ile görüşün.

## <a name="request-an-access-package"></a>Erişim paketi isteme

Access Portal 'da erişim paketini bulduktan sonra bir istek gönderebilirsiniz.

**Önkoşul rolü:** Sahibinin

1. Listede erişim paketini bulun.  Gerekirse, arama dizesi yazarak ve ardından **ad**, **Katalog** veya **kaynaklar** filtresini seçerek arama yapabilirsiniz.

    ![Erişim Portalı-kaynak arama](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Erişim paketini seçmek için onay işaretine tıklayın.

1. Istek erişimi bölmesini açmak için **erişim iste** ' ye tıklayın.

    ![Erişim Portalı-erişim paketlerim](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. **İş gerekçe** kutusu görüntüleniyorsa, erişim gereksinimi için bir gerekçe yazın.

1. **Belirli bir döneme yönelik istek** etkinleştirilirse, **Evet** veya **Hayır**' ı seçin.

1. Gerekirse, başlangıç tarihini ve bitiş tarihini belirtin.

    ![Erişim Portalı-erişim ISTEME](./media/entitlement-management-shared/my-access-request-access.png)

1. İşiniz bittiğinde, isteğinizi göndermek için **Gönder** ' e tıklayın.

1. İsteklerinizin ve durumun listesini görmek için **istek geçmişi** ' ne tıklayın.

    Erişim paketi onay gerektiriyorsa, istek artık bekleyen bir onay durumundadır.

### <a name="select-a-policy"></a>İlke seçme

Uygulanan birden çok ilkeye sahip bir erişim paketine erişim isteğinde karşılaşırsanız, bir ilke seçmeniz istenebilir. Örneğin, bir erişim paketi Yöneticisi iki dahili çalışan grubu için iki ilkeye sahip bir erişim paketi yapılandırabilir. İlk ilke 60 gün boyunca erişime izin verebilir ve onay gerektirebilir. İkinci ilke 2 gün boyunca erişime izin verebilir ve onay gerektirmez. Bu senaryoyla karşılaşırsanız, kullanmak istediğiniz ilkeyi seçmeniz gerekir.

![Erişim Portalı-erişim ıste-birden çok ilke](./media/entitlement-management-request-access/my-access-multiple-policies.png)

### <a name="fill-out-requestor-information"></a>İstek sahibi bilgilerini doldur

Erişim paketine erişim vermeden önce iş gerekçesini ve ek istek sahibi bilgilerini gerektiren bir erişim paketine erişim isteyebilirsiniz. Erişim paketine erişmek için gereken tüm istek sahibi bilgilerini doldurun.

![Erişim Portalı-erişim ISTEME-istek sahibi bilgilerini doldur](./media/entitlement-management-request-access/my-access-requestor-information.png)

## <a name="resubmit-a-request"></a>İsteği yeniden gönder

Bir erişim paketine erişim istediğinizde isteğiniz reddedilebilir veya onaylayanlar zaman yanıt vermezse isteğiniz zaman alabilir. Erişmeniz gerekiyorsa, tekrar deneyebilir ve isteğinizi yeniden gönderebilirsiniz. Aşağıdaki yordamda bir erişim isteğinin nasıl yeniden başlatılacağını açıklanmaktadır:

**Önkoşul rolü:** Sahibinin

1. **Erişim** Portalı ' nda oturum açın.

1. Gezinti menüsünden sol taraftaki **istek geçmişi** ' ne tıklayın.

1. Bir isteği yeniden dağıttığınız erişim paketini bulun.

1. Erişim paketini seçmek için onay işaretine tıklayın.

1. Seçili erişim paketinin sağ tarafındaki mavi **Görünüm** bağlantısına tıklayın.
    
    ![Erişim paketi ve görünüm bağlantısını seçin](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Bir bölme, erişim paketinin istek geçmişiyle sağ tarafta açılır.
    
    ![Yeniden Gönder düğmesini seçin](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Bölmenin alt kısmındaki yeniden **Gönder** düğmesine tıklayın.

## <a name="cancel-a-request"></a>İsteği iptal et

Bir erişim isteği gönderirseniz ve istek hala **Bekleyen onay** durumundaysa, isteği iptal edebilirsiniz.

**Önkoşul rolü:** Sahibinin

1. İsteklerinizin ve durumun listesini görmek için, sol taraftaki erişim Portalı ' nda, **istek geçmişi** ' ne tıklayın.

1. İptal etmek istediğiniz isteğin **Görünüm** bağlantısına tıklayın.

1. İstek hala **Bekleyen onay** durumundaysa, isteği iptal etmek Için Isteği **iptal et** ' e tıklayabilirsiniz.

    ![Erişim Portalı-isteği Iptal et](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. İsteğin iptal edildiğini onaylamak için **istek geçmişi** ' ne tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim isteklerini onaylama veya reddetme](entitlement-management-request-approve.md)
- [İşlem ve e-posta bildirimleri iste](entitlement-management-process.md)
