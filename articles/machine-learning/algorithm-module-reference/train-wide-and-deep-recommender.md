---
title: Eğitme geniş & derin öneren modülünü kullanın
titleSuffix: Azure Machine Learning
description: Öneri modelini eğmek için Azure Machine Learning tasarımcısında eğitme geniş & derin öneren modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/12/2020
ms.openlocfilehash: d7dd7105ddb0d6503faefb996b84c0e53a62ce49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655385"
---
# <a name="train-wide--deep-recommender"></a>Geniş kapsamlı & öneren eğitimi
Bu makalede, bir öneri modelini eğmek için Azure Machine Learning tasarımcısında **eğitme geniş & derin öneren** modülünün nasıl kullanılacağı açıklanır. Bu modül, Google tarafından önerilen geniş & derinlemesine öğrenmeyi temel alır.

**Eğitme geniş & derin öneren** modülü, Kullanıcı-öğe derecelendirmesi ve isteğe bağlı olarak, bazı Kullanıcı ve öğe özelliklerinin bir veri kümesini okur. Eğitilen geniş bir & derin öneren döndürür.  Daha sonra eğitilen modeli, [puan ve derin öneren](score-wide-and-deep-recommender.md) modülünü kullanarak derecelendirme tahminleri veya önerileri oluşturmak için kullanabilirsiniz.  

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>Öneri modelleri ve geniş & derin öneren hakkında daha fazla bilgi  

Öneri sisteminin ana amacı, sistem *kullanıcılarına* bir veya daha fazla *öğe* önermektir. Bir öğe örnekleri bir film, Restoran, kitap veya şarkı olabilir. Bir Kullanıcı bir kişi, kişi grubu veya öğe tercihleri olan başka bir varlık olabilir.  

Öneren sistemlerinde iki ana yaklaşım vardır. 

+ İlki, her iki Kullanıcı ve öğe için özellikleri kullanan **içerik tabanlı** yaklaşımdır. Kullanıcılar Age ve cinsiyet gibi özelliklerle açıklanabilir ve öğeler yazar ve üretici gibi özelliklerle açıklanabilir. İçerik tabanlı öneri sistemlerinin tipik örnekleri, sosyal eşleştirme sitelerinde bulunabilir. 
+ İkinci yaklaşım, yalnızca kullanıcıların ve öğelerin tanımlayıcılarını kullanan ve bu varlıklar hakkındaki örtük bilgileri, kullanıcılar tarafından verilen derecelendirmelerin (seyrek) matrisinden elde eden **ortak filtrelemedir**. Derecelendirdikleri öğelerden ve aynı öğeleri derecelendirdiğiniz diğer kullanıcılardan bir kullanıcı hakkında bilgi edinebilirsiniz.  

Geniş & derin öneren, bu yaklaşımları, içerik tabanlı bir yaklaşım ile işbirliğine dayalı filtreleme kullanarak birleştirir. Bu nedenle **karma öneren** olarak değerlendirilir. 

Bu nasıl çalışır: bir kullanıcının sisteme görece yeni olduğu durumlarda, kullanıcı hakkındaki Özellik bilgileri kullanılarak tahminler geliştirilmiştir ve bu sayede iyi bilinen "soğuk-başlatma" sorununu ele alınır. Bununla birlikte, belirli bir kullanıcıdan yeterli sayıda derecelendirme topladıktan sonra, kendi özellikleri yerine kendilerine özgü derecelendirmelere göre tamamen kişiselleştirilmiş tahminler yapmak mümkündür. Bu nedenle, içerik tabanlı önerilerden, işbirliğine dayalı filtreleme temelinde önerilere yönelik kesintisiz bir geçiş vardır. Kullanıcı veya öğe özellikleri kullanılabilir olmasa bile, geniş & derin öneren işbirliği filtreleme modunda çalışmaya devam edecektir.  

