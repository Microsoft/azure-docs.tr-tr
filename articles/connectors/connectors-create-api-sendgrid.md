---
title: Azure Logic Apps 'tan SendGrid 'e Bağlan | Microsoft Docs
description: Azure Logic Apps kullanarak e-posta gönderen ve posta listelerini gönderen görev ve iş akışlarını otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 4f5efd73ef2a08069e3b9f2c7d60be99c209ddca
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050814"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak e-posta gönderme ve gönderme listesini SendGrid içinde yönetme

Azure Logic Apps ve SendGrid Bağlayıcısı sayesinde, e-posta gönderen ve alıcı listelerinizi yöneten otomatik görevler ve iş akışları oluşturabilirsiniz, örneğin:

* E-posta gönderin.
* Listelere alıcı ekleyin.
* Genel gizleme alın, ekleyin ve yönetin.

Bu görevleri gerçekleştirmek için, mantıksal uygulamalarınızda SendGrid eylemlerini kullanabilirsiniz. Ayrıca, SendGrid eylemlerinden alınan çıktıyı başka eylemlere de kullanabilirsiniz. 

Bu bağlayıcı yalnızca eylemler sağlar, bu nedenle mantıksal uygulamanızı başlatmak için **yineleme** tetikleyicisi gibi ayrı bir tetikleyici kullanın. Örneğin, listelerinizi düzenli olarak listelere eklerseniz, Office 365 Outlook bağlayıcısını veya Outlook.com bağlayıcısını kullanarak alıcılar ve listeler hakkında e-posta gönderebilirsiniz.
Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* [SendGrid hesabı](https://www.sendgrid.com/) ve [SendGrid API anahtarı](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   API anahtarınız, mantıksal uygulamanızı bir bağlantı oluşturmak ve SendGrid hesabınıza erişmek için yetkilendirir.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* SendGrid hesabınıza erişmek istediğiniz mantıksal uygulama. SendGrid eylemini kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="connect-to-sendgrid"></a>SendGrid 'e bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Bir yol seçin: 

   * Eylem eklemek istediğiniz son adım altında **yeni adım**' ı seçin. 

     -veya-

   * Eylem eklemek istediğiniz adımlar arasında, işaretçinizi adımlar arasındaki oka taşıyın. 
   Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak "SendGrid" yazın. Eylemler listesi altında istediğiniz eylemi seçin.

1. Bağlantınıza bir ad verin. 

1. SendGrid API anahtarınızı girip **Oluştur**' u seçin.

1. Seçtiğiniz eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/sendgrid/)gözden geçirin.

## <a name="get-support"></a>Destek alın

* Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin