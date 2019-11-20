---
title: Azure 'da SQLRuleAction söz dizimi başvurusu | Microsoft Docs
description: SQLRuleAction dilbilgisinde ayrıntılar.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: 0f9365b72da1cec81eed82756097d32b1d72ca71
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "60307487"
---
# <a name="sqlruleaction-syntax"></a>SQLRuleAction söz dizimi

Bir *Sqlruleaction* , [sqlruleaction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) sınıfının bir örneğidir ve bir [brokeredmessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)öğesine karşı gerçekleştirilen SQL dil tabanlı sözdiziminde yazılmış eylemlerin kümesini temsil eder.   
  
Bu makalede, SQL kuralı eylem dilbilgisinde ayrıntıları listelenmektedir.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Bağımsız Değişkenler  
  
-   `<scope>`, `<property_name>`kapsamını belirten isteğe bağlı bir dizedir. Geçerli değerler veya `sys` `user`' dir. Değer, [aracılı edmessage sınıfının](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)ortak özellik adı `<property_name>` olan sistem kapsamını gösterir. `sys` `user`Kullanıcı kapsamını `<property_name>` , [aracılı edmessage sınıf](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) sözlüğünün bir anahtarı olduğunu gösterir. `user`belirtilmemişse, kapsam varsayılan kapsamdır `<scope>` .  
  
### <a name="remarks"></a>Açıklamalar  

Var olmayan bir sistem özelliğine erişme girişimi bir hatadır, ancak varolmayan bir Kullanıcı özelliğine erişme girişimi bir hata değildir. Bunun yerine, var olmayan bir Kullanıcı özelliği, dahili olarak bilinmeyen bir değer olarak değerlendirilir. Bilinmeyen bir değer, işleç değerlendirmesi sırasında özel olarak değerlendirilir.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Bağımsız Değişkenler  
 `<regular_identifier>`, aşağıdaki normal ifade tarafından temsil edilen bir dizedir:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Bu, bir harfle başlayan ve ardından bir veya daha fazla alt çizgi/harf/rakam gelen bir dize anlamına gelir.  
  
 `[:IsLetter:]`Unicode harfi olarak kategorilere ayrılan herhangi bir Unicode karakter anlamına gelir. `System.Char.IsLetter(c)`bir Unicode harfse döndürür `true`. `c`  
  
 `[:IsDigit:]`ondalık basamak olarak kategorilere ayrılan herhangi bir Unicode karakter anlamına gelir. `System.Char.IsDigit(c)`Unicode `true` basamağı `c` ise döndürür.  
  
 `<regular_identifier>` , Ayrılmış bir anahtar sözcük olamaz.  
  
 `<delimited_identifier>`sol/sağ köşeli ayraç ([]) ile çevrelenen herhangi bir dizedir. Sağ köşeli ayraç iki sağ köşeli ayraç olarak gösterilir. Aşağıdakiler aşağıda `<delimited_identifier>`verilmiştir:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`, çift tırnak işaretleriyle çevrelenen herhangi bir dizedir. Tanımlayıcıdaki çift tırnak işareti iki çift tırnak işareti olarak temsil edilir. Bir dize sabiti ile kolayca karışabileceğinden, tırnak işareti tanımlayıcıları kullanılması önerilmez. Mümkünse sınırlı bir tanımlayıcı kullanın. Aşağıda bir örneği `<quoted_identifier>`verilmiştir:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>kalıp  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Açıklamalar
  
 `<pattern>`dize olarak değerlendirilen bir ifade olmalıdır. LIKE işleci için bir model olarak kullanılır.      Aşağıdaki joker karakterleri içerebilir:  
  
-   `%`:  Herhangi bir sıfır veya daha fazla karakter dizesi.  
  
-   `_`: Herhangi bir tek karakter.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Açıklamalar
  
 `<escape_char>`1 uzunluğunda bir dize olarak değerlendirilen bir ifade olmalıdır. LIKE işleci için bir kaçış karakteri olarak kullanılır.  
  
 Örneğin, `property LIKE 'ABC\%' ESCAPE '\'` ile `ABC%` başlayanbir`ABC`dize yerine eşleşmeler.  
  
## <a name="constant"></a>Sabit  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Bağımsız Değişkenler  
  
-   `<integer_constant>`, tırnak işaretleri içine alınmış ve ondalık noktaları içermeyen sayıların bir dizesidir. Değerler dahili olarak `System.Int64` depolanır ve aynı aralığı izler.  
  
     Aşağıda, uzun sabitler örnekleri verilmiştir:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`, tırnak işaretleri içine alınmış sayıların bir dizesidir ve bir ondalık noktası içerir. Değerler dahili olarak `System.Double` depolanır ve aynı aralığa/duyarlığa uyar.  
  
     Gelecekteki bir sürümde, bu numara tam sayı semantiğini desteklemek için farklı bir veri türünde depolanabilir, bu nedenle temel alınan veri türünün için `System.Double` `<decimal_constant>`olduğu olguyu temel almamalıdır.  
  
     Ondalık sabitlerin örnekleri aşağıda verilmiştir:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`bilimsel gösterimde yazılmış bir sayıdır. Değerler dahili olarak `System.Double` depolanır ve aynı aralığa/duyarlığa uyar. Aşağıda, yaklaşık sayıda Sabitte örnek verilmiştir:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Açıklamalar
  
Boole sabitleri, anahtar kelimeleri `TRUE` veya `FALSE`ile temsil edilir. Değerler olarak `System.Boolean`depolanır.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Açıklamalar
  
Dize sabitleri tek tırnak işaretleri içine alınır ve geçerli Unicode karakterleri içerir. Bir dize sabitine gömülü tek bir tırnak işareti, iki tek tırnak işareti olarak temsil edilir.  
  
## <a name="function"></a>işlev  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Açıklamalar  

İşlevi, `System.Guid.NewGuid()` yöntemi tarafından oluşturulan bir **System. Guid** döndürür. `newid()`  
  
`property(name)` İşlevi tarafından`name`başvurulan özelliğin değerini döndürür. Değer `name` , bir dize değeri döndüren geçerli bir ifade olabilir.  
  
## <a name="considerations"></a>Dikkat edilmesi gerekenler

- SET, yeni bir özellik oluşturmak veya var olan bir özelliğin değerini güncelleştirmek için kullanılır.
- Kaldır özelliği kaldırmak için kullanılır.
- KÜME, ifade türü ve var olan özellik türü farklı olduğunda mümkün olduğunda örtük dönüştürme gerçekleştirir.
- Mevcut olmayan sistem özelliklerine başvuruluyorsa, eylem başarısız olur.
- Mevcut olmayan kullanıcı özelliklerine başvuruluyorsa eylem başarısız olmaz.
- Var olmayan bir Kullanıcı özelliği "Bilinmeyen" olarak değerlendirilir ve işleçler değerlendirilirken [Sqlfilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) ile aynı semantiğini takip edin.

## <a name="next-steps"></a>Sonraki adımlar

- [SQLRuleAction sınıfı](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter sınıfı](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
