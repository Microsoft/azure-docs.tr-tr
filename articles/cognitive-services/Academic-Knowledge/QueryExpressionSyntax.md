---
title: Sorgu ifadesi sözdizimi-Akademik Bilgi API
titlesuffix: Azure Cognitive Services
description: Sorgu ifadesi söz dizimini Akademik Bilgi API 'de nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 3b87e04c2d6380a0ee4157e73db0cd4057fadee1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704918"
---
# <a name="query-expression-syntax"></a>Sorgu Ifadesi söz dizimi

Bir **yorumlama** isteğine yönelik yanıtın bir sorgu ifadesi içerdiğini gördük. Kullanıcının sorgusunu yorumlayan dilbilgisinde her yorum için bir sorgu ifadesi oluşturulur. Daha sonra, varlık arama sonuçlarını almak için bir **değerlendir** isteği vermek üzere bir sorgu ifadesi kullanılabilir.

Ayrıca kendi sorgu ifadelerinizi oluşturabilir ve bunları bir **değerlendirme** isteğinde kullanabilirsiniz. Bu, kullanıcının eylemlerine yanıt olarak bir sorgu ifadesi oluşturan kendi Kullanıcı arabiriminizi oluşturuyorsanız yararlı olabilir. Bunu yapmak için sorgu ifadelerinin söz dizimini bilmeniz gerekir.  

Bir sorgu ifadesine eklenebilecek her varlık özniteliği belirli bir veri türüne ve olası sorgu işleçleri kümesine sahiptir. Varlık öznitelikleri ve her öznitelik için desteklenen işleçler kümesi varlık [özniteliklerinde](EntityAttributes.md)belirtilir. Tek değerli bir sorgu, *eşittir* işlemini desteklemek için özniteliği gerektirir. Ön ek sorgusu, *StartsWith* işlemini desteklemek için özniteliği gerektirir. Sayısal Aralık sorguları, *ısbetween* işlemini desteklemek için özniteliği gerektirir.

Bazı varlık verileri, öznitelik adında bir nokta '. ' ile gösterildiği gibi bileşik öznitelikler olarak depolanır. Örneğin, yazar/Ilişki bilgileri bileşik bir öznitelik olarak temsil edilir. 4 bileşen içerir: AuN, Auıd, AfN, AID. Bu bileşenler tek bir varlık özniteliği değeri oluşturan ayrı veri parçalarından oluşur.


**Dize özniteliği: Tek değer** (eş anlamlıya yönelik eşleşmeleri içerir)  
TI = ' görünmeyen anlam Analizi tarafından dizin oluşturma '  
Bileşik (AA. AuN = ' oya dumasıs ')

**Dize özniteliği: Tam tek değer** (yalnızca kurallı değerlerle eşleşir)  
TI = = ' görünmeyen anlam Analizi tarafından dizin oluşturma '  
Bileşik (AA. AuN = = ' çiğdem t dumasıs ')
     
**Dize özniteliği: Ön ek değeri**   
TI = ' görünmeyen Seman tarafından dizin oluşturma '...  
Bileşik (AA. AuN = ' oya du '...)

**Sayısal öznitelik: Tek değer**  
Y = 2010
 
**Sayısal öznitelik: Aralık değeri**  
Y > 2005  
Y > = 2005  
Y < 2010  
Y < = 2010  
Y =\[2010, 2012\) (yalnızca sol sınır değerini içerir: 2010, 2011)  
Y =\[2010, 2012\] (her iki sınır değerini de içerir: 2010, 2011, 2012)
 
**Sayısal öznitelik: Ön ek değeri**  
Y = ' 19 '... (19 ile başlayan herhangi bir sayısal değer) 
 
**Tarih özniteliği: Tek değer**  
D = ' 2010-02-04 '

**Tarih özniteliği: Aralık değeri**  
D > ' 2010-02-03 '  
D = [' 2010-02-03 ', ' 2010-02-05 ']

**Ve/veya sorguları:**  
Ve (Y = 1985, TI = ' siparişli elektronik sistemler ')  
OR (TI = ' siparişli elektronik sistemler ', TI = ' hata toleransı ilkeleri ve uygulama ')  
Ve (veya (Y = 1985, Y = 2008), TI = ' siparişli elektronik sistemler ')
 
**Bileşik sorgular:**  
Bileşik bir özniteliğin bileşenlerini sorgulamak için, Birleşik () işlevindeki bileşik özniteliğe başvuran sorgu ifadesinin bölümünü almanız gerekir. 

Örneğin, yazar adına göre kağıt sorgulamak için aşağıdaki sorguyu kullanın:
```
Composite(AA.AuN='mike smith')
```
<br>Yazar belirli bir kuruluşdayken belirli bir yazarın kağıtlarını sorgulamak için aşağıdaki sorguyu kullanın:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Bileşik () işlevi bileşik özniteliğin iki bölümünü birlikte bir araya bağladığında. Bu, yalnızca yazarlardan birinin Harvard iken "Mike Smith" olduğu konusunda bir kağıt edindiğimiz anlamına gelir. 

Belirli bir kuruluştan gelen (diğer) yazarlar ile ilişkilerdeki belirli bir yazarın kağıtlarını sorgulamak için aşağıdaki sorguyu kullanın:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>Bu sürümde, bileşik () bir önce ve () öncesinde tek tek bir yazara ve ilişkisine uygulandığından, yazarların birinin "Mike Smith" olduğu ve yazarların ilişkilerdeki her birinin "Harvard" olduğu tüm kağıtları alırız. Bu, önceki sorgu örneğine benzer ancak aynı şey değildir.

Genel olarak, aşağıdaki örneği göz önünde bulundurun: A ve B olmak üzere iki bileşeni olan bir bileşik öznitelik C sunuyoruz. Bir varlık, C için birden fazla değere sahip olabilir. Varlıklarımız şunlardır:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>Sorgu 
```
Composite(And(C.A=1, C.B=2))
```

<br>yalnızca c. A bileşeni 1 ve C. B bileşeni 2 olduğu için c değeri olan varlıkların eşleşmesini bulur. Bu sorguyla yalnızca E1 eşleşir.

Sorgu 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>c. A ' nin 1 olduğu ve c. B ' nin 2 olduğu c için bir değere sahip olan varlıkları eşleştirir. Hem E1 hem de E2 Bu sorguyla eşleşir.

Lütfen unutmayın:  
- Bileşik bir özniteliğin bir bölümüne bileşik () işlevi dışında başvuramaz.
- Aynı bileşik () işlevi içinde iki farklı bileşik özniteliğin bölümlerine başvurulamıyor.
- Bileşik () işlevi içindeki bir bileşik özniteliğin parçası olmayan bir özniteliğe başvurulamıyor.
