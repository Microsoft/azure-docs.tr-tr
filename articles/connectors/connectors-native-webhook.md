---
title: Olaylara bekle ve Yanıtla-Azure Logic Apps
description: Azure Logic Apps kullanarak bir hizmet uç noktasındaki olaylara göre tetikleme, duraklatma ve devam eden iş akışlarını otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 10/10/2019
tags: connectors
ms.openlocfilehash: ee86f53795b1b3e7bd61480a490d4e18c844d4c2
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804267"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Azure Logic Apps 'de HTTP Web kancalarını kullanarak otomatik olay tabanlı iş akışları oluşturun ve çalıştırın

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve yerleşik http Web kancası Bağlayıcısı sayesinde, mantıksal uygulamalar oluşturarak http veya HTTPS uç noktasında gerçekleşen belirli olaylara dayalı olarak bekleyen ve çalışan iş akışlarını otomatikleştirebilirsiniz. Örneğin, bir hizmet uç noktasını, iş akışını tetiklemeden önce belirli bir olayı bekleyerek ve bu uç noktayı düzenli olarak denetlemek veya *yoklamak* yerine, belirtilen eylemleri çalıştırarak izleyen bir mantıksal uygulama oluşturabilirsiniz.

Örnek olay tabanlı bazı iş akışları aşağıda verilmiştir:

* Mantıksal uygulama çalıştırmayı tetiklemeden önce bir öğenin [Azure Olay Hub 'ından](https://github.com/logicappsio/EventHubAPI) gelmesini bekleyin.
* Bir iş akışına devam etmeden önce onay bekle.

## <a name="how-do-webhooks-work"></a>Web kancaları nasıl çalışır?

HTTP Web kancası tetikleyicisi, olay tabanlıdır ve yeni öğeler için düzenli olarak denetim veya yoklamaya bağlı değildir. Bir Web kancası tetikleyicisi ile başlayan bir mantıksal uygulamayı kaydettiğinizde veya mantıksal uygulamanızı devre dışı iken etkin olarak değiştirdiğinizde Web kancası tetikleyicisi, bu hizmet veya uç noktaya *geri çağırma URL 'si* kaydederek belirli bir hizmete veya uç noktaya *abone olur* . Tetikleyici daha sonra bu hizmetin veya uç noktanın mantıksal uygulamayı çalıştırmaya başlayan URL 'YI aramasını bekler. [İstek tetikleyicisine](connectors-native-reqres.md)benzer şekilde, mantıksal uygulama, belirtilen olay gerçekleştiğinde hemen başlatılır. Tetikleyiciyi kaldırır ve mantıksal uygulamanızı kaydettiğinizde veya mantıksal uygulamanızı etkin durumundan devre dışı olarak değiştirdiğinizde tetikleyici, hizmetten veya uç noktadan *aboneliği* kaldırır.

Bir HTTP Web kancası eylemi ayrıca olay tabanlıdır ve bu hizmet veya uç noktaya *geri çağırma URL 'si* kaydederek belirli bir hizmete veya uç noktaya *abone* olur. Web kancası eylemi mantıksal uygulamanın iş akışını duraklatır ve Logic App çalışmaya devam etmeden önce hizmet veya uç nokta URL 'YI çağırana kadar bekler. Eylem mantığı uygulaması, bu durumlarda hizmetten veya uç noktadan *aboneliği* kaldırır:

* Web kancası eylemi başarıyla tamamlandığında
* Bir yanıt beklenirken mantıksal uygulama çalıştırması iptal edilirse
* Mantıksal uygulama zaman aşımına uğramadan önce

Örneğin, Office 365 Outlook bağlayıcısının [**onay e-postası gönder**](connectors-create-api-office365-outlook.md) eylemi, bu kalıbı izleyen bir Web kancası eyleminin örneğidir. Web kancası eylemini kullanarak bu kalıbı herhangi bir hizmete genişletebilirsiniz.

> [!NOTE]
> Logic Apps, geri çağrıyı HTTP Web kancası tetikleyicisine veya eylemine geri alırken Aktarım Katmanı Güvenliği (TLS) 1,2 ' i zorlar. SSL el sıkışma hataları görürseniz, TLS 1,2 kullandığınızdan emin olun.

Daha fazla bilgi için şu konulara bakın:

* [HTTP Web kancası tetikleyici parametreleri](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Web kancaları ve abonelikler](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Web kancasını destekleyen özel API 'Ler oluşturma](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* [Mantıksal uygulamalar veya Web](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) kancası [eylemleri](../logic-apps/logic-apps-create-api-app.md#webhook-actions) için Web kancası abone ve abonelik kaldırma düzenlerini destekleyen, önceden dağıtılan BIR uç noktanın veya API 'nin URL 'si

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)hakkında temel bilgi. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

* Hedef uç noktada belirli olayları beklemek istediğiniz mantıksal uygulama. HTTP Web kancası tetikleyicisinden başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). HTTP Web kancası eylemini kullanmak için, mantıksal uygulamanızı istediğiniz tetikleyiciyle başlatın. Bu örnek, ilk adım olarak HTTP tetikleyicisini kullanır.

## <a name="add-an-http-webhook-trigger"></a>HTTP Web kancası tetikleyicisi ekleme

