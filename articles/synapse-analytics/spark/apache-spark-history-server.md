---
title: Uygulamalarda hata ayıklamak için genişletilmiş Spark geçmiş sunucusunu kullanma
description: Azure SYNAPSE Analytics 'te Spark uygulamalarında hata ayıklamak ve tanılamak için genişletilmiş Spark geçmiş sunucusunu kullanın.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4471fed7b423fe05147db30afe57f6c845fe640e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670690"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Apache Spark uygulamalarda hata ayıklamak ve tanılamak için genişletilmiş Apache Spark geçmiş sunucusunu kullanma

Bu makale, hata ayıklama ve tanılama uygulamalarının çalıştırılması için genişletilmiş Apache Spark geçmişi sunucusunun nasıl kullanılacağına ilişkin yönergeler sağlar.

Uzantı bir veri sekmesi, grafik sekmesi ve Tanılama sekmesi içerir. Spark işinin giriş ve çıkış verilerini denetlemek için **veri** sekmesini kullanın. **Grafik** sekmesi, iş grafiğinin veri akışını ve yeniden oynamanın size ait olduğunu gösterir. **Tanılama** sekmesi, **veri eğme**, **zaman eğriltme** ve **yürütücü kullanım analizinin analizini** gösterir.

## <a name="access-the-apache-spark-history-server"></a>Apache Spark geçmiş sunucusuna erişin

Apache Spark geçmiş sunucusu, tamamlanan ve Spark uygulamalarının çalıştırıldığı Web kullanıcı arabirimidir. Azure SYNAPSE Analytics 'ten Apache Spark geçmiş sunucusu Web arabirimini açabilirsiniz.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Apache Spark uygulamaları düğümünden Spark geçmiş sunucusu Web Kullanıcı arabirimini açın

