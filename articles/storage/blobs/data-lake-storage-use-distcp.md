---
title: DistCp kullanarak Azure Data Lake Storage 2. verileri kopyalama | Microsoft Docs
description: Apache Hadoop Dağıtılmış kopyalama aracı 'nı (DistCp) kullanarak Azure Data Lake Storage 2. veri kopyalama.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e69a97a86a357fb36dde572f292b5cac7963d14a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912493"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Azure depolama Blobları ve Azure Data Lake Storage 2. arasında veri kopyalamak için DistCp kullanma

Genel amaçlı v2 depolama hesabı ile genel amaçlı v2 depolama hesabı arasında verileri, hiyerarşik ad alanı etkinleştirilmiş olarak kopyalamak için [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) kullanabilirsiniz. Bu makale, DistCp aracını kullanma hakkında yönergeler sağlar.

DistCp, çeşitli komut satırı parametreleri sağlar ve kullanımınızı iyileştirmek için bu makaleyi okumanızı kesinlikle öneririz. Bu makalede, verileri hiyerarşik bir ad alanı etkin bir hesaba kopyalamak için kullanımına odaklanırken temel işlevsellik gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* Data Lake Storage 2. yetenekleri olmayan mevcut bir Azure depolama hesabı (hiyerarşik ad alanı) etkin.
* Data Lake Storage 2. Özellikleri (hiyerarşik ad alanı) etkin bir Azure depolama hesabı. Bir oluşturma hakkında yönergeler için bkz. [Azure depolama hesabı oluşturma](../common/storage-account-create.md)
* Hiyerarşik ad alanı etkinleştirilmiş depolama hesabında oluşturulmuş bir kapsayıcı.
* Hiyerarşik ad alanı özelliği etkinleştirilmiş bir depolama hesabına erişimi olan bir Azure HDInsight kümesi. Bkz. [Azure HDInsight kümeleri ile Azure Data Lake Storage 2. kullanma](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Küme için Uzak Masaüstü 'Nü etkinleştirdiğinizden emin olun.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux kümesinden DistCp kullanma

An HDInsight küme, farklı kaynaklardaki verileri bir HDInsight kümesine kopyalamak için kullanılabilen DistCp yardımcı programıyla birlikte gelir. HDInsight kümesini Azure Blob depolamayı kullanacak şekilde yapılandırdıysanız ve birlikte Azure Data Lake Storage, verileri aynı zamanda kopyalamak için DistCp yardımcı programı kullanılabilir. Bu bölümde, DistCp yardımcı programının nasıl kullanılacağını inceleyeceğiz.

1. HDI kümenizde bir SSH oturumu oluşturun. Bkz. [Linux tabanlı HDInsight kümesine bağlanma](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Mevcut genel amaçlı v2 hesabınıza erişip erişemeyeceğinizi doğrulayın (hiyerarşik ad alanı etkin olmadan).

    ```bash
    hdfs dfs –ls wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/
    ```

   Çıktı, kapsayıcıdaki içeriklerin bir listesini sağlamalıdır.

3. Benzer şekilde, küme üzerinde etkinleştirilmiş hiyerarşik ad alanı ile depolama hesabına erişip erişemeyeceğinizi doğrulayın. Şu komutu çalıştırın:

    ```bash
    hdfs dfs -ls abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/
    ```

    Çıktı, Data Lake depolama hesabındaki dosyaların/klasörlerin bir listesini sağlamalıdır.

4. Verileri bir Data Lake Storage hesabına kopyalamak için DistCp kullanın.

    ```bash
    hadoop distcp wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
    ```

    Komut, blob depolamada **/example/Data/Gutenberg/** klasörünün içeriğini Data Lake Storage hesabındaki **/myFolder** klasörüne kopyalar.

5. Benzer şekilde, Data Lake Storage hesabındaki verileri BLOB depolama alanına (. 1) kopyalamak için DistCp kullanın.

    ```bash
    hadoop distcp abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg
    ```

    Komutu, Data Lake Store hesabındaki **/myFolder** içeriğini, **/example/Data/Gutenberg/** klasörüne kopyalar.

## <a name="performance-considerations-while-using-distcp"></a>DistCp kullanırken performans konuları

DistCp 'nin en düşük ayrıntı düzeyi tek bir dosya olduğundan, en fazla eşzamanlı kopya sayısını ayarlamak, Data Lake Storage karşı iyileştirmek için en önemli parametredir. Eşzamanlı kopya sayısı, komut satırındaki mappay (**e**) parametresinin sayısına eşittir. Bu parametre, verileri kopyalamak için kullanılan en fazla Map, eşleme sayısını belirtir. Varsayılan değer 20 ' dir.

**Örnek**

```bash
hadoop distcp -m 100 wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
```

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Nasıl yaparım? kullanılacak mapbir sayı mı var?

Aşağıda kullanabileceğiniz bazı yönergeler verilmiştir.

* **1. Adım: ' varsayılan ' YARN uygulama kuyruğu için kullanılabilen toplam belleği belirleme** -ilk adım, ' varsayılan ' YARN uygulama kuyruğu için kullanılabilir belleği belirlemektir. Bu bilgiler, kümeyle ilişkili olan ambarı portalında kullanılabilir. YARN ' ye gidin ve ' varsayılan ' uygulama sırasının kullanabileceği YARN belleğini görmek için configs sekmesini görüntüleyin. Bu, DistCp işiniz için (aslında MapReduce işi olan) toplam kullanılabilir bellektir.

* **2. Adım: mapcontroller sayısını hesaplama** -l DEĞERI, Yarn kapsayıcı boyutuna bölünen toplam Yarn bellek bölümüne eşittir.  YARN kapsayıcı boyut bilgileri ayrıca, ambarı portalında da mevcuttur. YARN 'ye gidin ve configs sekmesini görüntüleyin. YARN kapsayıcı boyutu bu pencerede görüntülenir. Mapcontroller (**e**) sayısına ulaşacak denklem

    d = (düğüm sayısı * her düğüm için YARN bellek)/YARN kapsayıcı boyutu

**Örnek**

Bir 4X D14v2s kümeniz olduğunu ve 10 farklı klasörden 10 TB veri aktarmaya çalıştığınız varsayıyoruz. Klasörlerin her biri farklı miktarda veri içerir ve her klasör içindeki dosya boyutları farklıdır.

* **Toplam YARN belleği**: ambarı portalından, Yarn belleğin bir D14 düğümü IÇIN 96 GB olduğunu belirlersiniz. Bu nedenle, dört düğümlü küme için toplam YARN bellek: 

    YARN bellek = 4 * 96GB = 384GB

* Eşleme **sayısı**: ambarı portalından, D14 küme düğümü için Yarn kapsayıcı boyutunun 3.072 MB olduğunu belirlersiniz. Bu nedenle, Mapper sayısı şu şekilde olur:

    m = (4 düğüm * 96gb)/3072mb = 128 map72

Diğer uygulamalar bellek kullanıyorsa, DistCp için yalnızca kümenizin YARN bellek belleğinin bir kısmını kullanmayı tercih edebilirsiniz.

### <a name="copying-large-datasets"></a>Büyük veri kümelerini kopyalama

Taşınacak veri kümesinin boyutu büyük olduğunda (örneğin, >1 TB) ya da birçok farklı klasör varsa, birden çok TCP işi kullanmayı göz önünde bulundurmanız gerekir. Büyük olasılıkla performans kazancı yoktur, ancak iş başarısız olursa işin tamamı yerine yalnızca belirli bir işi yeniden başlatmanız gerekir.

### <a name="limitations"></a>Sınırlamalar

* DistCp, performansı iyileştirmek için boyut olarak benzer bir mapbir oluşturma girişiminde bulunur. Maprların sayısını artırmak performansı her zaman artırabilir.

* DistCp dosya başına yalnızca bir Eşleyici ile sınırlıdır. Bu nedenle, dosyalarınıza sahip olduğunuz için daha fazla mapto sahibi olmanız gerekir. DistCp bir dosyaya yalnızca bir Eşleyici atayabileceği için, bu, büyük dosyaları kopyalamak için kullanılabilecek eşzamanlılık miktarını sınırlandırır.

* Az sayıda büyük dosyanız varsa, size daha fazla eşzamanlılık sağlamak için bunları 256 MB dosya öbeklere bölmeniz gerekir.