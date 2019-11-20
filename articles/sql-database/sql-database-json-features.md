---
title: JSON verileriyle çalışma
description: Azure SQL veritabanı, JavaScript Nesne Gösterimi (JSON) gösteriminde verileri ayrıştırabilmenizi, sorgulamanıza ve biçimlendirmenize olanak sağlar.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 01/15/2019
ms.openlocfilehash: 958d937ad85fd62249c7ce3f0e0ab2f8cc1d1b80
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819932"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Azure SQL veritabanı 'nda JSON özellikleriyle çalışmaya başlama
Azure SQL veritabanı, JavaScript Nesne Gösterimi [(JSON)](https://www.json.org/) biçiminde temsil edilen verileri ayrıştırabilmenizi ve sorgulamanızı ve ILIŞKISEL verilerinizi JSON metni olarak dışarı aktarmanızı sağlar. Azure SQL veritabanı 'nda aşağıdaki JSON senaryoları kullanılabilir:
- `FOR JSON` yan tümcesini kullanarak [JSON biçimindeki ilişkisel verileri biçimlendirme](#formatting-relational-data-in-json-format) .
- [JSON verileriyle çalışma](#working-with-json-data)
- JSON skalar işlevleri kullanılarak [JSON verileri sorgulanıyor](#querying-json-data) .
- `OPENJSON` işlevi kullanılarak [JSON tablo biçimine dönüştürülüyor](#transforming-json-into-tabular-format) .

## <a name="formatting-relational-data-in-json-format"></a>JSON biçimindeki ilişkisel verileri biçimlendirme
Veritabanı katmanından veri alan bir Web hizmetiniz varsa ve JSON biçiminde bir yanıt veya JSON biçiminde biçimlendirilen verileri kabul eden bir istemci tarafı JavaScript çerçeveleri veya kitaplıklarını içeriyorsa, veritabanı içeriğinizi doğrudan bir SQL sorgusunda JSON olarak biçimlendirebilirsiniz. Artık Azure SQL veritabanı 'ndan gelen sonuçları JSON olarak biçimlendiren uygulama kodu yazmanız gerekmez veya tablo sorgu sonuçlarını dönüştürmek ve ardından nesneleri JSON biçimine seri hale getirmek için bazı JSON serileştirme kitaplığı dahil etmeniz gerekmez. Bunun yerine, FOR JSON yan tümcesini kullanarak SQL sorgu sonuçlarını Azure SQL veritabanı 'nda JSON olarak biçimlendirebilir ve doğrudan uygulamanızda kullanabilirsiniz.

Aşağıdaki örnekte Sales. Customer tablosundaki satırlar FOR JSON yan tümcesi kullanılarak JSON olarak biçimlendirilir:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

FOR JSON PATH yan tümcesi sorgunun sonuçlarını JSON metni olarak biçimlendirir. Sütun adları anahtar olarak kullanılır, ancak hücre değerleri JSON değerleri olarak oluşturulur:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Sonuç kümesi, her satırın ayrı bir JSON nesnesi olarak biçimlendirildiği bir JSON dizisi olarak biçimlendirilir.

YOL, sütun diğer adları ' nda nokta gösterimini kullanarak JSON sonuçlarınızın çıkış biçimini özelleştirebileceğinizi gösterir. Aşağıdaki sorgu, çıkış JSON biçimindeki "CustomerName" anahtarının adını değiştirir ve telefon ve faks numaralarını "Iletişim" alt nesnesine koyar:

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Bu sorgunun çıktısı şöyle görünür:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Bu örnekte, [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) seçeneğini belirterek dizi yerine tek bir JSON nesnesi döndürüyoruz. Sorgu sonucu olarak tek bir nesne döndürdüğünü biliyorsanız, bu seçeneği kullanabilirsiniz.

FOR JSON yan tümcesinin ana değeri, veritabanınızdaki iç içe geçmiş JSON nesneleri veya diziler olarak biçimlendirilen karmaşık hiyerarşik verileri döndürmenize olanak tanır. Aşağıdaki örnek, `Orders`iç içe geçmiş dizisi olarak `Customer` ait `Orders` tablosundan satırların nasıl ekleneceğini gösterir:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Müşteri verilerini almak için ayrı sorgular göndermek ve sonra ilgili siparişlerin listesini getirmek yerine, aşağıdaki örnek çıktıda gösterildiği gibi, tek bir sorgu ile tüm gerekli verileri alabilirsiniz:

```
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

JSON, Azure SQL veritabanı 'ndaki herhangi bir dize türü gibi kullanılabilen bir metin biçimidir. JSON verilerini standart bir NVARCHAR olarak gönderebilir veya kaydedebilirsiniz:

```
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

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Azure SQL veritabanı 'nda dize verileriyle çalışan tüm istemci tarafı dilleri veya kitaplıkları JSON verileriyle de çalışacaktır. JSON, bellek için iyileştirilmiş tablo veya sistem sürümü tutulan tablo gibi NVARCHAR türünü destekleyen herhangi bir tabloda depolanabilir. JSON, istemci tarafı kodda ya da veritabanı katmanında herhangi bir kısıtlama sunmaz.

## <a name="querying-json-data"></a>JSON verilerini sorgulama
Azure SQL tablolarında depolanan JSON olarak biçimlendirilen veriler varsa, JSON işlevleri bu verileri herhangi bir SQL sorgusunda kullanmanıza olanak sağlar.

Azure SQL veritabanı 'nda kullanılabilen JSON işlevleri, JSON olarak biçimlendirilen verileri diğer SQL veri türleri gibi değerlendirmenizi sağlar. JSON metinden değerleri kolayca ayıklayabilir ve herhangi bir sorgudaki JSON verilerini kullanabilirsiniz:

```
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

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Giriş metni JSON olarak düzgün biçimlendirildiyse, ıSJSON işlevi 1 değerini döndürür. JSON sütununun her eklendiğinde veya güncelleştirmesinde, bu kısıtlama yeni metin değerinin hatalı biçimlendirilmiş JSON olduğunu doğrular.

## <a name="transforming-json-into-tabular-format"></a>JSON 'ı tablosal biçimine dönüştürme
Azure SQL veritabanı Ayrıca JSON koleksiyonlarını tablo biçimine dönüştürmenizi ve JSON verilerini yüklemeyi veya sorgulamanızı sağlar.

OPENJSON, JSON metnini ayrıştırır, JSON nesnelerinin bir dizisini bulur, dizinin öğelerini yineler ve dizideki her öğe için çıkış sonucunda bir satır döndürür.

![JSON tablolu](./media/sql-database-json-features/image_2.png)

Yukarıdaki örnekte, açılması gereken JSON dizisinin yerini nerede bulabileceğiniz belirtebilirsiniz ($. Siparişler yolu), sonuç olarak döndürülecek sütunlar ve hücre olarak döndürülecek JSON değerlerinin nerede bulunacağı.

@orders değişkeninde bir JSON dizisini bir dizi satıra dönüştürebiliriz, bu sonuç kümesini çözümleyebilir veya bir standart tabloya satır ekleyebilirsiniz:

```
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

* [TechNet blogu](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [MSDN belgeleri](https://msdn.microsoft.com/library/dn921897.aspx)
* [Channel 9 videosu](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

JSON ile uygulamanıza tümleştirme hakkında çeşitli senaryolar hakkında daha fazla bilgi edinmek için bu [Channel 9 videosundaki](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) tanıtımlar bölümüne bakın veya [JSON blog gönderilerinde](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/)kullanım durumuyla eşleşen bir senaryo bulun.

