---
title: Azure Service Bus abonelik kuralı SQL eylemi sözdizimi | Microsoft Docs
description: Bu makale, SQL kuralı eylem sözdizimi için bir başvuru sağlar. Eylemler, bir iletiyle gerçekleştirilen SQL dil tabanlı söz dizimine yazılır.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 75ff437bace59d7f4de07342277f0760480a5b0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652845"
---
# <a name="subscription-rule-sql-action-syntax"></a>Abonelik kuralı SQL eylemi sözdizimi

Bir ileti bir abonelik kuralı filtresi tarafından seçildikten sonra ileti meta verilerini işlemek için bir *SQL eylemi* kullanılır. Bu, SQL-92 standardının bir alt kümesini içeren bir metin deyimidir. Eylem ifadeleri `sqlExpression` , bir Azure Resource Manager şablonundaki bir Service Bus ' Action ' özelliğinin öğesi `Rule` veya Azure CLI [](service-bus-resource-manager-namespace-topic-with-rule.md) `az servicebus topic subscription rule create` komutunun [`--action-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create) bağımsız değişkeni ve ABONELIK kurallarının yönetilmesine izin veren çeşitli SDK işlevleri ile birlikte kullanılır.
  
  
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
  
## <a name="arguments"></a>Bağımsız değişkenler  
  
-   `<scope>` , kapsamını belirten isteğe bağlı bir dizedir `<property_name>` . Geçerli değerler veya ' dir `sys` `user` . `sys`Değer, `<property_name>` [aracılı edmessage sınıfının](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)ortak özellik adı olan sistem kapsamını gösterir. `user` Kullanıcı kapsamını `<property_name>` , [aracılı Edmessage sınıf](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) sözlüğünün bir anahtarı olduğunu gösterir. `user` belirtilmezse, kapsam varsayılan kapsamdır `<scope>` .  
  
### <a name="remarks"></a>Açıklamalar  

Var olmayan bir sistem özelliğine erişme girişimi bir hatadır, ancak varolmayan bir Kullanıcı özelliğine erişme girişimi bir hata değildir. Bunun yerine, var olmayan bir Kullanıcı özelliği, dahili olarak bilinmeyen bir değer olarak değerlendirilir. Bilinmeyen bir değer, işleç değerlendirmesi sırasında özel olarak değerlendirilir.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Bağımsız değişkenler  
 `<regular_identifier>` , aşağıdaki normal ifade tarafından temsil edilen bir dizedir:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Bu, bir harfle başlayan ve ardından bir veya daha fazla alt çizgi/harf/rakam gelen bir dize anlamına gelir.  
  
 `[:IsLetter:]` Unicode harfi olarak kategorilere ayrılan herhangi bir Unicode karakter anlamına gelir. `System.Char.IsLetter(c)``true` `c` bir Unicode harfse döndürür.  
  
 `[:IsDigit:]` ondalık basamak olarak kategorilere ayrılan herhangi bir Unicode karakter anlamına gelir. `System.Char.IsDigit(c)``true` `c` Unicode basamağı ise döndürür.  
  
 , `<regular_identifier>` Ayrılmış bir anahtar sözcük olamaz.  
  
 `<delimited_identifier>` sol/sağ köşeli ayraç ([]) ile çevrelenen herhangi bir dizedir. Sağ köşeli ayraç iki sağ köşeli ayraç olarak gösterilir. Aşağıdakiler aşağıda verilmiştir `<delimited_identifier>` :  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` , çift tırnak işaretleriyle çevrelenen herhangi bir dizedir. Tanımlayıcıdaki çift tırnak işareti iki çift tırnak işareti olarak temsil edilir. Bir dize sabiti ile kolayca karışabileceğinden, tırnak işareti tanımlayıcıları kullanılması önerilmez. Mümkünse sınırlı bir tanımlayıcı kullanın. Aşağıda bir örneği verilmiştir `<quoted_identifier>` :  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>kalıp  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Açıklamalar
  
 `<pattern>` dize olarak değerlendirilen bir ifade olmalıdır. LIKE işleci için bir model olarak kullanılır.      Aşağıdaki joker karakterleri içerebilir:  
  
-   `%`: Herhangi bir sıfır veya daha fazla karakter dizesi.  
  
-   `_`: Herhangi bir tek karakter.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Açıklamalar
  
 `<escape_char>` 1 uzunluğunda bir dize olarak değerlendirilen bir ifade olmalıdır. LIKE işleci için bir kaçış karakteri olarak kullanılır.  
  
 Örneğin, `property LIKE 'ABC\%' ESCAPE '\'` `ABC%` ile başlayan bir dize yerine eşleşmeler `ABC` .  
  
## <a name="constant"></a> sabiti  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Bağımsız değişkenler  
  
-   `<integer_constant>` , tırnak işaretleri içine almayan ve ondalık noktaları içermeyen sayıların bir dizesidir. Değerler dahili olarak depolanır `System.Int64` ve aynı aralığı izler.  
  
     Aşağıda, uzun sabitler örnekleri verilmiştir:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` , tırnak işaretleri içine almayan sayıların bir dizesidir ve bir ondalık noktası içerir. Değerler dahili olarak depolanır `System.Double` ve aynı aralığa/duyarlığa uyar.  
  
     Gelecekteki bir sürümde, bu numara tam sayı semantiğini desteklemek için farklı bir veri türünde depolanabilir, bu nedenle temel alınan veri türünün bir olgusuna güvenmemelisiniz `System.Double` `<decimal_constant>` .  
  
     Ondalık sabitlerin örnekleri aşağıda verilmiştir:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` bilimsel gösterimde yazılmış bir sayıdır. Değerler dahili olarak depolanır `System.Double` ve aynı aralığa/duyarlığa uyar. Aşağıda, yaklaşık sayıda Sabitte örnek verilmiştir:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Açıklamalar
  
Boole sabitleri, anahtar kelimeleri veya ile temsil edilir `TRUE` `FALSE` . Değerler olarak depolanır `System.Boolean` .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Açıklamalar
  
Dize sabitleri tek tırnak işaretleri içine alınır ve geçerli Unicode karakterleri içerir. Bir dize sabitine gömülü tek bir tırnak işareti, iki tek tırnak işareti olarak temsil edilir.  
  
## <a name="function"></a> işlevi  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Açıklamalar  

`newid()`İşlevi `System.Guid` , yöntemi tarafından oluşturulan bir döndürür `System.Guid.NewGuid()` .  
  
`property(name)`İşlevi tarafından başvurulan özelliğin değerini döndürür `name` . `name`Değer, bir dize değeri döndüren geçerli bir ifade olabilir.  

## <a name="examples"></a>Örnekler
Örnekler için bkz. [Service Bus filtresi örnekleri](service-bus-filter-examples.md).
  
## <a name="considerations"></a>Dikkat edilmesi gerekenler

- SET, yeni bir özellik oluşturmak veya var olan bir özelliğin değerini güncelleştirmek için kullanılır.
- Kaldır özelliği kaldırmak için kullanılır.
- KÜME, ifade türü ve var olan özellik türü farklı olduğunda mümkün olduğunda örtük dönüştürme gerçekleştirir.
- Mevcut olmayan sistem özelliklerine başvuruluyorsa, eylem başarısız olur.
- Mevcut olmayan kullanıcı özelliklerine başvuruluyorsa eylem başarısız olmaz.
- Var olmayan bir Kullanıcı özelliği "Bilinmeyen" olarak değerlendirilir ve işleçler değerlendirilirken [Sqlfilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) ile aynı semantiğini takip edin.

## <a name="next-steps"></a>Sonraki adımlar

- [SQLRuleAction sınıfı (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLRuleAction sınıfı (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)
- [SqlRuleAction sınıfı (Java)](/java/api/com.microsoft.azure.servicebus.rules.sqlruleaction)
- [SqlRuleAction (JavaScript)](/javascript/api/@azure/service-bus/sqlruleaction)
- [`az servicebus topic subscription rule`](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)