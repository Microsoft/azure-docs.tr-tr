---
title: Küme oluşturma sırasında kitaplıklar Apache Hive-Azure HDInsight
description: Küme oluşturma sırasında bir HDInsight kümesine Apache Hive kitaplıklarının (jar dosyaları) nasıl ekleneceğini öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: b6695e5e985a30d6f912095225c4899e1c910e34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945946"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>HDInsight kümenizi oluştururken özel Apache Hive kitaplıkları ekleyin

HDInsight 'ta [Apache Hive](https://hive.apache.org/) kitaplıklarını önceden yüklemeyi öğrenin. Bu belge, küme oluşturma sırasında kitaplıkları önceden yüklemek için bir betik eylemi kullanmayla ilgili bilgiler içerir. Bu belgedeki adımlar kullanılarak eklenen kitaplıklar, Hive içinde genel kullanıma sunulmuştur. yüklemek için [jar Ekle](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) ' nin kullanılmasına gerek yoktur.

## <a name="how-it-works"></a>Nasıl çalışır?

Bir küme oluştururken, küme düğümlerini oluşturuldukları sırada değiştirmek için bir betik eylemi kullanabilirsiniz. Bu belgedeki betik, kitaplıkların konumu olan tek bir parametreyi kabul eder. Bu konum bir Azure depolama hesabında olmalıdır ve kitaplıkların jar dosyaları olarak depolanması gerekir.

Küme oluşturma sırasında betik, dosyaları numaralandırır, bunları `/usr/lib/customhivelibs/` baş ve çalışan düğümlerinde dizine kopyalar ve ardından bunları `hive.aux.jars.path` dosyadaki özelliğine ekler `core-site.xml` . Linux tabanlı kümeler üzerinde `hive-env.sh` Dosya konumuyla birlikte dosyayı da güncelleştirir.

Bu makaledeki betik eyleminin kullanılması, **Web Hcat** ve **HiveServer2** için Hive istemcisi kullanılırken kitaplıkları kullanılabilir hale getirir.

## <a name="the-script"></a>Betik

**Betik konumu**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Gereksinimler

* Betikler hem **baş düğümlere** hem de **çalışan düğümlerine** uygulanmalıdır.

* Yüklemek istediğiniz jar dosyaları dışındaki 'ın Azure Blob depolama alanında **tek bir kapsayıcıda** depolanması gerekir.

* Jar dosyalarının kitaplığını içeren depolama hesabı, oluşturma sırasında HDInsight **kümesine bağlanmalıdır.** Bu, varsayılan depolama hesabı ya da __depolama hesabı ayarları__ aracılığıyla eklenmiş bir hesap olmalıdır.

* Kapsayıcının ınsıb yolu, komut dosyası eyleminin parametresi olarak belirtilmelidir. Örneğin, JAR dosyaları dışındaki, **depolamam** adlı bir depolama hesabındaki **LIBS** adlı bir kapsayıcıda depolanıyorsa parametre olur `wasbs://libs@mystorage.blob.core.windows.net/` .

  > [!NOTE]  
  > Bu belge, zaten bir depolama hesabı, blob kapsayıcısı oluşturmuş olduğunuzu ve dosyaları ona yüklediğinizi varsayar.
  >
  > Bir depolama hesabı oluşturmadıysanız, [Azure Portal](https://portal.azure.com)aracılığıyla bunu yapabilirsiniz. Daha sonra [Azure Depolama Gezgini](https://storageexplorer.com/) gibi bir yardımcı programını kullanarak hesapta bir kapsayıcı oluşturabilir ve dosyaları karşıya yükleyebilirsiniz.

## <a name="create-a-cluster-using-the-script"></a>Betiği kullanarak bir küme oluşturma

1. [Linux 'Ta HDInsight kümeleri sağlama](hdinsight-hadoop-provision-linux-clusters.md)bölümündeki adımları kullanarak bir kümeyi sağlamaya başlayın, ancak sağlamayı tamammayın. Bu betiği kullanarak bir küme oluşturmak için Azure PowerShell veya HDInsight .NET SDK 'sını de kullanabilirsiniz. Bu yöntemleri kullanma hakkında daha fazla bilgi için bkz. [HDInsight kümelerini betik eylemleriyle özelleştirme](hdinsight-hadoop-customize-cluster-linux.md). Azure portal için, **yapılandırma + fiyatlandırma** sekmesinden **+ betik Ekle eylemini** seçin.

1. **Depolama** için, jar dosyalarının kitaplığını içeren depolama hesabı, küme için kullanılan hesaptan farklı olur, **ek depolama hesapları** doldurun.

1. **Betik eylemleri** için aşağıdaki bilgileri sağlayın:

    |Özellik |Değer |
    |---|---|
    |Betik türü|-Özel|
    |Name|Kitaplıklar |
    |Bash betiği URI 'SI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Düğüm türleri|Baş, çalışan|
    |Parametreler|Cars 'ı içeren kapsayıcı ve depolama hesabına ait olan adresi girin. Örneğin, `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Apache Spark 2,1 için şu Bash betiği URI 'sini kullanın: `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh` .

1. [Linux 'Ta HDInsight kümeleri sağlama](hdinsight-hadoop-provision-linux-clusters.md)bölümünde açıklandığı gibi kümeyi sağlamaya devam edin.

Küme oluşturma işlemi tamamlandıktan sonra, ifadesini kullanmak zorunda kalmadan Hive 'den bu komut dosyası aracılığıyla eklenen jar dosyaları dışındaki 'ı kullanabilirsiniz `ADD JAR` .

## <a name="next-steps"></a>Sonraki adımlar

Hive ile çalışma hakkında daha fazla bilgi için bkz. [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
