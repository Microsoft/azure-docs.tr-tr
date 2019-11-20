---
title: Genel Bakış-Azure sunucusuz
description: Altyapı hakkında endişelenmeden bulutta güçlü çözümler oluşturun
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 363002712bdd06e74360de9af186f5a458a4999d
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "68385364"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Genel Bakış: Azure Logic Apps ve Azure Işlevleri ile Azure sunucusuz

[Sunucusuz](https://azure.microsoft.com/solutions/serverless/) uygulamalar, daha fazla geliştirme hızı, azaltılmış kod, basitlik ve ölçek gibi avantajlar sunar. Bu makalede sunucusuz çözümlerin ve Azure sunucusuz tekliflerin farklı öznitelikleri ele alınmaktadır.

## <a name="what-is-serverless"></a>Sunucusuz nedir?

Sunucusuz hiçbir sunucu olmadığı anlamına gelir, ancak bunun yerine geliştiricilerin sunucular hakkında endişelenmek zorunda kalmaz. Geleneksel uygulama geliştirmenin büyük bir bölümü, uygulamanın taleplerini karşılamak üzere ölçeklendirme, barındırma ve izleme çözümlerini izleyen sorulara yanıt verebilir. Sunucusuz ile, bu sorular çözümün parçası olarak ele alınmıştır. Ayrıca, sunucusuz uygulamalar tüketim tabanlı bir planda faturalandırılır. Uygulama hiç kullanılmıyorsa, hiçbir ücret alınmaz. Bu özellikler, geliştiricilerin yalnızca bir çözümün iş mantığına odaklanmalarına yardımcı olur.

Sunucusuz için çekirdek Azure Hizmetleri [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) ve [Azure işlevleridir](https://azure.microsoft.com/services/functions/). Her iki çözüm de daha önce açıklanan ilkeleri izler ve geliştiricilerin en az kodla güçlü bulut uygulamaları oluşturmalarına yardımcı olur.

## <a name="what-is-azure-logic-apps"></a>Azure Logic Apps nedir?

[Azure Logic Apps](logic-apps-overview.md) , bulutta ölçeklenebilir tümleştirmelerin ve iş akışlarının basitleştirilmesi ve uygulanması için bir yol sağlar. Bu hizmet, işleminizi iş akışı olarak adlandırılan bir dizi adım olarak modelleyebilir ve otomatikleştirebileceğiniz bir görsel tasarımcı sağlar. Bulut hizmetlerinde ve şirket içi sistemlerde, sunucusuz bir uygulamayı diğer API 'lere hızlıca bağlayan çok sayıda [bağlayıcı](../connectors/apis-list.md) vardır. Her mantıksal uygulama, "Dynamics CRM 'ye bir hesap eklendiğinde" gibi bir tetikleyici ile başlar. Tetikleyici etkinleştirildikten sonra, iş akışı eylem, dönüşüm ve koşullu mantık birleşimlerini çalıştırabilir. Logic Apps, özellikle işlem bir dış sistemle veya API ile etkileşimde bulunmayı gerektirdiğinde, bir işlemdeki farklı Azure Işlevlerini düzenleyen harika bir seçimdir.

Logic Apps kullanmaya başlamak için [ilk mantıksal uygulamanızı oluşturmaya](quickstart-create-first-logic-app-workflow.md)başlayın. Logic Apps hakkında daha fazla teknik bilgi için bkz. [Geliştirici başvurusu](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Azure Işlevleri nedir?

Azure Işlevleri, bulutta kod parçalarını veya "işlevleri" kolayca çalıştırmaya yönelik bir hizmettir. Tüm uygulama veya gerekli altyapı hakkında endişelenmeden yalnızca geçerli sorun için gerekli olan kodu yazabilirsiniz. İşlevler, geliştirmeyi daha da üretken hale getirir ve tercih C# F#ettiğiniz, Node. js, Python veya PHP gibi geliştirme dilinizi kullanabilirsiniz. Yalnızca kodunuzun çalıştırıldığı süre için ödeme yaparsınız ve gerektiğinde Azure ölçeklendiriyor olursunuz.

Azure Işlevleri 'ni kullanmaya başlamak için [Ilk Azure işlevinizi oluşturma](../azure-functions/functions-create-first-azure-function.md)ile başlayın. Işlevler hakkında daha fazla teknik bilgi için bkz. [Geliştirici başvurusu](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Azure 'da sunucusuz uygulamalar oluşturup nasıl dağıtabilirim?

Azure, sunucusuz uygulamalar geliştirmek, dağıtmak ve yönetmek için zengin araçlar sağlar. Uygulamaları doğrudan Azure portal, [Visual Studio 'daki araçlarla](logic-apps-serverless-get-started-vs.md)veya [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)oluşturabilirsiniz. Uygulamanızı derledikten sonra, [Azure Resource Manager şablonlarıyla bu uygulamayı hızlıca dağıtabilirsiniz](logic-apps-deploy-azure-resource-manager-templates.md). Azure ayrıca Azure portal, API veya SDK 'lar aracılığıyla ya da Azure Izleyici günlükleri ve Application Insights tümleşik araçları aracılığıyla erişebileceğiniz izleme sağlar.

## <a name="next-steps"></a>Sonraki adımlar

* [Visual Studio 'da sunucusuz uygulama oluşturma](logic-apps-serverless-get-started-vs.md)
* [Sunucusuz ile bir müşteri öngörüleri panosu oluşturma](logic-apps-scenario-social-serverless.md)
* [Mantıksal uygulama dağıtımını otomatikleştirme](logic-apps-azure-resource-manager-templates-overview.md)
