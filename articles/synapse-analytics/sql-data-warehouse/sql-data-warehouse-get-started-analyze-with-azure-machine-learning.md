---
title: Azure Machine Learning ile veri çözümleme
description: Azure SYNAPSE 'de depolanan verileri temel alan tahmine dayalı bir makine öğrenimi modeli oluşturmak için Azure Machine Learning kullanın.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 76a154d3a137017f374247308a3980d598698246
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678668"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Azure Machine Learning ile veri çözümleme

Bu öğretici, tahmine dayalı bir makine öğrenimi modeli oluşturmak için [Azure Machine Learning tasarımcısını](../../machine-learning/concept-designer.md) kullanır. Model, Azure SYNAPSE 'de depolanan verileri temel alır. Öğreticinin senaryosu, bir müşterinin bir bisiklet satın alabileceği ya da Adventure Works 'Ün hedeflenen bir pazarlama kampanyası oluşturabileceğiniz şekilde tahmin edilmesine yönelik bir tahmin sağlamaktır.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide ilerleyebilmeniz için şunlar gereklidir:

* AdventureWorksDW örnek verileriyle önceden yüklenmiş bir SQL Havuzu. Bu SQL havuzunu sağlamak için bkz. [SQL havuzu oluşturma](create-data-warehouse-portal.md) ve örnek verileri yüklemeyi seçme. Zaten bir veri ambarınız varsa ancak örnek verileriniz yoksa, [örnek verileri el ile](./load-data-from-azure-blob-storage-using-copy.md)yükleyebilirsiniz.
* bir Azure Machine Learning çalışma alanı. Yeni bir tane oluşturmak için [Bu öğreticiyi](../../machine-learning/how-to-manage-workspace.md) izleyin.

## <a name="get-the-data"></a>Verileri alma

Kullanılan veriler, AdventureWorksDW ' deki dbo. vTargetMail görünümüdür. Bu öğreticide veri deposunu kullanmak için, Azure SYNAPSE Şu anda veri kümelerini desteklemediği için veriler ilk olarak Azure Data Lake Storage hesaba aktarılabilir. Azure Data Factory, [kopyalama etkinliğini](../../data-factory/copy-activity-overview.md)kullanarak verileri veri ambarından Azure Data Lake Storage dışarı aktarmak için kullanılabilir. İçeri aktarma için aşağıdaki sorguyu kullanın:

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

