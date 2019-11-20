---
title: Örnekler & ortak senaryolar-Azure Logic Apps
description: Azure Logic Apps için örnekler, yaygın senaryolar, öğreticiler ve izlenecek yollar bulun
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 9e245d1a0054d9de5b8b524416c8c92eb02353a7
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025512"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Azure Logic Apps için genel senaryolar, örnekler, öğreticiler ve izlenecek yollar

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) , şirket içi SQL Server veya SAP 'den Azure bilişsel hizmetler 'e kadar çeşitli [kullanıma açık bağlayıcılar](../connectors/apis-list.md)sunarak farklı Hizmetleri düzenlemenize ve tümleştirmenize yardımcı olur. Logic Apps hizmeti "sunucusuz" olduğundan ölçek veya örnekler hakkında endişelenmeniz gerekmez. Yapmanız gereken tek şey, bir tetikleyiciyle iş akışını ve iş akışının gerçekleştirdiği eylemleri tanımlar. Temel alınan platform ölçek, kullanılabilirlik ve performansı işler. Logic Apps, özellikle birden fazla sistem genelinde birden çok eylemi koordine etmeniz gereken kullanım durumları ve senaryolar için yararlıdır.

Azure Logic Apps desteklediği birçok model ve özellik hakkında daha fazla bilgi edinmenize yardımcı olmak için, yaygın örnekler ve senaryolar aşağıda verilmiştir.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Mantıksal uygulama iş akışları için popüler başlangıç noktaları

