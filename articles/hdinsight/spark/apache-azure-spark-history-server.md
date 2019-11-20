---
title: Uygulamalarda hata ayıklamak için genişletilmiş Spark geçmiş sunucusu-Azure HDInsight
description: Spark uygulamalarında hata ayıklamak ve tanılamak için genişletilmiş Spark geçmiş sunucusunu kullanın-Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 1320764687f3eb2f033ca70703a9bcb16ab616ea
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494732"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Apache Spark uygulamalarda hata ayıklamak ve tanılamak için genişletilmiş Apache Spark geçmiş sunucusunu kullanma

Bu makalede, hata ayıklama ve tanılama uygulamalarının çalıştırılması için genişletilmiş Apache Spark geçmiş sunucusunun nasıl kullanılacağına ilişkin yönergeler sunulmaktadır. Uzantı, veri sekmesini ve grafik sekmesini ve tanılama sekmesini içerir. **Veriler** sekmesinde, kullanıcılar Spark işinin giriş ve çıkış verilerini denetleyebilir. **Grafik** sekmesinde, kullanıcılar veri akışını denetleyebilir ve iş grafiğini yeniden oynatılabilir. **Tanılama** sekmesinde, Kullanıcı **veri eğme**, **zaman eğriltme** ve **yürütücü kullanım analizine**başvurabilir.

## <a name="get-access-to-apache-spark-history-server"></a>Apache Spark geçmiş sunucusuna erişim sağlayın

Apache Spark geçmiş sunucusu, tamamlanan ve Spark uygulamalarının çalıştırıldığı Web Kullanıcı arabirimi.

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Azure portal Apache Spark geçmiş sunucusu Web Kullanıcı arabirimini açın

