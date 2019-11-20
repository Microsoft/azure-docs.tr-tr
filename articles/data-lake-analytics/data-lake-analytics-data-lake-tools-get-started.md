---
title: Sorgu Azure Data Lake Analytics-Visual Studio
description: Visual Studio için Data Lake Araçları'nı nasıl yükleyeceğinizi ve U-SQL betiklerini nasıl geliştirip test edeceğinizi öğrenin.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: dacce0d4f40f077b5da6221000192a4398da99e0
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315783"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Visual Studio için Data Lake Araçları'nı kullanarak U-SQL betikleri geliştirme

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Data Lake ve Stream Analytics araçları, iki Azure hizmeti, Azure Data Lake Analytics ve Azure Stream Analytics ile ilgili işlevsellik içerir. Azure Stream Analytics senaryoları hakkında daha fazla bilgi için bkz. [Visual Studio için Azure Stream Analytics araçları](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Bu makalede, Azure Data Lake Analytics hesapları oluşturmak için Visual Studio 'Nun nasıl kullanılacağı açıklanır. İşleri [U-SQL](data-lake-analytics-u-sql-get-started.md)içinde tanımlayabilir ve Data Lake Analytics hizmetine işler gönderebilirsiniz. Data Lake Analytics hakkında daha fazla bilgi için bkz. [Azure Data Lake Analytics'e genel bakış](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Visual Studio için Azure Data Lake Araçları sürüm 2.3.3000.4 veya sonraki bir sürüme yükseltmenizi öneririz. Önceki sürümler artık indirilemiyor ve kullanım dışı.
>
> 1. Visual Studio için Azure Data Lake Araçları’nın 2.3.3000.4’ten önceki bir sürümünü kullanıp kullanmadığınızı denetleyin.
>
>    ![Araç sürümünü denetleyin](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Sürümünüz 2.3.3000.4 sürümünden eskiyse, indirme merkezini ziyaret ederek Visual Studio için Azure Data Lake Araçları sürümünüzü güncelleştirin:
>    - [Visual Studio 2017 ve 2019 için](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Visual Studio 2013 ve 2015 için](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Önkoşullar

* **Visual Studio**: Express dışındaki tüm sürümler desteklenir.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **.NET için Microsoft Azure SDK** 2.7.1 sürümü veya sonraki sürümleri. [Web platformu yükleyicisini](https://www.microsoft.com/web/downloads/platform.aspx) kullanarak yükleyin.
* **Data Lake Analytics** hesabı. Hesap oluşturmak için bkz. [Azure portalı kullanarak Azure Data Lake Analytics ile çalışmaya başlama](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Visual Studio için Azure Data Lake Araçları’nı yükleme

Bu öğretici, Visual Studio için Data Lake Araçları’nın yüklü olmasını gerektirir. Daha fazla bilgi için bkz. [Visual Studio için Data Lake araçları 'Nı yükler](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Azure Data Lake Analytics hesabına bağlanma

1. Visual Studio'yu açın.

1. **Sunucu Gezgini** **Sunucu Gezgini** **göster** > ' i seçerek açın.

1. **Azure**' a sağ tıklayıp **Microsoft Azure aboneliğine Bağlan**' ı seçin. **Hesabınızda oturum açmak için**yönergeleri izleyin.

1. **Sunucu Gezgini**' de **Azure** > **Data Lake Analytics**' nı seçin. Data Lake Analytics hesaplarınızın listesini görürsünüz.

## <a name="write-your-first-u-sql-script"></a>İlk U-SQL betiğinizi yazma

Aşağıda basit bir U-SQL betiği gösterilmiştir. Küçük bir veri kümesini tanımlar ve bu veri kümesini `/data.csv` adlı bir dosya olarak varsayılan Data Lake Store’a yazar.

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Data Lake Analytics işi gönderme

1. Visual Studio'da **dosya** > **yeni** > **proje**.

1. **U-SQL proje** türünü seçin ve ardından **İleri**' yi seçin. **Yeni projenizi yapılandırın**bölümünde **Oluştur**' u seçin.

   Visual Studio, bir **Script. usql** dosyası içeren bir çözüm oluşturur.

1. Betiği [Ilk U-SQL betiğinizden](#write-your-first-u-sql-script) **Script. usql** penceresine yapıştırın.

1. **Çözüm Gezgini**, **Script. usql**öğesine sağ tıklayın ve **betiği gönder**' i seçin.

1. **Işi gönder**' de Data Lake Analytics hesabınızı seçin ve **Gönder**' i seçin.

   ![U-SQL Visual Studio projesini gönderme](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

İş gönderildikten sonra açılan **İş görünümü** sekmesinde işin ilerleme durumu gösterilir.

* **İş Özeti**, işin özetini gösterir.
* **İş Grafı**, işin ilerleme durumunu görselleştirir.
* **Meta Veri İşlemleri**, U-SQL kataloğunda yapılan tüm işlemleri gösterir.
* **Veri** tüm girdileri ve çıktıları gösterir.
* **Durum Geçmişi** bölümünde zaman çizelgesi ve durum ayrıntıları gösterilir.
* **Au Analizi** işte kaç au kullanıldığını gösterir ve farklı au ayırma stratejilerinin benzetimlerini keşfedebilir.
* **Tanılama**, iş yürütme ve performans iyileştirme için gelişmiş bir analiz sağlar.

![U-SQL Visual Studio Data Lake Analytics iş performans grafiği](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

En son iş durumunu görmek ve ekranı yenilemek için **Yenile**' yi seçin.

## <a name="check-job-status"></a>İş durumunu kontrol etme

1. **Sunucu Gezgini**' de **Azure** > **Data Lake Analytics**' nı seçin.

1. Data Lake Analytics hesap adını genişletin.

1. **İşler**’e çift tıklayın.

1. Daha önce gönderdiğiniz işi seçin.

## <a name="see-the-job-output"></a>İş çıktısını görme

1. **Sunucu Gezgini**, gönderdiğiniz işe gidin.

1. **Veri** sekmesine tıklayın.

1. **İş Çıktıları** sekmesinde `"/data.csv"` dosyasını seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Test etmek ve hata ayıklamak için kendi iş istasyonunuzda U-SQL betiklerini çalıştırma](data-lake-analytics-data-lake-tools-local-run.md)
* [Visual Studio Code için Azure Data Lake Araçları'nı kullanarak U-SQL işlerindeki C# kodunda hata ayıklama](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Visual Studio Code için Azure Data Lake Araçları’nı kullanma](data-lake-analytics-data-lake-tools-for-vscode.md)
