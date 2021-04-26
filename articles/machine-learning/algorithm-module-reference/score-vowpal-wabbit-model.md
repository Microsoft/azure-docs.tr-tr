---
title: Vowpal Wabbit Modelini Puanlama
titleSuffix: Azure Machine Learning
description: Mevcut bir eğitimli Vowpal Wabbit modelini kullanarak bir giriş verileri kümesi için puanlar oluşturmak üzere Vowpal Wabbit model modülünü nasıl kullanacağınızı öğrenin.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 23dfee7b78f2606c54525391e1260af69a9b0779
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898404"
---
# <a name="score-vowpal-wabbit-model"></a>Vowpal Wabbit Modelini Puanlama
Bu makalede, var olan bir eğitimcal Wabbit modelini kullanarak bir giriş verileri kümesinin puanlarını oluşturmak için Azure Machine Learning tasarımcısında **Vowpal Wabbit modeli** modülünün nasıl kullanılacağı açıklanır.  

Bu modül Vowpal Wabbit Framework 'ün en son sürümünü (sürüm 8.8.1) sağlar. Bu modülü, VW sürüm 8 biçiminde kaydedilmiş eğitilen bir model kullanarak verileri öğrenmek için kullanın.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Skor Vowpal Wabbit modelini yapılandırma

1.  Denemenize **Vowpal Wabbit model** modülünü ekleyin.  
  
2.  Eğitimli bir Vowpal Wabbit modeli ekleyin ve sol taraftaki giriş bağlantı noktasına bağlayın. Aynı deneymede oluşturulan eğitilen bir modeli kullanabilir veya tasarımcı 'nın sol gezinti bölmesindeki **veri kümeleri** kategorisinde kaydedilmiş bir modeli bulabilirsiniz. Ancak, model Azure Machine Learning tasarımcısında kullanılabilir olmalıdır.  
  
    > [!NOTE]
    > Yalnızca Vowpal Wabbit 8.8.1 modelleri desteklenir; diğer algoritmalar kullanılarak eğitilen kaydedilmiş modellere bağlanamazsınız.
  
3.  Test veri kümesini ekleyin ve sağ taraftaki giriş bağlantı noktasına bağlayın. Test veri kümesi, test veri dosyasını içeren bir dizindir, test veri dosyası adını test veri dosyası **adıyla** belirtin. Test veri kümesi tek bir dosya ise, **test veri dosyasının adını** boş bırakın.

4. **VW bağımsız değişkenleri** metin kutusunda, Vowpal Wabbit yürütülebilir dosyasına geçerli bir komut satırı bağımsız değişkeni kümesi yazın.  

    Azure Machine Learning ' de desteklenmeyen ve desteklenmeyen Vowpal Wabbit bağımsız değişkenleri hakkında bilgi için bkz. [Teknik notlar](#technical-notes) bölümü.  

5.  **Test veri dosyasının adı**: giriş verilerini içeren dosyanın adını yazın. Bu bağımsız değişken yalnızca test veri kümesi bir dizin olduğunda kullanılır.

6. **Dosya türünü belirtin**: eğitim verilerinizin kullandığı biçimi gösterin. Vowpal Wabbit, bu iki giriş dosyası biçimini destekler:  

   - **VW** , Vowpal Wabbit tarafından kullanılan iç biçimi temsil eder. Ayrıntılar için bkz. [Vowpal Wabbit wiki sayfası](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
   - **Svmlight** , bazı diğer makine öğrenimi araçları tarafından kullanılan bir biçimdir. 

7. Etiketleri, puanlarla birlikte çıkış yapmak istiyorsanız, **etiketleri içeren fazladan bir sütun ekleyin**.  

   Genellikle, metin verileri işlenirken Vowpal Wabbit etiketleri gerektirmez ve yalnızca her bir veri satırının puanlarını döndürür.  

8. Ham puanları sonuçlarla birlikte çıkış yapmak istiyorsanız, **Ham puanları içeren fazladan bir sütun ekleyin** seçeneğini belirleyin.  

9. İşlem hattını gönderme.

## <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:

+ Sonuçları görselleştirmek için, [Vowpal Wabbit model modülünün puanı](score-vowpal-wabbit-model.md) ' na sağ tıklayın. Çıktı, 0 ' dan 1 ' e normalleştirilmiş bir tahmin puanı gösterir. 

+ Sonuçları değerlendirmek için, çıkış veri kümesi, model modülü gereksinimlerini değerlendir ' i karşılayan belirli puan sütun adlarını içermelidir.

  + Gerileme görevi için, değerlendirilecek veri kümesinin, puanlanmış etiketleri temsil eden adlı bir sütunu olmalıdır `Regression Scored Labels` .
  + İkili sınıflandırma görevi için, değerlendirilecek veri kümesi, `Binary Class Scored Labels` `Binary Class Scored Probabilities` sırasıyla puanlanmış etiketleri ve olasılıkların temsil ettiği iki sütuna sahip olmalıdır.
  + Birden çok sınıflandırma görevi için, değerlendirilecek veri kümesinin, puanlanmış etiketleri temsil eden adlı bir sütunu olmalıdır `Multi Class Scored Labels` .

  Vowpal Wabbit model modülünün Puanlama sonuçlarının doğrudan değerlendirilemediğini unutmayın. Değerlendirmeden önce, veri kümesinin yukarıdaki gereksinimlere göre değiştirilmesi gerekir.

##  <a name="technical-notes"></a>Teknik notlar

Bu bölümde, sık sorulan soruların uygulama ayrıntıları, ipuçları ve yanıtları yer almaktadır.

### <a name="parameters"></a>Parametreler

Vowpal Wabbit, algoritma seçme ve ayarlama için birçok komut satırı seçeneklerine sahiptir. Burada bu seçeneklerin tam bir tartışması mümkün değildir; [Vowpal Wabbit wiki sayfasını](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)görüntülemenizi öneririz.  

Aşağıdaki parametreler Azure Machine Learning Studio (klasik) içinde desteklenmez.  

-   İçinde belirtilen giriş/çıkış seçenekleri [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Bu özellikler modül tarafından zaten otomatik olarak yapılandırılmıştır.  
  
-   Ayrıca, birden çok çıkış üreten veya birden çok giriş alan herhangi bir seçeneğe izin verilmez. Bunlar *`--cbt`* , *`--lda`* ve içerir *`--wap`* .  
  
-   Yalnızca denetimli öğrenme algoritmaları desteklenir. Bu, bu seçeneklere izin vermez: *`–active`* , `--rank` , *`--search`* vb.  

Yukarıda açıklananlar dışında tüm bağımsız değişkenlere izin verilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 