---
title: Örnekler & ortak senaryolar
description: Azure Logic Apps için örnekler, yaygın senaryolar, öğreticiler ve izlenecek yollar bulun
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 5f7afe044ab4f782f2028598c2c56e75edf95860
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92317387"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Azure Logic Apps için genel senaryolar, örnekler, öğreticiler ve izlenecek yollar

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) , SQL Server veya SAP 'den Azure bilişsel hizmetler 'e kadar [olan yüzlerce kullanıma yönelik bağlayıcı](../connectors/apis-list.md)sağlayarak farklı Hizmetleri düzenlemenize ve tümleştirmenize yardımcı olur. Logic Apps hizmeti "sunucusuz" olduğundan ölçek veya örnekler hakkında endişelenmeniz gerekmez. Yapmanız gereken tek şey, bir tetikleyiciyle iş akışını ve iş akışının gerçekleştirdiği eylemleri tanımlar. Temel alınan platform ölçek, kullanılabilirlik ve performansı işler. Logic Apps, özellikle birden çok sistem ve hizmet arasında eylemleri koordine etmeniz gereken kullanım durumları ve senaryolar için yararlıdır.

Azure Logic Apps desteklediği yetenekler ve desenler hakkında bilgi edinmenize yardımcı olması için, bu makalede yaygın başlangıç noktaları, örnekler ve senaryolar açıklanmaktadır.

## <a name="common-starting-points-for-logic-app-workflows"></a>Mantıksal uygulama iş akışları için ortak başlangıç noktaları

