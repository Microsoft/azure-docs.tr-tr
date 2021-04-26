---
title: Microsoft Graph API
description: Microsoft Graph API 'SI, Microsoft Bulut hizmet kaynaklarına erişmenize olanak tanıyan bir yeniden takip eden Web API 'sidir.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 2e689e620a5aeb7c5028f1a1b30dd6def8e447ab
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529990"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API 'SI, Microsoft Bulut hizmet kaynaklarına erişmenize olanak tanıyan bir yeniden takip eden Web API 'sidir. Uygulamanızı kaydettikten ve bir kullanıcı veya hizmet için kimlik doğrulama belirteçleri aldıktan sonra, Microsoft Graph API 'sine istek yapabilirsiniz. Daha fazla bilgi için bkz. [Microsoft Graph genel bakış](/graph/overview).

Microsoft Graph, aşağıdaki Microsoft 365 hizmetlerindeki verilere erişmek için REST API 'Leri ve istemci kitaplıklarını kullanıma sunar:
- Microsoft 365 Services: Delve, Excel, Microsoft kayıtları, Microsoft ekipleri, OneDrive, OneNote, Outlook/Exchange, Planner ve SharePoint
- Enterprise Mobility and Security Services: Gelişmiş tehdit analizi, Gelişmiş tehdit koruması, Azure Active Directory, Identity Manager ve Intune
- Windows 10 Hizmetleri: Etkinlikler, cihazlar, bildirimler
- Dynamics 365 Business Central

## <a name="versions"></a>Sürümler

Microsoft Graph Şu anda iki sürümü desteklemektedir: v 1.0 ve Beta. V 1.0 sürümü, genel olarak kullanılabilir API 'Leri içerir. Tüm üretim uygulamaları için v 1.0 sürümünü kullanın. Beta, şu anda önizleme aşamasında olan API 'Leri içerir. Beta API 'lerimiz üzerinde önemli değişiklikler sunabileceğimizden, yalnızca geliştirme aşamasında olan uygulamaları test etmek için beta sürümünü kullanmanızı öneririz. üretim uygulamalarınızda Beta API 'Lerini kullanmayın. Daha fazla bilgi için, bkz. [Microsoft Graph Için sürüm oluşturma, destek ve bölme değiştirme ilkeleri](/graph/versioning-and-support).

Beta API 'Lerini kullanmaya başlamak için bkz. [Microsoft Graph Beta uç noktası başvurusu](/graph/api/overview?view=graph-rest-beta&preserve-view=true)

V 1.0 API 'Lerini kullanmaya başlamak için bkz. [Microsoft Graph REST API v 1.0 başvurusu](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)

## <a name="get-started"></a>başlarken

Kullanıcı veya e-posta iletisi gibi bir kaynağı okumak veya bir kaynağa yazmak için aşağıdaki gibi görünen bir istek oluşturursunuz:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Oluşturulan isteğin öğeleri hakkında daha fazla bilgi için bkz [. MICROSOFT Graph API 'Sini kullanma](/graph/use-the-api)

Hızlı başlangıç örnekleri, Microsoft Graph API 'sinin gücüne nasıl erişekullanacağınızı göstermek için kullanılabilir. Kullanılabilir örnekler, bir kimlik doğrulaması ile iki hizmete erişir: Microsoft hesabı ve Outlook. Her hızlı başlangıç, Microsoft hesabı kullanıcıların profillerinin bilgilerine erişir ve takviminden olayları görüntüler.
Hızlı başlangıçlarda dört adım vardır:
- Platformunuzu seçin
- Uygulama KIMLIĞINIZI (istemci KIMLIĞI) alın
- Örneği oluşturma
- Oturum açın ve takviminizde olayları görüntüleyin

Hızlı başlangıcı tamamladığınızda, çalıştırmaya hazırlamış bir uygulamanız vardır. Daha fazla bilgi için [Microsoft Graph hızlı başlangıç hakkında SSS](/graph/quick-start-faq)bölümüne bakın. Örnekleri kullanmaya başlamak için bkz. [Microsoft Graph hızlı başlangıç](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Araçlar

Microsoft Graph Explorer, Microsoft Graph API 'Leri kullanarak istek derlemek ve test etmek için kullanabileceğiniz web tabanlı bir araçtır. Microsoft Graph Explorer 'a şu adreste erişebilirsiniz: `https://developer.microsoft.com/graph/graph-explorer` .

Postman, Microsoft Graph API 'Lerini kullanarak istek derlemek ve test etmek için de kullanabileceğiniz bir araçtır. Postman 'yi şurada indirebilirsiniz: `https://www.getpostman.com/` . Postman 'da Microsoft Graph etkileşimde bulunmak için Postman 'da Microsoft Graph koleksiyonunu kullanırsınız. Daha fazla bilgi için bkz. [MICROSOFT Graph API Ile Postman kullanma](/graph/use-postman).
