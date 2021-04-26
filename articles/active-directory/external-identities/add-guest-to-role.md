---
title: Role bir B2B işbirliği kullanıcısı ekleme-Azure Active Directory
description: Azure Active Directory bir role Konuk Kullanıcı ekleme
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32a931fe43b6be406f0b2a4b8193c1631261f7e5
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575675"
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Azure Active Directory kiracınızdaki iş ortağı kuruluşlarından kullanıcılara izin verme

Azure Active Directory (Azure AD) B2B işbirliği kullanıcıları, dizine Konuk Kullanıcı olarak eklenir ve dizindeki Konuk izinleri varsayılan olarak kısıtlıdır. İşletmeniz, bazı konuk kullanıcıların kuruluşunuzdaki yüksek ayrıcalıklı rolleri doldurmasına gerek duyar. Daha yüksek ayrıcalıklı rolleri tanımlamayı desteklemek için, Konuk kullanıcılar kuruluşunuzun ihtiyaçlarına bağlı olarak istediğiniz rollere eklenebilir.

Bir dizin rolü bir konuk kullanıcıya atanırsa, Konuk Kullanıcı, temel okuma izinleri dahil olmak üzere rolle birlikte gelen ek izinlerle verilir. Bkz. [Azure AD yerleşik rolleri](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference).

## <a name="default-role"></a>Varsayılan rol

![Varsayılan dizin rolünü gösteren ekran görüntüsü](./media/add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Genel yönetici rolü

![Genel yönetici rolünü gösteren ekran görüntüsü](./media/add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Sınırlı yönetici rolü

![Sınırlı yönetici rolünü gösteren ekran görüntüsü](./media/add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
- [B2B işbirliği kullanıcı özellikleri](user-properties.md)
