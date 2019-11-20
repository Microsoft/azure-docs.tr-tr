---
title: Özellik kısıtlamaları
description: Azure SQL veritabanı özellik kısıtlamaları, veritabanınızdaki bilgilere erişim kazanmak için saldırganlar tarafından olabilecek özellikleri kısıtlayarak veritabanı güvenlerinizi geliştirir.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: ce10daca23299f838e4086426fa89d9cade314ea
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823440"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL veritabanı özellik kısıtlamaları

SQL Server saldırılarının yaygın bir kaynağı, veritabanı hakkında bilgi edinmek için çeşitli SQL ekleme saldırılarının biçimlerinin kullanıldığı veritabanına erişen web uygulamalarından biridir.  Uygulama kodu ideal olarak geliştirilmiştir, bu nedenle SQL ekleme için izin vermez.  Ancak, eski ve harici kod içeren büyük kod tabanlarında, biri tüm durumların sağlandığından emin olamaz, bu nedenle SQL eklentileri, korunmamıza yönelik hayatın bir olgusu olur.  Özellik kısıtlamalarının amacı, SQL ekleme işleminin başarılı olduğu durumlarda bile, bazı SQL ekleme biçimlerinin veritabanı hakkında bilgi sızıntısına engel olmaktır.

## <a name="enabling-feature-restrictions"></a>Özellik kısıtlamalarını etkinleştirme

Özellik kısıtlamalarını etkinleştirme, aşağıdaki gibi `sp_add_feature_restriction` saklı yordamı kullanılarak yapılır:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Aşağıdaki özellikler kısıtlanabilir:

| Özellik          | Açıklama |
|------------------|-------------|
| N'ErrorMessages ' | Bu, kısıtlı olduğunda, hata iletisindeki tüm Kullanıcı verileri maskelenir. Bkz. [hata Iletileri Özellik kısıtlaması](#error-messages-feature-restriction) |
| Nwaitfor '       | Kısıtlanmış olduğunda, komut herhangi bir gecikme olmadan hemen döndürülür. Bkz. [WAITFOR Özellik kısıtlaması](#waitfor-feature-restriction) |

`object_class` değeri, `object_name` bir Kullanıcı adı ya da bir rol adı olup olmadığını belirtmek için `N'User'` veya `N'Role'` olabilir.

Aşağıdaki örnek, Kullanıcı `MyUser` için tüm hata iletilerinin maskelenecek olmasına neden olur:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Özellik kısıtlamalarını devre dışı bırakma

Özellik kısıtlamalarını devre dışı bırakmak, aşağıdaki gibi `sp_drop_feature_restriction` saklı yordamı kullanılarak yapılır:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

Aşağıdaki örnek, Kullanıcı `MyUser`için hata iletisi maskelemeyi devre dışı bırakır:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Özellik kısıtlamalarını görüntüleme

`sys.sql_feature_restrictions` görünümü veritabanında geçerli olarak tanımlanmış tüm özellik kısıtlamalarını gösterir. Aşağıdaki sütunlara sahiptir:

| Sütun adı | Veri türü | Açıklama |
|-------------|-----------|-------------|
| sınıfı       | nvarchar (128) | Kısıtlamanın uygulandığı nesne sınıfı |
| object      | nvarchar (256) | Kısıtlamanın uygulandığı nesnenin adı |
| Özellik     | nvarchar (128) | Kısıtlanmış Özellik |

## <a name="feature-restrictions"></a>Özellik kısıtlamaları

### <a name="error-messages-feature-restriction"></a>Hata Iletileri Özellik kısıtlaması

Yaygın bir SQL ekleme saldırısı yöntemi, hataya neden olan kodu eklemek için kullanılır.  Bir saldırgan, hata iletisini inceleyerek sistemle ilgili bilgileri öğrenerek daha fazla hedefe daha fazla saldırı imkanı sağlayabilir.  Bu saldırı özellikle uygulamanın bir sorgunun sonuçlarını görüntülemey, ancak hata iletilerini görüntülemesi durumunda kullanışlı olabilir.

Şu biçimde bir istek olan bir Web uygulaması düşünün:

```html
http://www.contoso.com/employee.php?id=1
```

Aşağıdaki veritabanı sorgusunu yürüten:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Web uygulaması isteğine `id` parametresi olarak geçirilen değer veritabanı sorgusunda $EmpId değiştirmek üzere kopyalanırsa, bir saldırgan aşağıdaki isteği yapabilir:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

Ve aşağıdaki hata döndürülür, Böylece saldırgan veritabanının adını öğrenmesine izin verir:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Veritabanındaki uygulama kullanıcısı için hata iletileri Özellik kısıtlamasını etkinleştirdikten sonra, döndürülen hata iletisi maskelenir, böylece veritabanı hakkında hiç iç bilgi sızmaz:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Benzer şekilde, saldırgan aşağıdaki isteği yapabilir:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

Ve aşağıdaki hata döndürülür, Böylece saldırgan çalışanın maaşını öğrenmesine olanak tanır:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Hata iletileri Özellik kısıtlamasını kullanarak, veritabanı şunu döndürür:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR özelliği kısıtlaması

Gizli bir SQL ekleme, bir uygulamanın eklenen SQL veya bir hata iletisiyle bir saldırgan sunmadığında, ancak saldırgan iki koşullu dalın bulunduğu koşullu bir sorgu oluşturarak veritabanından bilgi çıkarabilir yürütülmesi için farklı bir süre alın. Yanıt süresi karşılaştırılırken saldırgan, hangi dalın yürütüldüğünü bilir ve bu nedenle sistem hakkında bilgi edinmiş olur. Bu saldırının en basit varyantı, gecikmeyi tanıtmak için `WAITFOR` ifadesini kullanmaktır.

Şu biçimde bir istek olan bir Web uygulaması düşünün:

```html
http://www.contoso.com/employee.php?id=1
```

Aşağıdaki veritabanı sorgusunu yürüten:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Web uygulaması isteklerine ID parametresi olarak geçirilen değer veritabanı sorgusundaki $EmpId değiştirmek üzere kopyalanırsa, bir saldırgan aşağıdaki isteği yapabilir:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

`sa` hesap kullanılıyorsa sorgu ek 5 saniye sürer. Veritabanında `WAITFOR` Özellik kısıtlaması devre dışıysa, `WAITFOR` deyimleri yok sayılır ve bu saldırı kullanılarak bilgiler sızdırılmaz.