1. [Azure Portal](https://portal.azure.com/)Spark kümesini açın. Daha fazla bilgi için bkz. [kümeleri listeleme ve gösterme](../hdinsight-administer-use-portal-linux.md#showClusters).
2. **Hızlı bağlantılardan** **küme panosu**' na ve ardından **Spark geçmiş sunucusu**' na tıklayın. İstendiğinde Spark kümesi için yönetici kimlik bilgilerini girin.

    ![Portal Spark geçmiş sunucusunu Başlat](./media/apache-azure-spark-history-server/launch-history-server.png "Spark geçmiş sunucusu")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>URL 'ye göre Spark geçmiş sunucusu Web Kullanıcı arabirimini açın

Aşağıdaki URL 'ye giderek Spark geçmiş sunucusunu açın, `<ClusterName>` adını müşterinin Spark kümesi adıyla değiştirin.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Spark geçmiş sunucusu Web Kullanıcı arabirimi şöyle görünür:

![HDInsight Spark geçmiş sunucusu](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="data-tab-in-spark-history-server"></a>Spark geçmiş sunucusunda veri sekmesi

Veri görünümünü almak için iş KIMLIĞI ' ni seçin ve araç menüsündeki **veriler** ' e tıklayın.

+ Sekmeleri ayrı olarak seçerek **girişleri**, **çıkışları**ve **tablo işlemlerini** kontrol edin.

    ![Spark uygulama sekmeleri için veriler](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Düğme **Kopyala**' ya tıklayarak tüm satırları kopyalayın.

    ![Spark uygulaması kopyalama verileri](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Düğme **CSV**' ye tıklayarak tüm verileri CSV dosyası olarak kaydedin.

    ![Spark uygulaması kaydetme verileri](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Arama sonucu, alan **aramasına**anahtar sözcükler girerek hemen görüntülenecektir.

    ![Spark uygulaması arama verileri](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Tabloyu sıralamak için sütun başlığına tıklayın, daha fazla ayrıntı göstermek üzere bir satırı genişletmek için artı işaretine tıklayın veya bir satırı daraltmak için eksi işaretine tıklayın.

    ![Spark uygulama tablosu verileri](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Tek bir dosyayı indirin ve sağ tarafta bulunan düğme **kısmi indirmesi** ' ne tıklayarak seçili dosya yerel olarak indirilir, ancak dosya artık yoksa hata iletilerini göstermek için yeni bir sekme açar.

    ![Spark uygulaması indirme satırı verileri](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Tam yolu **Kopyala**, indirme menüsünden genişleyen **göreli yolu Kopyala** ' yı seçerek tam yolu veya göreli yolu kopyalayın. Azure Data Lake depolama dosyaları için **Azure Depolama Gezgini ' de açın** Azure Depolama Gezgini başlatılır ve oturum açtığınızda klasörü bulun.

    ![Spark uygulaması kopyalama yolu verileri](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Tek sayfada görüntülenecek çok fazla satır olduğunda sayfalarda gezinmek için tablonun altındaki sayıya tıklayın.

    ![Spark uygulama sayfası verileri](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Araç ipucunu göstermek için verilerin yanındaki soru işaretine gelin veya daha fazla bilgi edinmek için soru işaretine tıklayın.

    ![Spark uygulaması için veriler daha fazla bilgi](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ **Bize geri bildirim sağlayın**' a tıklayarak sorunlar ile geri bildirim gönderin.

    ![Spark Graph bize geri bildirim sağlar](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark geçmiş sunucuda grafik sekmesi

İş KIMLIĞI ' ni seçin ve ardından araç menüsündeki **Graph** ' a tıklayarak iş grafiği görünümünü alın.

+ Oluşturulan iş grafiğine göre işinizin genel görünümünü denetleyin.

+ Varsayılan olarak, tüm işleri gösterir ve **Iş kimliğine**göre filtrelenebilir.

    ![Spark uygulaması ve iş grafiği iş KIMLIĞI](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ Varsayılan olarak, **ilerleme** seçilidir. Kullanıcı, **ekran**açılan listesinde **Oku/yazılır** ' i seçerek veri akışını denetleyebilir.

    ![Spark uygulaması ve iş grafiği görüntüsü](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Grafik düğümü, heatmap 'i gösteren renkli renkte görüntülenir.

    ![Spark uygulaması ve iş grafiği ısı haritasını](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ **Kayıttan yürütme** düğmesine tıklayıp Durdur düğmesine tıklayarak her zaman durarak işi oynatın. Kayıttan yürütme sırasında farklı durumu göstermek için görev Color olarak görüntülenir:

  + İçin yeşil: iş başarıyla tamamlandı.
  + Yeniden denenme için turuncu: işin son sonucunu etkilemeyen, ancak başarısız olan görevlerin örnekleri. Bu görevler daha sonra başarılı olabilecek örnekleri tekrarlamıştır veya yeniden dener.
  + Çalıştırmak için mavi: görev çalışıyor.
  + Bekliyor veya atlandı: görev çalışmayı bekliyor veya aşama atlandı.
  + Red başarısız: görev başarısız oldu.

    ![Spark uygulaması ve iş grafiği renk örneği, çalışıyor](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

    Atlanan aşama beyaz olarak görüntülenir.
    Spark uygulaması ve iş grafiği renk örneği ![](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png) atla

    ![Spark uygulaması ve iş grafiği renk örneği, başarısız oldu](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

    > [!NOTE]  
    > Her iş için kayıttan yürütmeye izin verilir. Tamamlanmamış iş için kayıttan yürütme desteklenmez.

+ Fare, iş grafiğini yakınlaştırmak/uzaklaştırmak için kayar veya ekrana sığacak hale getirmek için **Yakınlaştır** ' a tıklayın.

    ![Spark uygulaması ve iş grafiği sığacak kadar yaklaştır](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Başarısız görevler olduğunda araç ipucunu görmek için Graph düğümünde üzerine gelin ve aşama sayfasını açmak için aşama ' a tıklayın.

    ![Spark uygulaması ve iş grafiği araç ipucu](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ İş grafiği sekmesinde, aşağıdaki koşullara uyan görevler varsa, aşamalar araç ipucu ve küçük simge olarak görüntülenir:
  + Veri eğriltme: veri okuma boyutu > Bu aşama içindeki tüm görevlerin ortalama veri okuma boyutu * 2 ve veri okuma boyutu > 10 MB.
  + Zaman eğriltme: yürütme süresi > Bu aşama içindeki tüm görevlerin ortalama yürütme süresi * 2 ve yürütme süresi > 2 dakika.

    ![Spark uygulaması ve iş grafiği eğriltme simgesi](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ İş grafiği düğümü her aşama için aşağıdaki bilgileri görüntüler:
  + Numarasını.
  + Ad veya açıklama.
  + Toplam görev numarası.
  + Okunan veriler: giriş boyutu ve karışık okuma boyutu toplamı.
  + Veri yazma: çıkış boyutunun ve karışık yazma boyutunun toplamı.
  + Yürütme Süresi: son denemede ilk deneme ve tamamlanma zamanının başlangıç saati arasındaki süre.
  + Satır sayısı: giriş kayıtlarının toplamı, çıkış kayıtları, okuma kayıtlarını karıştırma ve yazma kayıtlarını karıştırma.
  + Lemesine.

    > [!NOTE]  
    > Varsayılan olarak, iş grafiği düğümü her bir aşamanın son denemesinden (aşama yürütme süresi hariç) bilgileri görüntüler, ancak oynatma grafiği düğümü sırasında her girişimin bilgileri gösterilir.

    > [!NOTE]  
    > Okuma ve yazma veri boyutu için 1 MB/1000 KB = 1000 * 1000 bayt kullanılır.

+ **Bize geri bildirim sağlayın**' a tıklayarak sorunlar ile geri bildirim gönderin.

    ![Spark uygulaması ve iş grafiği geri bildirimi](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Apache Spark geçmiş sunucusunda Tanılama sekmesi

İş KIMLIĞI ' ni seçin ve ardından araç menüsünde **Tanılama** ' ya tıklayarak iş tanılama görünümünü alın. Tanılama sekmesi **veri eğriltme**, **zaman eğriltme**ve **yürütücü kullanım analizini**içerir.

+ Sırasıyla sekmeleri seçerek **veri eğriltme**, **zaman eğriltme**ve **yürütücü kullanım analizlerini** denetleyin.

    ![Mini UI tanılama veri eğriltme sekmesi yeniden](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Veri eğriltme

**Veri eğriltme** sekmesi ' ne tıklayın, ilgili asimetrik görevler belirtilen parametrelere göre görüntülenir.

+ **Parametreleri belirtin** -ilk bölüm, veri eğriliğini algılamak için kullanılan parametreleri görüntüler. Yerleşik kural: okunan görev verileri, okunan ortalama görev verilerinin 3 katından büyük ve okunan görev verileri 10 MB 'tan fazla. Çarpıtılmış görevler için kendi kuralınızı tanımlamak istiyorsanız, parametrelerinizi, **asimetrik aşamayı**ve **çarpıklık** bölümünü seçebilirsiniz.

+ **Çarpıtılmış aşama** -ikinci bölüm, yukarıda belirtilen ölçütlere uyan bir görev gösteren aşamaları görüntüler. Bir aşamada birden çok asimetrik görev varsa, asimetrik aşama tablosu yalnızca en çok eğilmiş görevi görüntüler (ör. veri eğriliği için en büyük veriler).

    ![Mini UI tanılama veri eğriltme sekmesi](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Grafiği eğriltme** : eğme aşaması tablosundaki bir satır seçildiğinde, eğriltme grafiği, verileri okuma ve yürütme zamanına göre daha fazla görev dağıtım ayrıntısı görüntüler. Asimetrik görevler kırmızı olarak işaretlenir ve normal görevler mavi olarak işaretlenir. Performans değerlendirmesi için grafik yalnızca en fazla 100 örnek görevi görüntüler. Görev ayrıntıları sağ alt panelde görüntülenir.

    ![10. aşama için mini UI eğriltme grafiği](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Zaman eğriltme

**Zaman eğriltme** sekmesi, görev yürütme zamanına göre asimetrik görevleri görüntüler.

+ **Parametreleri belirtin** -ilk bölüm, zaman eğriliğini algılamak için kullanılan parametreleri görüntüler. Zaman eğriliğini algılamaya yönelik varsayılan ölçüt: görev yürütme süresi, ortalama yürütme süresinin 3 katından büyük ve görev yürütme süresi 30 saniyeden fazla. Parametreleri gereksinimlerinize göre değiştirebilirsiniz. **Asimetrik aşama** ve **eğme grafiği** , yukarıdaki **veri eğriltme** sekmesinde olduğu gibi ilgili aşamaları ve görev bilgilerini görüntüler.

+ **Zaman eğriliği**' ne tıklayın ve ardından filtrelenmiş sonuç, bölüm **belirtme**bölümünde ayarlanan parametrelere göre **asimetrik aşama** bölümünde görüntülenir. **Asimetrik aşama** bölümünde bir öğeye tıklayın, ardından karşılık gelen grafik section3 içinde drafted ve görev ayrıntıları sağ alt panelde görüntülenir.

    ![Mini UI tanılama zaman eğriltme bölümü](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Yürütücü Kullanım Analizi

Yürütücü kullanım grafiği, Spark işi gerçek yürütücü ayırmayı ve çalışma durumunu görselleştirir.  

+ **Yürütücü Kullanım Analizi**' ne tıklayın, ardından yürütücü kullanımı ile ilgili dört tür eğrileri drafted, **yürütme**,**boştaki Yürüticileri**ve **en fazla yürütücü örnekleri** **de dahil olmak**üzere, Ayrılmış yürüticilere ilgili olarak, her "yürütücü eklenmiş" veya "yürütücü kaldırıldı" olayı ayrılan yürütmeleri arttırır veya azaltacaktır, daha fazla karşılaştırma için "Işler" sekmesinde "olay zaman çizelgesini" kontrol edebilirsiniz.

    ![Mini UI tanılama yürüticileri sekmesi](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Tüm taslaklarına karşılık gelen içeriği seçmek veya seçimini kaldırmak için renk simgesine tıklayın.

    ![Mini UI tanısı grafik seç](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>SSS

### <a name="1-revert-to-community-version"></a>1. topluluk sürümüne dönün

Topluluk sürümüne dönmek için aşağıdaki adımları uygulayın:

1. Kümeyi ambarı 'nda açın. Sol panelde **Spark2** ' a tıklayın.
2. Yapılandırma **' ya** tıklayın.
3. Grup **özel spark2-varsayılanlar**' ı genişletin.
4. **Özellik Ekle**' ye tıklayın, **spark. UI. geliştirmesi Ekle. Enabled = False**, Kaydet.
5. Özelliği şimdi **yanlış** olarak ayarlanır.
6. Yapılandırmayı kaydetmek için **Kaydet** ' e tıklayın.

    ![Apache ambarı özelliği kapanır](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

7. Sol bölmede **Spark2** ' ye tıklayın, **Özet** sekmesinde, **Spark2 geçmiş sunucusu**' na tıklayın.

    ![Apache ambarı Spark2 Özet görünümü](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

8. **Spark2 geçmiş sunucusunu** **Yeniden Başlat** ' a tıklayarak geçmiş sunucusunu yeniden başlatın.

    ![Apache ambarı Spark2 geçmişi yeniden başlatması](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  
9. Spark geçmiş sunucusu Web Kullanıcı arabirimini yenileyerek, bu işlem topluluk sürümüne geri döndürülecek.

### <a name="2-upload-history-server-event"></a>2. geçmiş sunucusu olayını karşıya yükle

Geçmiş sunucu hatasıyla karşılaşırsanız, olayı sağlamak için aşağıdaki adımları izleyin:

1. Geçmiş sunucusu Web Kullanıcı arabiriminde **İndir** ' e tıklayarak olayı indirin.

    ![Spark2 geçmiş sunucusu indirme](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Veri/grafik sekmesinden **bize geri bildirim sağla** ' ya tıklayın.

    ![Spark Graph bize geri bildirim sağlar](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Hatanın başlığını ve açıklamasını girip, ZIP dosyasını düzenleme alanına sürükleyin ve sonra **yeni sorun gönder**' e tıklayın.

    ![Apache Spark dosyası sorun örneği](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. bir düzeltme senaryosu için jar dosyasını yükseltin

Düzeltme ile yükseltmek istiyorsanız, aşağıdaki betiği kullanın, Spark-Enhancement. jar * öğesini yükseltecek.

**upgrade_spark_enhancement. sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

**Kullanım**:

`upgrade_spark_enhancement.sh https://${jar_path}`

**Örnek**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

**Azure portal bash dosyasını kullanmak için**

1. [Azure Portal](https://ms.portal.azure.com)başlatın ve kümenizi seçin.
2. **Betik eylemleri**' ne ve ardından **Yeni Gönder**' e tıklayın. **Betik eylemini gönder** formunu tamamlayıp **Oluştur** düğmesine tıklayın.

    + **Betik türü**: **özel**' i seçin.
    + **Ad**: bir betik adı belirtin.
    + **Bash betiği URI 'si**: bash dosyasını özel kümeye yükleyin ve URL 'yi buraya kopyalayın. Alternatif olarak, sağlanmış URI 'yi kullanın.

   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

   + **Baş** ve **çalışanı**denetleyin.
   + **Parametreler**: parametreleri ayarla Bash kullanımını izleyin.

     ![Betik eylemini Azure portal gönder](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Bilinen sorunlar

1. Şu anda yalnızca Spark 2,3 ve 2,4 kümesi için geçerlidir.

2. RDD kullanan giriş/çıkış verileri, veri sekmesinde gösterilmez.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight üzerinde Apache Spark kümesi için kaynakları yönetme](apache-spark-resource-manager.md)
* [Apache Spark ayarlarını yapılandırma](apache-spark-settings.md)

## <a name="contact-us"></a>Bizimle iletişim kurun

Herhangi bir geri bildiriminiz varsa veya bu aracı kullanırken başka sorunlarla karşılaşırsanız, ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)) adresine bir e-posta gönderin.
