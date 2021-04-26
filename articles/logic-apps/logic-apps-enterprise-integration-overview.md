---
title: B2B kurumsal tümleştirme
description: Azure Logic Apps ve Enterprise Integration Pack kullanarak kurumsal tümleştirme için otomatik B2B iş akışları oluşturma hakkında bilgi edinin
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 03/24/2021
ms.openlocfilehash: 09d253aae4c054db5efdc252f62986044e1d366b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771866"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps ve Enterprise Integration Pack ile B2B kurumsal tümleştirme çözümleri

İşletmeler arası (B2B) çözümleri ve kuruluşlar arasındaki sorunsuz iletişim için [Azure Logic Apps](../logic-apps/logic-apps-overview.md)ile Enterprise Integration Pack (EIP) kullanarak otomatik ölçeklendirilebilir kurumsal tümleştirme iş akışları oluşturabilirsiniz. Kuruluşlar farklı protokoller ve biçimler kullanmasına karşın, iletileri elektronik olarak değiş tokuş edebilirler. EıP, farklı biçimleri, kuruluşların sistemlerinin [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [x12](logic-apps-enterprise-integration-x12.md)ve [ediolgu](../logic-apps/logic-apps-enterprise-integration-edifact.md)gibi sektör standardı protokollerini işleyebildiğine ve desteklediğinden bir biçime dönüştürür. Ayrıca, hem şifreleme hem de dijital imza kullanarak iletiler için güvenliği geliştirebilirsiniz. EıP, bu [kurumsal tümleştirme bağlayıcılarını](../connectors/managed.md#enterprise-connectors) ve bu sektör standartlarını destekler:

* Elektronik veri değişimi (EDI)
* Kurumsal uygulama tümleştirme (EAı)

Microsoft BizTalk Server veya Azure BizTalk Services hakkında bilgi sahibiyseniz, EıP benzer kavramları izler ve özelliklerin kullanımını kolaylaştırır. Ancak, önemli bir farklılık, B2B iletişimlerinde kullanılan yapıların depolanmasını ve yönetimini basitleştirmek amacıyla "tümleştirme hesapları" temelinde, EıP 'nin mimarilere göre mimarmalarıdır. Bu hesaplar, iş ortakları, sözleşmeler, şemalar, haritalar ve sertifikalar gibi tüm yapıtlarınızı depolayan bulut tabanlı kapsayıcılardır. 

## <a name="why-use-the-enterprise-integration-pack"></a>Enterprise Integration Pack neden kullanılmalıdır?

* EıP ile, tüm yapıtlarınızı tek bir yerde, tümleştirme hesabınızda da saklayabilirsiniz.

* Azure Logic Apps ve bağlayıcıları kullanarak B2B iş akışları oluşturabilir ve üçüncü taraf hizmet olarak yazılım (SaaS) uygulamalarıyla, şirket içi uygulamalarla ve özel uygulamalarla tümleştirebilirsiniz.

* Azure işlevleri ile mantıksal uygulamalarınız için özel kod oluşturabilirsiniz.

## <a name="how-do-i-get-started"></a>Nasıl kullanmaya başlayabilirim?

EıP ile B2B Logic App iş akışları oluşturmaya başlayabilmeniz için önce şu öğeler gerekir:

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Kullanmak istediğiniz yapıtlara sahip bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Haritalar ve şemalar oluşturmak için, [Visual studio 2015 2,0 ve Visual studio 2015 Microsoft Azure Logic Apps kurumsal tümleştirme araçlarını](https://aka.ms/vsmapsandschemas) kullanabilirsiniz.

Bir tümleştirme hesabı oluşturup yapılarınızı ekledikten sonra, Azure portal bir mantıksal uygulama oluşturarak bu yapıtlarla B2B iş akışları oluşturmaya başlayabilirsiniz. Logic Apps 'e yeni başladıysanız, [temel bir mantıksal uygulama oluşturmayı](../logic-apps/quickstart-create-first-logic-app-workflow.md)deneyin. Bu yapıtlar ile çalışmak için, tümleştirme hesabınızı mantıksal uygulamanıza bağlamanız gerekir. Bundan sonra mantıksal uygulamanız tümleştirme hesabınıza erişebilir. Ayrıca, Visual Studio veya [PowerShell](/powershell/module/az.logicapp)kullanarak mantıksal uygulamalar oluşturabilir, yönetebilir ve dağıtabilirsiniz.

B2B Logic Apps oluşturmaya başlamak için üst düzey adımlar şunlardır:

![B2B Logic Apps oluşturmak için Önkoşullar](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Hemen deneyin

[AS2 iletileri gönderen ve alan tam işlemsel bir örnek mantıksal uygulama dağıtma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Sonraki adımlar

* [Ticari iş ortakları oluşturma](logic-apps-enterprise-integration-partners.md)
* [Anlaşma oluşturma](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Şema ekleme](logic-apps-enterprise-integration-schemas.md)
* [Haritalar ekleme](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [BizTalk Services’dan geçirme](../logic-apps/logic-apps-move-from-mabs.md)
