---
title: Azure Logic Apps Twitter 'a bağlanma | Microsoft Docs
description: Azure Logic Apps kullanarak Twitter hesabınızdan izleme ve yönetme, takip eden kullanıcılar, diğer kullanıcılar, zaman çizelgelerinizi ve daha fazlasını içeren görevleri ve iş akışlarını otomatikleştirin.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 22b75b2d2b47dbd496eda06c09b418ae66f6403a
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050693"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak Twitter 'ı izleyin ve yönetin

Azure Logic Apps ve Twitter Bağlayıcısı sayesinde, sözgelimi Twitter, takiciler, kullanıcılar ve takip eden kullanıcılar, zaman çizelgeleri ve daha fazlası gibi diğer eylemlerle birlikte istediğiniz verileri izleyen ve yöneten otomatik görevler ve iş akışları oluşturabilirsiniz, örneğin:

* Fazla doldurulabilir, nakledin ve arama yapın.
* İzleme, takip eden kullanıcılar, zaman çizelgeleri ve daha fazlası gibi verileri alın.

Twitter hesabınızdan yanıt alan Tetikleyicileri kullanabilir ve çıktıyı diğer eylemler için kullanılabilir hale getirebilirsiniz. Twitter hesabınızla görev gerçekleştiren eylemleri kullanabilirsiniz. Ayrıca, Twitter eylemlerinden alınan çıktıyı başka eylemlere de kullanabilirsiniz. Örneğin, belirli bir diyez etiketi ile yeni bir tweet göründüğünde, bolluk bağlayıcısıyla iletiler gönderebilirsiniz. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* Twitter hesabınız ve Kullanıcı kimlik bilgileriniz

   Kimlik bilgileriniz, bir bağlantı oluşturmak ve Twitter hesabınıza erişmek için mantıksal uygulamanızı yetkilendirirsiniz.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Twitter hesabınıza erişmek istediğiniz mantıksal uygulama. Twitter tetikleyicisiyle başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Twitter eylemini kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="connect-to-twitter"></a>Twitter’a Bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Bir yol seçin: 

   * Boş Logic Apps için, arama kutusuna filtreniz olarak "Twitter" yazın. 
   Tetikleyiciler listesinde istediğiniz tetikleyiciyi seçin. 

     -veya-

   * Mevcut mantıksal uygulamalar için: 
   
     * Eylem eklemek istediğiniz son adım altında **yeni adım**' ı seçin. 

       -veya-

     * Eylem eklemek istediğiniz adımlar arasında, işaretçinizi adımlar arasındaki oka taşıyın. 
     Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.
     
       Arama kutusuna filtreniz olarak "Twitter" yazın. 
       Eylemler listesi altında istediğiniz eylemi seçin.

1. Twitter 'da oturum açmanız istenirse, mantıksal uygulamanıza erişimi yetkilendirebilmeniz için şimdi oturum açın.

1. Seçtiğiniz tetikleyici veya eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="examples"></a>Örnekler

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter tetikleyicisi: Yeni bir tweet gönderildiğinde

Bu tetikleyici yeni bir tweet algıladığında, örneğin diyez etiketiyle, #Seattle bir mantıksal uygulama iş akışı başlatır. Örneğin, bu ara bellek bulunduğunda Dropbox bağlayıcısını kullanarak bir Dropbox hesabı gibi depolama alanına sahip bir dosya ekleyebilirsiniz. 

İsteğe bağlı olarak, en az belirtilen takipçilerin bulunduğu kullanıcılardan, uygun olan çok sayıda sahip olan bir koşul ekleyebilirsiniz.

**Kurumsal örnek**: Bu tetikleyiciyi, şirketiniz hakkındaki bilgileri izlemek ve bu içeriği bir SQL veritabanına yüklemek için kullanabilirsiniz.

### <a name="twitter-action-post-a-tweet"></a>Twitter eylemi: Tweet at

Bu eylem bir tweet nakleder, ancak bu eylemi, Tweet 'in daha önce açıklanan tetikleyici tarafından bulunan çok fazla yer alan alanı içeriğini içermesi için ayarlayabilirsiniz. 

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/twitterconnector/)gözden geçirin.

## <a name="get-support"></a>Destek alın

* Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
