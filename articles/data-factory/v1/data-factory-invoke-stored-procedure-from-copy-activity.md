---
title: Azure Data Factory kopyalama etkinliğinden saklı yordam çağır
description: Azure SQL veritabanı 'nda saklı yordamı çağırmayı veya Azure Data Factory kopyalama etkinliğinden SQL Server öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0c5bb7ab4e8932c9568293620352435c7259d810
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682502"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Azure Data Factory kopyalama etkinliğinden saklı yordamı çağır
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory saklı yordam kullanarak verileri dönüştürme etkinliği](../transform-data-using-stored-procedure.md).


Verileri [SQL Server](data-factory-sqlserver-connector.md) veya [Azure SQL veritabanı](data-factory-azure-sql-connector.md)'na kopyalarken, saklı yordamı çağırmak Için kopyalama etkinliğinde **sqlsink** ' ı yapılandırabilirsiniz. Hedef tabloya veri eklemeden önce herhangi bir ek işleme (sütunları birleştirme, değerleri arama, birden çok tabloya ekleme vb.) gerçekleştirmek için saklı yordamı kullanmak isteyebilirsiniz. Bu özellik [tablo değerli parametrelerden](https://msdn.microsoft.com/library/bb675163.aspx)yararlanır. 

Aşağıdaki örnek, bir SQL Server veritabanında bir saklı yordamın Data Factory bir işlem hattından (kopyalama etkinliği) nasıl çağıralınacağını gösterir:  

## <a name="output-dataset-json"></a>Çıkış veri kümesi JSON
JSON çıktı veri kümesinde, **türü** : **sqlservertable**olarak ayarlayın. Bunu bir Azure SQL veritabanı ile kullanmak için **Azurestabtable** olarak ayarlayın. **TableName** özelliğinin değeri, saklı yordamın ilk parametresinin adı ile aynı olmalıdır.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>Kopyalama etkinliği JSON 'daki SqlSink bölümü
Kopyalama etkinliği JSON ' daki **Sqlsink** bölümünü aşağıdaki gibi tanımlayın. Havuz/hedef veritabanına veri eklerken saklı bir yordamı çağırmak için, hem **SqlWriterStoredProcedureName** hem de **Sqlwritertabletype** özellikleri için değerler belirtin. Bu özelliklerin açıklamaları için, [SQL Server Bağlayıcısı makalesindeki Sqlsink bölümüne](data-factory-sqlserver-connector.md#sqlsink)bakın.

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Saklı yordam tanımı 
Veritabanınızda, **SqlWriterStoredProcedureName**ile aynı ada sahip saklı yordamı tanımlayın. Saklı yordam, kaynak veri deposundan giriş verilerini işler ve hedef veritabanındaki bir tabloya veri ekler. Saklı yordamın ilk parametresinin adı JSON (Pazarlama) veri kümesinde tanımlanan tableName ile aynı olmalıdır.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Tablo türü tanımı
Veritabanınızda, **Sqlwritertabletype**ile aynı ada sahip tablo türünü tanımlayın. Tablo türünün şeması, giriş veri kümesinin şemasıyla eşleşmelidir.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Sonraki adımlar
Tüm JSON örnekleri için aşağıdaki bağlayıcı makalelerini gözden geçirin: 

- [Azure SQL Veritabanı](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
