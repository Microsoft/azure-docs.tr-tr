---
title: Vowpal Wabbit Modelini Eğitme
titleSuffix: Azure Machine Learning
description: Bir Vowpal Wabbit örneği kullanarak bir makine öğrenimi modeli oluşturmak için Vowpal Wabbit model modülünü nasıl kullanacağınızı öğrenin.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 70d0fc456b3697e3c74a5ec45cc936a02b77e591
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657663"
---
# <a name="train-vowpal-wabbit-model"></a>Vowpal Wabbit Modelini Eğitme
Bu makalede, Vowpal Wabbit kullanarak bir makine öğrenimi modeli oluşturmak için Azure Machine Learning tasarımcısında **Vowpal Wabbit modeli** modülünün nasıl kullanılacağı açıklanır.  

Machine Learning için Vowpal Wabbit kullanmak için, girişinizi Vowpal Wabbit gereksinimlerine göre biçimlendirin ve verileri gerekli biçimde hazırlayın. Vowpal Wabbit komut satırı bağımsız değişkenlerini belirtmek için bu modülü kullanın. 

İşlem hattı çalıştırıldığında, bir Vowpal Wabbit örneği, belirtilen verilerle birlikte deneme çalışma zamanına yüklenir. Eğitim tamamlandığında, model çalışma alanına yeniden serileştirilir. Modeli veri puanlandırmaya hemen kullanabilirsiniz. 

Mevcut bir modeli yeni verilerde artımlı olarak eğitebilmeniz için, kaydedilmiş bir modeli, **Vowpal Wabbit modeline eğitme** için **önceden eğitimli Vowpal Wabbit modeli** giriş bağlantı noktasına bağlayın ve yeni verileri diğer giriş bağlantı noktasına ekleyin.  

## <a name="what-is-vowpal-wabbit"></a>Vowpal Wabbit nedir?  

Vowpal Wabbit (VW), Yahoo! tarafından dağıtılmış bilgi işlem için geliştirilen hızlı, paralel bir makine öğrenimi çerçevesidir Araştır. Daha sonra Windows 'a ve paralel mimarilerde bilimsel bilgi işlem için John Langford (Microsoft Research) tarafından uyarlanmıştır.  

Machine Learning için önemli olan Vowpal Wabbit özellikleri, sürekli öğrenme (çevrimiçi öğrenme), boyutlılık azaltma ve etkileşimli öğrenimi içerir. Vowpal Wabbit, model verilerini belleğe sığdırmadığında bir sorun için de çözümdür.  

Vowpal Wabbit 'in birincil kullanıcıları, daha önce çerçevesini sınıflandırma, regresyon, konu modelleme veya matris oluşturma gibi makine öğrenimi görevleri için kullanmış olan veri Bilimleridir. Vowpal Wabbit için Azure sarmalayıcı, şirket içi sürüme çok benzer performans özelliklerine sahiptir, bu nedenle Vowpal Wabbit ' in güçlü özelliklerini ve yerel performansını kullanabilir ve eğitilen modeli bir çalışan hizmet olarak kolayca yayımlayabilirsiniz.  

[Özellik karma](feature-hashing.md) modülü, Vowpal Wabbit tarafından sunulan işlevleri de içerir. Bu, metin veri kümelerini karma algoritma kullanarak ikili özelliklere dönüştürmenizi sağlar.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Vowpal Wabbit modelini yapılandırma  

Bu bölümde, yeni bir modelin nasıl eğiteyapılacağı ve var olan bir modele yeni verilerin nasıl ekleneceği açıklanmaktadır.

Tasarımcıda diğer modüllerin aksine, bu modül her ikisi de modül parametrelerini belirtir ve modeli gösterir. Mevcut bir modeliniz varsa, modeli artımlı olarak eğitebilmeniz için isteğe bağlı bir giriş olarak ekleyebilirsiniz.

