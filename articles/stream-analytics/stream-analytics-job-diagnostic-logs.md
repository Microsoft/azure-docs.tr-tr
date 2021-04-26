---
title: Kaynak günlüklerini kullanarak Azure Stream Analytics sorunlarını giderme
description: Bu makalede Azure Stream Analytics kaynak günlüklerinin nasıl analiz edileceği açıklanır.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: troubleshooting
ms.custom: contperf-fy21q1
ms.date: 06/18/2020
ms.openlocfilehash: 93d881419c4854b8e46608e150b55072267e0347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574409"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>Kaynak günlüklerini kullanarak Azure Stream Analytics sorunlarını giderme

Bazen, Azure Stream Analytics işi çalışmayı beklenmedik bir şekilde durdurur. Bu tür bir olayın sorunlarını giderebilmek önemlidir. Hatalara beklenmedik bir sorgu sonucu, cihazların bağlantısı veya beklemedik bir hizmet kesintisi neden olmuş olabilir. Stream Analytics kaynak günlükleri, oluşabilecek sorunları belirlemenize ve kurtarma süresini azaltmanıza yardımcı olabilir.

Bu, hata ayıklama ve izleme konusunda önemli ölçüde yardımcı olacağı için tüm işler için kaynak günlüklerinin etkinleştirilmesi önemle önerilir.

## <a name="log-types"></a>Günlük türleri

Stream Analytics iki tür günlük sunar:

* İşler üzerinde gerçekleştirilen işlemlere Öngörüler veren [etkinlik günlükleri](../azure-monitor/essentials/platform-logs-overview.md) (Always on).

* İş ile gerçekleşen her şeye daha zengin Öngörüler sağlayan [kaynak günlükleri](../azure-monitor/essentials/platform-logs-overview.md) (yapılandırılabilir). Kaynak günlükleri iş oluşturulduğunda başlar ve iş silindiğinde biter. Bunlar iş güncelleştirilirken ve çalışırken olayları kapsar.

> [!NOTE]
> Uyumsuz verileri çözümlemek için Azure depolama, Azure Event Hubs ve Azure Izleyici günlükleri gibi hizmetleri kullanabilirsiniz. Bu hizmetlerin fiyatlandırma modeline göre ücretlendirilirsiniz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Etkinlik günlüklerini kullanarak hata ayıklama

Etkinlik günlükleri varsayılan olarak açık ve Stream Analytics işiniz tarafından gerçekleştirilen işlemlere yüksek düzeyde öngörüler sağlar. Etkinlik günlüklerinde bulunan bilgiler, işinizi etkileyen sorunların temel nedenini bulmanıza yardımcı olabilir. Stream Analytics 'de etkinlik günlüklerini kullanmak için aşağıdaki adımları uygulayın:

1. Azure portal oturum açın ve **genel bakış** altında **etkinlik günlüğü** ' nü seçin.

   ![Etkinlik günlüğünü Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Gerçekleştirilmiş işlemlerin bir listesini görebilirsiniz. İşinizin başarısız olmasına neden olan herhangi bir işlemin kırmızı bir bilgi balonu vardır.

3. Özet görünümünü görmek için bir işleme tıklayın. Buradaki bilgiler genellikle sınırlıdır. İşlem hakkında daha fazla bilgi edinmek için **JSON**' a tıklayın.

   ![Stream Analytics etkinlik günlüğü işlem Özeti](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. JSON 'ın **Özellikler** bölümüne ilerleyin ve bu, başarısız olan işleme neden olan hatanın ayrıntılarını sağlar. Bu örnekte hata, bağlı Enlem değerlerinin dışında bir çalışma zamanı hatası nedeniyle oluştu. Bir Stream Analytics işi tarafından işlenen verilerde tutarsızlık bir veri hatasına neden olur. Farklı [giriş ve çıkış verileri hataları ve neden gerçekleştikleri](./data-errors.md)hakkında bilgi edinebilirsiniz.

   ![JSON hata ayrıntıları](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. JSON 'daki hata iletisine göre düzeltici eylemler gerçekleştirebilirsiniz. Bu örnekte, enlem değerinin ila 90 derece arasında olduğundan emin olup, sorguya 90 derece eklenmelidir.

6. Etkinlik günlüklerindeki hata iletisi, kök nedeni belirlemek için yararlı değilse, kaynak günlüklerini etkinleştirin ve Azure Izleyici günlüklerini kullanın.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Azure Izleyici günlüklerine tanılama gönder

Kaynak günlüklerini açıp Azure Izleyici günlüklerine göndermek, önemle önerilir. Varsayılan olarak **kapalıdır** . Bunları açmak için şu adımları izleyin:

1.  Henüz bir tane yoksa Log Analytics çalışma alanı oluşturun. Log Analytics çalışma alanınızın Stream Analytics işiniz ile aynı bölgede olması önerilir.

2.  Azure portal oturum açın ve Stream Analytics işinize gidin. **İzleme** altında **tanılama günlükleri**' ni seçin. Sonra **tanılamayı aç '** ı seçin.

    ![Kaynak günlüklerine dikey pencere gezintisi](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  **Tanılama Ayarları adında** bir **ad** girin ve **günlük** altında **yürütme** ve **yazma** kutularını işaretleyin ve **ölçüm** bölümündeki **allölçümler** ' i inceleyin. Sonra **Log Analytics gönder** ' i seçin ve çalışma alanınızı seçin. **Kaydet**’e tıklayın.

    ![Kaynak günlükleri ayarları](./media/stream-analytics-job-diagnostic-logs/logs-setup.png)

3. Stream Analytics işiniz başlatıldığında, kaynak günlükleri Log Analytics çalışma alanınıza yönlendirilir. İşinizin kaynak günlüklerini görüntülemek için **izleme** bölümünün altındaki **Günlükler** ' i seçin.

   ![Ekran görüntüsü Günlükler seçili olan genel menüyü gösterir.](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics, ilgilendiğiniz günlüklere kolayca arama yapmanıza olanak tanıyan önceden tanımlanmış sorgular sağlar. Sol bölmede önceden tanımlanmış herhangi bir sorguyu seçip **Çalıştır**' ı seçebilirsiniz. Sorgunun sonuçlarını alt bölmede görürsünüz. 

   ![Ekran görüntüsü bir Stream Analytics işinin günlüklerini gösterir.](./media/stream-analytics-job-diagnostic-logs/logs-example.png)

## <a name="resource-log-categories"></a>Kaynak günlüğü kategorileri

Azure Stream Analytics, kaynak günlüklerinin iki kategorisini yakalar:

* **Yazma**: iş oluşturma, giriş ve çıkışları ekleme ve silme, sorguyu ekleme ve güncelleştirme ve işi başlatma veya durdurma gibi iş yazma işlemleriyle ilgili günlük olaylarını yakalar.

* **Yürütme**: iş yürütme sırasında oluşan olayları yakalar.
    * Bağlantı hataları
    * Aşağıdakiler dahil olmak üzere veri işleme hataları:
        * Sorgu tanımına uymayan Olaylar (eşleşmeyen alan türleri ve değerleri, eksik alanlar vb.)
        * İfade değerlendirme hataları
    * Diğer olaylar ve hatalar

## <a name="resource-logs-schema"></a>Kaynak günlükleri şeması

Tüm Günlükler JSON biçiminde depolanır. Her giriş aşağıdaki ortak dize alanlarına sahiptir:

Ad | Açıklama
------- | -------
time | Günlüğün zaman damgası (UTC).
resourceId | İşlemin üzerinde gerçekleştiği kaynağın KIMLIĞI (büyük harfle). Abonelik KIMLIĞI, kaynak grubu ve iş adını içerir. Örneğin, **/Subscriptions/6503d296-dac1-4449-9B03-609a1f4a1c87/ResourceGroups/My-Resource-Group/Providers/Microsoft. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMMINGJOB**.
category | Günlük kategorisi, **yürütme** ya da **yazma**.
operationName | Günlüğe kaydedilen işlemin adı. Örneğin, **olay gönderme: mysqloutput 'e SQL çıkışı yazma hatası**.
durum | İşlemin durumu. Örneğin, **başarısız** veya **başarılı** oldu.
düzey | Günlük düzeyi. Örneğin, **hata**, **Uyarı** veya **bilgilendirme**.
properties | JSON dizesi olarak seri hale getirilen, girişe özel ayrıntı günlüğe kaydeder. Daha fazla bilgi için bu makaledeki aşağıdaki bölümlere bakın.

### <a name="execution-log-properties-schema"></a>Yürütme günlüğü özellikleri şeması

Yürütme günlükleri Stream Analytics işi yürütme sırasında gerçekleşen olaylar hakkında bilgi sahibi olabilir. Özelliklerin şeması, olayın bir veri hatası veya genel bir olay olmasına bağlı olarak değişir.

### <a name="data-errors"></a>Veri hataları

İş işlenirken oluşan herhangi bir hata, bu Günlükler kategorisindedir. Bu Günlükler genellikle veri okuma, serileştirme ve yazma işlemleri sırasında oluşturulur. Bu Günlükler bağlantı hatalarını içermez. Bağlantı hataları genel olaylar olarak değerlendirilir. Çeşitli farklı [giriş ve çıkış verileri hatalarının](./data-errors.md)nedeni hakkında daha fazla bilgi edinebilirsiniz.

Ad | Açıklama
------- | -------
Kaynak | Hatanın oluştuğu iş girişinin veya çıktının adı.
İleti | Hatayla ilişkili ileti.
Tür | Hata türü. Örneğin, **Dataconversionerror**, **csvparsererror** veya **Servicebuspropertycolumnmissingerror**.
Veriler | Hatanın kaynağını doğru bir şekilde bulmak için yararlı olan verileri içerir. Boyuta göre kesilme konusu.

**OperationName** değerine bağlı olarak, veri hataları aşağıdaki şemaya sahiptir:

* Olay okuma işlemleri sırasında **serileştirme olayları** oluşur. Bu nedenlerden biri, girişte bulunan veriler sorgu şemasını karşılamadığı zaman oluşur:

   * *Olay (de) serileştirme sırasında tür uyuşmazlığı*: hataya neden olan alanı tanımlar.

   * *Bir olay okunamıyor, geçersiz seri hale getirme*: Hatanın gerçekleştiği giriş verilerinde yer hakkındaki bilgileri listeler. Blob girişi, kayması ve verilerin bir örneği için blob adı içerir.

* Yazma işlemleri sırasında **gönderme olayları** oluşur. Hataya neden olan akış olayını belirler.

### <a name="generic-events"></a>Genel olaylar

Genel olaylar diğer her şeyi kapsar.

Ad | Açıklama
-------- | --------
Hata | seçim Hata bilgileri. Genellikle, bu, varsa özel durum bilgileri olur.
İleti| Günlük iletisi.
Tür | İleti türü. İç hata kategorilerine eşlenir. Örneğin, **Jobvalidationerror** veya **Bloi Putadapterınitializationfailure**.
Bağıntı Kimliği | İş yürütmesini benzersiz bir şekilde tanımlayan GUID. İş durdurulmadan önce işin başladığı zamandan itibaren tüm yürütme günlüğü girdileri aynı **BAĞıNTı kimliği** değerine sahip olamaz.

## <a name="next-steps"></a>Sonraki adımlar

* [Stream Analytics veri hataları](./data-errors.md)
* [Stream Analytics sorgu dili başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)