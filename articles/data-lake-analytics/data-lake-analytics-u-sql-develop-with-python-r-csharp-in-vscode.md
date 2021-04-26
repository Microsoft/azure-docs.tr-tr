---
title: Python, R ve C# ' de U-SQL işlerini çalıştırma-Azure Data Lake Analytics
description: Azure Data Lake içinde iş göndermek Için Python, R ve C# ile arkasındaki kodu nasıl kullanacağınızı öğrenin.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/22/2017
ms.custom: devx-track-python
ms.openlocfilehash: d6066bd6ec2a4c986ae17ad0cce3e7f6f73b21e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219981"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Visual Studio Code Azure Data Lake Analytics Için Python, R ve C# ile U-SQL geliştirme
U-SQL ile arka planda Python, R ve C# kodu yazmak ve Azure Data Lake hizmetine iş göndermek için Visual Studio Code (VSCode) kullanmayı öğrenin. VSCode için Azure Data Lake araçları hakkında daha fazla bilgi için bkz. [Visual Studio için Azure Data Lake araçları kodunu kullanma](data-lake-analytics-data-lake-tools-for-vscode.md).

Kod arkasındaki özel kodu yazmadan önce, VSCode 'da bir klasörü veya çalışma alanını açmanız gerekir.


## <a name="prerequisites-for-python-and-r"></a>Python ve R için Önkoşullar
ADL hesabınız için Python ve R uzantıları derlemelerini kaydettirin. 
1. Hesabınızı portalda açın.
   - **Genel bakış**'ı seçin. 
   - **Örnek komut dosyası**' na tıklayın.
2. **Daha fazla**'ya tıklayın.
3. **U-SQL uzantılarını Install** seçeneğini belirleyin. 
4. U-SQL uzantıları yüklendikten sonra onay iletisi görüntülenir. 

   ![Python ve R için ortamı ayarlama](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Python ve R dil hizmeti ile ilgili en iyi deneyimler için lütfen VSCode Python ve R uzantısını yüklemelisiniz. 

## <a name="develop-python-file"></a>Python dosyası geliştirme
1. Çalışma alanınızdaki **yeni dosya** ' ya tıklayın.
2. Kodunuzu U-SQL ' e yazın. Aşağıda bir kod örneği verilmiştir.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Bir betik dosyasına sağ tıklayın ve ardından **adl: dosya arkasında Python kodu oluştur**' u seçin. 
4. **Xxx.usql.py** dosyası çalışma klasörünüzde oluşturulur. Kodunuzu Python dosyasına yazın. Aşağıda bir kod örneği verilmiştir.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. **Usql** dosyasına sağ tıklayın, **betiği derle** veya işi çalışan iş **Gönder** ' e tıklayabilirsiniz.

## <a name="develop-r-file"></a>R dosyası geliştirme
1. Çalışma alanınızdaki **yeni dosya** ' ya tıklayın.
2. Kodunuzu U-SQL dosyasına yazın. Aşağıda bir kod örneği verilmiştir.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. **Usql** dosyasına sağ tıklayın ve ardından **adl: dosyanın arkasında R kodu oluştur**' u seçin. 
4. **Xxx. usql. r** dosyası çalışma klasörünüzde oluşturulur. Kodunuzu R dosyasına yazın. Aşağıda bir kod örneği verilmiştir.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. **Usql** dosyasına sağ tıklayın, **betiği derle** veya işi çalışan iş **Gönder** ' e tıklayabilirsiniz.

## <a name="develop-c-file"></a>C# dosyası geliştirme
Arka plan kod dosyası, tek bir U-SQL betiği ile ilişkili bir C# dosyasıdır. Arka plan kod dosyasında UDO, UDA, UDT ve UDF için adanmış bir komut dosyası tanımlayabilirsiniz. UıDO, UDA, UDT ve UDF, önce derlemeyi kaydetmeden doğrudan betikte kullanılabilir. Arka plan kod dosyası, eşleme U-SQL betik dosyası ile aynı klasöre konur. Betik xxx. usql olarak adlandırılmışsa, arka plan kodu xxx. usql. cs olarak adlandırılır. Arka plan kod dosyasını el ile silerseniz, arka plan kod özelliği, ilişkili U-SQL betiği için devre dışıdır. U-SQL betiği için müşteri kodu yazma hakkında daha fazla bilgi için, bkz. [u-SQL Için özel kod yazma ve kullanma: User-Defined işlevleri]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Çalışma alanınızdaki **yeni dosya** ' ya tıklayın.
2. Kodunuzu U-SQL dosyasına yazın. Aşağıda bir kod örneği verilmiştir.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. **Usql** dosyasına sağ tıklayın ve ardından **adl: dosya arkasında cs kod oluştur**' u seçin. 
4. **Xxx. usql. cs** dosyası çalışma klasörünüzde oluşturulur. Kodunuzu CS dosyasına yazın. Aşağıda bir kod örneği verilmiştir.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. **Usql** dosyasına sağ tıklayın, **betiği derle** veya işi çalışan iş **Gönder** ' e tıklayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
* [Visual Studio Code için Azure Data Lake Araçları’nı kullanma](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL yerel çalıştırma ve Visual Studio Code ile yerel hata ayıklama](data-lake-tools-for-vscode-local-run-and-debug.md)
* [PowerShell kullanarak Data Lake Analytics kullanmaya başlama](data-lake-analytics-get-started-powershell.md)
* [Azure portal kullanarak Data Lake Analytics kullanmaya başlama](data-lake-analytics-get-started-portal.md)
* [Visual Studio için Data Lake araçları 'nı kullanarak U-SQL uygulamaları geliştirme](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake Analytics (U-SQL) kataloğunu kullanma](./data-lake-analytics-u-sql-get-started.md)