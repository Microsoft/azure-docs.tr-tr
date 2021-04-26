---
title: Machine Learning-Team Data Science Işlemindeki Özellik Mühendisliği
description: Machine Learning 'in veri geliştirme sürecinde Özellik Mühendisliği ve rolü hakkında bilgi edinin.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: b20a6744644678879fedf44e960854f558eb0f03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98610446"
---
# <a name="feature-engineering-in-machine-learning"></a>Machine Learning 'de Özellik Mühendisliği

Bu makalede, Machine Learning 'de verileri geliştirmeyle ilgili özellik Mühendisliği ve rolü hakkında bilgi edineceksiniz. [Azure Machine Learning Studio (klasik)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) denemeleri 'den çizilen tanım örneklerden öğrenin. 

* **Özellik Mühendisliği**: öğrenme algoritmasının tahmine dayalı gücünü artırmak için ham verilerden yeni özellikler oluşturma işlemi. Uygulanan özellikler, özgün özellik kümesinde kolayca açık olmayan ek bilgileri yakalemelidir.
* **Özellik seçimi**: eğitim sorununun boyutlılık düzeyini azaltmak için özelliklerin anahtar alt kümesini seçme işlemi.

Normalde **özellik Mühendisliği** , ek özellikler oluşturmak için önce uygulanır ve ardından **Özellik seçimi** , ilgisiz, yedekli veya çok bağıntılı özellikleri ortadan kaldıracak şekilde yapılır.

Özellik Mühendisliği ve seçimi, Team Data Science Işleminin (TDSP) [modelleme aşamasının](lifecycle-modeling.md) bir parçasıdır. TDSP ve veri bilimi yaşam döngüsü hakkında daha fazla bilgi edinmek için bkz. [TDSP nedir?](overview.md)

## <a name="what-is-feature-engineering"></a>Özellik Mühendisliği nedir?

Eğitim verileri satırlardan ve sütunlardan oluşan bir matristen oluşur. Matristeki her satır bir gözlem veya kayıttır. Her bir satırın sütunları, her bir kaydı tanımlayan özelliklerdir. Deneysel tasarımda belirtilen özellikler, verilerdeki desenleri nitelemelidir.

Birçok ham veri alanı doğrudan bir modeli eğitede kullanılabilse de, genellikle gelişmiş bir eğitim veri kümesi için ek (mühendislik uygulanan) Özellikler oluşturulması gerekir.

Eğitimin sağladığı özellikler, verilerdeki desenleri daha iyi bir şekilde farklılaştırır. Ancak bu işlem bir resimden birdir. Ses ve üretkenlik kararları genellikle etki alanı uzmanlığı gerektirir.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Örnek 1: regresyon modeli için zamana bağlı özellikler ekleme

Bir gerileme görevi için özelliklerin nasıl mühendislik yapılacağını göstermek üzere Azure Machine Learning Studio (klasik) [bisiklet için bisiklet talebi tahmini](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) 'ni kullanalım. Bu denemenin amacı, belirli bir ay/saat içindeki Bisiklet yeniden alma taleplerini tahmin etmeye yöneliktir.

### <a name="bike-rental-dataset"></a>Bisiklet Kiralama veri kümesi

[Bisiklet Kiralama UCI veri kümesi](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/) , Birleşik Devletler göre bir bisiklet paylaşma şirketinin gerçek verilerine dayalıdır. Bu, 2011 ve 2012 yılları için bir günün belirli bir saati içindeki Bisiklet kiratları sayısını temsil eder. 17.379 satır ve 17 sütun içerir.

Ham özellik kümesi, hava durumu koşullarını (sıcaklık/nem/rüzgar hızı) ve günün türünü (tatil/gün) içerir. Tahmin edilecek alan, belirli bir saat içindeki Bisiklet kiratlarının temsil ettiği sayıdır. 1 ile 977 arasında aralıklar sayılır.

### <a name="create-a-feature-engineering-experiment"></a>Özellik Mühendisliği denemesi oluşturma

Eğitim verilerinde etkili özellikler oluşturma amacını taşıyan dört regresyon modeli, aynı algoritma kullanılarak oluşturulmuştur ancak dört farklı eğitim veri kümesi ile oluşturulur. Dört veri kümesi aynı ham giriş verilerini temsil eder, ancak artan sayıda özellik ayarlanır. Bu özellikler dört kategoride gruplandırılır:

1. A = Hava durumu + tatil + HAFTANINGÜNÜ + tahmin edilen gün için hafta sonu özellikleri
2. B = önceki 12 saatin her birinde bırakılan Bisiklet sayısı
3. C = aynı saat içinde önceki 12 günün her birinde bırakılan Bisiklet sayısı
4. D = önceki 12 haftadan her birinde aynı saat ve aynı gün içinde bırakılan Bisiklet sayısı

Özgün ham verilerde zaten bulunan özellik kümesi A 'nın yanı sıra, özellik Mühendisliği işlemi aracılığıyla diğer üç özellik kümesi oluşturulur. B özellik kümesi, Bisikletler için son talebi yakalar. Özellik kümesi C, belirli bir saatteki Bisiklet taleplerini yakalar. Özellik kümesi D, belirli bir saat ve haftanın belirli bir gününde bisiklet taleplerini yakalar. Dört eğitim veri kümesi, sırasıyla A, A + B, A + B + C ve + B + C + D özellik kümesini içerir.

### <a name="feature-engineering-using-studio-classic"></a>Studio kullanarak özellik Mühendisliği (klasik)

Studio (klasik) denemesinde bu dört eğitim veri kümesi, önceden işlenmiş giriş veri kümesinden dört dal aracılığıyla oluşturulur. En soldaki dal haricinde, bu dalların her biri, türetilmiş özelliklerin (özellik kümesi B, C ve D) oluşturulduğu ve içeri aktarılan veri kümesine eklendiği bir [yürütme R betiği](/azure/machine-learning/studio-module-reference/execute-r-script) modülü içerir.

Aşağıdaki şekilde, ikinci sol dalda B özellik kümesi oluşturmak için kullanılan R betiği gösterilmektedir.

![Özellik Oluştur](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>Sonuçlar

Dört modelin performans sonuçlarının bir karşılaştırması aşağıdaki tabloda özetlenmiştir: 

![Sonuç karşılaştırması](./media/create-features/result1.png)

En iyi sonuçlar + B + C özelliklerine göre gösterilir. Eğitim verilerine ek özellik kümesi eklendiğinde hata oranı azalır. Bu özellik B özelliğinin ayarlandığı presumption doğrular, C, regresyon göreviyle ilgili ek bilgileri sağlar. Ancak, D özelliğinin eklenmesi hata ücretine ek bir azalma sağlamıyor gibi görünüyor.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> Örnek 2: metin madenciliği için özellikler oluşturma

Özellik Mühendisliği, belge sınıflandırması ve yaklaşım analizi gibi metin madenciliği ile ilgili görevlerde yaygın olarak uygulanır. Ham metnin bağımsız parçaları genellikle giriş verileri olarak kullanıldığından, özellik Mühendisliği işlemi, sözcük/tümcecik sıklıklarını içeren özellikleri oluşturmak için gereklidir.

### <a name="feature-hashing"></a>Özellik karma

Bu görevi gerçekleştirmek için, [özellik](/azure/machine-learning/studio-module-reference/feature-hashing) karması adlı bir teknik, rastgele metin özelliklerini etkin bir şekilde dizinler halinde dönüştürmek üzere uygulanır. Her metin özelliğini (sözcük/tümcecik) belirli bir dizinle ilişkilendirmek yerine, bu yöntem özelliklere bir karma işlevi uygular ve karma değerlerini doğrudan dizinler olarak kullanarak kullanın.

Studio 'da (klasik), kolayca Word/tümcecik özellikleri oluşturan bir [özellik karma](/azure/machine-learning/studio-module-reference/feature-hashing) modülü vardır. Aşağıdaki şekilde, Bu modülün kullanımıyla bir örnek gösterilmektedir. Giriş veri kümesi iki sütun içerir: 1 ile 5 arasında olan kitap derecelendirmesi ve gerçek gözden geçirme içeriği. Bu modülün amacı, belirli bir kitap incelemesinin içindeki karşılık gelen sözcük (ler) in oluşma sıklığını gösteren bir dizi yeni özelliği almak için kullanılır. Bu modülü kullanmak için aşağıdaki adımları izleyin:

* İlk olarak, giriş metnini içeren sütunu seçin (Bu örnekte "Col2").
* İkinci olarak, "bit boyutunu karma" olarak ayarlayın, bu da 2 ^ 8 = 256 özelliğin oluşturulacağı anlamına gelir. Tüm metindeki sözcük/aşama 256 dizin için karma oluşturacak. "Karma bit boyutu" parametresi 1 ile 31 arasındadır. Küçük tümcecikler, daha büyük bir sayı olarak ayarlanarak aynı dizinde karma hale getirilir.
* Üçüncü olarak, "N-gram" parametresini 2 olarak ayarlayın. Bu değer, giriş metininden tek tek sözcüklerin (her bir sözcüğe yönelik bir özellik) ve bigram (her komşu sözcük çiftinin bir özelliği için bir özellik) oluşma sıklığını alır. "N-gram" parametresi, bir özelliğe dahil edilecek en fazla sıralı sözcük sayısını gösteren 0 ile 10 arasında değişir.  

!["Özellik karma" modülü](./media/create-features/feature-Hashing1.png)

Aşağıdaki şekilde bu yeni özelliğin nasıl göründüğünü gösterilmektedir.

!["Özellik karma" örneği](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Sonuç
Uygulanan ve seçilen özellikler, eğitim sürecinin verimliliğini artırır ve bu da verilerde bulunan anahtar bilgilerini çıkarmaya çalışır. Ayrıca, giriş verilerini doğru şekilde sınıflandırmak ve daha fazla robustly hakkında daha fazla bilgi almak için bu modellerin gücünden de gelişir.

Daha ayrıntılı bilgi edinmek için özellik Mühendisliği ve seçimi de birleştirebilir. Bu, bir modeli ayarlamak veya eğitme için gereken özellik sayısını artırır ve azaltır. Matematik olarak, seçilen özellikler verilerdeki desenleri açıklayan ve sonuçları başarıyla tahmin eden en düşük bağımsız değişkenler kümesidir.

Her zaman özellik Mühendisliği veya özellik seçimi yapmak gerekmez. Bu, verilere, seçili algoritmaya ve denemenin hedefine bağlıdır.

## <a name="next-steps"></a>Sonraki adımlar

Belirli ortamlardaki verilere yönelik özellikler oluşturmak için aşağıdaki makalelere bakın:

* [SQL Server veri için özellikler oluşturma](create-features-sql-server.md)
* [Hive sorguları kullanarak Hadoop kümesindeki veriler için özellikler oluşturma](create-features-hive.md)