Veriler Azure Data Lake Storage kullanılabilir olduktan sonra, [Azure Storage Services 'a bağlanmak](../../machine-learning/how-to-access-data.md)için Azure Machine Learning Içindeki veri depoları kullanılır. Bir veri deposu ve karşılık gelen bir veri kümesi oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal Azure Machine Learning Studio 'yu başlatın veya [Azure Machine Learning Studio](https://ml.azure.com/)'da oturum açın.

1. **Yönet** bölümündeki sol bölmede bulunan **veri depoları** ' na tıklayın ve ardından **Yeni veri deposu**' na tıklayın.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Azure Machine Learning arabiriminin sol bölmesinin ekran görüntüsü":::

1. Veri deposu için bir ad sağlayın, türü ' Azure Blob Storage ' olarak seçin, konum ve kimlik bilgilerini sağlayın. Ardından, **Oluştur**'u tıklatın.

1. Sonra, **varlıklar** bölümünde sol bölmedeki **veri kümeleri** ' ne tıklayın. Veri **deposundan** seçeneğini kullanarak **veri kümesi oluştur** ' u seçin.

1. Veri kümesinin adını belirtin ve **tablo** olarak kullanılacak türü seçin. Ardından **İleri** ' ye tıklayarak ileri ' ye tıklayın.

1. **Veri deposu Seç veya Oluştur bölümünde**, **önceden oluşturulmuş veri deposu** seçeneğini belirleyin. Daha önce oluşturulan veri deposunu seçin. Ileri ' ye tıklayın ve yolu ve dosya ayarlarını belirtin. Dosyalar içeriyorsa sütun üst bilgisi belirttiğinizden emin olun.

1. Son olarak, veri kümesini oluşturmak için **Oluştur** ' a tıklayın.

## <a name="configure-designer-experiment"></a>Tasarımcı denemesini yapılandırma

Sonra, tasarımcı yapılandırması için aşağıdaki adımları izleyin:

1. **Yazar** bölümünün sol bölmesindeki **Tasarımcı** sekmesine tıklayın.

1. Yeni bir işlem hattı oluşturmak için kullanımı **kolay önceden oluşturulmuş modüller** ' i seçin.

1. Sağdaki ayarlar bölmesinde, işlem hattının adını belirtin.

1. Ayrıca, daha önce sağlanan bir kümeye tüm denemeler için bir hedef işlem kümesi seçin. Ayarlar bölmesini kapatın.

## <a name="import-the-data"></a>Verileri içeri aktarma

1. Arama kutusunun altındaki sol bölmedeki **veri kümeleri** alt sekmesini seçin.

1. Daha önce oluşturduğunuz veri kümesini tuvale sürükleyin.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="Tuval üzerindeki veri kümesi modülünün ekran görüntüsü.":::

## <a name="clean-the-data"></a>Verileri temizleyin

Verileri temizlemek için, modeliyle ilgili olmayan sütunları bırakın. Aşağıdaki adımları izleyin:

1. Sol bölmedeki **modüller** alt sekmesini seçin.

1. **Veri dönüştürme <** altındaki **sütun Seç** modülünü tuvale sürükleyin. Bu modülü **veri kümesi** modülüne bağlayın.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="Tuvalde sütun seçimi modülünün ekran görüntüsü." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. Özellikler bölmesini açmak için modüle tıklayın. Hangi sütunları bırakmak istediğinizi belirtmek için sütunu Düzenle ' ye tıklayın.

1. Şu iki sütunu dışlayın: CustomerAlternateKey ve GeographyKey. **Kaydet**’e tıklayın

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="Bırakılan sütunları gösteren ekran görüntüsü.":::

## <a name="build-the-model"></a>Modeli oluşturma

Veriler makine öğrenimi modelini eğitmek için% 80-20:80 ' ü ve modeli test etmek için %20 ' i bölünür. Bu ikili sınıflandırma sorunu için "iki sınıf" algoritmaları kullanılır.

1. **Bölünmüş veri** modülünü tuvale sürükleyin.

1. Özellikler bölmesinde, **ilk çıkış veri kümesindeki satır kesiri** için 0,8 girin.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="0,8 bölme oranını gösteren ekran görüntüsü.":::

1. **Two-Class Boosted Decision Tree (İki Sınıflı Gelişmiş Karar Ağacı)** modülünü tuvale sürükleyin.

1. **Model eğitme** modülünü tuvale sürükleyin. **Iki sınıflı bir karar ağacına** (ml algoritması) bağlanarak girişleri belirtin ve verileri (algoritma üzerinde eğitme verileri) modülleri ile **ayırın** .

1. Model modeli eğitimi için, Özellikler bölmesinde **etiket sütunu** seçeneğinde sütunu Düzenle ' yi seçin. Tahmin edilecek sütun olarak **Bikesatınalmacı** sütununu seçin ve **Kaydet**' i seçin.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="Bikesatınalmacı etiket sütununu gösteren ekran görüntüsü.":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="Two-Class, artırılmış karar ağacına ve bölünmüş veri modüllerine bağlı eğitim modeli modülünü gösteren ekran görüntüsü.":::

## <a name="score-the-model"></a>Modeli puanlama

Şimdi modelin test verilerinde nasıl gerçekleştirileceğini test edin. Hangisinin daha iyi bir şekilde gerçekleştiğini görmek için iki farklı algoritma karşılaştırılacaktır. Aşağıdaki adımları izleyin:

1. **Puanlama modeli** modülünü tuvale sürükleyin ve **modeli eğmek** ve **veri modüllerini bölmek** için bağlayın.

1. **Iki sınıf Bayes ortalama Perceptron** ' i deneme tuvaline sürükleyin. Bu algoritmanın Two-Class, artırılmış karar ağacına kıyasla nasıl gerçekleştiğini karşılaştırırsınız.

1. Modülleri kopyalayıp yapıştırın modeli **eğitme** model ve **Puanlama modeli** .

1. İki algoritmayı karşılaştırmak için **Evaluate Model (Model Değerlendirme)** modülünü tuvale sürükleyin.

1. İşlem hattı çalıştırmasını ayarlamak için **Gönder** ' e tıklayın.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="Tuvaldeki diğer tüm modüllerin ekran görüntüsü." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. Çalıştırma tamamlandığında **modeli değerlendir** modülünü sağ tıklatın ve **değerlendirme sonuçlarını görselleştirin**' i tıklatın.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="Sonuçların ekran görüntüsü.":::

Belirtilen ölçümler ROC eğrisi, duyarlık geri çağırma diyagramı ve yükseltme eğrisi. Birinci modelin ikinciden daha iyi gerçekleştirildiğini görmek için bu ölçümlere bakın. İlk modelin tahmin edilen bölümüne bakmak için, puan modeli modülüne sağ tıklayın ve tahmin edilen sonuçları görmek için puanlanmış veri kümesini Görselleştir ' e tıklayın.

Test veri kümenize daha fazla sütun eklendiğini göreceksiniz.

* Puanlanmış Olasılıklar: müşterinin bir bisiklet alıcısı olma olasılığı.
* Puanlanmış Etiketler: model tarafından yapılan sınıflandırma; bisiklet alıcısı (1) veya değil (0). Etiketlemeye ilişkin bu olasılık eşiği %50 olarak belirlenmiş olup ayarlanabilir.

Modelin ne kadar iyi gerçekleştirildiğini görmek için, Bikealıcısı (gerçek) ile puanlanmış Etiketler (tahmin) sütununu karşılaştırın. Daha sonra bu modeli kullanarak yeni müşterileri tahmin edebilirsiniz. [Bu modeli bir Web hizmeti olarak yayımlayabilir](../../machine-learning/tutorial-designer-automobile-price-deploy.md) veya sonuçları Azure SYNAPSE 'e geri yazabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hakkında daha fazla bilgi edinmek için bkz. [Azure 'da Machine Learning 'ye giriş](../../machine-learning/overview-what-is-azure-ml.md).

Veri ambarındaki yerleşik Puanlama hakkında bilgi [edinin.](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)