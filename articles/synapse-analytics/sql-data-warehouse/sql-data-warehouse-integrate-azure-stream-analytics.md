---
title: Adanmış SQL havuzunda Azure Stream Analytics kullanma
description: Gerçek zamanlı çözümler geliştirmek için Azure SYNAPSE 'te adanmış SQL havuzu ile Azure Stream Analytics kullanmaya yönelik ipuçları.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 0c7f139b50cd43e3e8862fda3f5401a853ced8d0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566588"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzu ile Azure Stream Analytics kullanma

Azure Stream Analytics, bulutta akış verileri üzerinde düşük gecikmeli, yüksek oranda kullanılabilir ve ölçeklenebilir karmaşık olay işleme sağlayan, tam olarak yönetilen bir hizmettir. [Azure Stream Analytics tanıtım](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)bilgilerini okuyarak temelleri öğrenebilirsiniz. Daha sonra, Azure Stream Analytics öğreticisini [kullanmaya başlarken](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ' i izleyerek Stream Analytics ile uçtan uca bir çözüm oluşturmayı öğrenebilirsiniz.

Bu makalede, Azure Stream Analytics işlerle yüksek aktarım hızı verileri alımı için adanmış SQL havuzunuzu bir çıkış havuzu olarak kullanmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

* Azure Stream Analytics Iş-Azure Stream Analytics bir iş oluşturmak Için, Azure Stream Analytics öğreticisini [kullanmaya başlamak](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) için şu adımları izleyin:  

    1. Olay Hub 'ı girişi oluşturma
    2. Olay Oluşturucu uygulamasını yapılandırma ve başlatma
    3. Stream Analytics işi sağlama
    4. İş girişi ve sorgu belirtme
* Adanmış SQL havuzu-yeni bir adanmış SQL havuzu oluşturmak Için [hızlı başlangıç: adanmış BIR SQL havuzu oluşturma](../quickstart-create-sql-pool-portal.md)' daki adımları izleyin.

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Adanmış SQL havuzunuza işaret etmek için akış çıkışı belirtme

### <a name="step-1"></a>1. Adım

Azure portal, Stream Analytics işinize gidin ve **iş topolojisi** menüsünde **çıktılar** ' e tıklayın.

### <a name="step-2"></a>2. Adım

**Ekle** düğmesine tıklayın ve açılan menüden **Azure SYNAPSE Analytics** ' i seçin.

![Azure SYNAPSE Analytics 'i seçme](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>3. Adım

Aşağıdaki değerleri girin:

* *Çıkış diğer adı*: Bu iş çıktısı için kolay bir ad girin.
* *Abonelik*:
  * Adanmış SQL havuzunuz Stream Analytics işle aynı abonelikte yer alıyorsa, ***aboneliklerinizden Azure SYNAPSE Analytics Seç***' e tıklayın.
  * Adanmış SQL havuzunuz farklı bir abonelikte yer alıyorsa, Azure SYNAPSE Analytics ayarlarını el ile sağla ' ya tıklayın.
* *Veritabanı*: açılan listeden hedef veritabanını seçin.
* *Kullanıcı adı*: veritabanı için yazma izinlerine sahip olan bir hesabın kullanıcı adını belirtin.
* *Parola*: belirtilen kullanıcı hesabı için parola belirtin.
* *Tablo*: veritabanındaki hedef tablonun adını belirtin.
* **Kaydet** düğmesine tıklayın

![Azure SYNAPSE Analytics formu tamamlandı](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>4. Adım

Bir testi çalıştırmadan önce, tabloyu adanmış SQL havuzunuzdaki oluşturmanız gerekir.  SQL Server Management Studio (SSMS) veya istediğiniz sorgu aracını kullanarak aşağıdaki tablo oluşturma betiğini çalıştırın.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>5. Adım

Stream Analytics Azure portal işi için, iş adı ' na tıklayın.  _ *_Çıkış ayrıntıları_** bölmesinde ***Test** _ düğmesine tıklayın.

![Outpout ayrıntılarında test düğmesi ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) veritabanı bağlantısı başarılı olduğunda portalda bir bildirim görürsünüz.

### <a name="step-6"></a>6. Adım

_*_İş topolojisi_*_ altında ***sorgu** _ menüsüne tıklayın ve oluşturduğunuz akış çıktısına veri eklemek için sorguyu değiştirin.  Sorgunuzu test etmek için _*_Seçili sorguyu sına_*_ düğmesine tıklayın.  Sorgu testiniz başarılı olduğunda _ *_sorguyu Kaydet_** düğmesine tıklayın.

![Sorguyu Kaydet](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>7. Adım

Azure Stream Analytics işini başlatın.  _ *_Genel bakış_** menüsünde ***Başlat** _ düğmesine tıklayın.

![Stream Analytics işini başlatma](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

İşi Başlat bölmesindeki ***Başlat*** düğmesine tıklayın.

![Başlat 'a tıklayın](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Sonraki adımlar

Tümleştirmeye genel bakış için bkz. [diğer hizmetleri tümleştirme](sql-data-warehouse-overview-integrate.md).
Daha fazla geliştirme ipucu için bkz. [ADANMıŞ SQL havuzu Için tasarım kararları ve kodlama teknikleri](sql-data-warehouse-overview-develop.md).
