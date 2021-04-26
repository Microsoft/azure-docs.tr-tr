---
title: Yinelenen görevleri ve iş akışlarını zamanlama
description: Yineleme tetikleyicisiyle yinelenen otomatik görevleri ve iş akışlarını zamanlayın ve çalıştırın Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 3749a7080bf17c020b48ae3ebc3cff3aa998eeef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382302"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Azure Logic Apps 'de yinelenme tetikleyicisiyle yinelenen görevleri ve iş akışlarını oluşturma, zamanlama ve çalıştırma

Görevleri, işlemleri veya işleri Belirli bir zamanlamaya göre düzenli olarak çalıştırmak için, mantıksal uygulama iş akışınızı, Azure Logic Apps yerel olarak çalışan yerleşik **yinelenme** tetikleyicisiyle başlatabilirsiniz. İş akışını başlatmak için bir tarih ve saat ve zaman dilimini ve bu iş akışını yinelemek için bir yinelenme belirleyebilirsiniz. Tetikleyici, örneğin kesintiler veya devre dışı iş akışları nedeniyle herhangi bir nedenden dolayı yinelenmezse, bu tetikleyici atlanan tekrarları işlemez ancak sonraki zamanlanan aralıkta yinelenmeleri yeniden başlatır. Yerleşik zamanlama Tetikleyicileri ve eylemleri hakkında daha fazla bilgi için bkz. [Azure Logic Apps ile yinelenen otomatik, görev ve iş akışlarını zamanlama ve çalıştırma](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

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

* Azure aboneliği. Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* [Logic Apps](../logic-apps/logic-apps-overview.md)hakkında temel bilgi. Logic Apps 'e yeni başladıysanız, [ilk mantıksal uygulamanızı oluşturmayı](../logic-apps/quickstart-create-first-logic-app-workflow.md)öğrenin.

## <a name="add-the-recurrence-trigger"></a>Yinelenme tetikleyicisini ekleme

1. [Azure portalında](https://portal.azure.com) oturum açın. Boş bir mantıksal uygulama oluşturma.

1. Mantıksal uygulama Tasarımcısı görüntülendikten sonra arama kutusuna `recurrence` filtreniz olarak girin. Tetikleyiciler listesinden, mantıksal uygulama iş akışınızın ilk adımı olarak bu tetikleyiciyi seçin: **yinelenme**

   !["Yinelenme" tetikleyicisi seçin](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Yinelenme aralığını ve sıklığını ayarlayın. Bu örnekte, iş akışınızı her hafta çalıştırmak için bu özellikleri ayarlayın.

   ![Aralık ve sıklığı ayarlama](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Özellik | JSON adı | Gerekli | Tür | Description |
   |----------|-----------|----------|------|-------------|
   | **Aralık** | `interval` | Yes | Tamsayı | İş akışının sıklık temelinde ne sıklıkta çalışacağını açıklayan pozitif bir tamsayı. En düşük ve en büyük aralıklar aşağıda verilmiştir: <p>-Ay: 1-16 ay <br>-Hafta: 1-71 hafta <br>Gün: 1-500 gün <br>-Saat: 1-12000 saat <br>-Dakika: 1-72000 dakika <br>-İkinci: 1-9999999 saniye<p>Örneğin, Aralık 6 ve Sıklık "month" ise, yinelenme 6 aydır. |
   | **Sıklık** | `frequency` | Evet | Dize | Yinelenme için zaman birimi: **saniye**, **dakika**, **saat**, **gün**, **hafta** veya **ay** |
   ||||||

   > [!IMPORTANT]
   > Bir yinelenme belirli bir [Başlangıç tarihi ve saati](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)belirtmezse, bu, tetikleyicinizin yinelenme kurulumuna rağmen mantıksal uygulamayı kaydettiğinizde veya dağıtırken ilk yinelenme hemen çalışır. Bu davranışı önlemek için, ilk tekrarın çalıştırmak istediğiniz zaman için bir başlangıç tarihi ve saati belirtin.
   >
   > Bir yinelenme gelecekteki tekrarları çalıştırmak için belirli saatler gibi başka Gelişmiş zamanlama seçenekleri belirtmezse, bu Yinelenmeler son çalışma zamanına göre yapılır. Sonuç olarak, bu Yinelenmeler için başlangıç zamanları, depolama aramaları sırasında gecikme süresi gibi faktörlerin düşmesine neden olmuş olabilirler. 
   > Mantıksal uygulamanızın bir yinelemeyi kaçırmayın, özellikle sıklık gün veya daha uzun bir süre içinde olduğundan emin olmak için şu seçenekleri deneyin:
   > 
   > * **Bu saatlere** ve yalnızca **gün** ve **hafta** sıklıklarında kullanılabilen özellikleri **kullanarak,** tekrarların ve sonraki yinelenmelerde çalıştırılacak belirli sürelerde bir başlangıç tarihi ve saati belirtin.
   > 
   > * Yineleme tetikleyicisi yerine [kayan pencere tetikleyicisini](../connectors/connectors-native-sliding-window.md)kullanın.

1. Gelişmiş zamanlama seçeneklerini ayarlamak için **yeni parametre Ekle** listesini açın. Seçtiğiniz tüm seçenekler, seçimden sonra tetikde görünür.

   ![Gelişmiş zamanlama seçenekleri](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Özellik | JSON adı | Gerekli | Tür | Description |
   |----------|-----------|----------|------|-------------|
   | **Saat dilimi** | `timeZone` | Hayır | Dize | Yalnızca bir başlangıç saati belirttiğinizde geçerlidir çünkü bu tetikleyici [UTC sapmasını](https://en.wikipedia.org/wiki/UTC_offset)kabul etmez. Uygulamak istediğiniz saat dilimini seçin. |
   | **Başlangıç zamanı** | `startTime` | Hayır | Dize | Gelecekte en fazla 49 yıl olan bir başlangıç tarihi ve saati sağlayın ve UTC [8601 tarih saat belirtimini](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) UTC [Tarih saat biçiminde](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)izlemelidir, ancak [UTC bir fark](https://en.wikipedia.org/wiki/UTC_offset)olmadan: <p><p>YYYY-MM-DDThh: mm: ss saat dilimi seçerseniz <p>-veya- <p>YYYY-MM-DDThh: mm: ssZ saat dilimi seçme <p>Örneğin, 18 Eylül 2020, 2:00 PM üzerinde istiyorsanız, "2020-09-18T14:00:00" belirtin ve Pasifik standart saati gibi bir saat dilimi seçin. Ya da bir saat dilimi olmadan "2020-09-18T14:00:00Z" belirtin. <p><p>**Önemli:** Bir saat dilimi seçmezseniz, sonunda boşluk olmadan "Z" harfini eklemeniz gerekir. Bu "Z", eşdeğer [nadeniz saati](https://en.wikipedia.org/wiki/Nautical_time)anlamına gelir. Bir saat dilimi değeri seçerseniz, **Başlangıç zamanı** değerinin sonuna bir "Z" eklemeniz gerekmez. Bunu yaparsanız, "Z" UTC saat biçimini belirten Logic Apps saat dilimi değerini yoksayar. <p><p>Basit zamanlamalar için başlangıç zamanı ilk oluşumdır, ancak karmaşık zamanlamalar için tetikleyici başlangıç zamanından daha önce harekete geçmez. [*Başlangıç tarihini ve saatini kullanmanın yolları nelerdir?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Şu günlerde** | `weekDays` | No | Dize veya dize dizisi | "Hafta" seçeneğini belirlerseniz, iş akışını çalıştırmak istediğinizde bir veya daha fazla gün seçebilirsiniz: **Pazartesi**, **Salı**, **Çarşamba**, **Perşembe**, **Cuma**, **Cumartesi** ve **Pazar** |
   | **Şu saatlerde** | `hours` | No | Tamsayı veya tamsayı dizisi | "Gün" veya "hafta" seçeneğini belirlerseniz, iş akışını çalıştırmak istediğiniz günün saati olarak 0 ile 23 arasında bir veya daha fazla tamsayı seçebilirsiniz. <p><p>Örneğin, "10", "12" ve "14" belirtirseniz günün saati için 10 har, 12 PM ve 2 PM elde edersiniz, ancak günün tutanakları yineleme başladığı zaman temel alınarak hesaplanır. Günün belirli dakikalarını ayarlamak için (örneğin, 10:00, 12:00 PM ve 2:00 PM), **Bu değerleri şu dakikalar** içinde adlı özelliği kullanarak belirtin. |
   | **Şu dakikalarda** | `minutes` | No | Tamsayı veya tamsayı dizisi | "Gün" veya "hafta" seçeneğini belirlerseniz, iş akışını çalıştırmak istediğiniz saatin dakikası olarak 0 ile 59 arasında bir veya daha fazla tamsayı seçebilirsiniz. <p>Örneğin, "30" öğesini dakika işareti olarak belirtebilir ve günün saati için önceki örneği kullanarak 10:30, 12:30 PM ve 2:30 PM kazanın. <p>**Note**: bazen tetiklenen çalıştırmanın zaman damgası, zamanlanan zamandan en fazla 1 dakikalık farklılık gösterebilir. Zaman damgasını sonraki eylemlere tam olarak zamanlandığı şekilde geçirmeniz gerekiyorsa, zaman damgasını uygun şekilde değiştirmek için şablon ifadelerini kullanabilirsiniz. Daha fazla bilgi için bkz. [ifadeler Için tarih ve saat işlevleri](../logic-apps/workflow-definition-language-functions-reference.md#date-time-functions). |
   |||||

   Örneğin, bugünden itibaren 4 Eylül 2020 ' in Cuma olduğunu varsayalım. Aşağıdaki yineleme tetikleyicisi başlangıç tarihi ve saatinden daha *önce* harekete geçmez. Bu, 18 Eylül 2020, 8:00. pst ' de 00 ' dır. Ancak, yineleme zamanlaması 10:30, 12:30 PM ve 2:30 PM için yalnızca Mondays için ayarlanır. Bu nedenle tetikleyici ilk kez tetiklendiğinde ve bir Logic App iş akışı örneği oluşturduğunda Pazartesi günü 10:30 ' de olur. Başlangıç sürelerinin nasıl çalıştığı hakkında daha fazla bilgi edinmek için bu [Başlangıç saati örneklerine](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)bakın.

   Gelecekteki çalıştırmalar, aynı günde 12:30 PM ve 2:30 PM 'de gerçekleşir. Her yinelenme kendi iş akışı örneğini oluşturur. Bundan sonra, tüm zamanlama sonraki Pazartesi günü tekrar yinelenir. [*Diğer örnek örnekleri nelerdir?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Gelişmiş zamanlama örneği](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

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
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

<a name="daylight-saving-standard-time"></a>

## <a name="trigger-recurrence-shift-between-daylight-saving-time-and-standard-time"></a>Gün ışığından yararlanma saati ve standart saat arasında yineleme değiştirme tetikleme

Yinelenen yerleşik Tetikleyiciler, belirlediğiniz zaman dilimi dahil olmak üzere ayarladığınız zamanlamayı kabul ediyor. Bir saat dilimi seçmezseniz, Tetikleyiciler çalıştırıldığında gün ışığından yararlanma saati (DST) etkileyebilir. Örneğin, DST başlatıldığında başlangıç saatini bir saat ileri ve DST sona erdiğinde bir saat geriye doğru kaydırma.

Mantıksal uygulamanızın belirtilen başlangıç saatinde çalışması için bu vardiyanın önüne geçmek için bir saat dilimi seçtiğinizden emin olun. Bu şekilde, mantıksal uygulamanız için UTC saati de mevsimlik zaman değişikliğine karşı değişir. Ancak, bazı durumlarda Windows zaman vardiyada sorunlara neden olabilir. Daha fazla bilgi ve örnek için bkz. [gün ışığından yararlanma saati ve standart saat Için yinelenme](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

## <a name="next-steps"></a>Sonraki adımlar

* [Gecikme eylemleriyle iş akışlarını duraklatma](../connectors/connectors-native-delay.md)
* [Logic Apps için bağlayıcılar](../connectors/apis-list.md)
