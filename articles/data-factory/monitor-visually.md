---
title: Azure veri fabrikalarını görsel olarak izleme
description: Azure veri fabrikalarını görsel olarak izlemeyi öğrenin
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 7b79fd9c87e97e624cce567b57c1c65fefcc151e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684629"
---
# <a name="visually-monitor-azure-data-factories"></a>Azure veri fabrikalarını görsel olarak izleme
Azure Data Factory, bulut tabanlı bir veri tümleştirme hizmetidir. Veri taşıma ve veri dönüştürmeyi düzenlemek ve otomatikleştirmek için bulutta veri odaklı iş akışları oluşturmak için bu uygulamayı kullanabilirsiniz. Azure Data Factory kullanarak şunları yapabilirsiniz:

- Farklı veri depolarından veri alabilen veri odaklı iş akışları (işlem hattı olarak adlandırılır) oluşturabilir ve zamanlayabilirsiniz.
- Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics ve Azure Machine Learning gibi işlem hizmetlerini kullanarak verileri işleyin ve dönüştürün.
- Çıktı verilerini iş zekası (BI) uygulamalarının kullanması için Azure SQL Veri Ambarı gibi veri depolarında yayımlayabilirsiniz.

Bu hızlı başlangıçta, tek bir kod satırı yazmadan Data Factory işlem hatlarını görsel olarak nasıl izleyebileceğinizi öğreneceksiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="monitor-data-factory-pipelines"></a>Data Factory işlem hatlarını izleme

Bir basit liste görünümü arabirimiyle işlem hattını ve etkinlik çalıştırmalarını izleyin. Tüm çalıştırmalar tarayıcının yerel saat diliminde görüntülenir. Saat dilimini değiştirirseniz, tüm tarih/saat alanları seçtiğiniz bir öğesine yastur.  

1. Microsoft Edge veya Google Chrome 'ı başlatın. Şu anda Data Factory Kullanıcı arabirimi yalnızca bu iki Web tarayıcısında desteklenir.
2. [Azure Portal](https://portal.azure.com/)oturum açın.
3. Azure portal oluşturulan veri fabrikası dikey penceresine gidin. Data Factory görsel izleme deneyimini başlatmak için **Izleyiciyi yönet &** bölmesini seçin.

## <a name="monitor-pipeline-runs"></a>İşlem hattı çalıştırmalarını izleme
Liste görünümü, Data Factory işlem hatlarınız için her bir işlem hattının çalışmasını gösterir. Şu sütunları içerir:

| **Sütun adı** | **Açıklama** |
| --- | --- |
| İşlem hattı adı | İşlem hattının adı |
| Eylemler | Etkinlik çalıştırmalarını görüntülemek için kullanılabilen tek eylem |
| Çalıştırma başlangıç | İşlem hattı çalışmasının başlangıç tarihi ve saati (AA/GG/YYYY, ss: DD: SS) |
| Süre | Çalıştırma süresi (SS: DD: SS) |
| Tetikleyen | El ile tetikleyici veya zamanlanan tetikleyici |
| Durum | **Başarısız**, **başarılı**veya **devam ediyor** |
| Parametreler | İşlem hattı çalıştırması için parametreler (ad/değer çiftleri) |
| Hata | İşlem hattı çalıştırma hatası (varsa) |
| Çalıştırma KIMLIĞI | İşlem hattı çalıştırmasının KIMLIĞI |

![İzleme işlem hattı çalıştırmaları için liste görünümü](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Etkinlik çalıştırmalarını izleme
Liste görünümü her bir işlem hattı çalıştırmasına karşılık gelen etkinlik çalıştırmalarını gösterir. Her Işlem hattı çalıştırması için etkinlik çalıştırmalarını görüntülemek için **Eylemler** sütununun altındaki **etkinlik çalıştırmaları** simgesini seçin. Liste görünümü şu sütunları içerir:

| **Sütun adı** | **Açıklama** |
| --- | --- |
| Etkinlik adı | İşlem hattının içindeki etkinliğin adı |
| Etkinlik türü | Etkinliğin türü, örneğin **Copy**, **HDInsightSpark**veya **hdınsighthive** |
| Çalıştırma başlangıç | Etkinlik çalıştırmasının başlangıç tarihi ve saati (AA/GG/YYYY, ss: DD: SS) |
| Süre | Çalıştırma süresi (SS: DD: SS) |
| Durum | **Başarısız**, **başarılı**veya **devam ediyor** |
| Girdi | Etkinlik girişlerini açıklayan JSON dizisi |
| Çıktı | Etkinlik çıkışlarını açıklayan JSON dizisi |
| Hata | Etkinlik çalıştırma hatası (varsa) |

![İzleme etkinliği çalıştırmaları için liste görünümü](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> İşlem hattı ve etkinlik çalıştırmaları listesini yenilemek için üstteki **Yenile** düğmesini seçmeniz gerekir. Otomatik yenileme şu anda desteklenmiyor.

![Yenile düğmesi](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>İzlenecek bir veri fabrikası seçin
Sol üstteki **Data Factory** simgesinin üzerine gelin. İzleyebilmeniz gereken Azure aboneliklerinin ve veri fabrikalarının listesini görmek için ok simgesini seçin.

![Veri fabrikasını seçin](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Liste görünümünü yapılandırma

### <a name="apply-rich-ordering-and-filtering"></a>Zengin sıralama ve filtreleme uygulama

Sıra işlem hattı, çalışma başlangıç zamanına göre DESC/ASC 'de çalışır. Aşağıdaki sütunları kullanarak filtre işlem hattı çalıştırmaları:

| **Sütun adı** | **Açıklama** |
| --- | --- |
| İşlem hattı adı | İşlem hattının adı. Seçenekler, **son 24 saat**, **son hafta**ve **son 30 gün**için hızlı filtreler içerir. Veya özel bir tarih ve saat seçin. |
| Çalıştırma başlangıç | İşlem hattı çalıştırmasının başlangıç tarihi ve saati. |
| Çalışma durumu | Durum, çalışma durumu: **başarılı**, **başarısız**veya **devam ediyor**. |

![Filtreleme seçenekleri](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Sütun ekleme veya kaldırma
Liste görünümü başlığına sağ tıklayın ve liste görünümünde görünmesini istediğiniz sütunları seçin.

![Sütun seçenekleri](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Sütun genişliklerini ayarla
Sütun üst bilgisinin üzerine gelerek liste görünümündeki sütun genişliklerini artırın ve azaltın.

## <a name="promote-user-properties-to-monitor"></a>Kullanıcı özelliklerini izlemeye yükselt

Herhangi bir işlem hattı etkinlik özelliğini, izleyebileceğiniz bir varlık olacak şekilde Kullanıcı özelliği olarak yükseltebilirsiniz. Örneğin, işlem hattınızdaki kopyalama etkinliğinin **kaynak** ve **hedef** özelliklerini Kullanıcı özellikleri olarak yükseltebilirsiniz. Ayrıca, bir kopyalama etkinliğinin **kaynak** ve **hedef** Kullanıcı özelliklerini oluşturmak için **Otomatik oluştur** ' u da seçebilirsiniz.

![Kullanıcı Özellikleri oluştur](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Yalnızca Kullanıcı özellikleri olarak en fazla beş ardışık düzen etkinliği özelliği yükseltebilirsiniz.

Kullanıcı özelliklerini oluşturduktan sonra bunları izleme listesi görünümlerinde izleyebilirsiniz. Kopyalama etkinliğinin kaynağı bir tablo adı ise, kaynak tablo adını etkinlik çalıştırmaları için liste görünümünde bir sütun olarak izleyebilirsiniz.

![Kullanıcı özellikleri olmadan etkinlik çalıştırmaları listesi](media/monitor-visually/monitor-user-properties-image2.png)

![Etkinlik çalıştırmaları listesine kullanıcı özellikleri için sütun ekleme](media/monitor-visually/monitor-user-properties-image3.png)

![Kullanıcı özellikleri için sütunları olan etkinlik çalıştırmaları listesi](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Etkinlikleri bir işlem hattı içinde yeniden çalıştırma

Artık etkinlikleri bir işlem hattı içinde yeniden çalıştırabilirsiniz. **Etkinlik çalıştırmalarını görüntüle**' yi seçin ve ardından işlem hattınızda etkinlik hattınızı yeniden çalıştırmak istediğiniz noktayı seçin.

![Etkinlik çalıştırmalarını görüntüleme](media/monitor-visually/rerun-activities-image1.png)

![Etkinlik çalıştırması seçin](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Yeniden çalıştırma geçmişini görüntüle

Liste görünümündeki tüm işlem hattı çalıştırmaları için yeniden çalıştırma geçmişini görüntüleyebilirsiniz.

![Geçmişi görüntüleme](media/monitor-visually/rerun-history-image1.png)

Ayrıca, belirli bir işlem hattı çalıştırması için yeniden çalıştırma geçmişini görüntüleyebilirsiniz.

![İşlem hattı çalıştırmasının geçmişini görüntüleme](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt görünümleri

Ardışık düzenleri ve etkinlik çalıştırmalarını hızlıca görselleştirmek için Gantt görünümlerini kullanın. İşlem hatlarında oluşturduğunuz ek açıklamaların/etiketlerin başına, ardışık düzen veya grup için Gantt görünümüne bakabilirsiniz.

![Gantt grafiğinin örneği](media/monitor-visually/gantt1.png)

![Gantt grafiği ek açıklamaları](media/monitor-visually/gantt2.png)

Çubuğun uzunluğu, işlem hattının süresini bilgilendirir. Daha fazla ayrıntı görmek için çubuğu da seçebilirsiniz.

![Gantt grafiği süresi](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Kılavuzlu Turlar
Sol alt köşedeki **bilgi** simgesini seçin. Ardından, işlem hattınızı ve etkinlik çalıştırmalarını nasıl izleyecağınız hakkında adım adım yönergeleri almak için **Kılavuzlu Turları** seçin.

![Kılavuzlu Turlar](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Geri Bildirim
Çeşitli özelliklerle ilgili geri bildirimde bulunmak için **geri bildirim** simgesini veya karşılaştığınız sorunları bize iletin.

![Geri Bildirim](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Uyarılar

Data Factory içinde desteklenen ölçümler üzerinde uyarı oluşturabilirsiniz. Başlamak için **Data Factory izleme sayfasında > ** **uyarıları & ölçümleri** ' ni seçin.

![Data Factory Izleyici sayfası](media/monitor-visually/alerts01.png)

Bu özelliğin yedi dakikalık bir girişi ve gösterimi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Uyarı oluşturma

1.  Yeni bir uyarı oluşturmak için **Yeni uyarı kuralı** ' nı seçin.

    ![Yeni uyarı kuralı düğmesi](media/monitor-visually/alerts02.png)

1.  Kural adını belirtin ve uyarı önem derecesini seçin.

    ![Kural adı ve önem derecesi için kutular](media/monitor-visually/alerts03.png)

1.  Uyarı ölçütlerini seçin.

    ![Hedef ölçütü kutusu](media/monitor-visually/alerts04.png)

    ![Ölçüt listesi](media/monitor-visually/alerts05.png)

1.  Uyarı mantığını yapılandırın. Tüm işlem hatları ve ilgili etkinlikler için seçili ölçüm için bir uyarı oluşturabilirsiniz. Ayrıca belirli bir etkinlik türünü, etkinlik adını, işlem hattı adını veya hata türünü de seçebilirsiniz.

    ![Uyarı mantığını yapılandırma seçenekleri](media/monitor-visually/alerts06.png)

1.  Uyarı için e-posta, SMS, push ve sesli bildirimleri yapılandırın. Uyarı bildirimleri için bir eylem grubu oluşturun veya mevcut olanı seçin.

    ![Bildirimleri yapılandırma seçenekleri](media/monitor-visually/alerts07.png)

    ![Bildirim ekleme seçenekleri](media/monitor-visually/alerts08.png)

1.  Uyarı kuralını oluşturun.

    ![Uyarı kuralı oluşturma seçenekleri](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Sonraki adımlar

İşlem hatlarını izleme ve yönetme hakkında bilgi edinmek için işlem [hatlarını programlama yoluyla izleme ve yönetme](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) makalesine bakın.
