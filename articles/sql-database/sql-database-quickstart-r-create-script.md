---
title: Basit R betikleri oluşturma ve çalıştırma
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Azure SQL veritabanı Machine Learning Services (Önizleme) basit R betikleri çalıştırın.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: a47e7a81ba486056841bdc0fe65cfd10f1b2c412
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123188"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Azure SQL veritabanı 'nda basit R betikleri oluşturma ve çalıştırma Machine Learning Services (Önizleme)

Bu hızlı başlangıçta, [Azure SQL veritabanı 'nda Machine Learning Services (R ile)](sql-database-machine-learning-services-overview.md)genel önizlemesini kullanarak bir basit R betikleri kümesi oluşturup çalıştıracaksınız. [Sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) saklı yordamında iyi biçimlendirilmiş bir R betiğini nasıl kaydıracağınızı ve BETIĞI bir SQL veritabanında nasıl çalıştıracağınızı öğreneceksiniz.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/) .

- Örnek kodu bu alıştırmalarda çalıştırmak için öncelikle Machine Learning Services (R ile) etkin bir Azure SQL veritabanınızın olması gerekir. Microsoft, genel önizleme sırasında, mevcut veya yeni veritabanınız için sizi kullanıma sunulacaktır ve makine öğrenimini etkinleştirecektir. [Önizlemeye kaydolma](sql-database-machine-learning-services-overview.md#signup)adımlarını izleyin.

- En son [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) yüklediğinizden emin olun. R komut dosyalarını diğer veritabanı yönetimini veya sorgu araçlarını kullanarak çalıştırabilirsiniz, ancak bu hızlı başlangıçta SSMS 'yi kullanacaksınız.

- Bu hızlı başlangıç, sunucu düzeyinde bir güvenlik duvarı kuralı yapılandırmanızı gerektirir. Bunun nasıl yapılacağı hakkında bilgi için bkz. [sunucu düzeyinde güvenlik duvarı kuralı oluşturma](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Basit bir komut dosyası çalıştırma

R betiğini çalıştırmak için, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)sistem saklı yordamına bir bağımsız değişken olarak geçireceğiz.

Aşağıdaki adımlarda, SQL veritabanınızda Bu örnek R betiğini çalıştıracaksınız:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. **SQL Server Management Studio**’yu açın ve SQL veritabanınıza bağlanın.

   Bağlantı için yardıma ihtiyacınız varsa bkz [. hızlı başlangıç: Azure SQL veritabanına bağlanmak ve veritabanını](sql-database-connect-query-ssms.md)sorgulamak için SQL Server Management Studio kullanın.

1. Tüm R betiğini [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) saklı yordamına geçirin.

   Betiği, `@script` bağımsız değişkeniyle geçirilir. `@script` Bağımsız değişkenin içindeki her şey geçerli bir R kodu olmalıdır.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Hata alırsanız Machine Learning Services (R ile) genel önizleme sürümü SQL veritabanınız için etkinleştirilmemiş olabilir. Yukarıdaki [önkoşulları](#prerequisites) inceleyin.

   > [!NOTE]
   > Yönetici değilseniz, dış kodu otomatik olarak çalıştırabilirsiniz. Komutunu kullanarak diğer kullanıcılara izin verebilirsiniz:
   <br>**herhangi BIR dış BETIĞI yürütmeye ver** *Kullanıcıadı\>. \<*

2. Doğru sonuç hesaplanır ve R `print` işlevi sonucu **iletiler** penceresine döndürür.

   Şuna benzer görünmelidir.

    **Sonuçlar**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Merhaba Dünya betiği çalıştırma

Tipik bir örnek komut dosyası, yalnızca "Merhaba Dünya" dizesinin çıktısını veren bir örnektir. Aşağıdaki komutu çalıştırın.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Bu saklı yordamın girişleri şunlardır:

| | |
|-|-|
| @language | çağrılacak dil uzantısını tanımlar, bu örnekte R |
| @script | R çalışma zamanına geçirilen komutları tanımlar. Tüm R betiğinizin Unicode metin olarak bu bağımsız değişkende olması gerekir. Ayrıca, **nvarchar** türünde bir değişkene metin ekleyebilir ve sonra değişkeni çağırabilirsiniz |
| @input_data_1 | veri çerçevesi olarak SQL Server verileri döndüren R çalışma zamanına geçilen sorgu tarafından döndürülen veriler |
|SONUÇ KÜMELERIYLE | yan tümcesi, SQL Server için döndürülen veri tablosunun şemasını tanımlar, sütun adı olarak "Merhaba Dünya" ekler, veri türü için **int** |

Komut aşağıdaki metni verir:

| Hello World |
|-------------|
| 1\. |

## <a name="use-inputs-and-outputs"></a>Girişleri ve çıkışları kullanma

Varsayılan olarak, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) , genellikle GEÇERLI bir SQL sorgusu biçiminde sağladığınız tek bir veri kümesini girdi olarak kabul eder. Ardından, çıkış olarak tek bir R veri çerçevesini döndürür.

Şimdilik, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)öğesinin varsayılan giriş ve çıkış değişkenlerini kullanalım: **Inputdataset** ve **outputdataset**.

1. Test verilerinin küçük bir tablosunu oluşturun.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Tabloyu sorgulamak için ifadesini kullanın. `SELECT`
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Sonuçlar**

    ![RTestData tablosunun içeriği](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Aşağıdaki R betiğini çalıştırın. `SELECT` Deyimini kullanarak tablodaki verileri alır, R çalışma zamanına geçirir ve verileri veri çerçevesi olarak döndürür. Yan tümce SQL veritabanı için döndürülen veri tablosunun şemasını tanımlar, *newcolname*sütun adını ekler. `WITH RESULT SETS`

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Sonuçlar**

    ![Tablodan veri döndüren R betiğinin çıktısı](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Şimdi giriş ve çıkış değişkenlerinin adlarını değiştirelim. Varsayılan giriş ve çıkış değişkeni adları **ınputdataset** ve **outputdataset**, bu betik Ise adları **SQL_in** ve **SQL_out**olarak değiştirir:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    R 'nin büyük/küçük harfe duyarlı olduğunu unutmayın. R betiğindeki (**SQL_out**, **SQL_in**) giriş ve çıkış değişkenlerinin, ve `@input_data_1_name` `@output_data_1_name`ile tanımlanan değerlerle eşleşmesi gerekir (örneğin, büyük/küçük harf).

   > [!TIP]
   > Yalnızca bir giriş veri kümesi parametre olarak iletilebilir ve yalnızca bir veri kümesini döndürebilirsiniz. Ancak R kodunuzdan diğer tüm veri kümelerini çağırabilir ve veri kümesine ek olarak diğer türlerin çıkışlarını döndürebilirsiniz. Ayrıca OUTPUT anahtar sözcüğünü herhangi bir parametreye ekleyerek sonuçlarla birlikte döndürülmesini sağlayabilirsiniz.

1. Ayrıca, giriş verisi olmayan (`@input_data_1` boş olarak ayarlanır) R betiği kullanarak yalnızca değerler oluşturabilirsiniz.

   Aşağıdaki betik "Hello" ve "World" metnini çıktı olarak verir.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Sonuçlar**

    ![Giriş olarak @script kullanarak sonuçları sorgulama](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>R sürümünü denetleme

SQL veritabanınızda hangi R sürümünün yüklü olduğunu görmek isterseniz, aşağıdaki betiği çalıştırın.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

R `print` işlevi, sürümü **İletiler** penceresinde döndürür. Aşağıdaki örnek çıktıda, bu durumda SQL Database 'in R sürüm 3.4.4 yüklü olduğunu görebilirsiniz.

**Sonuçlar**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>R paketlerinin listesi

Microsoft, SQL veritabanınızda önceden Machine Learning Services ile birlikte yüklenmiş bir dizi R paketi sunar.

Sürüm, bağımlılıklar, lisans ve kitaplık yolu bilgileri de dahil olmak üzere hangi R paketlerinin yüklü olduğunu görmek için aşağıdaki betiği çalıştırın.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

Çıktı R 'den `installed.packages()` ve sonuç kümesi olarak döndürülür.

**Sonuçlar**

![R içindeki yüklü paketler](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Sonraki adımlar

SQL veritabanı 'nda R 'yi kullanarak bir makine öğrenimi modeli oluşturmak için bu hızlı başlangıcı izleyin:

> [!div class="nextstepaction"]
> [Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de tahmine dayalı bir model oluşturma ve eğitme](sql-database-quickstart-r-train-score-model.md)

R (Önizleme) ile Azure SQL veritabanı Machine Learning Services hakkında daha fazla bilgi için aşağıdaki makalelere bakın.

- [R ile Azure SQL veritabanı Machine Learning Services (Önizleme)](sql-database-machine-learning-services-overview.md)
- [Machine Learning Services kullanarak Azure SQL veritabanı 'nda gelişmiş R işlevleri yazma (Önizleme)](sql-database-machine-learning-services-functions.md)
- [Azure SQL veritabanı 'nda R ve SQL verileriyle çalışma Machine Learning Services (Önizleme)](sql-database-machine-learning-services-data-issues.md)
