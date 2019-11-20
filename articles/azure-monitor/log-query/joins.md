---
title: Azure Izleyici günlük sorgularıyla birleşimler | Microsoft Docs
description: Bu makale, Azure Izleyici günlük sorgularıyla birleştirmeleri kullanma konusunda bir ders içerir.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 526c359367271c69ccd461e4421c3223b00fbc36
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900279"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Azure Izleyici günlük sorgularındaki birleşimler

> [!NOTE]
> Bu dersi tamamlamadan önce [Azure izleyici Log Analytics kullanmaya başlayın](get-started-portal.md) ve [Azure İzleyici günlüğü sorgularını](get-started-queries.md) tamamlamanız gerekir.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Birleşimler, aynı sorgudaki birden çok tablodan verileri analiz etmenize olanak tanır. Belirtilen sütunların değerleriyle eşleşen iki veri kümesi satırını birleştirirlar.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

Bu örnekte, ilk veri kümesi tüm oturum açma olaylarına filtre uygular. Bu, tüm oturum kapatma olaylarını filtreleyen ikinci bir veri kümesiyle birleştirilir. Tasarlanan sütunlar _bilgisayar_, _Hesap_, _Targetlogonıd_ve _TimeGenerated_' dir. Veri kümeleri, bir paylaşılan sütunla bağıntılı, _Targetlogonıd_. Çıktı, bağıntı başına, oturum açma ve oturum kapatma zamanına sahip tek bir kayıttır.

Her iki DataSet 'in de aynı ada sahip sütunları varsa, sağ taraftaki veri kümesinin sütunlarına bir dizin numarası verilir, bu nedenle bu örnekte sonuçlar, sol taraftaki tablodaki değerlerle _Targetlogonıd_ ve _TargetLogonId1_ değerleriyle gösterilir sağ taraftaki tablodan. Bu durumda, ikinci _TargetLogonId1_ sütunu `project-away` işleci kullanılarak kaldırılmıştır.

> [!NOTE]
> Performansı artırmak için, `project` işlecini kullanarak yalnızca birleştirilmiş veri kümelerinin ilgili sütunlarını saklayın.


İki veri kümesini birleştirmek için aşağıdaki sözdizimini kullanın ve birleştirilmiş anahtar iki tablo arasında farklı bir ada sahiptir:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Arama tabloları
Birleşimlerin yaygın kullanımı, sonuçları daha edileni bir şekilde dönüştürmeye yardımcı olabilecek `datatable` kullanarak değerlerin statik eşlemesini kullanmaktır. Örneğin, güvenlik olay verilerini her olay KIMLIĞI için olay adıyla zenginleştirme.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![DataTable ile birleştirin](media/joins/dim-table.png)

## <a name="join-kinds"></a>Ekleme türleri
_Tür_ bağımsız değişkeniyle birlikte JOIN türünü belirtin. Her tür, aşağıdaki tabloda açıklandığı gibi, belirtilen tabloların kayıtları arasında farklı bir eşleşme gerçekleştirir.

| JOIN türü | Açıklama |
|:---|:---|
| ınnerunique | Bu, varsayılan JOIN modudur. İlk olarak, sol tablodaki eşleşen sütunun değerleri bulunur ve yinelenen değerler kaldırılır.  Sonra benzersiz değerler kümesi, doğru tabloyla eşleştirilir. |
| Dahili | Sonuçlara yalnızca her iki tablodaki eşleşen kayıtlar dahil edilir. |
| soltouter | Sol tablodaki tüm kayıtlar ve sağ tablodaki eşleşen kayıtlar, sonuçlara dahildir. Eşleşmeyen çıkış özellikleri null değerleri içeriyor.  |
| soltantı | Sol taraftaki kayıtlar, sağdan eşleşme içermeyen sonuçlara dahildir. Sonuçlar tablosunda yalnızca sol tablodaki sütunlar bulunur. |
| leftyarı | Sağ taraftaki eşleşmeler ile eşleşen kayıtlar sonuçlara dahildir. Sonuçlar tablosunda yalnızca sol tablodaki sütunlar bulunur. |


## <a name="best-practices"></a>En iyi uygulamalar

En iyi performans için aşağıdaki noktaları göz önünde bulundurun:

- JOIN için değerlendirilmesi gereken kayıtları azaltmak için her tabloda bir zaman filtresi kullanın.
- `where` ve `project` kullanarak, birleşimden önce giriş tablolarındaki satır ve sütun sayılarını azaltabilirsiniz.
- Bir tablo her zaman diğerinin daha küçükse, bunu birleştirmenin sol tarafında kullanın.


## <a name="next-steps"></a>Sonraki adımlar
Bkz. Azure Izleyici günlük sorgularını kullanmaya yönelik diğer dersler:

- [Dize işlemleri](string-operations.md)
- [Toplama işlevleri](aggregations.md)
- [Gelişmiş toplamalar](advanced-aggregations.md)
- [JSON ve veri yapıları](json-data-structures.md)
- [Gelişmiş sorgu yazma](advanced-query-writing.md)
- [Grafik](charts.md)