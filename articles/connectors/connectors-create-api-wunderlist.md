---
title: Azure Logic Apps için Wunderlist 'e bağlanın | Microsoft Docs
description: Azure Logic Apps kullanarak Wunderlist hesabınızda listeleri, görevleri, anımsatıcıları ve daha fazlasını izleyen ve yöneten görevleri ve iş akışlarını otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: d57ab4a7b655a7d49a7120d358dccc627099e5fd
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050652"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak Wunderlist 'i izleyin ve yönetin

Azure Logic Apps ve Wunderlist bağlayıcısıyla, Wunderlist hesabınızda, diğer eylemlerle birlikte yapılacaklar listelerini, görevleri, anımsatıcıları ve daha fazlasını izleyen ve yöneten otomatik görevler ve iş akışları oluşturabilirsiniz, örneğin:

* Yeni görevler oluşturulduğunda, görevler ne zaman olduğunda veya anımsatıcılar gerçekleştiğinde izleyin.
* Listeler, notlar, görevler, alt görevler ve daha fazlasını oluşturun ve yönetin.
* Anımsatıcıları ayarlayın.
* Listeler, görevler, alt görevler, anımsatıcılar, dosyalar, notlar, açıklamalar ve daha fazlasını alın.

[Wunderlist](https://www.wunderlist.com/) , projelerinizi, yapılacaklar listelerinizi ve görevlerinizi dilediğiniz cihazda, her yerde planlayıp, yönetmenize ve bitirebilmenizi sağlayan bir hizmettir. Wunderlist hesabınızdan yanıt alan Tetikleyicileri kullanabilir ve çıktıyı diğer eylemler için kullanılabilir hale getirebilirsiniz. Wunderlist hesabınızla görev gerçekleştiren eylemleri kullanabilirsiniz. Ayrıca, başka eylemlere Wunderlist eylemlerinin çıkışını kullanmasını sağlayabilirsiniz. Örneğin, yeni görevlerin süresi geldiğinde, bolluk bağlayıcısıyla iletiler gönderebilirsiniz. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* Wunderlist hesabınız ve Kullanıcı kimlik bilgileriniz

   Kimlik bilgileriniz, bir bağlantı oluşturmak ve Wunderlist hesabınıza erişmek için mantıksal uygulamanızı yetkilendiremez.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Yammer hesabınıza erişmek istediğiniz mantıksal uygulama. Wunderlist tetikleyicisiyle başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Wunderlist eylemi kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="connect-to-wunderlist"></a>Wunderlist 'e bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Bir yol seçin: 

   * Boş Logic Apps için, arama kutusuna filtreniz olarak "Wunderlist" yazın. 
   Tetikleyiciler listesinde istediğiniz tetikleyiciyi seçin. 

     -veya-

   * Mevcut mantıksal uygulamalar için: 
   
     * Eylem eklemek istediğiniz son adım altında **yeni adım**' ı seçin. 

       -veya-

     * Eylem eklemek istediğiniz adımlar arasında, işaretçinizi adımlar arasındaki oka taşıyın. 
     Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.
     
       Arama kutusuna filtreniz olarak "Wunderlist" yazın. 
       Eylemler listesi altında istediğiniz eylemi seçin.

1. Wunderlist 'te oturum açmanız istenirse, erişime izin vermek için şimdi oturum açın.

1. Seçtiğiniz tetikleyici veya eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/wunderlist/)gözden geçirin.

## <a name="get-support"></a>Destek alın

* Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin