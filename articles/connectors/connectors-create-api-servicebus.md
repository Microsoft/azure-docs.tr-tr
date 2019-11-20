---
title: Azure Service Bus Azure Logic Apps Exchange iletileri
description: Azure Logic Apps Azure Service Bus kullanarak ileti gönderme ve alma
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 477fdc2291d875c57bfb0ae3cb87b6df9bd41398
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024301"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Azure Logic Apps ve Azure Service Bus kullanarak bulutta ileti alışverişi yapın

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) Bağlayıcısı sayesinde, kuruluşunuz için uygulamalar arasında satış ve satın alma siparişleri, Günlükler ve stok hareketleri gibi verileri aktarmak üzere otomatik görevler ve iş akışları oluşturabilirsiniz. Bağlayıcı yalnızca iletileri izleyip yönetmez, gönderir ve yönetir, ancak aynı zamanda kuyruklar, oturumlar, konular, abonelikler vb. ile eylemler gerçekleştirir, örneğin:

* Kuyruklar, konular ve konu aboneliklerinde iletiler ne zaman ulaştığında (otomatik olarak tamamlanır) veya alındığında (Peek kilidi).
* İleti gönderin.
* Konu abonelikleri oluşturun ve silin.
* Kuyruklar ve konu aboneliklerinde iletileri yönetme (örneğin, Get, Gecikmeli, tam, erteleme, bırakma ve atılacak harf).
* Kuyruklar ve konu aboneliklerindeki ileti ve oturumlardaki kilitleri yenileyin.
* Kuyruklar ve konular 'daki oturumları kapatın.

Service Bus yanıt alan Tetikleyicileri kullanabilir ve çıktıyı mantıksal uygulamalarınızda diğer eylemler için kullanılabilir hale getirebilirsiniz. Ayrıca, Service Bus eylemlerdeki çıktıyı kullanan başka eylemlere de sahip olabilirsiniz. Service Bus ve Logic Apps yeni başladıysanız [ne Azure Service Bus? ne](../service-bus-messaging/service-bus-messaging-overview.md) olduğunu ve [Azure Logic Apps ne](../logic-apps/logic-apps-overview.md)olduğunu gözden geçirin.

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Kuyruk gibi bir Service Bus ad alanı ve mesajlaşma varlığı. Bu öğelerin ve mantıksal uygulamanızın aynı Azure aboneliğini kullanması gerekir. Bu öğeleriniz yoksa, [Service Bus ad alanınızı ve bir kuyruğu oluşturmayı](../service-bus-messaging/service-bus-create-namespace-portal.md)öğrenin.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Service Bus ad alanını ve mesajlaşma varlığını kullandığınız mantıksal uygulama. Mantıksal uygulamanız ve Service Bus 'ın aynı Azure aboneliğini kullanması gerekir. Service Bus tetikleyicisiyle iş akışınızı başlatmak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). İş akışınızda bir Service Bus eylemi kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin [yineleme tetikleyicisi](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>İzinleri denetle

Mantıksal uygulamanızın Service Bus ad alanına erişim izinleri olduğunu doğrulayın.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.

