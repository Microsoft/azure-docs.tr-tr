---
title: Bıcep işleçleri
description: Azure Resource Manager dağıtımları için kullanılabilen Bıcep işleçlerini açıklar.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 0838ebf6bc03f4237ef76e07f1eb6f25aa996fc0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537825"
---
# <a name="bicep-operators"></a>Bıcep işleçleri

Bu makalede, bir Bıcep şablonu oluşturduğunuzda ve kaynakları dağıtmak için Azure Resource Manager kullandığınızda kullanılabilen Bıcep işleçleri açıklanmaktadır. İşleçler değerleri hesaplamak, değerleri karşılaştırmak veya koşulları değerlendirmek için kullanılır. Üç tür bicep işleci vardır:

- [Il](#comparison)
- [mantıksal](#logical)
- [rakamlardan](#numeric)

Ve arasında bir ifadeyi `(` kapsayan `)` varsayılan bicep işleç önceliğini geçersiz kılmanızı sağlar. Örneğin, x + y/z ifadesi önce bölmeyi ve sonra eklemeyi değerlendirir. Ancak, ifade (x + y)/z, ilk ve bölme ikincisini değerlendirir.

## <a name="comparison"></a>Karşılaştırma

Karşılaştırma işleçleri değerleri karşılaştırır ve ya da döndürür `true` `false` .

| Operatör | Ad | Açıklama |
| ---- | ---- | ---- |
| `>=` | [Büyüktür veya eşittir](bicep-operators-comparison.md#greater-than-or-equal-) | İlk değerin ikinci değere eşit veya ondan büyük olup olmadığını değerlendirir. |
| `>`  | [Büyüktür](bicep-operators-comparison.md#greater-than-) | İlk değerin ikinci değerden büyük olup olmadığını değerlendirir. |
| `<=` | [Küçüktür veya eşittir](bicep-operators-comparison.md#less-than-or-equal-) | İlk değerin ikinci değere eşit veya ondan küçük olup olmadığını değerlendirir. |
| `<`  | [Küçüktür](bicep-operators-comparison.md#less-than-) | İlk değerin ikinci değerden küçük olup olmadığını değerlendirir. |
| `==` | [Eşittir](bicep-operators-comparison.md#equals-) | İki değerin eşit olup olmadığını değerlendirir. |
| `!=` | [Eşit değildir](bicep-operators-comparison.md#not-equal-) | İki **değerin eşit olmadığını** değerlendirir. |
| `=~` | [Eşittir büyük/küçük harf duyarsız](bicep-operators-comparison.md#equal-case-insensitive-) | İki değerin eşit olup olmadığını anlamak için büyük/küçük harf durumunu yoksayar. |
| `!~` | [Eşit değildir büyük küçük harf duyarsız](bicep-operators-comparison.md#not-equal-case-insensitive-) | İki **değerin eşit olup olmadığını anlamak** için büyük/küçük harf durumunu yoksayar. |

## <a name="logical"></a>Mantıksal

Mantıksal işleçler Boole değerlerini değerlendirir, null olmayan değerler döndürür veya koşullu bir ifadeyi değerlendirir.

| Operatör | Ad | Açıklama |
| ---- | ---- | ---- |
| `&&` | ['](bicep-operators-logical.md#and-) | `true`Tüm değerler true ise döndürür. |
| `||`| [Veya](bicep-operators-logical.md#or-) | `true`Her iki değer de true ise döndürür. |
| `!` | [Başlatılmadı](bicep-operators-logical.md#not-) | Boole değerini geçersiz kılar. |
| `??` | [Coalesce](bicep-operators-logical.md#coalesce-) | Null olmayan ilk değeri döndürür. |
| `?` `:` | [Koşullu ifade](bicep-operators-logical.md#conditional-expression--) | Doğru veya yanlış için bir koşulu değerlendirir ve bir değer döndürür. |

## <a name="numeric"></a>Sayısal

Sayısal işleçler, hesaplamalar yapmak ve tamsayı değerleri döndürmek için tamsayılar kullanır.

| Operatör | Ad | Açıklama |
| ---- | ---- | ---- |
| `*` | [Çarp](bicep-operators-numeric.md#multiply-) | İki tamsayının çarpar. |
| `/` | [Sayısına](bicep-operators-numeric.md#divide-) | Tamsayıyı bir tamsayıya böler. |
| `%` | [Mod](bicep-operators-numeric.md#modulo-) | Tamsayıyı bir tamsayıya böler ve kalanı döndürür. |
| `+` | [Ekle](bicep-operators-numeric.md#add-) | İki tamsayı ekler. |
| `-` | [Çıkar](bicep-operators-numeric.md#subtract--) | Tamsayıyı bir tamsayıdan çıkartır. |
| `-` | [Dakini](bicep-operators-numeric.md#minus--) | Bir tamsayıyı ile çarpar `-1` . |

> [!NOTE]
> Çıkart ve eksi aynı işleci kullanır. Çıkarma iki işlenen kullandığından ve eksi bir işlenen kullandığından, işlevsellik farklıdır.

## <a name="next-steps"></a>Sonraki adımlar

- Bir bicep dosyası oluşturmak için bkz. [öğretici: ilk Azure Resource Manager Bıcep dosyası oluşturma ve dağıtma](bicep-tutorial-create-first-bicep.md).
- Bicep türü hatalarını çözme hakkında daha fazla bilgi için bkz. [bıcep Için herhangi bir işlev](template-functions-any.md).
- Bıcep ve JSON için sözdizimini karşılaştırmak için bkz. [Şablonlar IÇIN JSON ve Bıcep karşılaştırması](compare-template-syntax.md).
- Bicep ve ARM şablon işlevlerinin örnekleri için bkz. [ARM şablon işlevleri](template-functions.md).