+ [Giriş verilerini gerekli biçimlerden birinde hazırlama](#prepare-the-input-data)
+ [Yeni bir modeli eğitme](#create-and-train-a-vowpal-wabbit-model)
+ [Var olan bir modeli artımlı olarak eğitme](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>Girdi verilerini hazırlama

Bu modülü kullanarak bir modeli eğitebilmeniz için, giriş veri kümesi desteklenen iki biçimden birindeki tek bir metin sütunundan oluşmalıdır: **Svmlight** veya **VW**. Bu, Vowpal Wabbit yalnızca metin verilerini analiz etmenin, yalnızca özellik ve değerlerin gerekli metin dosyası biçiminde hazırlanmalıdır.  

Veriler iki tür veri kümesi, dosya veri kümesi veya tablo veri kümesi tarafından okunabilir. Bu veri kümelerinin her ikisi de SVMLight veya VW biçiminde olmalıdır. Vowpal Wabbit veri biçimi, seyrek verilerle ilgilenirken boş alan kaydeden bir sütunlu biçim gerektirmeyen avantajın avantajlarından yararlanır. Bu biçim hakkında daha fazla bilgi için bkz. [Vowpal Wabbit wiki sayfası](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format).  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Vowpal Wabbit modeli oluşturma ve eğitme

1. Denemeniz için **Vowpal Wabbit model** modülünü ekleyin. 
  
2. Eğitim veri kümesini ekleyin ve **eğitim verilerine** bağlayın. Eğitim veri dosyası bir dizin ise, eğitim veri dosyasını içeren bir dizin ise eğitim veri dosyası adını **eğitim** veri dosyası adıyla belirtin. Eğitim veri kümesi tek bir dosya ise, **eğitim verileri dosyasının adını** boş bırakın.

3. **VW bağımsız değişkenleri** metin kutusunda Vowpal Wabbit yürütülebilir dosyası için komut satırı bağımsız değişkenlerini yazın.

     Örneğin, *`–l`* öğrenme hızını belirtmek için veya karma bitlerin sayısını belirtmek için ekleyebilirsiniz *`-b`* .  

     Daha fazla bilgi için bkz. [Vowpal Wabbit parametreleri](#supported-and-unsupported-parameters) bölümü.  

4. **Eğitim veri dosyasının adı**: giriş verilerini içeren dosyanın adını yazın. Bu bağımsız değişken yalnızca eğitim veri kümesi bir dizin olduğunda kullanılır.

5. **Dosya türünü belirtin**: eğitim verilerinizin kullandığı biçimi gösterin. Vowpal Wabbit, bu iki giriş dosyası biçimini destekler:  

    - **VW** , Vowpal Wabbit tarafından kullanılan iç biçimi temsil eder. Ayrıntılar için bkz. [Vowpal Wabbit wiki sayfası](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
    - **Svmlight** , bazı diğer makine öğrenimi araçları tarafından kullanılan bir biçimdir. 

6. **Çıkış okunabilir model dosyası**: modülün, okunabilir modeli çalıştırma kayıtlarına kaydetmesini istiyorsanız bu seçeneği belirleyin. Bu bağımsız değişken, `--readable_model` VW komut satırındaki parametresine karşılık gelir.  

7. **Ters çevrilmiş karma dosya**: modülün, ters karma işlevini çalıştırma kayıtlarındaki bir dosyaya kaydetmesini istiyorsanız bu seçeneği belirleyin. Bu bağımsız değişken, `--invert_hash` VW komut satırındaki parametresine karşılık gelir.  

8. İşlem hattını gönderme.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>Mevcut bir Vowpal Wabbit modelini yeniden eğitme

Vowpal Wabbit, mevcut bir modele yeni veri ekleyerek artımlı eğitimi destekler. Yeniden eğitim için mevcut bir modeli almanın iki yolu vardır:

+ Aynı işlem hattındaki başka bir **tren Vowpal Wabbit modeli** modülünün çıkışını kullanın.  
  
+ Tasarımcı 'nın sol gezinti bölmesindeki **veri kümeleri** kategorisinde kaydedilmiş bir modeli bulun ve ardışık düzene sürükleyin.  

1. İşlem hattınızı **Vowpal Wabbit model** modülünü ekleyin.  
2. Önceden eğitilen modeli, modülün **önceden eğitilen Vowpal Wabbit modeli** giriş bağlantı noktasına bağlayın.
3. Yeni eğitim verilerini modülün **eğitim verileri** giriş bağlantı noktasına bağlayın.
4. **Vowpal Wabbit modelini eğitme** parametreleri bölmesinde, yeni eğitim verilerinin biçimini ve giriş veri kümesi bir dizin ise eğitim veri dosyası adını belirtin.
5. Karşılık gelen dosyaların çalıştırma kayıtlarına kaydedilmesi gerekiyorsa **çıktıyı okunabilir model dosyası** ve **ters çevrilmiş karma dosya** seçeneklerini belirleyin.

6. İşlem hattını gönderme.  
7. Azure Machine Learning çalışma alanınızda güncelleştirilmiş modeli korumak için modülü seçin ve sağ bölmedeki **çıktılar + Günlükler** sekmesinde **veri kümesini kaydet** ' i seçin.  Yeni bir ad belirtmezseniz, güncelleştirilmiş model var olan kaydedilen modelin üzerine yazar.

## <a name="results"></a>Sonuçlar

+ Modelden puan oluşturmak için [Vowpal Wabbit modelini](score-vowpal-wabbit-model.md)kullanın.

> [!NOTE]
> Eğitilen modeli tasarımcıda dağıtmanız gerekiyorsa, **Puanlama modeli** yerine [Vowpal Wabbit modelinin puanı](score-vowpal-wabbit-model.md) , çıkarım ardışık düzeninde [Web hizmeti çıkış modülünün](web-service-input-output.md) girişine bağlı olduğundan emin olun.

## <a name="technical-notes"></a>Teknik notlar

Bu bölümde, sık sorulan soruların uygulama ayrıntıları, ipuçları ve yanıtları yer almaktadır.

### <a name="advantages-of-vowpal-wabbit"></a>Vowpal Wabbit avantajları

Vowpal Wabbit, n-gram gibi doğrusal olmayan özellikler üzerinde son derece hızlı öğrenme sağlar.  

Vowpal Wabbit, tek seferde bir modele bir kayıt olmak için Stokastik gradyan (SGD) gibi *çevrimiçi öğrenme* teknikleri kullanır. Böylece ham verileri çok hızlı bir şekilde yineler ve diğer modellerden daha hızlı bir tahmin geliştirebilir. Bu yaklaşım ayrıca tüm eğitim verilerini belleğe okumak zorunda kalmaktan kaçınır.  

Vowpal Wabbit, yalnızca metin verileri değil diğer kategorik değişkenler değil, tüm verileri karmaya dönüştürür. Karma kullanımı, regresyon ağırlıklarını, etkin Stokastik gradyan açısından kritik olan bir şekilde daha verimli hale getirir.  

###  <a name="supported-and-unsupported-parameters"></a>Desteklenen ve desteklenmeyen parametreler 

Bu bölümde Azure Machine Learning tasarımcısında Vowpal Wabbit komut satırı parametrelerine yönelik destek açıklanmaktadır. 

Genellikle, sınırlı bir bağımsız değişken kümesi desteklenir. Bağımsız değişkenlerin tüm listesi için [Vowpal Wabbit wiki sayfasını](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)kullanın.    

Aşağıdaki parametreler desteklenmez:

-   İçinde belirtilen giriş/çıkış seçenekleri [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Bu özellikler modül tarafından zaten otomatik olarak yapılandırılmıştır.  
  
-   Ayrıca, birden çok çıkış üreten veya birden çok giriş alan herhangi bir seçeneğe izin verilmez. Bunlar *`--cbt`* , *`--lda`* ve içerir *`--wap`* .  
  
-   Yalnızca denetimli öğrenme algoritmaları desteklenir. Bu nedenle, bu seçenekler desteklenmez: *`–active`* , `--rank` , *`--search`* vb. 

### <a name="restrictions"></a>Kısıtlamalar

Hizmetin hedefi Vowpal Wabbit 'in deneyimli kullanıcılarını destekleyeceği için, giriş verileri, diğer modüller tarafından kullanılan veri kümesi biçimi yerine Vowpal Wabbit yerel metin biçimi kullanılarak önceden hazırlanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
