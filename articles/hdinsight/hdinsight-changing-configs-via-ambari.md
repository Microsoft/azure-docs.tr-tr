---
title: Azure HDInsight 'ta Apache ambarı ile kümeleri iyileştirme
description: Azure HDInsight kümelerini yapılandırmak ve iyileştirmek için Apache ambarı Web Kullanıcı arabirimini kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 2146ccb0c4d7f263c3e1a69db9b172649fcd25ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863505"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache ambarı ile kümeleri iyileştirme

HDInsight, büyük ölçekli veri işleme uygulamaları için Apache Hadoop kümeleri sağlar. Bu karmaşık çok düğümlü kümelerin yönetilmesi, izlenmesi ve iyileştirgetirilmesi zor olabilir. Apache ambarı, HDInsight Linux kümelerini yönetmek ve izlemek için bir Web arabirimidir.

Ambarı Web Kullanıcı arabirimini kullanmaya giriş için bkz [. Apache ambarı Web Kullanıcı arabirimini kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari.md)

Küme kimlik bilgilerinizle, ambarı ' nda oturum açın `https://CLUSTERNAME.azurehdidnsight.net` . İlk ekranda Genel Bakış Panosu görüntülenir.

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png" alt-text="Apache ambarı Kullanıcı panosu görüntülendi":::

Ambarı Web Kullanıcı arabirimi, Konakları, Hizmetleri, uyarıları, konfigürasyonları ve görünümleri yönetmek için kullanılır. Bir HDInsight kümesi oluşturmak veya Hizmetleri yükseltmek için ambarı kullanılamaz. Ayrıca yığınları ve sürümleri yönetemez, ana bilgisayarları kullanımdan çıkarmayı veya yeniden komisyon veya kümeye hizmet ekleme.

## <a name="manage-your-clusters-configuration"></a>Kümenizin yapılandırmasını yönetme

Yapılandırma ayarları belirli bir hizmeti ayarlamanıza yardımcı olur. Hizmetin yapılandırma ayarlarını değiştirmek için **Hizmetler** kenar çubuğundan (solda) hizmeti seçin. Ardından hizmet ayrıntısı sayfasında **, yapılandırma sekmesine gidin** .

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png" alt-text="Apache ambarı Hizmetleri kenar çubuğu":::

## <a name="modify-namenode-java-heap-size"></a>Süs Code Java yığın boyutunu değiştir

Süs Code Java yığın boyutu, kümedeki yük gibi birçok faktöre bağlıdır. Ayrıca, dosya sayıları ve blok sayıları. 1 GB varsayılan boyutu çoğu küme ile iyi çalışmaktadır, ancak bazı iş yükleri daha fazla veya daha az bellek gerektirebilir.

Süs Yot Java yığın boyutunu değiştirmek için:

1. Hizmetler **kenar çubuğundan bir i seçin ve** **configs** sekmesine gidin.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png" alt-text="Apache ambarı, yapılandırma":::

1. **Süs Code Java yığın boyutu** ayarını bulun. Belirli bir ayarı yazmak ve bulmak için **filtre** metin kutusunu da kullanabilirsiniz. Ayar adının yanındaki **kalem** simgesini seçin.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png" alt-text="Apache ambarı süs Code Java yığın boyutu":::

1. Metin kutusuna yeni değeri yazın ve ardından değişikliği kaydetmek için **ENTER** tuşuna basın.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png" alt-text="Ambarı düzenleme süs Code Java yığın size1":::

1. Süs Code Java yığın boyutu 2 GB 'tan 1 GB olarak değişir.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png" alt-text="Düzenlenmiş süs Code Java yığın size2":::

1. Yapılandırma ekranının üstündeki yeşil **Kaydet** düğmesine tıklayarak yaptığınız değişiklikleri kaydedin.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png" alt-text="' Apache ambarı kayıt yapılandırması '":::

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerini Apache ambarı Web Kullanıcı arabirimi ile yönetme](hdinsight-hadoop-manage-ambari.md)
* [Apache ambarı REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Apache HBase’i iyileştirme](./optimize-hbase-ambari.md)
* [Apache Hive’ı iyileştirme](./optimize-hive-ambari.md)
* [Apache Pig’i iyileştirme](./optimize-pig-ambari.md)
