---
title: Otomatik makine öğrenimi (Otomatikml) modellerinden verileri işlemek için veri akışlarını kullanma
description: Otomatik makine öğrenimi (Otomatikml) modellerinden verileri işlemek için Azure Data Factory veri akışlarını nasıl kullanacağınızı öğrenin.
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595388"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>Veri akışlarını kullanarak otomatik makine öğrenimi modellerinden verileri işleme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Otomatik makine öğrenimi (otomatik ml), sınıflandırma, gerileme ve zaman serisi tahmin için belirttiğiniz hedef ölçümleri kullanarak en iyi modelleri eğitmek, ayarlamak ve elde etmek üzere Machine Learning projeleri tarafından benimsenmiştir.

Bir veri ambarından veya bir işlem veritabanından ham verilerin çok büyük bir veri kümesi olması, belki de 10 GB olması, oto ml için bir zorluk. Büyük bir veri kümesi modelleri eğitmek için daha uzun bir süre gerektirir, bu nedenle Azure Machine Learning modellerini eğitmeden önce veri işlemeyi iyileştirmenizi öneririz. Bu öğretici, bir veri kümesini bir Machine Learning veri kümesi için oto ml dosyalarına bölümlemek üzere Azure Data Factory nasıl kullanacağınızı öğreneceksiniz.

Oto ml projesi aşağıdaki üç veri işleme senaryosunu içerir:

