---
title: PowerShell kullanarak Apache Hadoop kümeleri oluşturma-Azure HDInsight
description: Azure PowerShell kullanarak HDInsight için Linux üzerinde Apache Hadoop, Apache HBase, Apache Storm veya Apache Spark kümeleri oluşturmayı öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: hrasheed
ms.openlocfilehash: a6847e75a0a6dcf944b033054ac466841294d28b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494785"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Azure PowerShell kullanarak HDInsight 'ta Linux tabanlı kümeler oluşturma

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell, Microsoft Azure iş yüklerinizin dağıtımını ve yönetimini denetlemek ve otomatikleştirmek için kullanabileceğiniz güçlü bir betik ortamıdır. Bu belge, Azure PowerShell kullanarak Linux tabanlı HDInsight kümesi oluşturma hakkında bilgi sağlar. Örnek bir betik da içerir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu yordama başlamadan önce aşağıdakilere sahip olmanız gerekir:

* Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-Az-ps)

    > [!IMPORTANT]  
    > Azure Service Manager kullanılarak HDInsight kaynaklarının yönetilmesi için Azure PowerShell desteği **kullanım dışı bırakılmış** ve 1 Ocak 2017 tarihinde kaldırılmıştır. Bu belgede yer alan adımlar, Azure Resource Manager ile çalışan yeni HDInsight cmdlet'lerini kullanır.
    >
    > Azure PowerShell 'un en son sürümünü yüklemek için lütfen [ınstall Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) içindeki adımları izleyin. Azure Resource Manager’la çalışan yeni cmdlet’lerle kullanmak için değiştirilmesi gereken komut dosyalarınız varsa, daha fazla bilgi için bkz. [HDInsight kümeleri için Azure Resource Manager tabanlı geliştirme araçlarına geçme](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="create-cluster"></a>Küme oluşturma

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure PowerShell kullanarak bir HDInsight kümesi oluşturmak için aşağıdaki yordamları gerçekleştirmeniz gerekir:

* Azure Kaynak grubu oluşturma
* Azure Depolama hesabı oluşturma
* Azure Blob kapsayıcısı oluşturma
* HDInsight kümesi oluşturma

Aşağıdaki betik, nasıl yeni bir küme oluşturulacağını göstermektedir:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Küme oturumu için belirttiğiniz değerler, kümenin Hadoop Kullanıcı hesabını oluşturmak için kullanılır. Kümede barındırılan, Web UIS veya REST API 'Leri gibi hizmetlere bağlanmak için bu hesabı kullanın.

SSH kullanıcısı için belirttiğiniz değerler, küme için SSH kullanıcısını oluşturmak için kullanılır. Kümede bir uzak SSH oturumu başlatmak ve işleri çalıştırmak için bu hesabı kullanın. Daha fazla bilgi için [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md) belgesine bakın.

> [!IMPORTANT]  
> 32 'den fazla çalışan düğümü kullanmayı planlıyorsanız (küme oluşturma sırasında veya oluşturulduktan sonra kümeyi ölçeklendirirken), en az 8 çekirdek ve 14 GB RAM içeren bir baş düğüm boyutu belirtmeniz gerekir.
>
> Düğüm boyutları ve ilişkili maliyetler hakkında daha fazla bilgi için bkz. [HDInsight fiyatlandırması](https://azure.microsoft.com/pricing/details/hdinsight/).

Kümenin oluşturulması 20 dakikaya kadar sürebilir.

## <a name="create-cluster-configuration-object"></a>Küme oluştur: yapılandırma nesnesi

Ayrıca, `New-AzHDInsightClusterConfig` cmdlet 'ini kullanarak bir HDInsight yapılandırma nesnesi de oluşturabilirsiniz. Daha sonra kümeniz için ek yapılandırma seçeneklerini etkinleştirmek üzere bu yapılandırma nesnesini değiştirebilirsiniz. Son olarak, yapılandırmayı kullanmak için `New-AzHDInsightCluster` cmdlet 'inin `-Config` parametresini kullanın.

Aşağıdaki betik, HDInsight küme türünde bir R Server yapılandırmak için bir yapılandırma nesnesi oluşturur. Yapılandırma bir Edge düğümü, RStudio ve ek bir depolama hesabı sunar.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> HDInsight kümesinden farklı bir konumda depolama hesabı kullanılması desteklenmez. Bu örneği kullanırken, sunucu ile aynı konumda ek depolama hesabı oluşturun.

## <a name="customize-clusters"></a>Kümeleri özelleştirme

* Bkz. [önyükleme kullanarak HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Bkz. [betik eylemini kullanarak HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Küme silme

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Sorun giderme

HDInsight kümeleri oluştururken sorun yaşarsanız bkz. [erişim denetimi gereksinimleri](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

Artık bir HDInsight kümesini başarıyla oluşturduğunuza göre, kümenizle nasıl çalışacağınızı öğrenmek için aşağıdaki kaynakları kullanın.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kümeleri

* [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
* [HDInsight ile Apache Pig kullanma](hadoop/hdinsight-use-pig.md)
* [HDInsight ile MapReduce kullanma](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase kümeleri

* [HDInsight 'ta Apache HBase ile çalışmaya başlama](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight 'ta Apache HBase için Java uygulamaları geliştirme](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Fırtınası kümeleri

* [HDInsight 'ta fırtınası için Java topolojileri geliştirme](storm/apache-storm-develop-java-topology.md)
* [HDInsight 'ta fırtınası 'da Python bileşenleri kullanma](storm/apache-storm-develop-python-topology.md)
* [HDInsight 'ta fırtınası ile topolojileri dağıtma ve izleme](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark kümeleri

* [Scala kullanarak tek başına uygulama oluşturma](spark/apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](spark/apache-spark-livy-rest-interface.md)
* [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](spark/apache-spark-use-bi-tools.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](spark/apache-spark-machine-learning-mllib-ipython.md)

