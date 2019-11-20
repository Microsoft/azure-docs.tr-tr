---
title: Platformlar arası sorguları kullanmaya başlama
description: dikey olarak bölümlenmiş veritabanları ile elastik veritabanı sorgusu kullanma
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: af93035766eaf1afa12d124b8379ee55c5567260
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823788"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Veritabanları arası sorguları kullanmaya başlama (dikey bölümlendirme) (Önizleme)

Azure SQL veritabanı için elastik veritabanı sorgusu (Önizleme), tek bir bağlantı noktası kullanarak birden çok veritabanına yayılan T-SQL sorguları çalıştırmanızı sağlar. Bu makale, [dikey olarak bölümlenmiş veritabanları](sql-database-elastic-query-vertical-partitioning.md)için geçerlidir.  

İşlem tamamlandığında, birden fazla ilgili veritabanına yayılan sorgular gerçekleştirmek için bir Azure SQL veritabanını yapılandırmayı ve kullanmayı öğreneceksiniz.

Elastik veritabanı sorgusu özelliği hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı elastik veritabanı sorgusuna genel bakış](sql-database-elastic-query-overview.md).

## <a name="prerequisites"></a>Ön koşullar

Herhangi bir dış VERI kaynağı iznini DEĞIŞTIR gereklidir. Bu izin ALTER DATABASE iznine dahildir. Temel alınan veri kaynağına başvurmak için herhangi bir dış VERI kaynağı izinlerini DEĞIŞTIRME gerekir.

## <a name="create-the-sample-databases"></a>Örnek veritabanları oluşturma

İle başlamak için, aynı ya da farklı SQL veritabanı sunucularında iki veritabanı, **Müşteri** ve **sipariş**oluşturun.

**Orderınformation** tablosunu oluşturmak ve örnek verileri girmek için **Orders** veritabanında aşağıdaki sorguları yürütün.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Şimdi, **CustomerInformation** tablosunu oluşturmak ve örnek verileri girmek için **Customers** veritabanında aşağıdaki sorguyu yürütün.

    CREATE TABLE [dbo].[CustomerInformation](
        [CustomerID] [int] NOT NULL,
        [CustomerName] [varchar](50) NULL,
        [Company] [varchar](50) NULL
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
    )
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')

## <a name="create-database-objects"></a>Veritabanı nesneleri oluştur

### <a name="database-scoped-master-key-and-credentials"></a>Veritabanı kapsamlı ana anahtar ve kimlik bilgileri

1. Visual Studio 'da SQL Server Management Studio veya SQL Server Veri Araçları açın.
2. Orders veritabanına bağlanın ve aşağıdaki T-SQL komutlarını yürütün:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    "Kullanıcı adı" ve "parola", müşteriler veritabanında oturum açmak için kullanılan Kullanıcı adı ve parola olmalıdır.
    Elastik sorgularla Azure Active Directory kullanan kimlik doğrulaması şu anda desteklenmiyor.

### <a name="external-data-sources"></a>Dış veri kaynakları

Dış veri kaynağı oluşturmak için, siparişler veritabanında aşağıdaki komutu yürütün:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Dış tablolar

Siparişler veritabanında, CustomerInformation tablosunun tanımıyla eşleşen bir dış tablo oluşturun:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Örnek esnek veritabanı T-SQL sorgusu yürütme

Dış veri kaynağınızı ve dış tablolarınızı tanımladıktan sonra artık dış tablolarınızı sorgulamak için T-SQL kullanabilirsiniz. Bu sorguyu siparişler veritabanında yürütün:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Maliyet

Şu anda elastik veritabanı sorgusu özelliği, Azure SQL veritabanınızın maliyetine dahildir.  

Fiyatlandırma bilgileri için bkz. [SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Sonraki adımlar

* Elastik sorguya genel bakış için bkz. [elastik sorguya genel bakış](sql-database-elastic-query-overview.md).
* Dikey olarak bölümlenmiş verilere yönelik sözdizimi ve örnek sorgular için bkz. [dikey olarak bölümlenmiş verileri sorgulama)](sql-database-elastic-query-vertical-partitioning.md)
* Yatay bölümleme (parçalama) öğreticisi için bkz. [Yatay bölümleme (parçalama) için elastik sorgu ile çalışmaya](sql-database-elastic-query-getting-started.md)başlama.
* Yatay olarak bölümlenmiş veriler için sözdizimi ve örnek sorgular için bkz. [yatay olarak bölümlenmiş verileri sorgulama)](sql-database-elastic-query-horizontal-partitioning.md)
* Tek bir uzak Azure SQL veritabanı üzerinde Transact-SQL ifadesini yürüten saklı yordam için bkz. [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) , yatay bölümleme düzeninde parçalar olarak hizmet veren veritabanları kümesi.
