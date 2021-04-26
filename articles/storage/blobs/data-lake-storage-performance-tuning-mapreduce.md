---
title: 'Performansı ayarlama: MapReduce, HDInsight & Azure Data Lake Storage 2. | Microsoft Docs'
description: Eşleme performansını ayarlamaya yönelik yönergeleri anlamak Azure Data Lake Storage 2. üzerindeki işleri azaltır.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b95d37e1725940799750dbd3c29174d9855390d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912935"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Performansı ayarlama: MapReduce, HDInsight & Azure Data Lake Storage 2.

Harita azaltma işlerinin performansını ayarladığınızda göz önünde bulundurmanız gereken faktörleri anlayın. Bu makalede, bir dizi performans ayarlama Kılavuzu ele alınmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage 2. hesabı**. Bir oluşturma hakkında yönergeler için bkz. [hızlı başlangıç: Azure Data Lake Storage 2. depolama hesabı oluşturma](../common/storage-account-create.md).
* Data Lake Storage 2. hesabına erişimi olan **Azure HDInsight kümesi** . Bkz. [Azure HDInsight kümeleri ile Azure Data Lake Storage 2. kullanma](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
* **HDInsight üzerinde MapReduce kullanma**.  Daha fazla bilgi için bkz. [HDInsight 'Ta Hadoop 'Ta MapReduce kullanma](../../hdinsight/hadoop/hdinsight-use-mapreduce.md)
* **Data Lake Storage 2. performans ayarlama yönergeleri**.  Genel performans kavramları için bkz. [Data Lake Storage 2. performans ayarlama Kılavuzu](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametreler

MapReduce işlerini çalıştırırken, Data Lake Storage 2. performansı artırmak için yapılandırabileceğiniz parametreler şunlardır:

* **MapReduce. Map. Memory. MB** – her bir Eşleyici 'e ayrılacak bellek miktarı
* **MapReduce. job. Maps** : iş başına harita görevi sayısı
* **MapReduce. küçültme. Memory. MB** – her bir Reducer ayrılacak bellek miktarı
* **MapReduce. job. azaltması** : iş başına görev azaltma sayısı

**MapReduce. Map. Memory/MapReduce. küçültme. bellek** Bu sayı, eşleme için gereken bellek miktarına ve/veya görevi azaltmaya göre ayarlanmalıdır.  MapReduce. Map. Memory ve MapReduce. küçültme. belleğin varsayılan değerleri, Yarn yapılandırması aracılığıyla ambarı 'nda görüntülenebilir.  Ambarı 'nda YARN ' ye gidin ve configs sekmesini görüntüleyin.  YARN belleği görüntülenecektir.  

**MapReduce. job. Maps/MapReduce. job. azaltıyor** Bu, oluşturulacak maksimum Map, veya azaltıcının sayısını belirleyecek.  Bölme sayısı, MapReduce işi için kaç tane eşleme oluşturulacağını saptacaktır.  Bu nedenle, istenen mapgt sayısından daha az bölme varsa, istenenden daha az mapto alabilirsiniz.       

## <a name="guidance"></a>Rehber

> [!NOTE]
> Bu belgedeki kılavuz, uygulamanızın kümenizde çalışan tek uygulama olduğunu varsayar.

**1. Adım: çalışan işlerin sayısını belirleme**

Varsayılan olarak, MapReduce, işiniz için tüm kümeyi kullanır.  Kullanılabilir kapsayıcılardan daha az mapın kullanarak, daha az sayıda küme kullanabilirsiniz.        

**2. Adım: MapReduce. Map. Memory/MapReduce. küçültme. bellek ' i ayarlayın**

Eşleme için bellek boyutu ve görevlerin azaltılması, belirli bir işinize göre değişir.  Eşzamanlılık arttırmak istiyorsanız bellek boyutunu azaltabilirsiniz.  Eşzamanlı çalışan görevlerin sayısı, kapsayıcı sayısına bağlıdır.  Eşleyici veya Reducer başına bellek miktarını azaltarak, daha fazla eşleme oluşturulabilir ve bu da daha fazla mapbir veya azaltıcının 'ın eşzamanlı olarak çalışmasını sağlar.  Bellek miktarını çok fazla azaltmak, bazı işlemlerin belleğin tükenmesine neden olabilir.  İşinizi çalıştırırken bir yığın hatası alırsanız, Eşleyici veya Reducer başına belleği artırmanız gerekir.  Daha fazla kapsayıcı eklemenin, performansı düşürebilecek ek bir kapsayıcı için ek yük ekleytireceği göz önünde bulundurmanız gerekir.  Diğer bir seçenek de daha yüksek miktarda belleğe sahip olan veya kümenizdeki düğüm sayısını artıran bir küme kullanarak daha fazla bellek edinmaktır.  Daha fazla bellek daha fazla kapsayıcının kullanılmasını sağlayacaktır, bu da daha fazla eşzamanlılık anlamına gelir.  

**3. Adım: Toplam YARN belleği belirleme**

MapReduce. job. Maps/MapReduce. job. azaltıyor olması için, kullanılabilecek toplam YARN bellek miktarını dikkate almalısınız.  Bu bilgiler, ambarı 'nda mevcuttur.  YARN 'ye gidin ve configs sekmesini görüntüleyin.  YARN belleği Bu pencerede görüntülenir.  Toplam YARN belleği almak için, YARN belleğini kümenizdeki düğüm sayısıyla çarpmalısınız.

Toplam YARN bellek = düğüm * düğüm başına YARN bellek

Boş bir küme kullanıyorsanız, bellek kümeniz için toplam YARN bellek olabilir.  Diğer uygulamalar bellek kullanıyorsa, kullanmak istediğiniz kapsayıcı sayısına göre Map, veya azaltıcının sayısını azaltarak yalnızca kümenizin belleğinin bir bölümünü kullanabilirsiniz.  

**4. Adım: YARN kapsayıcıları sayısını hesaplama**

YARN kapsayıcıları, iş için kullanılabilir eşzamanlılık miktarını belirler.  Toplam YARN belleği alıp MapReduce. Map. Memory ile ayırın.  

\# YARN kapsayıcıları = toplam YARN bellek/MapReduce. Map. Memory

**5. Adım: MapReduce. job. Maps/MapReduce. job. azaltımı ayarlayın**

MapReduce. job. Maps/MapReduce. job., en az kullanılabilir kapsayıcı sayısına göre azaltılır.  Daha iyi performans elde ediyorsanız, bkz. mapbir ve azaltıcının sayısını artırarak daha fazla deneyebilirsiniz.  Daha fazla mapa 'nın daha fazla ek yüke sahip olacağını aklınızda bulundurun. bu nedenle, çok fazla eşleme olması performansı düşürebilir.  

CPU zamanlaması ve CPU yalıtımı varsayılan olarak kapalıdır, böylece YARN kapsayıcılarının sayısı bellekle sınırlandırılır.

## <a name="example-calculation"></a>Örnek hesaplama

8 D14 düğümden oluşan bir kümeniz olduğunu ve g/ç yoğunluklu bir işi çalıştırmak istiyoruz.  Yapmanız gereken hesaplamalar şunlardır:

**1. Adım: çalışan işlerin sayısını belirleme**

Bu örnekte, işimizin çalışan tek iş olduğunu varsayalım.  

**2. Adım: MapReduce. Map. Memory/MapReduce. küçültme. bellek ' i ayarlayın**

Bu örnekte, bir g/ç yoğun işi çalıştırdık ve eşleme görevleri için bir dizi belleğin yeterli olacağını seçiyoruz.

MapReduce. Map. Memory = 3GB

**3. Adım: Toplam YARN belleği belirleme**

Kümeden alınan toplam bellek, bir D14 = 768GB için 200 GB 'lık YARN bellek

**4. Adım: YARN kapsayıcıları sayısını hesaplama**

\# YARN kapsayıcıları = 768GB kullanılabilir bellek/3 GB bellek = 256

**5. Adım: MapReduce. job. Maps/MapReduce. job. azaltımı ayarlayın**

mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Çalıştırılacak örnekler

MapReduce 'nin Data Lake Storage 2. nasıl çalıştığını göstermek için aşağıdaki ayarlara sahip bir kümede çalıştırılan bazı örnek kodlar aşağıda verilmiştir:

* 16 düğüm D14v2
* HDI 3,6 çalıştıran Hadoop kümesi

Başlangıç noktası için, MapReduce Teragen, Terasort ve Teravalidate çalıştırmak için bazı örnek komutlar aşağıda verilmiştir.  Kaynaklarınıza göre bu komutları ayarlayabilirsiniz.

**Teragen**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input
```

**Terasort**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output
```

**Teravalidate**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
```