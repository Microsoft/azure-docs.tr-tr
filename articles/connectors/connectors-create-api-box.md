---
title: Box-Azure Logic Apps Bağlan
description: Box REST API 'Leri ve Azure Logic Apps dosya oluşturun ve yönetin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 5fbc64194182c41a70eb27a4049234973c39fffe
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050934"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Azure Logic Apps ile kutu içinde dosya oluşturma ve yönetme

Bu makalede, Box Bağlayıcısı ile bir mantıksal uygulamanın içinden dosyaları nasıl oluşturabileceğiniz ve yönetebileceğiniz gösterilmektedir. Bu şekilde, dosyalarınızı ve diğer eylemlerinizi yönetmek için görevleri ve iş akışlarını otomatikleştirebilen mantıksal uygulamalar oluşturabilirsiniz, örneğin:

* İş akışınızı, aldığınız verilere göre oluşturun.

* Bir dosya oluşturulduğunda veya güncelleştirilirken otomatikleştirilmiş görevleri ve iş akışını tetikler.

* Bir dosyayı kopyalayan veya dosyayı silen bir eylem çalıştırın.

  Bu eylemler bir yanıt edindiklerinde, çıktıyı diğer eylemler için kullanılabilir hale getirir. 
  Örneğin, Box 'ta bir dosya değiştirildiğinde, Office 365 kullanarak bu dosyayı e-posta ile gönderebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* [Kutu hesabı](https://www.box.com/home)

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* Box hesabınıza erişmek istediğiniz mantıksal uygulama. Mantıksal uygulamanızı bir kutu tetikleyicisi ile başlatmak için [boş bir mantıksal uygulama](../logic-apps/quickstart-create-first-logic-app-workflow.md)gerekir.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)hakkında temel bilgi.
Logic Apps 'e yeni başladıysanız [Azure Logic Apps ne olduğunu](../logic-apps/logic-apps-overview.md)gözden geçirin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) dosyasında açıklandığı gibi Tetikleyiciler, Eylemler ve sınırlar gibi teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/box/)bakın.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin