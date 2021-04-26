---
title: 'Öğretici: adanmış SQL havuzlarıyla verileri çözümlemeye başlama'
description: Bu öğreticide, SQL havuzunun analitik yeteneklerini araştırmak için NYC TAXI örnek verilerini kullanacaksınız.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 0def1f957842417c3936e3f1c7bb5bc023109818
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536332"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Adanmış SQL havuzları ile verileri analiz etme

Bu öğreticide, özel bir SQL havuzunun yeteneklerini araştırmak için NYC TAXI verilerini kullanacaksınız.

## <a name="create-a-dedicated-sql-pool"></a>Adanmış bir SQL havuzu oluşturma

1. SYNAPSE Studio 'da, sol taraftaki bölmede   >  **SQL havuzlarını** Yönet ' i seçin.
1. **Yeni** ' yi seçin
1. **SQL havuzu adı** için **SQLPOOL1** seçin
1. **Performans düzeyi** için **DW100C** seçin
1. **Gözden geçir ve oluştur** > **Oluştur**'u seçin. Adanmış SQL havuzunuz birkaç dakika içinde hazırlanacaktır. 

Adanmış SQL havuzunuz, **SQLPOOL1** olarak da BILINEN bir SQL veritabanı ile ilişkilendirilir.
1. **Veri**  >  **çalışma alanına** gidin.
1. **SQLPOOL1** adlı bir veritabanı görmeniz gerekir. Bunu görmüyorsanız **Yenile**' ye tıklayın.

Adanmış bir SQL havuzu, etkin olduğu sürece faturalanabilir kaynakları kullanır. Daha sonra maliyetleri azaltmak için havuzu duraklatabilirsiniz.

> [!NOTE] 
> Çalışma alanınızda yeni bir adanmış SQL Havuzu (eski adıyla SQL DW) oluştururken, adanmış SQL havuzu sağlama sayfası açılır. Sağlama, mantıksal SQL Server üzerinde gerçekleşir.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>NYC TAXI verilerini SQLPOOL1 'e yükleme

1. SYNAPSE Studio 'da **geliştirme** merkezine gidin, **+** Yeni kaynak eklemek için düğmeye tıklayın ve ardından yeni SQL betiği oluşturun.
1. Betiğin üzerindeki ' Bağlan ' açılan listesinde ' SQLPOOL1 ' havuzunu (Bu öğreticinin [1. adımında](./get-started-create-workspace.md) oluşturulan havuz) seçin.
1. Aşağıdaki kodu girin:
    ```
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5,
    PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, 
    PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, 
    PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, 
    FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, 
    TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. Betiği yürütmek için Çalıştır düğmesine tıklayın.
1. Bu betik 60 saniyeden daha az bir süre içinde sona acaktır. NYC TAXI verilerinin 2.000.000 satırlarını dbo adlı bir tabloya yükler **. Seyahat**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Özel SQL havuzundaki NYC TAXI verilerini keşfet

1. SYNAPSE Studio 'da **veri** merkezine gidin.
1. **SQLPOOL1**  >  **Tables** bölümüne gidin. 
3. Dbo öğesine sağ tıklayın **. NYCTaxiTripSmall** tablosu ve **Yeni SQL betiği** Seç  >  **ilk 100 satır seçin**.
4. Yeni bir SQL betiği oluşturulup çalışırken bekleyin.
5. SQL **komut dosyasının en üstünde,** **SQLPOOL1** adlı SQL havuzuna otomatik olarak ayarlandığını unutmayın.
6. SQL komut dosyasının metnini bu kodla değiştirin ve çalıştırın.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Bu sorgu, toplam seyahat mesafeleri ve ortalama seyahat mesafesinin, pascların sayısıyla ilişkisini gösterir.
1. SQL komut dosyası sonucu penceresinde, sonuçların bir çizgi grafik olarak görselliğini görmek için **görünümü** **grafik** olarak değiştirin.
    
## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure depolama hesabındaki verileri analiz etme](get-started-analyze-storage.md)
