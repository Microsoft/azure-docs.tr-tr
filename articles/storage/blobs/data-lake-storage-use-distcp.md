---
title: DistCp kullanarak Azure Data Lake Storage 2. verileri kopyalama | Microsoft Docs
description: Data Lake Storage 2. veri kopyalamak için DistCp aracını kullanma
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3c09a95309e001def306698bbba4f6d0a1a2804d
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543662"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Azure depolama Blobları ve Azure Data Lake Storage 2. arasında veri kopyalamak için DistCp kullanma

Genel amaçlı v2 depolama hesabı ile genel amaçlı v2 depolama hesabı arasında verileri, hiyerarşik ad alanı etkinleştirilmiş olarak kopyalamak için [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) kullanabilirsiniz. Bu makale, DistCp aracını kullanma hakkında yönergeler sağlar.

DistCp, çeşitli komut satırı parametreleri sağlar ve kullanımınızı iyileştirmek için bu makaleyi okumanızı kesinlikle öneririz. Bu makalede, verileri hiyerarşik bir ad alanı etkin bir hesaba kopyalamak için kullanımına odaklanırken temel işlevsellik gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/pricing/free-trial/).
* **Data Lake Storage 2. yetenekleri olmayan mevcut bir Azure depolama hesabı (hiyerarşik ad alanı) etkin**.
* **Data Lake Storage 2. özelliği etkinleştirilmiş bir Azure depolama hesabı**. Bir oluşturma hakkında yönergeler için bkz. [Azure Data Lake Storage 2. Storage hesabı oluşturma](data-lake-storage-quickstart-create-account.md)
* Hiyerarşik ad alanı etkinleştirilmiş depolama hesabında oluşturulmuş **bir dosya sistemi** .
* Data Lake Storage 2. etkinleştirilmiş bir depolama hesabına erişimi olan **Azure HDInsight kümesi** . Bkz. [Azure HDInsight kümeleri ile Azure Data Lake Storage 2. kullanma](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Küme için Uzak Masaüstü 'Nü etkinleştirdiğinizden emin olun.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux kümesinden DistCp kullanma

An HDInsight küme, farklı kaynaklardaki verileri bir HDInsight kümesine kopyalamak için kullanılabilen DistCp yardımcı programıyla birlikte gelir. HDInsight kümesini Azure Blob depolamayı kullanacak şekilde yapılandırdıysanız ve birlikte Azure Data Lake Storage, verileri aynı zamanda kopyalamak için DistCp yardımcı programı kullanılabilir. Bu bölümde, DistCp yardımcı programının nasıl kullanılacağını inceleyeceğiz.

1. HDI kümenizde bir SSH oturumu oluşturun. Bkz. [Linux tabanlı HDInsight kümesine bağlanma](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Mevcut genel amaçlı v2 hesabınıza erişip erişemeyeceğinizi doğrulayın (hiyerarşik ad alanı etkin olmadan).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Çıktı, kapsayıcıdaki içeriklerin bir listesini sağlamalıdır.

3. Benzer şekilde, küme üzerinde etkinleştirilmiş hiyerarşik ad alanı ile depolama hesabına erişip erişemeyeceğinizi doğrulayın. Şu komutu çalıştırın:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Çıktı, Data Lake Storage hesabındaki dosyaların/klasörlerin bir listesini sağlamalıdır.

4. Verileri bir Data Lake Storage hesabına kopyalamak için DistCp kullanın.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Komut, blob depolamada **/example/Data/Gutenberg/** klasörünün içeriğini Data Lake Storage hesabındaki **/myFolder** klasörüne kopyalar.

5. Benzer şekilde, Data Lake Storage hesabındaki verileri BLOB depolama alanına (. 1) kopyalamak için DistCp kullanın.

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Komutu, Data Lake Store hesabındaki **/myFolder** içeriğini, **/example/Data/Gutenberg/** klasörüne kopyalar.

## <a name="performance-considerations-while-using-distcp"></a>DistCp kullanırken performans konuları

DistCp 'nin en düşük ayrıntı düzeyi tek bir dosya olduğundan, en fazla eşzamanlı kopya sayısını ayarlamak, Data Lake Storage karşı iyileştirmek için en önemli parametredir. Eşzamanlı kopya sayısı, komut satırındaki mappay (**e**) parametresinin sayısına eşittir. Bu parametre, verileri kopyalamak için kullanılan en fazla Map, eşleme sayısını belirtir. Varsayılan değer 20 ' dir.

**Örnek**

    hadoop distcp -m 100 wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Nasıl yaparım? kullanılacak mapbir sayı mı var?

Aşağıda kullanabileceğiniz bazı yönergeler verilmiştir.

* **1. Adım: ' Varsayılan ' YARN uygulama kuyruğu** için kullanılabilen toplam belleği belirle-ilk adım, ' varsayılan ' YARN uygulama sırasının kullanabileceği belleği belirlemektir. Bu bilgiler, kümeyle ilişkili olan ambarı portalında kullanılabilir. YARN ' ye gidin ve ' varsayılan ' uygulama sırasının kullanabileceği YARN belleğini görmek için configs sekmesini görüntüleyin. Bu, DistCp işiniz için (aslında MapReduce işi olan) toplam kullanılabilir bellektir.

* **2. Adım:**  Mapcontroller sayısını hesaplama-l değeri, Yarn kapsayıcı boyutuna bölünen toplam Yarn bellek bölümüne eşittir. YARN kapsayıcı boyut bilgileri ayrıca, ambarı portalında da mevcuttur. YARN 'ye gidin ve configs sekmesini görüntüleyin. YARN kapsayıcı boyutu bu pencerede görüntülenir. Mapcontroller (**e**) sayısına ulaşacak denklem

        m = (number of nodes * YARN memory for each node) / YARN container size

**Örnek**

Bir 4X D14v2s kümeniz olduğunu ve 10 farklı klasörden 10 TB veri aktarmaya çalıştığınız varsayıyoruz. Klasörlerin her biri farklı miktarda veri içerir ve her klasör içindeki dosya boyutları farklıdır.

* **Toplam YARN belleği**: Ambarı portalından, YARN belleğinin bir D14 düğümü için 96 GB olduğunu belirlersiniz. Bu nedenle, dört düğümlü küme için toplam YARN bellek: 

        YARN memory = 4 * 96GB = 384GB

* **Mapto sayısı**: Ambarı portalından, YARN kapsayıcısı boyutunun bir D14 küme düğümü için 3.072 MB olduğunu belirlersiniz. Bu nedenle, Mapper sayısı şu şekilde olur:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Diğer uygulamalar bellek kullanıyorsa, DistCp için yalnızca kümenizin YARN bellek belleğinin bir kısmını kullanmayı tercih edebilirsiniz.

### <a name="copying-large-datasets"></a>Büyük veri kümelerini kopyalama

Taşınacak veri kümesinin boyutu büyük olduğunda (örneğin, > 1 TB) ya da birçok farklı klasör varsa, birden çok TCP işi kullanmayı göz önünde bulundurmanız gerekir. Büyük olasılıkla performans kazancı yoktur, ancak iş başarısız olursa işin tamamı yerine yalnızca belirli bir işi yeniden başlatmanız gerekir.

### <a name="limitations"></a>Sınırlamalar

* DistCp, performansı iyileştirmek için boyut olarak benzer bir mapbir oluşturma girişiminde bulunur. Maprların sayısını artırmak performansı her zaman artırabilir.

* DistCp dosya başına yalnızca bir Eşleyici ile sınırlıdır. Bu nedenle, dosyalarınıza sahip olduğunuz için daha fazla mapto sahibi olmanız gerekir. DistCp bir dosyaya yalnızca bir Eşleyici atayabileceği için, bu, büyük dosyaları kopyalamak için kullanılabilecek eşzamanlılık miktarını sınırlandırır.

* Az sayıda büyük dosyanız varsa, size daha fazla eşzamanlılık sağlamak için bunları 256 MB dosya öbeklere bölmeniz gerekir.