1. [Azure SYNAPSE Analytics](https://web.azuresynapse.net/)'i açın.

2. **İzleme**' yi ve ardından **Apache Spark uygulamalar**' ı seçin.

    ![İzleme ' yi ve Spark uygulaması ' nı seçin.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Bir uygulama seçin, sonra **günlük sorgusunu** seçerek açın.

    ![Günlük sorgusu penceresini açın.](./media/apache-spark-history-server/open-application-window.png)

4. **Spark geçmiş sunucusu**' nu seçin, ardından Spark geçmiş sunucusu Web Kullanıcı arabirimi görünür.

    ![Spark geçmiş sunucusunu açın.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node&quot;></a>Veri düğümünden Spark geçmiş sunucusu Web Kullanıcı arabirimini açın

1. Azure SYNAPSE Studio Not defterinizden, iş yürütme çıkış hücresinden veya Not defteri belgesinin altındaki durum panelinden **Spark geçmiş sunucusu** ' nu seçin. **Oturum ayrıntıları**’nı seçin.

   ![Spark geçmiş sunucusu 1 ' i Başlat](./media/apache-spark-history-server/launch-history-server2.png &quot;Spark geçmiş sunucusunu Başlat")

2. Slayt çıkış panelinden **Spark geçmiş sunucusu** ' nu seçin.

   ![Spark geçmiş sunucusunu Başlat 2](./media/apache-spark-history-server/launch-history-server.png "Spark geçmiş sunucusunu Başlat")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Spark geçmiş sunucusu 'nda veri sekmesini keşfet

Görüntülemek istediğiniz iş için Iş KIMLIĞINI seçin. Ardından, veri görünümünü almak için araç menüsündeki **veriler** ' i seçin. Bu bölümde, veri sekmesinde çeşitli görevlerin nasıl yapılacağı gösterilmektedir.

* Sekmeleri ayrı olarak seçerek **girişleri**, **çıkışları** ve **tablo işlemlerini** kontrol edin.

    ![Spark uygulama sekmeleri için veriler](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* **Kopyala**' yı seçerek tüm satırları kopyalayın.

    ![Spark uygulaması kopyalama verileri](./media/apache-spark-history-server/apache-spark-data-copy.png)

* **CSV**'yi seçerek tüm verileri CSV dosyası olarak kaydedin.

    ![Spark uygulaması kaydetme verileri](./media/apache-spark-history-server/apache-spark-data-save.png)

* Alan **aramasına** anahtar sözcükler girerek arama yapın. Arama sonuçları hemen görüntülenir.

    ![Spark uygulaması arama verileri](./media/apache-spark-history-server/apache-spark-data-search.png)

* Tabloyu sıralamak için sütun üst bilgisini seçin, daha fazla ayrıntı göstermek için bir satırı genişletmek üzere artı işaretini seçin ya da bir satırı daraltmak için eksi işaretini seçin.

    ![Spark uygulama tablosu verileri](./media/apache-spark-history-server/apache-spark-data-table.png)

* **Kısmi indirmeyi** seçerek tek bir dosyayı indirin. Seçili dosya yerel olarak indirilir. Dosya artık yoksa, bir hata iletisiyle birlikte yeni bir sekme görüntülenir.

    ![Spark uygulaması indirme satırı verileri](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Tam yolu veya göreli yolu kopyalamak için, açılan menüden genişlettikten sonra **tam yolu Kopyala** veya **göreli yolu Kopyala** seçeneklerini belirleyin. Azure Data Lake Storage dosyaları için **Azure Depolama Gezgini açın** Azure Depolama Gezgini başlatılır ve oturum açtığınızda klasörü konumlandırır.

    ![Spark uygulaması kopyalama yolu verileri](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Bir sayfada görüntülenecek çok fazla satır olduğunda sayfalarda gezinmek için tablonun altındaki sayfa numaralarını seçin.

    ![Spark uygulama sayfası verileri](./media/apache-spark-history-server/apache-spark-data-page.png)

* Araç ipucunu göstermek için **verilerin** yanındaki soru işaretine gelin veya daha fazla bilgi almak için soru işaretini seçin.

    ![Spark uygulaması için veriler daha fazla bilgi](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Sorunları **bize geri bildirim** gönderin ' i seçerek geri bildirimde bulunun.

    ![Spark Graph bize geri bildirim sağlar](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark geçmiş sunucuda grafik sekmesi

Görüntülemek istediğiniz iş için Iş KIMLIĞINI seçin. Ardından, araç menüsündeki **grafik** ' i seçerek iş grafiği görünümünü alın.

### <a name="overview"></a>Genel Bakış

Oluşturulan iş grafiğinde işinize bir genel bakış görebilirsiniz. Varsayılan olarak, grafik tüm işleri gösterir. Bu görünümü **Iş kimliğine** göre filtreleyebilirsiniz.

![Spark uygulaması ve iş grafiği iş KIMLIĞI](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Göster

Varsayılan olarak, **Ilerleme durumu** ekranı seçilidir. **Görüntüleme** açılır listesinde **okuma** veya **yazma** seçeneğini belirleyerek veri akışını kontrol edebilirsiniz.

![Spark uygulaması ve iş grafiği görüntüsü](./media/apache-spark-history-server/sparkui-graph-display.png)

Graph düğümü, ısı haritasını göstergesinde gösterilen renkleri görüntüler.

![Spark uygulaması ve iş grafiği ısı haritasını](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Kayıttan yürütme

İşi kayıttan yürütmek için **kayıttan yürütme**' yi seçin. Durdurmak için dilediğiniz zaman **Durdur** seçeneğini belirleyebilirsiniz. Görev renkleri kayıttan yürütülürken farklı durumlar gösterir:

|Renk|Anlamı|
|-|-|
|Yeşil|Başarılı: iş başarıyla tamamlandı.|
|Orange|Yeniden denendi: başarısız olan ancak işin nihai sonucunu etkilemeyen görev örnekleri. Bu görevler daha sonra başarılı olabilecek örnekleri tekrarlamıştır veya yeniden dener.|
|Mavi|Çalışıyor: görev çalışıyor.|
|Beyaz|Bekliyor veya atlandı: görev çalışmayı bekliyor veya aşama atlandı.|
|Kırmızı|Başarısız oldu: görev başarısız oldu.|

Aşağıdaki görüntüde yeşil, turuncu ve mavi durum renkleri gösterilmektedir.

![Spark uygulaması ve iş grafiği renk örneği, çalışıyor](./media/apache-spark-history-server/sparkui-graph-color-running.png)

Aşağıdaki görüntüde yeşil ve beyaz durum renkleri gösterilmektedir.

![Spark uygulaması ve iş grafiği renk örneği, atla](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

Aşağıdaki görüntüde kırmızı ve yeşil durum renkleri gösterilmektedir.

![Spark uygulaması ve iş grafiği renk örneği, başarısız oldu](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Her iş için kayıttan yürütmeye izin verilir. Tamamlanmamış işler için kayıttan yürütme desteklenmez.

### <a name="zoom"></a>Zoom

Farenizi kullanarak iş grafiğini yakınlaştırın ve uzaklaştırın veya ekrana sığacak hale getirmek için **yakınlaştırmak Için Yakınlaştır** ' ı seçin.

![Spark uygulaması ve iş grafiği sığacak kadar yaklaştır](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Araç İpuçları

Başarısız görevler olduğunda araç ipucunu görmek için Graph düğümünde üzerine gelin ve aşama sayfasını açmak için bir aşama seçin.

![Spark uygulaması ve iş grafiği araç ipucu](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

İş grafiği sekmesinde, aşamaların bir araç ipucu vardır ve aşağıdaki koşullara uyan görevler varsa küçük bir simge gösterilir:

|Koşul|Description|
|-|-|
|Veri eğriltme|veri okuma boyutu > bu aşama içindeki tüm görevlerin ortalama veri okuma boyutu * 2 ve veri okuma boyutu > 10 MB|
|Zaman eğriltme|yürütme süresi > bu aşama içindeki tüm görevlerin ortalama yürütme süresi * 2 ve yürütme süresi > 2 dakika|
   
![Spark uygulaması ve iş grafiği eğriltme simgesi](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Grafik düğümü açıklaması

İş grafiği düğümü her aşama için aşağıdaki bilgileri görüntüler:

  * Numarasını.
  * Ad veya açıklama.
  * Toplam görev numarası.
  * Okunan veriler: giriş boyutu ve karışık okuma boyutu toplamı.
  * Veri yazma: çıkış boyutunun ve karışık yazma boyutunun toplamı.
  * Yürütme Süresi: son denemede ilk deneme ve tamamlanma zamanının başlangıç saati arasındaki süre.
  * Satır sayısı: giriş kayıtlarının toplamı, çıkış kayıtları, okuma kayıtlarını karıştırma ve yazma kayıtlarını karıştırma.
  * Lemesine.

    > [!NOTE]  
    > Varsayılan olarak, iş grafiği düğümü her aşamanın son denemesinden (aşama yürütme süresi dışında) bilgileri görüntüler. Ancak, kayıttan yürütme sırasında Graph düğümü her girişimin bilgilerini gösterir.
    >  
    > Okuma ve yazma veri boyutu 1 MB/1000 KB = 1000 * 1000 bayttır.

### <a name="provide-feedback"></a>Geribildirim gönderme

Sorunları **bize geri bildirim** gönderin ' i seçerek geri bildirimde bulunun.

![Spark uygulaması ve iş grafiği geri bildirimi](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Apache Spark geçmiş sunucusunda tanılama sekmesini keşfet

Tanılama sekmesine erişmek için bir iş KIMLIĞI seçin. Ardından araç menüsünde **Tanılama** ' yı seçerek iş tanılama görünümünü alın. Tanılama sekmesi **veri eğriltme**, **zaman eğriltme** ve **yürütücü kullanım analizini** içerir.

Sırasıyla sekmeleri seçerek **veri eğriltme**, **zaman eğriltme** ve **yürütücü kullanım analizlerini** denetleyin.

![Mini UI tanılama veri eğriltme sekmesi yeniden](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Veri eğriltme

**Veri eğriltme** sekmesini seçtiğinizde, ilgili asimetrik görevler belirtilen parametrelere göre görüntülenir.

* **Parametreleri belirtin** -ilk bölüm, veri eğriliğini algılamak için kullanılan parametreleri görüntüler. Varsayılan kural: okunan görev verileri ortalama görev verilerinin üç katından daha büyük ve okunan görev verileri 10 MB 'tan fazla. Asimetrik görevler için kendi kuralınızı tanımlamak istiyorsanız parametrelerinizi seçebilirsiniz. **Asimetrik aşama** ve **eğriltme char** bölümleri buna göre yenilenir.

* **Çarpıkık aşama** -ikinci bölüm, yukarıda belirtilen ölçütlere uyan, eğimli görevlere sahip aşamaları görüntüler. Bir aşamada birden çok asimetrik görev varsa, asimetrik aşama tablosu yalnızca en çok asimetrik görevi (örneğin, veri eğriliği için en büyük verileri) görüntüler.

    ![Mini UI tanılama veri eğriltme sekmesi](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Grafiği eğriltme** : eğme aşaması tablosundaki bir satır seçildiğinde, eğriltme grafiği, verileri okuma ve yürütme zamanına göre daha fazla görev dağıtım ayrıntısı görüntüler. Asimetrik görevler kırmızı olarak işaretlenir ve normal görevler mavi olarak işaretlenir. Grafik en fazla 100 örnek görevi görüntüler ve görev ayrıntıları sağ alt panelde görüntülenir.

    ![10. aşama için mini UI eğriltme grafiği](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Zaman eğriltme

**Zaman eğriltme** sekmesi, görev yürütme zamanına göre asimetrik görevleri görüntüler.

* **Parametreleri belirtin** -ilk bölüm, zaman eğriliğini algılamak için kullanılan parametreleri görüntüler. Zaman eğriliğini algılamaya yönelik varsayılan ölçüt: görev yürütme süresi, ortalama yürütme süresinin üç katından büyük ve görev yürütme süresi 30 saniyeden fazla. Parametreleri gereksinimlerinize göre değiştirebilirsiniz. **Asimetrik aşama** ve **eğme grafiği** , yukarıdaki **veri eğriltme** sekmesinde olduğu gibi ilgili aşamaları ve görev bilgilerini görüntüler.

* **Zaman eğriliğini** seçin, sonra filtrelenmiş sonuç, bölüm **belirtme** bölümünde ayarlanan parametrelere göre **asimetrik aşama** bölümünde görüntülenir. **Asimetrik aşama** bölümünde bir öğe seçin, ardından karşılık gelen grafik section3 içinde drafted ve görev ayrıntıları sağ alt panelde görüntülenir.

    ![Mini UI tanılama zaman eğriltme bölümü](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Yürütücü Kullanım Analizi

Yürütücü kullanım grafiği Spark iş yürütücişinin ayırmasını ve çalışma durumunu görselleştirir.  

1. **Yürütücü Kullanım Analizi**' ni seçin, sonra da Yürütücü kullanımı hakkında dört tür eğrileri, **ayrılmış Yürüticileri**, **yürüticileri**, **Boşta** çalışma ve **en fazla yürütücü örneklerini** içeren drafted. Ayrılmış yürüticileri için, her "yürütücü eklendi" veya "yürütücü kaldırıldı" olayı ayrılan yürütmeleri arttırır veya düşürür. Daha fazla karşılaştırma için "Işler" sekmesinde "olay zaman çizelgesini" kontrol edebilirsiniz.

   ![Mini UI tanılama yürüticileri sekmesi](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Tüm taslaklarına karşılık gelen içeriği seçmek veya seçimini kaldırmak için renk simgesini seçin.

    ![Mini UI tanılar grafik seç](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Bilinen sorunlar

Esnek dağıtılmış veri kümeleri (RDDs) kullanan giriş/çıkış verileri veri sekmesinde gösterilmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Synapse Analytics](../overview-what-is.md)
- [Apache Spark belgeleri için .NET](/dotnet/spark)

