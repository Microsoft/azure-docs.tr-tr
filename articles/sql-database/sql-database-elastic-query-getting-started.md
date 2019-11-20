---
title: Ölçeği genişletilen bulut veritabanlarında raporlama
description: Birden çok veritabanı arasında raporlamak için veritabanları arası veritabanı sorguları kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: bad52b364dc83994e7985fc80b1b9f9e7f50481e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823785"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Ölçekli bulut veritabanları arasında rapor (Önizleme)

[Elastik bir sorgu](sql-database-elastic-query-overview.md)kullanarak, tek bir bağlantı noktasından birden çok Azure SQL veritabanından rapor oluşturabilirsiniz. Veritabanlarının yatay olarak bölümlenmesi gerekir ("parçalar" olarak da bilinir).

Var olan bir veritabanınız varsa, bkz. [var olan veritabanlarını ölçekli veritabanlarına geçirme](sql-database-elastic-convert-to-use-elastic-tools.md).

Sorgulamak için gereken SQL nesnelerini anlamak için bkz. [yatay bölümlenmiş veritabanlarında sorgulama](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Ön koşullar

[Esnek veritabanı araçları](sql-database-elastic-scale-get-started.md)'nı kullanmaya başlama örneğini indirip çalıştırın.

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Örnek uygulamayı kullanarak parça eşleme Yöneticisi oluşturma
Burada, birden fazla parça ile birlikte bir parça eşleme Yöneticisi oluşturacaksınız ve sonra verileri parçalara eklemekirsiniz. Zaten parçalar içindeki parçalı verileri içeren parçalar varsa, aşağıdaki adımları atlayabilir ve sonraki bölüme geçebilirsiniz.

1. [Örnek uygulamayı indirme ve çalıştırma](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app-1)makalesindeki adımları Izleyerek **elastik veritabanı araçları** örnek uygulamasını derleyin ve çalıştırın. Tüm adımları tamamladıktan sonra aşağıdaki komut istemi görüntülenir:

    ![komut istemi][1]
2. Komut penceresinde, "1" yazın ve **ENTER**tuşuna basın. Bu, parça eşleme yöneticisini oluşturur ve sunucuya iki parçalama ekler. Ardından "3" yazın ve **ENTER**tuşuna basın. eylemi dört kez tekrarlayın. Bu, parçalara örnek veri satırları ekler.
3. [Azure Portal](https://portal.azure.com) , sunucunuzda üç yeni veritabanı göstermelidir:

   ![Visual Studio onayı][2]

   Bu noktada, çapraz veritabanı sorguları elastik veritabanı istemci kitaplıkları aracılığıyla desteklenir. Örneğin, komut penceresinde 4 seçeneğini kullanın. Çok parçalı bir sorgunun sonuçları her zaman tüm parçalardaki sonuçların bir **birleşimidir** .

   Sonraki bölümde, parçaları genelinde verilerin daha zengin sorgulanmasını destekleyen bir örnek veritabanı uç noktası oluşturacağız.

## <a name="create-an-elastic-query-database"></a>Elastik sorgu veritabanı oluşturma
1. [Azure Portal](https://portal.azure.com) açın ve oturum açın.
2. Parça kurulumlarınız ile aynı sunucuda yeni bir Azure SQL veritabanı oluşturun. "Elaun dbquery" veritabanını adlandırın.

    ![Azure portal ve Fiyatlandırma Katmanı][3]

    > [!NOTE]
    > var olan bir veritabanını kullanabilirsiniz. Bunu yapabiliyorsanız, sorgularınızı yürütmek istediğiniz parçaların biri olmamalıdır. Bu veritabanı, elastik veritabanı sorgusu için meta veri nesneleri oluşturmak üzere kullanılacaktır.
    >

## <a name="create-database-objects"></a>Veritabanı nesneleri oluştur
### <a name="database-scoped-master-key-and-credentials"></a>Veritabanı kapsamlı ana anahtar ve kimlik bilgileri
Bunlar, parça eşleme Yöneticisi ve parçaları 'na bağlanmak için kullanılır:

1. Visual Studio 'da SQL Server Management Studio veya SQL Server Veri Araçları açın.
2. Elaun dbquery veritabanına bağlanın ve aşağıdaki T-SQL komutlarını yürütün:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "Kullanıcı adı" ve "parola", **elastik veritabanı araçları ile çalışmaya başlama** makalesinde [Örnek uygulamayı indirme ve çalıştırma](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) başlıklı Bölüm adım 3 ' te kullanılan oturum açma bilgileri ile aynı olmalıdır.

### <a name="external-data-sources"></a>Dış veri kaynakları
Dış veri kaynağı oluşturmak için, Elaun dbquery veritabanında aşağıdaki komutu yürütün:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "Customerıdshardmap", parçalı harita ve parça eşleme yöneticisini elastik veritabanı araçları örneği kullanarak oluşturduysanız parça eşlemenin adıdır. Ancak, bu örnek için özel kurulumunuzu kullandıysanız, uygulamanızda seçtiğiniz parça haritası adı olmalıdır.

### <a name="external-tables"></a>Dış tablolar
Elagardbquery veritabanında aşağıdaki komutu yürüterek parçaların içindeki Customers tablosuyla eşleşen bir dış tablo oluşturun:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Örnek esnek veritabanı T-SQL sorgusu yürütme
Dış veri kaynağınızı ve dış tablolarınızı tanımladıktan sonra, dış Tablolarınızda artık tam T-SQL kullanabilirsiniz.

Bu sorguyu Elaun dbquery veritabanında yürütün:

    select count(CustomerId) from [dbo].[Customers]

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
6. Liste görünümünde **Customers** tablosunu seçin ve **İleri**' ye tıklayın. Ardından **son**' a tıklayın.
7. **Veri al** formunda, **çalışma kitabınızda bu verileri nasıl görüntülemek Istediğinizi seçin**altında **tablo** ' yı seçin ve **Tamam**' ı tıklatın.

**Müşteriler** tablosundaki tüm satırlar, farklı parçalar halinde saklanan Excel sayfasını doldurur.

Artık Excel 'in güçlü veri görselleştirme işlevlerini kullanabilirsiniz. Bı ve veri tümleştirme araçlarınızı elastik sorgu veritabanına bağlamak için sunucu adı, veritabanı adı ve kimlik bilgilerinizle bağlantı dizesini kullanabilirsiniz. SQL Server, aracınız için bir veri kaynağı olarak desteklendiğinden emin olun. Daha esnek sorgu veritabanına ve dış tablolara, tıpkı diğer tüm SQL Server veritabanları ve araç ile bağlandığınız SQL Server tabloları gibi başvurabilirsiniz.

### <a name="cost"></a>Maliyet
Elastik veritabanı sorgu özelliğinin kullanılması için ek ücret alınmaz.

Fiyatlandırma bilgileri için bkz. [SQL Veritabanı Fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Sonraki adımlar

* Elastik sorguya genel bakış için bkz. [elastik sorguya genel bakış](sql-database-elastic-query-overview.md).
* Dikey bölümleme öğreticisi için bkz. [çapraz veritabanı sorgusuna Başlarken (dikey bölümlendirme)](sql-database-elastic-query-getting-started-vertical.md).
* Dikey olarak bölümlenmiş verilere yönelik sözdizimi ve örnek sorgular için bkz. [dikey olarak bölümlenmiş verileri sorgulama)](sql-database-elastic-query-vertical-partitioning.md)
* Yatay olarak bölümlenmiş veriler için sözdizimi ve örnek sorgular için bkz. [yatay olarak bölümlenmiş verileri sorgulama)](sql-database-elastic-query-horizontal-partitioning.md)
* Tek bir uzak Azure SQL veritabanı üzerinde Transact-SQL ifadesini yürüten saklı yordam için bkz. [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) , yatay bölümleme düzeninde parçalar olarak hizmet veren veritabanları kümesi.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