1. Service Bus *ad alanına*gidin. Ad alanı sayfasında, **Ayarlar**' ın altında, **paylaşılan erişim ilkeleri**' ni seçin. **Talepler**altında, bu ad alanı Için izinleri **yönetme** izinlerine sahip olup olmadığınızı kontrol edin.

   ![Service Bus ad alanı için izinleri yönetme](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Service Bus ad alanınız için bağlantı dizesini alın. Mantıksal uygulamanızda bağlantı bilgilerini sağladığınızda Bu dizeye ihtiyacınız vardır.

   1. **Paylaşılan erişim ilkeleri** bölmesinde **RootManageSharedAccessKey**' yi seçin.
   
   1. Birincil bağlantı dizeniz yanındaki Kopyala düğmesini seçin. Bağlantı dizesini daha sonra kullanmak üzere kaydedin.

      ![Service Bus ad alanı bağlantı dizesini Kopyala](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Bağlantı dizeniz Service Bus ad alanınız veya bir kuyruk gibi bir mesajlaşma varlığı ile ilişkili olup olmadığını doğrulamak için, `EntityPath`  parameter bağlantı dizesini arayın. Bu parametreyi bulursanız, bağlantı dizesi belirli bir varlık içindir ve mantıksal uygulamanızla birlikte kullanılacak doğru dize değildir.

## <a name="add-service-bus-trigger"></a>Service Bus tetikleyicisi Ekle

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve mantıksal uygulama tasarımcısında boş mantıksal uygulamanızı açın.

1. Arama kutusuna filtreniz olarak "Azure Service Bus" yazın. Tetikleyiciler listesinden istediğiniz tetikleyiciyi seçin.

   Örneğin, mantıksal uygulamanızı yeni bir öğe Service Bus kuyruğuna gönderildiğinde tetiklemeniz için **bir kuyrukta (otomatik olarak tamamlanır) bir ileti alındığında** ' ı seçin.

   ![Service Bus tetikleyicisi seçin](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Tüm Service Bus Tetikleyicileri *uzun yoklama* tetikleyicilerinde bulunur. Bu açıklama, tetikleyicinin tetiklendiği anlamına gelir, tetikleyici tüm iletileri işler ve sonra sıra veya konu aboneliğinde daha fazla ileti görünmesi için 30 saniye bekler. 30 saniye içinde herhangi bir ileti görünmezse, tetikleyici çalıştırması atlanır. Aksi takdirde tetikleyici, kuyruk veya konu aboneliği boş olana kadar iletileri okumaya devam eder. Sonraki tetikleyici yoklama, tetikleyicisinin özelliklerinde belirtilen yinelenme aralığına göre belirlenir.

   Bir **veya daha fazla ileti bir sıraya ulaştığında (otomatik tamamlamayı)** tetikleyicisi gibi bazı Tetikleyiciler, bir veya daha fazla ileti döndürebilir. Bu Tetikleyiciler tetiklendiğinde, tetikleyicisinin **en yüksek ileti sayısı** özelliği tarafından belirtilen ileti sayısı arasında bir değer döndürür.

1. Tetikleyiciniz ilk kez Service Bus ad alanına bağlanıyorsa, mantıksal uygulama Tasarımcısı sizden bağlantı bilgilerini isterse, bu adımları izleyin.

   1. Bağlantınız için bir ad girin ve Service Bus ad alanınızı seçin.

      ![Service Bus bağlantı oluştur, Bölüm 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Bunun yerine bağlantı dizesini el ile girmek için **bağlantı bilgilerini el ile girin**' i seçin. Bağlantı dizeniz yoksa, [Bağlantı dizenizi bulmayı](#permissions-connection-string)öğrenin.

   1. Service Bus ilkenizi seçin ve **Oluştur**' u seçin.

      ![Service Bus bağlantı oluştur, Bölüm 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Kuyruk veya konu gibi istediğiniz mesajlaşma varlığını seçin. Bu örnek için Service Bus kuyruğunuzu seçin.
   
      ![Service Bus kuyruğu seçin](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Seçtiğiniz Tetikleyiciniz için gerekli bilgileri sağlayın. Eyleme diğer kullanılabilir özellikleri eklemek için **yeni parametre Ekle** listesini açın ve istediğiniz özellikleri seçin.

   Bu örneğin tetikleyicisi için, kuyruğu denetleme sıklığını ve yoklama aralığını seçin.

   ![Yoklama aralığını ayarla](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Kullanılabilir Tetikleyiciler ve özellikler hakkında daha fazla bilgi için bağlayıcının [başvuru sayfasına](/connectors/servicebus/)bakın.

1. İstediğiniz eylemleri ekleyerek mantıksal uygulamanızı oluşturmaya devam edin.

   Örneğin, yeni bir ileti geldiğinde e-posta gönderen bir eylem ekleyebilirsiniz. Tetikleyiciniz kuyruğunuzu denetlediğinde ve yeni bir ileti bulduğunda, mantıksal uygulamanız bulunan ileti için seçtiğiniz eylemleri çalıştırır.

## <a name="add-service-bus-action"></a>Service Bus eylem Ekle

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Eylem eklemek istediğiniz adım altında **yeni adım**' ı seçin.

   Ya da adımlar arasında bir eylem eklemek için, işaretçinizi Bu adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak "Azure Service Bus" yazın. Eylemler listesinden istediğiniz eylemi seçin. 

   Bu örnek için **Ileti gönder** eylemini seçin.

   ![Service Bus eylemi seçin](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Eyleminiz Service Bus ad alanına ilk kez bağlanıyorsa, Logic App Designer sizden bağlantı bilgilerini isterse, bu adımları izleyin.

   1. Bağlantınız için bir ad girin ve Service Bus ad alanınızı seçin.

      ![Service Bus bağlantı oluştur, Bölüm 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Bunun yerine bağlantı dizesini el ile girmek için **bağlantı bilgilerini el ile girin**' i seçin. Bağlantı dizeniz yoksa, [Bağlantı dizenizi bulmayı](#permissions-connection-string)öğrenin.

   1. Service Bus ilkenizi seçin ve **Oluştur**' u seçin.

      ![Service Bus bağlantı oluştur, Bölüm 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Kuyruk veya konu gibi istediğiniz mesajlaşma varlığını seçin. Bu örnek için Service Bus kuyruğunuzu seçin.

      ![Service Bus kuyruğu seçin](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Seçtiğiniz eyleminiz için gerekli ayrıntıları sağlayın. Eyleme diğer kullanılabilir özellikleri eklemek için **yeni parametre Ekle** listesini açın ve istediğiniz özellikleri seçin.

   Örneğin, eyleme eklemek için **içerik** ve **içerik türü** özelliklerini seçin. Ardından, göndermek istediğiniz iletinin içeriğini belirtin.

   ![İleti içeriğini ve ayrıntılarını sağlayın](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Kullanılabilir eylemler ve özellikleri hakkında daha fazla bilgi için bağlayıcının [başvuru sayfasına](/connectors/servicebus/)bakın.

1. İstediğiniz diğer eylemleri ekleyerek mantıksal uygulamanızı oluşturmaya devam edin.

   Örneğin, iletinizin gönderildiğini onaylamak için e-posta gönderen bir eylem ekleyebilirsiniz.

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Service Bus Bağlayıcısı, bir hizmet veri yolundan bağlayıcı önbelleğine en çok 1.500 benzersiz oturum kaydedebilir. Oturum sayısı bu sınırı aşarsa, eski oturumlar önbellekten kaldırılır. Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili diğer teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/servicebus/)gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
