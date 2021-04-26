---
title: ML Studio (klasik)-ekip veri bilimi Işlemi için veri hazırlığı
description: Makine öğrenimi için etkili bir şekilde kullanılmak üzere hazırlama ve verileri temizleme.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96012416"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Gelişmiş machine learning için verileri hazırlama görevleri
Ön işleme ve temizleme verileri, model eğitimi için bir veri kümesinin kullanılabilmesi için önce gerçekleştirilmesi gereken önemli görevlerdir. Ham veriler genellikle gürültülü ve güvenilir değildir ve eksik değerler olabilir. Modelleme için bu tür verilerin kullanılması yanıltıcı sonuçlar verebilir. Bu görevler, Team Data Science Işleminin (TDSP) bir parçasıdır ve genellikle, gerekli ön işlemden sonra keşfedilmesi ve planlanması için kullanılan bir veri kümesinin ilk araştırmasını izler. TDSP işlemi hakkında daha ayrıntılı yönergeler için, [Team Data Science işleminde](overview.md)özetlenen adımlara bakın.

Veri araştırma görevi gibi önceden işleme ve temizleme görevleri, SQL veya Hive veya Azure Machine Learning Studio (klasik) gibi çok çeşitli ortamlarda ve verilerinizin nerede depolandığını ve nasıl biçimlendirildiğine bağlı olarak R veya Python gibi çeşitli araçlar ve dillerle gerçekleştirilebilir. TDSP, doğası içinde yinelemeli olduğundan, bu görevler işlemin iş akışındaki çeşitli adımlarda meydana getirebilir.

Bu makalede, verileri Azure Machine Learning Studio (klasik) uygulamasına geri almadan önce veya sonra alınabilecek çeşitli veri işleme kavramları ve görevleri açıklanır.

Veri keşif ve ön işleme bir örnek Azure Machine Learning Studio (klasik) içinde yapılan bir örnek için, bkz. [önceden işleme veri](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) videosu.

## <a name="why-pre-process-and-clean-data"></a>Neden önceden işlem ve temizleme verileri?
Gerçek dünya verileri çeşitli kaynaklardan ve işlemlerden toplanır ve veri kümesinin kalitesiyle ödün vermeyen veya bozuk veriler bulunabilir. Ortaya çıkan tipik veri kalitesi sorunları şunlardır:

* **Tamamlanmamış**: verilerde öznitelikler eksik veya eksik değerler içeriyor.
* **Gürültülü**: veriler hatalı kayıtlar veya aykırı değerler içeriyor.
* **Tutarsız**: veriler çakışan kayıtlar veya tutarsızlıklar içeriyor.

Kalite verileri, kalite tahmine dayalı modeller için bir önkoşuldur. "Çöp kutusu, atık çıkış" yapmak ve veri kalitesini geliştirmek ve bu nedenle performansı modellemek için veri sistem durumu ekranının, veri sorunlarını erken olarak belirlemek ve ilgili veri işleme ve Temizleme adımlarına karar vermek zorunludur.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Çalışan bazı tipik veri durum ekranları nelerdir?
Aşağıdakileri denetleyerek verilerin genel kalitesini kontrol edebilirsiniz:

* **Kayıt** sayısı.
* **Öznitelik** sayısı (veya **özellikleri**).
* Öznitelik **veri türleri** (nominal, Ordinal veya sürekli).
* **Eksik değer** sayısı.
* **İyi biçimlendirilmiş** veriler.
  * Veriler TSV veya CSV 'de ise, sütun ayırıcılarının ve çizgi ayırıcılarının her zaman sütunları ve satırları doğru şekilde ayırmadığını kontrol edin.
  * Veriler HTML veya XML biçimindeyse, verilerin ilgili standartlarına göre doğru şekilde oluşturulup oluşturulmayacağını kontrol edin.
  * Ayrıca, yarı yapılandırılmış veya yapılandırılmamış verilerden yapılandırılmış bilgileri ayıklamak için ayrıştırma de gerekebilir.
* **Tutarsız veri kayıtları**. Değer aralığına izin verildiğini denetleyin. Örneğin, veriler öğrenci GPA (sınıf noktası ortalaması) içeriyorsa, GPA 'in belirlenen aralıkta olup olmadığını kontrol edin, 0 ~ 4 deyin.

Verilerle ilgili sorunları bulduğunuzda, **işleme adımları** gereklidir; bu, genellikle eksik değerleri Temizleme, veri normalleştirme, ayırma, metin işleme ve veri hizalamasını etkileyebilecek gömülü karakterleri, ortak alanlardaki karışık veri türlerini ve diğerlerini temizleyen katıştırılmış karakterlerin değiştirilmesini içerir.

**Azure Machine Learning iyi biçimlendirilmiş tablo verileri tüketir**.  Veriler zaten tablosal formundadır, veri ön işleme Machine Learning doğrudan Azure Machine Learning Studio (klasik) ile gerçekleştirilebilir.  Veriler tablosal biçiminde değilse, XML 'de olduğunu söyleyin, verileri tablo biçimine dönüştürmek için ayrıştırma gerekebilir.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Verilerin önceden işlenmesinde önemli görevlerden bazıları nelerdir?
* **Veri temizleme**: eksik değerleri girin, gürültülü verileri ve aykırı değerleri algılayın ve kaldırın.
* **Veri dönüştürme**: boyutları ve paraziti azaltmak için verileri normalleştirin.
* **Veri azaltma**: daha kolay veri işleme için örnek veri kayıtları veya öznitelikler.
* **Veri** ayırma: belirli makine öğrenimi yöntemleriyle kullanım kolaylığı için sürekli öznitelikleri kategorik özniteliklere dönüştürün.
* **Metin Temizleme**: Örneğin, sekmeyle ayrılmış bir veri dosyasındaki katıştırılmış sekmeler, kayıtları kesintiye uğralabilen ekli yeni satırlar gibi, verilerin hizalanmasına neden olabilecek gömülü karakterleri kaldırın.

