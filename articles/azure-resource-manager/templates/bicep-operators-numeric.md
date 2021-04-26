---
title: Bıcep sayısal işleçleri
description: Değerleri hesaplayan Bıcep sayısal işleçlerini açıklar.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: f2c7f722a3f13648c94b69039d31e5095a3256f7
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211348"
---
# <a name="bicep-numeric-operators"></a>Bıcep sayısal işleçleri

Sayısal işleçler, hesaplamalar yapmak ve tamsayı değerleri döndürmek için tamsayılar kullanır. Örnekleri çalıştırmak için, Azure CLı veya Azure PowerShell kullanarak [bir Bıcep dosyası dağıtın](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operatör | Name |
| ---- | ---- |
| `*` | [Çarp](#multiply-) |
| `/` | [Sayısına](#divide-) |
| `%` | [Mod](#modulo-) |
| `+` | [Ekle](#add-) |
| `-` | [Çıkar](#subtract--) |
| `-` | [Dakini](#minus--) |

> [!NOTE]
> Çıkart ve eksi aynı işleci kullanır. Çıkarma iki işlenen kullandığından ve eksi bir işlenen kullandığından, işlevsellik farklıdır.

## <a name="multiply-"></a>Bilirsiniz

`operand1 * operand2`

İki tamsayının çarpar.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1`  | tamsayı | Çarpılacak sayı. |
| `operand2`  | tamsayı  | Sayının çarpanı. |

### <a name="return-value"></a>Döndürülen değer

Çarpma, ürünü bir tamsayı olarak döndürür.

### <a name="example"></a>Örnek

İki tamsayılar çarpılıp çarpımı döndürülür.

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `product` | tamsayı | 10 |

## <a name="divide-"></a>Sayısına

`operand1 / operand2`

Tamsayıyı bir tamsayıya böler.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | tamsayı | Bölünen tamsayı. |
| `operand2` | tamsayı | Bölüm için kullanılan tamsayı. Sıfır olamaz. |

### <a name="return-value"></a>Döndürülen değer

Bölüm, bölümü bir tamsayı olarak döndürür.

### <a name="example"></a>Örnek

İki tamsayı bölünür ve bölümü döndürür.

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `quotient` | tamsayı | 5 |

## <a name="modulo-"></a>Modül

`operand1 % operand2`

Tamsayıyı bir tamsayıya böler ve kalanı döndürür.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1`  | tamsayı  | Bölünen tamsayı. |
| `operand2`  | tamsayı  | Bölüm için kullanılan tamsayı. 0 olamaz. |

### <a name="return-value"></a>Döndürülen değer

Geri kalan değer bir tamsayı olarak döndürülür. Bölüm bir geri kalanı oluşturmuyorsa 0 döndürülür.

### <a name="example"></a>Örnek

İki tamsayı çifti bölünür ve kalanları döndürür.

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `remainder` | tamsayı | 1 |
| `zeroRemainder` | tamsayı | 0 |

## <a name="add-"></a>Ekle +

`operand1 + operand2`

İki tamsayı ekler.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | tamsayı | Eklenecek sayı. |
| `operand2` | tamsayı | Bir sayıya eklenen sayı. |

### <a name="return-value"></a>Döndürülen değer

Toplama, toplamı bir tamsayı olarak döndürür.

### <a name="example"></a>Örnek

İki tamsayı eklenir ve toplamı döndürür.

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `sum` | tamsayı | 12 |

## <a name="subtract--"></a>Çıkarma

`operand1 - operand2`

Tamsayıyı bir tamsayıdan çıkartır.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | tamsayı | Çıkarılan daha büyük sayı. |
| `operand2` | tamsayı | Daha büyük sayıdan çıkarılan sayı. |

### <a name="return-value"></a>Döndürülen değer

Çıkarma, farkı bir tamsayı olarak döndürür.

### <a name="example"></a>Örnek

Bir tamsayı çıkarılır ve farkı döndürür.

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `difference` | tamsayı | 6 |

## <a name="minus--"></a>Dakini

`-integerValue`

Bir tamsayıyı ile çarpar `-1` .

### <a name="operand"></a>Değişkeni

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `integerValue` | tamsayı | Tamsayı çarpılarak çarpılır `-1` . |

### <a name="return-value"></a>Döndürülen değer

Bir tamsayı ile çarpılır `-1` . Pozitif bir tamsayı negatif bir tamsayı döndürür ve negatif tamsayı pozitif bir tamsayı döndürür. Değerler parantez ile sarmalanabilir.

### <a name="example"></a>Örnek

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `startedPositive` | tamsayı | -10 |
| `startedNegative` | tamsayı | 20 |

## <a name="next-steps"></a>Sonraki adımlar

- Bir bicep dosyası oluşturmak için bkz. [öğretici: ilk Azure Resource Manager Bıcep dosyası oluşturma ve dağıtma](bicep-tutorial-create-first-bicep.md).
- Bicep türü hatalarını çözme hakkında daha fazla bilgi için bkz. [bıcep Için herhangi bir işlev](template-functions-any.md).
- Bıcep ve JSON için sözdizimini karşılaştırmak için bkz. [Şablonlar IÇIN JSON ve Bıcep karşılaştırması](compare-template-syntax.md).
- Bicep ve ARM şablon işlevlerinin örnekleri için bkz. [ARM şablon işlevleri](template-functions.md).
