---
title: GitHub deponuzu erişin, izleyin ve yönetin
description: Azure Logic Apps ile otomatik iş akışları oluşturarak GitHub olaylarını izleyin ve GitHub deponuzu yönetin
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999561"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak GitHub deponuzu izleyin ve yönetin

GitHub, git ve diğer özelliklerde tüm dağıtılmış düzeltme denetimi ve kaynak kodu yönetimi (SCM) işlevlerini sunan Web tabanlı bir git deposu barındırma hizmetidir.

GitHub bağlayıcısını kullanmaya başlamak için [önce bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>GitHub bağlantısı oluşturma

Bir mantıksal uygulamada GitHub bağlayıcısını kullanmak için, önce bir *bağlantı* oluşturmanız ve ardından bu özelliklerin ayrıntılarını sağlamanız gerekir: 

| Özellik | Gerekli | Açıklama | 
| -------- | -------- | ----------- | 
| Belirteç | Yes | GitHub kimlik bilgilerinizi sağlayın. |

Bağlantıyı oluşturduktan sonra, eylemleri yürütebilir ve bu makalede açıklanan Tetikleyicileri dinleyebilirsiniz.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için [bağlayıcının başvuru sayfasını](/connectors/github/)gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin