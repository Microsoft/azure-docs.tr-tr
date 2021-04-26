---
title: Azure Active Directory uygulamalar için OıDC tabanlı çoklu oturum açma (SSO) anlayın
description: Azure Active Directory uygulamalar için OıDC tabanlı çoklu oturum açma (SSO) anlayın.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: iangithinji
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: 990e0c09f8a49b83bc68d7123f5f8146a3551575
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374548"
---
# <a name="understand-oidc-based-single-sign-on"></a>OıDC tabanlı çoklu oturum açmayı anlama
Uygulama yönetiminde [hızlı başlangıç serisinde](view-applications-portal.md) , Azure AD 'yi bir uygulama Için kimlik sağlayıcısı (IDP) olarak kullanmayı öğrendiniz. Bu makale, OpenID Connect standardını kullanan uygulamalar hakkında çoklu oturum açma uygulamak için daha fazla ayrıntıya gider. 

## <a name="before-you-begin"></a>Başlamadan önce
Azure Active Directory kiracınıza uygulama ekleme işlemi, uygulamanın uygulandığı çoklu oturum açma türüne bağlıdır. Kimlik yönetimi için Azure AD kullanan uygulamalar için kullanılabilen çoklu oturum açma seçenekleri hakkında daha fazla bilgi edinmek için bkz. [Çoklu oturum açma seçenekleri](sso-options.md). Bu makalede, OıDC tabanlı uygulamalar ele alınmaktadır.


## <a name="basic-oidc-configuration"></a>Temel OıDC yapılandırması
[Hızlı başlangıç serisinde](add-application-portal-setup-oidc-sso.md), çoklu oturum açmayı yapılandırma hakkında bir makale vardır. Burada, Azure kiracınıza OıDC tabanlı bir uygulama eklemeyi öğreneceksiniz.

Çoklu oturum açma için OıDC standardını kullanan bir uygulama eklemenin en iyi şeyi, yapılandırmanın en düşük düzeyde olmasıdır. Kiracınıza OıDC tabanlı bir uygulamanın nasıl ekleneceğini gösteren kısa bir video aşağıda verilmiştir.

Azure Active Directory bir OıDC tabanlı uygulama ekleme

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Kullanıcı ve yönetici onayı hakkında daha fazla bilgi edinmek için bkz. [Kullanıcı ve yönetici onayını anlama](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama yönetiminde hızlı başlangıç serisi](add-application-portal-setup-oidc-sso.md)
- [Çoklu oturum açma seçenekleri](sso-options.md)
- [Nasıl yapılır: Çok kiracılı uygulama desenini kullanarak istediğiniz bir Azure Active Directory kullanıcısıyla oturum açma](../develop/howto-convert-app-to-be-multi-tenant.md)
