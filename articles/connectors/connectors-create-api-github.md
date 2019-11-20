---
title: GitHub 'a bağlanma-Azure Logic Apps
description: GitHub REST API 'Leri ve Azure Logic Apps GitHub olaylarını izleme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 6a6e2a803ee2a272189abf0f21796b2305eea40b
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050897"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Azure Logic Apps GitHub 'a bağlanma

GitHub, git ve diğer özelliklerde tüm dağıtılmış düzeltme denetimi ve kaynak kodu yönetimi (SCM) işlevlerini sunan Web tabanlı bir git deposu barındırma hizmetidir.

GitHub bağlayıcısını kullanmaya başlamak için [önce bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>GitHub bağlantısı oluşturma

Bir mantıksal uygulamada GitHub bağlayıcısını kullanmak için, önce bir *bağlantı* oluşturmanız ve ardından bu özelliklerin ayrıntılarını sağlamanız gerekir: 

| Özellik | Gerekli | Açıklama | 
| -------- | -------- | ----------- | 
| Belirteç | Evet | GitHub kimlik bilgilerinizi sağlayın. |

Bağlantıyı oluşturduktan sonra, eylemleri yürütebilir ve bu makalede açıklanan Tetikleyicileri dinleyebilirsiniz.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için [bağlayıcının başvuru sayfasını](/connectors/github/)gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin