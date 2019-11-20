---
title: Microsoft Azure Backup Server v3 için sürüm notları
description: Bu makalede, Microsoft Azure Backup Server (MABS) v3 için bilinen sorunlar ve geçici çözümler hakkında bilgi verilmektedir.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: b47d83e0e3714f3f035397acaadeac9cda39d12a
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172272"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Microsoft Azure Backup sunucusu için sürüm notları

Bu makalede Microsoft Azure Backup Server (MABS) v3 için bilinen sorunlar ve geçici çözümler sağlanmaktadır.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Kümelenmiş iş yükleri için yedekleme ve kurtarma başarısız oluyor

**Açıklama:** MABS v2 'yi MABS v3 sürümüne yükselttikten sonra, Hyper-V kümesi veya SQL kümesi (SQL her zaman açık) veya veritabanı kullanılabilirlik grubu (DAG) gibi kümelenmiş veri kaynakları için yedekleme/geri yükleme başarısız olur.

**Geçici çözüm:** Bunu engellemek için SQL Server Management Studio (SSMS)) açın ve aşağıdaki SQL betiğini DPM DB 'de çalıştırın:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Rusça yerel ayarında MABS v3 'e yükseltme başarısız oluyor

**Açıklama:** MABS v2 'den MABS v3 sürümüne yükseltme, Rusça yerel ayarında **4387**hata koduyla başarısız oluyor.

**Geçici çözüm:** Rusça Install paketini kullanarak MABS v3 'e yükseltmek için aşağıdaki adımları uygulayın:

1. SQL veritabanınızı [yedekleyin](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) ve Mabs v2 'yi kaldırın (kaldırma sırasında korumalı verileri tutmayı seçin).
2. Yükseltme kapsamında SQL 2017 (Enterprise) ve raporlamayı Kaldır ' a yükseltin.
3. [Yüklemesi](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Yüklemesi](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. [SQL 2017 Ile SSRS yapılandırmasında](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs)belgelenen parametreleri kullanarak raporlamayı yapılandırın.
6. [Yüklemesi](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Geri yükle](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SSMS kullanarak SQL ve [burada](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10))AÇıKLANDıĞı gibi DPM-Sync aracını çalıştırın.
8. Aşağıdaki komutu kullanarak dbo. tbl_DLS_GlobalSetting tablosundaki ' DataBaseVersion ' özelliğini güncelleştirin:

```sql
        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'
```

9. MSDPM hizmetini başlatın.

## <a name="next-steps"></a>Sonraki adımlar

[MABS v3 'deki yenilikler](backup-mabs-whats-new-mabs.md)
