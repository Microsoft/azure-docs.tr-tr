---
title: Azure Active Directory Reporting API 'de hata giderme | Microsoft Docs
description: Azure Active Directory Raporlama API 'Lerini çağırırken hata çözümü sağlar.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f1f34dcece9acb20d0db091152b24b26cb9fa2
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989525"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Azure Active Directory Raporlama API 'sindeki hataların sorunlarını giderme

Bu makalede, MS Graph API kullanarak etkinlik raporlarına ve bunların çözünürlüğüne yönelik adımlara erişirken karşılaşabileceğiniz ortak hata iletileri listelenmektedir.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Microsoft Graph v2 uç noktasına erişirken HTTP iç sunucu hatası 500

Şu anda Microsoft Graph v2 uç noktasını desteklemiyoruz-Microsoft Graph v1 uç noktasını kullanarak etkinlik günlüklerine erişebildiğinizden emin olun.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Hata: AD grafiğinden Kullanıcı rolleri alınamadı

Bu hata iletisini, Graph Explorer kullanarak oturum açma erişimlerine erişmeye çalışırken alabilirsiniz. Aşağıdaki görüntüde gösterildiği gibi, Graph Explorer Kullanıcı arabirimindeki oturum açma düğmelerinden her ikisini kullanarak hesabınızda oturum açtığınızdan emin olun. 

![Graph Gezgini](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Hata: AD grafiğinden Premium lisans denetimi yapılamadı 

Graph Explorer kullanarak oturum açma erişimiyle çalışmaya çalışırken bu hata iletisiyle karşılaşırsanız, sol gezinti bölmesinde hesabınızın altındaki **Izinleri Değiştir** ' i seçin ve **Görevler. ReadWrite** ve **Dizin. Read. All**' ı seçin. 

![İzin Kullanıcı arabirimini değiştirme](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Hata: Hiçbir kiracı B2C değil veya kiracıda Premium lisansa sahip değil

Oturum açma raporlarına erişmek için Azure Active Directory Premium 1 (P1) lisansı gerekir. Oturum açma işlemleri sırasında bu hata iletisini görürseniz, kiracınızın bir Azure AD P1 lisansıyla lisanslanmasını sağlayın.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Hata: Kullanıcı izin verilen rollerde değil 

API kullanarak denetim günlüklerine veya oturum açma erişimine erişmeye çalışırken bu hata iletisini görürseniz, hesabınızın Azure Active Directory kiracınızdaki **güvenlik okuyucusu** veya **rapor okuyucu** rolünün bir parçası olduğundan emin olun. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hata: Uygulamada AAD ' Read Directory Data ' izni eksik 

Lütfen uygulamanızın doğru izin kümesiyle çalıştığından emin olmak için [Azure Active Directory Raporlama API 'sine erişmek üzere ön koşullar](howto-configure-prerequisites-for-reporting-api.md) bölümündeki adımları izleyin. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Hata: Uygulama eksik MSGraph API 'SI ' tüm denetim günlüğü verilerini oku ' izni

Lütfen uygulamanızın doğru izin kümesiyle çalıştığından emin olmak için [Azure Active Directory Raporlama API 'sine erişmek üzere ön koşullar](howto-configure-prerequisites-for-reporting-api.md) bölümündeki adımları izleyin. 

## <a name="next-steps"></a>Sonraki Adımlar

[Denetim API 'si başvurusunu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
kullanma[oturum açma etkinlik raporu API başvurusunu kullan](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
