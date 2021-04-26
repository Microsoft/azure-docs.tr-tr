---
title: Azure HDInsight kümelerini izlemek için Azure Izleyici günlüklerini sorgulama
description: HDInsight kümesinde çalışan işleri izlemek için Azure Izleyici günlüklerinde sorguları çalıştırmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 3cf97039983ecec44a7c3a32e178fdcf9f9c45ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872192"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>HDInsight kümelerini izlemek için Azure Izleyici günlüklerini sorgulama

Azure HDInsight kümelerini izlemek için Azure Izleyici günlüklerini kullanma hakkında bazı temel senaryolar öğrenin:

* [HDInsight kümesi ölçümlerini çözümleyin](#analyze-hdinsight-cluster-metrics)
* [Olay uyarıları oluşturma](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Önkoşullar

Azure Izleyici günlüklerini kullanmak için bir HDInsight kümesi yapılandırmış olmanız ve çalışma alanına HDInsight kümesine özgü Azure Izleyici günlükleri izleme çözümlerini eklemiş olmanız gerekir. Yönergeler için bkz. [HDInsight kümeleri Ile Azure izleyici günlüklerini kullanma](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight kümesi ölçümlerini çözümleyin

HDInsight kümeniz için belirli ölçümleri nasıl bakacağınızı öğrenin.

1. HDInsight kümenizle ilişkili Log Analytics çalışma alanını Azure portal açın.
1. **Genel** bölümünde **Günlükler**’i seçin.
1. Azure Izleyici günlüklerini kullanacak şekilde yapılandırılmış tüm HDInsight kümeleri için tüm kullanılabilir ölçümler için tüm ölçümleri aramak üzere arama kutusuna aşağıdaki sorguyu yazın ve **Çalıştır**' ı seçin. Sonuçları gözden geçirin.

    ```kusto
    search *
    ```

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png" alt-text="Apache ambarı Analizi tüm ölçümleri ara":::

1. Sol menüden **filtre** sekmesini seçin.

1. **Tür** altında **sinyal**' yı seçin. Sonra **uygula & Çalıştır**' ı seçin.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png" alt-text="Günlük Analizi arama belirli ölçümler":::

1. Metin kutusundaki sorgunun şu şekilde değişdiğine dikkat edin:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Sol menüde bulunan seçenekleri kullanarak daha ayrıntılı bir şekilde izleyebilirsiniz. Örnek:

   - Belirli bir düğümdeki günlükleri görmek için:

     :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png" alt-text="Belirli hataları ara output1":::

   - Günlükleri belirli zamanlarda görmek için:

     :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png" alt-text="Belirli hataları ara output2":::

1. **Uygula & Çalıştır** ' ı seçin ve sonuçları gözden geçirin. Sorgunun şu şekilde güncelleştirildiğini de unutmayın:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Ek örnek sorgular

Küme adına göre sınıflandırılan 10 dakikalık bir aralıkta kullanılan kaynak ortalamasını temel alan örnek sorgu:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Kullanılan kaynak ortalaması temelinde geliştirme yapmak yerine, 10 dakikalık bir pencerede en yüksek kaynakların ne zaman kullanıldığını (Ayrıca 90. ve 95. yüzdebirlik) temel alarak sonuçları iyileştirmek için aşağıdaki sorguyu kullanabilirsiniz:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Olayları izlemek için uyarı oluşturma

Uyarı oluşturmanın ilk adımı, uyarının tetiklendiği bir sorguya ulaşacak. Uyarı oluşturmak istediğiniz herhangi bir sorgu kullanabilirsiniz.

1. HDInsight kümenizle ilişkili Log Analytics çalışma alanını Azure portal açın.
1. **Genel** bölümünde **Günlükler**’i seçin.
1. Üzerinde bir uyarı oluşturmak istediğiniz aşağıdaki sorguyu çalıştırın ve ardından **Çalıştır**' ı seçin.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    Sorgu, HDInsight kümelerinde çalışan başarısız uygulamaların listesini sağlar.

1. Sayfanın üst kısmında **Yeni uyarı kuralı** ' nı seçin.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png" alt-text="Yeni uyarı kuralı":::

1. **Kural oluştur** penceresinde, bir uyarı oluşturmak için sorguyu ve diğer ayrıntıları girin ve ardından **Uyarı kuralı oluştur**' u seçin.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png" alt-text="Uyarı koşulunu tanımlayın.":::

### <a name="edit-or-delete-an-existing-alert"></a>Var olan bir uyarıyı düzenleme veya silme

1. Azure portal Log Analytics çalışma alanını açın.

1. Sol taraftaki menüden, **izleme** altında **Uyarılar**' ı seçin.

1. En üste doğru, **Uyarı kurallarını yönet**' i seçin.

1. Düzenlemek veya silmek istediğiniz uyarıyı seçin.

1. Şu seçeneklere sahipsiniz: **Kaydet**, **çıkart**, **devre dışı bırak** ve **Sil**.

    :::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png" alt-text="HDInsight Azure Izleyici günlükleri uyarı silme düzenlemesi":::

Daha fazla bilgi için bkz. [Azure izleyici kullanarak ölçüm uyarıları oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-metric.md).

## <a name="see-also"></a>Ayrıca bkz.

* [Azure İzleyici’de günlük sorgularını kullanmaya başlama](../azure-monitor/logs/get-started-queries.md)
* [Azure Izleyici 'de Görünüm Tasarımcısı 'nı kullanarak özel görünümler oluşturma](../azure-monitor/visualize/view-designer.md)
