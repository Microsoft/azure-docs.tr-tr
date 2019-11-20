---
title: Azure AD Yetkilendirme Yönetimi 'nde paket yöneticilerine erişim yönetimi temsilcisi-Azure Active Directory
description: BT yöneticilerinde erişim yönetimi yetkisini, erişimi yönetebilmeleri için paket yöneticilerine ve proje yöneticilerine erişme hakkında bilgi edinin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174362"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde paket yöneticilerine erişim yönetimine temsilci seçme

Bir katalogdaki erişim paketlerinin oluşturulmasını ve yönetimini devretmek için, erişim paketi Yöneticisi rolüne kullanıcılar eklersiniz. Erişim paketi yöneticileri, kullanıcıların bir katalogdaki kaynaklara erişim istemesi gereksinimini öğrenmelidir. Örneğin, bir proje için bir katalog kullanılıyorsa, proje lideri bu katalog için bir erişim paketi yöneticisi olabilir.  Erişim paketi yöneticileri bir kataloğa kaynak ekleyemez, ancak erişim paketlerini ve ilkelerini bir katalogda yönetebilir.  Bir erişim paketi yöneticisine temsilci seçerken, bu kişi bundan sonra sorumlu olabilir:

- Bir kullanıcının bir katalogdaki kaynaklara sahip olacağı roller
- Erişime ihtiyacı olacak
- Erişim isteklerini onaylaması gereken
- Projenin en son ne kadar süre

Bu videoda, Katalog sahibinden, paket yöneticisine erişim için erişim yönetimini devretme konusunda genel bir bakış sunulmaktadır.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Bir katalog sahibi olarak, bir erişim paketi yöneticisine temsilci olarak

Erişim paketi Yöneticisi rolüne bir kullanıcı atamak için aşağıdaki adımları izleyin:

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi veya katalog sahibi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol taraftaki menüden **kataloglar** ' a tıklayın ve ardından Yöneticiler eklemek istediğiniz kataloğu açın.

1. Sol taraftaki menüden **Roller ve yöneticiler**' e tıklayın.

    ![Rolleri ve yöneticileri kataloglandırır](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Bu rollerin üyelerini seçmek için **erişim paketi yöneticileri Ekle** ' ye tıklayın.

1. Bu üyeleri eklemek için **Seç** ' e tıklayın.

## <a name="remove-an-access-package-manager"></a>Erişim paketi yöneticisini kaldırma

Bir kullanıcıyı erişim paketi Yöneticisi rolünden kaldırmak için aşağıdaki adımları izleyin:

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi veya katalog sahibi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol taraftaki menüden **kataloglar** ' a tıklayın ve ardından Yöneticiler eklemek istediğiniz kataloğu açın.

1. Sol taraftaki menüden **Roller ve yöneticiler**' e tıklayın.

1. Kaldırmak istediğiniz bir erişim paketi yöneticisinin yanına onay işareti ekleyin.

1. **Kaldır**' a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Yeni bir erişim paketi oluşturun](entitlement-management-access-package-create.md)
