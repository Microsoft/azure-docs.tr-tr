---
title: Olayları bekle ve Yanıtla
description: Azure Logic Apps kullanarak bir hizmet uç noktasındaki olaylara göre tetikleme, duraklatma ve devam eden iş akışlarını otomatikleştirin
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: cdbf853a96f319cb27c10136004a1398014e602f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109161"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Azure Logic Apps 'de HTTP Web kancalarını kullanarak otomatik olay tabanlı iş akışları oluşturun ve çalıştırın

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve yerleşik http Web kancası Bağlayıcısı sayesinde, bir hizmet uç noktasına abone olan otomatik görevler ve iş akışları oluşturabilir, belirli olayları bekleyebilir ve bu uç noktayı düzenli olarak denetlemek veya *yoklamak* yerine bu olaylara göre çalıştırabilirsiniz.

Aşağıda bazı örnek Web kancası tabanlı iş akışları verilmiştir:

* Mantıksal uygulama çalıştırmayı tetiklemeden önce bir öğenin [Azure Olay Hub 'ından](https://github.com/logicappsio/EventHubAPI) gelmesini bekleyin.
* Bir iş akışına devam etmeden önce onay bekle.

Bu makalede, mantıksal uygulamanızın bir hizmet uç noktasındaki olayları alabilmesi ve yanıtlayabilmesi için Web kancası tetikleyicisi ve Web kancası eyleminin nasıl kullanılacağı gösterilmektedir.

## <a name="how-do-webhooks-work"></a>Web kancaları nasıl çalışır?

Web kancası tetikleyicisi, olay tabanlıdır ve yeni öğeler için düzenli olarak denetim veya yoklamaya bağlı değildir. Bir Web kancası tetikleyicisi ile başlayan bir mantıksal uygulamayı kaydettiğinizde veya mantıksal uygulamanızı devre dışı iken etkin olarak değiştirdiğinizde Web kancası tetikleyicisi, bu uç noktaya *geri çağırma URL 'si* kaydederek belirtilen hizmet uç noktasına *abone olur* . Tetikleyici daha sonra bu hizmet uç noktasının mantıksal uygulamayı çalıştırmaya başlayan URL 'YI aramasını bekler. [İstek tetikleyicisine](connectors-native-reqres.md)benzer şekilde, mantıksal uygulama, belirtilen olay gerçekleştiğinde hemen başlatılır. Web kancası tetikleyicisi, tetikleyiciyi kaldırır ve mantıksal uygulamanızı kaydederseniz veya mantıksal uygulamanızı etkin durumundan devre dışı olarak değiştirdiğinizde hizmet uç noktasından *abone olur* .

Bir Web kancası eylemi ayrıca olay tabanlıdır ve bu uç noktaya *geri çağırma URL 'si* kaydederek belirtilen hizmet uç noktasına *abone* olur. Web kancası eylemi mantıksal uygulamanın iş akışını duraklatır ve Logic App çalışmaya devam etmeden önce hizmet uç noktası URL 'YI çağırana kadar bekler. Web kancası eylemi bu durumlarda hizmet uç noktasından *aboneliği kaldırır* :

* Web kancası eylemi başarıyla tamamlandığında
* Bir yanıt beklenirken mantıksal uygulama çalıştırması iptal edilirse
* Mantıksal uygulama zaman aşımına uğramadan önce

Örneğin, Office 365 Outlook bağlayıcısının [**onay e-postası gönder**](connectors-create-api-office365-outlook.md) eylemi, bu kalıbı izleyen bir Web kancası eyleminin örneğidir. Web kancası eylemini kullanarak bu kalıbı herhangi bir hizmete genişletebilirsiniz.

Daha fazla bilgi için şu konulara bakın:

* [Web kancaları ve abonelikler](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Web kancasını destekleyen özel API 'Ler oluşturma](../logic-apps/logic-apps-create-api-app.md)

Mantıksal uygulamanıza yönelik olarak, [Aktarım Katmanı Güvenliği (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), daha önce GÜVENLI yuva KATMANı (SSL) veya [Azure Active Directory açık kimlik doğrulaması (Azure AD OAuth)](../active-directory/develop/index.yml)gibi bilinen bir gelen çağrılar için şifreleme, güvenlik ve yetkilendirme hakkında bilgi için bkz. [İstek tabanlı tetikleyicilere gelen çağrılar Için güvenli erişim ve veri erişimi](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* [Mantıksal uygulamalar veya Web](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) kancası [eylemleri](../logic-apps/logic-apps-create-api-app.md#webhook-actions) için Web kancası abone ve abonelik kaldırma düzenlerini destekleyen, önceden dağıtılan BIR uç noktanın veya API 'nin URL 'si

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)hakkında temel bilgi. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

* Hedef uç noktada belirli olayları beklemek istediğiniz mantıksal uygulama. HTTP Web kancası tetikleyicisinden başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). HTTP Web kancası eylemini kullanmak için, mantıksal uygulamanızı istediğiniz tetikleyiciyle başlatın. Bu örnek, ilk adım olarak HTTP tetikleyicisini kullanır.

## <a name="add-an-http-webhook-trigger"></a>HTTP Web kancası tetikleyicisi ekleme

Bu yerleşik tetikleyici, hedef hizmette Subscribe uç noktasını çağırır ve hedef hizmete bir geri çağırma URL 'SI kaydeder. Mantıksal uygulamanız daha sonra hedef hizmetin `HTTP POST` geri ÇAĞıRMA URL 'sine istek göndermesini bekler. Bu olay gerçekleştiğinde, tetikleyici ateşlenir ve istekteki tüm verileri iş akışına karşı geçirir.

1. [Azure portalında](https://portal.azure.com) oturum açın. Mantıksal uygulama tasarımcısında boş mantıksal uygulamanızı açın.

1. Tasarımcı 'nın arama kutusuna `http webhook` filtreniz olarak girin. **Tetikleyiciler** listesinden **http Web kancası** tetikleyicisi ' ni seçin.

   ![HTTP Web kancası tetikleyicisi seçin](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Bu örnek, `HTTP Webhook trigger` adımının daha açıklayıcı bir ada sahip olması için tetikleyiciyi olarak yeniden adlandırır. Ayrıca, örnek daha sonra bir HTTP Web kancası eylemi ekler ve her iki ad de benzersiz olmalıdır.

1. Abone ol ve abonelik kaldırma çağrıları için kullanmak istediğiniz [http Web kancası tetikleyici parametrelerinin](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) değerlerini belirtin.

   Bu örnekte tetikleyici, abone ve abonelik kaldırma işlemleri gerçekleştirilirken kullanılacak yöntemleri, URI 'Leri ve ileti gövdelerini içerir.

   ![HTTP Web kancası tetikleyici parametrelerini girin](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Subscription-yöntemi** | Yes | Hedef uç noktaya abone olurken kullanılacak yöntem |
   | **Abone ol-URI** | Yes | Hedef uç noktaya abone olmak için kullanılacak URL |
   | **Abone ol-gövde** | No | Abonelik isteğine dahil edilecek herhangi bir ileti gövdesi. Bu örnek, mantıksal uygulamanızın `@listCallbackUrl()` geri arama URL 'sini almak için ifadesini kullanarak mantıksal uygulamanız olan aboneyi benzersiz bir şekilde tanımlayan geri çağırma URL 'sini içerir. |
   | **Abonelik kaldırma yöntemi** | No | Hedef uç noktadan abone olunurken kullanılacak yöntem |
   | **Abonelik kaldırma-URI** | No | Hedef uç noktadan abone olunurken kullanılacak URL |
   | **Abonelik kaldırma-gövde** | No | Abonelik kaldırma isteğine eklenecek isteğe bağlı bir ileti gövdesi <p><p>**Note**: Bu özellik, işlevinin kullanımını desteklemiyor `listCallbackUrl()` . Ancak, tetikleyici otomatik olarak, `x-ms-client-tracking-id` ve `x-ms-workflow-operation-name` hedef hizmetin aboneyi benzersiz olarak tanımlamak için kullanabileceği üst bilgileri ekler ve gönderir. |
   ||||

1. Başka tetikleyici özellikleri eklemek için **yeni parametre Ekle** listesini açın.

   ![Daha fazla tetikleyici özelliği Ekle](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Örneğin, kimlik doğrulaması kullanmanız gerekiyorsa, **abone ol-Authentication** ve **abonelikten çıkma-Authentication** özelliklerini ekleyebilirsiniz. HTTP Web kancası için kullanılabilen kimlik doğrulama türleri hakkında daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Tetikleyici tetiklendiğinde çalıştırılan eylemlerle mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

1. İşiniz bittiğinde, işiniz bittiğinde mantıksal uygulamanızı kaydetmeyi unutmayın. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

   Mantıksal uygulamanızı kaydetmek, hedef hizmette abone ol uç noktasını çağırır ve geri çağırma URL 'sini kaydeder. Mantıksal uygulamanız daha sonra hedef hizmetin `HTTP POST` geri ÇAĞıRMA URL 'sine istek göndermesini bekler. Bu olay gerçekleştiğinde, tetikleyici ateşlenir ve istekteki tüm verileri iş akışına karşı geçirir. Bu işlem başarıyla tamamlanırsa, tetikleyici, uç noktadan abone olur ve mantıksal uygulamanız kalan iş akışını sürdürür.

## <a name="add-an-http-webhook-action"></a>HTTP Web kancası eylemi ekleme

Bu yerleşik eylem, hedef hizmette abone ol uç noktasını çağırır ve hedef hizmete bir geri çağırma URL 'SI kaydeder. Daha sonra mantıksal uygulamanız duraklatılır ve hedef hizmetin `HTTP POST` geri ÇAĞıRMA URL 'sine istek göndermesini bekler. Bu olay gerçekleştiğinde eylem, istekteki tüm verileri iş akışına karşı geçirir. İşlem başarıyla tamamlanırsa, eylem uç noktadan aboneliği kaldırır ve mantıksal uygulamanız kalan iş akışını çalıştırmaya devam eder.

1. [Azure portalında](https://portal.azure.com) oturum açın. Mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   Bu örnek, ilk adım olarak HTTP Web kancası tetikleyicisini kullanır.

1. HTTP Web kancası eylemini eklemek istediğiniz adım altında **yeni adım**' ı seçin.

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Tasarımcı 'nın arama kutusuna `http webhook` filtreniz olarak girin. **Eylemler** listesinden **http Web kancası** eylemini seçin.

   ![HTTP Web kancası eylemini seç](./media/connectors-native-webhook/select-http-webhook-action.png)

   Bu örnek, adımın daha açıklayıcı bir ada sahip olması için eylemi "HTTP Web kancası eylemi" olarak yeniden adlandırır.

1. Abone ve abonelik kaldırma çağrıları için kullanmak istediğiniz http Web kancası [tetikleyici parametrelerine](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)benzer http Web kancası eylem parametreleri için değerler sağlayın.

   Bu örnekte, eylem abone ve abonelik kaldırma işlemleri gerçekleştirilirken kullanılacak yöntemleri, URI 'Leri ve ileti gövdelerini içerir.

   ![HTTP Web kancası eylem parametrelerini girin](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Subscription-yöntemi** | Yes | Hedef uç noktaya abone olurken kullanılacak yöntem |
   | **Abone ol-URI** | Yes | Hedef uç noktaya abone olmak için kullanılacak URL |
   | **Abone ol-gövde** | No | Abonelik isteğine dahil edilecek herhangi bir ileti gövdesi. Bu örnek, mantıksal uygulamanızın `@listCallbackUrl()` geri arama URL 'sini almak için ifadesini kullanarak mantıksal uygulamanız olan aboneyi benzersiz bir şekilde tanımlayan geri çağırma URL 'sini içerir. |
   | **Abonelik kaldırma yöntemi** | No | Hedef uç noktadan abone olunurken kullanılacak yöntem |
   | **Abonelik kaldırma-URI** | No | Hedef uç noktadan abone olunurken kullanılacak URL |
   | **Abonelik kaldırma-gövde** | No | Abonelik kaldırma isteğine eklenecek isteğe bağlı bir ileti gövdesi <p><p>**Note**: Bu özellik, işlevinin kullanımını desteklemiyor `listCallbackUrl()` . Ancak, eylem otomatik olarak, `x-ms-client-tracking-id` ve `x-ms-workflow-operation-name` hedef hizmetin aboneyi benzersiz bir şekilde tanımlamak için kullanabileceği üst bilgileri ekler ve gönderir. |
   ||||

1. Başka eylem özellikleri eklemek için **yeni parametre Ekle** listesini açın.

   ![Daha fazla eylem özelliği ekleyin](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Örneğin, kimlik doğrulaması kullanmanız gerekiyorsa, **abone ol-Authentication** ve **abonelikten çıkma-Authentication** özelliklerini ekleyebilirsiniz. HTTP Web kancası için kullanılabilen kimlik doğrulama türleri hakkında daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. İşiniz bittiğinde mantıksal uygulamanızı kaydetmeyi unutmayın. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

   Bu eylem çalıştırıldığında, mantıksal uygulamanız hedef hizmette abone ol uç noktasını çağırır ve geri arama URL 'sini kaydeder. Mantıksal uygulama daha sonra iş akışını duraklatır ve hedef hizmetin `HTTP POST` geri ÇAĞıRMA URL 'sine istek göndermesini bekler. Bu olay gerçekleştiğinde eylem, istekteki tüm verileri iş akışına karşı geçirir. İşlem başarıyla tamamlanırsa, eylem uç noktadan aboneliği kaldırır ve mantıksal uygulamanız kalan iş akışını çalıştırmaya devam eder.

## <a name="trigger-and-action-outputs"></a>Tetikleyici ve eylem çıkışları

Bu bilgileri döndüren bir HTTP Web kancası tetikleyicisinden veya eyleminden alınan çıktılar hakkında daha fazla bilgi bulabilirsiniz:

| Özellik adı | Tür | Description |
|---------------|------|-------------|
| bilgisinde | object | İstekten gelen üstbilgiler |
| body | object | İstekten gelen gövde içeriğine sahip nesne |
| durum kodu | int | İstekteki durum kodu |
||||

| Durum kodu | Description |
|-------------|-------------|
| 200 | Tamam |
| 202 | Kabul edildi |
| 400 | Hatalı istek |
| 401 | Yetkisiz |
| 403 | Yasak |
| 404 | Bulunamadı |
| 500 | İç sunucu hatası. Bilinmeyen bir hata oluştu. |
|||

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Tetikleyici ve eylem parametreleri hakkında daha fazla bilgi için, bkz. [http Web kancası parametreleri](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

## <a name="next-steps"></a>Sonraki adımlar

* [İstek tabanlı tetikleyicilere gelen çağrılar için güvenli erişim ve veri erişimi](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Logic Apps için bağlayıcılar](../connectors/apis-list.md)
