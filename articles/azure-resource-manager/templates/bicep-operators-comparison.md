---
title: Bıcep karşılaştırma işleçleri
description: Değerleri karşılaştıran Bıcep karşılaştırma işleçlerini açıklar.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c382ef355131d271d9f4fa4a978a807b9aac1e4f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211349"
---
# <a name="bicep-comparison-operators"></a>Bıcep karşılaştırma işleçleri

Karşılaştırma işleçleri değerleri karşılaştırır ve ya da döndürür `true` `false` . Örnekleri çalıştırmak için, Azure CLı veya Azure PowerShell kullanarak [bir Bıcep dosyası dağıtın](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operatör | Name |
| ---- | ---- |
| `>=` | [Büyüktür veya eşittir](#greater-than-or-equal-) |
| `>`  | [Büyüktür](#greater-than-) |
| `<=` | [Küçüktür veya eşittir](#less-than-or-equal-) |
| `<`  | [Küçüktür](#less-than-) |
| `==` | [Eşittir](#equals-) |
| `!=` | [Eşit değildir](#not-equal-) |
| `=~` | [Eşittir büyük/küçük harf duyarsız](#equal-case-insensitive-) |
| `!~` | [Eşit değildir büyük küçük harf duyarsız](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>Büyük veya eşittir >=

`operand1 >= operand2`

İlk değerin ikinci değere eşit veya ondan büyük olup olmadığını değerlendirir.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | tamsayı, dize | Karşılaştırmayla ilk değer. |
| `operand2` | tamsayı, dize | Karşılaştırmayla ikinci değer. |

### <a name="return-value"></a>Döndürülen değer

İlk değer ikinci değerden büyükse veya eşitse, `true` döndürülür. Aksi takdirde, `false` döndürülür.

### <a name="example"></a>Örnek

Bir çift tamsayılar ve dize çifti karşılaştırılır.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `intGtE` | boolean | true |
| `stringGtE` | boolean | true |

## <a name="greater-than-"></a>> 'den büyük

`operand1 > operand2`

İlk değerin ikinci değerden büyük olup olmadığını değerlendirir.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | tamsayı, dize | Karşılaştırmayla ilk değer. |
| `operand2` | tamsayı, dize | Karşılaştırmayla ikinci değer. |

### <a name="return-value"></a>Döndürülen değer

İlk değer ikinci değerden büyükse, `true` döndürülür. Aksi takdirde, `false` döndürülür.

### <a name="example"></a>Örnek

Bir çift tamsayılar ve dize çifti karşılaştırılır.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

Örneğin çıktısı:

**Dirseği** içindeki **e** , ilk dizeyi daha büyük yapar.

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `intGt` | boolean | true |
| `stringGt` | boolean | true |

## <a name="less-than-or-equal-"></a>Küçüktür veya eşittir <=

`operand1 <= operand2`

İlk değerin ikinci değere eşit veya ondan küçük olup olmadığını değerlendirir.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | tamsayı, dize | Karşılaştırmayla ilk değer. |
| `operand2` | tamsayı, dize | Karşılaştırmayla ikinci değer. |

### <a name="return-value"></a>Döndürülen değer

İlk değer ikinci değerden küçükse veya eşitse, `true` döndürülür. Aksi takdirde, `false` döndürülür.

### <a name="example"></a>Örnek

Bir çift tamsayılar ve dize çifti karşılaştırılır.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `intLtE` | boolean | true |
| `stringLtE` | boolean | true |

## <a name="less-than-"></a>< küçüktür

`operand1 < operand2`

İlk değerin ikinci değerden küçük olup olmadığını değerlendirir.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | tamsayı, dize | Karşılaştırmayla ilk değer. |
| `operand2` | tamsayı, dize | Karşılaştırmayla ikinci değer. |

### <a name="return-value"></a>Döndürülen değer

İlk değer ikinci değerden küçükse, `true` döndürülür. Aksi takdirde, `false` döndürülür.

### <a name="example"></a>Örnek

Bir çift tamsayılar ve dize çifti karşılaştırılır.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

Örneğin çıktısı:

Dize, `true` küçük harflerin büyük harften küçük olmasından kaynaklanır.

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `intLt` | boolean | true |
| `stringLt` | boolean | true |

## <a name="equals-"></a>Eşittir = =

`operand1 == operand2`

Değerlerin eşit olup olmadığını değerlendirir.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | String, Integer, Boolean, Array, Object | Karşılaştırmayla ilk değer. |
| `operand2` | String, Integer, Boolean, Array, Object | Karşılaştırmayla ikinci değer. |

### <a name="return-value"></a>Döndürülen değer

İşlenenler eşitse, `true` döndürülür. İşlenenler farklıysa, `false` döndürülür.

### <a name="example"></a>Örnek

Tamsayılar, dizeler ve Boole değerleri çiftleri karşılaştırılır.

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `intEqual` | boolean | true |
| `stringEqual` | boolean | true |
| `boolEqual` | boolean | true |

## <a name="not-equal-"></a>Eşit değildir! =

`operand1 != operand2`

İki **değerin eşit olmadığını** değerlendirir.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | String, Integer, Boolean, Array, Object | Karşılaştırmayla ilk değer. |
| `operand2` | String, Integer, Boolean, Array, Object | Karşılaştırmayla ikinci değer. |

### <a name="return-value"></a>Döndürülen değer

İşlenenler **eşitse,** `true` döndürülür. İşlenenler eşitse, `false` döndürülür.

### <a name="example"></a>Örnek

Tamsayılar, dizeler ve Boole değerleri çiftleri karşılaştırılır.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | true |
| `stringNotEqual` | boolean | true |
| `boolNotEqual` | boolean | true |

## <a name="equal-case-insensitive-"></a>Eşittir büyük küçük harf duyarsız = ~

`operand1 =~ operand2`

İki değerin eşit olup olmadığını anlamak için büyük/küçük harf durumunu yoksayar.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1`  | dize | Karşılaştırmayla ilk dize. |
| `operand2`  | string | Karşılaştırmayla ikinci dize. |

### <a name="return-value"></a>Döndürülen değer

Dizeler eşitse, `true` döndürülür. Aksi takdirde, `false` döndürülür.

### <a name="example"></a>Örnek

Karışık büyük harfler kullanan dizeleri karşılaştırır.

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `strEqual1` | boolean | true |
| `strEqual2` | boolean | yanlış |

## <a name="not-equal-case-insensitive-"></a>Eşit değildir büyük küçük harf duyarsız! ~

`operand1 !~ operand2`

İki **değerin eşit olup olmadığını anlamak** için büyük/küçük harf durumunu yoksayar.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | dize | Karşılaştırmayla ilk dize. |
| `operand2` | string | Karşılaştırmayla ikinci dize. |

### <a name="return-value"></a>Döndürülen değer

Dizeler **eşitse,** `true` döndürülür. Aksi takdirde, `false` döndürülür.

### <a name="example"></a>Örnek

Karışık büyük harfler kullanan dizeleri karşılaştırır.

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | true |
| `strNotEqual2` | boolean | yanlış |

## <a name="next-steps"></a>Sonraki adımlar

- Bir bicep dosyası oluşturmak için bkz. [öğretici: ilk Azure Resource Manager Bıcep dosyası oluşturma ve dağıtma](bicep-tutorial-create-first-bicep.md).
- Bicep türü hatalarını çözme hakkında daha fazla bilgi için bkz. [bıcep Için herhangi bir işlev](template-functions-any.md).
- Bıcep ve JSON için sözdizimini karşılaştırmak için bkz. [Şablonlar IÇIN JSON ve Bıcep karşılaştırması](compare-template-syntax.md).
- Bicep ve ARM şablon işlevlerinin örnekleri için bkz. [ARM şablon işlevleri](template-functions.md).
