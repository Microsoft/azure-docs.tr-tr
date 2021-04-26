---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 9447cec55c53861ca57d5416a91ffefd35fdd20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91571903"
---
## <a name="repeatability-during-copy"></a>Kopyalama sırasında yinelenebilirlik
Diğer verilerden Azure SQL/SQL Server veri kopyalama sırasında, istenmeyen sonuçların önüne geçmek için yinelenebilirlik aklınızda bulundurmanız gerekir. 

Verileri Azure SQL/SQL Server veritabanına kopyalarken, varsayılan olarak veri kümesi varsayılan olarak havuz tablosuna eklenir. Örneğin, iki kayıt içeren bir CSV (virgülle ayrılmış değerler verileri) dosya kaynağındaki verileri Azure SQL/SQL Server veritabanına kopyalarken, bu tablo şöyle görünür:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Kaynak dosyasında hata bulduğunuzu ve kaynak dosyada 2 ' den 4 ' e kadar aşağı boru miktarını güncelleştirdiğinizi varsayın. Bu dönem için veri dilimini yeniden çalıştırırsanız, Azure SQL/SQL Server veritabanına eklenen iki yeni kayıt bulacaksınız. Aşağıdaki tabloda, tablodaki sütunlardan hiçbirinin birincil anahtar kısıtlaması olmadığı varsayılır.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Bunu önlemek için, aşağıda belirtilen 2 mekanizmalardan birini kullanarak, UPSERT semantiği belirtmeniz gerekecektir.

> [!NOTE]
> Bir dilim, belirtilen yeniden deneme ilkesine göre Azure Data Factory otomatik olarak yeniden çalıştırılabilir.
> 
> 

### <a name="mechanism-1"></a>Mekanizma 1
Yalnızca bir dilim çalıştırıldığında Temizleme eylemini gerçekleştirmek için **Sqlwritercleanupscript** özelliğinden yararlanabilirsiniz. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Temizleme betiği, söz konusu dilime karşılık gelen SQL tablosundan verileri silecek belirli bir dilim için kopyalama sırasında yürütülür. Etkinlik daha sonra verileri SQL tablosuna ekler. 

Dilim şimdi yeniden çalışıyorsa, miktarın istendiği şekilde güncelleştirildiğini görürsünüz.

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
Yeni bir şey yapılması gerekiyordu. Kopyalama etkinliği, bu dilim için karşılık gelen verileri silmek üzere Temizleme betiğini çalıştırdı. Ardından CSV 'den (daha sonra yalnızca 1 kayıt içeren) girişi okur ve tabloya yerleştirilir. 

### <a name="mechanism-2"></a>Mekanizma 2
> [!IMPORTANT]
> Şu anda Azure SYNAPSE Analytics için Daeıdentifiersütunadı desteklenmiyor. 

Yinelenebilirlik elde etmek için başka bir mekanizma, hedef tabloda adanmış bir sütun ('**Feleştirme ColumnName**) içermelidir. Bu sütun, kaynak ve hedefin eşitlenmiş kalmasını sağlamak için Azure Data Factory tarafından kullanılır. Bu yaklaşım, hedef SQL tablo şemasını değiştirme veya tanımlama konusunda esneklik olduğunda işe yarar. 

Bu sütun, yinelenebilirlik amaçları için Azure Data Factory tarafından ve işlem Azure Data Factory, tabloda herhangi bir şema değişikliği yapmayacak. Bu yaklaşımı kullanmanın yolu:

1. Hedef SQL tablosunda ikili (32) türünde bir sütun tanımlayın. Bu sütunda hiçbir kısıtlama olmamalıdır. Bu örnek için bu sütunu ' ColumnForADFuseOnly ' olarak adlandırın.
2. Bunu kopyalama etkinliğinde aşağıdaki gibi kullanın:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory, kaynak ve hedefin eşitlendiğinden emin olmak için bu sütunu gerek temelinde dolduracaktır. Bu sütunun değerleri, Kullanıcı tarafından bu bağlamın dışında kullanılmamalıdır. 

Mekanizmaya benzer şekilde, kopyalama etkinliği, hedef SQL tablosundan belirli bir dilim için verileri otomatik olarak temizler ve sonra bu dilim için kaynaktan hedefe veri eklemek üzere kopyalama etkinliğini normal olarak çalıştırır. 