Bu yerleşik tetikleyici, belirtilen hizmetle bir geri çağırma URL 'SI kaydeder ve bu hizmetin bu URL 'ye bir HTTP POST isteği göndermesini bekler. Bu olay gerçekleştiğinde, tetikleyici ateşlenir ve mantıksal uygulamayı hemen çalıştırır.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın. Mantıksal uygulama tasarımcısında boş mantıksal uygulamanızı açın.

1. Tasarımcıda arama kutusuna filtreniz olarak "http Web kancası" yazın. **Tetikleyiciler** listesinden **http Web kancası** tetikleyicisi ' ni seçin.

   ![HTTP Web kancası tetikleyicisi seçin](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Bu örnek, adımın daha açıklayıcı bir ada sahip olması için tetikleyiciyi "HTTP Web kancası tetikleyicisi" olarak yeniden adlandırır. Ayrıca, örnek daha sonra bir HTTP Web kancası eylemi ekler ve her iki ad de benzersiz olmalıdır.

1. Abone ol ve abonelik kaldırma çağrıları için kullanmak istediğiniz [http Web kancası tetikleyici parametrelerinin](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) değerlerini girin, örneğin:

   ![HTTP Web kancası tetikleyici parametrelerini girin](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Kullanılabilir başka parametreler eklemek için **yeni parametre Ekle** listesini açın ve istediğiniz parametreleri seçin.

   HTTP Web kancası için kullanılabilen kimlik doğrulama türleri hakkında daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Tetikleyici tetiklendiğinde çalıştırılan eylemlerle mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

1. İşiniz bittiğinde, işiniz bittiğinde mantıksal uygulamanızı kaydetmeyi unutmayın. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

   Mantıksal uygulamanızı kaydetmek, abonelik uç noktasını çağırır ve bu mantıksal uygulamayı tetiklemek için geri çağırma URL 'sini kaydeder.

1. Artık, hedef hizmet geri çağırma URL 'sine `HTTP POST` isteği gönderdiğinde, mantıksal uygulama ateşlenir ve istek aracılığıyla geçirilen tüm verileri içerir.

## <a name="add-an-http-webhook-action"></a>HTTP Web kancası eylemi ekleme

Bu yerleşik eylem, belirtilen hizmetle bir geri çağırma URL 'SI kaydeder, mantıksal uygulamanın iş akışını duraklatır ve bu hizmetin bu URL 'ye bir HTTP POST isteği göndermesini bekler. Bu olay gerçekleştiğinde, eylem mantıksal uygulamayı çalıştırmaya devam eder.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın. Mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   Bu örnek, ilk adım olarak HTTP Web kancası tetikleyicisini kullanır.

1. HTTP Web kancası eylemini eklemek istediğiniz adım altında **yeni adım**' ı seçin.

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Tasarımcıda arama kutusuna filtreniz olarak "http Web kancası" yazın. **Eylemler** listesinden **http Web kancası** eylemini seçin.

   ![HTTP Web kancası eylemini seç](./media/connectors-native-webhook/select-http-webhook-action.png)

   Bu örnek, adımın daha açıklayıcı bir ada sahip olması için eylemi "HTTP Web kancası eylemi" olarak yeniden adlandırır.

1. Abone ve abonelik kaldırma çağrıları için kullanmak istediğiniz [http Web kancası tetikleyici parametrelerine](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) benzer http Web kancası eylem parametrelerinin değerlerini sağlayın, örneğin:

   ![HTTP Web kancası eylem parametrelerini girin](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Çalışma zamanı sırasında mantıksal uygulama, bu eylemi çalıştırırken abonelik uç noktasını çağırır. Daha sonra mantıksal uygulamanız iş akışını duraklatır ve hedef hizmetin geri çağırma URL 'sine `HTTP POST` isteği göndermesini bekler. Eylem başarıyla tamamlanırsa, eylem uç noktadan aboneliği kaldırır ve mantıksal uygulamanız iş akışını çalıştırmaya devam eder.

1. Kullanılabilir başka parametreler eklemek için **yeni parametre Ekle** listesini açın ve istediğiniz parametreleri seçin.

   HTTP Web kancası için kullanılabilen kimlik doğrulama türleri hakkında daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. İşiniz bittiğinde mantıksal uygulamanızı kaydetmeyi unutmayın. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Tetikleyici ve eylem parametreleri hakkında daha fazla bilgi için, bkz. [http Web kancası parametreleri](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Çıkış ayrıntıları

Bu bilgileri döndüren bir HTTP Web kancası tetikleyicisinden veya eyleminden alınan çıktılar hakkında daha fazla bilgi bulabilirsiniz:

| Özellik adı | Tür | Açıklama |
|---------------|------|-------------|
| Bilgisinde | object | İstekten gelen üstbilgiler |
| bölümü | object | JSON nesnesi | İstekten gelen gövde içeriğine sahip nesne |
| durum kodu | int | İstekteki durum kodu |
|||

| Durum kodu | Açıklama |
|-------------|-------------|
| 200 | TAMAM |
| 202 | Eden |
| 400 | Hatalı istek |
| 401 | Yetkilendirilmemiş |
| 403 | Yasak |
| 404 | Bulunamadı |
| 500 | İç sunucu hatası. Bilinmeyen bir hata oluştu. |
|||

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
