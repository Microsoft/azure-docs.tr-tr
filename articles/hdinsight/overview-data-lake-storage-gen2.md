---
title: HDInsight 'ta Azure Data Lake Storage 2. genel bakış
description: HDInsight 'ta Data Lake Storage 2. genel bakış.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: e69838f18efc08d0f64dd9ea904f502617073a8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938869"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>HDInsight 'ta Azure Data Lake Storage 2. genel bakış

Azure Data Lake Storage 2., Azure Data Lake Storage 1. temel özellikleri alır ve bunları Azure Blob depolama alanına tümleştirir. Bu özellikler Hadoop, Azure Active Directory (Azure AD) ve POSIX tabanlı erişim denetim listeleri (ACL 'Ler) ile uyumlu bir dosya sistemi içerir. Bu bileşim Azure Data Lake Storage 1. performansından yararlanmanızı sağlar. Ayrıca, blob depolamanın katmanlama ve veri yaşam döngüsü yönetimini de kullanıyor.

Azure Data Lake Storage 2. hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 2. giriş](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. temel işlevselliği

* **Hadoop ile uyumlu olan erişim:** Azure Data Lake Storage 2., verileri bir Hadoop Dağıtılmış Dosya Sistemi (bir) ile yaptığınız gibi yönetebilir ve erişebilirsiniz. Azure blob dosya sistemi (ABFS) sürücüsü, Azure HDInsight ve Azure Databricks dahil olmak üzere tüm Apache Hadoop ortamlarında kullanılabilir. Data Lake Storage 2. depolanan verilere erişmek için ABFS kullanın.

* **POSIX Izinlerinin bir üst kümesi:** Data Lake Gen2 için güvenlik modeli, Data Lake Storage 2. özgü bazı ayrıntı düzeyi ile birlikte ACL ve POSIX izinlerini destekler. Ayarlar, Apache Hive ve Apache Spark gibi yönetim araçları veya çerçeveler aracılığıyla yapılandırılabilir.

* **Maliyet verimliliği:** Data Lake Storage 2. düşük maliyetli depolama kapasitesi ve işlemler sunar. Azure Blob depolama ömrü döngüleri, verilerin yaşam döngüsü boyunca taşınmasıyla faturalandırma tarifelerinin daha düşük maliyetlerine yardımcı olur.

* **BLOB depolama araçları, çerçeveler ve uygulamalarla uyumluluk:** Data Lake Storage 2. blob depolamaya yönelik geniş bir araç, çerçeve ve uygulama dizisiyle çalışmaya devam eder.

* **İyileştirilmiş sürücü:** ABFS sürücüsü, büyük veri analizi için özel olarak iyileştirilmiştir. Karşılık gelen REST API 'Leri, dağıtılmış dosya sistemi (DFS) uç noktası üzerinden dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 ' deki yenilikler

### <a name="managed-identities-for-secure-file-access"></a>Güvenli dosya erişimi için Yönetilen kimlikler

Azure HDInsight, Azure Data Lake Storage 2. içindeki dosyalara küme erişiminin güvenliğini sağlamak için Yönetilen kimlikler kullanır. Yönetilen kimlikler, Azure hizmetlerini otomatik olarak yönetilen kimlik bilgileri kümesiyle sağlayan Azure Active Directory özelliğidir. Bu kimlik bilgileri, Active Directory kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanılabilir. Yönetilen kimliklerin kullanılması, kimlik bilgilerini kod veya yapılandırma dosyalarında depolamanızı gerektirmez.

Daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Azure blob dosya sistemi sürücüsü

Apache Hadoop uygulamalar yerel disk depolamadan veri okumayı ve yazmayı bekler. ABFS gibi bir Hadoop dosya sistemi sürücüsü, Hadoop uygulamalarının bulut depolamayla çalışmasını sağlar. , Normal Hadoop dosya sistemi işlemlerine öykünürken işe yarar. Sürücü, uygulamadan alınan komutları gerçek bulut depolama platformunun anladığı işlemlere dönüştürür.

Daha önce, Hadoop dosya sistemi sürücüsü, tüm dosya sistemi işlemlerini istemci tarafında Azure depolama REST API olarak dönüştüryordu. Ve sonra REST API çağırılır. Bununla birlikte, bu istemci tarafı dönüştürmesi, bir dosyanın yeniden adlandırılması gibi tek bir dosya sistemi işlemi için birden çok REST API çağrısı sonucu oluşur. ABFS, Hadoop dosya sistemi mantığını istemci tarafından sunucu kenarına taşımıştır. Azure Data Lake Storage 2. API 'SI artık BLOB API 'SI ile paralel olarak çalışır. Artık yaygın Hadoop dosya sistemi işlemleri tek bir REST API çağrısıyla yürütülemediğinden, bu geçiş performansı geliştirir.

Daha fazla bilgi için bkz. [Azure blob dosya sistemi sürücüsü (ABFS): Hadoop için adanmış bir Azure depolama sürücüsü](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 için URI düzeni

Azure Data Lake Storage 2., HDInsight 'tan Azure Storage 'daki dosyalara erişmek için yeni bir URI şeması kullanır:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI şeması, SSL şifreli erişim sağlar.

`<FILE_SYSTEM_NAME>` Data Lake Storage 2. dosya sisteminin yolunu tanımlar.

`<ACCOUNT_NAME>` Azure depolama hesabı adını tanımlar. Tam uygun etki alanı adı (FQDN) gereklidir.

`<PATH>` dosya ya da dizin \ yol adı.

`<FILE_SYSTEM_NAME>`Ve değerleri `<ACCOUNT_NAME>` belirtilmemişse, varsayılan dosya sistemi kullanılır. Varsayılan dosya sistemindeki dosyalar için göreli bir yol veya mutlak bir yol kullanın. Örneğin, `hadoop-mapreduce-examples.jar` HDInsight kümeleriyle birlikte gelen dosyaya aşağıdaki yollardan biri kullanılarak başvurulabilir:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> Dosya adı `hadoop-examples.jar` HDInsight sürüm 2,1 ve 1,6 kümelerinde bulunur. HDInsight dışındaki dosyalarla çalışırken, çoğu yardımcı programın ABFS biçimini tanımadığı ancak bunun yerine temel bir yol biçimi beklediği fark edeceksiniz `example/jars/hadoop-mapreduce-examples.jar` .

Daha fazla bilgi için bkz. [Azure Data Lake Storage 2. URI 'Si kullanma](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Lake Storage 2. Nesil'e giriş](../storage/blobs/data-lake-storage-introduction.md)
* [Azure Depolama’ya giriş](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage 1. Nesil’e genel bakış](./overview-data-lake-storage-gen1.md)