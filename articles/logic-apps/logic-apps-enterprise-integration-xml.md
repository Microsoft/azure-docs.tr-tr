---
title: XML iletileri ve düz dosyalar-Azure Logic Apps
description: Enterprise Integration Pack ile Azure Logic Apps XML iletilerini işleme, doğrulama ve dönüştürme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 730adf3e6ef3ddab5a8b0e927f94ffe3725358f4
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679916"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Enterprise Integration Pack ile Azure Logic Apps içindeki XML iletileri ve düz dosyalar

[Azure Logic Apps](logic-apps-overview.md), Enterprise Integration Pack kullanarak gönderme ve alma yaptığınız xml iletilerini işleyebilirsiniz. BizTalk Server kullandıysanız, Enterprise Integration Pack iletileri dönüştürmek ve doğrulamak, düz dosyalarla çalışmak ve hatta belirli özellikleri bir iletiden zenginleştirmek veya ayıklamak için XPath 'i kullanmak için benzer özellikler sağlar. Bu alana yeni başladıysanız, bu özellikler mantıksal uygulamanızın iş akışındaki iletileri nasıl işleyeceğini genişletir. Örneğin, işletmeden işletmeye (B2B) senaryonuz varsa ve belirli XML şemalarıyla çalışıyorsanız, şirketinizin bu iletileri nasıl işleceğini geliştirmek için Enterprise Integration Pack kullanabilirsiniz.

Örneğin, Enterprise Integration Pack şu özellikleri içerir:

* [XML doğrulaması](logic-apps-enterprise-integration-xml-validation.md): belirli bir şemaya karşı gelen veya gıden bir XML iletisini doğrulayın.

* [XML Transform](logic-apps-enterprise-integration-transform.md): Haritalar kullanarak bir iş ortağının gereksinimlerine veya gereksinimlerinize göre bir XML iletisini dönüştürün veya özelleştirin.

* [Düz dosya kodlama ve düz dosya kod çözme](logic-apps-enterprise-integration-flatfile.md): düz bir dosyayı kodla veya kodunu çözün.

  Örneğin SAP, ıDOC dosyalarını düz dosya biçiminde kabul eder ve gönderir. Birçok tümleştirme platformu, Logic Apps dahil olmak üzere XML iletileri oluşturur. Bu nedenle, düz dosya kodlayıcısını kullanarak "XML dosyalarını düz dosyalara" dönüştürmek için bir mantıksal uygulama oluşturabilirsiniz.

* [XPath](workflow-definition-language-functions-reference.md#xpath): bir iletiyi zenginleştirin ve iletiden belirli özellikleri ayıklayın. Sonra, iletiyi bir hedefe veya bir ara uç noktasına yönlendirmek için ayıklanan özellikleri kullanabilirsiniz.

## <a name="sample"></a>Örnek

Azure Logic Apps içindeki XML özelliklerini kullanarak [tam işletimsel mantıksal uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub örneği) dağıtın.

## <a name="next-steps"></a>Sonraki adımlar

[Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) hakkında daha fazla bilgi edinin