Her mantıksal uygulama bir [*tetikleyici*](../logic-apps/logic-apps-overview.md#logic-app-concepts)ile başlar ve mantıksal uygulama iş akışınızı başlatan ve bu tetikleyicinin bir parçası olarak herhangi bir verilerde geçiş yapan yalnızca bir tetikleyici. Bazı bağlayıcılar bu türlerde gelen Tetikleyiciler sağlar:

* *Yoklama Tetikleyicileri*: bir hizmet uç noktasını düzenli olarak yeni veriler için denetler. Yeni veriler mevcut olduğunda, tetikleyici verileri girdi olarak kullanarak yeni bir iş akışı örneği oluşturur ve çalıştırır.

* *İtme Tetikleyicileri*: bir hizmet uç noktasındaki verileri dinler ve belirli bir olay gerçekleşene kadar bekler. Olay gerçekleştiğinde, tetikleyici hemen başlatılır, giriş olarak kullanılabilir verileri kullanan yeni bir iş akışı örneği oluşturur ve çalışır.

İşte yalnızca birkaç popüler tetikleyici örnekleri:

* Masını

  * [ **Yinelenme** tetikleyicisi](../connectors/connectors-native-recurrence.md) , mantıksal uygulamanızı başlatmak için başlangıç tarihi ve saati ile yinelenme süresini ayarlamanıza olanak sağlar. Örneğin, mantıksal uygulamanızı tetiklemek için haftanın günlerini ve günün saatlerini seçebilirsiniz. Daha fazla bilgi için şu konulara bakın:

    * [Azure Logic Apps ile yinelenen otomatikleştirilmiş görevler, süreçler ve iş akışları zamanlayın ve çalıştırın](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Öğretici: Azure Logic Apps bir zamanlamaya göre trafiği denetleyin](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * "E-posta alındığında" tetikleyicisi, mantıksal uygulamanızın Logic Apps tarafından desteklenen herhangi bir posta sağlayıcısından yeni e-postayı denetlemesini sağlar; örneğin, [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/)vb. Daha fazla bilgi için şu konulara bakın: 

    * [Öğretici: Azure Logic Apps ile posta listesi isteklerini yönetme](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Öğretici: Azure Logic Apps ile e-postaları ve ekleri işlemeyi otomatikleştirme](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [ **Http** tetikleyicisi](../connectors/connectors-native-http.md) , mantıksal uygulamanızın http üzerinden iletişim kurarak belirtilen bir hizmet uç noktasını denetlemesini sağlar.
  
* Hareketle

  * [ **İstek** tetikleyicisi](../connectors/connectors-native-reqres.md) , mantıksal uygulamanızın http isteklerini almasını ve belirli bir şekilde olaylara gerçek zamanlı olarak yanıt vermesini sağlar.

  * [ **Http Web kancası** tetikleyicisi](../connectors/connectors-native-webhook.md) , bu hizmete bir *geri çağırma URL 'si* kaydederek bir hizmet uç noktasına abone olur. Bu şekilde, hizmet yalnızca belirtilen olay gerçekleştiğinde tetikleyiciyi bilgilendirebilir, böylece tetikleyici hizmeti yoklamayı gerektirmez.

Yeni veriler veya bir olay hakkında bildirim aldıktan sonra tetikleyici ateşlenir, yeni bir mantıksal uygulama iş akışı örneği oluşturur ve iş akışındaki eylemleri çalıştırır. İş akışı boyunca tetikleyiciden herhangi bir veriye erişebilirsiniz. Örneğin, "yeni bir tweet üzerinde" tetikleyicisi Tweet içeriğini mantıksal uygulama çalıştırmasına geçirir. Azure Logic Apps kullanmaya başlamak için bu hızlı başlangıç konularını deneyin:

* [Hızlı başlangıç: Azure portal Azure Logic Apps ile ilk otomatik iş akışınızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Hızlı başlangıç: Visual Studio 'Yu kullanarak Azure Logic Apps ile otomatik görevler, süreçler ve iş akışları oluşturma](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Hızlı başlangıç: Visual Studio Code kullanarak otomatik mantıksal uygulama iş akışları oluşturma ve yönetme](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="respond-to-triggers-and-extend-actions"></a>Tetiklere yanıt verme ve eylemleri genişletme

Yayımlanmış bağlayıcılar bulunmayan sistemler ve hizmetler için Logic Apps 'i de genişletebilirsiniz.

* [Özel Tetikleyiciler veya eylemler oluşturma](../logic-apps/logic-apps-create-api-app.md)
* [İş akışı çalıştırmaları için uzun süre çalışan eylemler ayarlama](../logic-apps/logic-apps-create-api-app.md)
* [Web kancaları ile dış olaylara ve eylemlere yanıt verme](../logic-apps/logic-apps-create-api-app.md)
* [HTTP isteklerine zaman uyumlu yanıtlar ile çağrı, tetikleyici veya iç içe geçmiş iş akışları](../logic-apps/logic-apps-http-endpoint.md)
* [Öğretici: Logic Apps ve Power BI ile birlikte bir AI destekli sosyal pano oluşturun](https://aka.ms/logicappsdemo)
* [Video: Twilio SMS Web kancalarına yanıt verin ve bir metin yanıtı gönderin](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Denetim akışı, hata işleme ve günlüğe kaydetme özellikleri

Logic Apps, koşullar, anahtarlar, döngüler ve kapsamlar gibi gelişmiş denetim akışı için zengin yetenekler içerir. Dayanıklı çözümler sağlamak için, iş akışlarınızda hata ve özel durum işleme de uygulayabilirsiniz. İş akışı çalıştırma durumu için bildirim ve tanılama günlükleri için Azure Logic Apps Ayrıca izleme ve uyarılar da sağlar.

* [Koşullu deyimler](../logic-apps/logic-apps-control-flow-conditional-statement.md) ve [Switch deyimlerini](../logic-apps/logic-apps-control-flow-switch-statement.md) temel alan farklı eylemler gerçekleştirin
* [Döngülerle adımları yineleyin veya diziler ve koleksiyonlardaki öğeleri işleyin](../logic-apps/logic-apps-control-flow-loops.md)
* [İşlemleri kapsamlar ile birlikte gruplandırma](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Bir iş akışında yazma hatası ve özel durum işleme](../logic-apps/logic-apps-exception-handling.md)
* [Kullanım örneği: bir sağlık şirketi HL7 FHıR iş akışları için mantıksal uygulama özel durum işlemeyi nasıl kullanır?](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Mevcut Logic Apps için izleme, günlüğe kaydetme ve uyarıları açma](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Mantıksal uygulamalar oluştururken izleme ve tanılama günlüğünü açma](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Logic Apps dağıtma ve yönetme

Visual Studio, Azure DevOps veya diğer kaynak denetimleri ve otomatikleştirilmiş derleme araçlarıyla mantıksal uygulamaları tamamen geliştirebilir ve dağıtabilirsiniz. Bir kaynak şablonundaki iş akışları ve bağımlı bağlantılar için dağıtımı desteklemek amacıyla Logic Apps, Azure kaynak dağıtım şablonlarını kullanır. Visual Studio Araçları otomatik olarak bu şablonları oluşturur. Bu şablonlar, sürüm oluşturma için kaynak denetimine iade edebilirsiniz.

* [Visual Studio ile mantıksal uygulamalar oluşturma ve dağıtma](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Mevcut Logic Apps için izleme, günlüğe kaydetme ve uyarıları açma](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Mantıksal uygulama dağıtımını otomatikleştirme](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Örnek: Azure DevOps 'da Azure Logic Apps Azure Service Bus kuyruklara bağlanma ve Azure Pipelines ile dağıtma](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps 'den Azure Storage hesaplarına bağlanma ve Azure DevOps 'da Azure Pipelines ile dağıtma](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps için bir işlev uygulama eylemi ayarlama ve Azure DevOps 'da Azure Pipelines ile dağıtma](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps 'ten bir tümleştirme hesabına bağlanın ve Azure DevOps 'da Azure Pipelines ile dağıtın](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Bir çalıştırma içindeki içerik türleri, dönüştürmeler ve dönüşümler

Azure Logic Apps [iş akışı tanımlama dilindeki](https://aka.ms/logicappsdocs)birçok işlevi kullanarak birden çok içerik türüne erişebilir, dönüşüm ve dönüştürme yapabilirsiniz. Örneğin, `@json()` ve `@xml()` iş akışı ifadeleriyle bir String, JSON ve XML arasında dönüştürme yapabilirsiniz. Logic Apps altyapısı, hizmetler arasında kayıpsız bir biçimde içerik aktarımını desteklemek için içerik türlerini korur.

* [Logic Apps 'te iş akışı ifadeleri nasıl çalışır](../logic-apps/logic-apps-author-definitions.md)
* `application/xml`, `application/octet-stream`ve `multipart/formdata` gibi [JSON olmayan içerik türlerini işleyin](../logic-apps/logic-apps-content-type.md)
* [Azure Logic Apps için iş akışı tanımı dil şeması](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Diğer tümleştirmeler ve yetenekler

Logic Apps Ayrıca Azure Işlevleri, Azure API Management, Azure uygulama hizmetleri ve özel HTTP uç noktaları gibi birçok hizmet ile tümleştirme sunar. Örneğin, REST ve SOAP.

* [Azure sunucusuz ile gerçek zamanlı sosyal pano oluşturma](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Logic Apps 'ten Azure Işlevleri çağırma](../logic-apps/logic-apps-azure-functions.md)
* [Öğretici: Azure Işlevleri ile mantıksal uygulamaları tetikleme](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Öğretici: Azure Event Grid ve Logic Apps ile sanal makine değişikliklerini Izleme](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Öğretici: Twitter gönderisini çözümlemek için Azure Logic Apps ve Azure bilişsel hizmetler ile tümleşen bir işlev oluşturma](../azure-functions/functions-twitter-email.md)
* [Öğretici: IoT Hub 'ınızı ve posta kutunuzu bağlama Azure Logic Apps IoT uzaktan izleme ve bildirimler](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: mantıksal uygulamalardan SOAP uç noktalarını çağırma](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Uçtan uca senaryolar

* [Teknik İnceleme: Logic Apps gibi Azure hizmetleriyle uçtan uca durum yönetimi tümleştirmesi](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Müşteri hikayeleri

Diğer Azure hizmetleri ve Microsoft ürünleriyle birlikte Azure Logic Apps, [Bu şirketlerin](https://aka.ms/logic-apps-customer-stories) çeviklerinin geliştirilmesine ve karmaşık süreçlerini basitleştirerek, düzenleyerek, otomatikleştirerek ve organize ederek temel işletmelere odaklanmaya nasıl yardımcı olduğunu öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

* [Logic Apps için bağlayıcılar](../connectors/apis-list.md) hakkında bilgi edinin
* [Azure Logic Apps Ile B2B kurumsal tümleştirme senaryoları](../logic-apps/logic-apps-enterprise-integration-overview.md) hakkında bilgi edinin
