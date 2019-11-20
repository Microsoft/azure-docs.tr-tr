---
title: Outlook.com-Azure Logic Apps Bağlan | Microsoft Docs
description: Outlook.com REST API 'Leri ile e-posta, takvim ve kişileri yönetme ve Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 4586255e96647267bc913f2bc054610163e16bd3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050887"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Azure Logic Apps ile e-posta, takvim ve kişileri Outlook.com içinde yönetme

Bu makalede, Outlook.com hesabınızı Box Bağlayıcısı ile bir mantıksal uygulama içinde nasıl oluşturabileceğiniz ve yönetebileceğiniz gösterilmektedir. Bu şekilde, Outlook.com hesabınız için görevleri ve iş akışlarını otomatikleştiren mantıksal uygulamalar oluşturabilirsiniz, örneğin:

* E-posta gönderin. 
* Toplantıları zamanlayın.
* Kişi ekle. 

Logic Apps 'e yeni başladıysanız [Azure Logic Apps ne olduğunu](../logic-apps/logic-apps-overview.md)gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Bir [Outlook.com hesabı](https://outlook.live.com/owa/)

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* Outlook.com hesabınıza erişmek istediğiniz mantıksal uygulama. Mantıksal uygulamanızı bir Outlook tetikleyicisiyle başlatmak için [boş bir mantıksal uygulama](../logic-apps/quickstart-create-first-logic-app-workflow.md)gerekir. 

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)hakkında temel bilgi.

## <a name="connect-to-outlookcom"></a>Outlook.com 'e bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Swagger dosyasında açıklandığı şekilde Tetikleyiciler, Eylemler ve sınırlar gibi teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/outlook/)bakın. 

## <a name="get-support"></a>Destek alın

* Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin