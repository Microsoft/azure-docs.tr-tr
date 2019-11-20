---
title: Azure Stream Analytics, tanılama günlüklerini kullanarak sorun giderme
description: Bu makalede, Azure Stream analytics'te tanılama günlüklerini çözümleme açıklar.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 68c40cf893bf150756f0a03056473e82cff5754f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620970"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Azure Stream Analytics, tanılama günlükleri kullanarak sorun giderme

Bazen, bir Azure Stream Analytics işi beklenmedik bir şekilde işlemeyi durdurur. Bu tür bir olay sorun giderme önemlidir. Hatalara beklenmedik bir sorgu sonucu, cihazların bağlantısı veya beklemedik bir hizmet kesintisi neden olmuş olabilir. Stream analytics'te tanılama günlükleri yardımcı olabilecek oluşur ve kurtarma süresini kısaltmak, sorunların nedenini tanımlayın.

Tüm üretim işleri için tanılama günlüklerini etkinleştirme önemle tavsiye edilir.

## <a name="log-types"></a>Günlük türü

Stream Analytics, günlükleri iki tür sunar:

* [Etkinlik günlükleri](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (her zaman açık), işleri üzerinde gerçekleştirilen işlemler hakkında Öngörüler sunar.

* [Tanılama günlükleri](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (yapılandırılabilir), her şeyin daha zengin Öngörüler sağlayan bir işlemle olur. İş silindiğinde tanılama iş oluşturulduğunda başlangıç ve bitiş günlüğe kaydeder. Bunlar, iş güncelleştirildiğinde ve çalışırken olayları kapsar.

> [!NOTE]
> Azure depolama, Azure Event Hubs gibi hizmetleri kullanabilirsiniz ve uyumsuz verileri çözümlemek için Azure İzleyici günlüğe kaydeder. Bu hizmetler için fiyatlandırma modeline göre ücretlendirilirsiniz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Etkinlik kullanarak hata ayıklama günlükleri

Etkinlik günlükleri, varsayılan olarak etkindir ve Stream Analytics işinizi tarafından gerçekleştirilen işlemlerin üst düzey Öngörüler sağlar. Etkinlik günlüklerinde mevcut bilgiler, işinizi etkileyen sorunlardan biri kök nedeni bularak yardımcı olabilir. Stream Analytics'te etkinlik günlüklerini kullanmak için aşağıdaki adımları uygulayın:

1. Azure portal ve Seç'i açın **etkinlik günlüğü** altında **genel bakış**.

   ![Stream Analytics etkinlik günlüğü](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Gerçekleştirilen işlemlerin bir listesi görebilirsiniz. Kırmızı bilgisi kabarcık, işinin başarısız olmasına neden olan herhangi bir işlem var.

3. Bir işlem, Özet görünümü görmek için tıklayın. Buradaki bilgiler sıklıkla sınırlıdır. İşlemi hakkında daha fazla bilgi edinmek için tıklayın **JSON**.

   ![Stream Analytics etkinlik günlüğü işlem özeti](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Ekranı aşağı kaydırarak **özellikleri** bölümünü JSON, başarısız olan işlemi neden olan hatanın ayrıntıları sağlar. Bu örnekte, ilişkili enlem değerleri dışındayken bir çalışma zamanı hatası nedeniyle başarısız oldu. Stream Analytics işi tarafından işlenen veri tutarsızlık bir veri hatasına neden olur. Farklı hakkında bilgi edinebilirsiniz [girdi ve çıktı verilerini hataları ve neden ortaya](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![JSON hatası ayrıntıları](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. JSON hata iletisinde göre düzeltici eylemleri gerçekleştirebilirsiniz. Bu örnekte, enlem-90 derece arasında değerdir ve 90 derece sorguya eklenmesi gerekir emin olmak için denetler.

6. Etkinlik günlükleri hata iletisinde kök nedeni tanımlanmasına yardımcı olmadı, tanılama günlüklerini etkinleştirme ve Azure İzleyici günlüklerine kullanın.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Azure İzleyici günlüklerine Tanılama verileri gönderme

Tanılama günlüklerini kapatılması ve Azure İzleyici günlüklerine göndererek önemle tavsiye edilir. Tanılama günlükleri **kapalı** varsayılan olarak. Tanılama günlüklerini etkinleştirmek için aşağıdaki adımları tamamlayın:

1.  Azure portalında oturum açın ve Stream Analytics işinize gidin. Altında **izleme**seçin **tanılama günlükleri**. Ardından **tanılamayı Aç**.

    ![Tanılama günlükleri dikey penceresinde gezinme](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Oluşturma bir **adı** içinde **tanılama ayarları** ve yanındaki kutuyu işaretleyin **Log Analytics'e gönderme**. Daha sonra mevcut bir ekleyin veya yeni bir **Log analytics çalışma alanı**. İçin onay kutularını işaretleyin **yürütme** ve **yazma** altında **günlük**, ve **AllMetrics** altında **ÖLÇÜM** . **Kaydet**’e tıklayın. Ek maliyetleri önlemek için aynı bölgede Azure Stream Analytics işinizin bir Log Analytics çalışma alanı kullanmanız önerilir.

    ![Tanılama günlükleri için ayarlar](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Stream Analytics işinizi başladığında, tanılama günlükleri Log Analytics çalışma alanınıza yönlendirilir. Log Analytics çalışma alanına gidin ve seçin **günlükleri** altında **genel** bölümü.

   ![Azure İzleyici günlüklerine genel bölümünün altında](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. Yapabilecekleriniz [kendi sorgunuzu yazma](../azure-monitor/log-query/get-started-portal.md) terimleri aramak için eğilimlerini, biçimlerini çözümleme ve verilerinizi temel alan ayrıntılı bilgiler sağlar. Örneğin, iletiyi olan tanılama günlükleri filtrelemek için sorgu "iş akışında başarısız oldu." yazabileceğiniz Azure Stream Analytics'ten gelen tanılama günlükleri depolanır **AzureDiagnostics** tablo.

   ![Tanılama sorgusu ve sonuçları](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. Günlükler için doğru aramaya bir sorgu varsa, seçerek Kaydet **Kaydet** ve bir ad ve kategori sağlayın. Ardından, seçerek bir uyarı oluşturabilirsiniz **yeni uyarı kuralı**. Ardından, Uyarı koşulu belirtin. Seçin **koşul** eşik değeri ve bu özel bir günlük araması değerlendirilir sıklığı girin.  

   ![Tanılama günlük arama sorgusu](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. Eylem grubu seçin ve uyarı kuralı oluşturabilmeniz için önce ad ve açıklama gibi uyarı ayrıntılarını belirtin. Tanılama günlükleri çeşitli işlerinin aynı Log Analytics çalışma alanınıza yönlendirebilir. Bu, tüm işlerde çalışma sonra uyarıları ayarlamak sağlar.  

## <a name="diagnostics-log-categories"></a>Tanılama günlük kategorileri

Azure Stream Analytics, tanılama günlüklerinin iki kategoriye yakalar:

* **Yazma**: Proje oluşturma, ekleme, girdileri ve çıktıları ekleme ve sorgu güncelleştiriliyor ve başlatma veya işi durdurma, silme gibi yazma işlemleri iş ile ilgili günlük olayları yakalar.

* **Yürütme**: İş yürütme sırasında gerçekleşen olayları yakalar.
    * Bağlantı hataları
    * Dahil olmak üzere, veri işleme hataları:
        * Sorgu tanımı (eşleşmeyen alan türleri ve değerleri, eksik alanlar vb.) için uygun olmayan olayları
        * İfade değerlendirme hataları
    * Diğer olayları ve hataları

## <a name="diagnostics-logs-schema"></a>Tanılama günlükleri şeması

Tüm günlükler, JSON biçiminde depolanır. Her girişin aşağıdaki ortak dize alanlara sahiptir:

Ad | Açıklama
------- | -------
time | Zaman damgası (UTC) günlüğü.
resourceId | İşlem büyük harf, yerinde geçen kaynak kimliği. Bu abonelik kimliği, kaynak grubunu ve iş adı içerir. Örneğin,   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMINGJOBS/STREAMANALYTICS/MYSTREAMINGJOB**.
category | Kategori ya da oturum **yürütme** veya **yazma**.
operationName | Oturum açmış işlemin adı. Örneğin, **olaylar gönderin: SQL çıkış yazma hatası için mysqloutput**.
status | İşlemin durumu. Örneğin, **başarısız** veya **başarılı**.
düzey | Günlük düzeyi. Örneğin, **hata**, **uyarı**, veya **bilgilendirici**.
properties | Günlük girdisi özgü ayrıntılı olarak serileştirilmiş bir JSON dizesi. Daha fazla bilgi için bu makalede aşağıdaki bölümlere bakın.

### <a name="execution-log-properties-schema"></a>Yürütme günlüğü özellikleri şeması

Stream Analytics iş yürütme sırasında gerçekleşen olaylar hakkında bilgi yürütme günlükleri vardır. Şema özellikleri, olay veri hatası veya genel bir olaya olmasına bağlı olarak değişir.

### <a name="data-errors"></a>Veri hataları

İş verileri işlerken oluşan herhangi bir hata günlükleri Bu kategoride var. Bu günlükler genellikle okuma, oluşturma sırasında yerleşim verilerine seri hale getirme, oluşturulur ve yazma işlemleri. Bu günlükler, bağlantı hataları içermez. Bağlantı hataları genel olayları olarak kabul edilir. Çeşitli farklı nedeni hakkında daha fazla bilgi [girdi ve çıktı verilerini hataları](https://docs.microsoft.com/azure/stream-analytics/data-errors).

Ad | Açıklama
------- | -------
Kaynak | İş adı, giriş veya hatanın oluştuğu çıktı.
İleti | Hatayla ilişkili ileti.
Tür | Hata türü. Örneğin, **DataConversionError**, **CsvParserError**, veya **ServiceBusPropertyColumnMissingError**.
Veriler | Hatanın kaynağını doğru bir şekilde bulmak yararlı olan veriler içerir. Kesilme tabidir, boyutuna bağlı olarak.

Yapılandırmanıza bağlı olarak **operationName** değeri aşağıdaki şema veri hatalar var:

* **Olayları sıralamak** olayı okuma işlemleri sırasında oluşur. Giriş verileri Bu nedenlerden biri için sorgu şeması karşılamaz bunlar ortaya çıkar:

   * *Tür uyumsuzluğu (de) olay sırasında serileştirmek*: Hataya neden olan bir alan tanımlar.

   * *Bir olay, geçersiz bir seri hale getirme okunamıyor*: Hatanın oluştuğu giriş verilerinin konumu hakkında bilgi listeler. BLOB adı için blob giriş uzaklığı ve verilerin bir örnek içerir.

* **Olayları gönderme** yazma işlemleri sırasında oluşur. Bunlar, hataya neden olan akış olayı tanımlar.

### <a name="generic-events"></a>Genel olaylar

Genel olaylar, diğer her şeyi kapsar.

Ad | Açıklama
-------- | --------
Hata | (isteğe bağlı) Hata bilgileri. Genellikle, varsa bu özel durum bilgilerini adıdır.
`Message`| Günlük iletisi.
Tür | İleti türü. İç hataların kategorilere ayrılması eşlenir. Örneğin, **JobValidationError** veya **BlobOutputAdapterInitializationFailure**.
Bağıntı Kimliği | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) tanımlamasının, iş yürütme. Aynı işi durur bulunana kadar andan itibaren tüm yürütme günlüğü girişleri işini başlatır. **bağıntı kimliği** değeri.

## <a name="next-steps"></a>Sonraki adımlar

* [Stream analytics'e giriş](stream-analytics-introduction.md)
* [Stream Analytics ile çalışmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Stream Analytics sorgu dili başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics veri hataları](https://docs.microsoft.com/azure/stream-analytics/data-errors)
