---
title: Azure HDInsight oluşturma-Azure Data Lake Storage 2.-Portal
description: Portalı kullanarak Azure HDInsight kümeleriyle Azure Data Lake Storage 2. kullanmayı öğrenin.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: 5eb6cfc7f0dbc0bc886550e1d601dc52dff66267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866378"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Azure portal kullanarak Data Lake Storage 2. bir küme oluşturma

Azure portal, Microsoft Azure bulutta barındırılan hizmetler ve kaynaklar için Web tabanlı bir yönetim aracıdır. Bu makalede, portalı kullanarak Linux tabanlı Azure HDInsight kümeleri oluşturmayı öğreneceksiniz. [HDInsight kümeleri oluşturma](./hdinsight-hadoop-provision-linux-clusters.md)' dan ek ayrıntılar bulunabilir.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Depolama için Data Lake Storage 2. kullanan bir HDInsight kümesi oluşturmak için, hiyerarşik bir ad alanına sahip bir depolama hesabı yapılandırmak için aşağıdaki adımları izleyin.

## <a name="create-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma

Henüz bir tane yoksa, Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol üst köşedeki **kaynak oluştur ' a** tıklayın.
1. Arama kutusuna **Kullanıcı atandı** yazın ve **Kullanıcı tarafından atanan yönetilen kimlik**' e tıklayın.
1. **Oluştur**’a tıklayın.
1. Yönetilen Kimliğiniz için bir ad girin, doğru aboneliği, kaynak grubunu ve konumu seçin.
1. **Oluştur**’a tıklayın.

Yönetilen kimliklerin Azure HDInsight 'ta nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure HDInsight 'Ta Yönetilen kimlikler](hdinsight-managed-identities.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png" alt-text="Kullanıcı tarafından atanan yönetilen kimlik oluşturma":::

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Data Lake Storage 2. ile kullanmak için bir depolama hesabı oluşturma

Azure Data Lake Storage 2. ile kullanmak için bir depolama hesabı oluşturun.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol üst köşedeki **kaynak oluştur ' a** tıklayın.
1. Arama kutusuna **depolama** yazın ve **depolama hesabı**' na tıklayın.
1. **Oluştur**’a tıklayın.
1. **Depolama hesabı oluştur** ekranında:
    1. Doğru aboneliği ve kaynak grubunu seçin.
    1. Data Lake Storage 2. olan depolama hesabınız için bir ad girin.
    1. **Gelişmiş** sekmesine tıklayın.
    1. **Data Lake Storage 2.** altındaki **hiyerarşik ad alanı** ' nın yanında **etkin** ' e tıklayın.
    1. **Gözden geçir ve oluştur**’a tıklayın.
    1. **Oluştur** seçeneğine tıklayın

Depolama hesabı oluşturma sırasında diğer seçenekler hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure Data Lake Storage 2. için depolama hesabı oluşturma](../storage/blobs/create-data-lake-storage-account.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png" alt-text="Azure portal depolama hesabı oluşturmayı gösteren ekran görüntüsü":::

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Data Lake Storage 2. yönetilen kimlik için izinleri ayarlayın

Yönetilen kimliği depolama hesabındaki **Depolama Blobu veri sahibi** rolüne atayın.

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.
1. Depolama hesabınızı seçin ve ardından **erişim denetimi (IAM)** öğesini seçerek hesabın erişim denetimi ayarlarını görüntüleyin. Rol atamalarının listesini görmek için **rol atamaları** sekmesini seçin.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png" alt-text="Depolama erişim denetimi ayarlarını gösteren ekran görüntüsü":::

1. Yeni bir rol eklemek için **+ rol ataması Ekle** düğmesini seçin.
1. **Rol ataması Ekle** penceresinde, **Depolama Blobu veri sahibi** rolünü seçin. Ardından, yönetilen kimliğe ve depolama hesabına sahip aboneliği seçin. Ardından, daha önce oluşturduğunuz Kullanıcı tarafından atanan yönetilen kimliği bulmak için arama yapın. Son olarak, yönetilen kimliği seçin ve **Seçilen Üyeler** altında listelenecektir.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png" alt-text="Azure rolü atamayı gösteren ekran görüntüsü":::

1. **Kaydet**’i seçin. Seçtiğiniz kullanıcı tarafından atanan kimlik artık seçili rol altında listelenmiştir.
1. Bu ilk kurulum tamamlandıktan sonra Portal aracılığıyla bir küme oluşturabilirsiniz. Küme, depolama hesabıyla aynı Azure bölgesinde olmalıdır. Küme oluşturma menüsünün **depolama** sekmesinde aşağıdaki seçenekleri belirtin:

    * **Birincil depolama türü** için **Azure Data Lake Storage 2.**' yi seçin.
    * **Birincil depolama hesabı** altında, Data Lake Storage 2. depolama ile yeni oluşturulan depolama hesabını arayıp seçin.

    * **Kimlik** altında, yeni oluşturulan kullanıcı tarafından atanan yönetilen kimliği seçin.

        :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png" alt-text="Azure HDInsight ile Data Lake Storage 2. kullanmaya yönelik depolama ayarları":::

    > [!NOTE]
    > * Depolama hesabı düzeyinde Data Lake Storage 2. olan bir ikincil depolama hesabı eklemek için, daha önce oluşturduğunuz yönetilen kimliği, eklemek istediğiniz yeni Data Lake Storage 2. atamanız yeterlidir. Lütfen HDInsight 'ta "ek depolama hesapları" dikey penceresi aracılığıyla Data Lake Storage 2. ikincil depolama hesabı eklemenin desteklenmediğinden emin olun.
    > * , HDInsight 'ın kullandığı Azure Blob depolama hesabında RA-GRS veya RA-ZRS 'yi etkinleştirebilirsiniz. Ancak, RA-GRS veya RA-ZRS ikincil uç noktasında bir küme oluşturmak desteklenmez.

## <a name="delete-the-cluster"></a>Küme silme

Bkz. [tarayıcınızı, PowerShell 'i veya Azure CLI 'yı kullanarak HDInsight kümesini silme](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Sorun giderme

HDInsight kümeleri oluştururken sorun yaşarsanız bkz. [erişim denetimi gereksinimleri](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Sonraki adımlar

Bir HDInsight kümesini başarıyla oluşturdunuz. Şimdi kümenizle nasıl çalışacağınızı öğrenin.

### <a name="apache-spark-clusters"></a>Apache Spark kümeleri

* [Betik eylemlerini kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md)
* [Scala kullanarak tek başına uygulama oluşturma](spark/apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](spark/apache-spark-livy-rest-interface.md)
* [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](spark/apache-spark-use-bi-tools.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kümeleri

* [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
* [HDInsight ile MapReduce kullanma](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase kümeleri

* [HDInsight 'ta Apache HBase ile çalışmaya başlama](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight 'ta Apache HBase için Java uygulamaları geliştirme](hbase/apache-hbase-build-java-maven-linux.md)
