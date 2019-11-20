---
title: Azure AD Yetkilendirme Yönetimi 'nde erişim paketi istemek için bağlantı paylaşma-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetimi 'nde erişim paketi istemek için bağlantı paylaşmayı öğrenin.
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
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94d32bf26def6832f51b4e09c697bca885162dc2
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174409"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde erişim paketi istemek için bağlantıyı paylaşma

Dizininizdeki çoğu kullanıcı erişim portalı 'nda oturum açabilir ve istedikleri erişim paketleri listesini otomatik olarak görebilir. Ancak, henüz dizininizden olmayan dış iş ortağı kullanıcıları için, onlara bir erişim paketi istemek üzere kullanabilecekleri bir bağlantı göndermeniz gerekecektir. 

Erişim paketinin kataloğu [dış kullanıcılar için etkinleştirildiğinden](entitlement-management-catalog-create.md) ve [Dış kullanıcının dizini için bir ilkeniz](entitlement-management-access-package-request-policy.md)varsa, dış Kullanıcı erişim paketi istemek için erişim portalı bağlantısını kullanabilir.

## <a name="share-link-to-request-an-access-package"></a>Erişim paketi istemek için bağlantıyı paylaşma

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. Genel Bakış sayfasında, **erişim portalı My bağlantısını**kopyalayın.

    ![Erişim paketine genel bakış-erişim portalı bağlantısı](./media/entitlement-management-shared/my-access-portal-link.png)

    Bir iç iş ortağına gönderirken erişim portalı 'nın tamamını kopyalamanız önemlidir. Bu, iş ortağının, kendi isteklerini yapmak için dizininizin portalına erişmesini sağlar. Bağlantı `myaccess` ile başlar, bir dizin ipucu içerir ve bir erişim paketi KIMLIĞIYLE biter.

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Dış iş ortağınıza e-posta gönderin veya bağlantıyı gönderin. Erişim paketini istemek için bağlantısını kullanıcılarla paylaşabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim paketine erişim isteme](entitlement-management-request-access.md)
- [Erişim isteklerini onaylama veya reddetme](entitlement-management-request-approve.md)