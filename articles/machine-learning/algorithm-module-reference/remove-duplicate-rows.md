---
title: 'Yinelenen satırları kaldır: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Bir veri kümesinden Potansiyel yinelemeleri kaldırmak için Azure Machine Learning yinelenen satırları kaldır modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: a0faba895c5717d061bacee2ce724c20464f8150
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497700"
---
# <a name="remove-duplicate-rows-module"></a>Yinelenen satırları kaldır modülü

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Bir veri kümesinden Potansiyel yinelemeleri kaldırmak için bu modülü kullanın.

Örneğin, verilerinizin aşağıdaki gibi göründüğünü varsayın ve hastalar için birden çok kaydı temsil eder. 

| Hatıd | Harf| Cinsiyet|Yaş|Kabul edilen|
|----|----|----|----|----|
|1|F.M.| M| 53| Oca|
|2| F.A.M.| M| 53| Oca|
|3| F.A.M.| M| 24| Oca|
|3| F.M.| M| 24| Şub|
|4| F.M.| M| 23| Şub|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NBir| |
|7| F.A.M.| M| NBir| |

Açık olarak, bu örnekte muhtemelen yinelenen verileri olan birden çok sütun vardır. Gerçekte yinelenen olup olmadıkları, verilerin bilgisine bağlıdır. 

+ Örneğin, birçok hastaların aynı ada sahip olduğunu bilirsiniz. Herhangi bir ad sütununu kullanarak Yinelenenleri ortadan kaldırmadınız ve yalnızca **ID** sütunu. Bu şekilde, hastaların aynı ada sahip olup olmamasına bakılmaksızın yalnızca yinelenen KIMLIK değerleri olan satırlar filtrelenmelidir.

+ Alternatif olarak, KIMLIK alanında yinelemelere izin vermeye karar verebilir ve ad, soyadı, yaş ve cinsiyeti gibi benzersiz kayıtları bulmak için başka bir dosya birleşimini kullanabilirsiniz.  

Bir satırın yinelenen olup olmadığına ilişkin ölçütleri ayarlamak için, tek bir sütun veya **anahtar**olarak kullanılacak bir sütun kümesi belirtirsiniz. İki satır yalnızca **Tüm** anahtar sütunlarındaki değerler eşitse yinelenen olarak değerlendirilir. Herhangi bir satırda **anahtarlar**için değer yoksa, bu değerler yinelenen satırlar olarak kabul edilmez. Örneğin, cinsiyet ve Age, yukarıdaki tabloda anahtar olarak ayarlanırsa, 6. ve 7. satır, yaş içinde eksik değere sahip olduklarından yinelenen satırlar değildir.

Modülünü çalıştırdığınızda, bir aday veri kümesi oluşturur ve belirttiğiniz sütunlar kümesi içinde tekrarsız bir satır kümesi döndürür.

> [!IMPORTANT]
> Kaynak veri kümesi değiştirilmez; Bu modül, belirttiğiniz ölçütlere göre yinelemeleri hariç tutmak üzere filtrelenmiş yeni bir veri kümesi oluşturur.

## <a name="how-to-use-remove-duplicate-rows"></a>Yinelenen satırları kaldır 'ı kullanma

1. İşlem hattınızı modüle ekleyin. **Veri dönüştürme**, **düzenleme**altında **yinelenen satırları kaldır** modülünü bulabilirsiniz.  

2. Yinelenen satırları denetlemek istediğiniz veri kümesini bağlayın.

3. **Özellikler** bölmesinde, **anahtar sütunu seçim filtresi ifadesi**altında, yinelemeleri tanımlamak için kullanılacak sütunları seçmek üzere **sütun seçiciyi Başlat**' a tıklayın.

    Bu bağlamda, **anahtar** benzersiz bir tanımlayıcı anlamına gelmez. Sütun seçiciyi kullanarak seçtiğiniz tüm sütunlar, **anahtar sütunları**olarak belirtilir. Tüm seçilmemiş sütunlar, anahtar olmayan sütunlar olarak kabul edilir. Anahtar olarak seçtiğiniz sütunların birleşimi kayıtların benzersizlik düzeyini belirler. (Birden çok equalkatlanmış birleşimler kullanan bir SQL ifadesiyle düşünün.)

    Örnekler:

    + "Kimliklerin benzersiz olduğundan emin olmak istiyorum": yalnızca KIMLIK sütununu seçin.
    + "Ad, son ad ve KIMLIK birleşiminin benzersiz olduğundan emin olmak istiyorum": tüm üç sütunu seçin.

4. Yinelemeler bulunduğunda hangi satırın dönecağını belirtmek için **ilk yinelenen satırı koru** onay kutusunu kullanın:

    + Seçilirse, ilk satır döndürülür ve diğerleri atılır. 
    + Bu seçeneğin işaretini kaldırırsanız, son yinelenen satır sonuçlarda tutulur ve diğerleri atılır. 

5. İşlem hattını çalıştırma.

6. Sonuçları gözden geçirmek için modüle sağ tıklayın, **sonuçlar veri kümesi**' ni seçin ve **Görselleştir**' e tıklayın. 

> [!TIP]
> Sonuçların anlaşılması zor olursa veya bazı sütunların dikkate alınması gerekiyorsa, [veri kümesindeki sütunları seçme](./select-columns-in-dataset.md) modülündeki sütunları kullanarak sütunu kaldırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 