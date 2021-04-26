---
title: Bıcep mantıksal işleçleri
description: Koşulları değerlendiren Bıcep mantıksal işleçlerini açıklar.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 1eecbe589ecf08e32246dd97d137c60fa7b5078f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211353"
---
# <a name="bicep-logical-operators"></a>Bıcep mantıksal işleçleri

Mantıksal işleçler Boole değerlerini değerlendirir, null olmayan değerler döndürür veya koşullu bir ifadeyi değerlendirir. Örnekleri çalıştırmak için, Azure CLı veya Azure PowerShell kullanarak [bir Bıcep dosyası dağıtın](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operatör | Name |
| ---- | ---- |
| `&&` | ['](#and-) |
| `||` | [Veya](#or-) |
| `!` | [Başlatılmadı](#not-) |
| `??` | [Coalesce](#coalesce-) |
| `?` `:` | [Koşullu ifade](#conditional-expression--) |

## <a name="and-"></a>Ve &&

`operand1 && operand2`

Her iki değerin de doğru olup olmadığını belirler.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | Doğru olup olmadığını kontrol etmek için ilk değer. |
| `operand2` | boolean | Doğru olup olmadığını denetlemek için ikinci değer. |
| Daha fazla işlenen | boolean | Daha fazla işlenen dahil edilebilir. |

### <a name="return-value"></a>Döndürülen değer

`True` Her iki değer de true olduğunda, aksi takdirde `false` döndürülür.

### <a name="example"></a>Örnek

Bir parametre değerleri kümesini ve bir dizi ifadeyi değerlendirir.

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `andResultParm` | boolean | true |
| `andResultExp` | boolean | true |

## <a name="or-"></a>Or | |

`operand1 || operand2`

Değerin doğru olup olmadığını belirler.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | Doğru olup olmadığını kontrol etmek için ilk değer. |
| `operand2` | boolean | Doğru olup olmadığını denetlemek için ikinci değer. |
| Daha fazla işlenen | boolean | Daha fazla işlenen dahil edilebilir. |

### <a name="return-value"></a>Döndürülen değer

`True` Her iki değer de true olduğunda, aksi takdirde `false` döndürülür.

### <a name="example"></a>Örnek

Bir parametre değerleri kümesini ve bir dizi ifadeyi değerlendirir.

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `orResultParm` | boolean | true |
| `orResultExp` | boolean | true |

## <a name="not-"></a>Başlatılmadı!

`!boolValue`

Boole değerini geçersiz kılar.

### <a name="operand"></a>Değişkeni

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `boolValue` | boolean | Değillenmiş Boolean değeri. |

### <a name="return-value"></a>Döndürülen değer

Başlangıç değerini geçersiz kılar ve bir Boole değeri döndürür. İlk değer ise `true` , `false` döndürülür.

### <a name="example"></a>Örnek

`not`İşleci bir değeri geçersiz kılar. Değerler parantez ile sarmalanabilir.

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `startedTrue` | boolean | yanlış |
| `startedFalse` | boolean | true |

## <a name="coalesce-"></a>Mi??

`operand1 ?? operand2`

İşlenenlerden null olmayan ilk değeri döndürür.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `operand1` | String, Integer, Boolean, Object, Array | Test edilecek değer `null` . |
| `operand2` | String, Integer, Boolean, Object, Array | Test edilecek değer `null` . |
| Daha fazla işlenen | String, Integer, Boolean, Object, Array | Test edilecek değer `null` . |

### <a name="return-value"></a>Döndürülen değer

Null olmayan ilk değeri döndürür. Boş dizeler, boş diziler ve boş nesneler değildir `null` ve bir \<empty> değer döndürülür.

### <a name="example"></a>Örnek

Output deyimleri null olmayan değerler döndürür. Çıkış türü, karşılaştırma içindeki türle eşleşmelidir veya bir hata oluşturulur.

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `nonNullStr` | string | demoString |
| `nonNullInt` | int | 10 |
| `nonNullEmpty` | string | \<empty> |

## <a name="conditional-expression--"></a>Koşullu ifade? :

`condition ? true-value : false-value`

Bir koşulu değerlendirir ve koşulun doğru veya yanlış olduğunu bir değer döndürür.

### <a name="operands"></a>İşlenenleri

| Değişkeni | Tür | Description |
| ---- | ---- | ---- |
| `condition` | boolean | Doğru veya yanlış olarak değerlendirilecek koşul. |
| `true-value` | String, Integer, Boolean, Object, Array | Koşul doğru olduğunda değer. |
| `false-value` | String, Integer, Boolean, Object, Array | Koşul false olduğunda değer. |

### <a name="example"></a>Örnek

Bu örnek, bir parametrenin ilk değerini değerlendirir ve koşulun true veya false olup olmadığını döndürür.

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

Örneğin çıktısı:

| Ad | Tür | Değer |
| ---- | ---- | ---- |
| `outValue` | string | doğru değer |

## <a name="next-steps"></a>Sonraki adımlar

- Bir bicep dosyası oluşturmak için bkz. [öğretici: ilk Azure Resource Manager Bıcep dosyası oluşturma ve dağıtma](bicep-tutorial-create-first-bicep.md).
- Bicep türü hatalarını çözme hakkında daha fazla bilgi için bkz. [bıcep Için herhangi bir işlev](template-functions-any.md).
- Bıcep ve JSON için sözdizimini karşılaştırmak için bkz. [Şablonlar IÇIN JSON ve Bıcep karşılaştırması](compare-template-syntax.md).
- Bicep ve ARM şablon işlevlerinin örnekleri için bkz. [ARM şablon işlevleri](template-functions.md).
