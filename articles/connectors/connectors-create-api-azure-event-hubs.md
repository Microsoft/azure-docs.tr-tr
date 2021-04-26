---
title: Azure Event Hubs bağlanma
description: Azure Event Hubs ve Azure Logic Apps kullanarak olayları izleyen ve yöneten otomatik görevler ve iş akışları oluşturun
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 65da8e07c01561577fe7eff449bfc10348c7f277
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101716610"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Azure Event Hubs ve Azure Logic Apps ile olayları izleme, alma ve gönderme

Bu makalede, Azure Event Hubs Bağlayıcısı ile bir mantıksal uygulamanın içinden [azure Event Hubs](../event-hubs/event-hubs-about.md) gönderilen olayları nasıl izleyebileceğinizi ve yönetebileceğinizi gösterir. Böylece, Olay Hub’ınızdan gelen olayları almaya, bu olayları denetlemeye ve göndermeye yönelik görevleri ve iş akışlarını otomatikleştiren mantıksal uygulamalar oluşturabilirsiniz. Bağlayıcıya özgü teknik bilgiler için bkz. [Azure Event Hubs Bağlayıcısı başvurusu](/connectors/eventhubs/) </a> .

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* [Azure Event Hubs ad alanı ve Olay Hub 'ı](../event-hubs/event-hubs-create.md)

* Olay Hub 'ınıza erişmek istediğiniz mantıksal uygulama. Mantıksal uygulamanızı Azure Event Hubs tetikleyicisiyle başlatmak için [boş bir mantıksal uygulama](../logic-apps/quickstart-create-first-logic-app-workflow.md)gerekir.
Logic Apps 'e yeni başladıysanız, [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [hızlı başlangıç: Ilk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)' yı gözden geçirin.

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>İzinleri denetle ve bağlantı dizesi al

Mantıksal uygulamanızın Olay Hub 'ınıza erişebildiğinizden emin olmak için, izinlerinizi denetleyin ve Event Hubs ad alanınız için bağlantı dizesini alın.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Belirli bir olay hub 'ına değil Event Hubs *ad alanına* gidin. 

1. Ad alanı menüsünde, **Ayarlar** altında, **paylaşılan erişim ilkeleri**' ni seçin. **Talepler** altında, bu ad alanı Için izinleri **yönetme** izinlerine sahip olup olmadığınızı kontrol edin.

   ![Olay Hub 'ı ad alanınız için izinleri yönetme](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Daha sonra bağlantı bilgilerinizi el ile girmek istiyorsanız, Event Hubs ad alanınız için bağlantı dizesini alın.

   1. **İlke** altında **RootManageSharedAccessKey** öğesini seçin.

   1. Birincil anahtarınızın bağlantı dizesini bulun. Kopyala düğmesini seçin ve daha sonra kullanmak üzere bağlantı dizesini kaydedin.

      ![Event Hubs ad alanı bağlantı dizesini Kopyala](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Bağlantı dizeniz Event Hubs ad alanınız veya belirli bir olay hub 'ı ile ilişkili olup olmadığını doğrulamak için, bağlantı dizesinin parametreye sahip olmadığından emin olun `EntityPath` . Bu parametreyi bulursanız, bağlantı dizesi belirli bir olay hub 'ı "varlığına" yöneliktir ve mantıksal uygulamanızla kullanılacak doğru dize değildir.

1. Şimdi [Event Hubs tetikleyici ekleme](#add-trigger) veya [Event Hubs eylemi ekleme](#add-action)ile devam edin.

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Event Hubs tetikleyicisi ekleme

Azure Logic Apps, her mantıksal uygulama, belirli bir olay gerçekleştiğinde veya belirli bir koşul karşılandığında tetiklenen bir [tetikleyiciyle](../logic-apps/logic-apps-overview.md#logic-app-concepts)başlamalıdır. Tetikleyici her tetiklendiğinde Logic Apps altyapısı bir mantıksal uygulama örneği oluşturur ve uygulamanızın iş akışını çalıştırmaya başlar.

Bu örnek, Olay Hub 'ınıza yeni olaylar gönderildiğinde bir mantıksal uygulama iş akışını nasıl başlatakullanabileceğinizi gösterir. 

> [!NOTE]
> Tüm Olay Hub 'ı Tetikleyicileri *uzun yoklama* tetiklerdir, bu da tetikleyicinin tüm olayları işlediği ve daha fazla olayın olay hub 'ında görünmesi için bölüm başına 30 saniye beklediği anlamına gelir. Bu nedenle, tetikleyici dört bölüm ile ayarlandıysa, tetikleyicinin tüm bölümleri yoklamak için bu gecikmenin iki dakika geçmesi gerekebilir. Bu gecikmeden hiçbir olay alınmıyorsa, tetikleyici çalıştırması atlanır. Aksi takdirde, tetikleyici olay hub 'ınız boş olana kadar olayları okumaya devam eder. Sonraki tetikleyici yoklama, tetikleyicisinin özelliklerinde belirttiğiniz yinelenme aralığına göre yapılır.

1. Azure portal veya Visual Studio 'da, Logic Apps tasarımcısını açan boş bir mantıksal uygulama oluşturun. Bu örnek Azure portal kullanır.

1. Arama kutusuna filtreniz olarak "Olay Hub 'ları" yazın. Tetikleyiciler listesinden şu tetikleyiciyi seçin: **Olay Hub 'ında olaylar kullanılabilir olduğunda-Event Hubs**

   ![Tetikleyiciyi seçin](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Bağlantı ayrıntıları istenirse [Event Hubs bağlantınızı hemen oluşturun](#create-connection). 

1. Tetikleyicide, izlemek istediğiniz olay hub 'ı hakkında bilgi sağlayın. Daha fazla özellik için **yeni parametre Ekle** listesini açın. Bir parametre seçilmesi, bu özelliği tetikleyici kartına ekler.

   ![Tetikleyici özellikleri](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Olay Hub'ı adı** | Yes | İzlemek istediğiniz olay hub 'ının adı |
   | **İçerik türü** | No | Olayın içerik türü. Varsayılan değer: `application/octet-stream`. |
   | **Tüketici grubu adı** | No | Olayları okumak için kullanılacak [Olay Hub 'ı Tüketici grubu adı](../event-hubs/event-hubs-features.md#consumer-groups) . Belirtilmemişse, varsayılan Tüketici grubu kullanılır. |
   | **En fazla olay sayısı** | No | En fazla olay sayısı. Tetikleyici, bu özellik tarafından belirtilen bir ve olay sayısı arasında döndürülür. |
   | **Aralık** | Yes | İş akışının sıklık temelinde ne sıklıkta çalışacağını açıklayan pozitif bir tamsayı |
   | **Sıklık** | Yes | Yinelenme için zaman birimi |
   ||||

   **Ek özellikler**

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **İçerik şeması** | No | Olay Hub 'ından okunan olaylara yönelik JSON içerik şeması. Örneğin, içerik şemasını belirtirseniz, mantıksal uygulamayı yalnızca şemayla eşleşen olaylar için tetikleyebilirsiniz. |
   | **Minimum bölüm anahtarı** | No | Okunacak minimum [bölüm](../event-hubs/event-hubs-features.md#partitions) kimliğini girin. Varsayılan olarak, tüm bölümler okunurdur. |
   | **En büyük bölüm anahtarı** | No | Okunacak [bölüm](../event-hubs/event-hubs-features.md#partitions) kimliği üst sınırını girin. Varsayılan olarak, tüm bölümler okunurdur. |
   | **Saat dilimi** | No | Yalnızca bir başlangıç saati belirttiğinizde geçerlidir çünkü bu tetikleyici UTC sapmasını kabul etmez. Uygulamak istediğiniz saat dilimini seçin. <p>Daha fazla bilgi için bkz. [Azure Logic Apps ile yinelenen görevler ve iş akışları oluşturma ve çalıştırma](../connectors/connectors-native-recurrence.md). |
   | **Başlangıç zamanı** | No | Bu biçimde bir başlangıç saati sağlayın: <p>YYYY-MM-DDThh: mm: ss saat dilimi seçerseniz<p>-veya-<p>YYYY-MM-DDThh: mm: ssZ saat dilimi seçme<p>Daha fazla bilgi için bkz. [Azure Logic Apps ile yinelenen görevler ve iş akışları oluşturma ve çalıştırma](../connectors/connectors-native-recurrence.md). |
   ||||

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

1. Şimdi, tetikleyici sonuçlarıyla gerçekleştirmek istediğiniz görevler için mantıksal uygulamanıza bir veya daha fazla eylem eklemeye devam edin. 

   Örneğin, olayları bir kategori gibi belirli bir değere göre filtrelemek için, **olay Gönder** eyleminin yalnızca koşullarınızı karşılayan olayları göndermesi için bir koşul ekleyebilirsiniz. 

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Event Hubs eylemi ekleme

Azure Logic Apps bir [eylem](../logic-apps/logic-apps-overview.md#logic-app-concepts) , iş akışınızda bir tetikleyiciyi veya başka bir eylemi izleyen bir adımdır. Bu örnekte, mantıksal uygulama, Olay Hub 'ınızdaki yeni olayları denetleyen Event Hubs tetikleyicisiyle başlar.

1. Azure portal veya Visual Studio 'da mantıksal uygulamanızı Logic Apps tasarımcısında açın. Bu örnek Azure portal kullanır.

1. Tetikleyici veya eylem altında **yeni adım**' ı seçin.

   Varolan adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyın. 
   Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak "Olay Hub 'ları" yazın.
Eylemler listesinden şu eylemi seçin: **olay Gönder-Event Hubs**

   !["Olay Gönder" eylemini seçin](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Bağlantı ayrıntıları istenirse [Event Hubs bağlantınızı hemen oluşturun](#create-connection). 

1. Eylemde, göndermek istediğiniz olaylar hakkında bilgi sağlayın. Daha fazla özellik için **yeni parametre Ekle** listesini açın. Bir parametre seçilmesi bu özelliği eylem kartına ekler.

   ![Olay Hub 'ı adını seçin ve olay içeriğini sağlayın](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Olay Hub'ı adı** | Yes | Olayı göndermek istediğiniz olay hub 'ı |
   | **İçerik** | No | Göndermek istediğiniz olay için içerik |
   | **Özellikler** | No | Gönderilen uygulama özellikleri ve değerler |
   | **Bölüm anahtarı** | No | Olayın gönderileceği [bölüm](../event-hubs/event-hubs-features.md#partitions) kimliği |
   ||||

   Örneğin, Event Hubs tetikleyicinizden çıktıyı başka bir olay hub 'ına gönderebilirsiniz:

   ![Olay örneği gönder](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Olay Hub 'ınıza bağlanın

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Bağlantı bilgileri istendiğinde, şu ayrıntıları sağlayın:

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Bağlantı adı** | Yes | <*bağlantı adı*> | Bağlantınız için oluşturulacak ad |
   | **Event Hubs ad alanı** | Yes | <*Olay-Hub 'lar-ad alanı*> | Kullanmak istediğiniz Event Hubs ad alanını seçin. |
   |||||  

   Örnek:

   ![Olay Hub 'ı bağlantısı oluştur](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Bağlantı dizesini el ile girmek için **bağlantı bilgilerini el ile girin**' i seçin. 
   [Bağlantı dizenizi bulmayı](#permissions-connection-string)öğrenin.

2. Henüz seçili değilse, kullanılacak Event Hubs ilkesini seçin. **Oluştur**' a tıklayın.

   ![Olay Hub 'ı bağlantısı oluşturma, Bölüm 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Bağlantınızı oluşturduktan sonra [Event Hubs tetikleyici Ekle](#add-trigger) veya [Event Hubs eylem Ekle](#add-action)' ye geçin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Swagger dosyasında açıklandığı şekilde Tetikleyiciler, Eylemler ve sınırlar gibi teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/eventhubs/)bakın.

> [!NOTE]
> Bir [tümleştirme hizmeti ortamındaki (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Logic Apps için, bu bağlayıcının Ise etiketli sürümü bunun yerine [Ise ileti sınırlarını](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) kullanır.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
