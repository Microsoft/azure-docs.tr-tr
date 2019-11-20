---
title: Azure AD Yetkilendirme Yönetimi 'nde bir erişim paketine erişim isteme-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetimi 'nde bir erişim paketine erişim istemek için erişim portalını nasıl kullanacağınızı öğrenin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddc0a3788075701fb4633895e7b22fff2c15f60b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173691"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde bir erişim paketine erişim isteme

Azure AD yetkilendirme yönetimi sayesinde bir erişim paketi, erişim paketinin ömrü boyunca erişimi otomatik olarak yöneten kaynak ve ilkelerin bir kerelik kurulumunu mümkün bir şekilde sunar. 

Erişim paketi Yöneticisi, kullanıcıların paketlere erişim sahibi olması için ilkeleri bir onay gerektirecek şekilde yapılandırabilir. Erişim paketine erişmesi gereken bir Kullanıcı, erişim sağlamak için bir istek gönderebilir. Bu makalede bir erişim isteğinin nasıl gönderileceği açıklanır.

## <a name="sign-in-to-the-my-access-portal"></a>Erişim Portalı 'nda oturum açın

İlk adım, erişim paketine erişim isteyebileceğiniz erişim portalı 'nda oturum açmak için kullanılır.

**Önkoşul rolü:** Sahibinin

1. Çalıştığınız proje veya iş Yöneticisi için bir e-posta veya bir ileti arayın. E-posta, erişmeniz gereken erişim paketine bir bağlantı içermelidir. Bağlantı `myaccess` ile başlar, bir dizin ipucu içerir ve bir erişim paketi KIMLIĞIYLE biter.
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Bağlantıyı açın.

1. Erişim Portalı ' nda oturum açın.

    Kuruluş (iş veya okul) hesabınızı kullandığınızdan emin olun. Emin değilseniz, projeniz veya Business Manager ile görüşün.

## <a name="request-an-access-package"></a>Erişim paketi isteme

Access Portal 'da erişim paketini bulduktan sonra bir istek gönderebilirsiniz.

**Önkoşul rolü:** Sahibinin

1. Listede erişim paketini bulun.  Gerekirse, arama dizesi yazarak ve ardından **ad**, **Katalog**veya **kaynaklar** filtresini seçerek arama yapabilirsiniz.

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
