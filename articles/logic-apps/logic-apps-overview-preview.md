---
title: Azure Logic Apps Önizleme sürümüne genel bakış
description: Azure Logic Apps Preview, uygulamaları, verileri, hizmetleri ve sistemleri kurumsal düzeyde senaryolar için en az kodla tümleştiren otomatik, tek kiracılı, durum bilgisiz ve durum bilgisi olmayan iş akışları oluşturmaya yönelik bir bulut çözümüdür.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 27889e8309c0efaf1e2869fc39d099f38f64f7c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764828"
---
# <a name="overview-azure-logic-apps-preview"></a>Genel Bakış: Azure Logic Apps Önizleme

> [!IMPORTANT]
> Bu özellik genel önizleme aşamasındadır, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Logic Apps önizlemesi sayesinde, yeni **mantıksal uygulama (Önizleme)** kaynak türü ile tek kiracılı mantıksal uygulamalar oluşturup çalıştırarak, uygulamalar, veriler, bulut hizmetleri ve sistemler arasında Otomasyon ve tümleştirme çözümleri oluşturabilirsiniz. Bu tek kiracılı mantıksal uygulama türünü kullanarak, yalnızca Azure değil de Docker kapsayıcıları dahil olmak üzere çeşitli barındırma ortamlarında taşınabilirlik, daha iyi performans ve çalıştırma esnekliği sağlayan, yeniden tasarlanan Azure Logic Apps Önizleme çalışma zamanı tarafından desteklenen birden fazla [ *durum* *bilgisiz ve olmayan* iş akışı](#stateful-stateless) oluşturabilir.

Bu nasıl mümkün? Yeniden tasarlanan çalışma zamanı [Azure işlevleri genişletilebilirlik modelini](../azure-functions/functions-bindings-register.md) kullanır ve Azure işlevleri çalışma zamanı 'nda bir uzantı olarak barındırılır. Bu mimari, tek kiracılı mantıksal uygulama türünü Azure Işlevlerinin çalıştığı her yerde çalıştırabilmeniz anlamına gelir. Yeniden tasarlanan çalışma zamanını neredeyse tüm ağ topolojisinde barındırabilir ve iş akışlarınız için gereken gerekli iş yükünü işlemek için kullanılabilir işlem boyutunu seçebilirsiniz. Daha fazla bilgi için bkz. [Azure Işlevlerine giriş](../azure-functions/functions-overview.md) ve [Azure işlevleri Tetikleyicileri ve bağlamaları](../azure-functions/functions-triggers-bindings.md).

[Azure Portal başlatarak](create-stateful-stateless-workflows-azure-portal.md) veya [Azure Logic Apps (önizleme) uzantısıyla Visual Studio Code bir proje oluşturarak](create-stateful-stateless-workflows-visual-studio-code.md) **mantıksal uygulama (Önizleme)** kaynağını oluşturabilirsiniz. Ayrıca, Visual Studio Code ' de, iş akışlarınızı geliştirme ortamınızda oluşturup *yerel olarak çalıştırabilirsiniz* . Portalı veya Visual Studio Code kullanmanıza bakılmaksızın, tek kiracılı mantıksal uygulama türünü aynı barındırma ortamları türlerinde dağıtabilir ve çalıştırabilirsiniz.

Bu genel bakışta aşağıdaki alanlarda yer verilmiştir:

* [Azure Logic Apps önizlemesi, Azure Logic Apps çok kiracılı ortam ve tümleştirme hizmeti ortamı arasındaki farklar](#preview-differences).

* [İç içe geçmiş ve durum bilgisi olmayan iş akışları](#nested-behavior)arasındaki davranış farklılıkları da dahil [durum bilgisiz ve durum bilgisi olmayan](#stateful-stateless)

* [Bu genel önizlemede bulunan yetenekler](#public-preview-contents).

* [Fiyatlandırma modelinin nasıl çalıştığı](#pricing-model).

* [Değiştirilmiş, sınırlı, kullanılamayan veya desteklenmeyen yetenekler](#limited-unavailable-unsupported).

* [Azure Logic Apps Önizlemedeki sınırlar](#limits).

Daha fazla bilgi için şu diğer konuları gözden geçirin:

* [Azure Logic Apps her yerde çalıştırma-çalışma zamanı derinlemesine bakış](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps genel önizleme bilinen sorunları (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Azure Logic Apps önizlemesi nasıl farklıdır?

Azure Logic Apps Preview çalışma zamanı, [Azure işlevleri](../azure-functions/functions-overview.md) genişletilebilirliği kullanır ve Azure işlevleri çalışma zamanı 'nda bir uzantı olarak barındırılır. Bu mimari, tek kiracılı mantıksal uygulama türünü Azure Işlevlerinin çalıştığı her yerde çalıştırabilmeniz anlamına gelir. Azure Logic Apps Preview çalışma zamanını istediğiniz neredeyse her türlü ağ topolojisinde barındırabilir ve iş akışınız için gereken gerekli iş yükünü işlemek için kullanılabilir işlem boyutunu seçebilirsiniz. Azure Işlevleri genişletilebilirliği hakkında daha fazla bilgi için bkz. [WebJobs SDK: özel giriş ve çıkış bağlamaları oluşturma](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings).

Bu yeni yaklaşımla, Azure Logic Apps Preview çalışma zamanı ve iş akışlarınız uygulamanızın bir parçası olarak birlikte paketleyebilir. Bu özellik, yalnızca yapıları barındırma ortamına kopyalayarak ve uygulamanızı başlatarak iş akışlarınızı dağıtmanıza ve çalıştırmanıza olanak tanır. Bu yaklaşım ayrıca, üretim ortamlarında değişiklik dağıtmadan önce gerekli testleri ve doğrulamaları çalıştırmaya yönelik iş akışı projelerinin çevresinde dağıtım işlem hatları oluşturmak için daha standartlaştırılmış bir deneyim sağlar. Daha fazla bilgi için bkz. [Azure Logic Apps her yerde çalıştırma-çalışma zamanı derinlemesine](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)bakış.

Aşağıdaki tabloda, çalıştığı ortama göre iş akışlarının kaynakları paylaşma yöntemiyle ilgili farklılıklar kısaca özetlenmektedir. Limitlere yönelik farklılıklar için [Azure Logic Apps önizlemede sınırlara](#limits)bakın.

| Ortam | Kaynak paylaşımı ve tüketimi |
|-------------|----------------------------------|
| Azure Logic Apps (çok kiracılı) | *Birden çok kiracının içindeki müşterilerden alınan* iş akışları aynı işleme (işlem), depolama, ağ vb. paylaşır. |
| Azure Logic Apps (Önizleme, tek kiracılı) | *Aynı mantıksal uygulamadaki iş akışları ve tek bir kiracı* aynı işleme (işlem), depolama, ağ ve benzeri bir şekilde paylaşır. |
| Tümleştirme hizmeti ortamı (önizlemede kullanılamaz) | *Aynı ortamdaki* iş akışları aynı işleme (işlem), depolama, ağ vb. paylaşır. |
|||

Bu arada, Azure portal çok kiracılı mantıksal uygulama türünü ve Visual Studio Code çok kiracılı Azure Logic Apps uzantısını kullanarak de oluşturabilirsiniz. Geliştirme deneyimleri, çok kiracılı ve tek kiracılı mantıksal uygulama türleri arasında farklılık gösterir, ancak Azure aboneliğiniz her iki türü de içerebilir. Azure aboneliğinizde dağıtılan tüm mantıksal uygulamaları görüntüleyebilir ve bunlara erişebilirsiniz, ancak uygulamalar kendi kategorileriyle ve bölümlerinde düzenlenir.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Durum bilgisi olan ve durumsuz iş akışları

Tek kiracılı mantıksal uygulama türü ile, bu iş akışı türlerini aynı mantıksal uygulama içinde oluşturabilirsiniz:

* *Durum Bilgisi Olan*

  Önceki olaylardaki verileri tutmanız, gözden geçirmeniz veya başvuru yapmanız gerektiğinde durum bilgisi olan iş akışları oluşturun. Bu iş akışları, her bir eylem için giriş ve çıkışları ve bunların durumlarını dış depolamaya kaydederek her çalıştırma tamamlandıktan sonra olası çalıştırma ayrıntılarını ve geçmişini gözden geçirmeyi sağlar. Durum bilgisi olan iş akışları, kesintiler meydana geliyorsa yüksek dayanıklılık sağlar. Hizmetler ve sistemler geri yüklendikten sonra, kesilen çalıştırmaları kaydedilen durumdan yeniden oluşturabilir ve tamamlanması için iş akışlarını yeniden çalıştırabilirsiniz. Durum bilgisi olan iş akışları bir yıla kadar çalışmaya devam edebilir.

* *Durum bilgisi olmayan*

  Daha sonra gözden geçirmek üzere dış depolamada bulunan önceki olaylara ait verileri kaydetmeniz, gözden geçirmeniz veya başvuru yapmanız gerekmiyorsa durum bilgisiz iş akışları oluşturun. Bu iş akışları, bu verileri dış depolamaya aktarmak yerine, her eylem için giriş ve çıkışları *yalnızca belleğe* kaydeder. Sonuç olarak, durum bilgisiz iş akışlarının genellikle 5 dakikadan fazla olmayan kısa çalıştırmaları, daha hızlı yanıt süreleriyle daha hızlı performans, daha yüksek aktarım hızı ve kullanım ayrıntıları ve geçmişi dış depolamada tutulmadığı için daha hızlı çalışma maliyetleri vardır. Ancak, kesintiler gerçekleşiyorsa, kesintiye uğramayan çalıştırmalar otomatik olarak geri yüklenmez, bu sayede çağıranın kesintiye uğramış çalıştırmaları el ile yeniden göndermelidir. Bu iş akışları yalnızca zaman uyumlu şekilde çalışabilir.

  Daha kolay hata ayıklama için, performans üzerinde bir etkisi olan, durum bilgisiz iş akışı için çalıştırma geçmişini etkinleştirebilir ve sonra işiniz bittiğinde çalıştırma geçmişini devre dışı bırakabilirsiniz. Daha fazla bilgi için, bkz. [Visual Studio Code durum bilgisiz ve durum bilgisi olmayan iş akışları oluşturma](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) veya [Azure Portal durum bilgisiz ve durum](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)bilgisi

  > [!NOTE]
  > Durum bilgisiz iş akışları Şu anda yalnızca Azure 'da dağıtılan ve tetikleyici olmayan [yönetilen bağlayıcılar](../connectors/managed.md)için *eylemleri* desteklemektedir. İş akışınızı başlatmak için [yerleşik istek, Event Hubs veya Service Bus tetikleyicisi](../connectors/built-in.md)' ni seçin. Bu Tetikleyiciler Azure Logic Apps Preview çalışma zamanında yerel olarak çalışır. Sınırlı, kullanılamayan veya desteklenmeyen Tetikleyiciler, Eylemler ve bağlayıcılar hakkında daha fazla bilgi için bkz. [değiştirilen, sınırlı, kullanılamayan veya desteklenmeyen yetenekler](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Durum bilgisiz ve durum bilgisi olmayan iş akışlarıyla iç içe geçmiş

[İstek tetikleyicisi](../connectors/connectors-native-reqres.md), [http Web kancası tetikleyicisi](../connectors/connectors-native-webhook.md)veya [apiconnectionweb kancası türüne](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) sahip yönetilen bağlayıcı tetikleyicilerini veya https Istekleri alabileceği şekilde, aynı **Logic App (Önizleme)** kaynağında bulunan diğer iş akışlarından [çağrılabilir bir iş akışı](../logic-apps/logic-apps-http-endpoint.md) sağlayabilirsiniz.

Ana iş akışı alt iş akışını çağırdığında, iç içe iş akışlarının izleyebildiğiniz davranış desenleri aşağıda verilmiştir:

* Zaman uyumsuz yoklama kriteri

  Üst öğe, ilk çağrısına yanıt beklemez, ancak alt çalışmayı bitirene kadar alt öğenin çalışma geçmişini sürekli olarak denetler. Varsayılan olarak, durum bilgisi olan iş akışları, [istek zaman aşımı sınırlarını](../logic-apps/logic-apps-limits-and-config.md)aşabileceğinden uzun süre çalışan alt iş akışları için ideal olan bu kalıbı izler.

* Zaman uyumlu model ("ateş ve unut")

  Alt öğe, hemen bir yanıt döndürerek çağrıyı onaylar `202 ACCEPTED` ve üst öğe, alt öğeden gelen sonuçları beklemeden bir sonraki eyleme devam eder. Bunun yerine, alt öğe çalışmayı bitirdiğinde sonuçları alır. Yanıt eylemi içermeyen, durum bilgisi olan alt iş akışları her zaman zaman uyumlu düzende takip eder. Alt durum bilgisi olan iş akışlarında, çalışma geçmişi gözden geçirmeniz için kullanılabilir.

  Bu davranışı etkinleştirmek için iş akışının JSON tanımında `operationOptions` özelliğini olarak ayarlayın `DisableAsyncPattern` . Daha fazla bilgi için bkz. [tetikleyici ve eylem türleri-işlem seçenekleri](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Tetikleme ve bekleme

  Alt durum bilgisi olmayan bir iş akışı için üst öğe, alt öğeden sonuçları döndüren bir yanıt bekler. Bu model, bir alt iş akışını çağırmak için yerleşik [http tetikleyicisi veya eylemini](../connectors/connectors-native-http.md) kullanmayla benzerdir. Yanıt eylemi içermeyen alt durum bilgisiz iş akışları hemen bir `202 ACCEPTED` yanıt döndürür, ancak üst öğe sonraki eyleme geçmeden önce alt öğenin bitmesini bekler. Bu davranışlar yalnızca alt durum bilgisiz iş akışları için geçerlidir.

Bu tablo, üst ve alt öğenin durum bilgisiz, durumsuz veya karışık iş akışı türleri olup olmadığına bağlı olarak alt iş akışının davranışını belirtir:

| Üst iş akışı | Alt iş akışı | Alt davranış |
|-----------------|----------------|----------------|
| Durum Bilgisi Olan | Durum Bilgisi Olan | Ayar ile zaman uyumsuz veya zaman uyumlu `"operationOptions": "DisableAsyncPattern"` |
| Durum Bilgisi Olan | Durum bilgisi olmayan | Tetikleme ve bekleme |
| Durum bilgisi olmayan | Durum Bilgisi Olan | Zaman Uyumlu |
| Durum bilgisi olmayan | Durum bilgisi olmayan | Tetikleme ve bekleme |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Özellikler

Azure Logic Apps önizleme birçok geçerli ve ek özellik içerir, örneğin:

* Hizmet olarak yazılım (SaaS) ve hizmet olarak platform (PaaS) uygulamaları ve hizmetleri için [400 + bağlayıcılarından](/connectors/connector-reference/connector-reference-logicapps-connectors) Logic Apps ve iş akışları oluşturun ve şirket içi sistemler için bağlayıcı oluşturun.

  * Bazı yönetilen bağlayıcılar artık Azure Logic Apps Preview çalışma zamanında yerel olarak çalıştırılan Istek tetikleyicisi ve HTTP eylemi gibi yerleşik Tetikleyiciler ve eylemlere benzer şekilde çalışan yerleşik sürümler olarak sunulmaktadır. Örneğin, bu yeni yerleşik bağlayıcılar Azure Service Bus, Azure Event Hubs, SQL Server ve MQ ' i içerir.

    > [!NOTE]
    > Yerleşik SQL Server Bağlayıcısı için, yalnızca **sorgu yürütme** eylemi, Şirket [içi veri ağ geçidine](logic-apps-gateway-connection.md)gerek duymadan doğrudan Azure sanal ağlarına bağlanabilir.

  * [Önizleme sürümünün genişletilebilirlik çerçevesini](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)kullanarak ihtiyacınız olan herhangi bir hizmet için kendi yerleşik bağlayıcılarınızı oluşturun. Azure Service Bus ve SQL Server gibi yerleşik bağlayıcılara benzer ancak önizleme için şu anda desteklenmeyen [özel bağlayıcıların](../connectors/apis-list.md#custom-apis-and-connectors) aksine, bu bağlayıcılar daha yüksek aktarım hızı, düşük gecikme süresi, yerel bağlantı sağlar ve Önizleme çalışma zamanı ile aynı işlemde yerel olarak çalışır.

    Yazma özelliği şu anda yalnızca Visual Studio Code ' de kullanılabilir, ancak varsayılan olarak etkinleştirilmemiştir. Bu bağlayıcıları oluşturmak için, [projenizi uzantı temelinde (Node.js) NuGet paket tabanlı (.net) olarak değiştirin](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring). Daha fazla bilgi için bkz. [her yerde yerleşik bağlayıcı genişletilebilirliği çalıştırma Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

  * Bir tümleştirme hesabı olmadan likit Işlemler ve XML Işlemleri için B2B eylemlerini kullanabilirsiniz. Bu eylemleri kullanmak için, Azure portal ilgili işlemler aracılığıyla karşıya yükleyebileceğiniz sıvı haritaları, XML haritaları veya XML şemaları olmalıdır veya ilgili **haritalar** ve **şemalar** klasörlerini kullanarak Visual Studio Code projenizin **yapıt** klasörüne ekleyebilirsiniz.

  * Azure Logic Apps hizmeti bu mantıksal uygulamaların bulut bağlantısı çalışma zamanı uç noktasına istek göndermek için kullanabileceği paylaşılan erişim Imzası (SAS) bağlantı dizeleri oluşturduğundan, her yerde çalışabilen Logic Apps oluşturun ve dağıtın. Logic Apps hizmeti bu bağlantı dizelerini diğer uygulama ayarlarıyla kaydeder; böylece Azure 'da dağıtırken bu değerleri Azure Key Vault kolayca depolayabilirler.

    > [!NOTE]
    > Varsayılan olarak, **mantıksal uygulama (Önizleme)** kaynağında, çalışma zamanında bağlantıların kimliğini doğrulamak için [sistem tarafından atanan yönetilen kimlik](../logic-apps/create-managed-service-identity.md) otomatik olarak etkinleştirilir. Bu kimlik, bağlantı oluştururken kullandığınız kimlik doğrulama kimlik bilgilerinden veya bağlantı dizesinden farklıdır. Bu kimliği devre dışı bırakırsanız, bağlantılar çalışma zamanında çalışmaz. Bu ayarı görüntülemek için, mantıksal uygulamanızın menüsünde, **Ayarlar** altında **kimlik**' i seçin.

* Yalnızca bellekte çalışan, durum bilgisiz iş akışlarıyla mantıksal uygulamalar oluşturun, böylece eylemler arasındaki çalıştırma geçmişi ve verileri dış depolamada kalıcı hale getirilerek daha hızlı bir şekilde yanıt verir. İsteğe bağlı olarak, daha kolay hata ayıklama için çalıştırma geçmişini etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [durum bilgisi içermeyen mantıksal uygulamalar](#stateful-stateless).

* Visual Studio Code geliştirme ortamında mantıksal uygulamalarınızı ve bunların iş akışlarını yerel olarak çalıştırın, test edin ve hatalarını ayıklayın.

  Mantıksal uygulamanızı çalıştırmadan ve test etmeden önce, bir iş akışı için dosyadaki **workflow.js** kesme noktaları ekleyerek ve kullanarak hata ayıklamayı daha kolay hale getirebilirsiniz. Ancak, kesme noktaları yalnızca şu anda eylemler için desteklenir, Tetikleyiciler değildir. Daha fazla bilgi için, bkz. [Visual Studio Code durum bilgisi olan ve durumsuz iş akışları oluşturma](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* Mantıksal uygulamaları ve bunların iş Visual Studio Code akışlarını Azure ve [Docker Kapsayıcıları](/dotnet/core/docker/introduction)gibi çeşitli barındırma ortamlarına doğrudan yayımlayın veya dağıtın.

* Azure aboneliğiniz ve mantıksal uygulama ayarlarınız tarafından desteklenerek [Application Insights](../azure-monitor/app/app-insights-overview.md) kullanarak mantıksal uygulamanız için tanılama günlüğü ve izleme özelliklerini etkinleştirin.

* Azure [Işlevleri Premium planını](../azure-functions/functions-premium-plan.md)kullanarak mantıksal uygulamalarınızı oluştururken ve dağıtırken Azure işlevlerine benzer şekilde, Azure sanal ağlarla özel olarak bağlanma ve tümleştirme gibi erişim ağı özellikleri. Daha fazla bilgi için şu konuları gözden geçirin:

  * [Azure İşlevleri ağ seçenekleri](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps önizlemesiyle her yerde ağ olasılıklarını çalıştırmak Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Tek kiracılı **mantıksal uygulama (Önizleme)** kaynağında bireysel iş akışları tarafından kullanılan yönetilen bağlantılar için erişim anahtarlarını yeniden oluşturun. Bu görev için, [çok kiracılı **Logic Apps** kaynağıyla aynı adımları izleyin](logic-apps-securing-a-logic-app.md#regenerate-access-keys), mantıksal uygulama kaynak düzeyi değil ayrı iş akışı düzeyinde.

* Çok kiracılı tasarımcı ile aynı adımları izleyerek, tek kiracılı tasarımcıya paralel dallar ekleyin.

Daha fazla bilgi için bkz. GitHub 'daki [değişen, sınırlı, kullanılamayan ve desteklenmeyen yetenekler](#limited-unavailable-unsupported) ve [Logic Apps genel önizleme bilinen sorunlar sayfası](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Fiyatlandırma modeli

Azure portal veya Visual Studio Code dağıtım için tek kiracılı mantıksal uygulama türünü oluşturduğunuzda, mantıksal uygulamanızın kullanması için [App Service veya Premium](../azure-functions/functions-scale.md)bir barındırma planı seçmelisiniz. Bu plan, mantıksal uygulamanızı çalıştırmak için geçerli olan fiyatlandırma modelini belirler. App Service planını seçerseniz, ayrıca bir [fiyatlandırma katmanı](../app-service/overview-hosting-plans.md)da seçmeniz gerekir.

*Durum bilgisi olan* iş akışları [harici depolama](../azure-functions/storage-considerations.md#storage-account-requirements)kullanır, bu nedenle [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/) Azure Logic Apps önizlemesi çalışma zamanının gerçekleştirdiği depolama işlemleri için geçerlidir. Örneğin, kuyruklar zamanlama için kullanılır, ancak tablolar ve Bloblar iş akışı durumlarını depolamak için kullanılır.

> [!NOTE]
> Genel Önizleme sırasında App Service Logic apps çalıştırmak, seçtiğiniz planın üzerine *ek* ücret vermez.

Tek kiracılı kaynak türüne uygulanan fiyatlandırma modelleri hakkında daha fazla bilgi için şu konuları gözden geçirin:

* [Azure İşlevleri’ni ölçeklendirme ve barındırma](../azure-functions/functions-scale.md)
* [Azure App Service bir uygulamayı büyütme](../app-service/manage-scale-up.md)
* [Azure Işlevleri fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/functions/)
* [App Service fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/app-service/)
* [Azure depolama fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Değiştirilmiş, sınırlı, kullanılamayan veya desteklenmeyen yetenekler

Azure Logic Apps önizlemede, bu yetenekler değiştirilmiştir veya şu anda sınırlı, kullanılamaz veya desteklenmez:

* **Işletim sistemi desteği**: şu anda Visual Studio Code TASARıMCı Linux işletim sisteminde çalışmıyor, ancak yine de Linux tabanlı sanal makinelere Logic Apps Preview çalışma zamanını kullanan Logic Apps dağıtımı yapabilirsiniz. Şimdilik, Logic Apps 'i Windows veya macOS üzerinde Visual Studio Code oluşturabilir ve ardından Linux tabanlı bir sanal makineye dağıtabilirsiniz.

* **Tetikleyiciler ve eylemler**: yerleşik Tetikleyiciler ve eylemler Azure Logic Apps Önizleme çalışma zamanında yerel olarak çalışır, ancak yönetilen bağlayıcılar Azure 'da dağıtılır. Bazı yerleşik Tetikleyiciler, kayan pencere ve toplu Işlem gibi kullanılamaz.

  İş akışınızı başlatmak için [yerleşik yinelenme, istek, http, http Web kancası, Event Hubs veya Service Bus tetikleyicisini](../connectors/apis-list.md)kullanın. Tasarımcıda yerleşik Tetikleyiciler ve eylemler **yerleşik** sekmesinde görünür, ancak yönetilen bağlayıcı Tetikleyicileri ve eylemler **Azure** sekmesinin altında görüntülenir.

  > [!NOTE]
  > Visual Studio Code yerel olarak çalıştırmak için, Web kancası tabanlı tetikleyiciler ve eylemler ek kurulum gerektirir. Daha fazla bilgi için, bkz. [Visual Studio Code durum bilgisi olan ve durumsuz iş akışları oluşturma](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup).

  * *Durum bilgisi olmayan iş akışlarında*, [tetiklemeleri değil yalnızca yönetilen bağlayıcı *eylemlerini*](../connectors/managed.md)seçebileceğiniz için bir tetikleyiciyi seçtiğinizde **Azure** sekmesi görünmez. Durum bilgisiz iş akışları için Azure tarafından dağıtılan yönetilen bağlayıcıları etkinleştirebilseniz de tasarımcı, eklemeniz için herhangi bir yönetilen bağlayıcı tetikleyicisi göstermez.

  * Aşağıda kullanılamaz olarak listelenen Tetikleyiciler ve eylemler dışında *durum bilgisi olan iş akışları* için hem [yönetilen bağlayıcı Tetikleyicileri hem de Eylemler](../connectors/managed.md) kullanabileceğiniz şekilde kullanılabilir.

  * Bu Tetikleyiciler ve eylemler değişmiştir ya da şu anda sınırlı, desteklenmiyor ya da kullanılamıyor:

    * [Şirket içi veri ağ geçidi *Tetikleyicileri*](../connectors/managed.md#on-premises-connectors) kullanılamaz, ancak ağ *geçidi eylemleri kullanılabilir* .

    * Yerleşik eylem, [Azure işlevleri-bir Azure Işlevi seçin](logic-apps-azure-functions.md) artık **Azure işlev Işlemleri-bir Azure işlevi çağırır**. Bu eylem şu anda yalnızca **http tetikleyici** şablonundan oluşturulan işlevler için geçerlidir.

      Azure portal, Kullanıcı deneyimi aracılığıyla bir bağlantı oluşturarak erişiminizin bulunduğu bir HTTP tetikleyici işlevi seçebilirsiniz. Kod görünümündeki veya dosyadaki **workflow.js** Işlev eyleminin JSON tanımını inceleyebilirsiniz, eylem bir başvuru kullanarak işleve başvurur `connectionName` . Bu sürüm, işlevin bilgilerini bir bağlantı olarak soyutlar, bu, bir bağlantı oluşturduktan sonra, projenizin **connections.js** dosyasında bulabilirsiniz.

      > [!NOTE]
      > Tek kiracılı sürümde işlev eylemi yalnızca sorgu dizesi kimlik doğrulamasını destekler. Azure Logic Apps önizleme, bağlantıyı yaparken işlevden varsayılan anahtarı alır, bu anahtarı uygulamanızın ayarlarına depolar ve işlevi çağırırken kimlik doğrulaması için anahtarı kullanır.
      >
      > Çok kiracılı sürümde olduğu gibi, bu anahtarı yenilemeniz (örneğin, portalda Azure Işlevleri deneyimi aracılığıyla), geçersiz anahtar nedeniyle işlev eylemi artık çalışmıyor. Bu sorunu gidermek için, yeni anahtarla uygulamanızın ayarlarını çağırmak veya güncelleştirmek istediğiniz işleve yeniden oluşturmanız gerekir.

    * Yerleşik eylem, [satır Içi kod yürütme JavaScript kodu](logic-apps-add-run-inline-code.md) artık **satır içi kod Işlemleri-çevrimiçi JavaScript çalıştırma**.

      * Satır içi kod Işlemleri eylemleri artık bir tümleştirme hesabı gerektirmez.

      * MacOS ve Linux için, Visual Studio Code ' de Azure Logic Apps (Önizleme) uzantısını kullandığınızda **satır Içi kod işlemleri** artık desteklenmektedir.

      * Artık **satır Içi kod işlemleri** eyleminde değişiklik yaparsanız mantıksal uygulamanızı yeniden başlatmanız gerekmez.

      * **Satır Içi kod işlemleri** eylemlerinin [sınırlamaları güncelleştirildi](logic-apps-overview-preview.md#inline-code-limits).

    * [Tümleştirme hesapları için bazı YERLEŞIK B2B Tetikleyicileri ve eylemleri](../connectors/managed.md#integration-account-connectors) kullanılamaz, örneğin, **düz dosya** kodlama ve kod çözme eylemleri.

    * Yerleşik eylem, [Azure Logic Apps-bir mantıksal uygulama iş akışı](logic-apps-http-endpoint.md) artık **iş akışı işlemleri-bu iş akışı uygulamasında bir iş akışı çağırır**.

* [Özel bağlayıcılar](../connectors/apis-list.md#custom-apis-and-connectors) Şu anda önizleme için desteklenmiyor.

* **Barındırma planı kullanılabilirliği**: Azure portal veya Visual Studio Code dağıtım için tek kiracılı **mantıksal uygulama (Önizleme)** kaynak türünü oluşturup, Azure 'da yalnızca Premium veya App Service barındırma planını kullanabilirsiniz. Tüketim barındırma planları bu kaynak türünü dağıtmak için kullanılamaz ve desteklenmez. Visual Studio Code bir Docker kapsayıcısına dağıtabilirsiniz, ancak bir [tümleştirme hizmeti ortamına (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)ekleyebilirsiniz.

* **Visual Studio Code kesme noktası hata ayıklaması**: bir iş akışı için dosyada **workflow.js** içinde kesme noktaları ekleyip kullanabilmenize karşın, kesme noktaları yalnızca şu anda eylemler için desteklenir, tetiklerdir. Daha fazla bilgi için, bkz. [Visual Studio Code durum bilgisi olan ve durumsuz iş akışları oluşturma](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* **Yakınlaştırma denetimi**: yakınlaştırma denetimi şu anda tasarımcıda kullanılamıyor.

* **Tetikleyici geçmişi ve çalışma geçmişi**: **mantıksal uygulama (Önizleme)** kaynak türü için Azure Portal tetikleyici geçmişi ve çalıştırma geçmişi, mantıksal uygulama düzeyi değil iş akışı düzeyinde görünür. Bu geçmiş verileri bulmak için şu adımları izleyin:

   * Çalıştırma geçmişini görüntülemek için, mantıksal uygulamanızda iş akışını açın. İş akışı menüsünde, **Geliştirici** altında, **izleme**' yi seçin.

   * Tetikleyici geçmişini gözden geçirmek için, mantıksal uygulamanızda iş akışını açın. İş akışı menüsünde, **Geliştirici** altında **tetikleyici geçmişi**' ni seçin.

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>Katı ağ ve güvenlik duvarı senaryolarında trafiğe izin verme

Ortamınızda trafiği sınırlayan katı ağ gereksinimleri veya güvenlik duvarları varsa, mantıksal uygulama iş akışlarınızda herhangi bir tetikleyici veya eylem bağlantısına erişime izin vermeniz gerekir.

Bu bağlantılar için tam etki alanı adlarını (FQDN) bulmak için, bu konularda ilgili bölümleri gözden geçirin:

* [Tek kiracılı mantık uygulamaları için güvenlik duvarı izinleri-Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [Tek kiracılı mantık uygulamaları için güvenlik duvarı izinleri-Azure portal](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>Güncelleştirilmiş sınırlar

Azure Logic Apps önizlemesi için çok sayıda sınır [çok kiracılı Azure Logic Apps limitleriyle](logic-apps-limits-and-config.md)aynı kalsa da, bu sınırlar Azure Logic Apps önizleme için değiştirilmiştir.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>HTTP zaman aşımı limitleri

Tek bir gelen çağrı veya giden çağrı için, bu Tetikleyiciler ve eylemler için zaman aşımı sınırı 230 saniyedir (3,9 dakika):

* Giden istek: HTTP tetikleyicisi, HTTP eylemi
* Gelen istek: Istek tetikleyicisi, HTTP Web kancası tetikleyicisi, HTTP Web kancası eylemi

Karşılaştırma bölümünde, mantıksal uygulamaların ve iş akışlarının çalıştırıldığı diğer ortamlarda bu Tetikleyiciler ve eylemler için zaman aşımı limitleri aşağıda verilmiştir:

* Çok kiracılı Azure Logic Apps: 120 saniye (2 dakika)
* Tümleştirme hizmeti ortamı: 240 saniye (4 dakika)

Daha fazla bilgi için bkz. [http sınırları](logic-apps-limits-and-config.md#http-limits).

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Yönetilen bağlayıcılar

Yönetilen bağlayıcılar her bağlantı için dakikada 50 istek ile sınırlıdır. Bağlayıcı azaltma sorunlarıyla çalışmak için, Azure Logic Apps için [işleme azaltma sorunları (429-"çok fazla istek" hatası)](handle-throttling-problems-429-errors.md#connector-throttling)konusuna bakın.

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Satır içi kod Işlemleri (JavaScript kodunu Yürüt)

Tek bir mantıksal uygulama tanımı için, satır Içi kod Işlemleri eylemi, [**JavaScript kodu yürütme**](logic-apps-add-run-inline-code.md), şu güncelleştirilmiş sınırlara sahiptir:

* En fazla kod karakter sayısı 1.024 karakterden 100.000 karaktere kadar artar.

* Kod çalıştırmanın en uzun süresi, beş saniyeden 15 saniyeye artar.

Daha fazla bilgi için bkz. [mantıksal uygulama tanımı sınırları](logic-apps-limits-and-config.md#definition-limits).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure portal durum bilgisiz ve durum bilgisi olmayan iş akışları oluşturma](create-stateful-stateless-workflows-azure-portal.md)
* [Visual Studio Code durum bilgisiz ve durum bilgisi olmayan iş akışları oluşturma](create-stateful-stateless-workflows-visual-studio-code.md)
* [GitHub 'daki genel önizleme bilinen sorunlar sayfasını Logic Apps](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Ayrıca, Azure Logic Apps önizlemede deneyimlerinizi öğrenmek istiyoruz!

* Hatalar veya sorunlar için [sorunlarınızı GitHub ' da oluşturun](https://github.com/Azure/logicapps/issues).
* Sorular, istekler, açıklamalar ve diğer geri bildirimler için [Bu geri bildirim formunu kullanın](https://aka.ms/lafeedback).
