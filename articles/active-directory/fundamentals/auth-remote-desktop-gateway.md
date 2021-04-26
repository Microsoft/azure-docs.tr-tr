---
title: Azure Active Directory sahip Hizmetleri Uzak Masaüstü Ağ Geçidi
description: Azure Active Directory ile Uzak Masaüstü Ağ Geçidi hizmetleri elde etmeye yönelik mimari rehberlik.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4baaf2de6fbe4a56f64d449644b8594217dc432c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172746"
---
# <a name="remote-desktop-gateway-services"></a>Uzak Masaüstü Ağ Geçidi Hizmetleri

Standart Uzak Masaüstü Hizmetleri (RDS) dağıtımı, Windows Server üzerinde çalışan çeşitli [Uzak Masaüstü rolü hizmetlerini](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) içerir. Azure Active Directory (Azure AD) uygulama proxy 'Si ile RDS dağıtımı, bağlayıcı hizmetini çalıştıran sunucudan kalıcı bir giden bağlantıya sahiptir. Diğer dağıtımlar, Açık gelen bağlantıları bir yük dengeleyici aracılığıyla bırakır. Bu kimlik doğrulama stili, Uzak Masaüstü Hizmetleri aracılığıyla şirket içi uygulamaları yayımlayarak daha fazla tür uygulama sunmanıza olanak tanır. Ayrıca, Azure AD Uygulama Ara Sunucusu kullanarak dağıtımının saldırı yüzeyini de azaltır.

## <a name="use-when"></a>Şu durumlarda kullanın

Uzaktan erişim sağlamanız ve Uzak Masaüstü Hizmetleri dağıtımınızı ön kimlik doğrulamasıyla korumanız gerekir.

![mimari diyagram](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>Sistem bileşenleri

* **Kullanıcı**: uygulama proxy 'si tarafından sunulan RDS 'ye erişir.

* **Web tarayıcısı**: kullanıcının uygulamanın dış URL 'sine erişmek için etkileşimde bulunduğu bileşen.

* **Azure AD**: kullanıcının kimliğini doğrular. 

* **Uygulama proxy hizmeti**: ISTEğI kullanıcıdan RDS 'ye iletmek için ters proxy işlevi görür. Uygulama proxy 'Si, koşullu erişim ilkelerini de uygulayabilir. 

* **Uzak Masaüstü Hizmetleri**: bağımsız sanallaştırılmış uygulamalar için bir platform görevi görür, güvenli mobil ve uzak masaüstü erişimi sağlar ve son kullanıcılara, uygulamalarını ve masaüstlerini buluttan çalıştırma yeteneği sağlar. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Azure AD ile Uzak Masaüstü Ağ Geçidi Hizmetleri uygulama

* [Azure AD Uygulama Ara Sunucusu uzak masaüstü 'nü yayımlama](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md) 

* [Azure AD 'de uygulama proxy 'Si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](../manage-apps/application-proxy-add-on-premises-application.md)

