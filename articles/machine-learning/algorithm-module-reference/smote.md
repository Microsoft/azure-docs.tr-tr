---
title: SMOTE
titleSuffix: Azure Machine Learning service
description: Fazla örnekleme kullanarak bir veri kümesindeki Low-olay örneklerinin sayısını artırmak için Azure Machine Learning hizmetinde SMOTE modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: d78e3d0d30cc44f2f30b1a856297f2c31d8f8469
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717025"
---
# <a name="smote"></a>SMOTE

Bu makalede, makine öğrenimi için kullanılan bir veri kümesindeki eksik temsil edilen durumların sayısını artırmak için Azure Machine Learning tasarımcısında (Önizleme) SMOTE modülünün nasıl kullanılacağı açıklanır. IÇGÖRÜME, yalnızca mevcut durumları çoğaltmaktan nadir durum sayısını artırmanın daha iyi bir yoludur.  

SMOTE modülünü *imdengeli*bir veri kümesine bağlayabilirsiniz. Bir veri kümesinin dengesde olmasının pek çok nedeni olabilir. Örneğin, hedeflediğiniz kategori nüfusda nadir olabilir veya verilerin toplanması zor olabilir. Genellikle, analiz etmek istediğiniz *sınıf* daha fazla temsil edildiğinde Smote kullanırsınız. 
  
Modül, özgün örnekleri içeren bir veri kümesi döndürür. Ayrıca, Belirttiğiniz yüzdeye bağlı olarak bir dizi yapay minınlık örneği de döndürür.  
  
## <a name="more-about-smote"></a>SMOTE hakkında daha fazla bilgi

Yapay Minınlık fazla örnekleme tekniği (SMOTE), veri kümenizdeki durum sayısını dengeli bir şekilde artırmak için istatistiksel bir tekniktir. Modül, giriş olarak sağladığınız mevcut minınlık çalışmalarından yeni örnekler oluşturarak işe yarar. Bu duman uygulamasının çoğunluğu büyük/ veya büyük/büyük

Yeni örnekler yalnızca mevcut minlık durumlarının kopyaları değildir. Bunun yerine, algoritma her bir hedef sınıf ve en yakın komşuları için *özellik alanının* örneklerini alır. Daha sonra algoritma, hedef durumunun özelliklerini komşular özellikleriyle birleştiren yeni örnekler oluşturur. Bu yaklaşım her sınıf için kullanılabilen özellikleri artırır ve örnekleri daha genel hale getirir.
  
DUMAN, tüm veri kümesini giriş olarak alır, ancak yalnızca azlık durumlarının yüzdesini artırır. Örneğin, yalnızca %1 ' in hedef değerine sahip olduğu (minınlık sınıfı) ve servis taleplerinin yüzde 99 ' unun B değerine sahip olduğu bir imdengeli veri kümeniz olduğunu varsayalım. Minınlık durumlarının yüzdesini önceki yüzdenin iki katına yükseltmek için, modülün özelliklerindeki **Smote yüzdesi** olarak **200** girersiniz.  
  
## <a name="examples"></a>Örnekler  

Nasıl çalıştığını görmek için küçük bir veri kümesiyle SMOTE kullanmayı denemeniz önerilir. Aşağıdaki örnek, Azure Machine Learning tasarımcısında bulunan kan bağış veri kümesini kullanır.
  
Veri kümesini bir işlem hattına ekler ve veri kümesinin çıktısında **Görselleştir** ' i seçerseniz, 748 satır veya veri kümesindeki satırın veya durumların, 570 servis taleplerinin (yüzde 76) 0 sınıfında olduğunu ve 178 Cases (%24) sınıfının 1 olduğunu görebilirsiniz. Bu sonuç, korsansız olmasa da, 1. sınıf, kanlı olan kişileri temsil ettiğinden, bu satırlar, modellemek istediğiniz *özellik alanını* içerir.
 
Durum sayısını artırmak için, aşağıdaki gibi 100 katları kullanarak **Smote yüzdesi**değerini ayarlayabilirsiniz:

||Sınıf 0|Sınıf 1|Toplamda|  
|-|-------------|-------------|-----------|  
|Özgün veri kümesi<br /><br /> ( = **Smolik yüzdesine** eşdeğerdir)|570<br /><br /> %76|178<br /><br /> 24|748|  
|**Smote yüzdesi** = **100**|570<br /><br /> %62|356<br /><br /> %38|926|  
|**Smote yüzdesi** = **200**|570<br /><br /> %52|534<br /><br /> %48|1\.104|  
|**Smote yüzdesi** = **300**|570<br /><br /> %44|712<br /><br /> %56|1\.282|  
  
> [!WARNING]
> Daha doğru modeller oluşturmak için, SMOTE kullanılarak durum sayısının artırılması garanti edilmez. Farklı yüzdeleri, farklı özellik kümelerini ve farklı sayıda komşu komşular ile ardışık düzen oluşturmayı deneyerek, servis taleplerinin modelinizi nasıl etkilediğini görün.  
  
## <a name="how-to-configure-smote"></a>SMOTE yapılandırma
  
1.  Ardışık düzene SMOTE modülünü ekleyin. Modül, **veri dönüştürme modülleri**altında, **düzenleme** kategorisinde bulunabilir.

