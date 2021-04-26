---
title: Apache Spark için .NET, HDI 'de v 1.0 sürümüne güncelleştiriliyor
description: Apache Spark 1,0 sürümü için .NET güncelleştirme hakkında bilgi edinin ve bu, mevcut kodunuzu ve kümelerinizi nasıl etkilediğini öğrenin.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788138"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>HDInsight 'ta Apache Spark için .NET sürümü v 1.0 sürümüne güncelleştirme

Bu belge, [Apache Spark için .net](https://github.com/dotnet/spark)ilk ana sürümü ve HDInsight kümelerinde geçerli üretim işlem hatlarınızı nasıl etkileyebileceği hakkında bilgi ister.

## <a name="about-net-for-apache-spark-version-100"></a>Apache Spark sürüm 1.0.0 için .NET hakkında

Bu, Apache Spark için .NET 'in ilk [önemli resmi sürümüdür](https://github.com/dotnet/spark/releases/tag/v1.0.0) ve Spark 2,4. x Için DATAFRAME API 'sinin yanı sıra Spark 3.0. x ' i de diğer özelliklerle birlikte sunmaktadır. Tüm özelliklerin, geliştirmelerin ve hata düzeltmelerinin tamamen listesi için bkz. resmi [v 1.0.0 sürüm notları](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md).
Dikkat edilmesi gereken diğer önemli şeyler, bu sürümün ve önceki sürümleri ile uyumlu **olmaması** `Microsoft.Spark` `Microsoft.Spark.Worker` . .NET Apache Spark uygulamanızı v 1.0.0 ile uyumlu olacak şekilde yükseltmeyi planlıyorsanız [geçiş kılavuzuna](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) göz atın.

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>HDInsight 'ta Apache Spark v 1.0 için .NET kullanma

Geçerli HDI kümeleri etkilenmeyecektir (yani aynı sürüme sahip olmaya devam eder), yeni oluşturulan HDI kümeleri Apache Spark için .NET 'in bu en son v 1.0.0 sürümünü taşır. Bunun anlamı:

- **Daha eski BIR HDI kümeniz var**: Spark .NET uygulamanızı v 1.0.0 'e yükseltmek istiyorsanız (önerilir), `Microsoft.Spark.Worker` HDI kümenizdeki sürümü güncelleştirmeniz gerekir. Daha fazla bilgi için bkz. [Apache Spark için .NET sürümlerini HDI kümesi üzerinde değiştirme bölümü](#changing-net-for-apache-spark-version-on-hdinsight).
Uygulamanızdaki Apache Spark için geçerli .NET sürümünü güncelleştirmek istemiyorsanız başka bir adım gerekmez.  

- **Yeni BIR HDI kümeniz var**: Spark .NET uygulamanızı v 1.0.0 (önerilen) sürümüne yükseltmek istiyorsanız, HDI üzerindeki çalışanı değiştirmek için herhangi bir adım gerekmez, ancak kodunuzu ve işlem hatlarınızı güncelleştirmek için gereken adımları anlamak için [geçiş kılavuzuna](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) başvurmanız gerekir.
Uygulamanızdaki Apache Spark için geçerli .NET sürümünü değiştirmek istemiyorsanız, HDI kümenizdeki sürümü v 1.0 'dan (yeni kümelerde varsayılan) kullandığınız sürüme değiştirmeniz gerekir. Daha fazla bilgi için bkz. [Apache Spark için .NET sürümlerini HDI kümesi üzerinde değiştirme bölümü](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight).  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>HDInsight 'ta Apache Spark sürümü için .NET 'i değiştirme

### <a name="deploy-microsoftsparkworker"></a>Microsoft. spark. Worker 'ı dağıtma

`Microsoft.Spark.Worker` , Spark kümenizin ayrı çalışan düğümlerinde bulunan bir arka uç bileşenidir. Bir C# UDF (Kullanıcı tanımlı işlev) yürütmek istediğinizde, Spark 'ın bu UDF 'yi yürütmek için .NET CLR 'yi nasıl başlatacağınızı anlaması gerekir. `Microsoft.Spark.Worker` Spark için bu işlevi etkinleştiren sınıfların bir koleksiyonunu sağlar. HDI kümesinde dağıtmak istediğiniz Apache Spark için .NET sürümüne bağlı olarak çalışan sürümünü seçin.

1. Belirli sürümünüzün Microsoft. spark. Worker Linux sürümünü indirin. Örneğin, isterseniz `.NET for Apache Spark v1.0.0` [Microsoft. spark. Worker. netcoreapp 3.1. Linux-x64-1.0.0. tar. gz](https://github.com/dotnet/spark/releases/tag/v1.0.0)dosyasını indirirsiniz.  

2. Adım 1 ' de indirilen çalışan ikililerini HDI kümenizin tüm çalışan düğümlerine yüklemek için [install-Worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) betiğini indirin.  

3. Yukarıdaki belirtilen dosyaları kümenizin erişimi olan Azure depolama hesabına yükleyin. Daha fazla ayrıntı için [Apache Spark HDI dağıtım makalesine .net](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) bölümüne başvurabilirsiniz.

4. Betik `install-worker.sh` eylemlerini kullanarak, komut dosyasını kümenizin tüm çalışan düğümlerinde çalıştırın. Daha fazla bilgi için [Apache Spark HDI dağıtım makalesine .net](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) bölümüne bakın.

### <a name="update-your-application-to-use-specific-version"></a>Uygulamanızı belirli sürümü kullanacak şekilde güncelleştirme

.NET Apache Spark uygulamanızı, projenizde [Microsoft. Spark NuGet paketinin](https://www.nuget.org/packages/Microsoft.Spark/) gerekli sürümünü seçerek belirli bir sürümü kullanacak şekilde güncelleştirebilirsiniz. Uygulamanızı v 1.0.0 olarak güncelleştirmeyi tercih ediyorsanız, belirli sürümün ve [geçiş kılavuzunun](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) sürüm notlarını aşağıda belirtildiği gibi kullanıma aldığınızdan emin olun.

## <a name="faqs"></a>SSS

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>< 1.0.0 sürümü ile var olan HDI Kümemin yeni sürümle başarısız olmasına neden olacak?

Mevcut HDI kümeleri, Apache Spark için .NET için aynı önceki sürüme sahip olmaya devam edecektir ve mevcut uygulamanız (Spark .NET 'in önceki sürümüne sahip) etkilenmeyecektir.

## <a name="next-steps"></a>Sonraki adımlar

[HDInsight 'ta .NET Apache Spark uygulamanızı dağıtma](/dotnet/spark/tutorials/hdinsight-deployment)