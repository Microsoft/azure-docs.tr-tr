---
title: Azure Stream Analytics işleri için uyarıları izleme işlevini ayarlama
description: Bu makalede, izleme ve Azure Stream Analytics işleri için uyarılar ayarlamak için Azure portalını kullanmayı açıklar.
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 0fd489d856a16953a5a450a347c9737fe440ad28
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621768"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics işleri için uyarıları ayarlama

İş sürekli olarak sorunsuz çalıştığından emin olmak için Azure Stream Analytics işinizi izlemek önemlidir. Bu makalede, izlenmesi gereken genel senaryolar için uyarıları ayarlama açıklar. 

Portal üzerinden işlem günlükleri verilerden ölçümlere ilişkin kurallar tanımlayabilirsiniz yanı [program aracılığıyla](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a).

## <a name="set-up-alerts-in-the-azure-portal"></a>Azure portalında uyarıları ayarlama
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Bir işi beklenmedik bir şekilde durduruluyor olduğunda uyarı alın

Aşağıdaki örnek, işinizi başarısız durumda girdiğinde için uyarıları ayarlama gösterilmektedir. Bu uyarı, tüm işler için önerilir.

1. Stream Analytics işi için bir uyarı oluşturmak istediğiniz Azure portalında açın.

2. Üzerinde **iş** sayfasında, gitmek **izleme** bölümü.  

3. Seçin **ölçümleri**, ardından **yeni uyarı kuralı**.

   ![Azure portalında Stream Analytics uyarıları Kurulumu](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Stream Analytics işinizin adı altında otomatik olarak görünmelidir **kaynak**. Tıklayın **koşul Ekle**seçip **tüm yönetim işlemlerini** altında **sinyal mantığını yapılandırma**.

   ![Stream Analytics uyarı için sinyal adı seçin](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Altında **sinyal mantığını yapılandırma**, değiştirme **olay düzeyi** için **tüm** değiştirip **durumu** için **başarısız** . Bırakın **olayı başlatan tarafından** seçin ve boş **Bitti**.

   ![Stream Analytics uyarı için sinyal mantığını yapılandırma](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Var olan bir eylem grubu seçin veya yeni bir grup oluşturun. Bu örnekte, yeni bir eylem grubu adı verilen **TIDashboardGroupActions** ile oluşturulmuş bir **e-postaları** sahip kullanıcılar bir e-posta gönderen eylemi **sahibi** Azure kaynak Yönetici rolü.

   ![Azure akış analizi işi için uyarı ayarlama](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. **Kaynak**, **koşul**, ve **Eylem grupları** her bir giriş olmalıdır. Uyarıların tetikleneceği sırada tanımlanan koşullar karşılanması gerektiğini unutmayın. Örneğin, son 15 dakika için ölçümün ortalama değerini her 5 dakikada bir ölçebilirsiniz.

   ![Stream Analytics uyarı kuralı oluşturma](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Ekleme bir **uyarı kuralı adı**, **açıklama**ve **kaynak grubu** için **uyarı ayrıntıları** tıklatıp **uyarı oluştur Kural** Stream Analytics işiniz için kural oluşturma.

   ![Stream Analytics uyarı kuralı oluşturma](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>İzleme senaryoları

Stream Analytics işinizin performansını izlemek için aşağıdaki uyarıları önerilir. Bu ölçümler son 5 dakika boyunca dakika başı değerlendirilmelidir.

|Ölçüm|Koşul|Zaman toplama|Eşik|Düzeltme eylemleri|
|-|-|-|-|-|
|SU kullanım yüzdesi|Büyüktür|Maksimum|80|SU kullanım yüzdesi artıran çok etken vardır. Sorgu paralelleştirmesiyle ölçeklendirme veya akış birimi sayısını artırabilirsiniz. Daha fazla bilgi için bkz. [Azure Stream Analytics'te sorgu paralelleştirmesinden yararlanma](stream-analytics-parallelization.md).|
|Çalışma zamanı hataları|Büyüktür|Toplam|0|Etkinlik veya tanılama günlüklerini inceleyin ve giriş, sorgu veya çıkış için uygun değişiklikleri yapın.|
|Eşik gecikmesi|Büyüktür|Maksimum|Son 15 dakika boyunca Bu ölçümün ortalama değerini (saniye cinsinden) geç varış toleransı büyük olduğunda. Geç varış toleransı değiştirmediyseniz varsayılan 5 saniye olarak ayarlanır.|Deneyin SUs sayısının artırılması veya sorguyu paralelleştirmek. SUs hakkında daha fazla bilgi için bkz. [anlayın ve akış birimi Ayarla](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Sorguyu paralelleştirmek daha fazla bilgi için bkz: [Azure Stream analytics'te sorgu paralelleştirmesinden](stream-analytics-parallelization.md).|
|Giriş serileştirme kaldırma hataları|Büyüktür|Toplam|0|Etkinlik veya tanılama günlüklerine inceleyin ve uygun değişiklikleri giriş yapın. Tanılama günlükleri hakkında daha fazla bilgi için bkz. [Azure Stream tanılama günlüklerini kullanarak Analiz sorunlarını giderme](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Yardım alın

Azure portalında uyarıları yapılandırma hakkında daha fazla ayrıntı için [uyarı bildirimleri alma](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

Daha fazla yardım için deneyin bizim [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-get-started.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)

