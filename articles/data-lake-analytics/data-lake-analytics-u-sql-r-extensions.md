---
title: U-SQL betiklerini Azure Data Lake Analytics R ile Genişlet
description: Azure Data Lake Analytics kullanarak U-SQL betiklerinizde R kodu çalıştırmayı öğrenin. R kodunu satır içi veya dosyalardan başvuru olarak ekleyin.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: c5dd3f493e85afc925b639c142a293eed1e8cbd7
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672692"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>U-SQL betiklerini Azure Data Lake Analytics R Code ile genişletme

Aşağıdaki örnek, R kodu dağıtmaya yönelik temel adımları göstermektedir:
* U-SQL betiği için R uzantıları 'nı etkinleştirmek üzere `REFERENCE ASSEMBLY` ifadesini kullanın.
* Giriş verilerini bir anahtarda bölümlemek için `REDUCE` işlemini kullanın.
* U-SQL için R uzantıları, Reducer atanan her bir köşede R kodu çalıştıran yerleşik bir Reducer (`Extension.R.Reducer`) içerir. 
* U-SQL ve R arasında veri geçirmek için sırasıyla `inputFromUSQL` ve `outputToUSQL` adlı adanmış adlandırılmış veri çerçevelerinin kullanımı sabittir. giriş ve çıkış veri çerçevesi tanımlayıcı adları sabittir (yani, kullanıcılar bu giriş ve çıkış veri çerçevesi tanımlayıcılarının önceden tanımlı adlarını değiştiremezler).

## <a name="embedding-r-code-in-the-u-sql-script"></a>U-SQL betiğine R kodu katıştırma

@No__t-0 ' ın komut parametresini kullanarak U-SQL betiğinizin R kodunu satır içi yapabilirsiniz. Örneğin, R betiğini bir dize değişkeni olarak bildirebilir ve bunu Reducer parametresi olarak geçirebilirsiniz.


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>R kodunu ayrı bir dosyada tutun ve U-SQL betiğine başvurun

Aşağıdaki örnekte daha karmaşık bir kullanım gösterilmektedir. Bu durumda, R kodu U-SQL betiği olan bir kaynak olarak dağıtılır.

Bu R kodunu ayrı bir dosya olarak kaydedin.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Bu R betiğini RESOURCE ifadesiyle dağıtmak için bir U-SQL betiği kullanın.

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>R, U-SQL ile nasıl tümleştirilir?

### <a name="datatypes"></a>Türleriyle
* U-SQL ' den gelen dize ve sayısal sütunlar, R DataFrame ve U-SQL [desteklenen türler: `double`, `string`, `bool`, `integer`, `byte`] arasında olduğu gibi dönüştürülür.
* U-SQL ' de `Factor` veri türü desteklenmiyor.
* `byte[]`, Base64 kodlamalı bir `string` olarak serileştirilmelidir.
* U-SQL dizeleri r-SQL create R Input veri çerçevesini bir kez veya Reducer parametresi ayarlanarak `stringsAsFactors: true` olarak ayarlanarak R Code 'daki faktörlere dönüştürülebilir.

### <a name="schemas"></a>Şemalar
* U-SQL veri kümelerinde yinelenen sütun adları olamaz.
* U-SQL veri kümeleri sütun adları dize olmalıdır.
* Sütun adları U-SQL ve R betikleriyle aynı olmalıdır.
* ReadOnly sütunu output veri çerçevesinin parçası olamaz. Salt okunur sütunlar, UDO çıkış şemasının bir parçası ise U-SQL tablosuna otomatik olarak geri eklenmiş.

### <a name="functional-limitations"></a>İşlev sınırlamaları
* R altyapısı aynı işlemde iki kez başlatılamaz. 
* Şu anda U-SQL, Reducer UDOs kullanılarak oluşturulan bölümlenmiş modelleri kullanarak tahmin için birleştirici UDOs 'yi desteklemez. Kullanıcılar bölümlenmiş modelleri kaynak olarak bildirebilir ve bunları R betiklerinde kullanabilir (bkz. örnek kodu `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>R sürümleri
Yalnızca R 3.2.2 desteklenir.

### <a name="standard-r-modules"></a>Standart R modülleri

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>Giriş ve çıkış boyutu sınırlamaları
Her köşenin kendisine atanan sınırlı miktarda belleği vardır. Giriş ve çıkış veri çerçevelerinin R kodundaki bellekte bulunması gerektiğinden, giriş ve çıkış için toplam boyut 500 MB 'ı aşamaz.

### <a name="sample-code"></a>Örnek Kod
U-SQL gelişmiş analiz uzantıları 'nı yükledikten sonra Data Lake Store hesabınızda daha fazla örnek kod kullanılabilir. Daha fazla örnek kod için yol: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>U-SQL ile özel R modülleri dağıtma

İlk olarak, bir R özel modülü oluşturun ve ZIP yapın ve ardından daraltılmış R özel modül dosyasını ADL deponuza yükleyin. Bu örnekte, magittr_ 1.5. zip ' i, kullanmakta olduğumuz ADLA hesabı için varsayılan ADLS hesabının köküne yükleyeceğiz. Modülü ADL deposuna yükledikten sonra, U-SQL betiğinizdeki kullanılabilir hale getirmek için DAĞıTıM kaynağı kullan ' ı kullanın ve yüklemek için `install.packages` ' ı çağırın.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>Sonraki Adımlar
* [Microsoft Azure Data Lake Analytics'e genel bakış](data-lake-analytics-overview.md)
* [Visual Studio için Data Lake Araçları'nı kullanarak U-SQL betikleri geliştirme](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics işleri için U-SQL pencere işlevlerini kullanma](data-lake-analytics-use-window-functions.md)
