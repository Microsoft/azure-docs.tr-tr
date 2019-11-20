---
title: Küme oluşturma sırasında kitaplıklar Apache Hive-Azure HDInsight
description: Küme oluşturma sırasında bir HDInsight kümesine Apache Hive kitaplıklarının (jar dosyaları) nasıl ekleneceğini öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 51a93aaec4abdb2dd9d8fad042c079a48d4ea7a3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494827"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>HDInsight kümenizi oluştururken özel Apache Hive kitaplıkları ekleyin

HDInsight 'ta [Apache Hive](https://hive.apache.org/) kitaplıklarını önceden yüklemeyi öğrenin. Bu belge, küme oluşturma sırasında kitaplıkları önceden yüklemek için bir betik eylemi kullanmayla ilgili bilgiler içerir. Bu belgedeki adımlar kullanılarak eklenen kitaplıklar, Hive içinde genel kullanıma sunulmuştur. yüklemek için [jar Ekle](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) ' nin kullanılmasına gerek yoktur.

## <a name="how-it-works"></a>Nasıl çalışır

Bir küme oluştururken, küme düğümlerini oluşturuldukları sırada değiştirmek için bir betik eylemi kullanabilirsiniz. Bu belgedeki betik, kitaplıkların konumu olan tek bir parametreyi kabul eder. Bu konum bir Azure depolama hesabında olmalıdır ve kitaplıkların jar dosyaları olarak depolanması gerekir.

Küme oluşturma sırasında betik, dosyaları numaralandırır, baş ve çalışan düğümlerinde `/usr/lib/customhivelibs/` dizinine kopyalar ve ardından bunları `core-site.xml` dosyasındaki `hive.aux.jars.path` özelliğine ekler. Linux tabanlı kümeler üzerinde `hive-env.sh` dosyasını da dosyaların konumuyla güncelleştirir.

> [!NOTE]  
> Bu makaledeki betik eylemlerinin kullanılması, kitaplıkları aşağıdaki senaryolarda kullanılabilir hale getirir:
>
> * **Linux tabanlı HDInsight** -Hive istemcisi kullanırken, **Webhcat**ve **HiveServer2**.
> * **Windows tabanlı HDInsight** -Hive Istemcisi ve **webhcat**kullanılırken.

## <a name="the-script"></a>Betik

**Betik konumu**

**Linux tabanlı kümeler**için: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

**Windows tabanlı kümeler**için: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**Gereksinimler**

* Betikler hem **baş düğümlere** hem de **çalışan düğümlerine**uygulanmalıdır.

* Yüklemek istediğiniz jar dosyaları dışındaki 'ın Azure Blob depolama alanında **tek bir kapsayıcıda**depolanması gerekir.

* Jar dosyalarının kitaplığını içeren depolama hesabı, oluşturma sırasında HDInsight **kümesine bağlanmalıdır.** Bu, varsayılan depolama hesabı ya da __isteğe bağlı yapılandırma__üzerinden eklenmiş bir hesap olmalıdır.

* Kapsayıcının ınsıb yolu, komut dosyası eyleminin parametresi olarak belirtilmelidir. Örneğin, JAR dosyaları dışındaki, **depolamam**adlı bir depolama hesabındaki **LIBS** adlı bir kapsayıcıda depolanıyorsa, parametre **wasb://libs\@Mystorage.blob.Core.Windows.net/** olur.

  > [!NOTE]  
  > Bu belge, zaten bir depolama hesabı, blob kapsayıcısı oluşturmuş olduğunuzu ve dosyaları ona yüklediğinizi varsayar.
  >
  > Bir depolama hesabı oluşturmadıysanız, [Azure Portal](https://portal.azure.com)aracılığıyla bunu yapabilirsiniz. Daha sonra [Azure Depolama Gezgini](https://storageexplorer.com/) gibi bir yardımcı programını kullanarak hesapta bir kapsayıcı oluşturabilir ve dosyaları karşıya yükleyebilirsiniz.

## <a name="create-a-cluster-using-the-script"></a>Betiği kullanarak bir küme oluşturma

> [!NOTE]  
> Aşağıdaki adımlarda, Linux tabanlı HDInsight kümesi oluşturulur. Windows tabanlı bir küme oluşturmak için, kümeyi oluştururken küme işletim sistemi olarak **Windows** ' u seçin ve Bash betiği yerine Windows (PowerShell) betiğini kullanın.
>
> Bu betiği kullanarak bir küme oluşturmak için Azure PowerShell veya HDInsight .NET SDK 'sını de kullanabilirsiniz. Bu yöntemleri kullanma hakkında daha fazla bilgi için bkz. [HDInsight kümelerini betik eylemleriyle özelleştirme](hdinsight-hadoop-customize-cluster-linux.md).

1. [Linux 'Ta HDInsight kümeleri sağlama](hdinsight-hadoop-provision-linux-clusters.md)bölümündeki adımları kullanarak bir kümeyi sağlamaya başlayın, ancak sağlamayı tamamlamayın.

2. **Isteğe bağlı yapılandırma** bölümünde **betik eylemleri**' ni seçin ve aşağıdaki bilgileri sağlayın:

   * **Ad**: betik eylemi için kolay bir ad girin.

   * **Betik URI 'si**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh.

   * **Head**: Bu seçeneği işaretleyin.

   * **Çalışan**: Bu seçeneği işaretleyin.

   * **Zookeeper**: bu alanı boş bırakın.

   * **Parametreler**: Cars 'ı içeren kapsayıcı ve depolama hesabına ait olan adresini girin. Örneğin, **wasb://libs\@Mystorage.blob.Core.Windows.net/** .

3. **Betik eylemlerinin**en altında, yapılandırmayı kaydetmek için **Seç** düğmesini kullanın.

4. **Isteğe bağlı yapılandırma** bölümünde **bağlı depolama hesapları** ' nı seçin ve **depolama anahtarı Ekle** bağlantısını seçin. Jars 'ı içeren depolama hesabını seçin. Ardından **seçme** düğmelerini kullanarak ayarları kaydedin ve **isteğe bağlı yapılandırmayı**döndürün.

5. İsteğe bağlı yapılandırmayı kaydetmek için, **Isteğe bağlı yapılandırma** bölümünün altındaki **Seç** düğmesini kullanın.

6. [Linux 'Ta HDInsight kümeleri sağlama](hdinsight-hadoop-provision-linux-clusters.md)bölümünde açıklandığı gibi kümeyi sağlamaya devam edin.

Küme oluşturma işlemi tamamlandıktan sonra, `ADD JAR` ifadesini kullanmak zorunda kalmadan bu betik aracılığıyla eklenen jar dosyaları dışındaki 'ı kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Hive ile çalışma hakkında daha fazla bilgi için bkz. [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
