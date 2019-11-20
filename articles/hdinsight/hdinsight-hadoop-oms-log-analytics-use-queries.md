---
title: Azure HDInsight kümelerini izlemek için Azure Izleyici günlüklerini sorgulama
description: HDInsight kümesinde çalışan işleri izlemek için Azure Izleyici günlüklerinde sorguları çalıştırmayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 51344ff7381b6392870b1fd0e331eed38a33915d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103513"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>HDInsight kümelerini izlemek için Azure Izleyici günlüklerini sorgulama

Azure HDInsight kümelerini izlemek için Azure Izleyici günlüklerini kullanma hakkında bazı temel senaryolar öğrenin:

* [HDInsight kümesi ölçümlerini çözümleyin](#analyze-hdinsight-cluster-metrics)
* [Belirli günlük iletilerini arama](#search-for-specific-log-messages)
* [Olay uyarıları oluşturma](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Önkoşullar

Azure Izleyici günlüklerini kullanmak için bir HDInsight kümesi yapılandırmış olmanız ve çalışma alanına HDInsight kümesine özgü Azure Izleyici günlükleri izleme çözümlerini eklemiş olmanız gerekir. Yönergeler için bkz. [HDInsight kümeleri Ile Azure izleyici günlüklerini kullanma](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight kümesi ölçümlerini çözümleyin

HDInsight kümeniz için belirli ölçümleri nasıl bakacağınızı öğrenin.

1. HDInsight kümenizle ilişkili Log Analytics çalışma alanını Azure portal açın.
1. **Günlük araması** kutucuğunu seçin.
1. Azure Izleyici günlüklerini kullanacak şekilde yapılandırılmış tüm HDInsight kümeleri için tüm kullanılabilir ölçümler için tüm ölçümleri aramak üzere arama kutusuna aşağıdaki sorguyu yazın ve **Çalıştır**' ı seçin.

        search *

    ![Apache ambarı Analizi tüm ölçümleri ara](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Tüm ölçümleri ara")

    Çıktı şöyle görünmelidir:

    ![Log Analytics tüm ölçümleri ara](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Tüm ölçüm çıktılarına ara")

1. Sol bölmedeki **tür**altında, derinlemesine olmasını istediğiniz bir ölçüm seçin ve ardından **Uygula**' yı seçin. Aşağıdaki ekran görüntüsünde `metrics_resourcemanager_queue_root_default_CL` tür seçili gösterilmektedir.

    > [!NOTE]  
    > Aradığınız ölçümü bulmak için **[+] daha fazla** düğmesini seçmeniz gerekebilir. Ayrıca, **Uygula** düğmesi listenin en altında bulunur, bu sayede onu görmek için aşağı kaydırmanız gerekir.

    Metin kutusundaki sorgunun aşağıdaki ekran görüntüsünde vurgulanan kutuda gösterilen şekilde değiştiğine dikkat edin:

    ![Günlük Analizi arama belirli ölçümler](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Belirli ölçümleri arama")

1. Bu özel ölçümü daha ayrıntılı bir şekilde incelemek için. Örneğin, aşağıdaki sorgu kullanılarak küme adına göre sınıflandırılan 10 dakikalık bir aralıkta kullanılan kaynak ortalaması temelinde mevcut çıktıyı geliştirebilirsiniz:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

1. Kullanılan kaynak ortalaması temelinde geliştirme yapmak yerine, 10 dakikalık bir pencerede en yüksek kaynakların ne zaman kullanıldığını (Ayrıca 90. ve 95. yüzdebirlik) temel alarak sonuçları iyileştirmek için aşağıdaki sorguyu kullanabilirsiniz:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Belirli günlük iletilerini arama

Belirli bir zaman penceresi sırasında hata iletilerini nasıl bakacağınızı öğrenin. Buradaki adımlar, ilgilendiğiniz hata iletisine nasıl gelebileceğiniz hakkında yalnızca bir örnektir. Bulmaya çalıştığınız hataları aramak için kullanılabilecek herhangi bir özelliği kullanabilirsiniz.

1. HDInsight kümenizle ilişkili Log Analytics çalışma alanını Azure portal açın.
2. **Günlük araması** kutucuğunu seçin.
3. Azure Izleyici günlüklerini kullanacak şekilde yapılandırılmış tüm HDInsight kümelerinin tüm hata iletilerini aramak için aşağıdaki sorguyu yazın ve ardından **Çalıştır**' ı seçin.

         search "Error"

    Aşağıdakine benzer bir çıktı görürsünüz:

    ![Günlük arama hatalarını Azure Portal](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Tüm hataları ara çıktısı")

4. Sol bölmeden, **tür** kategorisi altında, derinlemesine olmasını istediğiniz bir hata türü seçin ve ardından **Uygula**' yı seçin.  Sonuçların yalnızca seçtiğiniz türdeki hatayı göstermek için iyileştirildiğine dikkat edin.

5. Sol bölmede bulunan seçenekleri kullanarak bu özel hata listesini daha ayrıntılı bir şekilde izleyebilirsiniz. Örneğin:

    - Belirli bir çalışan düğümünden hata iletilerini görmek için:

        ![Belirli hataları ara output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Belirli hataları ara output1")

    - Belirli bir zamanda bir hata oluştuğunu görmek için:

        ![Belirli hataları ara output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Belirli hataları ara output2")

6. Özel hatayı görmek için. Gerçek hata iletisine bakmak için **[+] daha fazla göster** seçeneğini belirleyebilirsiniz.

    ![Belirli hataları ara output3](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Belirli hataları ara output3")

## <a name="create-alerts-for-tracking-events"></a>Olayları izlemek için uyarı oluşturma

Uyarı oluşturmanın ilk adımı, uyarının tetiklendiği bir sorguya ulaşacak. Uyarı oluşturmak istediğiniz herhangi bir sorgu kullanabilirsiniz.

1. HDInsight kümenizle ilişkili Log Analytics çalışma alanını Azure portal açın.
2. **Günlük araması** kutucuğunu seçin.
3. Üzerinde bir uyarı oluşturmak istediğiniz aşağıdaki sorguyu çalıştırın ve ardından **Çalıştır**' ı seçin.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    Sorgu, HDInsight kümelerinde çalışan başarısız uygulamaların listesini sağlar.

4. Sayfanın üst kısmında **Yeni uyarı kuralı** ' nı seçin.

    ![Bir alert1 oluşturmak için sorgu girin](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Bir alert1 oluşturmak için sorgu girin")

5. **Kural oluştur** penceresinde, bir uyarı oluşturmak için sorguyu ve diğer ayrıntıları girin ve ardından **Uyarı kuralı oluştur**' u seçin.

    ![Bir alert2 oluşturmak için sorgu girin](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Bir alert2 oluşturmak için sorgu girin")

Var olan bir uyarıyı düzenlemek veya silmek için:

1. Azure portal Log Analytics çalışma alanını açın.
2. Sol menüden **Uyarı**' yı seçin.
3. Düzenlemek veya silmek istediğiniz uyarıyı seçin.
4. Aşağıdaki seçenekleriniz vardır: **Kaydet**, **at**, **devre dışı bırak**ve **Sil**.

    ![HDInsight Azure Izleyici günlükleri uyarı silme düzenlemesi](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Daha fazla bilgi için bkz. [Azure izleyici kullanarak ölçüm uyarıları oluşturma, görüntüleme ve yönetme](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Ayrıca bkz.

* [Azure Izleyici 'de Görünüm Tasarımcısı 'nı kullanarak özel görünümler oluşturma](../azure-monitor/platform/view-designer.md)
* [Azure Izleyici kullanarak ölçüm uyarıları oluşturma, görüntüleme ve yönetme](../azure-monitor/platform/alerts-metric.md)
