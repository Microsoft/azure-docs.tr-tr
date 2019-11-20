---
title: Azure Logic Apps 'den Yammer 'a bağlanma | Microsoft Docs
description: Azure Logic Apps kullanarak Yammer 'da iletileri, akışları ve daha fazlasını izleyen, görüntüleyen ve yöneten görevleri ve iş akışlarını otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 9228a94dcf27d8987b16e2caa2681cf973db0657
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050647"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak Yammer hesabınızı izleyin ve yönetin

Azure Logic Apps ve Yammer Bağlayıcısı sayesinde, diğer eylemlerle birlikte, Yammer hesabınızda iletileri, akışları ve daha fazlasını izleyen ve yöneten otomatik görevler ve iş akışları oluşturabilirsiniz, örneğin:

* Yeni iletilerin izlenen akışlar ve Gruplar bölümünde ne zaman göründüğünü izleyin.
* İletileri, grupları, ağları, kullanıcıların ayrıntılarını ve daha fazlasını alın.
* İletileri gönderin ve beğenin.

Yammer hesabınızdan yanıt alan Tetikleyicileri kullanabilir ve çıktıyı diğer eylemler için kullanılabilir hale getirebilirsiniz. Yammer hesabınızla görev gerçekleştiren eylemleri kullanabilirsiniz. Ayrıca, başka eylemlere Yammer eylemlerinden gelen çıktıyı da kullanabilirsiniz. Örneğin, akışlar veya gruplar içinde yeni iletiler görüntülendiğinde, bu iletileri bolluk bağlayıcısıyla paylaşabilirsiniz. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* Yammer hesabınız ve Kullanıcı kimlik bilgileriniz

   Kimlik bilgileriniz, bir bağlantı oluşturmak ve Yammer hesabınıza erişmek için mantıksal uygulamanızı yetkilendirirsiniz.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Yammer hesabınıza erişmek istediğiniz mantıksal uygulama. Bir Yammer tetikleyicisiyle başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Bir Yammer eylemi kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="connect-to-yammer"></a>Yammer 'a Bağlan

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Bir yol seçin: 

   * Boş Logic Apps için, arama kutusuna filtreniz olarak "Yammer" yazın. 
   Tetikleyiciler listesinde istediğiniz tetikleyiciyi seçin. 

     -veya-

   * Mevcut mantıksal uygulamalar için: 
   
     * Eylem eklemek istediğiniz son adım altında **yeni adım**' ı seçin. 

       -veya-

     * Eylem eklemek istediğiniz adımlar arasında, işaretçinizi adımlar arasındaki oka taşıyın. 
     Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.
     
       Arama kutusuna filtreniz olarak "Yammer" yazın. 
       Eylemler listesi altında istediğiniz eylemi seçin.

1. Yammer 'da oturum açmanız istenirse, erişime izin vermek için şimdi oturum açın.

1. Seçtiğiniz tetikleyici veya eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/yammer/)gözden geçirin.

## <a name="get-support"></a>Destek alın

* Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin