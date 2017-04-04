---
title: "Azure Machine Learning ile veri çözümleme | Microsoft Belgeleri"
description: "Azure SQL Data Warehouse’a depolanmış verilere göre tahmine dayalı bir machine learning modeli oluşturmak için Azure Machine Learning’i kullanın."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 95635460-150f-4a50-be9c-5ddc5797f8a9
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 03/02/2017
ms.author: kevin
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a5befbf1dc1862b3b0803eb4940341d246ec036c
ms.lasthandoff: 03/14/2017


---
# <a name="analyze-data-with-azure-machine-learning"></a>Azure Machine Learning ile veri çözümleme
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Bu öğretici Azure SQL Data Warehouse’a depolanmış verilere göre tahmine dayalı bir machine learning modeli oluşturmak için Azure Machine Learning’i kullanır. Özellikle, bir müşterinin bisiklet alma olasılığı hakkında tahminde bulunarak Adventure Works adlı bisiklet satış mağazası için hedeflenen bir pazarlama kampanyası oluşturulur.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Ön koşullar
Bu öğreticide ilerleyebilmeniz için şunlar gereklidir:

* AdventureWorksDW örnek verileri önceden yüklenmiş bir SQL Data Warehouse. Bunu sağlamak için [SQL Veri Ambarı Oluşturma][Create a SQL Data Warehouse] bölümüne bakın ve örnek verileri yüklemeyi seçin. Bir veri ambarınız olmasına karşın örnek verileriniz yoksa [örnek verileri elle yükleyebilirsiniz][load sample data manually].

## <a name="1-get-the-data"></a>1. Verileri alma
Veriler AdventureWorksDW veritabanında bulunan dbo.vTargetMail görünümündedir. Bu verileri okumak için:

1. [Azure Machine Learning Studio][Azure Machine Learning studio]'da oturum açıp denemelerim seçeneğine tıklayın.
2. **+NEW (+YENİ)** düğmesine tıklayıp **Blank Experiment (Boş Deneme)** öğesini seçin.
3. Denemeniz için bir ad girin: Hedeflenen Pazarlama.
4. Modüller bölmesindeki **Reader (Okuyucu)** modülünü tuvale sürükleyin.
5. Özellikler bölmesinde SQL Data Warehouse veritabanınıza ilişkin bilgileri belirtin.
6. İstediğiniz verileri okumak için veritabanı **sorgusunu** belirtin.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Deneme tuvalinin altında bulunan **Run (Çalıştır)** düğmesine tıklayarak denemeyi çalıştırın.
![Denemeyi çalıştırma][1]

Denemeyi çalıştırma işlemi başarıyla sonlandıktan sonra, Okuyucu modülünün altındaki çıkış bağlantı noktasına tıklayıp içeri aktarılan verileri görmek için **Visualiza (Görselleştir)** seçeneğine tıklayın.
![İçeri aktarılan verileri görüntüleme][3]

## <a name="2-clean-the-data"></a>2. Verileri temizleyin
Verileri temizlemek için modelle ilgili olmayan bazı sütunları kaldırın. Bunu yapmak için:

1. **Project Columns (Proje Sütunları)** modülünü tuvale sürükleyin.
2. Hangi sütunları kaldırmak istediğinizi belirtmek için Properties (Özellikler) bölmesindeki **Launch column selector (Sütun seçiciyi başlat)** öğesine tıklayın.
   ![Proje Sütunları][4]
3. Şu iki sütunu dışlayın: CustomerAlternateKey ve GeographyKey.
   ![Gereksiz sütunları kaldırma][5]

## <a name="3-build-the-model"></a>3. Modeli oluşturma
Biz verilerin %80'ini Machine Learning modelini eğitmek ve %20'sini de modeli test etmek üzere kullanacak şekilde 80'e 20 oranında böleceğiz. Bu ikili sınıflandırma sorunu için "İki Sınıflı" algoritmalardan yararlanacağız.

