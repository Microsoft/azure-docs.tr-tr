---
title: "Öğretici: R 'de kümeleme modeli dağıtma"
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Bu üç bölümden oluşan öğretici serisinin üçünde, Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'ye bir kümeleme modeli dağıtırsınız.
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
ms.openlocfilehash: 6f4d237d5e923aab61ae34a235d2e1f759399e6d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640915"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Öğretici: Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de kümeleme modeli dağıtma

Bu üç bölümden oluşan öğretici serisinin üçüncü kısmında, Azure SQL veritabanı Machine Learning Services (Önizleme) kullanarak R 'de geliştirilen bir kümeleme modelini bir SQL veritabanına dağıtırsınız.

Kümeleme gerçekleştiren gömülü bir R betiği ile saklı bir yordam oluşturacaksınız. Modeliniz Azure SQL veritabanında yürütüldüğü için, veritabanında depolanan verilere karşı kolayca eğitim yapılabilir.

Bu makalede aşağıdakileri nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * Modeli oluşturan bir saklı yordam oluşturma
> * SQL veritabanında kümeleme gerçekleştir
> * Kümeleme bilgilerini kullanma

[Birinci bölümde](sql-database-tutorial-clustering-model-prepare-data.md), verileri BIR Azure SQL veritabanından kümeleme gerçekleştirmek üzere nasıl hazırlayacağınızı öğrendiniz.

[İkinci bölümde](sql-database-tutorial-clustering-model-build.md), R 'de K-bir kümeleme modeli oluşturmayı ve eğiteyi öğrendiniz.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Bu öğretici serisinin üçüncü kısmı, [**bir**](sql-database-tutorial-clustering-model-prepare-data.md) kısmını ve [**ikinci kısmını**](sql-database-tutorial-clustering-model-build.md)tamamladığınız varsayılır.

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Modeli oluşturan bir saklı yordam oluşturma

Saklı yordamı oluşturmak için aşağıdaki T-SQL betiğini çalıştırın. Yordam, bu öğretici serisinin bir ve iki bölümünde geliştirdiğiniz adımları yeniden oluşturur:

* müşterileri satın alma ve iade geçmişine göre sınıflandır
* K-anlamı algoritması kullanarak dört müşteri kümesi oluşturma

Yordam, sonuç müşteri kümesi eşlemelerini **customer_return_clusters**veritabanı tablosunda depolar.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
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
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>SQL veritabanında kümeleme gerçekleştir

Saklı yordamı oluşturduğdığınıza göre, kümelendirmeyi gerçekleştirmek için aşağıdaki betiği yürütün.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Çalıştığını ve aslında müşteriler ve küme eşlemeleriyle ilgili olduğunu doğrulayın.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Kümeleme bilgilerini kullanma

Kümeleme yordamını veritabanında depoladığınız için, aynı veritabanında depolanan müşteri verilerine göre kümeleme işlemi etkili bir şekilde gerçekleştirilebilir. Müşteri verileriniz güncelleştirildiğinde yordamı yürütebilir ve güncelleştirilmiş kümeleme bilgilerini kullanabilirsiniz.

Küme 3 ' teki müşterilere bir promosyon e-postası göndermek istediğinizi varsayalım. daha etkin bir geri dönüş davranışı olan grup (dört kümenin [ikinci bölümünde](sql-database-tutorial-clustering-model-build.md#analyze-the-results)açıklandığını görebilirsiniz). Aşağıdaki kod, küme 3 ' teki müşterilerin e-posta adreslerini seçer.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

**R. Cluster** değerini, diğer kümelerdeki müşterilere ait e-posta adreslerini döndürecek şekilde değiştirebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticiyi tamamladığınızda, tpcxbb_1gb veritabanını Azure SQL veritabanı sunucusundan silebilirsiniz.

Azure portal, aşağıdaki adımları izleyin:

1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı veya **SQL veritabanları**' nı seçin.
1. **Ada göre filtrele...** alanına **tpcxbb_1gb**girin ve aboneliğinizi seçin.
1. **Tpcxbb_1gb** veritabanınızı seçin.
1. **Genel Bakış** sayfasında **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici serisinin üçüncü kısmında, şu adımları tamamladınız:

* Modeli oluşturan bir saklı yordam oluşturma
* SQL veritabanında kümeleme gerçekleştir
* Kümeleme bilgilerini kullanma

Azure SQL veritabanı Machine Learning Services (Önizleme) içinde R kullanma hakkında daha fazla bilgi için bkz.:

* [Öğretici: Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de tahmine dayalı bir model eğitme için veri hazırlama](sql-database-tutorial-predictive-model-prepare-data.md)
* [Machine Learning Services kullanarak Azure SQL veritabanı 'nda gelişmiş R işlevleri yazma (Önizleme)](sql-database-machine-learning-services-functions.md)
* [Azure SQL veritabanı 'nda R ve SQL verileriyle çalışma Machine Learning Services (Önizleme)](sql-database-machine-learning-services-data-issues.md)
* [Azure SQL veritabanı 'na R paketi ekleme Machine Learning Services (Önizleme)](sql-database-machine-learning-services-add-r-packages.md)
