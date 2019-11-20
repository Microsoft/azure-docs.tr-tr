---
title: Azure AD Galeri uygulamasına Kullanıcı hazırlama saati veya daha fazlasını sürüyor | Microsoft Docs
description: Uygulamanıza yönelik sağlamasının neden beklediğinizden uzun sürüyor olabileceğini öğrenin
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca2257a46815dd5e685b9cc746a64cede510d10a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034179"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Azure AD Galeri uygulamasına Kullanıcı hazırlama saati veya daha fazlasını sürüyor

İlk zaman bir uygulama için otomatik sağlamayı etkinleştirdiğinizde, Azure AD dizininin boyutuna ve sağlama kapsamındaki kullanıcı sayısına bağlı olarak, ilk zaman 20 dakikadan birkaç saate kadar sürebilir. 

İlk döngüden sonra sonraki eşitlemeler daha hızlı olur, çünkü sağlama hizmeti başlangıç döngüsünden sonra her iki sistemin durumunu temsil eden filigranları depolar, sonraki eşitlemeler performansını geliştirir.

## <a name="how-to-improve-provisioning-performance"></a>Sağlama performansını geliştirme

İlk döngüyü birkaç saatten fazla sürerse, performansı artırmak için yapabileceğiniz bir şey vardır:

-   **Kullanıcı kapsamı filtreleri.** Kapsam filtreleri, kullanıcıları belirli öznitelik değerlerine göre filtreleyerek, sağlama hizmetinin Azure AD 'den ayıklayan verileri ayarlamanıza olanak sağlar. Kapsam filtreleri hakkında daha fazla bilgi için bkz. [kapsam filtreleriyle öznitelik tabanlı uygulama sağlama](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Sonraki adımlar
[Azure Active Directory ile SaaS Uygulamalarına Kullanıcı Hazırlama ve Sağlamayı Kaldırma İşlemlerini Otomatik Hale Getirme](user-provisioning.md)

