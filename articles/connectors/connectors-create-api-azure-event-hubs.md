---
title: Azure Event Hubs bağlanma-Azure Logic Apps
description: Azure Event Hubs ve Azure Logic Apps ile olayları yönetme ve izleme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 24f66782821f372f5c045dbb82db24fa8b6ad482
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051087"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Azure Event Hubs ve Azure Logic Apps ile olayları izleyin, alın ve gönderin

Bu makalede, Azure Event Hubs Bağlayıcısı ile bir mantıksal uygulamanın içinden [azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) gönderilen olayları nasıl izleyebileceğinizi ve yönetebileceğinizi gösterir. Böylece, Olay Hub’ınızdan gelen olayları almaya, bu olayları denetlemeye ve göndermeye yönelik görevleri ve iş akışlarını otomatikleştiren mantıksal uygulamalar oluşturabilirsiniz. Bağlayıcıya özgü teknik bilgiler için bkz. [Azure Event Hubs Bağlayıcısı başvurusu](https://docs.microsoft.com/connectors/eventhubs/)</a>.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* [Azure Event Hubs ad alanı ve Olay Hub 'ı](../event-hubs/event-hubs-create.md)

* Olay Hub 'ınıza erişmek istediğiniz mantıksal uygulama. Mantıksal uygulamanızı Azure Event Hubs tetikleyicisiyle başlatmak için [boş bir mantıksal uygulama](../logic-apps/quickstart-create-first-logic-app-workflow.md)gerekir.
Logic Apps 'e yeni başladıysanız [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [hızlı başlangıç ' ı inceleyin: İlk mantıksal uygulamanızı](../logic-apps/quickstart-create-first-logic-app-workflow.md)oluşturun.

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>İzinleri denetle ve bağlantı dizesi al

Mantıksal uygulamanızın Olay Hub 'ınıza erişebildiğinizden emin olmak için, izinlerinizi denetleyin ve Event Hubs ad alanınız için bağlantı dizesini alın.

