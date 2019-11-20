---
title: Sürekli verileri işleyecek görevleri zamanlayın-Azure Logic Apps
description: Azure Logic Apps ' de kayan pencereleri kullanarak bitişik verileri işleyen yinelenen görevleri oluşturun ve çalıştırın
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0312d9480d00d4430cd5d42dc22ef9dac005ee2e
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679057"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Azure Logic Apps içindeki kayan pencere tetikleyicisini kullanarak ardışık veriler için görevleri zamanlayın ve çalıştırın

Sürekli öbeklerdeki verileri işlemesi gereken görevleri, işlemleri veya işleri düzenli olarak çalıştırmak için, mantıksal uygulama iş akışınızı **kayan pencere** tetikleyicisi ile başlatabilirsiniz. İş akışını başlatmak için bir tarih ve saat ve zaman dilimini ve bu iş akışını yinelemek için bir yinelenme belirleyebilirsiniz. Herhangi bir nedenle Yinelenmeler kaçırıldığında, bu tetikleyici bu atlanan tekrarları işler. Örneğin, veritabanınız ve yedekleme depolama alanı arasında verileri eşitlerken, verilerin boşluklar olmadan eşitlenmesi için kayan pencere tetikleyicisini kullanın. Yerleşik zamanlama Tetikleyicileri ve eylemleri hakkında daha fazla bilgi için bkz. [Azure Logic Apps ile yinelenen otomatik, görev ve iş akışlarını zamanlama ve çalıştırma](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Bu tetikleyicinin desteklediği bazı desenler aşağıda verilmiştir:

* Hemen çalıştırın ve her *n* saniye, dakika veya saat sayısını yineleyin.

* Belirli bir tarih ve saatte başlayıp, her *n* saniye, dakika veya saat sonra çalıştırın ve tekrarlayın. Bu tetikleyici ile, geçmişte tüm geçmiş yinelenmeleri çalıştıran bir başlangıç saati belirtebilirsiniz.

* Çalışmadan önce belirli bir süre için her yinelemeyi geciktir.

Bu tetikleyici ile yineleme tetikleyicisi arasındaki farklar veya yinelenen iş akışlarının zamanlanması hakkında daha fazla bilgi için, bkz. [Azure Logic Apps ile yinelenen otomatikleştirilmiş görevler, süreçler ve iş akışlarını zamanlama ve çalıştırma](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Mantıksal uygulamanızı tetiklemek ve gelecekte yalnızca bir kez çalıştırmak istiyorsanız, bkz. [işleri yalnızca bir kez çalıştır](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolabilirsiniz](https://azure.microsoft.com/free/).

* [Logic Apps](../logic-apps/logic-apps-overview.md)hakkında temel bilgi. Logic Apps 'e yeni başladıysanız, [ilk mantıksal uygulamanızı oluşturmayı](../logic-apps/quickstart-create-first-logic-app-workflow.md)öğrenin.

## <a name="add-sliding-window-trigger"></a>Kayan pencere tetikleyicisi Ekle

1. [Azure Portal](https://portal.azure.com)’ında oturum açın. Boş bir mantıksal uygulama oluşturma.

1. Mantıksal uygulama Tasarımcısı görüntülendikten sonra arama kutusuna filtreniz olarak "kayan pencere" yazın. Tetikleyiciler listesinden, mantıksal uygulama iş akışınızın ilk adımı olarak bu tetikleyiciyi seçin: **kayan pencere**

   !["Kayan pencere" tetikleyicisi seçin](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Yinelenme aralığını ve sıklığını ayarlayın. Bu örnekte, iş akışınızı her hafta çalıştırmak için bu özellikleri ayarlayın.

   ![Aralık ve sıklığı ayarlama](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Özellik | Gereklidir | JSON adı | Tür | Açıklama |
   |----------|----------|-----------|------|-------------|
   | **Aralık** | Yes | interval | Tamsayı | İş akışının sıklık temelinde ne sıklıkta çalışacağını açıklayan pozitif bir tamsayı. En düşük ve en büyük aralıklar aşağıda verilmiştir: <p>-Saat: 1-12000 saat </br>-Dakika: 1-72000 dakika </br>-İkinci: 1-9999999 saniye<p>Örneğin, Aralık 6, sıklık ise "saat" ise yinelenme 6 saattir. |
   | **Sıklık** | Yes | frequency | Dize | Yinelenme için zaman birimi: **saniye**, **dakika**veya **saat** |
   ||||||

   ![Gelişmiş yinelenme seçenekleri](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Daha fazla yinelenme seçeneği için **yeni parametre Ekle** listesini açın. 
   Seçtiğiniz tüm seçenekler, seçimden sonra tetikde görünür.

   | Özellik | Gereklidir | JSON adı | Tür | Açıklama |
   |----------|----------|-----------|------|-------------|
   | **İlir** | Hayır | ilir | Dize | [Iso 8601 tarih saat belirtimini](https://en.wikipedia.org/wiki/ISO_8601#Durations) kullanarak her tekrarın geciktirime süresi |
   | **Saat dilimi** | Hayır | timeZone | Dize | Yalnızca bir başlangıç saati belirttiğinizde geçerlidir çünkü bu tetikleyici [UTC sapmasını](https://en.wikipedia.org/wiki/UTC_offset)kabul etmez. Uygulamak istediğiniz saat dilimini seçin. |
   | **Başlangıç saati** | Hayır | startTime | Dize | Bu biçimde bir başlangıç tarihi ve saati belirtin: <p>YYYY-MM-DDThh: mm: ss saat dilimi seçerseniz <p>-veya- <p>YYYY-MM-DDThh: mm: ssZ saat dilimi seçme <p>Örneğin, 18 Eylül 2017, 2:00 PM üzerinde istiyorsanız, "2017-09-18T14:00:00" belirtin ve Pasifik standart saati gibi bir saat dilimi seçin. Ya da saat dilimi olmadan "2017-09-18T14:00:00Z" belirtin. <p>**Note:** Bu başlangıç saati UTC [8601 tarih saat belirtimini](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) [UTC Tarih saat biçiminde](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)izlemelidir, ancak [UTC boşluğu](https://en.wikipedia.org/wiki/UTC_offset)olmadan gelmelidir. Bir saat dilimi seçmezseniz, sonunda boşluk olmadan "Z" harfini eklemeniz gerekir. Bu "Z", eşdeğer [nadeniz saati](https://en.wikipedia.org/wiki/Nautical_time)anlamına gelir. <p>Basit zamanlamalar için başlangıç zamanı ilk oluşumdır, ancak gelişmiş Yinelenmeler için tetikleyici başlangıç zamanından daha önce harekete geçmez. [*Başlangıç tarihini ve saatini kullanmanın yolları nelerdir?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Şimdi diğer eylemlerle kalan iş akışınızı derleyin. Ekleyebileceğiniz daha fazla eylem için bkz. [bağlayıcılar Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>İş akışı tanımı-kayan pencere

Mantıksal uygulamanızın, JSON kullanan temel alınan iş akışı tanımında, kayan pencere tetikleme tanımını seçtiğiniz seçeneklerle görüntüleyebilirsiniz. Bu tanımı görüntülemek için tasarımcı araç çubuğunda **kod görünümü**' ne tıklayın. Tasarımcıya dönmek için tasarımcı araç çubuğunda **Tasarımcı**' yı seçin.

Bu örnekte, bir kayan pencere tetikleme tanımının, saatlik yineleme için her yinelenme gecikmesi beş saniye olduğu bir temel alınan iş akışı tanımına nasıl görünebileceği gösterilmektedir:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [İş akışlarında sonraki eylemi geciktir](../connectors/connectors-native-delay.md)
* [Logic Apps için bağlayıcılar](../connectors/apis-list.md)