---
title: Azure Active Directory Reporting API 'de hata giderme | Microsoft Docs
description: Azure Active Directory Raporlama API 'Lerini çağırırken hata çözümü sağlar.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 445514297550210d80cd50895201d1129fac4f20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533719"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Azure Active Directory Raporlama API 'sindeki hataların sorunlarını giderme

Bu makalede, Microsoft Graph API 'sini ve bunların çözümlenme adımlarını kullanarak etkinlik raporlarına erişirken karşılaşabileceğiniz ortak hata iletileri listelenmektedir.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Microsoft Graph v2 uç noktasına erişirken HTTP iç sunucu hatası 500

Şu anda Microsoft Graph v2 uç noktasını desteklemiyoruz-Microsoft Graph v1 uç noktasını kullanarak etkinlik günlüklerine erişebildiğinizden emin olun.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Hata: hiçbir kiracı B2C değil veya kiracıda Premium lisans yok

Oturum açma raporlarına erişmek için Azure Active Directory Premium 1 (P1) lisansı gerekir. Oturum açma işlemleri sırasında bu hata iletisini görürseniz, kiracınızın bir Azure AD P1 lisansıyla lisanslanmasını sağlayın.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Hata: Kullanıcı izin verilen rollerde değil 

API kullanarak denetim günlüklerine veya oturum açma erişimine erişmeye çalışırken bu hata iletisini görürseniz, hesabınızın Azure Active Directory kiracınızdaki **güvenlik okuyucusu** veya **rapor okuyucu** rolünün bir parçası olduğundan emin olun. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hata: uygulamada AAD ' dizin verilerini oku ' izni eksik 

Lütfen uygulamanızın doğru izin kümesiyle çalıştığından emin olmak için [Azure Active Directory Raporlama API 'sine erişmek üzere ön koşullar](howto-configure-prerequisites-for-reporting-api.md) bölümündeki adımları izleyin. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Hata: uygulamada Microsoft Graph API ' tüm denetim günlüğü verilerini oku ' izni eksik

Lütfen uygulamanızın doğru izin kümesiyle çalıştığından emin olmak için [Azure Active Directory Raporlama API 'sine erişmek üzere ön koşullar](howto-configure-prerequisites-for-reporting-api.md) bölümündeki adımları izleyin. 

## <a name="next-steps"></a>Sonraki Adımlar

[Denetim API 'si başvurusunu kullanma](/graph/api/resources/directoryaudit) 
 [Oturum açma Etkinliği raporu API başvurusunu kullanın](/graph/api/resources/signin)