---
title: "Öğretici: R 'de kümeleme gerçekleştirmeye yönelik verileri hazırlama"
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Bu üç bölümden oluşan öğretici serisinin birinci bölümünde, Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de kümeleme gerçekleştirmek üzere bir Azure SQL veritabanından veri hazırlarsınız.
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
ms.date: 07/29/2019
ms.openlocfilehash: 800dbfc05c47a949bf024e9a5c671979b49ad201
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639980"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Öğretici: Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de kümeleme gerçekleştirmeye yönelik verileri hazırlama

Bu üç bölümden oluşan öğretici serisinin birinci kısmında, verileri R kullanarak bir Azure SQL veritabanından içeri ve hazırlarsınız. Bu serinin ilerleyen kısımlarında, Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de bir kümeleme modeli eğitmek ve dağıtmak için bu verileri kullanacaksınız.

*Kümeleme* , bir grubun üyelerinin bir şekilde benzer olduğu gruplara veri düzenleme olarak açıklanabilir.
Ürün satın alımlarının bir veri kümesinde müşteri kümelemesini gerçekleştirmek için **K-ortalıyorum** algoritmasını kullanacaksınız ve iade edersiniz. Kullanıcıları kümeleyerek, belirli grupları hedefleyerek pazarlama çabalarınızı daha verimli bir şekilde odaklabilirsiniz.
K-kümeleme, benzerlere göre verilerde desenleri gösteren bir *öğrenme* algoritması olduğunu belirtir.

Bu serinin bir ve ikinci kısımlarında, verilerinizi hazırlamak ve makine öğrenimi modelini eğtirecek RStudio 'da bazı R betikleri geliştirirsiniz. Ardından, üçüncü kısmında, saklı yordamları kullanarak bu R betiklerini bir SQL veritabanı içinde çalıştıracaksınız.

Bu makalede aşağıdakileri nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * Örnek bir veritabanını Azure SQL veritabanına aktarma
> * R kullanarak müşterileri farklı boyutlarda ayır
> * Verileri Azure SQL veritabanından R veri çerçevesine yükleme

[İkinci bölümde](sql-database-tutorial-clustering-model-build.md), R 'de K-bir kümeleme modeli oluşturma ve eğitme hakkında bilgi edineceksiniz.

[Üçünde](sql-database-tutorial-clustering-model-deploy.md), BIR Azure SQL veritabanında yeni verileri temel alan R 'de kümeleme gerçekleştirebilen bir saklı yordam oluşturmayı öğreneceksiniz.

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

Bu öğreticide kullanılan örnek veri kümesi, indirmeniz ve kullanmanız için bir **. bacpac** veritabanı yedekleme dosyasına kaydedildi. Bu veri kümesi, [Işlem performans Council (TPC)](http://www.tpc.org/default.asp)tarafından sunulan [tpcx-BB](http://www.tpc.org/tpcx-bb/default.asp) veri kümesinden türetilir.

1. [Tpcxbb_1gb. bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac)dosyasını indirin.

1. Aşağıdaki ayrıntıları kullanarak bir [Azure SQL veritabanı oluşturmak IÇIN BACPAC dosyasını Içeri aktarma](https://docs.microsoft.com/azure/sql-database/sql-database-import)bölümündeki yönergeleri izleyin:

   * İndirdiğiniz **tpcxbb_1gb. bacpac** dosyasından içeri aktarma
   * Genel Önizleme sırasında yeni veritabanı için **5. nesil/sanal çekirdek** yapılandırmasını seçin
   * "Tpcxbb_1gb" adlı yeni veritabanını adlandırın

## <a name="separate-customers"></a>Ayrı müşteriler

RStudio 'da yeni bir RScript dosyası oluşturun ve aşağıdaki betiği çalıştırın.
SQL sorgusunda, müşterileri şu boyutlara göre ayırıyoruz:

* **Orderratio** = dönüş siparişi oranı (Toplam sipariş sayısına göre kısmen veya tam olarak döndürülen toplam sipariş sayısı)
* **ıtemratio** = dönüş öğesi oranı (döndürülen toplam öğe sayısı ve alınan öğe sayısı)
* **Monetaryratio** = dönüş tutarı oranı (döndürülen miktara göre döndürülen toplam parasal miktar)
* **Sıklık** = dönüş sıklığı

**Yapıştır** işlevinde **sunucu**, **UID**ve **PWD** değerlerini kendi bağlantı bilgileriniz ile değiştirin.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
            WHEN (returns_count IS NULL)
            THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
"
```

## <a name="load-the-data-into-a-data-frame"></a>Verileri bir veri çerçevesine yükleme

Şimdi, **Rxsqlserverdata** işlevini kullanarak sorgudan sonuçları bir R veri çerçevesine döndürmek için aşağıdaki betiği kullanın.
İşlemin bir parçası olarak, türlerin R 'ye doğru şekilde aktarıldığından emin olmak için Seçili sütunların türünü (colClasses kullanarak) tanımlayacaksınız.

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

Aşağıdakine benzer sonuçlar görmeniz gerekir.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

***Bu öğreticiye devam***edemeyecekinizden Tpcxbb_1gb VERITABANıNı Azure SQL veritabanı sunucusundan silin.

Azure portal, aşağıdaki adımları izleyin:

1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı veya **SQL veritabanları**' nı seçin.
1. **Ada göre filtrele...** alanına **tpcxbb_1gb**girin ve aboneliğinizi seçin.
1. **Tpcxbb_1gb** veritabanınızı seçin.
1. **Genel Bakış** sayfasında **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici serisinin birinci kısmında, şu adımları tamamladınız:

* Örnek bir veritabanını Azure SQL veritabanına aktarma
* R kullanarak müşterileri farklı boyutlarda ayır
* Verileri Azure SQL veritabanından R veri çerçevesine yükleme

Bu müşteri verilerini kullanan bir makine öğrenimi modeli oluşturmak için, bu öğretici serisinin ikinci bölümünü izleyin:

> [!div class="nextstepaction"]
> [Öğretici: R 'de Azure SQL veritabanı Machine Learning Services tahmine dayalı model oluşturma (Önizleme)](sql-database-tutorial-clustering-model-build.md)
