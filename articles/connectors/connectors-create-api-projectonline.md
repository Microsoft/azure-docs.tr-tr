---
title: Azure Logic Apps 'den Project Online 'a bağlanın | Microsoft Docs
description: Azure Logic Apps kullanarak proje çevrimiçi projelerini, görevleri ve kaynakları izleyen, oluşturan ve yöneten iş akışlarını otomatikleştirin
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: fe571209d28fe098ce9b507cb67b0a9a5abd25a3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050869"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak Project Online projelerini, görevlerini ve kaynaklarını yönetme

Azure Logic Apps ve Project Online Bağlayıcısı sayesinde, Office 365 aracılığıyla Project Online 'daki projeleriniz, görevleriniz ve kaynaklarınız için otomatikleştirilmiş görevler ve iş akışları oluşturabilirsiniz. İş akışlarınız bu eylemleri ve diğerlerini gerçekleştirebilir, örneğin:

* Yeni proje, görev veya kaynak oluşturulduğunda izleyin. Ya da yeni projeler yayımlandığında izleyin.
* Yeni projeler, görevler veya kaynaklar oluşturun.
* Mevcut projeleri veya görevleri listeleyin.
* Projeleri kullanıma alma, iade etme veya yayımlama.

Project Online, güçlü proje yönetimi özellikleri sağlayarak neredeyse tüm cihazlarda projeler ve proje portföyü yatırımlarını planlamanızı, önceliklendirmenize ve yönetmenize yardımcı olur. Projeden çevrimiçi olarak yanıt alan Project Online tetikleyicilerini kullanabilir ve çıktıyı diğer eylemler için kullanılabilir hale getirebilirsiniz. Project Online 'da çeşitli görevleri gerçekleştirmek için Logic Apps 'teki işlemleri kullanabilirsiniz. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* [Office 365 hesabıyla](https://www.office.com/)sunulan Project Online, 

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Project Online verilerinize erişmek istediğiniz mantıksal uygulama. Bir Project Online tetikleyicisiyle başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Project Online eylemlerini kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="connect-to-project-online"></a>Project Online 'a bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Bir yol seçin: 

   * Boş Logic Apps için, arama kutusuna filtreniz olarak "Project Online" yazın. 
   Tetikleyiciler listesinde istediğiniz tetikleyiciyi seçin. 

     -veya-

   * Mevcut Logic Apps için, eylem eklemek istediğiniz adım altında **yeni adım**' ı seçin. Arama kutusuna filtreniz olarak "Project Online" yazın. Eylemler listesi altında istediğiniz eylemi seçin.

1. Project Online 'da oturum açmanız istenirse, şimdi oturum açın.

   Kimlik bilgileriniz, mantıksal uygulamanızı Project Online 'a bir bağlantı oluşturmak ve verilerinize erişmek için yetkilendirin.

1. Seçtiğiniz tetikleyici veya eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/projectonline/)gözden geçirin.

## <a name="get-support"></a>Destek alın

* Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin