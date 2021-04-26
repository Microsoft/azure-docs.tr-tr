---
title: Azure Data Factory’yi görsel olarak izleme
description: Azure veri fabrikalarını görsel olarak izlemeyi öğrenin
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d177513af9f0ee4fcadb1ea316edf1ad8cb89e5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783668"
---
# <a name="visually-monitor-azure-data-factory"></a>Azure Data Factory’yi görsel olarak izleme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory bir işlem hattı oluşturup yayımladıktan sonra, bir tetikleyici ile ilişkilendirebilir veya bir geçici çalıştırmayı el ile başlatabilirsiniz. Azure Data Factory kullanıcı deneyiminde tüm işlem hattı çalıştırmalarının yerel olarak izlenmesini sağlayabilirsiniz. İzleme deneyimini açmak için [Azure Portal](https://portal.azure.com/)Veri Fabrikası dikey penceresinde **izleyici & Yönet** kutucuğunu seçin. Zaten ADF UX kullanıyorsanız sol kenar çubuğundaki **izleyici** simgesine tıklayın.

Varsayılan olarak, tüm Data Factory çalıştırmaları tarayıcının yerel saat diliminde görüntülenir. Saat dilimini değiştirirseniz, tüm tarih/saat alanları seçtiğiniz bir öğesine yastur.

## <a name="monitor-pipeline-runs"></a>İşlem hattı çalıştırmalarını izleme

Varsayılan izleme görünümü, seçilen dönemde tetiklenen işlem hattı çalıştırmaları listesidir. Zaman aralığını değiştirebilir ve durum, işlem hattı adı veya ek açıklamasına göre filtreleyebilirsiniz. Yeniden çalıştırma ve tüketim raporu gibi çalıştırmaya özgü eylemleri almak için belirli işlem hattı çalıştırmasının üzerine gelin.

![İzleme işlem hattı çalıştırmaları için liste görünümü](media/monitor-visually/pipeline-runs.png)

İşlem hattı Çalıştırma Kılavuzu şu sütunları içerir:

| **Sütun adı** | **Açıklama** |
| --- | --- |
| İşlem hattı adı | İşlem hattının adı |
| Çalıştırma başlangıç | İşlem hattı çalışmasının başlangıç tarihi ve saati (AA/GG/YYYY, ss: DD: SS) |
| Çalıştırma bitişi | İşlem hattı çalışmasının bitiş tarihi ve saati (AA/GG/YYYY, ss: DD: SS) |
| Süre | Çalıştırma süresi (SS: DD: SS) |
| Tetikleyen | İşlem hattını Başlatan tetikleyicinin adı |
| Durum | **Başarısız**, **başarılı**, **devam ediyor**, **iptal edildi** veya **sıraya alındı** |
| Ek Açıklamalar | İşlem hattı ile ilişkili filtrelenebilir Etiketler  |
| Parametreler | İşlem hattı çalıştırması için parametreler (ad/değer çiftleri) |
| Hata | İşlem hattı başarısız olursa, çalıştırma hatası |
| Çalıştırma kimliği | İşlem hattı çalıştırmasının KIMLIĞI |

İşlem hattı ve etkinlik çalıştırmaları listesini yenilemek için **Yenile** düğmesini el ile seçmeniz gerekir. Oto yenileme şu anda desteklenmiyor.

![Yenile düğmesi](media/monitor-visually/refresh.png)

Hata ayıklama çalıştırmasının sonuçlarını görüntülemek için **Hata Ayıkla** sekmesini seçin.

![Etkin hata ayıklama çalıştırmalarını görüntüle simgesini seçin](media/iterative-development-debugging/view-debug-runs.png)

## <a name="monitor-activity-runs"></a>Etkinlik çalıştırmalarını izleme

Belirli bir işlem hattı çalıştırmasının ayrı etkinlik çalıştırmalarının ayrıntılı bir görünümünü almak için işlem hattı adına tıklayın.

![Etkinlik çalıştırmalarını görüntüleme](media/monitor-visually/view-activity-runs.png)

Liste görünümü her bir işlem hattı çalıştırmasına karşılık gelen etkinlik çalıştırmalarını gösterir. JSON girişi, JSON çıktısı ve ayrıntılı etkinliğe özgü izleme deneyimleri gibi çalıştırmaya özgü bilgileri almak için belirli etkinlik çalıştırmasının üzerine gelin.

![SalesAnalyticsMLPipeline hakkında, ardından etkinlik çalıştırmalarının bir listesi ile ilgili bilgiler vardır.](media/monitor-visually/activity-runs.png)

| **Sütun adı** | **Açıklama** |
| --- | --- |
| Etkinlik Adı | İşlem hattının içindeki etkinliğin adı |
| Etkinlik Türü | Etkinliğin türü; örneğin **Copy**, **executedataflow** veya **AzureMLExecutePipeline** |
| Eylemler | JSON giriş bilgilerini, JSON çıktı bilgilerini veya ayrıntılı etkinliğe özgü izleme deneyimlerini görmenizi sağlayan simgeler | 
| Çalıştırma başlangıç | Etkinlik çalıştırmasının başlangıç tarihi ve saati (AA/GG/YYYY, ss: DD: SS) |
| Süre | Çalıştırma süresi (SS: DD: SS) |
| Durum | **Başarısız**, **başarılı**, **devam ediyor** veya **iptal edildi** |
| Tümleştirme Çalışma Zamanı | Etkinliğin çalıştırıldığı Integration Runtime |
| Kullanıcı Özellikleri | Etkinliğin Kullanıcı tanımlı özellikleri |
| Hata | Etkinlik başarısız olursa, çalıştırma hatası |
| Çalıştırma kimliği | Etkinlik çalıştırmasının KIMLIĞI |

Etkinlik başarısız olursa, hata sütunundaki simgesine tıklayarak ayrıntılı hata iletisini görebilirsiniz. 

![Hata kodu, hata türü ve hata ayrıntıları dahil hata ayrıntıları içeren bir bildirim görüntülenir.](media/monitor-visually/activity-run-error.png)

### <a name="promote-user-properties-to-monitor"></a>Kullanıcı özelliklerini izlemeye yükselt

Herhangi bir işlem hattı etkinlik özelliğini Kullanıcı özelliği olarak yükselterek izlediğiniz bir varlık haline gelir. Örneğin, işlem hattınızdaki kopyalama etkinliğinin **kaynak** ve **hedef** özelliklerini Kullanıcı özellikleri olarak yükseltebilirsiniz.

> [!NOTE]
> Yalnızca Kullanıcı özellikleri olarak en fazla beş ardışık düzen etkinliği özelliği yükseltebilirsiniz.

![Kullanıcı Özellikleri oluştur](media/monitor-visually/promote-user-properties.png)

Kullanıcı özelliklerini oluşturduktan sonra bunları izleme listesi görünümlerinde izleyebilirsiniz.

![Etkinlik çalıştırmaları listesine kullanıcı özellikleri için sütun ekleme](media/monitor-visually/choose-user-properties.png)

 Kopyalama etkinliğinin kaynağı bir tablo adı ise, kaynak tablo adını etkinlik çalıştırmaları için liste görünümünde bir sütun olarak izleyebilirsiniz.

![Kullanıcı özellikleri için sütunları olan etkinlik çalıştırmaları listesi](media/monitor-visually/view-user-properties.png)

## <a name="rerun-pipelines-and-activities"></a>İşlem hatlarını ve etkinlikleri yeniden çalıştır

Daha önce başlangıçtan çalıştırılan bir işlem hattını yeniden çalıştırmak için, belirli işlem hattı çalıştırmasının üzerine gelin ve **yeniden çalıştır**' ı seçin. Birden çok işlem hattı seçerseniz, tümünü çalıştırmak için **yeniden** Çalıştır düğmesini kullanabilirsiniz.

![İşlem hattını yeniden çalıştırma](media/monitor-visually/rerun-pipeline.png)

Belirli bir noktadan başlayarak yeniden çalıştırmak istiyorsanız, bunu etkinlik çalıştırmaları görünümünden yapabilirsiniz. Başlamak istediğiniz etkinliği seçin ve **etkinlikten yeniden çalıştır**' ı seçin. 

![Etkinlik çalıştırmasını yeniden çalıştırma](media/monitor-visually/rerun-activity.png)

### <a name="rerun-from-failed-activity"></a>Başarısız etkinlikten yeniden çalıştır

Bir etkinlik başarısız olursa, zaman aşımına uğrar veya iptal edilirse, **başarısız etkinlikten yeniden çalıştır**' ı seçerek bu başarısız etkinlikten ardışık düzeni yeniden çalıştırabilirsiniz.

![Başarısız etkinliği yeniden çalıştır](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Yeniden çalıştırma geçmişini görüntüle

Liste görünümündeki tüm işlem hattı çalıştırmaları için yeniden çalıştırma geçmişini görüntüleyebilirsiniz.

![Geçmişi görüntüleme](media/monitor-visually/rerun-history-1.png)

Ayrıca, belirli bir işlem hattı çalıştırması için yeniden çalıştırma geçmişini görüntüleyebilirsiniz.

![İşlem hattı çalıştırmasının geçmişini görüntüleme](media/monitor-visually/view-rerun-history.png)

## <a name="monitor-consumption"></a>Tüketimi izleme

Bir işlem hattı çalıştırması tarafından tüketilen kaynakları, çalıştırmanın yanındaki tüketim simgesine tıklayarak görebilirsiniz. 

![Bir işlem hattı tarafından tüketilen kaynakları görebileceğiniz konumu gösteren ekran görüntüsü.](media/monitor-visually/monitor-consumption-1.png)

Simgeye tıkladığınızda, bu işlem hattı çalıştırması tarafından kullanılan kaynakların tüketim raporu açılır. 

![Tüketimi izleme](media/monitor-visually/monitor-consumption-2.png)

İşlem hattı çalıştırmasının maliyetini tahmin etmek için bu değerleri [Azure fiyatlandırma hesaplayıcıya](https://azure.microsoft.com/pricing/details/data-factory/) takabilirsiniz. Azure Data Factory fiyatlandırması hakkında daha fazla bilgi için bkz. [fiyatlandırmayı anlama](pricing-concepts.md).

> [!NOTE]
> Fiyatlandırma Hesaplayıcı tarafından döndürülen bu değerler bir tahmindir. Azure Data Factory, faturalandırılacaksınız tam miktarı yansıtmaz 

## <a name="gantt-views"></a>Gantt görünümleri

Bir Gantt grafiği, çalışma geçmişini bir zaman aralığı üzerinde görmenizi sağlayan bir görünümdedir. Bir Gantt görünümüne geçiş yaparak, tüm işlem hattı çalıştırmalarını, çalıştırmanın ne kadar süreyle gerçekleşdüğüne bağlı olarak görünen ada göre gruplanmış olarak görürsünüz. Ayrıca, işlem hattınızda oluşturduğunuz ek açıklamaların/etiketlerin yanı sıra gruplandırabilirsiniz. Gantt görünümü etkinlik çalıştırma düzeyinde de kullanılabilir.

![Gantt grafiğinin örneği](media/monitor-visually/select-gantt.png)

Çubuğun uzunluğu, işlem hattının süresini bilgilendirir. Daha fazla ayrıntı görmek için çubuğu da seçebilirsiniz.

![Gantt grafiği süresi](media/monitor-visually/view-gantt-run.png)

## <a name="alerts"></a>Uyarılar

Data Factory içinde desteklenen ölçümler üzerinde uyarı oluşturabilirsiniz. Başlamak   >  için Data Factory izleme sayfasında,**uyarıları & ölçümleri** izleyin ' ı seçin.

![Data Factory Izleyici sayfası](media/monitor-visually/start-page.png)

Bu özelliğin yedi dakikalık bir girişi ve gösterimi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Uyarı oluşturma

1.  Yeni bir uyarı oluşturmak için **Yeni uyarı kuralı** ' nı seçin.

    ![Yeni uyarı kuralı düğmesi](media/monitor-visually/new-alerts.png)

1.  Kural adını belirtin ve uyarı önem derecesini seçin.

    ![Kural adı ve önem derecesi için kutular](media/monitor-visually/name-and-severity.png)

1.  Uyarı ölçütlerini seçin.

    ![Hedef ölçütü kutusu](media/monitor-visually/add-criteria-1.png)

    ![Uyarı koşulunu ayarlamak için bir ölçümü seçtiğiniz yeri gösteren ekran görüntüsü.](media/monitor-visually/add-criteria-2.png)

    ![Ölçüt listesi](media/monitor-visually/add-criteria-3.png)

    ADF varlık sayısı/boyutu, etkinlik/ardışık düzen/tetikleyici çalıştırmaları, Integration Runtime (IR) CPU kullanımı/belleği/düğüm sayısı/kuyruğu ve SSIS paket yürütmeleri ve SSIS IR başlatma/durdurma işlemleri dahil olmak üzere çeşitli ölçümler üzerinde uyarılar oluşturabilirsiniz.

1.  Uyarı mantığını yapılandırın. Tüm işlem hatları ve ilgili etkinlikler için seçili ölçüm için bir uyarı oluşturabilirsiniz. Ayrıca belirli bir etkinlik türünü, etkinlik adını, işlem hattı adını veya hata türünü de seçebilirsiniz.

    ![Uyarı mantığını yapılandırma seçenekleri](media/monitor-visually/alert-logic.png)

1.  Uyarı için e-posta, SMS, push ve sesli bildirimleri yapılandırın. Uyarı bildirimleri için bir eylem grubu oluşturun veya mevcut olanı seçin.

    ![Bildirimleri yapılandırma seçenekleri](media/monitor-visually/configure-notification-1.png)

    ![Bildirim ekleme seçenekleri](media/monitor-visually/configure-notification-2.png)

1.  Uyarı kuralını oluşturun.

    ![Uyarı kuralı oluşturma seçenekleri](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Sonraki adımlar

İşlem hatlarını izleme ve yönetme hakkında bilgi edinmek için işlem [hatlarını programlama yoluyla izleme ve yönetme](./monitor-programmatically.md) makalesine bakın.