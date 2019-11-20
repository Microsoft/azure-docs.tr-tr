---
title: "Öğretici: R 'de tahmine dayalı modelleri eğitme ve karşılaştırma"
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Bu üç bölümden oluşan öğretici serisinin ikinci bölümünde, R 'de Azure SQL veritabanı Machine Learning Services (Önizleme) ile birlikte iki tahmine dayalı model oluşturacak ve en doğru modeli seçeceksiniz.
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
ms.openlocfilehash: 2c85a378dc219e8af1b6458344ee4dba0fa73e68
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596803"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Öğretici: R 'de Azure SQL veritabanı Machine Learning Services tahmine dayalı model oluşturma (Önizleme)

Bu üç bölümden oluşan öğretici serisinin ikinci bölümünde, R 'de iki tahmine dayalı model oluşturacak ve en doğru modeli seçmelisiniz. Bu serinin bir sonraki bölümünde, bu modeli Azure SQL veritabanı Machine Learning Services (Önizleme) ile bir SQL veritabanında dağıtacaksınız.

Bu makalede aşağıdakileri nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * İki makine öğrenimi modeli eğitimi
> * Her iki modelden de tahminler yapın
> * En doğru modeli seçmek için sonuçları karşılaştırın

[Birinci bölümde](sql-database-tutorial-predictive-model-prepare-data.md), örnek bir veritabanını içeri aktarmayı ve ardından R 'deki tahmine dayalı bir modeli eğitmek için kullanılacak verileri oluşturmayı öğrendiniz.

[Üçüncü kısımda](sql-database-tutorial-predictive-model-deploy.md), modeli bir veritabanında nasıl depolayacağınızı öğrenirsiniz ve sonra bir ve iki bölümde geliştirdiğiniz R betiklerinden saklı yordamlar oluşturabilirsiniz. Saklı yordamlar yeni verilere göre tahmine dayalı hale getirmek için bir SQL veritabanında çalışır.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Bu öğreticinin ikinci bölümünde [**Birinci bölüm bir**](sql-database-tutorial-predictive-model-prepare-data.md) ve önkoşulları tamamladığınız varsayılır.

## <a name="train-two-models"></a>İki modeli eğitme

Kayak kiralama verileri için en iyi modeli bulmak için, iki farklı model (doğrusal regresyon ve karar ağacı) oluşturun ve hangisinin daha doğru bir şekilde tahmin edilebileceğine bakın. Bu serinin bir bölümünde oluşturduğunuz veri `rentaldata` çerçevesini kullanacaksınız.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>Her iki modelden de tahminler yapın

Her eğitilen modeli kullanarak Kiralama sayılarını tahmin etmek için bir tahmin işlevi kullanın.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Sonuçları karşılaştırın

Artık modellerden hangisinin en iyi tahminlere sahip olduğunu görmek istiyorsunuz. Bunu yapmanın hızlı ve kolay bir yolu, eğitim verilerinizde gerçek değerler ve tahmin edilen değerler arasındaki farkı görüntülemek için temel bir çizim işlevi kullanmaktır.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![İki modeli karşılaştırma](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Karar ağacı modeli, iki modelden daha doğru bir şekilde görünür.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticiye devam edemeyecekinizden Tutorialdb 'yi veritabanını Azure SQL veritabanı sunucusundan silin.

Azure portal, aşağıdaki adımları izleyin:

1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı veya **SQL veritabanları**' nı seçin.
1. **Ada göre filtrele...** alanına **tutorialdb 'yi**girin ve aboneliğinizi seçin.
1. Tutorialdb 'yi veritabanınızı seçin.
1. **Genel Bakış** sayfasında **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici serisinin ikinci bölümünde, şu adımları tamamladınız:

* İki makine öğrenimi modeli eğitimi
* Her iki modelden de tahminler yapın
* En doğru modeli seçmek için sonuçları karşılaştırın

Oluşturduğunuz Machine Learning modelini dağıtmak için, bu öğretici serisinin üçüncü kısmını izleyin:

> [!div class="nextstepaction"]
> [Öğretici: Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de tahmine dayalı bir model dağıtma](sql-database-tutorial-predictive-model-deploy.md)
