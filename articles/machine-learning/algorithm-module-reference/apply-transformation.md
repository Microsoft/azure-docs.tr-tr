---
title: 'Dönüştürme uygula: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Önceden hesaplanan bir dönüşüme göre giriş veri kümesini değiştirmek için Azure Machine Learning dönüştürme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 19385870d184654d902cd40b45d4be3646c87b46
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493898"
---
# <a name="apply-transformation-module"></a>Dönüştürme modülünü Uygula

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Bir giriş veri kümesini önceden hesaplanan bir dönüşüme göre değiştirmek için bu modülü kullanın.  
  
Örneğin, **normalize verileri** modülünü kullanarak eğitim verilerinizi normalleştirmek için z puanları kullandıysanız, Puanlama aşamasında eğitim için hesaplanmış z puanı değerini de kullanmak istersiniz. Azure Machine Learning ' de, normalleştirme yöntemini bir dönüşüm olarak kaydedebilir ve sonra, Puanlama yapmadan önce giriş verilerine z puanı uygulamak için **dönüştürme uygula** ' yı kullanabilirsiniz.
  
Azure Machine Learning birçok farklı özel dönüştürme türü oluşturma ve uygulama desteği sağlar. Örneğin, dönüştürmeleri kaydetmek ve sonra yeniden kullanmak isteyebilirsiniz:  
  
- Eksik değerleri Temizleme, eksik **verileri silme** veya değiştirme
- Verileri **Normalleştir kullanarak verileri** normalleştirin
  

## <a name="how-to-use-apply-transformation"></a>Dönüştürme uygula kullanımı  
  
1. İşlem hattınızda **dönüştürme modülünü Uygula** ' yı ekleyin. Bu modülü, **puan** kategorisinde **Machine Learning**altında bulabilirsiniz. 
  
2. Giriş olarak kullanmak için mevcut bir dönüştürmeyi bulun.  Daha önce kaydedilen dönüşümler sol gezinti bölmesindeki **dönüşümler** grubunda bulunabilir.  
  
   
  
3. Dönüştürmek istediğiniz veri kümesini bağlayın. Veri kümesi, dönüştürmenin ilk tasarlandığı veri kümesiyle tam olarak aynı şemaya (sütun, sütun adı, veri türleri) sahip olmalıdır.  
  
4. Dönüştürme tanımlanırken tüm özelleştirmeler yapıldığından başka parametre ayarlanması gerekmez.  
  
5. Yeni veri kümesine bir dönüşüm uygulamak için işlem hattını çalıştırın.  

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 