---
title: 'Öğretici: R’de kümeleme modeli oluşturma'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Bu üç bölümden oluşan öğretici serisinin ikinci bölümünde, Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de kümeleme gerçekleştirmek üzere bir K-bit modeli oluşturacaksınız.
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
ms.openlocfilehash: 9f16ebc5acff7bbccc9de28e2fab0d223c6e244b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640003"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Öğretici: Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de kümeleme modeli oluşturma

Bu üç bölümden oluşan öğretici serisinin ikinci bölümünde, kümeleme gerçekleştirmek için R ' de bir K-anlamı modeli oluşturacaksınız. Bu serinin bir sonraki bölümünde, bu modeli Azure SQL veritabanı Machine Learning Services (Önizleme) ile bir SQL veritabanında dağıtacaksınız.

Bu makalede aşağıdakileri nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * K-Ortalamalar algoritması için küme sayısını tanımlayın
> * Kümeleme gerçekleştir
> * Sonuçları çözümleyin

[Birinci bölümde](sql-database-tutorial-clustering-model-prepare-data.md), verileri BIR Azure SQL veritabanından kümeleme gerçekleştirmek üzere nasıl hazırlayacağınızı öğrendiniz.

[Üçünde](sql-database-tutorial-clustering-model-deploy.md), BIR Azure SQL veritabanında yeni verileri temel alan R 'de kümeleme gerçekleştirebilen bir saklı yordam oluşturmayı öğreneceksiniz.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Bu öğreticinin ikinci bölümünde [**Birinci bölüm bir**](sql-database-tutorial-clustering-model-prepare-data.md) ve önkoşulları tamamladığınız varsayılır.

## <a name="define-the-number-of-clusters"></a>Küme sayısını tanımlayın

Müşteri verilerinizi Kümelendirmek için, verilerin gruplandırılması için en basit ve en iyi bilinen yollardan biri olan **K-anlamı** kümeleme algoritmasını kullanırsınız.
K-ortalamalar hakkında daha fazla bilgi edinmek [için, k-bit küme algoritması için tam bir kılavuzda](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Algoritma iki girişi kabul eder: Verilerin kendisi ve oluşturulacak kümelerin sayısını temsil eden önceden tanımlanmış bir "*k*" numarası.
Çıktı, kümeler arasında bölümlenmiş giriş verilerinin bulunduğu *k* kümeleridir.

Kullanılacak algoritmanın küme sayısını öğrenmek için, ayıklanan küme sayısına göre kareler toplamı içindeki bir çizim kullanın. Kullanılacak uygun küme sayısı, çizimin dirseği veya "dirseği" olur.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Dirsek grafiği](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Grafiğe bağlı olarak, *k = 4* gibi bir değer, denemek için iyi bir değer olacaktır. Bu *k* değeri, müşterileri dört kümeye gruplacaktır.

## <a name="perform-clustering"></a>Kümeleme gerçekleştir

Aşağıdaki R betiğindeki **Rxkanlamý**Işlevini, iptal edilmiş paketteki K-Ortalamalar işlevini kullanacaksınız.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Sonuçları çözümleyin

Artık K-bit kullanarak kümelendirmeyi tamamladığınıza göre, bir sonraki adım sonucu analiz etmek ve eyleme dönüştürülebilir herhangi bir bilgiyi bulup bulamadığınızı görmektedir.

**Clust** nesnesi, K-anlamı Kümelemesi sonuçlarını içerir.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

Dört küme, [Birinci bölümde](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers)tanımlanan değişkenler kullanılarak verilmiştir:

* *Orderratio* = dönüş siparişi oranı (Toplam sipariş sayısına göre kısmen veya tam olarak döndürülen toplam sipariş sayısı)
* *ıtemratio* = dönüş öğesi oranı (döndürülen toplam öğe sayısı ve alınan öğe sayısı)
* *Monetaryratio* = dönüş tutarı oranı (döndürülen miktara göre döndürülen toplam parasal miktar)
* *Sıklık* = dönüş sıklığı

K-u kullanan veri madenciliği genellikle sonuçların daha fazla analizini ve her kümeyi daha iyi anlamak için daha fazla adım gerektirir, ancak bu durum iyi bir müşteri adayı sağlayabilir.
Bu sonuçları yorumlayabilmeniz için birkaç yol aşağıda verilmiştir:

* Küme 1 (en büyük küme) etkin olmayan bir müşteri grubu gibi görünüyor (tüm değerler sıfırdır).
* Küme 3, dönüş davranışının koşullarını temsil eden bir grup gibi görünüyor.

## <a name="clean-up-resources"></a>Kaynakları temizleme

***Bu öğreticiye devam***edemeyecekinizden Tpcxbb_1gb VERITABANıNı Azure SQL veritabanı sunucusundan silin.

Azure portal, aşağıdaki adımları izleyin:

1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı veya **SQL veritabanları**' nı seçin.
1. **Ada göre filtrele...** alanına **tpcxbb_1gb**girin ve aboneliğinizi seçin.
1. **Tpcxbb_1gb** veritabanınızı seçin.
1. **Genel Bakış** sayfasında **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici serisinin ikinci bölümünde, şu adımları tamamladınız:

* K-Ortalamalar algoritması için küme sayısını tanımlayın
* Kümeleme gerçekleştir
* Sonuçları çözümleyin

Oluşturduğunuz Machine Learning modelini dağıtmak için, bu öğretici serisinin üçüncü kısmını izleyin:

> [!div class="nextstepaction"]
> [Öğretici: Azure SQL veritabanı Machine Learning Services (Önizleme) ile R 'de kümeleme modeli dağıtma](sql-database-tutorial-clustering-model-deploy.md)