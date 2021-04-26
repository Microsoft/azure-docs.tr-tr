---
title: Azure HDInsight üzerinde Grafana kullanma
description: Azure HDInsight 'ta Apache Hadoop kümeleriyle Grafana panosuna erişmeyi öğrenin
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: 81fd3b368f9405192c164ed7a0638caad0cd75fc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869761"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Azure HDInsight'ta Grafana'ya erişme

[Grafana](https://grafana.com/) , popüler, açık kaynaklı bir grafik ve Pano Oluşturucu. Grafana özelliği zengin; yalnızca kullanıcıların özelleştirilebilir ve paylaşılabilir panolar oluşturmasına izin vermez, ayrıca şablonlu/komut dosyalı panolar, LDAP tümleştirmesi, birden çok veri kaynağı ve daha fazlasını sunar.

Şu anda Azure HDInsight 'ta Grafana, Spark, HBase, Kafka ve etkileşimli sorgu kümesi türleriyle desteklenir. Enterprise Security Pack etkin olan kümeler için desteklenmez.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop kümesi oluşturma

Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md). **Küme türü** için **Spark**, **Kafka**, **HBase** veya **etkileşimli sorgu**' yı seçin.

## <a name="access-the-grafana-dashboard"></a>Grafana panosuna erişin

1. Bir Web tarayıcısından `https://CLUSTERNAME.azurehdinsight.net/grafana/` clustername öğesinin Kümenizin adı olduğu yere gidin.

1. Hadoop kümesi Kullanıcı kimlik bilgilerini girin.

1. Grafana panosu görüntülenir ve şu örneğe benzer şekilde görünür:

    :::image type="content" source="./media/hdinsight-grafana/hdinsight-grafana-dashboard.png " alt-text="HDInsight Grafana Web panosu" border="true":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, oluşturduğunuz kümeyi aşağıdaki adımlarla silin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Üstteki **arama** kutusuna **HDInsight** yazın.

1. **Hizmetler** altında **HDInsight kümeleri** ' ni seçin.

1. Görüntülenen HDInsight kümeleri listesinde, oluşturduğunuz kümenin yanındaki **...** seçeneğini belirleyin.

1. **Sil**’i seçin. **Evet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

HDInsight ile veri çözümleme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [HDInsight ile Apache Hive kullanın](../hadoop/hdinsight-use-hive.md).

* [HDInsight Ile MapReduce kullanın](../hadoop/hdinsight-use-mapreduce.md).

* [HDInsight Için Visual Studio Hadoop araçları 'nı kullanmaya](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)başlayın.
