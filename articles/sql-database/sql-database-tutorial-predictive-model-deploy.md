---
title: "Öğretici: R 'de tahmine dayalı model dağıtma"
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Üç bölümden oluşan Bu öğreticinin üçüncü bölümünde Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de tahmine dayalı bir model dağıtacaksınız.
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
ms.openlocfilehash: 9fa816b2a8e736f03c99b66b898f48bd2a483b31
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596779"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Öğretici: Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de tahmine dayalı bir model dağıtma

Üç bölümden oluşan Bu öğreticinin üçüncü kısmında, Azure SQL veritabanı Machine Learning Services (Önizleme) kullanarak R 'de geliştirilen bir tahmine dayalı modeli SQL veritabanına dağıtırsınız.

Modeli kullanarak tahminleri yapan gömülü bir R betiği ile saklı bir yordam oluşturacaksınız. Modeliniz Azure SQL veritabanında yürütüldüğü için, veritabanında depolanan verilere karşı kolayca eğitim yapılabilir.

Bu makalede, bir ve iki bölümde geliştirmiş olduğunuz R betiklerini kullanarak şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> * Machine Learning modelini üreten bir saklı yordam oluşturma
> * Modeli bir veritabanı tablosunda depolayın
> * Modeli kullanarak tahminleri yapan bir saklı yordam oluşturma
> * Modeli yeni verilerle yürütme

[Birinci bölümde](sql-database-tutorial-predictive-model-prepare-data.md), örnek bir veritabanını içeri aktarmayı ve ardından R 'deki tahmine dayalı bir modeli eğitmek için kullanılacak verileri oluşturmayı öğrendiniz.

[İkinci bölümde](sql-database-tutorial-predictive-model-build-compare.md), R 'de birden çok makine öğrenimi modeli oluşturup eğitme ve en doğru olanı seçme hakkında bilgi edindiniz.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Bu öğretici serisinin üçüncü kısmı, [**bir**](sql-database-tutorial-predictive-model-prepare-data.md) kısmını ve [**ikinci kısmını**](sql-database-tutorial-predictive-model-build-compare.md)tamamladığınız varsayılır.

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Modeli oluşturan bir saklı yordam oluşturma

Bu öğretici serisinin ikinci bölümünde, karar ağacı (dTree) modelinin en doğru olduğunu kararırdı. Şimdi, geliştirmiş olduğunuz R betiklerini kullanarak, iptal edilen bir saklı yordam`generate_rental_rx_model`() oluşturun ve rxdtree paketinden rxdtree kullanarak dTree modelini oluşturur.

Azure Data Studio veya SSMS 'de aşağıdaki komutları çalıştırın.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Modeli bir veritabanı tablosunda depolayın

Tutorialdb 'yi veritabanında bir tablo oluşturun ve ardından modeli tabloya kaydedin.

1. Modeli depolamak için bir`rental_rx_models`tablo () oluşturun.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Modeli, model adı "rxDTree" olan bir ikili nesne olarak tabloya kaydedin.

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Tahminleri yapan bir saklı yordam oluşturma

Eğitilen modeli ve yeni`predict_rentalcount_new`veri kümesini kullanarak tahminleri yapan bir saklı yordam () oluşturun.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Modeli yeni verilerle yürütme

Artık yeni verilerden Kiralama sayısını tahmin etmek `predict_rentalcount_new` için saklı yordamı kullanabilirsiniz.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Aşağıdakine benzer bir sonuç görmeniz gerekir.

```results
RentalCount_Predicted
332.571428571429
```

Bir Azure SQL veritabanında başarıyla bir model oluşturdunuz, eğitilen ve dağıttınız. Daha sonra bu modeli, değerleri yeni verilere göre tahmin etmek için bir saklı yordamda kullandınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Tutorialdb 'yi veritabanını kullanmayı bitirdiğinizde Azure SQL veritabanı sunucusundan silin.

Azure portal, aşağıdaki adımları izleyin:

1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı veya **SQL veritabanları**' nı seçin.
1. **Ada göre filtrele...** alanına **tutorialdb 'yi**girin ve aboneliğinizi seçin.
1. Tutorialdb 'yi veritabanınızı seçin.
1. **Genel Bakış** sayfasında **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici serisinin üçüncü kısmında, şu adımları tamamladınız:

* Machine Learning modelini üreten bir saklı yordam oluşturma
* Modeli bir veritabanı tablosunda depolayın
* Modeli kullanarak tahminleri yapan bir saklı yordam oluşturma
* Modeli yeni verilerle yürütme

Azure SQL veritabanı Machine Learning Services (Önizleme) içinde R kullanma hakkında daha fazla bilgi için bkz.:

* [Machine Learning Services kullanarak Azure SQL veritabanı 'nda gelişmiş R işlevleri yazma (Önizleme)](sql-database-machine-learning-services-functions.md)
* [Azure SQL veritabanı 'nda R ve SQL verileriyle çalışma Machine Learning Services (Önizleme)](sql-database-machine-learning-services-data-issues.md)
* [Azure SQL veritabanı 'na R paketi ekleme Machine Learning Services (Önizleme)](sql-database-machine-learning-services-add-r-packages.md)