1. [Azure Portal](https://portal.azure.com) oturum açın.

1. Belirli bir olay hub 'ına değil Event Hubs *ad alanına*gidin. 

1. Ad alanı menüsünde, **Ayarlar**altında, **paylaşılan erişim ilkeleri**' ni seçin. **Talepler**altında, bu ad alanı Için izinleri **yönetme** izinlerine sahip olup olmadığınızı kontrol edin.

   ![Olay Hub 'ı ad alanınız için izinleri yönetme](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Daha sonra bağlantı bilgilerinizi el ile girmek istiyorsanız, Event Hubs ad alanınız için bağlantı dizesini alın.

   1. **İlke**altında **RootManageSharedAccessKey**öğesini seçin.

   1. Birincil anahtarınızın bağlantı dizesini bulun. Kopyala düğmesini seçin ve daha sonra kullanmak üzere bağlantı dizesini kaydedin.

      ![Event Hubs ad alanı bağlantı dizesini Kopyala](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Bağlantı dizeniz Event Hubs ad alanınız veya belirli bir olay hub 'ı ile ilişkili olup olmadığını doğrulamak için, bağlantı dizesinin `EntityPath`  parametreye sahip olmadığından emin olun. Bu parametreyi bulursanız, bağlantı dizesi belirli bir olay hub 'ı "varlığına" yöneliktir ve mantıksal uygulamanızla kullanılacak doğru dize değildir.

1. Şimdi [Event Hubs tetikleyici ekleme](#add-trigger) veya [Event Hubs eylemi ekleme](#add-action)ile devam edin.

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Event Hubs tetikleyicisi Ekle

Azure Logic Apps, her mantıksal uygulama, belirli bir olay gerçekleştiğinde [](../logic-apps/logic-apps-overview.md#logic-app-concepts)veya belirli bir koşul karşılandığında tetiklenen bir tetikleyiciyle başlamalıdır. Tetikleyici her tetiklendiğinde Logic Apps altyapısı bir mantıksal uygulama örneği oluşturur ve uygulamanızın iş akışını çalıştırmaya başlar.

Bu örnek, Olay Hub 'ınıza yeni olaylar gönderildiğinde bir mantıksal uygulama iş akışını nasıl başlatakullanabileceğinizi gösterir. 

1. Azure portal veya Visual Studio 'da, Logic Apps tasarımcısını açan boş bir mantıksal uygulama oluşturun. Bu örnek Azure portal kullanır.

1. Arama kutusuna filtreniz olarak "Olay Hub 'ları" yazın. Tetikleyiciler listesinde, bu tetikleyiciyi seçin: **Olay Hub 'ında olaylar kullanılabilir olduğunda-Event Hubs**

   ![Tetikleyici seçin](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Bağlantı ayrıntıları istenirse [Event Hubs bağlantınızı hemen oluşturun](#create-connection). 

1. Tetikleyicide, izlemek istediğiniz olay hub 'ı hakkında bilgi sağlayın. Daha fazla özellik için **yeni parametre Ekle** listesini açın. Bir parametre seçilmesi, bu özelliği tetikleyici kartına ekler.

   ![Tetikleyici özellikleri](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Olay hub'ı adı** | Evet | İzlemek istediğiniz olay hub 'ının adı |
   | **İçerik türü** | Hayır | Olayın içerik türü. Varsayılan, `application/octet-stream` değeridir. |
   | **Tüketici grubu adı** | Hayır | Olayları okumak için kullanılacak [Olay Hub 'ı Tüketici grubu adı](../event-hubs/event-hubs-features.md#consumer-groups) . Belirtilmemişse, varsayılan Tüketici grubu kullanılır. |
   | **En fazla olay sayısı** | Hayır | En fazla olay sayısı. Tetikleyici, bu özellik tarafından belirtilen bir ve olay sayısı arasında döndürülür. |
   | **Aralık** | Evet | İş akışının sıklık temelinde ne sıklıkta çalışacağını açıklayan pozitif bir tamsayı |
   | **Sıklık** | Evet | Yinelenme için zaman birimi |
   ||||

   **Ek özellikler**

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **İçerik şeması** | Hayır | Olay Hub 'ından okunan olaylara yönelik JSON içerik şeması. Örneğin, içerik şemasını belirtirseniz, mantıksal uygulamayı yalnızca şemayla eşleşen olaylar için tetikleyebilirsiniz. |
   | **Minimum bölüm anahtarı** | Hayır | Okunacak minimum [bölüm](../event-hubs/event-hubs-features.md#partitions) kimliğini girin. Varsayılan olarak, tüm bölümler okunurdur. |
   | **En büyük bölüm anahtarı** | Hayır | Okunacak [bölüm](../event-hubs/event-hubs-features.md#partitions) kimliği üst sınırını girin. Varsayılan olarak, tüm bölümler okunurdur. |
   | **Saat dilimi** | Hayır | Yalnızca bir başlangıç saati belirttiğinizde geçerlidir çünkü bu tetikleyici UTC sapmasını kabul etmez. Uygulamak istediğiniz saat dilimini seçin. <p>Daha fazla bilgi için bkz. [Azure Logic Apps ile yinelenen görevler ve iş akışları oluşturma ve çalıştırma](../connectors/connectors-native-recurrence.md). |
   | **Başlangıç saati** | Hayır | Bu biçimde bir başlangıç saati sağlayın: <p>YYYY-MM-DDThh: mm: ss saat dilimi seçerseniz<p>-veya-<p>YYYY-MM-DDThh: mm: ssZ saat dilimi seçme<p>Daha fazla bilgi için bkz. [Azure Logic Apps ile yinelenen görevler ve iş akışları oluşturma ve çalıştırma](../connectors/connectors-native-recurrence.md). |
   ||||

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

1. Şimdi, tetikleyici sonuçlarıyla gerçekleştirmek istediğiniz görevler için mantıksal uygulamanıza bir veya daha fazla eylem eklemeye devam edin. 

   Örneğin, olayları bir kategori gibi belirli bir değere göre filtrelemek için, **olay Gönder** eyleminin yalnızca koşullarınızı karşılayan olayları göndermesi için bir koşul ekleyebilirsiniz. 

> [!NOTE]
> Tüm Olay Hub 'ı Tetikleyicileri *uzun yoklama* tetiklerdir, bu da bir tetikleyici tetiklendiğinde tetikleyicinin tüm olayları işlediği ve daha fazla olayın olay hub 'ında görünmesi için 30 saniye bekler.
> 30 saniye içinde hiçbir olay alınmıyorsa, tetikleyici çalıştırması atlanır. Aksi takdirde, tetikleyici olay hub 'ınız boş olana kadar olayları okumaya devam eder.
> Sonraki tetikleyici yoklama, tetikleyicisinin özelliklerinde belirttiğiniz yinelenme aralığına göre yapılır.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Event Hubs eylem Ekle

Azure Logic Apps bir [eylem](../logic-apps/logic-apps-overview.md#logic-app-concepts) , iş akışınızda bir tetikleyiciyi veya başka bir eylemi izleyen bir adımdır. Bu örnekte, mantıksal uygulama, Olay Hub 'ınızdaki yeni olayları denetleyen Event Hubs tetikleyicisiyle başlar.

1. Azure portal veya Visual Studio 'da mantıksal uygulamanızı Logic Apps tasarımcısında açın. Bu örnek Azure portal kullanır.

1. Tetikleyici veya eylem altında **yeni adım**' ı seçin.

   Varolan adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyın. 
   Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak "Olay Hub 'ları" yazın.
Eylemler listesinden şu eylemi seçin: **Olay gönderme-Event Hubs**

   !["Olay Gönder" eylemini seçin](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Bağlantı ayrıntıları istenirse [Event Hubs bağlantınızı hemen oluşturun](#create-connection). 

1. Eylemde, göndermek istediğiniz olaylar hakkında bilgi sağlayın. Daha fazla özellik için **yeni parametre Ekle** listesini açın. Bir parametre seçilmesi bu özelliği eylem kartına ekler.

   ![Olay Hub 'ı adını seçin ve olay içeriğini sağlayın](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **Olay hub'ı adı** | Evet | Olayı göndermek istediğiniz olay hub 'ı |
   | **İçeriği** | Hayır | Göndermek istediğiniz olay için içerik |
   | **Özelliklerinin** | Hayır | Gönderilen uygulama özellikleri ve değerler |
   | **Bölüm anahtarı** | Hayır | Olayın gönderileceği [bölüm](../event-hubs/event-hubs-features.md#partitions) kimliği |
   ||||

   Örneğin, Event Hubs tetikleyicinizden çıktıyı başka bir olay hub 'ına gönderebilirsiniz:

   ![Olay örneği gönder](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Olay Hub 'ınıza bağlanın

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Bağlantı bilgileri istendiğinde, şu ayrıntıları sağlayın:

   | Özellik | Gerekli | Value | Açıklama |
   |----------|----------|-------|-------------|
   | **Bağlantı Adı** | Evet | <*bağlantı adı*> | Bağlantınız için oluşturulacak ad |
   | **Event Hubs ad alanı** | Evet | <*Olay-Hub 'lar-ad alanı*> | Kullanmak istediğiniz Event Hubs ad alanını seçin. |
   |||||  

   Örneğin:

   ![Olay Hub 'ı bağlantısı oluştur](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Bağlantı dizesini el ile girmek için **bağlantı bilgilerini el ile girin**' i seçin. 
   [Bağlantı dizenizi bulmayı](#permissions-connection-string)öğrenin.

2. Henüz seçili değilse, kullanılacak Event Hubs ilkesini seçin. **Oluştur**’u seçin.

   ![Olay Hub 'ı bağlantısı oluşturma, Bölüm 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Bağlantınızı oluşturduktan sonra [Event Hubs tetikleyici Ekle](#add-trigger) veya [Event Hubs eylem Ekle](#add-action)' ye geçin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) dosyasında açıklandığı gibi Tetikleyiciler, Eylemler ve sınırlar gibi teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/eventhubs/)bakın.

## <a name="next-steps"></a>Sonraki adımlar

Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin