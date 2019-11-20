---
title: "Öğretici: R 'de tahmine dayalı bir model eğitme için veri hazırlama"
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Bu üç bölümden oluşan öğretici serisinin birinci bölümünde, Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'deki tahmine dayalı bir modeli eğitebilmeniz için verileri bir Azure SQL veritabanından hazırlarsınız.
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
ms.date: 07/26/2019
ms.openlocfilehash: c1271d5b63fa796fe44b7a40c364953464a87539
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596673"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Öğretici: Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de tahmine dayalı bir model eğitme için veri hazırlama

Bu üç bölümden oluşan öğretici serisinin birinci kısmında, R kullanarak bir Azure SQL veritabanından veri alıp hazırlarsınız. Bu serinin ilerleyen kısımlarında, Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de tahmine dayalı bir makine öğrenimi modeli eğitmek ve dağıtmak için bu verileri kullanacaksınız.

Bu öğretici serisi için, bir kayak kiralama işiniz olduğunu düşünün ve gelecekteki bir tarihte sahip olduğunuz Kiralama sayısını tahmin etmek istiyorsunuz. Bu bilgiler, stoklarınızı, personelinizi ve tesislerinizi hazır hale getirmenize yardımcı olur.

Bu serinin bir ve ikinci kısımlarında, verilerinizi hazırlamak ve makine öğrenimi modelini eğtirecek RStudio 'da bazı R betikleri geliştirirsiniz. Ardından, üçüncü kısmında, saklı yordamları kullanarak bu R betiklerini bir SQL veritabanı içinde çalıştıracaksınız.

Bu makalede aşağıdakileri nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * R kullanarak örnek bir veritabanını Azure SQL veritabanı 'na aktarma
> * Verileri Azure SQL veritabanından R veri çerçevesine yükleme
> * Bazı sütunları kategorik olarak tanımlayarak R 'de verileri hazırlama

[İkinci bölümde](sql-database-tutorial-predictive-model-build-compare.md), R 'de birden çok makine öğrenimi modeli oluşturup eğitme ve en doğru olanı seçme hakkında bilgi edineceksiniz.

[Üçüncü kısımda](sql-database-tutorial-predictive-model-deploy.md), modeli bir veritabanında nasıl depolayacağınızı öğrenirsiniz ve sonra bir ve iki bölümde geliştirdiğiniz R betiklerinden saklı yordamlar oluşturabilirsiniz. Saklı yordamlar yeni verilere göre tahmine dayalı hale getirmek için bir SQL veritabanında çalışır.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği-Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/) .

* Machine Learning Services etkinleştirilmiş Azure SQL veritabanı sunucusu-genel önizleme sırasında, Microsoft, mevcut veya yeni veritabanlarınız için sizi kullanıma sunulacaktır ve makine öğrenimini etkinleştirecektir. [Önizlemeye kaydolma](sql-database-machine-learning-services-overview.md#signup)adımlarını izleyin.

* Iptal paketi-bu paketi yerel olarak yüklemek için seçenekler için [iptal](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) 'e bakın.

* R IDE-Bu öğretici [rstudio Desktop](https://www.rstudio.com/products/rstudio/download/)kullanır.

* SQL sorgu aracı-Bu öğreticide [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) veya [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) kullandığınız varsayılır.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure Portal](https://portal.azure.com/) oturum açın.

## <a name="import-the-sample-database"></a>Örnek veritabanını içeri aktarma

Bu öğreticide kullanılan örnek veri kümesi, indirmeniz ve kullanmanız için bir **. bacpac** veritabanı yedekleme dosyasına kaydedildi.

1. [Tutorialdb 'yi. bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac)dosyasını indirin.

1. Aşağıdaki ayrıntıları kullanarak bir [Azure SQL veritabanı oluşturmak IÇIN BACPAC dosyasını Içeri aktarma](https://docs.microsoft.com/azure/sql-database/sql-database-import)bölümündeki yönergeleri izleyin:

   * İndirdiğiniz **tutorialdb 'yi. bacpac** dosyasından içeri aktarma
   * Genel Önizleme sırasında yeni veritabanı için **5. nesil/sanal çekirdek** yapılandırmasını seçin
   * "Tutorialdb 'yi" adlı yeni veritabanını adlandırın

## <a name="load-the-data-into-a-data-frame"></a>Verileri bir veri çerçevesine yükleme

Verileri R 'de kullanmak için, verileri Azure SQL veritabanından bir veri çerçevesine (`rentaldata`) yüklersiniz.

RStudio 'da yeni bir RScript dosyası oluşturun ve aşağıdaki betiği çalıştırın. **Sunucu**, **UID**ve **PWD** bilgilerini kendi bağlantı bilgileriniz ile değiştirin.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

Aşağıdakine benzer sonuçlar görmeniz gerekir.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>Verileri hazırlama

Bu örnek veritabanında, hazırlığının büyük bir kısmında zaten yapılmış olsa da daha fazla hazırlık yapacaksınız.
Veri türlerini *faktör*olarak değiştirerek üç sütunu *Kategori* olarak tanımlamak için aşağıdaki R betiğini kullanın.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Aşağıdakine benzer sonuçlar görmeniz gerekir.

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

Veriler artık eğitim için hazırlandı.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticiye devam edemeyecekinizden Tutorialdb 'yi veritabanını Azure SQL veritabanı sunucusundan silin.

Azure portal, aşağıdaki adımları izleyin:

1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı veya **SQL veritabanları**' nı seçin.
1. **Ada göre filtrele...** alanına **tutorialdb 'yi**girin ve aboneliğinizi seçin.
1. Tutorialdb 'yi veritabanınızı seçin.
1. **Genel Bakış** sayfasında **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici serisinin birinci kısmında, şu adımları tamamladınız:

* R kullanarak örnek bir veritabanını Azure SQL veritabanı 'na aktarma
* Verileri Azure SQL veritabanından R veri çerçevesine yükleme
* Bazı sütunları kategorik olarak tanımlayarak R 'de verileri hazırlama

Tutorialdb 'yi veritabanından veri kullanan bir Machine Learning modeli oluşturmak için, bu öğretici serisinin ikinci bölümünü izleyin:

> [!div class="nextstepaction"]
> [Öğretici: R 'de Azure SQL veritabanı Machine Learning Services tahmine dayalı model oluşturma (Önizleme)](sql-database-tutorial-predictive-model-build-compare.md)
