---
title: 'Veri kümesindeki sütunları seçin: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Aşağı akış işlemlerinde kullanılacak sütunların bir alt kümesini seçmek için Azure Machine Learning içindeki sütunları seçme modülünde nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 3511c448298aa96c95dc970d1d192869c127eb0d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497656"
---
# <a name="select-columns-in-dataset-module"></a>Veri kümesi modülündeki sütunları seçme

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Aşağı akış işlemlerinde kullanılacak sütunların bir alt kümesini seçmek için bu modülü kullanın. Modül, kaynak veri kümesinden sütunları fiziksel olarak kaldırmaz; Bunun yerine, bir veritabanı *görünümü* veya *projeksiyonu*gibi sütunların bir alt kümesini oluşturur.

Bu modül, bir aşağı akış işlemi için kullanılabilir sütunları sınırlandırmanız gerektiğinde veya gereksiz sütunları kaldırarak veri kümesinin boyutunu azaltmak istediğinizde faydalıdır.

Veri kümesindeki sütunlar, farklı bir düzende belirtseniz bile, özgün verilerle aynı sırada çıktılardır.

## <a name="how-to-use"></a>Nasıl kullanılır

Bu modülün parametresi yok. Dahil edilecek veya hariç tutulacak sütunları seçmek için sütun seçiciyi kullanın.

### <a name="choose-columns-by-name"></a>Sütunları ada göre seçin

Modülde ada göre sütun seçmek için birden çok seçenek vardır: 

+ Filtre ve arama

    **Ada göre** seçeneğine tıklayın.

    Zaten doldurulmuş bir veri kümesini bağladıysanız, kullanılabilir sütunların bir listesi görünmelidir. Hiçbir sütun görünmezse, sütun listesini görüntülemek için yukarı akış modüllerini çalıştırmanız gerekebilir.

    Listeyi filtrelemek için, arama kutusuna yazın. Örneğin, arama kutusuna `w` harfini yazarsanız, liste, `w`harfini içeren sütun adlarını gösterecek şekilde filtrelenir.

    Sütunlar ' ı seçin ve sağ taraftaki bölmedeki seçili sütunları listeye taşımak için sağ ok düğmesine tıklayın.

    + Sürekli sütun adları aralığını seçmek için **SHIFT + tıklama**tuşlarına basın.
    + Seçime ayrı sütunlar eklemek için **CTRL + tıklama**tuşlarına basın.

    Kaydetmek ve kapatmak için onay işareti düğmesine tıklayın.

+ Diğer kurallarla birlikte adları kullanın

    **Kurallar ile** seçeneğine tıklayın.
    
    Belirli bir veri türünün sütunlarını gösterme gibi bir kural seçin.

    Ardından, seçim listesine eklemek için bu türden tek tek sütunları ada göre seçin.

+ Sütun adlarının virgülle ayrılmış bir listesini yazın veya yapıştırın

    Veri kümeniz genişse, sütunları tek tek seçmek yerine dizinleri veya oluşturulmuş adların listesini kullanmak daha kolay olabilir. Listeyi önceden hazırladığınıza varsayılarak:

    1. **Kurallar ile** seçeneğine tıklayın. 
    2. **Sütun yok**' u seçin, **Ekle**' yi seçin ve sonra kırmızı ünlem işaretiyle metin kutusunun içine tıklayın. 
    3. Önceden doğrulanan sütun adlarının virgülle ayrılmış bir listesini içine yapıştırın veya yazın. Herhangi bir sütunda geçersiz bir ad varsa modülü kaydedemezsiniz, bu nedenle adları önceden denetlediğinizden emin olun.
    
    Bu yöntemi, dizin değerlerini kullanarak sütunların bir listesini belirtmek için de kullanabilirsiniz. 

### <a name="choose-by-type"></a>Türe göre seçin

**WıTH Rules** seçeneğini kullanırsanız, sütun seçimlerinde birden çok koşul uygulayabilirsiniz. Örneğin, bir sayısal veri türünün yalnızca özellik sütunlarını almanız gerekebilir.

**Ile başla** seçeneği Başlangıç noktanızı belirler ve sonuçları anlamak için önemlidir. 

+ **Tüm sütunlar** seçeneğini belirlerseniz, tüm sütunlar listeye eklenir. Ardından, belirli koşullara uyan sütunları *kaldırmak* için **Dışla** seçeneğini kullanmanız gerekir. 

    Örneğin, tüm sütunlarla başlayabilir ve ardından sütunları ada veya türe göre kaldırabilirsiniz.

+ **Sütun yok** seçeneğini belirlerseniz, sütun listesi boş olarak başlar. Daha sonra listeye sütun *eklemek* için koşulları belirtirsiniz. 

    Birden çok kural uygularsanız, her koşul **eklenebilir**. Örneğin, herhangi bir sütun olmadan başlamanızı ve sonra tüm sayısal sütunları almak için bir kural eklemenizi söyleyin. Otomobil fiyat veri kümesinde, 16 sütun sonucu oluşur. Ardından, yeni bir koşul eklemek için **+** işaretine tıklayıp **tüm özellikleri dahil et**' i seçin. Elde edilen veri kümesi, bazı dize özelliği sütunları da dahil olmak üzere tüm sayısal sütunları ve tüm özellik sütunlarını içerir.

### <a name="choose-by-column-index"></a>Sütun dizinine göre seçin

Sütun dizini, özgün veri kümesindeki sütunun sırasını ifade eder.

+ Sütunlar 1 ' den başlayarak ardışık olarak numaralandırılır.  
+ Bir dizi sütun almak için kısa çizgi kullanın. 
+ `1-` veya `-3` gibi açık uçlu belirtimlerde izin verilmez.
+ Yinelenen Dizin değerlerine (veya sütun adlarına) izin verilmez ve bir hata oluşmasına neden olabilir.

Örneğin, veri kümenizin en az sekiz sütunu olduğu varsayıldığında, birden çok bitişik olmayan sütun döndürmek için aşağıdaki örneklerden herhangi birini yapıştırabilirsiniz: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

Son örnek bir hatayla sonuçlanmaz; Ancak, `4`sütunun tek bir örneğini döndürür.



### <a name="change-order-of-columns"></a>Sütunların sırasını değiştir

**Seçimdeki yinelenen öğelere Izin ver ve sütun sırasını koru** seçeneği boş bir listeyle başlar ve ad ya da dizine göre belirttiğiniz sütunları ekler. Sütunları her zaman "doğal sırasıyla" döndüren diğer seçeneklerin aksine, bu seçenek sütunları ad veya listeettiğiniz sırada çıkarır. 

Örneğin, Sütun1, Col2, Col3 ve col4 sütunlarına sahip bir veri kümesinde aşağıdaki listelerden birini belirterek sütunların sırasını ters çevirebilirsiniz ve sütun 2 ' yi dışarıda bırakabilirsiniz:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 