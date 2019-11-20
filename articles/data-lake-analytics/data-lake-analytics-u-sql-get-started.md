---
title: Azure Data Lake Analytics 'de U-SQL dili ile çalışmaya başlama
description: Azure Data Lake Analytics 'deki U-SQL dilinin temellerini öğrenin. Dosyalardan daha fazla veri eklemek, satır kümesini dönüştürmek ve verileri toplamak için değişkenleri kullanarak ilk sorgunuzu yazın.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 8130679dcc519cecd25abf43902c003ad8047df3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672831"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics 'de U-SQL ile çalışmaya başlama
U-SQL, herhangi bir ölçekte verileri işlemenizi sağlamak için C# , BILDIRIME dayalı SQL 'i kesinlik ile birleştiren bir dildir. U-SQL ' i n ölçeklenebilir, dağıtılmış sorgu özelliği aracılığıyla Azure SQL veritabanı gibi ilişkisel depolarda verileri verimli bir şekilde çözümleyebilirsiniz. U-SQL ile, okuma ve özel mantık ve UDF 'ler ekleme üzerine şema uygulayarak yapılandırılmamış verileri işleyebilirsiniz. Ayrıca, U-SQL, ölçekte nasıl yürütülebilecek konusunda ayrıntılı denetim sağlayan genişletilebilirlik içerir. 

## <a name="learning-resources"></a>Öğrenme kaynakları

* [U-SQL öğreticisi](https://aka.ms/usqltutorial) , u-SQL dilinin büyük bir adım adım kılavuz sağlar. Bu belge, U-SQL öğrenmekle mücadele eden tüm geliştiriciler için okunmaya önerilir.
* **U-SQL dili sözdizimi**hakkında ayrıntılı bilgi için, bkz. [u-SQL dili başvurusu](https://docs.microsoft.com/u-sql/).
* **U-SQL tasarım FI**'sini anlamak Için, Visual Studio blog gönderisine ( [u-SQL tanıtımı), büyük veri işlemeyi kolay hale getiren bir dili](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)inceleyin.

## <a name="prerequisites"></a>Önkoşullar

Bu belgedeki U-SQL örneklerinden geçmeden önce [Öğreticisi ' ni okuyun ve tamamlayın: Visual Studio @ no__t-0 için Data Lake araçları kullanarak U-SQL betikleri geliştirin. Bu öğreticide, Visual Studio için Azure Data Lake Araçları ile U-SQL kullanma mekanizması açıklanmaktadır.

## <a name="your-first-u-sql-script"></a>İlk U-SQL betiğiniz

Aşağıdaki U-SQL betiği basittir ve U-SQL dilinin birçok yönlerini araştırmamızı sağlar.

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Bu betikte herhangi bir dönüştürme adımı yok. @No__t-0 adlı kaynak dosyadan okur, şemalar ve satır kümesini SearchLog-first-u-sql. csv adlı bir dosyaya geri yazar.

@No__t-0 alanındaki veri türünün yanındaki soru işaretine dikkat edin. @No__t-0 alanının null olabileceği anlamına gelir.

### <a name="key-concepts"></a>Önemli kavramlar
* **Satır kümesi değişkenleri**: Bir satır kümesi üreten her sorgu ifadesi bir değişkene atanabilir. U-SQL, betikteki T-SQL değişken adlandırma düzenine (örneğin `@searchlog`) uyar.
* **Extract** anahtar sözcüğü bir dosyadaki verileri okur ve okunan şemayı tanımlar. `Extractors.Tsv`, sekmeyle ayrılmış değer dosyaları için yerleşik bir U-SQL ayıklayıcısı. Özel ayıklayıcıları geliştirebilirsiniz.
* **Çıktı** , verileri bir satır kümesinden dosyaya yazar. `Outputters.Csv()`, virgülle ayrılmış değer dosyası oluşturmak için yerleşik bir U-SQL çıktıcısı olur. Özel çıktıcılar geliştirebilirsiniz.

### <a name="file-paths"></a>Dosya yolları

AYıKLA ve OUTPUT deyimleri dosya yollarını kullanır. Dosya yolları mutlak veya göreli olabilir:

Aşağıdaki mutlak dosya yolu, Data Lake Store `mystore` adlı bir dosyaya başvurur:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Aşağıdaki dosya yolu `"/"` ile başlar. Varsayılan Data Lake Store hesabındaki bir dosyaya başvurur:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Skalar değişkenler kullanın

Skalar değişkenleri kullanabilir ve betik bakımının daha kolay olmasını sağlayabilirsiniz. Önceki U-SQL betiği de şöyle yazılabilir:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Satır kümelerini Dönüştür

Satır kümelerini dönüştürmek için **Seç ' i** kullanın:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

WHERE yan tümcesi bir [ C# Boole ifadesi](/dotnet/csharp/language-reference/operators/index)kullanır. Kendi ifadelerinizi ve C# işlevlerinizi yapmak için ifade dilini kullanabilirsiniz. Hatta bunları mantıksal yarışmalar (and) ve ayırt edici (ORs) ile birleştirerek daha karmaşık filtreler de gerçekleştirebilirsiniz.

Aşağıdaki betik DateTime. Parse () yöntemini ve bir birlikte kullanır.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >İkinci sorgu, iki filtrenin bileşimini oluşturan ilk satır kümesinin sonucu üzerinde çalışıyor. Ayrıca bir değişken adını yeniden kullanabilirsiniz ve adlar kapsama göre kapsamlandırılır.

## <a name="aggregate-rowsets"></a>Toplu satır kümeleri
U-SQL, size göre tanıdık SıRALAMA, gruplama ölçütü ve toplamaları sağlar.

Aşağıdaki sorgu, bölge başına toplam süreyi bulur ve sonra ilk beş süreyi sırasıyla görüntüler.

U-SQL satır kümeleri bir sonraki sorgu için sıralarını korumaz. Bu nedenle, bir çıktıyı sıralamak için OUTPUT ifadesine ORDER BY eklemeniz gerekir:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

U-SQL ORDER BY yan tümcesi bir SELECT ifadesinde FETCH yan tümcesinin kullanılmasını gerektirir.

U-SQL HAVING yan tümcesi, çıkış koşulunu karşılayan gruplarla sınırlamak için kullanılabilir:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
        GROUP BY Region
        HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Gelişmiş toplama senaryolarında, bkz. [toplu, analitik ve başvuru işlevleri](/u-sql/built-in-functions) için U-SQL başvurusu belgeleri

## <a name="next-steps"></a>Sonraki adımlar
* [Microsoft Azure Data Lake Analytics'e genel bakış](data-lake-analytics-overview.md)
* [Visual Studio için Data Lake Araçları'nı kullanarak U-SQL betikleri geliştirme](data-lake-analytics-data-lake-tools-get-started.md)
