---
title: Azure Logic Apps 'ten Salesforce 'a bağlanma | Microsoft Docs
description: Azure Logic Apps kullanarak Salesforce kayıtlarını ve işlerini izleyen, oluşturan ve yöneten görevleri ve iş akışlarını otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: e3e9f7c841d25fa988ae7e0c97adf64a51d8ef87
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050833"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak Salesforce kaynaklarını izleyin, oluşturun ve yönetin

Azure Logic Apps ve Salesforce Bağlayıcısı sayesinde, Salesforce kaynaklarınız için kayıtlar, işler ve nesneler gibi otomatikleştirilmiş görevler ve iş akışları oluşturabilirsiniz, örneğin:

* Kayıtların ne zaman oluşturulduğunu veya değiştirildiğini izleyin. 
* Ekleme, güncelleştirme ve silme eylemleri dahil olmak üzere işleri ve kayıtları oluşturun, alın ve yönetin.

Salesforce 'tan yanıt alan Salesforce tetikleyicilerini kullanabilir ve çıktıyı diğer eylemler için kullanılabilir hale getirebilirsiniz. Salesforce kaynaklarıyla görevler gerçekleştirmek için mantıksal uygulamalarınızdaki eylemleri kullanabilirsiniz. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* Bir [Salesforce hesabı](https://salesforce.com/)

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Salesforce hesabınıza erişmek istediğiniz mantıksal uygulama. Bir Salesforce tetikleyicisiyle başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Bir Salesforce eylemi kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="connect-to-salesforce"></a>Salesforce 'a bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Bir yol seçin: 

   * Boş Logic Apps için, arama kutusuna filtreniz olarak "Salesforce" yazın. 
   Tetikleyiciler listesinde istediğiniz tetikleyiciyi seçin. 

     -veya-

   * Mevcut Logic Apps için, eylem eklemek istediğiniz adım altında **yeni adım**' ı seçin. Arama kutusuna filtreniz olarak "Salesforce" yazın. Eylemler listesi altında istediğiniz eylemi seçin.

1. Salesforce 'ta oturum açmanız istenirse, şimdi oturum açın ve erişime izin verin.

   Kimlik bilgileriniz, mantıksal uygulamanızı Salesforce ile bağlantı oluşturmak ve verilerinize erişmek için yetkilendirin.

1. Seçtiğiniz tetikleyici veya eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/salesforce/)gözden geçirin.

## <a name="get-support"></a>Destek alın

* Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin