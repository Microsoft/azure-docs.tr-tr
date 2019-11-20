---
title: Azure Logic Apps 'ten SMTP 'ye Bağlan | Microsoft Docs
description: Azure Logic Apps kullanarak SMTP (Basit Posta Aktarım Protokolü) hesabınız aracılığıyla e-posta gönderen görevleri ve iş akışlarını otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 1649f197d4dbd88e2b485ab32f254a2d09696a84
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050750"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Azure Logic Apps ile SMTP hesabınızdan e-posta gönderin

Azure Logic Apps ve Basit Posta Aktarım Protokolü (SMTP) bağlayıcısıyla, SMTP hesabınızdan e-posta gönderen otomatikleştirilmiş görevler ve iş akışları oluşturabilirsiniz. Ayrıca, diğer eylemlerdeki çıktıyı SMTP eylemleriyle kullanmasını sağlayabilirsiniz. Örneğin, SMTP 'niz bir e-posta gönderdikten sonra, bolluk bağlayıcısıyla ekibinize bolluk içinde bildirimde bulunabilir. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* SMTP hesabınız ve Kullanıcı kimlik bilgileriniz

  Kimlik bilgileriniz, mantıksal uygulamanızı bir bağlantı oluşturmak ve SMTP hesabınıza erişmek için yetkilendirin.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* SMTP hesabınıza erişmek istediğiniz mantıksal uygulama. Bir SMTP eylemi kullanmak için, bir Salesforce hesabınız varsa, bir Salesforce tetikleyicisi gibi bir tetikleyici ile mantıksal uygulamanızı başlatın.

  Örneğin, **bir kayıt** Salesforce tetikleyicisi oluşturulduğunda mantıksal uygulamanızı ile başlatabilirsiniz. 
  Bu tetikleyici, Salesforce 'ta bir müşteri adayı gibi yeni bir kaydın oluşturulduğu her seferinde ateşlenir. 
  Daha sonra bu tetikleyiciyi SMTP **e-posta gönder** eylemiyle izleyebilirsiniz. Bu şekilde, yeni kayıt oluşturulduğunda mantıksal uygulamanız, SMTP hesabınızdan yeni kayıtla ilgili bir e-posta gönderir.

## <a name="connect-to-smtp"></a>SMTP 'ye Bağlan

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. SMTP eylemi eklemek istediğiniz son adım altında **yeni adım**' ı seçin. 

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. 
   Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak "SMTP" yazın. Eylemler listesi altında istediğiniz eylemi seçin.

1. İstendiğinde, bu bağlantı bilgilerini sağlayın:

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Bağlantı Adı** | Evet | SMTP sunucunuza bağlantı için bir ad | 
   | **SMTP sunucu adresi** | Evet | SMTP sunucunuzun adresi | 
   | **Kullanıcı adı** | Evet | SMTP hesabınız için Kullanıcı adınız | 
   | **Parola** | Evet | SMTP hesabınız için parolanız | 
   | **SMTP sunucusu bağlantı noktası** | Hayır | SMTP sunucunuzda kullanmak istediğiniz belirli bir bağlantı noktası | 
   | **SSL etkinleştirilsin mi?** | Hayır | SSL şifrelemesini açın veya kapatın. | 
   |||| 

1. Seçtiğiniz eyleminiz için gerekli ayrıntıları sağlayın. 

1. Mantıksal uygulamanızı kaydedin veya mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/smtpconnector/)gözden geçirin.

## <a name="get-support"></a>Destek alın

* Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin