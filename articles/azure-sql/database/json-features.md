---
title: JSON verileriyle çalışma
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği, JavaScript Nesne Gösterimi (JSON) gösteriminde verileri ayrıştırmanıza, sorgulamanızı ve formatlamanızı sağlar.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 4258c60335a13a6c0f27588aac1c6cdb185fcf6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92782817"
---
# <a name="getting-started-with-json-features-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde JSON özellikleriyle çalışmaya başlama
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve Azure SQL yönetilen örneği, JavaScript Nesne Gösterimi [(JSON)](https://www.json.org/) biçiminde temsil edilen verileri ayrıştırmanıza ve sorgulamanızı ve ILIŞKISEL verilerinizi JSON metni olarak dışarı aktarmayı sağlar. Aşağıdaki JSON senaryoları kullanılabilir:

- Yan tümcesini kullanarak [JSON biçimindeki ilişkisel verileri biçimlendirme](#formatting-relational-data-in-json-format) `FOR JSON` .
- [JSON verileriyle çalışma](#working-with-json-data)
- JSON skalar işlevleri kullanılarak [JSON verileri sorgulanıyor](#querying-json-data) .
- İşlevi kullanarak [JSON tablo biçimine dönüştürülüyor](#transforming-json-into-tabular-format) `OPENJSON` .

## <a name="formatting-relational-data-in-json-format"></a>JSON biçimindeki ilişkisel verileri biçimlendirme

Veritabanı katmanından veri alan bir Web hizmetiniz varsa ve JSON biçiminde bir yanıt veya JSON biçiminde biçimlendirilen verileri kabul eden bir istemci tarafı JavaScript çerçeveleri veya kitaplıklarını içeriyorsa, veritabanı içeriğinizi doğrudan bir SQL sorgusunda JSON olarak biçimlendirebilirsiniz. Artık Azure SQL veritabanı veya Azure SQL yönetilen örneği 'nden gelen sonuçları JSON olarak biçimlendiren uygulama kodu yazmanız gerekmez veya tablo sorgu sonuçlarını dönüştürmek ve ardından nesneleri JSON biçimine seri hale getirmek için bazı JSON serileştirme kitaplığı ekleyebilirsiniz. Bunun yerine, FOR JSON yan tümcesini kullanarak SQL sorgu sonuçlarını JSON olarak biçimlendirebilir ve doğrudan uygulamanızda kullanabilirsiniz.

Aşağıdaki örnekte Sales. Customer tablosundaki satırlar FOR JSON yan tümcesi kullanılarak JSON olarak biçimlendirilir:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

FOR JSON PATH yan tümcesi sorgunun sonuçlarını JSON metni olarak biçimlendirir. Sütun adları anahtar olarak kullanılır, ancak hücre değerleri JSON değerleri olarak oluşturulur:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Sonuç kümesi, her satırın ayrı bir JSON nesnesi olarak biçimlendirildiği bir JSON dizisi olarak biçimlendirilir.

YOL, sütun diğer adları ' nda nokta gösterimini kullanarak JSON sonuçlarınızın çıkış biçimini özelleştirebileceğinizi gösterir. Aşağıdaki sorgu, çıkış JSON biçimindeki "CustomerName" anahtarının adını değiştirir ve telefon ve faks numaralarını "Iletişim" alt nesnesine koyar:

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Bu sorgunun çıktısı şöyle görünür:

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Bu örnekte, [WITHOUT_ARRAY_WRAPPER](/sql/relational-databases/json/remove-square-brackets-from-json-without-array-wrapper-option) seçeneğini belirterek dizi yerine tek bir JSON nesnesi döndürüyoruz. Sorgu sonucu olarak tek bir nesne döndürdüğünü biliyorsanız, bu seçeneği kullanabilirsiniz.

FOR JSON yan tümcesinin ana değeri, veritabanınızdaki iç içe geçmiş JSON nesneleri veya diziler olarak biçimlendirilen karmaşık hiyerarşik verileri döndürmenize olanak tanır. Aşağıdaki örnek `Orders` , `Customer` öğesinin iç içe bir dizisi olarak öğesine ait olan tablodaki satırların nasıl ekleneceğini gösterir `Orders` :

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

Müşteri verilerini almak için ayrı sorgular göndermek ve sonra ilgili siparişlerin listesini getirmek yerine, aşağıdaki örnek çıktıda gösterildiği gibi, tek bir sorgu ile tüm gerekli verileri alabilirsiniz:

```json
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
  ]
}
```

## <a name="working-with-json-data"></a>JSON verileriyle çalışma

Kesin olarak yapılandırılmış verileriniz yoksa karmaşık alt nesneler, diziler veya hiyerarşik verileriniz varsa veya veri yapılarınız zaman içinde geliştikçe JSON biçimi herhangi bir karmaşık veri yapısını temsil etmenize yardımcı olabilir.

JSON, Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde herhangi bir dize türü gibi kullanılabilen bir metin biçimidir. JSON verilerini standart bir NVARCHAR olarak gönderebilir veya kaydedebilirsiniz:

```sql
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Bu örnekte kullanılan JSON verileri NVARCHAR (MAX) türü kullanılarak temsil edilir. JSON bu tabloya eklenebilir veya aşağıdaki örnekte gösterildiği gibi standart Transact-SQL sözdizimi kullanılarak saklı yordamın bir bağımsız değişkeni olarak sağlanmış olabilir:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Azure SQL veritabanı ve Azure SQL yönetilen örneğindeki dize verileriyle çalışan tüm istemci tarafı dilleri veya kitaplıkları JSON verileriyle de çalışacaktır. JSON, bellek için iyileştirilmiş tablo veya sistem sürümü tutulan tablo gibi NVARCHAR türünü destekleyen herhangi bir tabloda depolanabilir. JSON, istemci tarafı kodda ya da veritabanı katmanında herhangi bir kısıtlama sunmaz.

## <a name="querying-json-data"></a>JSON verilerini sorgulama

Azure SQL tablolarında depolanan JSON olarak biçimlendirilen veriler varsa, JSON işlevleri bu verileri herhangi bir SQL sorgusunda kullanmanıza olanak sağlar.

Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde kullanılabilen JSON işlevleri, JSON olarak biçimlendirilen verileri diğer SQL veri türleri gibi değerlendirmenizi sağlar. JSON metinden değerleri kolayca ayıklayabilir ve herhangi bir sorgudaki JSON verilerini kullanabilirsiniz:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

JSON_VALUE işlevi, veri sütununda depolanan JSON metinden bir değer ayıklar. Bu işlev, Ayıklanacak JSON metnindeki bir değere başvurmak için JavaScript benzeri bir yol kullanır. Ayıklanan değer SQL sorgusunun herhangi bir bölümünde kullanılabilir.

JSON_QUERY işlevi JSON_VALUE benzerdir. JSON_VALUE aksine, bu işlev JSON metnine yerleştirilmiş diziler veya nesneler gibi karmaşık alt nesneyi ayıklar.

JSON_MODIFY işlevi, silinecek JSON metninde değerin yolunu, ayrıca eskilerinin üzerine yazacak yeni bir değeri belirtmenize olanak tanır. Bu şekilde, tüm yapıyı yeniden ayrıştırmadan JSON metnini kolayca güncelleştirebilirsiniz.

JSON standart bir metinde depolandığından, metin sütunlarında depolanan değerlerin doğru biçimlendirildiğinden garanti yoktur. JSON sütununda depolanan metnin standart Azure SQL veritabanı denetim kısıtlamaları ve ıSJSON işlevi kullanılarak düzgün şekilde biçimlendirildiğini doğrulayabilirsiniz:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Giriş metni JSON olarak düzgün biçimlendirildiyse, ıSJSON işlevi 1 değerini döndürür. JSON sütununun her eklendiğinde veya güncelleştirmesinde, bu kısıtlama yeni metin değerinin hatalı biçimlendirilmiş JSON olduğunu doğrular.

## <a name="transforming-json-into-tabular-format"></a>JSON 'ı tablosal biçimine dönüştürme

Azure SQL veritabanı ve Azure SQL yönetilen örneği Ayrıca JSON koleksiyonlarını tablo biçimine dönüştürmenizi ve JSON verilerini yüklemeyi veya sorgulamanızı sağlar.

OPENJSON, JSON metnini ayrıştırır, JSON nesnelerinin bir dizisini bulur, dizinin öğelerini yineler ve dizideki her öğe için çıkış sonucunda bir satır döndürür.

![JSON tablolu](./media/json-features/image_2.png)

Yukarıdaki örnekte, açılması gereken JSON dizisinin yerini nerede bulabileceğiniz belirtebilirsiniz ($. Siparişler yolu), sonuç olarak döndürülecek sütunlar ve hücre olarak döndürülecek JSON değerlerinin nerede bulunacağı.

Değişkende bir JSON dizisini @orders bir dizi satıra dönüştürebiliriz, bu sonuç kümesini çözümleyebilir veya bir standart tabloya satır ekleyebilirsiniz:

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

JSON dizisi olarak biçimlendirilen ve saklı yordama bir parametre olarak belirtilen siparişlerin koleksiyonu, bir ayrıştırılmış ve Siparişler tablosuna eklenebilir.

## <a name="next-steps"></a>Sonraki adımlar

JSON ile uygulamanıza nasıl tümleştirileceğini öğrenmek için şu kaynaklara göz atın:

JSON ile uygulamanıza tümleştirme hakkında çeşitli senaryolar hakkında daha fazla bilgi edinmek için bu [Channel 9 videosundaki](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) tanıtımlar bölümüne bakın veya [JSON blog gönderilerinde](/archive/blogs/sqlserverstorageengine/json-in-sql-server-use-cases)kullanım durumuyla eşleşen bir senaryo bulun.