---
title: Azure 'da tümleştirme ve otomasyon platformu seçenekleri
description: 'Tümleştirme görevleri için optimize edilmiş Microsoft bulut hizmetlerini karşılaştırın: Power otomatikleştirmek, Logic Apps, Işlevleri ve Web Işleri.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: e6bd97469a6b7b6c42427e130ac103954377c652
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643373"
---
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>Azure’da doğru tümleştirme ve otomasyon hizmetlerini seçme

Bu makalede aşağıdaki Microsoft bulut hizmetleri karşılaştırılır:

* [Microsoft Power otomatikleştir](https://flow.microsoft.com/) (was Microsoft Flow)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure İşlevleri](https://azure.microsoft.com/services/functions/)
* [Azure App Service Web İşleri](../app-service/webjobs-create.md)

Tüm bu hizmetler, tümleştirme sorunlarını çözebilir ve iş süreçlerini otomatikleştirebilir. Tümü giriş, eylemler, koşullar ve çıkış tanımı yapabilir. Her birini belirli bir zamanlamayla veya tetikleyiciyle çalıştırabilirsiniz. Her hizmetin benzersiz avantajları vardır ve bu makalede farklar açıklanmaktadır. 

Azure Işlevleri ve diğer Azure işlem seçenekleri arasında daha genel bir karşılaştırma arıyorsanız, [Azure işlem hizmeti seçme](/azure/architecture/guide/technology-choices/compute-comparison) ve [mikro hizmetler için bir Azure Işlem seçeneği seçme](/azure/architecture/microservices/design/compute-options)ölçütlerine bakın.

## <a name="compare-microsoft-power-automate-and-azure-logic-apps"></a>Microsoft Power otomatikleştirmesini ve Azure Logic Apps karşılaştırın

Güç otomatikleştirme ve Logic Apps, iş akışları oluşturabileceğiniz *Tasarımcı-ilk* tümleştirme hizmetlerdir. Her iki hizmet de çeşitli SaaS uygulamaları ve kurumsal uygulamalarla tümleştirilir. 

Güç otomatikleştirme, Logic Apps üzerine kurulmuştur. Aynı iş akışı tasarımcısını ve aynı [bağlayıcıları](../connectors/apis-list.md)paylaşır. 

Güç otomatikleştirme, herhangi bir Office çalışanını, geliştiricilerin veya BT üzerinden geçmeden basit tümleştirmeler (örneğin, bir SharePoint belge kitaplığı üzerinde bir onay işlemi) gerçekleştirmesini sağlar. Logic Apps, kurumsal düzeyde Azure DevOps ve güvenlik uygulamalarının gerekli olduğu gelişmiş tümleştirmelere (örneğin, B2B işlemleri) de olanak sağlayabilir. Kurumsal iş akışının zamanla karmaşık hale gelmesi tipik bir durumdur. Buna uygun olarak, ilk olarak bir akışta başlayabilir ve ardından gerektiğinde bir Logic App 'e dönüştürebilirsiniz.

Aşağıdaki tablo, Power otomatikleştirin veya Logic Apps 'nin belirli bir tümleştirme için en iyi şekilde olup olmadığını belirlemenize yardımcı olur:

|  | Power Automate | Logic Apps |
| --- | --- | --- |
| **Kullanıcılar** |Ofis çalışanları, iş kullanıcıları veya SharePoint yöneticileri |Uzman tümleştiriciler ve geliştiriciler, BT uzmanları |
| **Senaryolar** |Self servis |Gelişmiş tümleştirmeler |
| **Tasarım Aracı** |Tarayıcı içi ve mobil uygulama, yalnızca kullanıcı arabirimi |Tarayıcı içi ve [Visual Studio](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), [Cod görünümü](../logic-apps/logic-apps-author-definitions.md) sağlanır |
| **Uygulama yaşam döngüsü yönetimi (ALM)** |Üretim dışı ortamlarda tasarım ve test etme, hazırlık sırasında üretime yükseltme |Azure DevOps: [Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) kaynak denetimi, test, destek, otomasyon ve yönetilebilirlik |
| **Yönetici deneyimi** |Power otomatikleştir ortamlarını ve veri kaybı önleme (DLP) ilkelerini yönetme, lisanslama izleme: [Yönetim Merkezi](https://admin.flow.microsoft.com) |Kaynak gruplarını, bağlantıları, erişim yönetimini ve günlüğe kaydetmeyi yönetme: [Azure Portal](https://portal.azure.com) |
| **Güvenlik** |Güvenlik Denetim günlüklerini, DLP 'yi, hassas veriler için [bekleyen şifrelemeyi](https://wikipedia.org/wiki/Data_at_rest#Encryption) Microsoft 365 |Azure güvenlik güvencesi: [Azure güvenliği](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/), [Denetim günlükleri](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Azure İşlevleri ve Azure Logic Apps karşılaştırması

İşlevler ve Logic Apps, sunucusuz iş yüklerine olanak tanıyan Azure hizmetleridir. Azure Işlevleri sunucusuz bir işlem hizmetidir, ancak Azure Logic Apps sunucusuz iş akışları sağlar. Her ikisi *de karmaşık düzenlemeler* oluşturabilir. Düzenleme, Logic Apps’te karmaşık bir görevin gerçekleştirilmesi için yürütülen, *eylemler* olarak adlandırılan işlevlerin veya adımların bir koleksiyonudur. Örneğin, bir dizi siparişi işlemek için, bir işlevin birçok örneğini paralel olarak yürütebilir, tüm örneklerin bitmesini bekleyebilir ve sonra toplama üzerinde bir sonuç hesaplayan bir işlev yürütebilirsiniz.

Azure İşlevleri için düzenlemeleri kod yazarak ve [Dayanıklı İşlevler uzantısını](durable/durable-functions-overview.md) kullanarak geliştirirsiniz. Logic Apps için düzenlemeleri, GUI kullanarak veya yapılandırma dosyalarını düzenleyerek oluşturursunuz.

Düzenleme oluşturduğunuzda, mantıksal uygulamalardan işlev çağırdığınızda ve işlevlerden mantıksal uygulama çağırdığınızda hizmetleri karıştırıp eşleştirebilirsiniz. Hizmet özelliklerine veya kişisel tercihinize göre her düzenlemenin nasıl oluşturulacağını seçin. Aşağıdaki tabloda bunlar arasındaki bazı önemli farklılıklar listelenmiştir:

|  | Dayanıklı İşlevler | Logic Apps |
| --- | --- | --- |
| **Geliştirme** | Koda öncelik veren (kesinlik temelli) | Tasarımcıya öncelik veren (bildirim temelli) |
| **Bağlantı** | [Bir düzine yerleşik bağlama türü hakkında](functions-triggers-bindings.md#supported-bindings), özel bağlamalar için kod yazın | [Büyük bağlayıcılar koleksiyonu](../connectors/apis-list.md), [B2B senaryoları için Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md), [özel bağlayıcılar oluşturma](../logic-apps/custom-connector-overview.md) |
| **Eylemler** | Her etkinlik bir Azure işlevidir; eylem işlevleri için kod yazma |[Hazır eylemlerden oluşan büyük koleksiyon](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **İzleme** | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Azure izleyici günlükleri](../logic-apps/monitor-logic-apps.md)|
| **Yönetim** | [REST API](durable/durable-functions-http-api.md), [Visual Studio](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](/rest/api/logic/), [PowerShell](/powershell/module/az.logicapp), [Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) |
| **Yürütme bağlamı** | [Yerel olarak](functions-runtime-overview.md) veya bulutta çalışabilir | Yalnızca bulutta çalışır|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>İşlevler Web İşleri karşılaştırması

Azure İşlevleri gibi, WebJobs SDK ile Azure App Service WebJobs da geliştiriciler için tasarlanmış, *koda öncelik veren* bir tümleştirme hizmetidir. Her ikisi de [Azure App Service](../app-service/overview.md) üzerinde derlenmiş olup [source control integration](../app-service/deploy-continuous-deployment.md), [authentication](../app-service/overview-authentication-authorization.md) ve [monitoring with Application Insights integration](functions-monitoring.md) gibi özellikleri destekler.

### <a name="webjobs-and-the-webjobs-sdk"></a>Web İşleri ve Web İşleri SDK’sı

Bir komut dosyası veya kodu bir App Service Web uygulaması bağlamında çalıştırmak için App Service *WebJobs* özelliğini kullanabilirsiniz. *WebJobs SDK*, Azure hizmetlerine yanıt olarak yazdığınız kodu kolaylaştıran WebJobs için tasarlanmış bir çerçevedir. Örneğin, bir küçük resim oluşturarak Azure depolamada bir görüntü blobu oluşturmaya yanıt verebilirsiniz. WebJobs SDK, WebJob’a dağıtabileceğiniz bir .NET konsol uygulaması olarak çalıştırılır. 

WebJobs ve WebJobs SDK birlikte en iyi şekilde çalışır; ancak WebJobs’ı WebJobs SDK olmadan kullanabilirsiniz; bunun tersi de olabilir. Bir Web İşi, App Service korumalı alanında çalışan herhangi bir programı veya betiği çalıştırabilir. Web İşleri SDK konsolu uygulaması, şirket içi sunucular gibi konsol uygulamalarının çalıştığı her yerde çalışabilir.

### <a name="comparison-table"></a>Karşılaştırma tablosu

Azure İşlevleri, WebJobs SDK’da derlendiğinden diğer Azure hizmetlerine yönelik aynı bağlantıların ve olay tetikleyicilerinin birçoğunu paylaşır. Web Işleri SDK 'Sı ile Azure Işlevleri ve Web Işleri arasında seçim yaparken göz önünde bulundurmanız gereken bazı etmenler aşağıda verilmiştir:

|  | İşlevler | WebJobs SDK ile WebJobs |
| --- | --- | --- |
|**[Otomatik ölçeklendirmeyle](event-driven-scaling.md) [sunucusuz uygulama modeli](https://azure.microsoft.com/solutions/serverless/)**|✔||
|**[Tarayıcıda geliştirin ve test edin](./functions-get-started.md)** |✔||
|**[Kullanım başına ödeme fiyatlandırması](consumption-plan.md)**|✔||
|**[Logic Apps ile tümleştirme](functions-twitter-email.md)**|✔||
| **Tetikleyici olayları** |[Zamanlayıcı](functions-bindings-timer.md)<br>[Azure Depolama kuyrukları ve blobları](functions-bindings-storage-blob.md)<br>[Azure Service Bus kuyrukları ve konuları](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Zamanlayıcı](functions-bindings-timer.md)<br>[Azure Depolama kuyrukları ve blobları](functions-bindings-storage-blob.md)<br>[Azure Service Bus kuyrukları ve konuları](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Dosya sistemi](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| **Desteklenen diller**  |C#<br>F#<br>JavaScript<br>Java<br>Python<br>PowerShell |C#<sup>1</sup>|
|**Paket yöneticileri**|NPM ve NuGet|NuGet<sup>2</sup>|

<sup>1</sup> Web Işleri (WEBJOBS SDK olmadan) C#, Java, JavaScript, Bash,. cmd,. bat, POWERSHELL, php, TypeScript, Python ve daha fazlasını destekler. Bu kapsamlı bir liste değildir. WebJob, App Service korumalı alanında çalıştırılabilen herhangi bir programı veya betiği çalıştırabilir.

<sup>2</sup> Web Işleri (WEBJOBS SDK olmadan) NPM ve NuGet 'yi destekler.

### <a name="summary"></a>Özet

Azure Işlevleri Azure App Service WebJobs 'dan daha fazla geliştirici verimliliği sunar. Ayrıca programlama dilleri, geliştirme ortamları, Azure hizmet tümleştirmesi ve fiyatlandırma için daha fazla seçenek sunar. Çoğu senaryo için bu en iyi seçenektir.

WebJobs’ın en iyi seçenek olduğu iki senaryo aşağıda verilmiştir:

* Olayları dinleyen kod (`JobHost` nesnesi) üzerinde daha fazla denetime ihtiyacınız vardır. İşlevler, [host.json](functions-host-json.md) dosyasında `JobHost` davranışını özelleştirmek için sınırlı sayıda yöntem sunar. Bazen bir JSON dosyasındaki dize tarafından belirtilemeyen şeyler yapmanız gerekir. Örneğin, yalnızca WebJobs SDK, Azure Depolama için özel bir yeniden deneme ilkesi yapılandırmanıza olanak sağlar.
* Kod parçacıklarını çalıştırmak istediğiniz App Service uygulamanız var ve bunları aynı Azure DevOps ortamında birlikte yönetmek istiyorsunuz.

Azure veya üçüncü taraf hizmetleri tümleştirmek için kod parçacıklarını çalıştırmak istediğiniz diğer durumlarda, WebJobs SDK ile WebJobs üzerinden Azure İşlevleri’ni seçin.

<a name="together"></a>

## <a name="power-automate-logic-apps-functions-and-webjobs-together"></a>Power otomatikleştirmek, Logic Apps, Işlevleri ve Web Işleri birlikte

Bu hizmetlerden yalnızca birini seçmeniz gerekmez. Bunlar birbirleriyle ve dış hizmetler ile tümleşir.

Akış bir mantıksal uygulamayı çağırabilir. Mantıksal uygulama bir işlevi çağırabilir ve işlev de bir mantıksal uygulamayı çağırabilir. Örneğin, bkz. [Azure Logic Apps ile tümleşen bir işlev oluşturma](functions-twitter-email.md).

Güç otomatikleştir, Logic Apps ve Işlevleri arasındaki tümleştirme, zaman içinde gelişmeye devam eder. Bir hizmette bir şey oluşturabilir ve bunu diğer hizmetlerde kullanabilirsiniz.

Aşağıdaki bağlantıları kullanarak tümleştirme hizmetleri hakkında daha fazla bilgi edinebilirsiniz:

* [Tümleştirme senaryoları için Azure İşlevleri ve Azure App Service'ten yararlanma - Christopher Anderson](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Tümleştirmeler Basitleşti - Charles Lamanna](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Canlı Logic Apps web yayını](https://aka.ms/logicappslive)
* [Power otomatikleştirmek sık sorulan sorular](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Sonraki adımlar

İlk akışınızı, mantıksal uygulamanızı veya işlev uygulamanızı oluşturarak başlayın. Aşağıdaki bağlantılardan birini seçin:

* [Power Automate kullanmaya başlama](/power-automate/getting-started)
* [Mantıksal uygulama oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [İlk Azure işlevinizi oluşturma](./functions-get-started.md)