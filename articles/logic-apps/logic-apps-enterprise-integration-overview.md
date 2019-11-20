---
title: B2B kurumsal tümleştirme-Azure Logic Apps
description: Azure Logic Apps ve Enterprise Integration Pack kullanarak kurumsal tümleştirme için otomatik B2B iş akışları oluşturma hakkında bilgi edinin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: overview
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.date: 08/01/2019
ms.openlocfilehash: 54d665d6a4fd9aa0216d9eccf821f5af539a3636
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "71087578"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps ve Enterprise Integration Pack ile B2B kurumsal tümleştirme çözümleri

İşletmeler arası (B2B) çözümleri ve kuruluşlar arasındaki sorunsuz iletişim için [Azure Logic Apps](../logic-apps/logic-apps-overview.md)ile Enterprise Integration Pack (EIP) kullanarak otomatik ölçeklendirilebilir kurumsal tümleştirme iş akışları oluşturabilirsiniz. Kuruluşlar farklı protokoller ve biçimler kullanmasına karşın, iletileri elektronik olarak değiş tokuş edebilirler. EıP, farklı biçimleri, kuruluşların sistemlerinin [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [x12](logic-apps-enterprise-integration-x12.md)ve [ediolgu](../logic-apps/logic-apps-enterprise-integration-edifact.md)gibi sektör standardı protokollerini işleyebildiğine ve desteklediğinden bir biçime dönüştürür. Ayrıca, hem şifreleme hem de dijital imzalarla iletileri güvenli hale getirebilirsiniz. EıP, bu [kurumsal tümleştirme bağlayıcılarını](../connectors/apis-list.md#integration-account-connectors) ve bu sektör standartlarını destekler:

* Elektronik veri değişimi (EDI)
* Kurumsal uygulama tümleştirme (EAı)

Microsoft BizTalk Server veya Azure BizTalk Services hakkında bilgi sahibiyseniz, EıP benzer kavramları izler ve özelliklerin kullanımını kolaylaştırır. Ancak, önemli bir farklılık, B2B iletişimlerinde kullanılan yapıların depolanmasını ve yönetimini basitleştirmek amacıyla "tümleştirme hesapları" temelinde, EıP 'nin mimarilere göre mimarmalarıdır. Bu hesaplar, iş ortakları, sözleşmeler, şemalar, haritalar ve sertifikalar gibi tüm yapıtlarınızı depolayan bulut tabanlı kapsayıcılardır. 

## <a name="why-use-the-enterprise-integration-pack"></a>Enterprise Integration Pack neden kullanılmalıdır?

* EıP ile, tüm yapıtlarınızı tek bir yerde, tümleştirme hesabınızda da saklayabilirsiniz.

* Azure Logic Apps ve bağlayıcıları kullanarak B2B iş akışları oluşturabilir ve üçüncü taraf hizmet olarak yazılım (SaaS) uygulamalarıyla, şirket içi uygulamalarla ve özel uygulamalarla tümleştirebilirsiniz.

* Azure işlevleri ile mantıksal uygulamalarınız için özel kod oluşturabilirsiniz.

## <a name="how-do-i-get-started"></a>Nasıl kullanmaya başlarım?

EıP ile B2B Logic App iş akışları oluşturmaya başlayabilmeniz için önce şu öğeler gerekir:

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Kullanmak istediğiniz yapıtlara sahip bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Haritalar ve şemalar oluşturmak için, [Visual studio 2015 2,0 ve Visual studio 2015 Microsoft Azure Logic Apps kurumsal tümleştirme araçlarını](https://aka.ms/vsmapsandschemas) kullanabilirsiniz.

Bir tümleştirme hesabı oluşturup yapılarınızı ekledikten sonra, Azure portal bir mantıksal uygulama oluşturarak bu yapıtlarla B2B iş akışları oluşturmaya başlayabilirsiniz. Logic Apps 'e yeni başladıysanız, [temel bir mantıksal uygulama oluşturmayı](../logic-apps/quickstart-create-first-logic-app-workflow.md)deneyin. Bu yapıtlar ile çalışmak için, tümleştirme hesabınızı mantıksal uygulamanıza bağlamanız gerekir. Bundan sonra mantıksal uygulamanız tümleştirme hesabınıza erişebilir. Ayrıca, Visual Studio veya [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp)kullanarak mantıksal uygulamalar oluşturabilir, yönetebilir ve dağıtabilirsiniz.

B2B Logic Apps oluşturmaya başlamak için üst düzey adımlar şunlardır:

![B2B Logic Apps oluşturmak için Önkoşullar](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Şimdi deneyin

[AS2 iletileri gönderen ve alan tam işlemsel bir örnek mantıksal uygulama dağıtma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Sonraki adımlar

* [Ticari iş ortakları oluşturma](logic-apps-enterprise-integration-partners.md)
* [Anlaşma oluşturma](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Şema ekleme](logic-apps-enterprise-integration-schemas.md)
* [Haritalar ekleme](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [BizTalk Services geçir](../logic-apps/logic-apps-move-from-mabs.md)
