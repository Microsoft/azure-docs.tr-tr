---
title: Linux tabanlı HDInsight 'ta Hadoop MapReduce ile .NET kullanma-Azure
description: Linux tabanlı HDInsight 'ta MapReduce akışı için .NET uygulamalarını nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 02/27/2018
ms.openlocfilehash: 9e626d08e7fd315ca70765477c5dae56030521c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866412"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Windows tabanlı HDInsight için .NET çözümlerini Linux tabanlı HDInsight 'a geçirme

Linux tabanlı HDInsight kümeleri mono kullanır [( https://mono-project.com) ](https://mono-project.com) .NET uygulamalarını çalıştırmak için). Mono, Linux tabanlı HDInsight ile MapReduce uygulamaları gibi .NET bileşenlerini kullanmanıza olanak sağlar. Bu belgede, Windows tabanlı HDInsight kümeleri için oluşturulan .NET çözümlerini, Linux tabanlı HDInsight 'ta Mono ile çalışacak şekilde geçirmeyi öğrenin.

## <a name="mono-compatibility-with-net"></a>.NET ile mono uyumluluğu

Tek bir sürüm 4.2.1, HDInsight sürüm 3,6 ' ye dahildir. HDInsight 'ta bulunan mono sürümü hakkında daha fazla bilgi için bkz. [HDInsight bileşen sürümleri](hdinsight-component-versioning.md).

Mono ve .NET arasındaki uyumlulukla ilgili daha fazla bilgi için bkz. [mono uyumluluğu https://www.mono-project.com/docs/about-mono/compatibility/) (](https://www.mono-project.com/docs/about-mono/compatibility/) belge.

> [!IMPORTANT]  
> SCP.NET Framework Mono ile uyumludur. Mono ile SCP.NET kullanma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Storm Için C# topolojileri geliştirmek üzere Visual Studio 'Yu kullanma](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Otomatik taşınabilirlik Analizi

[.Net taşınabilirlik Çözümleyicisi](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) , uygulamanız ve mono arasındaki uyumsuzluklar hakkında bir rapor oluşturmak için kullanılabilir. Çözümleyicisi 'ni, uygulamayı mono taşınabilirlik için denetleyecek şekilde yapılandırmak için aşağıdaki adımları kullanın:

1. [.Net taşınabilirlik Çözümleyicisi](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)'ni yükler. Yükleme sırasında, kullanılacak Visual Studio sürümünü seçin.

2. Visual Studio 2015 ' den   >  __taşınabilirlik Çözümleyicisi ayarlarını__ Çözümle ' yi seçin ve __mono__ bölümünde __4,5__ ' nin işaretli olduğundan emin olun.

    :::image type="content" source="./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png" alt-text="4,5, çözümleyici ayarları için mono bölümünde işaretlendi":::

    Yapılandırmayı kaydetmek için __Tamam ' ı__ seçin.

3. Analiz   >  __taşınabilirlik derlemesini__ Çözümle ' yi seçin. Çözümünüzü içeren derlemeyi seçin ve ardından Analize Başlamak için __Aç__ ' ı seçin.

4. Analiz tamamlandıktan sonra,   >  __Görünüm analiz raporlarını__ analiz et ' i seçin. __Taşınabilirlik Analizi sonuçlarında__ rapor açmak Için __raporu aç__ ' ı seçin.

    :::image type="content" source="./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png" alt-text="Taşınabilirlik Çözümleyicisi sonuçları iletişim kutusu":::

> [!IMPORTANT]  
> Çözümleyici, çözümünüzle ilgili her sorunu yakalayabilir. Örneğin, `c:\temp\file.txt` mono Windows üzerinde çalışıyorsa bir dosya yolu tamam olarak değerlendirilir. Aynı yol bir Linux platformunda geçerli değildir.

## <a name="manual-portability-analysis"></a>El ile taşınabilirlik Analizi

[Uygulama taşınabilirliği https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/) içindeki bilgileri kullanarak kodunuzun el ile denetimini gerçekleştirin (belge.

## <a name="modify-and-build"></a>Değiştir ve oluştur

HDInsight için .NET çözümlerini oluşturmak üzere Visual Studio 'Yu kullanmaya devam edebilirsiniz. Ancak, projenin 4,5 .NET Framework kullanacak şekilde yapılandırıldığından emin olmanız gerekir.

## <a name="deploy-and-test"></a>Dağıtın ve test edin

Çözümünüzü .NET taşınabilirlik Çözümleyicisi 'nden veya el ile bir analizde kullanarak değiştirdikten sonra, HDInsight ile test etmeniz gerekir. Çözümü Linux tabanlı HDInsight kümesinde test etmek, düzeltilmesi gereken hafif sorunları açığa çıkarmayabilir. Test ederken uygulamanızda ek günlüğe kaydetmeyi etkinleştirmenizi öneririz.

Günlüklere erişme hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

* [Linux tabanlı HDInsight 'ta YARN uygulama günlüklerine erişim Apache Hadoop](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight üzerinde MapReduce Ile C# kullanma](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Apache Hive ve Apache Pig ile C# Kullanıcı tanımlı işlevleri kullanma](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [HDInsight üzerinde Apache Storm için C# topolojileri geliştirme](storm/apache-storm-develop-csharp-visual-studio-topology.md)
