---
title: Azure Data Factory tekrarlanabilir kopya
description: Verileri kopyalayan bir dilim birden çok kez çalıştırılsa bile, yinelemeleri nasıl önleyeceğinizi öğrenin.
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
ms.openlocfilehash: e4264bb198a0c167e33f35958079b0523303d29d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682368"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Azure Data Factory tekrarlanabilir kopya

## <a name="repeatable-read-from-relational-sources"></a>İlişkisel kaynaklardan yinelenebilir okuma
İlişkisel veri depolarından veri kopyalarken, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda dilimin yeniden çalıştırılması için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim her iki şekilde yeniden çalıştırıldığında, bir dilimin kaç kez çalıştırıldıklarından bağımsız olarak aynı verilerin okunmasını sağlayın.  
 
> [!NOTE]
> Aşağıdaki örnekler Azure SQL içindir, ancak dikdörtgen veri kümelerini destekleyen tüm veri depolamaları için geçerlidir. Veri deposu için kaynak **türünü** ve **sorgu** özelliğini (örneğin, sqlreaderquery yerine sorgu) ayarlamanız gerekebilir.   

Genellikle, ilişkisel depolardan okurken yalnızca söz konusu dilime karşılık gelen verileri okumak istersiniz. Bunu yapmanın bir yolu, Azure Data Factory ' de kullanılabilen WindowStart ve WindowEnd sistem değişkenlerini kullanmaktır. [Azure Data Factory-işlevler ve sistem değişkenleri](data-factory-functions-variables.md) makalesindeki Azure Data Factory değişkenleri ve işlevleri hakkında bilgi edinin. Örnek: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Bu sorgu, Tablom tablosundan (WindowStart-> WindowEnd) dilim süresi aralığında yer alan verileri okur. Bu dilimin yeniden çalıştırılması ayrıca aynı verilerin okunacağından de her zaman emin olur. 

Diğer durumlarda, tüm tabloyu okumak isteyebilirsiniz ve sqlReaderQuery öğesini şöyle tanımlayabilir:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>SqlSink olarak yinelenebilir yazma
Verileri **Azure SQL/SQL Server** diğer veri depolarından kopyalarken, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda tutmanız gerekir. 

Verileri Azure SQL/SQL Server veritabanına kopyalarken kopyalama etkinliği varsayılan olarak havuz tablosuna veri ekler. Bir Azure SQL/SQL Server veritabanında bulunan aşağıdaki tabloya iki kayıt içeren bir CSV (virgülle ayrılmış değerler) dosyasından veri kopyaladığınızı varsayalım. Bir dilim çalıştığında, iki kayıt SQL tablosuna kopyalanır. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Kaynak dosyasında hata bulduğunuzu ve 2 ile 4 arasında aşağı boru miktarını güncelleştirdiğinizi varsayın. Bu dönem için veri dilimini el ile yeniden çalıştırırsanız, Azure SQL/SQL Server veritabanına eklenen iki yeni kayıt bulacaksınız. Bu örnek, tablodaki sütunlardan hiçbirinin birincil anahtar kısıtlamasına sahip olduğunu varsayar.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Bu davranışı önlemek için, aşağıdaki iki mekanizmalardan birini kullanarak, UPSERT semantiği belirtmeniz gerekir:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mekanizma 1: sqlWriterCleanupScript kullanma
Bir dilim çalıştırıldığında verileri eklemeden önce havuz tablosundan verileri temizlemek için **Sqlwritercleanupscript** özelliğini kullanabilirsiniz. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Bir dilim çalıştığında, SQL tablosundan dilime karşılık gelen verileri silmek için Temizleme betiği ilk kez çalıştırılır. Kopyalama etkinliği daha sonra verileri SQL tablosuna ekler. Dilim yeniden çalıştırıldığında, miktar istenen şekilde güncelleştirilir.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Düz rondela kaydının orijinal CSV 'den kaldırıldığını varsayın. Sonra dilimi yeniden çalıştırmak aşağıdaki sonucu verir: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Kopyalama etkinliği, bu dilim için karşılık gelen verileri silmek üzere Temizleme betiğini çalıştırdı. Ardından CSV 'den (daha sonra yalnızca bir kayıt içeren) girişi okur ve tabloya yerleştirilir. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mekanizma 2: Daeıdentifiercolumnname kullanma
> [!IMPORTANT]
> Şu anda, Azure SQL veri ambarı için Feeıdentifiercolumnname desteklenmez. 

Yinelenebilirlik elde etmek için ikinci mekanizma, hedef tabloda ayrılmış bir sütuna (bir Dacıdentifiercolumnname) sahip olur. Bu sütun, kaynak ve hedefin eşitlenmiş kalmasını sağlamak için Azure Data Factory tarafından kullanılır. Bu yaklaşım, hedef SQL tablo şemasını değiştirme veya tanımlama konusunda esneklik olduğunda işe yarar. 

Bu sütun, yinelenebilirlik amaçları için Azure Data Factory tarafından kullanılır ve işlem Azure Data Factory tabloda herhangi bir şema değişikliği yapmaz. Bu yaklaşımı kullanmanın yolu:

1. Hedef SQL tablosunda **ikili (32)** türünde bir sütun tanımlayın. Bu sütunda hiçbir kısıtlama olmamalıdır. Bu sütunu bu örnek için Adfdilimleyiceıdentifier olarak adlandırın.


    Kaynak tablo:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Hedef tablo: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Bunu kopyalama etkinliğinde aşağıdaki gibi kullanın:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory, kaynak ve hedefin eşitlenmiş kalmasını sağlamak için bu sütunu gerek başına olarak doldurur. Bu sütunun değerleri bu bağlamın dışında kullanılmamalıdır. 

Mekanizmaya benzer şekilde, kopyalama etkinliği hedef SQL tablosundan verilen dilim için verileri otomatik olarak temizler. Ardından kaynaktan hedef tabloya veri ekler. 

## <a name="next-steps"></a>Sonraki adımlar
Tüm JSON örnekleri için aşağıdaki bağlayıcı makalelerini gözden geçirin: 

- [Azure SQL Veritabanı](data-factory-azure-sql-connector.md)
- [Azure SQL Veri Ambarı](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
