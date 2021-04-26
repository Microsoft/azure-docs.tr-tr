---
title: Ölçekli bulut veritabanları arasında rapor
description: Birden çok veritabanı arasında raporlamak için veritabanları arası veritabanı sorguları kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: 586dad7439cc57ed2c863ee5f6692e12f7a78c50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92781235"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Ölçekli bulut veritabanları arasında rapor (Önizleme)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Elastik bir sorgu](elastic-query-overview.md)kullanarak, tek bir bağlantı noktasındaki birden çok veritabanından rapor oluşturabilirsiniz. Veritabanlarının yatay olarak bölümlenmesi gerekir ("parçalar" olarak da bilinir).

Var olan bir veritabanınız varsa, bkz. [var olan veritabanlarını ölçekli veritabanlarına geçirme](elastic-convert-to-use-elastic-tools.md).

Sorgulamak için gereken SQL nesnelerini anlamak için bkz. [yatay bölümlenmiş veritabanlarında sorgulama](elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Önkoşullar

[Esnek veritabanı araçları](elastic-scale-get-started.md)'nı kullanmaya başlama örneğini indirip çalıştırın.

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Örnek uygulamayı kullanarak parça eşleme Yöneticisi oluşturma
Burada, birden fazla parça ile birlikte bir parça eşleme Yöneticisi oluşturacaksınız ve sonra verileri parçalara eklemekirsiniz. Zaten parçalar içindeki parçalı verileri içeren parçalar varsa, aşağıdaki adımları atlayabilir ve sonraki bölüme geçebilirsiniz.

1. [Örnek uygulamayı indirme ve çalıştırma](elastic-scale-get-started.md#download-and-run-the-sample-app-1)makalesindeki adımları Izleyerek **elastik veritabanı araçları** örnek uygulamasını derleyin ve çalıştırın. Tüm adımları tamamladıktan sonra aşağıdaki komut istemi görüntülenir:

    ![komut istemi][1]
2. Komut penceresinde, "1" yazın ve **ENTER** tuşuna basın. Bu, parça eşleme yöneticisini oluşturur ve sunucuya iki parçalama ekler. Ardından "3" yazın ve **ENTER** tuşuna basın. eylemi dört kez tekrarlayın. Bu, parçalara örnek veri satırları ekler.
3. [Azure Portal](https://portal.azure.com) , sunucunuzda üç yeni veritabanı göstermelidir:

   ![Visual Studio onayı][2]

   Bu noktada, çapraz veritabanı sorguları elastik veritabanı istemci kitaplıkları aracılığıyla desteklenir. Örneğin, komut penceresinde 4 seçeneğini kullanın. Çok parçalı bir sorgunun sonuçları her zaman tüm parçalardaki sonuçların bir **birleşimidir** .

   Sonraki bölümde, parçaları genelinde verilerin daha zengin sorgulanmasını destekleyen bir örnek veritabanı uç noktası oluşturacağız.

## <a name="create-an-elastic-query-database"></a>Elastik sorgu veritabanı oluşturma

1. [Azure Portal](https://portal.azure.com) açın ve oturum açın.
2. Azure SQL veritabanı 'nda, parça kurulumlarınız ile aynı sunucuda yeni bir veritabanı oluşturun. "Elaun dbquery" veritabanını adlandırın.

    ![Azure portal ve Fiyatlandırma Katmanı][3]

    > [!NOTE]
    > var olan bir veritabanını kullanabilirsiniz. Bunu yapabiliyorsanız, sorgularınızı yürütmek istediğiniz parçaların biri olmamalıdır. Bu veritabanı, elastik veritabanı sorgusu için meta veri nesneleri oluşturmak üzere kullanılacaktır.
    >

## <a name="create-database-objects"></a>Veritabanı nesneleri oluştur
### <a name="database-scoped-master-key-and-credentials"></a>Veritabanı kapsamlı ana anahtar ve kimlik bilgileri
Bunlar, parça eşleme Yöneticisi ve parçaları 'na bağlanmak için kullanılır:

1. Visual Studio 'da SQL Server Management Studio veya SQL Server Veri Araçları açın.
2. Elaun dbquery veritabanına bağlanın ve aşağıdaki T-SQL komutlarını yürütün:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';
    ```

    "Kullanıcı adı" ve "parola", **elastik veritabanı araçları ile çalışmaya başlama** makalesinde [Örnek uygulamayı indirme ve çalıştırma](elastic-scale-get-started.md#download-and-run-the-sample-app) başlıklı Bölüm adım 3 ' te kullanılan oturum açma bilgileri ile aynı olmalıdır.

### <a name="external-data-sources"></a>Dış veri kaynakları
Dış veri kaynağı oluşturmak için, Elaun dbquery veritabanında aşağıdaki komutu yürütün:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = SHARD_MAP_MANAGER,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
    CREDENTIAL = ElasticDBQueryCred,
    SHARD_MAP_NAME = 'CustomerIDShardMap'
) ;
```    

 "Customerıdshardmap", parçalı harita ve parça eşleme yöneticisini elastik veritabanı araçları örneği kullanarak oluşturduysanız parça eşlemenin adıdır. Ancak, bu örnek için özel kurulumunuzu kullandıysanız, uygulamanızda seçtiğiniz parça haritası adı olmalıdır.

### <a name="external-tables"></a>Dış tablolar
Elagardbquery veritabanında aşağıdaki komutu yürüterek parçaların içindeki Customers tablosuyla eşleşen bir dış tablo oluşturun:

```tsql
CREATE EXTERNAL TABLE [dbo].[Customers]
( [CustomerId] [int] NOT NULL,
    [Name] [nvarchar](256) NOT NULL,
    [RegionId] [int] NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc,
    DISTRIBUTION = SHARDED([CustomerId])
) ;
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Örnek esnek veritabanı T-SQL sorgusu yürütme
Dış veri kaynağınızı ve dış tablolarınızı tanımladıktan sonra, dış Tablolarınızda artık tam T-SQL kullanabilirsiniz.

Bu sorguyu Elaun dbquery veritabanında yürütün:

```tsql
select count(CustomerId) from [dbo].[Customers]
```

Sorgunun tüm parçalardan sonuçları topladığına ve aşağıdaki çıktıyı sunduğuna dikkat edin:

![Çıkış ayrıntıları][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Elastik veritabanı sorgu sonuçlarını Excel 'e aktar
 Sorgunun sonuçlarını bir Excel dosyasına aktarabilirsiniz.

1. Excel 2013 ' i başlatın.
2. **Veri** şeridine gidin.
3. **Diğer kaynaklardan** öğesine tıklayın ve **SQL Server**' ye tıklayın.

   ![Diğer kaynaklardan Excel içeri aktarma][5]
4. **Veri bağlantısı Sihirbazı** 'nda sunucu adını ve oturum açma kimlik bilgilerini yazın. Ardından **İleri**'ye tıklayın.
5. İletişim kutusunda istediğiniz **verileri içeren veritabanını seçin**, **Elaun dbquery** veritabanını seçin.
6. Liste görünümünde **Customers** tablosunu seçin ve **İleri**' ye tıklayın. Ardından, **Son**'a tıklayın.
7. **Veri al** formunda, **çalışma kitabınızda bu verileri nasıl görüntülemek Istediğinizi seçin** altında **tablo** ' yı seçin ve **Tamam**' ı tıklatın.

**Müşteriler** tablosundaki tüm satırlar, farklı parçalar halinde saklanan Excel sayfasını doldurur.

Artık Excel 'in güçlü veri görselleştirme işlevlerini kullanabilirsiniz. Bı ve veri tümleştirme araçlarınızı elastik sorgu veritabanına bağlamak için sunucu adı, veritabanı adı ve kimlik bilgilerinizle bağlantı dizesini kullanabilirsiniz. SQL Server, aracınız için bir veri kaynağı olarak desteklendiğinden emin olun. Daha esnek sorgu veritabanına ve dış tablolara, tıpkı diğer tüm SQL Server veritabanları ve araç ile bağlandığınız SQL Server tabloları gibi başvurabilirsiniz.

### <a name="cost"></a>Maliyet
Elastik veritabanı sorgu özelliğinin kullanılması için ek ücret alınmaz.

Fiyatlandırma bilgileri için bkz. [SQL Veritabanı Fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Sonraki adımlar

* Elastik sorguya genel bakış için bkz. [elastik sorguya genel bakış](elastic-query-overview.md).
* Dikey bölümleme öğreticisi için bkz. [çapraz veritabanı sorgusuna Başlarken (dikey bölümlendirme)](elastic-query-getting-started-vertical.md).
* Dikey olarak bölümlenmiş verilere yönelik sözdizimi ve örnek sorgular için bkz. [dikey olarak bölümlenmiş verileri sorgulama)](elastic-query-vertical-partitioning.md)
* Yatay olarak bölümlenmiş veriler için sözdizimi ve örnek sorgular için bkz. [yatay olarak bölümlenmiş verileri sorgulama)](elastic-query-horizontal-partitioning.md)
* Tek bir uzak Azure SQL veritabanı üzerinde Transact-SQL ifadesini yürüten saklı yordam için bkz. [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) , yatay bölümleme düzeninde parçalar olarak hizmet veren veritabanları kümesi.


<!--Image references-->
[1]: ./media/elastic-query-getting-started/cmd-prompt.png
[2]: ./media/elastic-query-getting-started/portal.png
[3]: ./media/elastic-query-getting-started/tiers.png
[4]: ./media/elastic-query-getting-started/details.png
[5]: ./media/elastic-query-getting-started/exel-sources.png
<!--anchors-->