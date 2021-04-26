---
title: Azure Logic Apps 'ten Salesforce 'a bağlanma
description: Azure Logic Apps kullanarak Salesforce kayıtlarını ve işlerini izleyen, oluşturan ve yöneten görevleri ve iş akışlarını otomatikleştirin
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 9950951ab5189c8c7b72de78bca9465ec5f22748
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87290592"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak Salesforce kaynaklarınızı izleme, oluşturma ve yönetme

Azure Logic Apps ve Salesforce Bağlayıcısı sayesinde, Salesforce kaynaklarınız için kayıtlar, işler ve nesneler gibi otomatikleştirilmiş görevler ve iş akışları oluşturabilirsiniz, örneğin:

* Kayıtların ne zaman oluşturulduğunu veya değiştirildiğini izleyin. 
* Ekleme, güncelleştirme ve silme eylemleri dahil olmak üzere işleri ve kayıtları oluşturun, alın ve yönetin.

Salesforce 'tan yanıt alan Salesforce tetikleyicilerini kullanabilir ve çıktıyı diğer eylemler için kullanılabilir hale getirebilirsiniz. Salesforce kaynaklarıyla görevler gerçekleştirmek için mantıksal uygulamalarınızdaki eylemleri kullanabilirsiniz. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* Bir [Salesforce hesabı](https://salesforce.com/)

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Salesforce hesabınıza erişmek istediğiniz mantıksal uygulama. Bir Salesforce tetikleyicisiyle başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Bir Salesforce eylemi kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="connect-to-salesforce"></a>Salesforce'a bağlan

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

* Sorular için, [Azure Logic Apps Için Microsoft Q&soru sayfasını](/answers/topics/azure-logic-apps.html)ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