2. Artırmak istediğiniz veri kümesini bağlayın. Yeni durumları oluşturmak için veya yalnızca belirli sütunları kullanarak ya da bazılarını dışlayarak özellik alanını belirtmek istiyorsanız, [veri kümesinde sütunları seç](select-columns-in-dataset.md) modülünü kullanın. Daha sonra, SMOTE kullanmadan önce kullanmak istediğiniz sütunları ayırabilirsiniz.
  
    Aksi takdirde, IÇNI aracılığıyla yeni durumların oluşturulması, giriş olarak sağladığınız *Tüm* sütunları temel alır. Özellik sütunlarının en az bir sütunu sayısaldır.
  
3.  Etiketi veya hedef sınıfı içeren sütunun seçili olduğundan emin olun. SMOTE yalnızca ikili Etiketler kabul eder.
  
4.  SMOTE modülü etiket sütunundaki minınlık sınıfını otomatik olarak tanımlar ve ardından minınlık sınıfı için tüm örnekleri alır. Tüm sütunlarda NaN değer bulunamaz.
  
5.  **Smote yüzdesi** seçeneğinde, çıkış veri kümesindeki minınlık durumlarının hedef yüzdesini gösteren bir tamsayı girin. Örneğin:  
  
    - **0**girersiniz. SMOTE modülü, giriş olarak belirttiğiniz veri kümesini tam olarak döndürür. Yeni bir minınlık durumu ekler. Bu veri kümesinde, sınıf oranı değişmemiştir.  
  
    - **100**girersiniz. SMOTE modülü yeni minlık durumları oluşturur. Özgün veri kümesindeki aynı sayıda minınlık durumunu ekler. SMOTE, büyük/büyük/büyük/büyük/veya her bir sınıfın servis taleplerinin oranını değiştirmediği için.  
  
    - **200**girersiniz. Modül, özgün veri kümesiyle karşılaştırıldığında minınlık durumlarının yüzdesini iki katına çıkarır. Bu *,* daha önce olduğu gibi birçok minlık durumuna iki kez sahip olmaya neden olmaz. Bunun yerine, veri kümesinin boyutu, çok sayıda büyük bir durumda kalacak şekilde artar. Minınlık durumlarının sayısı, istenen yüzde değeri ile eşleşene kadar artar.  
  
    > [!NOTE]
    > SMOTE yüzdesi için yalnızca 100 katları kullanın.

6.  SMOTE algoritmasının yeni durumlar oluştururken kullandığı özellik alanının boyutunu belirtmek için **en yakın komşu sayısı** seçeneğini kullanın. En yakın komşu bir hedef örneğine benzer bir veri satırıdır (bir durumdur). İki durum arasındaki mesafe, tüm özelliklerin ağırlıklı vektörlerini birleştirerek ölçülür.  
  
    + En yakın komşu sayısını artırarak, daha fazla durumda özellikler alırsınız.
    + En yakın komşuların sayısını düşük tutarak, özgün örnekteki gibi olan özellikleri kullanabilirsiniz.  
  
7. Aynı işlem hattının çalıştırılmasıyla aynı sonuçları aynı verilerle aynı şekilde sağlamak istiyorsanız **rastgele çekirdek** kutusunda bir değer girin. Aksi takdirde, işlem hattı dağıtıldığında modül işlemci saati değerlerini temel alarak rastgele bir çekirdek oluşturur. Rastgele bir çekirdek oluşturulması, çalıştırmalar üzerinde biraz farklı sonuçlara neden olabilir.

8. İşlem hattını çalıştırma.  
  
   Modülün çıktısı, özgün satırları içeren bir veri kümesidir ve azlık durumları içeren bir dizi eklenen satır.  

## <a name="technical-notes"></a>Teknik notlar

+ **Smote** modülünü kullanan bir modeli yayımlarken, bir Web hizmeti olarak yayınlanmadan önce, tahmine dayalı işlem hattından **Smote** 'yı kaldırın. Bunun nedeni, SMOME 'nin eğitim sırasında bir modeli geliştirmek için tasarlanmış olması, Puanlama için değil. Yayımlanan bir tahmine dayalı işlem hattı, SMOTE modülünü içeriyorsa bir hata alabilirsiniz.

+ Eksik değerleri temizleyip, IÇGÖRÜLERINIZI uygulamadan önce verileri onarmak için diğer dönüştürmeleri uygularsanız, genellikle daha iyi sonuçlar elde edebilirsiniz. 

+ Bazı araştırmacılar, SMOTE 'ın, metin sınıflandırması veya genomikler veri kümelerinde kullanılan veriler gibi yüksek boyutlu veya seyrek veriler üzerinde etkin olup olmadığını araştırmıştır. Bu raporda, bu gibi durumlarda SMOTE uygulamanın teorik ve teorik geçerliliği hakkında iyi bir Özet vardır: [Blagus ve lusa: yüksek boyutlu sınıf tarafından imlenen veriler IÇIN Smote](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ SIGTE, veri kümeniz için geçerli değilse, göz önünde bulundurmanız gereken diğer yaklaşımlar şunlardır:
  + Minınlık durumlarını fazla örnekleme veya çoğunluk örneklerinin temelini örnekleme yöntemleri.
  + Kümeleme, Bama veya Uyarlamalı yükseltme kullanarak doğrudan öğrenme yardımcı olan teknikler.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 

