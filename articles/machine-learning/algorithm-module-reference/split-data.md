---
title: 'Verileri bölme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Bir veri kümesini iki ayrı küme içine bölmek için Azure Machine Learning bölünmüş veri modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a4c93b12ad654e54a7f3c7ee0e75507d5cb45e90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90907827"
---
# <a name="split-data-module"></a>Veri modülünü Böl

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Bir veri kümesini iki ayrı küme içine bölmek için bölünmüş veri modülünü kullanın.

Bu modül, verileri eğitim ve test kümelerine ayırmanız gerektiğinde faydalıdır. Verilerin bölünme şeklini de özelleştirebilirsiniz. Bazı seçenekler verilerin rastgele olarak kullanımını destekler. Bazıları belirli bir veri türü veya model türü için uyarlanmıştır.

## <a name="configure-the-module"></a>Modülü yapılandırma

> [!TIP]
> Bölme modunu seçmeden önce, ihtiyacınız olan bölme türünü belirlemek için tüm seçenekleri okuyun.
> Bölme modunu değiştirirseniz, diğer tüm seçenekler sıfırlanmış olabilir.

1. Tasarımcı 'daki işlem hattınızı **bölünmüş veri** modülünü ekleyin. Bu modülü, **veri dönüştürme** altında, **örnek ve bölünmüş** kategoride bulabilirsiniz.

1. **Ayırma modu**: sahip olduğunuz veri türüne ve onu bölmek istediğiniz bir değere bağlı olarak aşağıdaki modlardan birini seçin. Her bölme modunun farklı seçenekleri vardır.

   - **Satırları Böl**: yalnızca verileri iki parçaya bölmek istiyorsanız bu seçeneği kullanın. Her bir bölünmeye yerleştirilecek veri yüzdesini belirtebilirsiniz. Varsayılan olarak, veriler bölünmüş 50/50 ' dir.

     Ayrıca, her grupta satır seçimini rastgele rasgele hale getirebilirsiniz ve bunları Strata örnekleme kullanabilirsiniz. Bağlı örnekleme sırasında, değerlerinin iki sonuç veri kümesi arasında eşit olarak olmasını istediğiniz tek bir veri sütunu seçmelisiniz.  

   - **Normal Ifade bölme**: bir değer için tek bir sütunu test ederek veri kümenizi bölmek istediğinizde bu seçeneği belirleyin.

     Örneğin, yaklaşımı analiz ediyorsanız, belirli bir ürün adının bir metin alanında bulunup bulunmadığını kontrol edebilirsiniz. Daha sonra hedef ürün adı olmayan hedef ürün adı ve satırlarıyla veri kümesini satırlara bölebilirsiniz.

   - **Göreli Ifade bölme**: bir sayı sütununa koşul uygulamak istediğinizde bu seçeneği kullanın. Sayı bir tarih/saat alanı, Yaş veya dolar miktarları içeren bir sütun veya hatta bir yüzde olabilir. Örneğin, veri kümenizi öğelerin maliyetine göre bölmek, kişileri yaş aralıklarına göre gruplamak veya bir Takvim tarihine göre ayırmak isteyebilirsiniz.

### <a name="split-rows"></a>Satırları Böl

1. Tasarımcı 'daki işlem hattınızı [bölünmüş veri](./split-data.md) modülünü ekleyin ve ayırmak istediğiniz veri kümesini bağlayın.
  
1. **Ayırma modu** Için **satırları Böl**' ü seçin. 

1. **İlk çıkış veri kümesindeki satırların kesri**: ilk (sol tarafta) çıktıya kaç satır gideceğini öğrenmek için bu seçeneği kullanın. Diğer tüm satırlar ikinci (sağ taraf) çıktıya gider.

   Oran, ilk çıkış veri kümesine gönderilen satırların yüzdesini temsil eder, bu nedenle 0 ile 1 arasında bir ondalık sayı girmeniz gerekir.
     
   Örneğin, değer olarak **0,75** girerseniz, veri kümesi 75/25 ' i bölecektir. Bu bölme bölümünde satırların yüzde 75 ' ü ilk çıkış veri kümesine gönderilir. Kalan yüzde 25 ikinci çıkış veri kümesine gönderilir.
  
1. Veri seçimini iki gruba rastgele atamak istiyorsanız **rastgele bölme** seçeneğini belirleyin. Bu, eğitim ve test veri kümeleri oluştururken tercih edilen seçenektir.

1. **Rastgele çekirdek**: kullanılacak örnek dizisini başlatmak için negatif olmayan bir tamsayı değeri girin. Bu varsayılan çekirdek, rastgele sayılar üreten tüm modüllerde kullanılır. 

   Bir çekirdek belirtme, sonuçların tekrarlanabilir olmasını sağlar. Bir bölme işleminin sonuçlarını tekrarlamanız gerekiyorsa, rastgele numara Oluşturucu için bir çekirdek belirtmeniz gerekir. Aksi takdirde, rastgele çekirdek varsayılan olarak **0** olarak ayarlanır; Bu, ilk çekirdek değer sistem saatinden elde edilen anlamına gelir. Sonuç olarak, her bölme yaptığınızda verilerin dağıtılması biraz farklı olabilir. 

1. **Ayrılmış bölme**: iki çıkış veri kümesinin *konuşmasıyla Strata sütunu* veya *Stratification Key sütunundaki* değerlerin temsili bir örneğini içermesini sağlamak için bu seçeneği **true** olarak ayarlayın. 

   Birbirine bağlı örnekleme ile, veriler her bir çıktı veri kümesinin her bir hedef değerin kabaca aynı yüzdesini almasını sağlayan şekilde bölünür. Örneğin, eğitimin ve test küistemlerinizin, sonuca veya başka bir sütuna (cinsiyet gibi) göre kabaca dengeleneceği şekilde emin olmak isteyebilirsiniz.

1. İşlem hattını gönderme.


## <a name="select-a-regular-expression"></a>Normal ifade seçin

1. Veri hattınızı [split Data](./split-data.md) modülünü ekleyin ve ayırmak istediğiniz veri kümesine giriş olarak bağlayın.  
  
1. **Ayırma modu** için **normal ifade Böl**' ü seçin.

1. **Normal ifade** kutusuna geçerli bir normal ifade girin. 
  
   Normal ifade, normal ifadeler için Python söz dizimini izlemelidir.

1. İşlem hattını gönderme.

   Sağladığınız normal ifadeye bağlı olarak, veri kümesi iki satır kümesine ayrılır: ifadeyle eşleşen değerleri olan satırlar ve kalan tüm satırlar. 

Aşağıdaki örneklerde, bir veri kümesinin **normal ifade** seçeneği kullanılarak nasıl bölüneceği gösterilmektedir. 

### <a name="single-whole-word"></a>Tek bir tam kelime 

Bu örnek, sütundaki metni içeren tüm satırları ilk veri kümesine yerleştirir `Gryphon` `Text` . Diğer satırları **bölünen verilerin** ikinci çıktısına yerleştirir.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Dizeden

Bu örnek, veri kümesinin ikinci sütunundaki herhangi bir konumda belirtilen dizeyi arar. Konum, 1 ' in dizin değeri olarak gösterilir. Eşleşme büyük/küçük harfe duyarlıdır.

```text
(\1) ^[a-f]
```

İlk sonuç veri kümesi, Dizin sütununun şu karakterlerden biriyle başladığı tüm satırları içerir: `a` , `b` ,, `c` `d` , `e` , `f` . Diğer tüm satırlar ikinci çıktıya yönlendirilir.

## <a name="select-a-relative-expression"></a>Göreli bir ifade seçin

1. Veri hattınızı [split Data](./split-data.md) modülünü ekleyin ve ayırmak istediğiniz veri kümesine giriş olarak bağlayın.
  
1. **Ayırma modu** Için **göreli ifade**' ı seçin.
  
1. **İlişkisel ifade** kutusunda, tek bir sütunda karşılaştırma işlemi gerçekleştiren bir ifade girin.

   **Sayısal sütun** için:
   - Sütun, tarih ve saat veri türleri dahil olmak üzere herhangi bir sayısal veri türü sayı içerir.
   - İfade, en fazla bir sütun adına başvurabilir.
   - `&`Ve işlemi için ve işareti karakterini kullanın. `|`Veya işlemi için kanal karakterini kullanın.
   - Aşağıdaki işleçler desteklenir: `<` ,,, `>` `<=` `>=` , `==` , `!=` .
   - Ve kullanarak işlemleri gruplayabilirsiniz `(` `)` .
   
   **Dize sütunu** için:
   - Aşağıdaki işleçler desteklenir: `==` , `!=` .

1. İşlem hattını gönderme.

   İfade, veri kümesini iki satır kümesine böler: koşula uyan değerler içeren satırlar ve kalan tüm satırlar.

Aşağıdaki örneklerde, **bölünmüş veri** modülündeki **göreli ifade** seçeneği kullanılarak bir veri kümesinin nasıl bölüneceği gösterilmektedir.  

### <a name="calendar-year"></a>Takvim yılı

Yaygın bir senaryo, bir veri kümesini yıla göre bölmektir. Aşağıdaki ifade, sütunundaki değerlerin büyük olduğu tüm satırları seçer `Year` `2010` .

```text
\"Year" > 2010
```

Tarih ifadesi, veri sütununa dahil edilen tüm tarih bölümlerinin dikkate alınmalıdır. Veri sütunundaki tarihlerin biçimi tutarlı olmalıdır. 

Örneğin, biçimini kullanan bir tarih sütununda `mmddyyyy` , ifadesi şuna benzer olmalıdır:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Sütun dizini

Aşağıdaki ifade, bir veri kümesinin ilk sütununda 30 ' a eşit veya daha küçük değerler içeren, ancak 20 ' ye eşit olmayan tüm satırları seçmek için nasıl kullanabileceğinizi gösterir.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
