---
title: Azure Logic Apps SharePoint 'e Bağlan | Microsoft Docs
description: Azure Logic Apps kullanarak SharePoint Online veya SharePoint Server 'daki kaynakları izleyen ve yöneten görevleri ve iş akışlarını otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 8a34ee4e90b551da35aff8802c8badc0d74ff539
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050776"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Azure Logic Apps ile SharePoint kaynaklarını izleme ve yönetme

Azure Logic Apps ve SharePoint bağlayıcısıyla, SharePoint Online veya şirket içi SharePoint Server 'daki dosya, klasör, liste, öğe, kişi vb. kaynakları izleyen ve yöneten otomatik görevler ve iş akışları oluşturabilirsiniz, örneğin:

* Dosya veya öğe oluşturma, değiştirme veya silme sırasında izleyin.
* Öğeleri oluşturun, alın, güncelleştirin veya silin.
* Ekleri ekleyin, alın veya silin. Eklerin içeriğini alın.
* Dosyaları oluşturun, kopyalayın, güncelleştirin veya silin. 
* Dosya özelliklerini güncelleştirin. Bir dosya için içerik, meta veri veya özellik alın.
* Klasörleri listeleyin veya ayıklayın.
* Listeler veya liste görünümleri alın.
* İçerik onay durumunu ayarlayın.
* Kişileri çözümleyin.
* HTTP isteklerini SharePoint 'e gönderme.
* Varlık değerlerini al.

SharePoint 'ten yanıt alan Tetikleyicileri kullanabilir ve çıktıyı diğer eylemler için kullanılabilir hale getirebilirsiniz. SharePoint 'te görevler gerçekleştirmek için mantıksal uygulamalarınızdaki eylemleri kullanabilirsiniz. Ayrıca, diğer eylemlerdeki çıktıyı SharePoint eylemleriyle kullanmasını sağlayabilirsiniz. Örneğin, SharePoint 'ten düzenli olarak dosya getirilerseniz, bolluk bağlayıcısını kullanarak ekibinize ileti gönderebilirsiniz.
Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* SharePoint site adresiniz ve Kullanıcı kimlik bilgileriniz

  Kimlik bilgileriniz, mantıksal uygulamanızı bir bağlantı oluşturmak ve SharePoint hesabınıza erişmek için yetkilendirirsiniz. 

* Mantıksal uygulamaları SharePoint Server gibi şirket içi sistemlere bağlayabilmeniz için önce şirket [içi veri ağ geçidini yüklemeniz ve ayarlamanız](../logic-apps/logic-apps-gateway-install.md)gerekir. Bu şekilde, mantıksal uygulamanız için SharePoint sunucu bağlantısı oluştururken ağ geçidi yüklemenizi kullanmayı belirtebilirsiniz.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* SharePoint hesabınıza erişmek istediğiniz mantıksal uygulama. Bir SharePoint tetikleyicisiyle başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Bir SharePoint eylemi kullanmak için, bir Salesforce hesabınız varsa, bir Salesforce tetikleyicisi gibi bir tetikleyici ile mantıksal uygulamanızı başlatın.

  Örneğin, **bir kayıt** Salesforce tetikleyicisi oluşturulduğunda mantıksal uygulamanızı ile başlatabilirsiniz. 
  Bu tetikleyici, Salesforce 'ta bir müşteri adayı gibi yeni bir kaydın oluşturulduğu her seferinde ateşlenir. 
  Daha sonra bu tetikleyiciyi SharePoint **dosya oluştur** eylemiyle takip edebilirsiniz. Bu şekilde, yeni kayıt oluşturulduğunda mantıksal uygulamanız SharePoint 'te bu yeni kayıtla ilgili bilgileri içeren bir dosya oluşturur.

## <a name="connect-to-sharepoint"></a>SharePoint’e bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Boş Logic Apps için, arama kutusuna filtreniz olarak "SharePoint" yazın. Tetikleyiciler listesinde istediğiniz tetikleyiciyi seçin. 

   -veya-

   Mevcut Logic Apps için, SharePoint eylemi eklemek istediğiniz son adım altında **yeni adım**' ı seçin. 
   Arama kutusuna filtreniz olarak "SharePoint" yazın. 
   Eylemler listesi altında istediğiniz eylemi seçin.

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. 
   Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Oturum açmanız istendiğinde, gerekli bağlantı bilgilerini sağlayın. SharePoint Server kullanıyorsanız, Şirket **içi veri ağ geçidi üzerinden Bağlan**' ı seçtiğinizden emin olun. İşiniz bittiğinde **Oluştur**’u seçin.

1. Seçtiğiniz tetikleyici veya eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/sharepoint/)gözden geçirin.

## <a name="get-support"></a>Destek alın

* Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
