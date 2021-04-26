---
title: Azure Logic Apps 'ten SMTP 'ye bağlanma
description: Azure Logic Apps kullanarak SMTP (Basit Posta Aktarım Protokolü) hesabınız aracılığıyla e-posta gönderen görevleri ve iş akışlarını otomatikleştirin
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 9989d0ebd95bfe5ee49be2ba76b73e07630b519a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283954"
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
   | **Bağlantı adı** | Yes | SMTP sunucunuza bağlantı için bir ad | 
   | **SMTP sunucu adresi** | Yes | SMTP sunucunuzun adresi | 
   | **Kullanıcı Adı** | Yes | SMTP hesabınız için Kullanıcı adınız | 
   | **Parola** | Yes | SMTP hesabınız için parolanız | 
   | **SMTP sunucusu bağlantı noktası** | No | SMTP sunucunuzda kullanmak istediğiniz belirli bir bağlantı noktası | 
   | **SSL etkinleştirilsin mi?** | No | TLS/SSL şifrelemesini açın veya kapatın. | 
   |||| 

1. Seçtiğiniz eyleminiz için gerekli ayrıntıları sağlayın. 

1. Mantıksal uygulamanızı kaydedin veya mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bu bağlayıcı hakkında, bağlayıcının Swagger dosyasında açıklanan Tetikleyiciler, Eylemler ve sınırlar gibi daha teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/smtpconnector/)bakın.

> [!NOTE]
> Bir [tümleştirme hizmeti ortamındaki (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Logic Apps için, bu bağlayıcının Ise etiketli sürümü bunun yerine [Ise ileti sınırlarını](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) kullanır.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
