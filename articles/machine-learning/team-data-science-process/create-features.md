---
title: Veri bilimi 'nde Özellik Mühendisliği-takım veri bilimi Işlemi
description: Özellik mühendisliğinin amaçlarını açıklar ve Machine Learning 'in veri geliştirme sürecinde rolünün örneklerini sağlar.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e633c5742b8a7882149a347ced46e55440cb6913
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492446"
---
# <a name="feature-engineering-in-data-science"></a>Veri bilimi 'nde Özellik Mühendisliği
Bu makalede, özellik mühendisliğinin amaçları açıklanmakta ve Machine Learning 'in veri geliştirme işleminde rolün örnekleri verilmiştir. Bu işlemi göstermek için kullanılan Örnekler Azure Machine Learning Studio çizilir. 

Bu görev, [ekip veri bilimi işlemindeki (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)bir adımdır.

Özellik Mühendisliği, öğrenme sürecinin kolaylaştırmasına yardımcı olan ham verilerden Özellikler oluşturarak öğrenme algoritmalarının tahmine dayalı gücünü artırmaya çalışır. Özelliklerin Mühendisliği ve seçimi, [Team Data Science işlem yaşam döngüsü nedir?](overview.md) bölümünde özetlenen TDSP 'nin bir parçasıdır mi? Özellik Mühendisliği ve seçimi, TDSP 'nin **Özellikler geliştirme** adımının parçalarından oluşur. 

* **özellik Mühendisliği**: Bu işlem, verilerdeki mevcut ham özelliklerden ek ilgili özellikler oluşturmaya çalışır ve öğrenme algoritmasının tahmine dayalı gücünü artırır.
* **Özellik seçimi**: Bu işlem, eğitim sorununun boyutlılık düzeyini azaltmak için bir denemede orijinal veri özelliklerinin anahtar alt kümesini seçer.

Normalde **özellik Mühendisliği** , ek özellikler oluşturmak için önce uygulanır ve ardından **Özellik seçimi** adımı, ilgisiz, yedekli veya çok bağıntılı özellikleri ortadan kaldırmak için gerçekleştirilir.

Machine Learning 'de kullanılan eğitim verileri genellikle toplanan ham verilerden Özellikler ayıklanarak geliştirilebilir. El ile yazılan karakterlerin görüntülerini nasıl sınıflandırakullanacağınızı öğrenme bağlamında uygulanan bir özellik örneği, ham bit dağıtım verilerinden oluşturulan bir bit yoğunluğu eşlemesinin oluşturduğu bir özelliktir. Bu harita, yalnızca ham dağıtımı doğrudan kullanmaktan daha verimli karakterlerin kenarlarının bulunmasına yardımcı olabilir.

Belirli ortamlardaki verilere yönelik özellikler oluşturmak için aşağıdaki makalelere bakın:

* [SQL Server veri için özellikler oluşturma](create-features-sql-server.md)
* [Hive sorguları kullanarak Hadoop kümesindeki veriler için özellikler oluşturma](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Veri özelliği mühendisinizden özellikler oluşturma
Eğitim verileri, her birinin bir dizi Özellik (değişkenler veya sütunlar halinde depolanan) içeren örneklerden (kayıtlar veya satırlarda depolanan gözlemler) oluşan bir matristen oluşur. Deneysel tasarımda belirtilen özelliklerin, verilerdeki desenleri niteleyen olması beklenir. Ham veri alanlarının birçoğu bir modeli eğitmek için kullanılan seçili özellik kümesine doğrudan dahil edilebilir olsa da, gelişmiş bir eğitim veri kümesi oluşturmak için ham verilerdeki özelliklerden ek (mühendislik uygulanmış) özelliklerin oluşturulması gerekir.

Bir modele eğitim yaparken veri kümesini iyileştirmek için ne tür özellikler oluşturulmalıdır? Eğitimin geliştirilmesine uygulanan özellikler, verilerdeki desenleri daha iyi fark eden bilgiler sağlar. Yeni özelliklerin, açıkça yakalanmayan veya orijinal ya da mevcut özellik kümesinde kolayca görünen ek bilgiler sağlaması beklenir. Ancak bu işlem bir resimden birdir. Ses ve üretkenlik kararları genellikle bazı etki alanı uzmanlığı gerektirir.

Azure Machine Learning ile Başlarken, bu işlemin en kolay şekilde, Studio 'da sağlanan örnekleri kullanarak bu süreci çok daha kolay bir şekilde kullanmaktır. Burada iki örnek sunulmaktadır:

* Hedef değerlerin bilindiği, denetimli bir deneyde [Bisiklet örnek sayısının](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) gerileme örneği tahmini
* [Özellik karmalama](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) kullanan bir metin araştırma sınıflandırması örneği

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Örnek 1: regresyon modeli için zamana bağlı özellikler ekleme
Bir gerileme görevi için özelliklere nasıl mühendislik yapılacağını göstermek üzere Azure Machine Learning Studio (klasik) içinde "Bisiklet tahmini Bisiklet" denemesini kullanalım. Bu denemenin amacı, Bisikletler için talebi tahmin etmek, diğer bir deyişle, belirli bir ay/gün/saat içindeki Bisiklet salları sayısını tahmin etmek içindir. "Bisiklet Kiralama veri kümesi" veri kümesi ham giriş verileri olarak kullanılır. Bu veri kümesi, Birleşik Devletler Washington DC 'de Bisiklet Kiralama ağını tutan büyük Bıkespaylaşma şirketinin gerçek verilerini temel alır. Veri kümesi, 2011 ve 15 2012 yılında bir günün belirli bir saati içindeki Bisiklet tuttleri sayısını temsil eder ve 17379 satırları ve 17 sütunları içerir. Ham özellik kümesi, hava durumu koşullarını (sıcaklık/nem/rüzgar hızı) ve günün türünü (tatil/gün) içerir. Tahmin edilecek alan, belirli bir saat içinde bisiklet ve 1 ile 977 arasında değişen Bisiklet alanlarını temsil eden "sayisi" sayıdır.

Eğitim verilerinde etkili özellikler oluşturma amacını taşıyan dört regresyon modeli, aynı algoritma kullanılarak oluşturulmuştur ancak dört farklı eğitim veri kümesi ile oluşturulur. Dört veri kümesi aynı ham giriş verilerini temsil eder, ancak artan sayıda özellik ayarlanır. Bu özellikler dört kategoride gruplandırılır:

1. A = Hava durumu + tatil + HAFTANINGÜNÜ + tahmin edilen gün için hafta sonu özellikleri
2. B = önceki 12 saatin her birinde bırakılan Bisiklet sayısı
3. C = aynı saat içinde önceki 12 günün her birinde bırakılan Bisiklet sayısı
4. D = önceki 12 haftadan her birinde aynı saat ve aynı gün içinde bırakılan Bisiklet sayısı

Özgün ham verilerde zaten bulunan özellik kümesi A 'nın yanı sıra, özellik Mühendisliği işlemi aracılığıyla diğer üç özellik kümesi oluşturulur. Özellik kümesi B, Bisiklet için çok son talebi yakalar. Özellik kümesi C, belirli bir saatteki Bisiklet taleplerini yakalar. Özellik kümesi D, belirli bir saat ve haftanın belirli bir gününde bisiklet taleplerini yakalar. Dört eğitim veri kümesi, sırasıyla A, A + B, A + B + C ve + B + C + D özellik kümesini içerir.

Azure Machine Learning denemesindeki bu dört eğitim veri kümesi, önceden işlenmiş giriş veri kümesinden dört dal aracılığıyla oluşturulur. En soldaki dal haricinde, bu dalların her biri, türetilmiş özelliklerin (özellik kümesi B, C ve D) oluşturulduğu ve içeri aktarılan veri kümesine eklendiği bir [yürütme R betiği](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) modülü içerir. Aşağıdaki şekilde, ikinci sol dalda B özellik kümesi oluşturmak için kullanılan R betiği gösterilmektedir.

![Özellik Oluştur](./media/create-features/addFeature-Rscripts.png)

Dört modelin performans sonuçlarının bir karşılaştırması aşağıdaki tabloda özetlenmiştir: 

![Sonuç karşılaştırması](./media/create-features/result1.png)

En iyi sonuçlar + B + C özelliklerine göre gösterilir. Eğitim verilerine ek özellik kümesi eklendiğinde hata oranının azaldığına göz önünde unutmayın. Bu özellik B özelliğinin ayarlandığı presumption doğrular, C, regresyon göreviyle ilgili ek bilgileri sağlar. Ancak, D özelliğinin eklenmesi hata ücretine ek bir azalma sağlamıyor gibi görünüyor.

## <a name="example2"></a>Örnek 2: metin madenciliği özellikler oluşturma
Özellik Mühendisliği, belge sınıflandırması ve yaklaşım analizi gibi metin madenciliği ile ilgili görevlerde yaygın olarak uygulanır. Örneğin, belgeleri birkaç kategoride sınıflandırmak istediğinizde, genellikle bir belge kategorisinde bulunan sözcüğün/deyimlerin başka bir belge kategorisinde oluşma olasılığını azaltır. Diğer bir deyişle, sözcüklerin/deyimlerin dağılımının sıklığı farklı belge kategorilerini nitelenebilir. Metin araştırma uygulamalarında, tek tek metin içeriği parçaları genellikle giriş verileri olarak görev yaptığından, özellik Mühendisliği işlemi, sözcük/tümcecik sıklıklarını içeren özellikleri oluşturmak için gereklidir.

Bu görevi gerçekleştirmek için, **özellik** karması adlı bir teknik, rastgele metin özelliklerini etkin bir şekilde dizinler halinde dönüştürmek üzere uygulanır. Her metin özelliğini (kelimeler/tümceleri) belirli bir dizinle ilişkilendirmek yerine, bu yöntem özelliklere bir karma işlev uygulayarak ve karma değerlerini doğrudan dizinler olarak kullanarak çalışır.

Azure Machine Learning, bu sözcük/tümcecik özelliklerini rahat bir şekilde oluşturan bir [özellik karma](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modülü vardır. Aşağıdaki şekilde, Bu modülün kullanımıyla bir örnek gösterilmektedir. Giriş veri kümesi iki sütun içerir: 1 ile 5 arasında olan kitap derecelendirmesi ve gerçek gözden geçirme içeriği. Bu [özellik karma](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modülünün amacı, belirli bir kitap incelemesinin içindeki karşılık gelen sözcük (ler) in oluşma sıklığını gösteren bir dizi yeni özelliği almak için kullanılır. Bu modülü kullanmak için aşağıdaki adımları izleyin:

* İlk olarak, giriş metnini içeren sütunu seçin (Bu örnekte "Col2").
* İkinci olarak, "bit boyutunu karma" olarak ayarlayın, bu da 2 ^ 8 = 256 özelliğin oluşturulacağı anlamına gelir. Tüm metindeki sözcük/aşama 256 dizin için karma oluşturacak. "Karma bit boyutu" parametresi 1 ile 31 arasındadır. Küçük tümcecikler, daha büyük bir sayı olarak ayarlanarak aynı dizinde karma hale getirilir.
* Üçüncü olarak, "N-gram" parametresini 2 olarak ayarlayın. Bu değer, giriş metininden tek tek sözcüklerin (her bir sözcüğe yönelik bir özellik) ve bigram (her komşu sözcük çiftinin bir özelliği için bir özellik) oluşma sıklığını alır. "N-gram" parametresi, bir özelliğe dahil edilecek en fazla sıralı sözcük sayısını gösteren 0 ile 10 arasında değişir.  

!["Özellik karma" modülü](./media/create-features/feature-Hashing1.png)

Aşağıdaki şekilde bu yeni özelliğin nasıl göründüğünü gösterilmektedir.

!["Özellik karma" örneği](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Sonuç
Uygulanan ve seçilen özellikler, eğitim sürecinin verimliliğini artırır ve bu da verilerde bulunan anahtar bilgilerini çıkarmaya çalışır. Ayrıca, giriş verilerini doğru şekilde sınıflandırmak ve daha fazla robustly hakkında daha fazla bilgi almak için bu modellerin gücünden de gelişir. Daha ayrıntılı bilgi edinmek için özellik Mühendisliği ve seçimi de birleştirebilir. Bu, bir modeli ayarlamak veya eğitme için gereken özellik sayısını artırır ve azaltır. Matematik olarak konuşma, modeli eğitme için seçilen özellikler, verilerdeki desenleri açıklayan ve sonra sonuçları başarıyla tahmin eden en düşük bağımsız değişkenler kümesidir.

Her zaman özellik Mühendisliği veya özellik seçimi yapmak gerekmez. Gerekli olup olmadığı veya toplanmasına, seçilen algoritmaya ve denemenin hedefine bağlıdır.