Aşağıdaki bölümler bu veri işleme adımlarından bazılarını ayrıntılandırır.

## <a name="how-to-deal-with-missing-values"></a>Eksik değerler nasıl ele alınacağını?
Eksik değerlerle başa çıkmak için, sorunu daha iyi işlemek üzere eksik değerlerin nedenini belirlemek en iyisidir. Eksik değer işleme yöntemlerinin tipik değerleri şunlardır:

* **Silme**: eksik değerlere sahip kayıtları kaldırma
* **Kukla değiştirme**: eksik değerleri bir kukla değerle değiştirin: Örneğin, kategorik için *Bilinmeyen* veya sayısal değerler için 0.
* **Ortalama değiştirme**: eksik veriler sayısal ise, eksik değerleri ortalama ile değiştirin.
* **Sık değiştirme**: eksik veriler kategorik ise, eksik değerleri en sık kullanılan öğe ile değiştirin
* **Gerileme değiştirme**: eksik değerleri, gerileyen değerlerle değiştirmek için bir gerileme yöntemi kullanın.  

## <a name="how-to-normalize-data"></a>Verileri Normalleştir?
Veri normalleştirme, sayısal değerleri belirtilen bir aralığa yeniden ayırır. Popüler veri normalleştirme yöntemleri şunları içerir:

* En **fazla normalleştirme**: verileri bir aralığa Dönüştür, 0 ile 1 arasında, en düşük değerin 0 ve en büyük değer olan 1 ' e ölçeklendirildi.
* **Z puanı normalleştirme**: verileri ortalama ve standart sapma temelinde ölçeklendirin: verileri ve ortalaması arasındaki farkı standart sapmayla bölün.
* **Ondalık ölçeklendirme**: öznitelik değerinin ondalık noktasını taşıyarak verileri ölçeklendirin.  

## <a name="how-to-discretize-data"></a>Veriler nasıl ayrılamıyor?
Veriler, sürekli değerleri nominal özniteliklere veya aralıklara dönüştürerek ayrılamıyor. Bunu yapmanın bazı yolları şunlardır:

* **Eşit genişlik bineni**: bir özniteliğin tüm olası değerlerinin aralığını aynı boyuttaki N grubuna böler ve bir bin içine düşen değerleri bin numarasıyla atayın.
* **Eşit yükseklik binı**: bir özniteliğin tüm olası değerlerinin aralığını N gruplara böler, her biri aynı sayıda örnek içerir ve sonra bir bin içine düşen değerleri bin numarasıyla atar.  

## <a name="how-to-reduce-data"></a>Verileri azaltma
Daha kolay veri işleme için veri boyutunu azaltmaya yönelik çeşitli yöntemler vardır. Veri boyutuna ve etki alanına bağlı olarak, aşağıdaki yöntemler uygulanabilir:

* **Kayıt örnekleme**: veri kayıtlarını örnekleme ve yalnızca verilerden temsili alt kümesini seçme.
* **Öznitelik örnekleme**: verilerden yalnızca en önemli özniteliklerin bir alt kümesini seçin.  
* **Toplama**: verileri gruplara bölün ve her grup için sayıları saklayın. Örneğin, son 20 yıla ait bir restoran zincirinin günlük gelir numaraları, verilerin boyutunu azaltmak için aylık gelirle toplanabilir.  

## <a name="how-to-clean-text-data"></a>Metin verileri nasıl temizlenir?
**Tablo verilerinde metin alanları** , sütunları hizalamayı ve/veya kayıt sınırlarını etkileyen karakterler içerebilir. Örneğin, sekmeyle ayrılmış bir dosyadaki katıştırılmış sekmeler sütun hizalanmasına neden olur ve katıştırılmış yeni satır karakterleri kayıt satırlarını keser. Metin yazma veya okuma sırasında metin kodlama işleme, yanlış okunamaz karakterlerin (null değerleri gibi) yanlışlıkla kullanıma sunulmasıyla ve metin ayrıştırmayı da etkileyebilecek şekilde hatalı şekilde. Doğru hizalama için metin alanlarını temizlemek ve/veya yapılandırılmamış ya da yarı yapılandırılmış metin verilerinden yapılandırılmış verileri ayıklamak için dikkatli ayrıştırma ve düzenlemenin kullanılması gerekebilir.

**Veri araştırması** , verilerin üzerinde erken bir görünüm sunar. Bu adım sırasında bazı veri sorunları ele alınamaz ve bu sorunları gidermek için ilgili yöntemler uygulanabilir.  Sorunun kaynağı ve sorunun nasıl tanıtılabileceği gibi sorular sormak önemlidir. Bu işlem Ayrıca, bunları çözmek için yapılması gereken veri işleme adımlarına karar vermenize yardımcı olur. Son kullanım durumlarını ve kişiler 'yi tanımlamak, veri işleme çabalarının önceliklerini belirlemek için de kullanılabilir.

## <a name="references"></a>Başvurular
> *Veri madenciliği: kavramlar ve teknikler*, üçüncü sürüm, Morgan Kaumann, 2011, Jiawei Han, Micheline Kakage ve Jian PEI
> 
> 

