---
title: Azure Logic Apps 'den Twilio 'e bağlanma | Microsoft Docs
description: Azure Logic Apps kullanarak genel SMS, MMS ve IP iletilerini Twilio hesabınızla yöneten görevleri ve iş akışlarını otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 09d4a6acf93f5fb01c70f9e9bd935c404c5abf50
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050713"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Azure Logic Apps ile Twilio içindeki iletileri yönetme

Azure Logic Apps ve Twilio Bağlayıcısı ile, genel SMS, MMS ve IP iletilerini içeren Twilio içindeki iletileri alan, gönderen ve listeleme otomatikleştirilmiş görevler ve iş akışları oluşturabilirsiniz. Bu eylemleri, Twilio hesabınızla görevleri gerçekleştirmek için kullanabilirsiniz. Ayrıca, Twilio eylemlerdeki çıktıyı kullanan başka eylemlere de sahip olabilirsiniz. Örneğin, yeni bir ileti geldiğinde, bir ileti içeriğini bolluk bağlayıcısıyla gönderebilirsiniz. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* [Twilio](https://www.twilio.com/)adresinden: 

  * Twilio panonuzda bulabileceğiniz Twilio hesap KIMLIĞINIZ ve [kimlik doğrulama belirteciniz](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them)

    Kimlik bilgileriniz, mantıksal uygulamanızı bir bağlantı oluşturmak ve mantıksal uygulamanızdan Twilio hesabınıza erişmek için yetkilendirirsiniz. 
    Bir Twilio deneme hesabı kullanıyorsanız, yalnızca *doğrulanan* telefon numaralarına SMS gönderebilirsiniz.

  * SMS gönderebileceği doğrulanmış bir Twilio telefon numarası

  * SMS alabilen doğrulanmış bir Twilio telefon numarası

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Twilio hesabınıza erişmek istediğiniz mantıksal uygulama. Bir Twilio eylemi kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="connect-to-twilio"></a>Twilio 'e bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Bir yol seçin: 

     * Eylem eklemek istediğiniz son adım altında **yeni adım**' ı seçin. 

       -veya-

     * Eylem eklemek istediğiniz adımlar arasında, işaretçinizi adımlar arasındaki oka taşıyın. 
     Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.
     
       Arama kutusuna filtreniz olarak "Twilio" yazın. 
       Eylemler listesi altında istediğiniz eylemi seçin.

1. Bağlantınız için gerekli ayrıntıları sağlayın ve **Oluştur**' u seçin:

   * Bağlantınız için kullanılacak ad
   * Twilio hesap KIMLIĞINIZ 
   * Twilio erişim (kimlik doğrulama) belirteciniz

1. Seçtiğiniz eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/twilio/)gözden geçirin.

## <a name="get-support"></a>Destek alın

* Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin