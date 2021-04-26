---
title: 'Meta verileri Düzenle: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Bir veri kümesindeki sütunlarla ilişkili meta verileri değiştirmek için Azure Machine Learning meta verileri Düzenle modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/10/2020
ms.openlocfilehash: e279a7c9f6810ece482b043284f0be0719a3dafe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90908045"
---
# <a name="edit-metadata-module"></a>Meta veri modülünü Düzenle

Bu makalede Azure Machine Learning tasarımcısında bulunan bir modül açıklanmaktadır.

Bir veri kümesindeki sütunlarla ilişkili meta verileri değiştirmek için meta verileri Düzenle modülünü kullanın. Veri kümesinin değeri ve veri türü, verileri Düzenle modülü kullanıldıktan sonra değişir.

Tipik meta veri değişiklikleri şunları içerebilir:
  
+ Boole veya sayısal sütunları kategorik değerler olarak davranma.
  
+ Hangi sütunun **sınıf** etiketini içerdiğini veya sınıflandırmak ya da tahmin etmek istediğiniz değerleri içerdiğini belirtir.
  
+ Sütunları özellik olarak işaretleme.
  
+ Tarih/saat değerlerini sayısal değerlere değiştirme veya tam tersi.
  
+ Sütunları yeniden adlandırma.
  
 Genellikle bir aşağı akış modülünün gereksinimlerini karşılamak için, bir sütunun tanımını değiştirmeniz gereken her zaman meta verileri Düzenle ' yi kullanın. Örneğin, bazı modüller yalnızca belirli veri türleriyle çalışır veya sütunlarında, veya gibi bayraklar gerektirir `IsFeature` `IsCategorical` .  
  
 Gerekli işlemi gerçekleştirdikten sonra, meta verileri özgün durumuna sıfırlayabilirsiniz.
  
## <a name="configure-edit-metadata"></a>Meta verileri düzenleme yapılandırma
  
1. Azure Machine Learning Tasarımcısı ' nda, meta veri düzenleme modülünü işlem hattınızla ekleyin ve güncelleştirmek istediğiniz veri kümesini bağlayın. Modülü **veri dönüştürme** kategorisinde bulabilirsiniz.
  
1. Modülün sağ panelindeki **sütunu Düzenle** ' ye tıklayın ve ile çalışmak için sütun veya sütun kümesi seçin. Sütunları ada veya dizine göre tek tek seçebilir veya bir sütun grubunu türüne göre seçebilirsiniz.  
  
1. Seçili sütunlara farklı bir veri türü atamanız gerekiyorsa **veri türü** seçeneğini belirleyin. Belirli işlemler için veri türünü değiştirmeniz gerekebilir. Örneğin, kaynak veri kümeniz metin olarak işlenen sayılar içeriyorsa, matematik işlemlerini kullanmadan önce bunları sayısal bir veri türü olarak değiştirmeniz gerekir.

    + Desteklenen veri türleri **String**, **Integer**, **Double**, **Boolean** ve **DateTime**.

    + Birden çok sütun seçerseniz, meta veri değişikliklerini seçili *Tüm* sütunlara uygulamanız gerekir. Örneğin, iki veya üç sayısal sütun seçtiğinizi varsayalım. Tümünü bir dize veri türü olarak değiştirebilir ve bunları tek bir işlemde yeniden adlandırabilirsiniz. Ancak, bir sütunu dize veri türüne ve kayan bir sayının başka bir sütununu tamsayı olarak değiştiremezsiniz.
  
    + Yeni bir veri türü belirtmezseniz, sütun meta verileri değiştirilmez.

    + Meta verileri düzenleme işlemini gerçekleştirdikten sonra sütun türü ve değerler değişecektir. Sütun veri türünü sıfırlamak için meta verileri Düzenle ' i kullanarak, özgün veri türünü dilediğiniz zaman kurtarabilirsiniz.  

    > [!NOTE]
    > **DateTime biçimi** [Python yerleşik tarih saat biçimini](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior)izler.  
    > Herhangi bir sayı türünü **DateTime** türüne değiştirirseniz, **DateTime biçim** alanını boş bırakın. Şu anda hedef veri biçimini belirtmek mümkün değildir.

1. Seçili olan sütunlardaki değerlerin kategori olarak değerlendirilip değerlendirilmeyeceğini belirtmek için **kategorik** seçeneğini belirleyin.

    Örneğin, 0, 1 ve 2 sayılarını içeren bir sütununuz olabilir, ancak sayıların gerçekten "Smoker", "Smomeyen" ve "bilinmiyor" anlamına geldiğini bilirsiniz. Bu durumda, sütuna kategorik olarak bayrak koyarak, değerlerin yalnızca verileri gruplandırmak için kullanılmasını ve sayısal hesaplamalarda olmamasını sağlayabilirsiniz.
  
1. Azure Machine Learning bir modeldeki verileri kullanma biçimini değiştirmek istiyorsanız **Fields** seçeneğini kullanın.

    + **Özellik**: bir sütunu yalnızca özellik sütunlarında çalışan modüllerde bir özellik olarak işaretlemek için bu seçeneği kullanın. Varsayılan olarak, tüm sütunlar başlangıçta özellik olarak değerlendirilir.  
  
    + **Etiket**: Bu seçeneği, tahmin edilebilir öznitelik veya hedef değişken olarak da bilinen etiketi işaretlemek için kullanın. Birçok modül, veri kümesinde tam olarak bir etiket sütununun bulunmasını gerektirir.

        Çoğu durumda, Azure Machine Learning bir sütunun sınıf etiketi içerdiğini çıkarsçıkarabilir. Bu meta verileri ayarlayarak, sütunun doğru şekilde tanımlanmasını sağlayabilirsiniz. Bu seçeneğin ayarlanması veri değerlerini değiştirmez. Yalnızca bazı makine öğrenimi algoritmalarının verileri işleme biçimi değişir.
  
    > [!TIP]
    > Bu kategorilere sığmayan verileriniz var mı? Örneğin, veri kümeniz değişken olarak faydalı olmayan benzersiz tanımlayıcılar gibi değerler içerebilir. Bazen bu kimlikler bir modelde kullanıldığında sorunlara neden olabilir.
    >
    > Neyse ki, bu tür sütunları veri kümesinden silmeniz gerekmiyorsa, tüm verilerinizi Azure Machine Learning. Özel bir sütun kümesinde işlem gerçekleştirmeniz gerektiğinde, tüm diğer sütunları geçici olarak [veri kümesindeki sütunları seç](select-columns-in-dataset.md) modülünde kullanarak kaldırmanız yeterlidir. Daha sonra sütunları [Ekle](add-columns.md) modülünü kullanarak sütunları veri kümesine geri birleştirebilirsiniz.  
  
1. Önceki seçimleri temizlemek ve meta verileri varsayılan değerlere geri yüklemek için aşağıdaki seçenekleri kullanın.  
  
    + **Özelliği temizle**: Özellik bayrağını kaldırmak için bu seçeneği kullanın.  
  
         Tüm sütunlar başlangıçta özellik olarak değerlendirilir. Matematik işlemleri gerçekleştiren modüller için, sayısal sütunların değişken olarak işlenmesine engel olmak için bu seçeneği kullanmanız gerekebilir.
  
    + **Etiketi temizle**: belirtilen sütundan **etiket** meta verilerini kaldırmak için bu seçeneği kullanın.  
  
    + **Puanı temizle**: belirtilen sütundan **puan** meta verilerini kaldırmak için bu seçeneği kullanın.  
  
         Şu anda bir sütunu Azure Machine Learning puan olarak açıkça işaretleyemezsiniz. Ancak, bazı işlemler dahili olarak bir puan olarak işaretlenmekte olan bir sütuna neden olacak. Ayrıca, özel bir R modülü çıkış puanı değerlerini de alabilir.

1. **Yeni sütun adları** için, seçilen sütun veya sütunların yeni adını girin.  
  
    + Sütun adları yalnızca UTF-8 kodlaması tarafından desteklenen karakterleri kullanabilir. Boş dizeler, null değerlere veya tamamen boşluklardan oluşan adlara izin verilmez.  
  
    + Birden çok sütunu yeniden adlandırmak için, adları sütun dizinleri sırasıyla virgülle ayrılmış bir liste olarak girin.  
  
    + Tüm seçili sütunlar yeniden adlandırılması gerekir. Sütunları atlayamaz veya atlayamazsınız.  
  
1. İşlem hattını gönderme.  

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.