Her mantıksal uygulama bir [*tetikleyici*](../logic-apps/logic-apps-overview.md#logic-app-concepts)ile başlar ve mantıksal uygulama iş akışınızı başlatan ve bu tetikleyicinin bir parçası olarak herhangi bir verilerde geçiş yapan yalnızca bir tetikleyici. Bazı bağlayıcılar bu türlerde gelen Tetikleyiciler sağlar:

* *Yoklama Tetikleyicileri*: bir hizmet uç noktasını düzenli olarak yeni veriler için denetler. Yeni veriler mevcut olduğunda, tetikleyici verileri girdi olarak kullanarak yeni bir iş akışı örneği oluşturur ve çalıştırır.

* *İtme Tetikleyicileri*: bir hizmet uç noktasındaki verileri dinler ve belirli bir olay gerçekleşene kadar bekler. Olay gerçekleştiğinde, tetikleyici hemen başlatılır, giriş olarak kullanılabilir verileri kullanan yeni bir iş akışı örneği oluşturur ve çalışır.

Yaygın olarak kullanılan Tetikleyicileri tanımlayan örnekler şunlardır:

* *Yoklama* Tetikleyicileri:

  * [ **Yinelenme** tetikleyicisi](../connectors/connectors-native-recurrence.md) , mantıksal uygulamanızı başlatmak için başlangıç tarihi ve saati ile yinelenme süresini ayarlamanıza olanak sağlar. Örneğin, mantıksal uygulamanızı tetiklemek için haftanın günlerini ve günün saatlerini seçebilirsiniz. Daha fazla bilgi için şu konulara bakın:<p>

    * [Azure Logic Apps ile yinelenen otomatik görevleri, işlemleri ve iş akışlarını zamanlama ve çalıştırma](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Öğretici: Azure Logic Apps kullanarak otomatik, zamanlamaya dayalı yinelenen iş akışları oluşturma](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * **Bir e-posta alındığında** tetikleyici, mantıksal uygulamanızın Logic Apps tarafından desteklenen herhangi bir posta sağlayıcısından yeni e-postayı denetlemesini sağlar; örneğin, [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](/connectors/gmail/), [Outlook.com](/connectors/outlook/)vb.

    > [!IMPORTANT]
    > Gmail bağlayıcısını kullanmak istiyorsanız, mantıksal uygulamalarda kısıtlama olmadan yalnızca G-Suite iş hesapları bu bağlayıcıyı kullanabilir. Gmail tüketicisi hesabınız varsa, bu bağlayıcıyı yalnızca belirli Google onaylı hizmetlerle kullanabilirsiniz veya [Gmail Bağlayıcınız ile kimlik doğrulaması için kullanmak üzere bir Google istemci uygulaması oluşturabilirsiniz](/connectors/gmail/#authentication-and-bring-your-own-application). Daha fazla bilgi için, bkz. [Azure Logic Apps Google bağlayıcıları Için veri güvenliği ve gizlilik ilkeleri](../connectors/connectors-google-data-security-privacy-policy.md).

    Daha fazla bilgi için şu konulara bakın:<p>

    * [Öğretici: Azure Logic Apps kullanarak otomatik onay tabanlı iş akışları oluşturma](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Öğretici: Azure Logic Apps, Azure Işlevleri ve Azure Storage kullanarak e-postaları işlemek için görevleri otomatikleştirme](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [ **Http** tetikleyicisi](../connectors/connectors-native-http.md) , http veya https üzerinden bir hizmet uç noktası çağırabilir. Daha fazla bilgi için bkz. [http uç noktalarını kullanarak çağrı, tetikleyici veya iç içe iş akışları](../logic-apps/logic-apps-http-endpoint.md).

* *Gönderim* Tetikleyicileri:

  * [ **İstek** tetikleyicisi](../connectors/connectors-native-reqres.md) gelen https isteklerini alabilir.

  * [ **Http Web kancası** tetikleyicisi](../connectors/connectors-native-webhook.md) , bu hizmete bir *geri çağırma URL 'si* kaydederek bir hizmet uç noktasına abone olur. Bu şekilde, hizmet yalnızca belirtilen olay gerçekleştiğinde tetikleyiciyi bilgilendirebilir, böylece tetikleyici hizmeti yoklamayı gerektirmez.

Belirtilen olay gerçekleştirildikten sonra tetikleyici ateşlenir, bu yeni bir mantıksal uygulama iş akışı örneği oluşturur ve iş akışındaki eylemleri çalıştırır. İş akışı boyunca tetikleyiciden herhangi bir veriye erişebilirsiniz. Örneğin, **Yeni bir tweet tetikleyicisi üzerindeki** Twitter, Tweet içeriğini mantıksal uygulama çalıştırmasına geçirir. Azure Logic Apps kullanmaya başlamak için bu hızlı başlangıç konularını deneyin:

* [Hızlı başlangıç: Azure Logic Apps Azure portal kullanarak ilk otomatikleştirilmiş iş akışınızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Hızlı başlangıç: Azure Logic Apps Visual Studio kullanarak otomatikleştirilmiş görevler, süreçler ve iş akışları oluşturma](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Hızlı başlangıç: Visual Studio Code kullanarak otomatik mantıksal uygulama iş akışları oluşturma ve yönetme](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Denetim akışı ve hata işleme özellikleri

Logic Apps, koşullar, anahtarlar, döngüler ve kapsamlar gibi gelişmiş denetim akışı için zengin yetenekler içerir. Dayanıklı çözümler sağlamak için, iş akışlarınızda hata ve özel durum işleme de uygulayabilirsiniz.

* [Koşullu deyimler](../logic-apps/logic-apps-control-flow-conditional-statement.md) ve [Switch deyimlerini](../logic-apps/logic-apps-control-flow-switch-statement.md) temel alan farklı eylemler gerçekleştirin
* [Döngülerle adımları yineleyin veya diziler ve koleksiyonlardaki öğeleri işleyin](../logic-apps/logic-apps-control-flow-loops.md)
* [İşlemleri kapsamlar ile birlikte gruplandırma](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Bir iş akışına hata ve özel durum işleme ekleme](../logic-apps/logic-apps-exception-handling.md)
* [Kullanım örneği: bir sağlık şirketi HL7 FHıR iş akışları için mantıksal uygulama özel durum işlemeyi nasıl kullanır?](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Özel API 'Ler ve bağlayıcılar oluşturma

Yayımlanmış bağlayıcılar bulunmayan sistemler ve hizmetler için Logic Apps 'i de genişletebilirsiniz.

* [Azure Logic Apps çağrısı için özel API 'Ler oluşturma](../logic-apps/logic-apps-create-api-app.md)
* [Yoklama tetikleyici modelini kullanarak yeni verileri veya olayları düzenli olarak denetleme](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Web kancası tetikleyici modelini kullanarak yeni verileri veya olayları bekleme ve dinleme](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Yoklama eylemi modelini kullanarak uzun süre çalışan görevler gerçekleştirin](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Web kancası eylem modelini kullanarak uzun süre çalışan görevler gerçekleştirme](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Azure Logic Apps özel bağlayıcılar](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>İşletmeden işletmeye (B2B) çözümleri oluşturun

Kuruluşlar arasında kurumsal tümleştirme çözümleri ve sorunsuz iletişim için, Azure Logic Apps ile Enterprise Integration Pack (EıP) kullanarak bu senaryolar için otomatik ölçeklendirilebilir iş akışları oluşturabilirsiniz. Kuruluşlar farklı protokoller ve biçimler kullanmasına karşın, iletileri elektronik olarak değiş tokuş edebilirler. EıP, farklı biçimleri, kuruluşların sistemlerinin AS2, x12, EDIOLGU ve RosettaNet gibi sektör standardı protokolleri işleyebildiğine ve desteklediğinden bir biçime dönüştürür. Bu çözümleri derlemek için, tanımladığınız ve mantıksal uygulama iş akışlarınızla kullandığınız yapıtlar için güvenli, ölçeklenebilir ve yönetilebilir bir kapsayıcı sağlayan ayrı bir Azure kaynağı olan bir tümleştirme hesabı oluşturursunuz. Örneğin, yapıtlar ticari iş ortakları, anlaşmalar, Haritalar, şemalar, sertifikalar ve toplu iş yapılandırması içerir.

* [Genel Bakış: Azure Logic Apps ve Enterprise Integration Pack B2B kurumsal tümleştirme çözümleri](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps'te B2B kurumsal tümleştirmeleri için tümleştirme hesabı oluşturma ve yönetme](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Azure sanal ağ kaynaklarına erişin

Bazen mantıksal uygulamalarınızın ve tümleştirme hesaplarınızın, Azure sanal ağındaki sanal makineler (VM) ve diğer sistemler veya hizmetler gibi güvenli kaynaklara erişmesi gerekir. Bu erişimi ayarlamak için, mantıksal uygulamalarınızı oluşturabileceğiniz ve çalıştırabileceğiniz bir tümleştirme hizmeti ortamı (ıSE) oluşturabilirsiniz. ISE, depolama gibi adanmış kaynakları kullanan Logic Apps hizmetin özel ve yalıtılmış bir örneğidir ve genel, "genel", çok kiracılı Logic Apps hizmetinden ayrı olarak çalışır. Yalıtılmış özel örneğinizi ve genel Global örneği ayırmak, diğer Azure kiracılarının uygulamalarınızın performansı üzerinde sahip olabileceği etkileri azaltmaya yardımcı olur ve bu da "gürültülü komşular" etkisi olarak da bilinir.

* [Genel Bakış: Azure Logic Apps Azure sanal ağ kaynaklarına erişim](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Azure Logic Apps Azure sanal ağlarına bağlanma](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Mantıksal uygulamaları dağıtma, yönetme ve izleme

Visual Studio, Azure DevOps veya diğer kaynak denetimleri ve otomatikleştirilmiş derleme araçlarıyla mantıksal uygulamaları tamamen geliştirebilir ve dağıtabilirsiniz. Bir kaynak şablonundaki iş akışları ve bağımlı bağlantılar için dağıtımı desteklemek amacıyla Logic Apps, Azure kaynak dağıtım şablonlarını kullanır. Visual Studio Araçları otomatik olarak bu şablonları oluşturur. Bu şablonlar, sürüm oluşturma için kaynak denetimine iade edebilirsiniz. İş akışı çalıştırma durumu için bildirim ve tanılama günlükleri için Azure Logic Apps Ayrıca izleme ve uyarılar da sağlar.

### <a name="deploy"></a>Dağıtma

* [Hızlı başlangıç: Azure Logic Apps Visual Studio kullanarak otomatikleştirilmiş görevler, süreçler ve iş akışları oluşturma](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Genel Bakış: mantıksal uygulama dağıtımını otomatikleştirme](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Azure Logic Apps dağıtımını otomatikleştirmek için Azure Resource Manager şablonu oluşturma](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Logic Apps için Azure Resource Manager şablonu oluşturma](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Örnek: Azure DevOps 'da Azure Logic Apps Azure Service Bus kuyruklara bağlanma ve Azure Pipelines ile dağıtma](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps 'den Azure Storage hesaplarına bağlanma ve Azure DevOps 'da Azure Pipelines ile dağıtma](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps için bir işlev uygulama eylemi ayarlama ve Azure DevOps 'da Azure Pipelines ile dağıtma](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps 'ten bir tümleştirme hesabına bağlanın ve Azure DevOps 'da Azure Pipelines ile dağıtın](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps kullanarak Azure Pipelines düzenleme](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Yönetme

* [Visual Studio 'Yu kullanarak mantıksal uygulamaları yönetme](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [B2B kurumsal Tümleştirmeler için tümleştirme hesapları oluşturma ve yönetme](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Azure Logic Apps tümleştirme hizmeti ortamını (ıSE) yönetme](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>İzleyici

* [Azure Logic Apps için çalışma durumunu izleme, tetikleyici geçmişini gözden geçirme ve uyarı ayarlama](../logic-apps/monitor-logic-apps.md)
* [Azure Izleyici günlüklerini ayarlama ve Azure Logic Apps için tanılama verilerini toplama](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Azure Logic Apps'te Azure İzleyici günlüklerini ayarlama ve B2B iletileri için tanılama verilerini toplama](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Azure Logic Apps için Azure Izleyici günlüklerinde izleme ve izleme sorguları görüntüleyin ve oluşturun](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>İçerik türlerini, dönüştürmeleri ve dönüştürmeleri işleme

Azure Logic Apps [iş akışı tanımlama dilindeki](./logic-apps-workflow-definition-language.md)birçok işlevi kullanarak birden çok içerik türüne erişebilir, dönüşüm ve dönüştürme yapabilirsiniz. Örneğin, `@json()` ve `@xml()` iş akışı ifadeleriyle bir STRING, JSON ve XML arasında dönüştürme yapabilirsiniz. Logic Apps altyapısı, hizmetler arasında kayıpsız bir biçimde içerik aktarımını desteklemek için içerik türlerini korur.

* , Ve gibi [Azure Logic Apps içerik türlerini işleme](../logic-apps/logic-apps-content-type.md). `application/` `application/octet-stream``multipart/formdata`
* [Azure Logic Apps ve güç otomatikleştirme için ifadelerde işlevleri kullanmaya yönelik başvuru kılavuzu](../logic-apps/workflow-definition-language-functions-reference.md)
* [Azure Logic Apps için iş akışı tanımı dil şeması](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Diğer tümleştirmeler ve yetenekler

Azure Logic Apps, Azure Işlevleri, Azure API Management, Azure App Service ve özel HTTP uç noktaları (örneğin, REST ve SOAP) gibi birçok hizmet ile tümleşir.

* [Azure Logic Apps Azure Işlevleri 'ni çağırma](../logic-apps/logic-apps-azure-functions.md)
* [Öğretici: Azure Işlevleri ve Azure Service Bus kullanarak Logic Apps 'i çağırma veya tetikleme](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Öğretici: Azure Logic Apps ve Azure Işlevleri ile akış müşteri öngörüleri panosu oluşturma](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Öğretici: Twitter gönderisini çözümlemek için Azure Logic Apps ve Azure bilişsel hizmetler ile tümleşen bir işlev oluşturma](../azure-functions/functions-twitter-email.md)
* [Öğretici: Power BI ve Azure Logic Apps kullanarak AI destekli sosyal pano oluşturma](https://aka.ms/logicappsdemo)
* [Öğretici: Azure Event Grid ve Logic Apps'i kullanarak sanal makine değişikliklerini izleme](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Öğretici: IoT Hub 'ınızı ve posta kutunuzu bağlama Azure Logic Apps IoT uzaktan izleme ve bildirimler](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Azure Logic Apps kullanarak SOAP hizmetlerini çağırma](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>Uçtan uca senaryolar

* [Teknik İnceleme: Logic Apps gibi Azure hizmetleriyle uçtan uca durum yönetimi tümleştirmesi](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Müşteri hikayeleri

Diğer Azure hizmetleri ve Microsoft ürünleriyle birlikte Azure Logic Apps, [Bu şirketlerin](https://aka.ms/logic-apps-customer-stories) çeviklerinin geliştirilmesine ve karmaşık süreçlerini basitleştirerek, düzenleyerek, otomatikleştirerek ve organize ederek temel işletmelere odaklanmaya nasıl yardımcı olduğunu öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

* [Logic Apps için bağlayıcılar](../connectors/apis-list.md) hakkında bilgi edinin
* [Azure Logic Apps Ile B2B kurumsal tümleştirme senaryoları](../logic-apps/logic-apps-enterprise-integration-overview.md) hakkında bilgi edinin