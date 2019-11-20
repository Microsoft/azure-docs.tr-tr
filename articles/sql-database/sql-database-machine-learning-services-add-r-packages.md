---
title: Machine Learning Services bir R paketi ekleme (Önizleme)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Bu makalede, Azure SQL veritabanı Machine Learning Services (Önizleme) ' de zaten yüklü olmayan bir R paketinin nasıl yükleneceği açıklanır.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ms.openlocfilehash: ce85f45d823df42e70af53824e175968439621d3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819876"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Azure SQL veritabanı 'na R paketi ekleme Machine Learning Services (Önizleme)

Bu makalede, Azure SQL veritabanı Machine Learning Services (Önizleme) için bir R paketinin nasıl ekleneceği açıklanmaktadır.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Ön koşullar

- Yerel bilgisayarınıza [R](https://www.r-project.org) ve [rstudio Desktop](https://www.rstudio.com/products/rstudio/download/) 'ı yükler. R; Windows, MacOS ve Linux platformlarında kullanılabilir. Bu makalede, Windows kullandığınızı varsaymaktadır.

- Bu makalede, Azure SQL veritabanı 'nda R betiği çalıştırmak için [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) veya [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) kullanma örneği yer almaktadır. R komut dosyalarını diğer veritabanı yönetimini veya sorgu araçlarını kullanarak çalıştırabilirsiniz, ancak bu örnekte Azure Data Studio veya SSMS varsayılır.
   
> [!NOTE]
> Azure Data Studio veya SSMS 'de **sp_execute_external_script** kullanarak bir R betiği çalıştırarak bir paket yükleyemezsiniz. Bu makalede açıklandığı gibi, yalnızca R komut satırını ve RStudio 'Yu kullanarak paketleri yükleyebilir ve kaldırabilirsiniz. Paket yüklendikten sonra, **sp_execute_external_script**kullanarak bir R betiğindeki paket işlevlerine erişebilirsiniz.

## <a name="list-r-packages"></a>R paketlerinin listesi

Microsoft, Azure SQL veritabanınızda Machine Learning Services önceden yüklenmiş bir dizi R paketi sağlar.
Azure Data Studio veya SSMS 'de aşağıdaki komutu çalıştırarak yüklü R paketlerinin bir listesini görebilirsiniz.

1. Azure Data Studio veya SSMS 'yi açın ve Azure SQL veritabanınıza bağlanın.

1. Şu komutu çalıştırın:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

Çıktı aşağıdakine benzer görünmelidir.

**Sonuçlar**

![R içindeki yüklü paketler](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Sqlmlutils ile paket ekleme

Azure SQL veritabanınızda zaten yüklü olmayan bir paket kullanmanız gerekiyorsa, bunu [sqlmlutils](https://github.com/Microsoft/sqlmlutils)kullanarak yükleyebilirsiniz. **sqlmlutils** , kullanıcıların SQL veritabanları (SQL Server ve Azure SQL veritabanı) ile etkileşime geçmesini ve r veya Python istemcisinden SQL 'de r veya Python kodu yürütmesini sağlamaya yardımcı olmak için tasarlanan bir pakettir. Şu anda, Azure SQL veritabanı 'nda yalnızca **sqlmlutils** 'ın R sürümü destekleniyor.

Aşağıdaki örnekte, dizeleri biçimlendirebilir ve enterpolacağımız **[birleştirici](https://cran.r-project.org/web/packages/glue/)** paketini yükleyeceksiniz. Bu adımlar **sqlmlutils** ve **Rodbcext** ( **sqlmlutils**için bir önkoşul) yükler ve **tutkalla** paketini ekler.

### <a name="install-sqlmlutils"></a>**Sqlmlutils** 'i yükler

1. https://github.com/Microsoft/sqlmlutils/tree/master/R/dist 'den en son **sqlmlutils** ZIP dosyasını yerel bilgisayarınıza indirin. Dosyayı sıkıştırmayı açmanız gerekmez.

1. Bir **komut istemi** açın ve yerel bilgisayarınıza **Rodbcext** ve **sqlmlutils** yüklemek için aşağıdaki komutları çalıştırın. İndirdiğiniz **sqlmlutils** ZIP dosyasının tam yolunu değiştirin (örnek, dosyanın Belgeler klasörünüzde olduğunu varsayar).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Gördüğünüz çıktı aşağıdakine benzer olmalıdır.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > "' R" iç veya dış komut, çalıştırılabilir program veya toplu iş dosyası olarak tanınmıyor, büyük olasılıkla R. exe yolunun Windows 'daki **Path** ortam değişkenine dahil olmadığı anlamına gelir. Yolu ortam değişkenine ekleyebilir veya komut isteminde klasörüne gidebilir (örneğin `cd C:\Program Files\R\R-3.5.3\bin`) ve sonra komutu yeniden deneyebilirsiniz.

### <a name="add-the-package"></a>Paketi ekleyin

1. RStudio 'Yu açın ve yeni bir **R betik** dosyası oluşturun. 

1. **Sqlmlutils**kullanarak **tutkalla** paketini yüklemek için aşağıdaki R kodunu kullanın. Kendi Azure SQL veritabanı bağlantı bilgilerinizi yerine koyun.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > **Kapsam** **genel** veya **özel**olabilir. Genel kapsam, veritabanı yöneticisinin tüm kullanıcıların kullanabileceği paketleri yüklemesi açısından yararlıdır. Özel kapsam, paketi yalnızca onu yükleyen kullanıcı için kullanılabilir hale getirir. Kapsam değeri belirtmezseniz **PRIVATE** varsayılan kapsamı kullanılır.

### <a name="verify-the-package"></a>Paketi doğrulama

RStudio 'da aşağıdaki R betiğini çalıştırarak, **birleştirici** paketinin yüklendiğini doğrulayın. Önceki adımda tanımladığınız **bağlantıyı** kullanın.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Sonuçlar**

![RTestData tablosunun içeriği](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Paketi kullanın

Paket yüklendikten sonra, **sp_execute_external_script**aracılığıyla bir R betikte kullanabilirsiniz.

1. Azure Data Studio veya SSMS 'yi açın ve Azure SQL veritabanınıza bağlanın.

1. Şu komutu çalıştırın:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    **İletiler** sekmesinde aşağıdaki sonucu görürsünüz.

    **Sonuçlar**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Paketi kaldır

Paketi kaldırmak istiyorsanız, RStudio 'da aşağıdaki R betiğini çalıştırın. Daha önce tanımladığınız **bağlantıyı** kullanın.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> R paketini Azure SQL veritabanınıza yüklemenin başka bir yolu da, **dış kitaplık** T-SQL Ifadesini kullanarak r paketini bir bayt akışından karşıya yüklemektir. [Dış kitaplık başvurusu oluşturma](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) belgelerindeki bir [bayt akışından kitaplık oluşturma](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

R (Önizleme) ile Azure SQL veritabanı Machine Learning Services hakkında daha fazla bilgi için aşağıdaki makalelere bakın.

- [R ile Azure SQL veritabanı Machine Learning Services (Önizleme)](sql-database-machine-learning-services-overview.md)
- [Machine Learning Services kullanarak Azure SQL veritabanı 'nda gelişmiş R işlevleri yazma (Önizleme)](sql-database-machine-learning-services-functions.md)
- [Azure SQL veritabanı 'nda R ve SQL verileriyle çalışma Machine Learning Services (Önizleme)](sql-database-machine-learning-services-data-issues.md)