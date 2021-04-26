---
title: Apache Spark için .NET kullanın
description: Toplu işleme, gerçek zamanlı akış, makine öğrenimi ve Azure SYNAPSE Analytics not defterlerinde geçici sorgular yazmak için .NET ve Apache Spark kullanma hakkında bilgi edinin.
author: luisquintanilla
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: luquinta
ms.reviewer: jrasnick
ms.openlocfilehash: 8d045c1ec96bb7b31a710a28e30e3d428922b65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378559"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Azure Synapse Analytics ile Apache Spark için .NET’i kullanma

[Apache Spark için .net](https://dot.net/spark) , Spark için ücretsiz, [açık kaynak](https://github.com/dotnet/spark)ve platformlar arası .NET desteği sağlar. 

Spark API 'Leri ile C# ve F # aracılığıyla Spark API 'Lerine erişmenize olanak tanıyan .NET bağlamaları sağlar. Apache Spark için .NET ile, .NET ' te yazılmış Spark için Kullanıcı tanımlı işlevler yazabilir ve çalıştırabilirsiniz. Spark için .NET API 'Leri, Spark SQL, Delta Gölü ve yapılandırılmış akış dahil olmak üzere verilerinizi çözümlemenize yardımcı olan Spark veri çerçevelerinin her yönüyle erişmenizi sağlar.

Spark Batch iş tanımları veya etkileşimli Azure SYNAPSE Analytics Not defterleri aracılığıyla Apache Spark için .NET ile verileri analiz edebilirsiniz. Bu makalede her iki tekniği kullanarak Azure SYNAPSE Apache Spark için .NET kullanmayı öğreneceksiniz.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Spark iş tanımını kullanarak Batch işlerini gönderme

[SYNAPSE Spark havuzları için Apache Spark iş tanımları oluşturmak](apache-spark-job-definitions.md)üzere Azure SYNAPSE Analytics 'i nasıl kullanacağınızı öğrenmek için öğreticiyi ziyaret edin. Uygulamanızı Azure SYNAPSE 'a göndermek üzere paketlenmemiş olarak ayarlarsanız aşağıdaki adımları izleyin.

1. Uygulamanızı yayımlamak için aşağıdaki komutları çalıştırın. *MySparkApp* 'i uygulamanızın yoluyla değiştirdiğinizden emin olun.
   
   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.18.04-x64
   ```

2. Yayımlama klasörünün içeriği ( `publish.zip` Örneğin, 1. adım). Tüm derlemelerin ZIP dosyasının ilk katmanında olması ve ara klasör katmanı olmaması gerekir. Bu, sıkıştırmayı kaldırdığınızda `publish.zip` tüm derlemelerin geçerli çalışma dizininize ayıklandığını gösterir.

    **Windows 'da:**

    Dosyayı yayımlanan tüm ikili dosyalarla bin dizinine ayıklamak için [7-zip](https://www.7-zip.org/) veya [WinZip](https://www.winzip.com/)gibi bir ayıklama programı kullanın.

    **Linux 'ta:**

    Bir bash kabuğu ve CD 'yi, yayımlanan tüm ikili dosyalarla bin dizinine açın ve aşağıdaki komutu çalıştırın.

    ```bash
    zip -r publish.zip
    ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure SYNAPSE Analytics not defterlerinde Apache Spark için .NET 

Not defterleri, Apache Spark işlem hatları ve senaryolar için .NET uygulamanızı prototip için harika bir seçenektir. Verilerinizi hızla ve verimli bir şekilde çalışmaya başlayabilir, düzenleyebilir, filtreleyerek, görüntüleyerek ve görselleştirmeyi başlatabilirsiniz. 

Veri mühendisleri, veri bilimcileri, iş analistleri ve makine öğrenimi mühendisleri, paylaşılan ve etkileşimli bir belge üzerinde işbirliği yapabilirler. Veri araştırmasının anında sonuçları görürsünüz ve verilerinizi aynı not defterinde görselleştirebilirsiniz.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>Apache Spark Not defterleri için .NET kullanma

Yeni bir not defteri oluşturduğunuzda, iş mantığınızı ifade etmek istediğiniz bir dil çekirdeği seçersiniz. Çekirdek desteği C# dahil olmak üzere çeşitli dillerde kullanılabilir.

Azure SYNAPSE Analytics Not defterinizde Apache Spark için .NET kullanmak istiyorsanız, çekirdek olarak **.net Spark (C#)** öğesini seçin ve Not defterini var olan bir sunucusuz Apache Spark havuzuna ekleyin.

.Net Spark Not defteri, [.net etkileşimli](https://github.com/dotnet/interactive) deneyimlerine dayalıdır ve daha önceden tanımlanmış Spark oturum değişkeniyle birlikte Box for Spark for the .NET kullanma özelliği Ile etkileşimli C# deneyimleri sağlar `spark` .

### <a name="install-nuget-packages-in-notebooks"></a>NuGet paketlerini not defterlerine yükler

`#r nuget`NuGet paketinin adından önce MAGIC komutunu kullanarak, seçtiğiniz NuGet paketlerini Not defterinize yükleyebilirsiniz. Aşağıdaki diyagramda bir örnek gösterilmektedir:

![Spark .NET Not defteri NuGet paketini yüklemek için #r kullanmayı gösteren ekran görüntüsü](./media/apache-spark-development-using-notebooks/synapse-spark-dotnet-notebook-nuget.png)

Not defterlerinde NuGet paketleri ile çalışma hakkında daha fazla bilgi edinmek için bkz. [.net etkileşimli belgeleri](https://github.com/dotnet/interactive/blob/main/docs/nuget-overview.md).

### <a name="net-for-apache-spark-c-kernel-features"></a>Apache Spark C# çekirdek özellikleri için .NET

Azure SYNAPSE Analytics not defterinde Apache Spark için .NET kullandığınızda aşağıdaki özellikler mevcuttur:

* Bildirime dayalı HTML: üst bilgiler, madde işaretli listeler ve hatta görüntü görüntüleme gibi HTML sözdizimini kullanarak hücrelerden çıkış oluşturun.
* Basit C# deyimleri (atamalar, konsola yazdırma, özel durum atma vb.).
* Çok satırlık C# kod blokları (if deyimleri, Foreach döngüleri, sınıf tanımları vb.).
* Standart C# kitaplığına (System, LINQ, numaralar vb.) erişin.
* C# 8,0 dil özellikleri desteği.
* `spark` Apache Spark oturumunuza erişim sağlamak için önceden tanımlanmış bir değişken olarak.
* [Apache Spark içinde çalışabilen .NET Kullanıcı tanımlı işlevleri](/dotnet/spark/how-to-guides/udf-guide)tanımlama desteği. Apache Spark etkileşimli deneyimler için .NET 'te UDF 'Leri nasıl kullanacağınızı öğrenmek için [.net 'teki Apache Spark etkileşimli ortamlar Için Write ve Call UDF 'leri](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue) öneririz.
* Kitaplığı kullanarak farklı grafikleri (örneğin, çizgi, çubuk veya histogram) ve düzenleri (örneğin, tek, kaplama vb.) kullanarak Spark işlerinizde çıktıyı görselleştirmeye yönelik destek `XPlot.Plotly` .
* C# Not defterinize NuGet paketleri ekleme özelliği.

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark belgeleri için .NET](/dotnet/spark/)
* [Apache Spark etkileşimli kılavuzlar için .NET](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue)
* [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)
* [.NET etkileşimli](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
