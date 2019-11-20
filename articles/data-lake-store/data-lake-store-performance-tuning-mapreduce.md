---
title: Azure Data Lake Storage 1. performans ayarlama-MapReduce
description: Azure Data Lake Storage 1. MapReduce performansı ayarlama yönergeleri
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904584"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight ve Azure Data Lake Storage 1. MapReduce için performans ayarlama Kılavuzu

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage 1. hesabı**. Bir oluşturma hakkında yönergeler için bkz. Azure Data Lake Storage 1. kullanmaya [başlama](data-lake-store-get-started-portal.md)
* Data Lake Storage 1. hesabına erişimi olan **Azure HDInsight kümesi** . Bkz. [Data Lake Storage 1. HDInsight kümesi oluşturma](data-lake-store-hdinsight-hadoop-use-portal.md). Küme için Uzak Masaüstü 'Nü etkinleştirdiğinizden emin olun.
* **HDInsight üzerinde MapReduce kullanma**. Daha fazla bilgi için bkz. [HDInsight 'Ta Hadoop 'Ta MapReduce kullanma](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Data Lake Storage 1. için performans ayarlama yönergelerini gözden geçirin**. Genel performans kavramları için bkz. [Data Lake Storage 1. performans ayarlama Kılavuzu](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametreler

MapReduce işlerini çalıştırırken, Data Lake Storage 1. performansını artırmak için yapılandırabileceğiniz en önemli parametreler şunlardır:

|Parametre      | Açıklama  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Her bir eşleştiriciyle ayrılacak bellek miktarı.  |
|`Mapreduce.job.maps`     |  İş başına harita görevi sayısı.  |
|`Mapreduce.reduce.memory.mb`     |  Her bir Reducer için ayrılacak bellek miktarı.  |
|`Mapreduce.job.reduces`    |   İş başına görev azaltma sayısı.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>MapReduce. Map. Memory/MapReduce. küçültme. bellek

Bu sayıyı eşleme için gereken bellek miktarına göre ayarlayın ve/veya görevi azaltın. Yarn yapılandırması aracılığıyla `mapreduce.map.memory` ve `mapreduce.reduce.memory` varsayılan değerlerini ambarı 'nda görüntüleyebilirsiniz. Ambarı 'nda YARN ' ye gidin ve **configs** sekmesini görüntüleyin. YARN belleği görüntülenecektir.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>MapReduce. job. Maps/MapReduce. job. azaltıyor

Bu, oluşturulacak maksimum Map, veya azaltıcının sayısını belirler. Bölme sayısı, MapReduce işi için kaç tane mapto oluşturulduğunu belirler. Bu nedenle, istenen mapgt sayısından daha az bölme varsa, istenenden daha az mapto alabilirsiniz.

## <a name="guidance"></a>Kılavuz

### <a name="step-1-determine-number-of-jobs-running"></a>1\. Adım: çalışan işlerin sayısını belirleme

Varsayılan olarak, MapReduce, işiniz için tüm kümeyi kullanır. Kullanılabilir kapsayıcılardan daha az maparacısız kullanarak, daha az sayıda eşleme kullanabilirsiniz. Bu belgedeki kılavuz, uygulamanızın kümenizde çalışan tek uygulama olduğunu varsayar.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>2\. Adım: MapReduce. Map. Memory/MapReduce. küçültme. bellek ' i ayarlayın

Eşleme için bellek boyutu ve görevlerin azaltılması, belirli bir işinize göre değişir. Eşzamanlılık arttırmak istiyorsanız bellek boyutunu azaltabilirsiniz. Eşzamanlı çalışan görevlerin sayısı, kapsayıcı sayısına bağlıdır. Eşleyici veya Reducer başına bellek miktarını azaltarak, daha fazla eşleme oluşturulabilir ve bu da daha fazla mapbir veya azaltıcının 'ın eşzamanlı olarak çalışmasını sağlar. Bellek miktarını çok fazla azaltmak, bazı işlemlerin belleğin tükenmesine neden olabilir. İşinizi çalıştırırken bir yığın hatası alırsanız, Eşleyici veya Reducer başına belleği artırın. Daha fazla kapsayıcı eklemenin, performansı düşürebilir olabilecek her ek kapsayıcı için ek yük eklemesini göz önünde bulundurun. Diğer bir seçenek de daha yüksek miktarda belleğe sahip olan veya kümenizdeki düğüm sayısını artıran bir küme kullanarak daha fazla bellek edinmaktır. Daha fazla bellek daha fazla kapsayıcının kullanılmasını sağlayacaktır, bu da daha fazla eşzamanlılık anlamına gelir.

### <a name="step-3-determine-total-yarn-memory"></a>3\. Adım: Toplam YARN belleği belirleme

MapReduce. job. Maps/MapReduce. job. azaltı ayarlamak için kullanılabilir toplam YARN bellek miktarını göz önünde bulundurun. Bu bilgiler, ambarı 'nda mevcuttur. YARN 'ye gidin ve **configs** sekmesini görüntüleyin. YARN belleği Bu pencerede görüntülenir. Toplam YARN belleği almak için, YARN belleğini kümenizdeki düğüm sayısıyla çarpın.

`Total YARN memory = nodes * YARN memory per node`

Boş bir küme kullanıyorsanız, bellek kümeniz için toplam YARN bellek olabilir. Diğer uygulamalar bellek kullanıyorsa, kullanmak istediğiniz kapsayıcı sayısına göre Map, veya azaltıcının sayısını azaltarak yalnızca kümenizin belleğinin bir bölümünü kullanabilirsiniz.

### <a name="step-4-calculate-number-of-yarn-containers"></a>4\. Adım: YARN kapsayıcıları sayısını hesaplama

YARN kapsayıcıları, iş için kullanılabilir eşzamanlılık miktarını belirler. Toplam YARN belleği alıp MapReduce. Map. Memory ile ayırın.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>5\. Adım: MapReduce. job. Maps/MapReduce. job. azaltımı ayarlayın

MapReduce. job. Maps/MapReduce. job., en az kullanılabilir kapsayıcı sayısına göre azaltılır. Daha iyi performans elde ediyorsanız, bkz. mapbir ve azaltıcının sayısını artırarak daha fazla deneyebilirsiniz. Daha fazla mapa 'nın daha fazla ek yüke sahip olacağını aklınızda bulundurun. bu nedenle, çok fazla eşleme olması performansı düşürebilir.

CPU zamanlaması ve CPU yalıtımı varsayılan olarak kapalıdır, böylece YARN kapsayıcılarının sayısı bellekle sınırlandırılır.

## <a name="example-calculation"></a>Örnek hesaplama

Şu anda 8 D14 düğümden oluşan bir kümeniz olduğunu ve g/ç yoğunluklu bir işi çalıştırmak istediğinizi varsayalım. Yapmanız gereken hesaplamalar şunlardır:

### <a name="step-1-determine-number-of-jobs-running"></a>1\. Adım: çalışan işlerin sayısını belirleme

Bizim örneğimiz için, işimizin tek çalışan olduğunu varsaytık.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>2\. Adım: MapReduce. Map. Memory/MapReduce. küçültme. bellek ' i ayarlayın

Bizim örneğimizde, g/ç yoğunluklu bir iş çalıştırıyorsunuz ve eşleme görevleri için 3 GB bellek yeterli olacak şekilde karar verdiniz.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>3\. Adım: Toplam YARN belleği belirleme

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>4\. Adım: YARN kapsayıcıları sayısını hesaplama

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>5\. Adım: MapReduce. job. Maps/MapReduce. job. azaltımı ayarlayın

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Sınırlamalar

**Data Lake Storage 1. azaltma**

Çok kiracılı bir hizmet olarak Data Lake Storage 1., hesap düzeyi bant genişliği sınırlarını ayarlar. Bu sınırlara ulaşırsanız, görev başarısızlıklarını görürsünüz. Bu, görev günlüklerinde azaltma hataları gözlemleyerek belirlenebilir. İşiniz için daha fazla bant genişliğine ihtiyacınız varsa lütfen bizimle iletişime geçin.

Kısıtlanıyor olup olmadığınızı denetlemek için istemci tarafında hata ayıklama günlüğünü etkinleştirmeniz gerekir. Şunları yapabilirsiniz:

1. Aşağıdaki özelliği > YARN > config > Advanced Yarn-Log4J: Log4J. günlükçü. com. Microsoft. Azure. datalake. Store = DEBUG ' daki Log4J özelliklerine yerleştirin

2. Yapılandırma için tüm düğümleri/hizmeti yeniden başlatın.

3. Azaldıysanız, YARN günlük dosyasında HTTP 429 hata kodunu görürsünüz. YARN günlük dosyası/t MP/&lt;User&gt;/yarn.log

## <a name="examples-to-run"></a>Çalıştırılacak örnekler

MapReduce 'nin Data Lake Storage 1. üzerinde nasıl çalıştığını göstermek için aşağıdaki ayarlara sahip bir kümede çalıştırılan bazı örnek kodlar aşağıda verilmiştir:

* 16 düğüm D14v2
* HDI 3,6 çalıştıran Hadoop kümesi

Başlangıç noktası için, MapReduce Teragen, Terasort ve Teravalidate çalıştırmak için bazı örnek komutlar aşağıda verilmiştir. Kaynaklarınıza göre bu komutları ayarlayabilirsiniz.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
