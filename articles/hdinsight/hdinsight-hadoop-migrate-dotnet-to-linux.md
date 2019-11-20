---
title: Linux tabanlı HDInsight 'ta Hadoop MapReduce ile .NET kullanma-Azure
description: Linux tabanlı HDInsight 'ta MapReduce akışı için .NET uygulamalarını nasıl kullanacağınızı öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 4b402975a151d26e8f335c07930274c156ac52fb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498217"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Windows tabanlı HDInsight için .NET çözümlerini Linux tabanlı HDInsight 'a geçirme

Linux tabanlı HDInsight kümeleri, .NET uygulamalarını çalıştırmak için [Mono (https://mono-project.com)](https://mono-project.com) kullanır. Mono, Linux tabanlı HDInsight ile MapReduce uygulamaları gibi .NET bileşenlerini kullanmanıza olanak sağlar. Bu belgede, Windows tabanlı HDInsight kümeleri için oluşturulan .NET çözümlerini, Linux tabanlı HDInsight 'ta Mono ile çalışacak şekilde geçirmeyi öğrenin.

## <a name="mono-compatibility-with-net"></a>.NET ile mono uyumluluğu

Tek bir sürüm 4.2.1, HDInsight sürüm 3,6 ' ye dahildir. HDInsight 'ta bulunan mono sürümü hakkında daha fazla bilgi için bkz. [HDInsight bileşen sürümleri](hdinsight-component-versioning.md).

Mono ve .NET arasında uyumluluk hakkında daha fazla bilgi için bkz. [mono uyumluluk (https://www.mono-project.com/docs/about-mono/compatibility/)](https://www.mono-project.com/docs/about-mono/compatibility/) belgesi.

> [!IMPORTANT]  
> SCP.NET Framework Mono ile uyumludur. Mono ile SCP.NET kullanma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Storm için C# topolojiler geliştirmek üzere Visual Studio 'yu kullanma](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Otomatik taşınabilirlik Analizi

[.Net taşınabilirlik Çözümleyicisi](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) , uygulamanız ve mono arasındaki uyumsuzluklar hakkında bir rapor oluşturmak için kullanılabilir. Çözümleyicisi 'ni, uygulamayı mono taşınabilirlik için denetleyecek şekilde yapılandırmak için aşağıdaki adımları kullanın:

1. [.Net taşınabilirlik Çözümleyicisi](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)'ni yükler. Yükleme sırasında, kullanılacak Visual Studio sürümünü seçin.

2. Visual Studio 2015 ' den > __taşınabilirlik Çözümleyicisi ayarlarını__ __Çözümle__ ' yi seçin ve __mono__ bölümünde __4,5__ ' nin işaretli olduğundan emin olun.

    ![4,5, çözümleyici ayarları için mono bölümünde işaretlendi](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Yapılandırmayı kaydetmek için __Tamam ' ı__ seçin.

3. __Bütünleştirilmiş kod taşınabilirliği__analiz > __Çözümle__ ' yi seçin. Çözümünüzü içeren derlemeyi seçin ve ardından Analize Başlamak için __Aç__ ' ı seçin.

4. Analiz tamamlandıktan sonra analiz __raporlarını görüntüle__ > __Çözümle__ ' yi seçin. __Taşınabilirlik Analizi sonuçlarında__rapor açmak Için __raporu aç__ ' ı seçin.

    ![Taşınabilirlik Çözümleyicisi sonuçları iletişim kutusu](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Çözümleyici, çözümünüzle ilgili her sorunu yakalayabilir. Örneğin, mono Windows üzerinde çalışıyorsa `c:\temp\file.txt` dosya yolu tamam olarak kabul edilir. Aynı yol bir Linux platformunda geçerli değildir.

## <a name="manual-portability-analysis"></a>El ile taşınabilirlik Analizi

[Uygulama taşınabilirlik (https://www.mono-project.com/docs/getting-started/application-portability/)](https://www.mono-project.com/docs/getting-started/application-portability/) belgesindeki bilgileri kullanarak kodunuzun el ile denetimini gerçekleştirin.

## <a name="modify-and-build"></a>Değiştir ve oluştur

HDInsight için .NET çözümlerini oluşturmak üzere Visual Studio 'Yu kullanmaya devam edebilirsiniz. Ancak, projenin 4,5 .NET Framework kullanacak şekilde yapılandırıldığından emin olmanız gerekir.

## <a name="deploy-and-test"></a>Dağıtın ve test edin

Çözümünüzü .NET taşınabilirlik Çözümleyicisi 'nden veya el ile bir analizde kullanarak değiştirdikten sonra, HDInsight ile test etmeniz gerekir. Çözümü Linux tabanlı HDInsight kümesinde test etmek, düzeltilmesi gereken hafif sorunları açığa çıkarmayabilir. Test ederken uygulamanızda ek günlüğe kaydetmeyi etkinleştirmenizi öneririz.

Günlüklere erişme hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

* [Linux tabanlı HDInsight 'ta YARN uygulama günlüklerine erişim Apache Hadoop](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight C# üzerinde MapReduce ile kullanma](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Apache Hive C# ve Apache Pig ile Kullanıcı tanımlı işlevler kullanma](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [HDInsight C# üzerinde Apache Storm için topolojiler geliştirin](storm/apache-storm-develop-csharp-visual-studio-topology.md)
