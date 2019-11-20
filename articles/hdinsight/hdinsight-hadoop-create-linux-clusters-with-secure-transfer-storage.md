---
title: Apache Hadoop & güvenli transme depolaması-Azure HDInsight
description: Güvenli aktarım özellikli Azure depolama hesapları ile HDInsight kümeleri oluşturma hakkında bilgi edinin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: b8a5d48e977009cf592f271760232a3b0138c8c5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044956"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Azure HDInsight 'ta güvenli aktarım depolama hesapları ile Apache Hadoop kümesi oluşturma

[Güvenli aktarım gereklidir](../storage/common/storage-require-secure-transfer.md) özelliği, güvenli bir bağlantı üzerinden tüm istekleri hesabınıza uygulayarak Azure Depolama hesabınızın güvenliğini artırır. Bu özellik ve wasbs şeması yalnızca HDInsight kümesi 3.6 veya sonraki sürümlerde desteklenir.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleye başlamadan önce, şunları yapmanız gerekir:

* **Azure aboneliği** gereklidir. Bir aylık ücretsiz deneme hesabı oluşturmak için [azure.microsoft.com/free](https://azure.microsoft.com/free) adresine gidin.
* **Güvenli aktarım özellikli bir Azure depolama hesabı**. Yönergeler için bkz. [Depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md) ve [Güvenli aktarım isteme](../storage/common/storage-require-secure-transfer.md).
* **Depolama hesabındaki bir Blob kapsayıcı**.

## <a name="create-cluster"></a>Küme oluşturma

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Bu bölümde, [Azure Resource Manager şablonu](../azure-resource-manager/resource-group-template-deploy.md) kullanarak HDInsight'ta Hadoop kümesi oluşturacaksınız. Şablon [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/)' da bulunur. Bu makaleyi izlemek için Kaynak Yöneticisi şablonuyla deneyim sahibi olmak gerekli değildir. Diğer küme oluşturma yöntemleri ve bu makalede kullanılan özellikleri anlamak için bkz. [HDInsight kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md).

1. Aşağıdaki resme tıklayarak Azure'da oturum açın ve Azure portalında Resource Manager şablonunu açın.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Aşağıdaki özelliklerle kümeyi oluşturmak için yönergeleri izleyin: 

    - HDInsight sürümü 3.6’yı belirtin. 3\.6 veya daha yeni bir sürüm gereklidir.
    - Güvenli aktarım özellikli bir depolama hesabı belirtin.
    - Depolama hesabı için kısa bir ad kullanın.
    - Hem depolama hesabı hem de blob kapsayıcı önceden oluşturulmalıdır.

      Yönergeler için bkz. [Küme oluşturma](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Kendi yapılandırma dosyalarınızı sağlamak için betik eylemi kullanıyorsanız, aşağıdaki ayarlarda wasbs kullanmanız gerekir:

- fs.defaultFS (çekirdek-site)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Başka depolama hesapları ekleme

Güvenli aktarım özellikli başka depolama hesapları eklemek için birkaç seçenek vardır:

- Son bölümdeki Azure Resource Manager şablonunu değiştirin.
- [Azure portalını](https://portal.azure.com) kullanarak bir küme oluşturun ve bağlı depolama hesabını belirtin.
- Var olan bir HDInsight kümesine güvenli aktarım özellikli başka depolama hesapları eklemek için betik eylemini kullanın. Daha fazla bilgi için bkz. [HDInsight’a başka depolama hesapları ekleme](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, bir HDInsight kümesi oluşturmayı ve depolama hesaplarına Güvenli aktarım özelliğini etkinleştirmeyi öğrendiniz.

HDInsight ile veri çözümleme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* Visual Studio 'dan Hive sorguları gerçekleştirme dahil olmak üzere HDInsight ile [Apache Hive](https://hive.apache.org/) kullanma hakkında daha fazla bilgi için bkz. [HDInsight ile Apache Hive kullanma][hdinsight-use-hive].
* Verileri dönüştürmek için kullanılan bir dil olan [Apache Pig](https://pig.apache.org/)hakkında bilgi edinmek için bkz. [HDInsight Ile Apache Pig kullanma][hdinsight-use-pig].
* Hadoop 'ta verileri işleyen programları yazmanın bir yolu olan [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)hakkında bilgi edinmek için bkz. [HDInsight Ile Apache Hadoop MapReduce kullanma][hdinsight-use-mapreduce].
* HDInsight 'ta verileri çözümlemek üzere Visual Studio için HDInsight araçları 'nı kullanma hakkında bilgi edinmek için bkz. [HDInsight Için Visual studio Apache Hadoop araçları 'nı kullanmaya başlama](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

HDInsight’ın verileri nasıl depoladığı veya HDInsight’a verilerin nasıl alındığı hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* HDInsight’ın Azure Depolama’yı nasıl kullandığı hakkında daha fazla bilgi için bkz. [HDInsight ile Azure Depolama kullanma](hdinsight-hadoop-use-blob-storage.md).
* HDInsight’a veril yükleme hakkında daha fazla bilgi için bkz. [Verileri HDInsight’a yükleme][hdinsight-upload-data].

HDInsight kümesi oluşturma ve yönetme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* Linux tabanlı HDInsight kümenizi yönetme hakkında bilgi edinmek için bkz. [Apache ambarı kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari.md).
* HDInsight kümesi oluştururken tercih edebileceğiniz seçenekler hakkında daha fazla bilgi için bkz. [Özel seçenekleri kullanarak Linux’ta HDInsight oluşturma](hdinsight-hadoop-provision-linux-clusters.md).
* Linux hakkında bilgi sahibiyseniz ve Apache Hadoop, ancak HDInsight 'ta Hadoop hakkında daha fazla bilgi edinmek istiyorsanız bkz. [Linux üzerinde HDInsight Ile çalışma](hdinsight-hadoop-linux-information.md). Bu makale aşağıdaki gibi bilgiler sağlar:

  * Kümede barındırılan, [Apache ambarı](https://ambari.apache.org/) ve [webhcat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) gibi hizmetlerin URL 'leri
  * Yerel dosya sistemindeki [Apache Hadoop](https://hadoop.apache.org/) dosya ve örneklerin konumu
  * Varsayılan veri deposu olarak [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) m yerine Azure Storage (te) kullanımı

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