* Modelleri eğitmadan önce, verileri oto ml dosyalarına bölümleyin.

     [Pandas veri çerçevesi](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) , modelleri eğitmadan önce verileri işlemek için yaygın olarak kullanılır. Pandas veri çerçevesi 1 GB 'tan küçük veri boyutları için iyi işler, ancak veriler 1 GB 'den büyükse, bir Pandas veri çerçevesi verileri işlemek için yavaşlar. Bazen bellek dışı bir hata iletisi alabilirsiniz. Makine öğrenimi için bir [Parquet dosya](https://parquet.apache.org/) biçimi kullanmanızı öneririz çünkü bu bir ikili sütunlu biçim.
    
     Data Factory eşleme veri akışları, veri mühendislerinin kod yazmasını sağlayan görsel olarak tasarlanan veri dönüştürmelerdir. İşlem hattı ölçekli Spark kümeleri kullandığından, veri akışlarını eşleme, büyük verileri işlemek için güçlü bir yoldur.

* Eğitim veri kümesini ve test veri kümesini ayırın.
    
    Eğitim veri kümesi, eğitim modeli için kullanılacaktır. Test veri kümesi, bir makine öğrenimi projesindeki modelleri değerlendirmek için kullanılır. Veri akışlarını eşlemek için koşullu bölünmüş etkinlik, eğitim verilerini ve test verilerini böler.

* Nitelenmemiş verileri kaldırın.

    Sıfır satırlık bir Parquet dosyası gibi, nitelenmemiş verileri kaldırmak isteyebilirsiniz. Bu öğreticide, satır sayısının sayısını almak için toplama etkinliğini kullanacağız. Satır sayısı, nitelenmemiş verileri kaldırmak için bir koşul olacak.

## <a name="preparation"></a>Hazırlık

Aşağıdaki Azure SQL veritabanı tablosunu kullanın.

```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Veri biçimini Parquet 'e Dönüştür

Aşağıdaki veri akışı bir SQL veritabanı tablosunu bir Parquet dosya biçimine dönüştürecek:

- **Kaynak veri kümesi**: SQL veritabanının işlem tablosu.
- **Havuz veri kümesi**: Parquet biçimiyle BLOB depolama.

## <a name="remove-unqualified-data-based-on-row-count"></a>Nitelenmemiş verileri satır sayısına göre kaldır

İkiden küçük olan bir satır sayısını kaldırdığımızda, diyelim.

1. Satır sayısının sayısını almak için toplama etkinliğini kullanın. Satır sayısı için Col2 ve **toplamalara** göre **gruplanmış olarak** kullanın `count(1)` .

    ![Satır sayısının sayısını almak için toplama etkinliğinin yapılandırılmasını gösteren ekran görüntüsü.](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Havuz etkinliğini kullanarak, **Havuz sekmesinde** **Havuz türünü** **önbellek** olarak seçin. Ardından, **Ayarlar** sekmesindeki **anahtar sütunları** aşağı açılan listesinden istediğiniz sütunu seçin.

    ![Önbelleğe alınmış bir havuzdaki satır sayısının sayısını almak için CacheSink etkinliğinin yapılandırılmasını gösteren ekran görüntüsü.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Kaynak akışa bir satır sayısı sütunu eklemek için türetilmiş sütun etkinliğini kullanın. **Türetilmiş sütunun ayarlar** sekmesinde, `CacheSink#lookup` cachesink 'den bir satır sayısı almak için ifadesini kullanın.

    ![Source1 içindeki satır sayısının sayısını eklemek için türetilmiş sütun etkinliğinin yapılandırılmasını gösteren ekran görüntüsü.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Nitelenmemiş verileri kaldırmak için koşullu bölme etkinliğini kullanın. Bu örnekte, satır sayısı col2 sütununu temel alır. Koşul, sıfırdan küçük bir satır sayısı kaldırmak, bu nedenle iki satır (KIMLIK = 2 ve KIMLIK = 7) kaldırılacak. Veri yönetimi için, nitelenmemiş verileri blob depolamaya kaydetmelisiniz.

    ![Sıfırdan büyük veya eşit verileri almak için koşullu bölme etkinliğinin yapılandırılmasını gösteren ekran görüntüsü.](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * Sonraki adımlarda orijinal kaynakta kullanılacak olan satır sayısının sayısını almak için yeni bir kaynak oluşturun.
>    * Performans açısından CacheSink kullanın.

## <a name="split-training-data-and-test-data"></a>Eğitim verilerini ve test verilerini bölme

Her bölüm için eğitim verilerini ve test verilerini bölmek istiyoruz. Bu örnekte, aynı col2 değeri için, test verileri olarak en üstteki iki satırı ve daha geri kalan satırları eğitim verileri olarak alın.

1. Her bölüm için bir sütun satır numarası eklemek için pencere etkinliğini kullanın. Yukarıdaki sekmede **,** bölüm için bir sütun seçin. Bu öğreticide, col2 için bölümleyeceğiz. **Sıralama** sekmesine bir sıra verin, bu ÖĞRETICIDE kimlik temel alınır. Her bölüm için bir satır numarası olarak bir sütun eklemek için **pencere sütunları** sekmesine bir sıralama verin.

    ![Yeni bir sütunun satır numarasına eklenmesi için pencere etkinliğinin yapılandırılmasını gösteren ekran görüntüsü.](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Her bir bölümün en üst iki satırını test veri kümesine ve diğer satırları eğitim veri kümesine bölmek için koşullu bölme etkinliğini kullanın. **Koşullu bölünmüş ayarlar** sekmesinde, deyimi `lesserOrEqual(RowNum,2)` koşul olarak kullanın.

    ![Geçerli veri kümesini eğitim veri kümesine ve test veri kümesine bölmek için koşullu bölünmüş etkinliğin yapılandırılmasını gösteren ekran görüntüsü.](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Eğitim ve test veri kümelerini Parquet biçimiyle bölme

Havuz etkinliğini kullanarak, bir sütunu bölüm için bir sütun anahtarı olarak ayarlamak için **en iyileştirme** sekmesinde, **bölüm başına benzersiz değer** ' i kullanın.

![Eğitim veri kümesinin bölümünü ayarlamak için havuz etkinliğinin yapılandırılmasını gösteren ekran görüntüsü.](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

Tüm işlem hattı mantığına geri bakalım.

![Tüm işlem hattının mantığını gösteren ekran görüntüsü.](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>Sonraki adımlar

Veri akışı [dönüştürmelerinin](concepts-data-flow-overview.md)eşlemesini kullanarak veri akışı mantığınızın geri kalanını oluşturun.
