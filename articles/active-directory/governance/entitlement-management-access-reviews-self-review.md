---
title: Azure AD Yetkilendirme Yönetimi 'nde bir erişim paketini kendi kendine gözden geçirme
description: Azure Active Directory erişim gözden geçirmeleri (Önizleme) içinde, Yetkilendirme Yönetimi erişim paketlerine yönelik Kullanıcı erişimini gözden geçirmeyi öğrenin.
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
ms.openlocfilehash: 89a346f48856ea33ae5e27e3537f53dfa1ca3ae3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608819"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde bir erişim paketini kendi kendine gözden geçirme

Azure AD Yetkilendirme Yönetimi, kuruluşların gruplara, uygulamalara ve SharePoint sitelerine erişimi nasıl yöneteceğini basitleştirir. Bu makalede, bir kullanıcının kendilerine atanan erişim paketleri için nasıl kendi kendini gözden geçirme yöntemi açıklanır.

## <a name="open-the-access-review"></a>Erişim gözden geçirmesini açın

Erişim gözden geçirmesi yapmak için, önce erişim gözden geçirmeyi açmanız gerekir. Erişim gözden geçirmesini bulmak ve açmak için aşağıdaki yordamı kullanın:

1. Microsoft 'tan erişimi incelemenizi isteyen bir e-posta alabilirsiniz. Erişim gözden geçirmesini açmak için e-postayı bulun. Erişim gözden geçirmesi isteyen bir e-posta örneği aşağıda verilmiştir: 
    
    ![Erişim gözden geçiren kendi e-postası](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. **Erişimi gözden geçir** bağlantısına tıklayın.

1. Ayrıca, e-posta almazsanız bekleyen erişim incelemelerinizi bulmak için https://myaccess.microsoft.com doğrudan da gidebilirsiniz.

1. Size atanan bekleyen erişim incelemelerinin listesini görmek için sol gezinti çubuğundaki **erişim İncelemeleri** ' ne tıklayın.


1.  Başlamak istediğiniz gözden geçirmeyi tıklatın.

## <a name="perform-the-access-review"></a>Erişim gözden geçirmesini gerçekleştir

Erişim gözden geçirmesini açtığınızda erişiminizi görebilirsiniz. Erişim gözden geçirmesini yapmak için aşağıdaki yordamı kullanın:

1.  Erişim paketine erişmeye hala ihtiyacınız olup olmadığına karar verin. Örneğin, üzerinde çalıştığınız proje tamamlanmamış olduğundan proje üzerinde çalışmaya devam etmek için hala erişmeniz gerekir.

1.  Erişiminizi korumak için **Evet** ' e tıklayın veya erişiminizi kaldırmak için **Hayır** ' a tıklayın.
    >[!NOTE]
    >Artık erişime ihtiyaç duymadığını belirtirseniz, erişim paketinden hemen kaldırılmamıştır. İnceleme sona erdiğinde veya bir yönetici incelemeyi durdurduğu zaman erişim paketinden kaldırılır.

1.  **Evet**' e tıkladıysanız, **neden** kutusuna bir gerekçe açıklaması eklemeniz gerekebilir.

1.  **Gönder**' e tıklayın.

Fikrinizi değiştirirseniz ve gözden geçirmeyi sonlandırmadan önce yanıtınızı değiştirmeye karar verirseniz incelemeye geri dönebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim paketlerine erişimi gözden geçirme](entitlement-management-access-reviews-review-access.md) 
