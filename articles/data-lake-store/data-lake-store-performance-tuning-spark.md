---
title: Performans ayarlama-Azure Data Lake Storage 1. ile Spark
description: Azure HDInsight ve Azure Data Lake Storage 1. Spark için performans ayarlama yönergeleri hakkında bilgi edinin.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 8a39e14b70827947687b7613b9ff86f18017ddfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97722513"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight ve Azure Data Lake Storage 1. Spark için performans ayarlama Kılavuzu

Spark üzerinde performans ayarlaması yaparken, kümenizde çalıştırılacak uygulama sayısını göz önünde bulundurmanız gerekir. Varsayılan olarak, HDI kümenizde dört uygulamayı eşzamanlı olarak çalıştırabilirsiniz (Note: varsayılan ayar değişikliğe tabidir). Varsayılan ayarları geçersiz kılabilmeniz ve bu uygulamalar için kümeyi daha fazla kullanabilmek için daha az uygulama kullanmaya karar verebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage 1. hesabı**. Bir oluşturma hakkında yönergeler için bkz. Azure Data Lake Storage 1. kullanmaya [başlama](data-lake-store-get-started-portal.md)
* Data Lake Storage 1. hesabına erişimi olan **Azure HDInsight kümesi** . Bkz. [Data Lake Storage 1. HDInsight kümesi oluşturma](data-lake-store-hdinsight-hadoop-use-portal.md). Küme için Uzak Masaüstü 'Nü etkinleştirdiğinizden emin olun.
* **Data Lake Storage 1. Spark kümesi çalıştırılıyor**. Daha fazla bilgi için bkz. [Data Lake Storage 1. verileri çözümlemek Için HDInsight Spark kümesini kullanma](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)
* **Data Lake Storage 1. performans ayarlama yönergeleri**. Genel performans kavramları için bkz. [Data Lake Storage 1. performans ayarlama Kılavuzu](./data-lake-store-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parametreler

Spark işlerini çalıştırırken, Data Lake Storage 1. performansı artırmak için ayarlanabilir en önemli ayarlar şunlardır:

* **Sayı yürüticileri** -yürütülebilecek eşzamanlı görevlerin sayısı.

* **Yürütücü-bellek** -her bir yürütücü için ayrılan bellek miktarı.

* **Yürütücü-çekirdekler** -her bir yürütücü için ayrılan çekirdek sayısı.

**Sayı yürüticileri** Sayı yürüticileri, paralel olarak çalışabilecek en fazla görev sayısını ayarlar. Paralel olarak çalışabilecek görevlerin gerçek sayısı, kümenizde bulunan bellek ve CPU kaynaklarıyla sınırlıdır.

**Yürütücü-bellek** Bu, her bir yürütücü için ayrılan bellek miktarıdır. Her yürütücü için gereken bellek, işe bağımlıdır. Karmaşık işlemler için belleğin daha yüksek olması gerekir. Okuma ve yazma gibi basit işlemler için bellek gereksinimleri daha düşük olacaktır. Her bir yürütücü için bellek miktarı, ambarı 'nda görüntülenebilir. Ambarı 'nda Spark ' a gidin ve **configs** sekmesini görüntüleyin.

**Yürütücü-çekirdekler** Bu, her yürütücü için kullanılan çekirdek miktarını belirler ve bu, her yürütücü için çalıştırılabilen paralel iş parçacığı sayısını belirler. Örneğin, yürütücü-çekirdekler = 2 ise, her yürütücü yürütücü içinde 2 paralel görev çalıştırabilir. Gereken yürütücü-çekirdekler işe bağlı olacaktır. G/ç ağır işleri görev başına büyük miktarda bellek gerektirmez, böylece her bir yürütücü daha fazla paralel görevi işleyebilir.

Varsayılan olarak, HDInsight 'ta Spark çalıştırılırken her fiziksel çekirdek için iki sanal YARN çekirdeği tanımlanmıştır. Bu sayı, birden fazla iş parçacığından eşzamanlılık ve bağlam değiştirme miktarının iyi bir bakiyesini sağlar.

## <a name="guidance"></a>Rehber

Spark analitik iş yüklerini Data Lake Storage 1. verilerle çalışacak şekilde çalıştırırken en son HDInsight sürümünü kullanarak Data Lake Storage 1. en iyi performansı elde etmenizi öneririz. İşiniz daha yoğun olan g/ç olduğunda, performansı artırmak için bazı parametreler yapılandırılabilir. Data Lake Storage 1. yüksek aktarım hızını işleyebilen, yüksek düzeyde ölçeklenebilir bir depolama platformudur. İş, genellikle okuma veya yazma işlemleri içeriyorsa, g/ç için eşzamanlılık kullanımını Data Lake Storage 1., performansı artırabilir.

G/ç yoğun işleri için eşzamanlılık artırmanın birkaç genel yolu vardır.

**1. Adım: kümenizde kaç uygulama çalıştığını belirleme** -kümede, geçerli olanı de içeren kaç uygulama çalıştığını bilmeniz gerekir. Her Spark ayarının varsayılan değerleri, eşzamanlı olarak çalışan 4 uygulamanın olduğunu varsayar. Bu nedenle, kümenin yalnızca %25 ' i her bir uygulama için kullanılabilir olacak. Daha iyi performans sağlamak için, yürüticileri sayısını değiştirerek Varsayılanları geçersiz kılabilirsiniz.

**2. Adım: yürütücü-bellek ayarlama** – ayarlanacak ilk şey, yürütücü-bellek ' dir. Bellek, çalıştıracaksınız iş üzerine bağlı olacaktır. Her yürütücü için daha az bellek ayırarak eşzamanlılık artırabilirsiniz. İşinizi çalıştırdığınızda bellek dışında özel durumlar görürseniz, bu parametre için değeri artırmanız gerekir. Diğer bir seçenek de daha yüksek miktarda belleğe sahip olan veya kümenizin boyutunu artıran bir küme kullanarak daha fazla bellek edinmaktır. Daha fazla bellek, daha fazla eşzamanlılık sağlamak için daha fazla yürütme kullanılmasını sağlayacaktır.

**3. Adım: yürütücü-çekirdekleri ayarlama** – karmaşık işlemleri olmayan g/ç yoğunluklu iş yükleri için, yürütücü başına paralel görev sayısını artırmak için yüksek sayıda yürütücü çekirdeği ile başlamak iyi olur. Yürütücü-çekirdekleri 4 olarak ayarlamak iyi bir başlangıç.

```console
executor-cores = 4
```

Yürütücü sayısının artırılması, farklı yürütücü çekirdekleriyle denemeler yapabilmeniz için size daha paralellik verir. Daha karmaşık işlemlere sahip işler için, yürütücü başına çekirdek sayısını azaltmalısınız. Yürütücü-çekirdekleri 4 ' ten yüksek ayarlandıysa, çöp toplama verimsiz hale gelebilir ve performans düşebilir.

**4. Adım: kümede YARN bellek miktarını belirleme** – bu bilgiler, ambarı 'nda mevcuttur. YARN 'ye gidin ve Contigs sekmesini görüntüleyin. YARN belleği Bu pencerede görüntülenir.
Göz atın, Ayrıca, varsayılan YARN kapsayıcı boyutunu da görebilirsiniz. YARN kapsayıcı boyutu, yürütücü parametresi başına bellek ile aynıdır.

Toplam YARN bellek = düğüm * düğüm başına YARN bellek

**5. Adım: sayı yürüticileri hesaplama**

**Bellek kısıtlamasını hesapla** -NUM-eXecutors parametresi, bellek veya CPU tarafından sınırlandırılır. Bellek kısıtlaması, uygulamanız için kullanılabilir YARN bellek miktarına göre belirlenir. Toplam YARN belleği alın ve bunu yürütücü belleği ile ayırın. Kısıtlama, uygulama sayısına göre bölünebilmemiz için uygulamanın sayısı için de ölçeklendirilmesi gerekir.

Bellek kısıtlaması = (Toplam YARN bellek/yürütücü belleği)/uygulama sayısı

**CPU kısıtlaması hesapla** -CPU kısıtlaması, toplam sanal çekirdekler için, yürütücü başına çekirdek sayısı olarak hesaplanır. Her fiziksel çekirdek için 2 sanal çekirdek vardır. Bellek kısıtlamasına benzer şekilde, uygulama sayısına göre böleceğiz.

sanal çekirdekler = (kümedeki düğümler * 2 düğümündeki fiziksel çekirdek sayısı * 2) CPU kısıtlaması = (her yürütücü için toplam sanal çekirdek/çekirdek sayısı)/uygulama sayısı

**Num-Yürüticileri ayarlama** – NUM-eXecutors parametresi, en az bellek KıSıTLAMASı ve CPU kısıtlaması alınarak belirlenir. 

sayı-yürüticileri = dk (her yürütücü için toplam sanal çekirdek/çekirdek sayısı, kullanılabilir YARN bellek/yürütücü-bellek) ayarı, daha yüksek sayıda NUM-yürüticideki, performansı artırmayabilir. Daha fazla yürütme eklemenin, performansı düşürebilecek her ek yürütücü için ek yük eklemesini göz önünde bulundurmanız gerekir. Sayı yürüticileri, küme kaynaklarıyla sınırlıdır.

## <a name="example-calculation"></a>Örnek hesaplama

Şu anda, çalıştırabileceğiniz bir tane dahil olmak üzere iki uygulama çalıştıran 8 D4v2 düğümden oluşan bir kümeniz olduğunu varsayalım.

**1. Adım: kümenizde kaç uygulama çalıştığını belirleme** -kümenizde çalıştırdığınız bir de dahil olmak üzere, kümenizde iki uygulama olduğunu bilirsiniz.

**2. Adım: yürütücü-bellek ayarlama** – Bu örnek IÇIN, 6GB 'ın yoğun çalışan iş için yeterli olacağını belirledik.

```console
executor-memory = 6GB
```

**3. Adım: yürütücü-çekirdekleri ayarlama** – bu bir g/ç yoğun iş olduğundan, her bir yürütücü için çekirdek sayısını dört olarak ayarlayabiliriz. Yürütücü başına çekirdekleri dörtten büyük olarak ayarlamak çöp toplama sorunlarına neden olabilir.

```console
executor-cores = 4
```

**4. Adım: kümede Yarn bellek miktarını belirleme** – her bir D4V2 'ıN Yarn belleği 25 GB sahip olduğunu öğrenmek için ambarı 'na gittik. 8 düğüm olduğundan, kullanılabilir YARN belleği 8 ile çarpılır.

Toplam YARN bellek = düğüm * YARN bellek * düğüm başına toplam YARN bellek = 8 düğüm * 25 GB = 200 GB

**5. Adım: sayı Yürüticileri hesaplama** – NUM-eXecutors parametresi, bellek kısıtlamasının minimum değeri ve Spark üzerinde çalışan # of Apps tarafından ayrılmış CPU kısıtlaması alınarak belirlenir.

**Bellek kısıtlamasını hesapla** – bellek kısıtlaması, her yürütücü için belleğe bölünen toplam Yarn belleği olarak hesaplanır.

Bellek kısıtlaması = (Toplam YARN bellek/yürütücü belleği)/uygulama bellek kısıtlaması = (200 GB/6 GB)/2 Bellek kısıtlaması = 16 (yuvarlanmış) **CPU kısıtlamasını hesapla** -CPU kısıtlaması, toplam Yarn çekirdeği, yürütücü başına çekirdek sayısına bölünen şekilde hesaplanır.

YARN çekirdekler = kümedeki düğümler * düğüm başına çekirdek sayısı * 2 YARN çekirdek = 8 düğüm * D14 * 2 = 128 CPU kısıtlaması = (Toplam YARN çekirdek/yürütücü başına çekirdek sayısı 128)/2 CPU kısıtlaması = 16

**Sayı yürüticileri ayarlama**

NUM-yürüticileri = dk (bellek kısıtlaması, CPU kısıtlaması) sayı-yürüticileri = dk (16, 16) NUM-yürüticileri = 16