1. **Split (Bölme)** modülünü tuvale sürükleyin.
2. Properties (Özellikler) bölmesindeki ilk çıkış veri kümesinde bulunan satırlar için kesir değerini 0,8 olarak girin.
   ![Verileri eğitim ve test kümesi olarak bölme][6]
3. **Two-Class Boosted Decision Tree (İki Sınıflı Gelişmiş Karar Ağacı)** modülünü tuvale sürükleyin.
4. **Train Model (Model Eğitme)** modülünü tuvale sürükleyip girişleri belirtin. Ardından Properties (Özellikler) bölmesindeki **Launch column selector (Sütun seçiciyi başlat)** öğesine tıklayın.
   * İlk giriş: ML algoritması
   * İkinci giriş: Algoritmayı eğitmeye yönelik veriler.
     ![Model Eğitme modülünü bağlama][7]
5. Tahminde bulunulacak sütun olarak **BikeBuyer** sütununu seçin.
   ![Tahminde bulunulacak sütunu seçme][8]

## <a name="4-score-the-model"></a>4. Modeli puanlama
Şimdi modelin test verileri üzerindeki işlevini test edeceğiz. Hangisinin daha iyi sonuç verdiğini görmek üzere kendi seçtiğimiz algoritmayla başka bir algoritmayı karşılaştıracağız.

1. **Score Model (Model Puanlama)** modülünü tuvale sürükleyin.
    İlk giriş: Eğitilmiş model İkinci giriş: Test verileri ![Modeli puanlama][9]
2. **Two-Class Bayes Point Machine (İki Sınıflı Bayes Noktası Makinesi)** modülünü deneme tuvaline sürükleyin. Bu algoritma ile Two-Class Boosted Decision Tree'nin (İki Sınıflı Gelişmiş Karar Ağacı'nın) işlevlerini karşılaştıracağız.
3. Train Model (Model Eğitme) ve Score Model (Model Puanlama) modüllerini kopyalayıp tuvale yapıştırın.
4. İki algoritmayı karşılaştırmak için **Evaluate Model (Model Değerlendirme)** modülünü tuvale sürükleyin.
5. Denemeyi çalıştırmak için **Run (Çalıştır)** düğmesine basın.
   ![Denemeyi çalıştırma][10]
6. Evaluate Model (Model Değerlendirme) modülünün altında bulunan çıkış bağlantı noktasına ve ardından Visualize (Görselleştir) düğmesine tıklayın.
   ![Değerlendirme sonuçlarını görselleştirme][11]

Sağlanan ölçümler şunlardır: ROC eğrisi, duyarlık geri çekme diyagramı ve yükseltme eğrisi. Bu ölçümlere bakarak birinci modelin ikinciye göre daha iyi sonuç verdiğini görebiliriz. Birinci modelin nasıl bir tahminde bulunduğunu görmek için Score Model (Model Puanlama) modülünün çıkış bağlantı noktasına ve ardından Visualize (Görselleştir) düğmesine tıklayın.
![Puanlama sonuçlarını görselleştirme][12]

Test veri kümenize iki sütunun daha eklendiğini göreceksiniz.

* Puanlanmış Olasılıklar: müşterinin bir bisiklet alıcısı olma olasılığı.
* Puanlanmış Etiketler: model tarafından yapılan sınıflandırma; bisiklet alıcısı (1) veya değil (0). Etiketlemeye ilişkin bu olasılık eşiği %50 olarak belirlenmiş olup ayarlanabilir.

BikeBuyer (gerçek) sütununu Puanlanmış Etiketler (tahmin) ile karşılaştırarak modelin ne derece iyi sonuç verdiğini görebilirsiniz. Sonraki adımlarda bu modeli yeni müşteriler için tahminde bulunmak üzere kullanabilir, bir web hizmeti olarak yayımlayabilir veya sonuçları sonradan SQL Data Warehouse'a yazabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Tahmine dayalı makine öğrenimi modellerinin oluşturulmasına ilişkin daha fazla bilgi edinmek için bkz. [Azure'da Machine Learning'e giriş][Introduction to Machine Learning on Azure].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

