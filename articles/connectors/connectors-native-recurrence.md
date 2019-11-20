---
title: Yinelenen görevleri ve iş akışlarını zamanlama-Azure Logic Apps
description: Yineleme tetikleyicisiyle yinelenen otomatik görevleri ve iş akışlarını zamanlayın ve çalıştırın Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 8715bbcb6926fbfc2f77bd05d0ce036a6255eb85
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679070"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Azure Logic Apps 'de yinelenme tetikleyicisiyle yinelenen görevleri ve iş akışlarını oluşturma, zamanlama ve çalıştırma

Görevleri, işlemleri veya işleri Belirli bir zamanlamaya göre düzenli olarak çalıştırmak için, mantıksal uygulama iş akışınızı yerleşik **yinelenme zamanlaması** tetikleyicisiyle başlatabilirsiniz. İş akışını başlatmak için bir tarih ve saat ve zaman dilimini ve bu iş akışını yinelemek için bir yinelenme belirleyebilirsiniz. Herhangi bir nedenle Yinelenmeler kaçırıldığında, bu tetikleyici zamanlanan bir sonraki aralıkta tekrarlamaya devam eder. Yerleşik zamanlama Tetikleyicileri ve eylemleri hakkında daha fazla bilgi için bkz. [Azure Logic Apps ile yinelenen otomatik, görev ve iş akışlarını zamanlama ve çalıştırma](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Daha gelişmiş Yinelenmeler ve karmaşık zamanlamalarla birlikte bu tetikleyicinin desteklediği bazı desenler aşağıda verilmiştir:

* Hemen çalıştırın ve her *n* saniye, dakika, saat, gün, hafta veya ay sayısını yineleyin.

* Belirli bir tarih ve saatte başlayıp, her *n* saniye, dakika, saat, gün, hafta veya ay çalıştırın ve tekrarlayın.

* Her gün bir veya daha fazla kez çalıştırın, örneğin, 8:00 ve 5:00 PM.

* Her hafta yalnızca Cumartesi ve Pazar gibi belirli günler için çalıştırın ve tekrarlayın.

* Her hafta bir kez çalıştırın ve tekrarlayın, ancak yalnızca belirli günler ve saatler için (örneğin, Pazartesi ile Cuma arası, 8:00 ve 5:00 PM).

Bu tetikleyici ve kayan pencere tetikleyicisi arasındaki farklar veya yinelenen iş akışlarını zamanlama hakkında daha fazla bilgi için, bkz. [Azure Logic Apps ile yinelenen otomatik görevler, süreçler ve iş akışlarını zamanlama ve çalıştırma](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Mantıksal uygulamanızı tetiklemek ve gelecekte yalnızca bir kez çalıştırmak istiyorsanız, bkz. [işleri yalnızca bir kez çalıştır](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolabilirsiniz](https://azure.microsoft.com/free/).

* [Logic Apps](../logic-apps/logic-apps-overview.md)hakkında temel bilgi. Logic Apps 'e yeni başladıysanız, [ilk mantıksal uygulamanızı oluşturmayı](../logic-apps/quickstart-create-first-logic-app-workflow.md)öğrenin.

## <a name="add-recurrence-trigger"></a>Yinelenme tetikleyicisi Ekle

1. [Azure Portal](https://portal.azure.com)’ında oturum açın. Boş bir mantıksal uygulama oluşturma.

1. Mantıksal uygulama Tasarımcısı görüntülendikten sonra arama kutusuna filtreniz olarak "yinelenme" yazın. Tetikleyiciler listesinden, mantıksal uygulama iş akışınızın ilk adımı olarak bu tetikleyiciyi seçin: **yinelenme**

   !["Yinelenme" tetikleyicisi seçin](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Yinelenme aralığını ve sıklığını ayarlayın. Bu örnekte, iş akışınızı her hafta çalıştırmak için bu özellikleri ayarlayın.

   ![Aralık ve sıklığı ayarlama](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Özellik | Gereklidir | JSON adı | Tür | Açıklama |
   |----------|----------|-----------|------|-------------|
   | **Aralık** | Yes | interval | Tamsayı | İş akışının sıklık temelinde ne sıklıkta çalışacağını açıklayan pozitif bir tamsayı. En düşük ve en büyük aralıklar aşağıda verilmiştir: <p>-Ay: 1-16 ay </br>Gün: 1-500 gün </br>-Saat: 1-12000 saat </br>-Dakika: 1-72000 dakika </br>-İkinci: 1-9999999 saniye<p>Örneğin, Aralık 6 ve Sıklık "month" ise, yinelenme 6 aydır. |
   | **Sıklık** | Yes | frequency | Dize | Yinelenme için zaman birimi: **saniye**, **dakika**, **saat**, **gün**, **hafta**veya **ay** |
   ||||||

   Daha fazla zamanlama seçeneği için **yeni parametre Ekle** listesini açın. 
   Seçtiğiniz tüm seçenekler, seçimden sonra tetikde görünür.

   ![Gelişmiş zamanlama seçenekleri](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Özellik | Gereklidir | JSON adı | Tür | Açıklama |
   |----------|----------|-----------|------|-------------|
   | **Saat dilimi** | Hayır | timeZone | Dize | Yalnızca bir başlangıç saati belirttiğinizde geçerlidir çünkü bu tetikleyici [UTC sapmasını](https://en.wikipedia.org/wiki/UTC_offset)kabul etmez. Uygulamak istediğiniz saat dilimini seçin. |
   | **Başlangıç saati** | Hayır | startTime | Dize | Bu biçimde bir başlangıç tarihi ve saati belirtin: <p>YYYY-MM-DDThh: mm: ss saat dilimi seçerseniz <p>-veya- <p>YYYY-MM-DDThh: mm: ssZ saat dilimi seçme <p>Örneğin, 18 Eylül 2017, 2:00 PM üzerinde istiyorsanız, "2017-09-18T14:00:00" belirtin ve Pasifik standart saati gibi bir saat dilimi seçin. Ya da saat dilimi olmadan "2017-09-18T14:00:00Z" belirtin. <p>**Note:** Bu başlangıç saati, gelecekte en fazla 49 yıla sahiptir ve UTC [8601 tarih saat belirtimini](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) UTC [Tarih saat biçiminde](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)izlemelidir, ancak [UTC bir fark](https://en.wikipedia.org/wiki/UTC_offset)olmadan gelmelidir. Bir saat dilimi seçmezseniz, sonunda boşluk olmadan "Z" harfini eklemeniz gerekir. Bu "Z", eşdeğer [nadeniz saati](https://en.wikipedia.org/wiki/Nautical_time)anlamına gelir. <p>Basit zamanlamalar için başlangıç zamanı ilk oluşumdır, ancak karmaşık zamanlamalar için tetikleyici başlangıç zamanından daha önce harekete geçmez. [*Başlangıç tarihini ve saatini kullanmanın yolları nelerdir?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Şu günlerde** | Hayır | weekDays | Dize veya dize dizisi | "Hafta" seçeneğini belirlerseniz, iş akışını çalıştırmak istediğinizde bir veya daha fazla gün seçebilirsiniz: **Pazartesi**, **Salı**, **Çarşamba**, **Perşembe**, **Cuma**, **Cumartesi**ve **Pazar** |
   | **Şu saatlerde** | Hayır | saat | Tamsayı veya tamsayı dizisi | "Gün" veya "hafta" seçeneğini belirlerseniz, iş akışını çalıştırmak istediğiniz günün saati olarak 0 ile 23 arasında bir veya daha fazla tamsayı seçebilirsiniz. <p><p>Örneğin, "10", "12" ve "14" belirtirseniz günün saati için 10 har, 12 PM ve 2 PM elde edersiniz, ancak günün tutanakları yineleme başladığı zaman temel alınarak hesaplanır. Günün dakikalarını ayarlamak için, **Bu dakika** özelliği için değeri belirtin. |
   | **Şu dakikalarda** | Hayır | dakika | Tamsayı veya tamsayı dizisi | "Gün" veya "hafta" seçeneğini belirlerseniz, iş akışını çalıştırmak istediğiniz saatin dakikası olarak 0 ile 59 arasında bir veya daha fazla tamsayı seçebilirsiniz. <p>Örneğin, "30" öğesini dakika işareti olarak belirtebilir ve günün saati için önceki örneği kullanarak 10:30, 12:30 PM ve 2:30 PM kazanın. |
   |||||

   Örneğin, bugün Pazartesi, 4 Eylül 2017 olduğunu varsayalım. Aşağıdaki yineleme tetikleyicisi başlangıç tarihi ve saatinden daha *önce* harekete geçmez. Bu, 18 Eylül 2017, 8:00. pst ' de 00 ' dır. Ancak, yineleme zamanlaması 10:30, 12:30 PM ve 2:30 PM için yalnızca Mondays için ayarlanır. Bu nedenle tetikleyici ilk kez ateşlenir ve bir Logic App iş akışı örneği oluşturduğunda 10:30 ' de olur. Başlangıç sürelerinin nasıl çalıştığı hakkında daha fazla bilgi edinmek için bu [Başlangıç saati örneklerine](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)bakın.

   Gelecekteki çalıştırmalar, aynı günde 12:30 PM ve 2:30 PM 'de gerçekleşir. Her yinelenme kendi iş akışı örneğini oluşturur. Bundan sonra, tüm zamanlama sonraki Pazartesi günü tekrar yinelenir. [*Diğer örnek örnekleri nelerdir?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Gelişmiş zamanlama örneği](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Tetikleyici, yalnızca sıklık olarak "gün" veya "hafta" seçeneğini belirlediğinizde belirttiğiniz yineleme için bir önizleme gösterir.

1. Şimdi diğer eylemlerle kalan iş akışınızı derleyin. Ekleyebileceğiniz daha fazla eylem için bkz. [bağlayıcılar Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>İş akışı tanımı-yinelenme

Mantıksal uygulamanızın, JSON kullanan temel alınan iş akışı tanımında, [yineleme tetikleyicisi tanımını](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) seçtiğiniz seçeneklerle görüntüleyebilirsiniz. Bu tanımı görüntülemek için tasarımcı araç çubuğunda **kod görünümü**' ne tıklayın. Tasarımcıya dönmek için tasarımcı araç çubuğunda **Tasarımcı**' yı seçin.

Bu örnek, bir yinelenme tetikleyicisi tanımının temel alınan bir iş akışı tanımına nasıl görünebileceğini gösterir:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Gecikme eylemleriyle iş akışlarını duraklatma](../connectors/connectors-native-delay.md)
* [Logic Apps için bağlayıcılar](../connectors/apis-list.md)