Geniş kapsamlı & derin öneren ve temel dayalı algoritması hakkında daha fazla ayrıntı, ilgili araştırma sayfasında bulunabilir: [öneren sistemleri Için geniş & derinlemesine öğrenme](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-train-wide--deep-recommender"></a>Eğitim genelinde & derin öneren yapılandırma  

+ [Eğitim verilerini hazırlama](#prepare-data)
+ [Modeli eğitme](#train-the-model)

### <a name="prepare-data"></a>Verileri hazırlama

Modülünü kullanmayı denemeden önce, verilerinizin öneri modeli tarafından beklenen biçimde olması önemlidir. Eğitim verileri **Kullanıcı-öğe derecelendirme Üçlü** kümesi gereklidir, ancak farklı veri kümelerinde Kullanıcı özelliklerini ve öğe özelliklerini (varsa) da dahil edebilirsiniz.

#### <a name="required-dataset-of-user-item-ratings"></a>Kullanıcı-öğe derecelendirmelerinin gerekli veri kümesi

Eğitim için kullanılan giriş verileri doğru biçimde veri türü içermelidir: 

+ İlk sütunun Kullanıcı tanımlayıcıları içermesi gerekir.
+ İkinci sütunun öğe tanımlayıcıları içermesi gerekir.
+ Üçüncü sütun, Kullanıcı öğesi çiftinin derecelendirmesini içerir. Derecelendirme değerleri sayısal türde olmalıdır. 

Örneğin, tipik bir Kullanıcı öğesi derecelendirme kümesi şöyle görünebilir:

|UserId|Movieıd|Derecelendirme|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>Kullanıcı özellikleri veri kümesi (isteğe bağlı)

**Kullanıcı özelliklerinin** veri kümesi, kullanıcılar için tanımlayıcı içermeli ve Users-Items-derecelendirmeler veri kümesinin ilk sütununda sağlanmış olan tanımlayıcıların aynısını kullanmalıdır. Kalan sütunlar, kullanıcıları tanımlayan herhangi bir sayıda özelliği içerebilir.  

Bir örnek için, tipik bir kullanıcı özellikleri kümesi şuna benzeyebilir: 

|UserId|Yaş|Cinsiyet|İlgi|Konum|
|------------|--------------|-----------------------|---------------|------------|
|1|25|erkek| Dram    |Avrupa|
|223|40|kadın|Romanya|Asya|

#### <a name="item-features-dataset-optional"></a>Öğe özellikleri veri kümesi (isteğe bağlı)

Öğe özelliklerinin veri kümesi ilk sütununda öğe tanımlayıcıları içermelidir. Kalan sütunlar, öğeler için herhangi bir sayıda açıklayıcı özellik içerebilir.  

Bir örnek için, öğe özelliklerinin tipik bir kümesi şöyle görünebilir:  

|Movieıd|Başlık|Özgün dil|Tür|Yıl|
|-------------|-------------|-------------------|-----------|---------------|
|68646|Godbaba|İngilizce|Dram|1972|
|31381|Rüzgar ile gitti|İngilizce|Geçmiş|1939|

### <a name="train-the-model"></a>Modeli eğitme

1.  Tasarımcıdaki denemenize **geniş ve derin öneren** modülünü ekleyin ve eğitim veri kümesine bağlayın.  
  
2. Kullanıcı özellikleri ve/veya öğe özelliklerinin ayrı bir veri kümeniz varsa, bunları **eğime ve derin öneren** modülüne bağlayın.  
  
    - **Kullanıcı özellikleri veri kümesi**: kullanıcıları açıklayan veri kümesini ikinci girişe bağlama.
    - **Öğe özellikleri veri kümesi**: öğeleri açıklayan veri kümesini üçüncü girişe bağlama.  
    
3.  **Dönemler**: algoritmanın tüm eğitim verilerini kaç kez işlemesi gerektiğini belirtin. 

    Bu sayı arttıkça eğitim daha uygun olur; Bununla birlikte, eğitim maliyetleri daha uzun sürebilir ve çok fazla yol açabilir.

4. **Toplu iş boyutu**: bir eğitim adımında kullanılan eğitim örneklerinin sayısını yazın. 

     Bu hiper parametre eğitim hızını etkileyebilir. Daha yüksek bir toplu iş boyutu, daha az bir süre maliyet dönemi doğurur, ancak Yakınsama süresini artırabilir. Toplu iş GPU/CPU 'ya sığmayacak kadar büyük ise, bir bellek hatası ortaya çıkabilir.

5.  **Geniş bölüm iyileştirici**: degradeleri modelin geniş bir bölümüne uygulamak için bir iyileştirici seçin.

6.  **Geniş iyileştirici öğrenme oranı**: geniş kapsamlı bölüm iyileştiricinin öğrenme oranını tanımlayan 0,0 ile 2,0 arasında bir sayı girin.

    Bu hyperparameter, en az bir kayıp işlevine hareket ederken her eğitim adımındaki adım boyutunu belirler. Çok büyük bir öğrenme hızı, çok küçük bir öğrenme oranının yakınsama sorununa neden olabileceğinden, Mini olarak öğrenmenin hızlı bir şekilde öğrenmesine neden olabilir.

7.  **Çapraz özellik boyutu**: istenen kullanıcı kimliklerini ve öğe kimliği özelliklerini girerek boyutu yazın. 

    Geniş & derin öneren, Kullanıcı kimliği ve öğe kimliği özellikleri üzerinden varsayılan olarak ürün içi dönüştürme gerçekleştirir. Çapraz sonuç, boyutun sağlamak için bu sayıya göre karma hale getirilir.

8.  **Derin bölüm iyileştirici**: degradeleri modelin derin bölümüne uygulamak için bir iyileştirici seçin.

9.  **Derin iyileştirici öğrenme oranı**: derin parçalı iyileştiricinin öğrenme oranını tanımlayan 0,0 ile 2,0 arasında bir sayı girin.

10.  **Kullanıcı ekleme boyutu**: Kullanıcı kimliği ekleme boyutunu belirtmek için bir tamsayı yazın.

     Geniş & derin öneren, hem geniş hem de ayrıntılı bölüm için paylaşılan kullanıcı kimliği ve öğe kimliği katıştırıcıyı oluşturur.

11.  **Öğe katıştırma boyutu**: öğe kimliği katıştırma boyutunu belirtmek için bir tamsayı yazın.

12.  **Kategorik özellikler ekleme boyutu**: kategorik Özellik katlamaları boyutunu belirtmek için bir tamsayı girin.

     Geniş kapsamlı & derin öneren, her kategorik özellik için bir katıştırma vektörü öğrendi. Ve bu ekleme vektörleri aynı boyutu paylaşır.

13.  **Gizli birimler**: derin bileşenin gizli düğümlerinin sayısını yazın. Her katmandaki düğüm sayısı virgülle ayrılır. Örneğin, "1.000.500.100" türüne göre derin bileşenin üç katman olduğunu belirtirsiniz ve en son katmanın ilk katmanında 1000 düğüm, 500 düğümleri ve 100 düğümleri bulunur.

14.  **Etkinleştirme işlevi**: her katmana uygulanan bir etkinleştirme işlevi seçin, varsayılan değer Relu olur.

15.  **Dropout**: eğitim sırasında her bir katmanda çıkışların hangi olasılığa bırakılacağını öğrenmek için 0,0 ile 1,0 arasında bir sayı girin.

     Dropout, sinir ağlarının fazla yer almasını önleyen bir düzenleme yöntemidir. Bu değer için yaygın olarak kullanılan bir karar, çok sayıda ağ ve görev için en iyi şekilde yakın olan 0,5 ile başlamadır.

16.  **Toplu normalleştirme**: derin bileşendeki her gizli katmandan sonra toplu normalleştirmeyi kullanmak için bu seçeneği belirleyin.

     Toplu normalleştirme, ağ eğitimi sırasında iç birlikte bulunan kaydırma sorununu ortadan kaldırmaya yönelik bir tekniktir. Genel olarak, ağların hızını, performansını ve kararlılığını artırmaya yardımcı olabilir. 

17.  İşlem hattını çalıştırma.

## <a name="results"></a>Sonuçlar

İşlem hattı çalıştırması tamamlandıktan sonra, Puanlama için modeli kullanmak üzere, yeni giriş örneklerine ilişkin değerleri tahmin etmek için [tren geniş ve derin öneren](train-wide-and-deep-recommender.md) [' ı bağlayın](score-wide-and-deep-recommender.md).

##  <a name="technical-notes"></a>Teknik notlar

Geniş kapsamlı &, anma ve genelleştirme güçlerini birleştirmek için geniş doğrusal modeller ve derin sinir ağlar Geniş bileşen, özellik etkileşimlerini yeniden denemek için ham Özellikler ve özellik dönüştürmeleri kümesini kabul eder. Daha az özellik mühendisliği sayesinde derin bileşen, düşük boyutlu yoğun Özellik birleşimleriyle görülmeyen Özellik kombinasyonlarını genelleştirir. 

Geniş kapsamlı & derin öneren uygulamasında modül varsayılan model yapısını kullanır. Geniş bileşen Kullanıcı kimliklerinin, öğe katıştırmaya ve kullanıcı kimliklerinin ve öğe kimliklerinin giriş olarak ürün içi dönüşümünü alır. Modelin derin bölümü için, her kategorik özellik için bir katıştırma vektörü öğrendi. Diğer sayısal Özellik vektörlerine sahip olan bu vektörler daha sonra derin akış-iletme sinir ağına dağıtılır. Geniş bölüm ve derin bölüm, son çıktı günlüğü Odd 'sini tahmin olarak toplayarak birleştirilir ve bu, son olarak Birleşik Eğitim için bir ortak kayıp işlevine gider.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning [kullanılabilen modül kümesine](module-reference.md) bakın. 
