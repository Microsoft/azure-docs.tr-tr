---
title: Genel bakış-bulut tabanlı uygulamalar ve çözümler için Azure sunucusuz
description: Azure Logic Apps ve Azure Işlevleri 'ni kullanarak altyapı hakkında endişelenmeden bulut tabanlı uygulamalar ve çözümler oluşturmayı öğrenin
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 470857883144f4bf8c21a1921afacb7ba3f5ca00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788104"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure sunucusuz: Azure Logic Apps ve Azure Işlevleri ile bulut tabanlı uygulamalar ve çözümler oluşturmaya genel bakış

[Sunucusuz](https://azure.microsoft.com/solutions/serverless/) uygulamalar, daha fazla geliştirme hızı, azaltılmış kod, basitlik ve ölçek gibi avantajlar sunar. Bu makalede sunucusuz çözümlerin ve Azure sunucusuz tekliflerin farklı öznitelikleri ele alınmaktadır.

## <a name="what-is-serverless"></a>Sunucusuz nedir?

Sunucusuz hiçbir sunucu olmadığı anlamına gelir, ancak bunun yerine geliştiricilerin sunucular hakkında endişelenmek zorunda kalmaz. Geleneksel uygulama geliştirmenin büyük bir bölümü, uygulamanın taleplerini karşılamak üzere ölçeklendirme, barındırma ve izleme çözümlerini izleyen sorulara yanıt verebilir. Sunucusuz ile, bu sorular çözümün parçası olarak ele alınmıştır. Ayrıca, sunucusuz uygulamalar tüketim tabanlı bir planda faturalandırılır. Uygulama hiç kullanılmıyorsa, hiçbir ücret alınmaz. Bu özellikler, geliştiricilerin yalnızca bir çözümün iş mantığına odaklanmalarına yardımcı olur.

Sunucusuz için çekirdek Azure Hizmetleri [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) ve [Azure işlevleridir](https://azure.microsoft.com/services/functions/). Her iki çözüm de daha önce açıklanan ilkeleri izler ve geliştiricilerin en az kodla güçlü bulut uygulamaları oluşturmalarına yardımcı olur.

## <a name="what-is-azure-logic-apps"></a>Azure Logic Apps nedir?

[Azure Logic Apps](logic-apps-overview.md) , bulutta ölçeklenebilir tümleştirmelerin ve iş akışlarının basitleştirilmesi ve uygulanması için bir yol sağlar. Bu hizmet, işleminizi iş akışı olarak adlandırılan bir dizi adım olarak modelleyebilir ve otomatikleştirebileceğiniz bir görsel tasarımcı sağlar. Bulut hizmetlerinde ve şirket içi sistemlerde, sunucusuz bir uygulamayı diğer API 'lere hızlıca bağlayan çok sayıda [bağlayıcı](../connectors/apis-list.md) vardır. Her mantıksal uygulama, "Dynamics CRM 'ye bir hesap eklendiğinde" gibi bir tetikleyici ile başlar. Tetikleyici etkinleştirildikten sonra, iş akışı eylem, dönüşüm ve koşullu mantık birleşimlerini çalıştırabilir. Logic Apps, özellikle işlem bir dış sistemle veya API ile etkileşimde bulunmayı gerektirdiğinde, bir işlemdeki farklı Azure Işlevlerini düzenleyen harika bir seçimdir.

Logic Apps kullanmaya başlamak için [ilk mantıksal uygulamanızı oluşturmaya](quickstart-create-first-logic-app-workflow.md)başlayın. Logic Apps hakkında daha fazla teknik bilgi için bkz. [Geliştirici başvurusu](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Azure İşlevleri nedir?

Azure Işlevleri, bulutta kod parçalarını veya "işlevleri" kolayca çalıştırmaya yönelik bir hizmettir. Tüm uygulama veya gerekli altyapı hakkında endişelenmeden yalnızca geçerli sorun için gerekli olan kodu yazabilirsiniz. İşlevler, geliştirmeyi daha da üretken hale getirir ve tercih ettiğiniz geliştirme dilini (C#, F #, Node.js, Python veya PHP gibi) kullanabilirsiniz. Yalnızca kodunuzun çalıştırıldığı süre için ödeme yaparsınız ve gerektiğinde Azure ölçeklendiriyor olursunuz.

Azure Işlevleri 'ni kullanmaya başlamak için [Ilk Azure işlevinizi oluşturma](../azure-functions/functions-get-started.md)ile başlayın. Işlevler hakkında daha fazla teknik bilgi için bkz. [Geliştirici başvurusu](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Azure 'da sunucusuz uygulamalar oluşturup nasıl dağıtabilirim?

Azure, sunucusuz uygulamalar geliştirmek, dağıtmak ve yönetmek için zengin araçlar sağlar. Uygulamaları doğrudan Azure portal, [Visual Studio 'daki araçlarla](logic-apps-serverless-get-started-vs.md)veya [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)oluşturabilirsiniz. Uygulamanızı derledikten sonra, [Azure Resource Manager şablonlarıyla bu uygulamayı hızlıca dağıtabilirsiniz](logic-apps-deploy-azure-resource-manager-templates.md). Azure ayrıca Azure portal, API veya SDK 'lar aracılığıyla ya da Azure Izleyici günlükleri ve Application Insights tümleşik araçları aracılığıyla erişebileceğiniz izleme sağlar.

## <a name="next-steps"></a>Sonraki adımlar

* [Visual Studio 'da sunucusuz uygulama oluşturma](logic-apps-serverless-get-started-vs.md)
* [Sunucusuz ile bir müşteri öngörüleri panosu oluşturma](logic-apps-scenario-social-serverless.md)
* [Mantıksal uygulama dağıtımı otomatikleştirme](logic-apps-azure-resource-manager-templates-overview.md)