---
title: Azure Veri Gezgini için sorgular yazma
description: Bu nasıl yapılır, Azure Veri Gezgini için temel ve daha gelişmiş sorgular gerçekleştirmeyi öğrenirsiniz.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 80d3eaaf7e588766d62f5e5885d75e61c590970e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881191"
---
# <a name="write-queries-for-azure-data-explorer"></a>Azure Veri Gezgini için sorgular yazma

Bu makalede, en yaygın işleçlerle basit sorgular gerçekleştirmek için Azure Veri Gezgini 'de sorgu dilini nasıl kullanacağınızı öğreneceksiniz. Ayrıca, dilin daha gelişmiş özelliklerinden bazılarını da açığa alırsınız.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki sorguları iki şekilde çalıştırabilirsiniz:

- Öğrendiğimiz Azure Veri Gezgini *Yardım kümesinde* .
    Azure Active Directory 'nin üyesi olan bir kurumsal e-posta hesabıyla [kümede oturum açın](https://dataexplorer.azure.com/clusters/help/databases/samples) .

- StormEvents örnek verilerini içeren kendi kümenizde. Daha fazla bilgi için bkz [. hızlı başlangıç: Azure Veri Gezgini kümesi ve veritabanı](create-cluster-database-portal.md) oluşturun ve [örnek verileri Azure Veri Gezgini içine alın](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Sorgu diline genel bakış

Azure Veri Gezgini 'deki bir sorgu dili, verileri işlemek ve sonuçları döndürmek için salt okunurdur bir istek. İstek, sözdizimini okumayı, yazmayı ve otomatikleştirmeyi kolaylaştırmak için tasarlanan bir veri akışı modeli kullanılarak düz metin olarak belirtilir. Sorgu SQL: veritabanları, tablolar ve sütunlara benzer bir hiyerarşide düzenlenmiş şema varlıklarını kullanır.

Sorgu, noktalı virgül (`;`) ile ayrılmış bir dizi sorgu deyiminden oluşur. Bu, en az bir deyim tablo benzeri bir sütun ve satır halinde düzenlenmiş veri üreten bir ifadedir. Sorgunun tablosal ifade deyimleri sorgunun sonuçlarını üretir.

Tablosal ifade deyiminin sözdizimi, bir tablosal sorgu işlecinden diğerine, veri kaynağıyla başlayarak (örneğin, veritabanındaki bir tablo veya veri üreten bir operatör) ve sonra bir veri dönüştürme kümesi üzerinden akan tablo veri akışı içerir Kanal (`|`) sınırlayıcısı kullanılarak birbirine bağlanan işleçler.

Örneğin, aşağıdaki sorguda tablolu ifade deyimi olan tek bir deyim vardır. İfade, adlı `StormEvents` bir tabloya başvuru ile başlar (Bu tabloyu barındıran veritabanı burada örtük olarak, bağlantı bilgilerinin bir parçası). Bu tabloya ait veriler (satırlar) daha sonra `StartTime` sütunun değerine göre filtrelenmiştir ve sonra `State` sütunun değerine göre filtrelenmiştir. Sorgu daha sonra "yeniden dönen" satırların sayısını döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA) **\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

Bu durumda, sonuç şu şekilde olur:

|Count|
|-----|
|   23|
| |

Daha fazla bilgi için bkz. [sorgu dili başvurusu](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>En yaygın işleçler

Bu bölümde ele alınan işleçler, Azure Veri Gezgini sorguları anlamak için yapı taşlarıdır. Yazdığınız çoğu sorguya bu işleçlerden birkaçı dahil edilir.

Yardım kümesinde sorguları çalıştırmak için: her bir sorgunun üzerindeki **sorguyu çalıştırmak Için tıklayın ' ı** seçin.

Sorguları kendi kümenizde çalıştırmak için:

1. Her sorguyu Web tabanlı sorgu uygulamasına kopyalayın ve ardından sorguyu seçin ya da imlecinizi sorguya yerleştirin.

1. Uygulamanın en üstünde **Çalıştır**' ı seçin.

### <a name="count"></a>count

[**sayı**](https://docs.microsoft.com/azure/kusto/query/countoperator): Tablodaki satır sayısını döndürür.

Aşağıdaki sorgu, StormEvents tablosundaki satır sayısını döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA) **\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>take

şunları [**yapın**](https://docs.microsoft.com/azure/kusto/query/takeoperator): Belirtilen sayıda veri satırı döndürür.

Aşağıdaki sorgu, StormEvents tablosundan beş satır döndürür. Anahtar sözcük *sınırı* , al için bir diğer addır *.*

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d) **\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Kaynak veriler sıralanmamışsa hangi kayıtların döndürüleceğini garanti yoktur.

### <a name="project"></a>project

[**Proje**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Sütunların bir alt kümesini seçer.

Aşağıdaki sorgu belirli bir sütun kümesini döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA) **\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>nereye

[**burada**](https://docs.microsoft.com/azure/kusto/query/whereoperator): Bir tabloyu bir koşula uyan satır alt kümesiyle filtreler.

Aşağıdaki sorgu, ve `EventType` `State`ile verileri filtreler.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>düzenine

[**Sırala**](https://docs.microsoft.com/azure/kusto/query/sortoperator): Giriş tablosunun satırlarını bir veya daha fazla sütuna göre sıraya göre sıralayın.

Aşağıdaki sorgu, verileri azalan sırada sıraya göre `DamageProperty`sıralar.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> İşlem sırası önemlidir. Daha önce `take 5` `sort by`yerleştirmeyi deneyin. Farklı sonuçlar elde edilsin mi?

### <a name="top"></a>Sayfanın Üstü

[**üst**](https://docs.microsoft.com/azure/kusto/query/topoperator): Belirtilen sütunlara göre sıralanan ilk *N* kaydı döndürür.

Aşağıdaki sorgu, daha az bir operatör ile aynı sonuçları döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>genişletmeyi

[**uzat**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Türetilmiş sütunları hesaplar.

Aşağıdaki sorgu her satırdaki bir değeri hesaplarken yeni bir sütun oluşturur.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

İfadeler her zamanki işleçleri (+,-, *,/,%) içerebilir ve çağırabilmeniz için kullanabileceğiniz bir dizi kullanışlı işlev vardır.

### <a name="summarize"></a>ölçütü

[**Özetle**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): Satır gruplarını toplar.

Aşağıdaki sorgu, tarafından `State`olayların sayısını döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA) **\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

**Özetleme** işleci, **by** yan tümcesinde aynı değerlere sahip satırları birlikte gruplandırır ve sonra her grubu tek bir satırda birleştirmek için toplama işlevini ( **Count**gibi) kullanır. Bu nedenle, bu durumda her durum için bir satır ve bu durumdaki satır sayısı için bir sütun vardır.

Bir dizi toplama işlevi vardır ve birkaç hesaplanan sütun oluşturmak için bunlardan birkaçını tek bir **özetleme** işlecinde kullanabilirsiniz. Örneğin, her durumda fırtınalarını sayısını ve durum başına benzersiz fırtınalarını sayısını alabilir ve en üst düzey olarak etkilenen durumları almak için **yukarıdan** yararlanın.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d) **\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

**Özetleme** işleminin sonucu:

- İçindeki adlı her sütun

- Hesaplanan her ifade için bir sütun

- Her değere göre birleşim için bir satır

### <a name="render"></a>işlenecek

[**işle**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Sonuçları bir grafik çıkış olarak işler.

Aşağıdaki sorgu bir sütun grafiğini görüntüler.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

Aşağıdaki sorgu basit bir zaman grafiği görüntüler.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA) **\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

Aşağıdaki sorgu, olayları bir günde bir kez, saat olarak bir kez ve bir zaman grafiği görüntüleyerek sayar.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

Aşağıdaki sorgu, zaman grafiğinde birden çok günlük serisini karşılaştırır.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA) **\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> **Render** işleci, altyapısının bir parçası yerine istemci tarafı bir özelliktir. Kullanım kolaylığı için dil ile tümleşiktir. Web uygulaması şu seçenekleri destekler: bargrafik, columnChart, piechart, timechart ve lineChart. 

## <a name="scalar-operators"></a>Skaler işleçler

Bu bölüm, en önemli skalar işleçlerden bazılarını ele alır.

### <a name="bin"></a>bin ()

[**bin ()** ](https://docs.microsoft.com/azure/kusto/query/binfunction): Değerleri, belirli bir bin boyutunun bir tam sayıya yuvarlar.

Aşağıdaki sorgu, sayıyı bir günün demet boyutuyla hesaplar.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>Case ()

[**Case ()** ](https://docs.microsoft.com/azure/kusto/query/casefunction): Koşulların listesini değerlendirir ve koşulu karşılanan ilk sonuç ifadesini veya son **Else** ifadesini döndürür. Bu işleci, verileri sınıflandırmak veya gruplandırmak için kullanabilirsiniz:

Aşağıdaki sorgu yeni bir sütun `deaths_bucket` ve grup sayısını sayı olarak döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d) **\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>Ayıkla ()

[**Ayıkla ()** ](https://docs.microsoft.com/azure/kusto/query/extractfunction): Bir metin dizesinden normal ifade için bir eşleşme alır.

Aşağıdaki sorgu, belirli öznitelik değerlerini bir izleden ayıklar.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d) **\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Bu sorgu, bir adı (Bu durumda `MyData`) bir ifadeye bağlayan Let deyimini kullanır. **Let** ifadesinin göründüğü kalan kapsam için (genel kapsam veya bir işlev gövdesi kapsamında), ad, ilişkili değerine başvurmak için kullanılabilir.

### <a name="parse_json"></a>parse_json()

[**parse_json ()** ](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Bir dizeyi JSON değeri olarak yorumlar ve değeri dinamik olarak döndürür. Bir bileşik JSON nesnesinin birden fazla öğesini ayıklamanız gerektiğinde **extractjson ()** işlevinin kullanımı üstün.

Aşağıdaki sorgu, JSON öğelerini bir diziden ayıklar.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d) **\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

Aşağıdaki sorgu JSON öğelerini ayıklar.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA) **\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

Aşağıdaki sorgu, JSON öğelerini dinamik bir veri türüyle ayıklar.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA) **\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>önce ()

[**önce ()** ](https://docs.microsoft.com/azure/kusto/query/agofunction): Verilen TimeSpan değeri geçerli UTC saat zamanından çıkartır.

Aşağıdaki sorgu, son 12 saat için verileri döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA) **\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek ()

[**startofweek ()** ](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): Belirtilmişse bir uzaklığa göre kaydırılan, tarihi içeren haftanın başlangıcını döndürür

Aşağıdaki sorgu, farklı uzaklıklarla haftanın başlangıcını döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d) **\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Bu sorgu, tek sütunlu bir değer tablosu üreten **Range** işlecini kullanır. Ayrıca bkz: [**startofday ()** ](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [**startofweek ()** ](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [**STARTOFYEAR ()** ](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [**STARTOFMONTH ()** ](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [**endofday ()** ](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [**endofweek ()** ](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [**ENDOFMONTH**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction)() ve [**ENDOFYEAR ()** ](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>Between ()

[ **() arasında**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Dahil edilen aralığın içindeki girişle eşleşir.

Aşağıdaki sorgu, verileri belirli bir tarih aralığına göre filtreliyor.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

Aşağıdaki sorgu, verileri belirli bir tarih aralığına göre filtreleyerek, başlangıç tarihinden üç güne (`3d`) sahip küçük bir çeşitle.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Tablolu işleçler

Kusto, bazıları bu makalenin diğer bölümlerinde ele alınan çok tablolu işleçlere sahiptir. Burada **ayrıştırmaya**odaklanacağız. 

### <a name="parse"></a>MAZ

[**ayrıştırma**](https://docs.microsoft.com/azure/kusto/query/parseoperator): Bir dize ifadesini değerlendirir ve değerini bir veya daha fazla hesaplanmış sütunda ayrıştırır. Ayrıştırmanın üç yolu vardır: basit (varsayılan), Regex ve gevşek.

Aşağıdaki sorgu bir izlemeyi ayrıştırır ve varsayılan bir basit ayrıştırma kullanarak ilgili değerleri ayıklar. İfade (StringConstant olarak adlandırılır) normal bir dize değeri ve eşleşme katı: genişletilmiş sütunlar gereken türlerle eşleşmelidir.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

Aşağıdaki sorgu, kullanarak `kind = regex`bir izlemeyi ayrıştırır ve ilgili değerleri ayıklar. StringConstant bir normal ifade olabilir.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

Aşağıdaki sorgu, kullanarak `kind = relaxed`bir izlemeyi ayrıştırır ve ilgili değerleri ayıklar. StringConstant normal bir dize değeridir ve eşleşme gevşek olabilir: genişletilmiş sütunlar, gereken türlerle kısmen eşleşir.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>Zaman serisi analizi

### <a name="make-series"></a>diziyi oluştur

[ **-Series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): [özetleme](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)gibi satır gruplarını toplar, ancak her değere göre her birleşimi için bir (Time) serisi vektörü oluşturur.

Aşağıdaki sorgu, gün başına fırtınası olay sayısı için bir zaman serisi kümesi döndürür. Sorgu, her durum için üç aylık bir dönemi ele alır ve kayıp sepetler 0 sabiti ile dolduruluyor:

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Bir dizi (Time) serisi oluşturduktan sonra anormal şekilleri, mevsimsel desenleri ve çok daha fazlasını tespit etmek için seriler işlevleri uygulayabilirsiniz.

Aşağıdaki sorgu, belirli bir günde en fazla olay bulunan en iyi üç durumu ayıklar:

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Daha fazla bilgi için [seri işlevlerinin](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions)tam listesini gözden geçirin.

## <a name="advanced-aggregations"></a>Gelişmiş toplamalar

Bu makalenin önceki bölümlerinde bulunan **say** ve **Özetle**gibi temel toplamalar kapsandık. Bu bölüm daha gelişmiş seçenekler sunar.

### <a name="top-nested"></a>üst iç içe

[**üst iç içe**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): Her düzeyin önceki düzey değerlere göre detaya gitme olduğu hiyerarşik üst sonuçlar üretir.

Bu işleç, pano görselleştirme senaryolarında veya aşağıdaki gibi bir soruyu yanıtlamak için gerekli olduğunda faydalıdır: "K1 'in top-N değerlerini bulur (bazı toplama kullanarak); Bunların her biri için, K2 (başka bir toplama kullanarak) en üst-b değerlerini bulun; ..."

Aşağıdaki sorgu, en üst düzeyde ve `State` `Sources`sonrasında bir hiyerarşik tablo döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>Pivot () eklentisi

[**Pivot () eklentisi**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): Giriş tablosundaki bir sütundan benzersiz değerleri çıkış tablosunda birden çok sütuna açıp bir tabloyu döndürür. İşleci, son çıktıda kalan herhangi bir sütun değeri için gerektiğinde toplamaları gerçekleştirir.

Aşağıdaki sorgu bir filtre uygular ve satırları sütunlar halinde özetler.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>DCount ()

[**DCount ()** ](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): Gruptaki bir ifadenin farklı değer sayısının tahminini döndürür. Tüm değerleri saymak için [**Count ()** ](https://docs.microsoft.com/azure/kusto/query/countoperator) kullanın.

Aşağıdaki sorgu, tarafından `Source` `State`birbirinden farklı sayılır.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d) **\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>değersay ()

[**değersay ()** ](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): Koşulun true olarak değerlendirdiği satırlar için ifadenin farklı değer sayısının tahminini döndürür.

Aşağıdaki sorgu, `Source` WHERE `DamageProperty < 5000`değerlerinin ayrı değerlerini sayar.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d) **\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcount_hll"></a>dcount_hll()

[**dcount_hll ()** ](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Hiperloglog sonuçlarından **DCount** değerini hesaplar ( [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) veya [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction)tarafından oluşturulur.

Aşağıdaki sorgu, sayımı oluşturmak için HLL algoritmasını kullanır.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA) **\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="arg_max"></a>arg_max()

[**arg_max()** ](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Grupta bir ifadeyi en üst düzeye çıkaran ve başka bir ifadenin değerini döndüren bir satırı bulur (veya * tüm satırı döndürmek için).

Aşağıdaki sorgu her durumda son taşma raporunun saatini döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset ()

[**makeset ()** ](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): Bir ifadenin grupta aldığı ayrı değerler kümesinin dinamik (JSON) dizisini döndürür.

Aşağıdaki sorgu, her durum tarafından bir taşma rapor edildiğinde ve ayrı değerler kümesinden bir dizi oluşturduğunda tüm süreleri geri döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>MV-Genişlet

[**MV-Genişlet**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): Koleksiyondaki her değerin ayrı bir satır alması için, dinamik olarak belirlenmiş bir sütundan çok değerli koleksiyonları genişletir. Genişletilmiş bir satırdaki diğer tüm sütunlar yinelenir. Bu, MakeList 'in tersidir.

Aşağıdaki sorgu, bir küme oluşturup daha sonra **MV-Expand** yeteneklerini göstermek için kullanarak örnek veriler üretir.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA) **\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>yüzdebirlik değeri ()

[**yüzdebirlik değeri ()** ](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Bir ifade tarafından tanımlanan popülasyonun belirtilen [**en yakın derecelendirme yüzdelik**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) değeri için bir tahmin döndürür. Doğruluk, yüzdelik bölge 'nin içindeki popülasyon yoğunluğunu gösterir. Yalnızca [**özetleme**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)içinde toplama bağlamında kullanılabilir.

Aşağıdaki sorgu, yüzdebirlik değeri for fırtınası süresini hesaplar.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Aşağıdaki sorgu, yüzdebirlik değeri for fırtınası süresini duruma göre hesaplar ve verileri beş dakikalık sepetler (`5m`) ile normalleştirir.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Çapraz veri kümesi

Bu bölüm, daha karmaşık sorgular oluşturmanızı, tablolar arasında veri katılmayı ve veritabanları ile kümeler arasında sorgulama yapmayı sağlayan öğeleri ele alır.

### <a name="let"></a>atalım

[**izin ver**](https://docs.microsoft.com/azure/kusto/query/letstatement): Modülerliği ve yeniden kullanımı geliştirir. **Let** deyimi, potansiyel olarak karmaşık bir ifadeyi birden çok parçaya bölebilir, her biri bir ada bağlanır ve bu parçaları birlikte oluşturabilir. Bir **Let** deyimi, Kullanıcı tanımlı işlevler ve görünümler oluşturmak için de kullanılabilir (sonuçları yeni bir tablo gibi görünür tablolar üzerinde ifadeler). **Let** deyimi tarafından sınırlanan ifadeler, tablo türü veya Kullanıcı tanımlı işlev (Lambdas) türünde skalar türde olabilir.

Aşağıdaki örnek tablosal türünde bir değişken oluşturur ve sonraki bir ifadede onu kullanır.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d) **\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>join

[**Birleştir**](https://docs.microsoft.com/azure/kusto/query/joinoperator): Her tablonun belirtilen sütun (ler) değerlerini eşleştirerek yeni bir tablo oluşturmak için iki tablo satırını birleştirin. Kusto, tam bir birleşim türü aralığını destekler: **FullOuter**, **Inner**, **ınnerunique**, **leftantı**, **leftantiyarı**, **LeftOuter**, **leftyarı**, **sağtantı**, **sağlaştırılmış tiyarı**,, **rightyarı**.

Aşağıdaki örnek iki tabloyu bir iç birleşim ile birleştirir.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==) **\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> JOIN öncesinde, giriş tablolarında bulunan satır ve sütun sayılarını azaltmak için **WHERE** ve **Proje** işleçlerini kullanın. Bir tablo her zaman diğerinin daha küçükse, bunu birleştirmenin sol (Piped) tarafı olarak kullanın. JOIN eşleşmesi için sütunlar aynı ada sahip olmalıdır. Tablolardan birindeki bir sütunu yeniden adlandırmak için gerekiyorsa **Proje** işlecini kullanın.

### <a name="serialize"></a>Serialize

[**seri hale getirme**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): **Row_number ()** gibi serileştirilmiş veriler gerektiren işlevleri kullanabilmeniz için satır kümesini seri hale getirir.

Aşağıdaki sorgu, veriler serileştirildiği için başarılı oldu.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

Satır kümesi de bir sonuç ise serileştirilmiş olarak değerlendirilir: **sıralama**, **üst**veya **Aralık** işleçleri, isteğe bağlı olarak **Proje**, **Proje-kondu**, **uzat**, **WHERE**, **Parse**, **MV-Genişlet** veya işleçlerden **yararlanın** .

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA) **\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Veritabanları arası ve çapraz küme sorguları

[Veritabanları arası ve çapraz küme sorguları](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): Aynı kümede bir veritabanını olarak `database("MyDatabase").MyTable`başvurarak sorgulayabilirsiniz. Uzak bir kümede bir veritabanını, olarak `cluster("MyCluster").database("MyDatabase").MyTable`başvurarak sorgulayabilirsiniz.

Aşağıdaki sorgu, `MyCluster` kümeden bir kümeden ve sorgu verilerinden çağrılır. Bu sorguyu çalıştırmak için kendi küme adınızı ve veritabanı adınızı kullanın.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Kullanıcı Analizi

Bu bölüm, kusto içinde kullanıcı davranışlarının analizini gerçekleştirmeye ne kadar kolay olduğunu gösteren öğeleri ve sorguları içerir.

### <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics eklentisi

[**activity_counts_metrics eklentisi**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): Faydalı etkinlik ölçümlerini (Toplam sayı değerleri, farklı sayı değerleri, farklı yeni değer sayısı ve toplanmış ayrı sayım) hesaplar. Ölçümler her bir zaman penceresi için hesaplanır, sonra karşılaştırılır ve tüm önceki zaman pencereleri ile toplanır.

Aşağıdaki sorgu, günlük etkinlik sayılarını hesaplayarak Kullanıcı benimsemesini analiz eder.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d) **\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activity_engagement-plugin"></a>activity_engagement eklentisi

[**activity_engagement eklentisi**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): Kayan bir zaman çizelgesi penceresinde KIMLIK sütununa göre etkinlik katılım oranını hesaplar. **activity_engagement eklentisi** , Dau, WAU ve Mau 'ları hesaplamak için kullanılabilir (günlük, haftalık ve aylık etkin kullanıcılar).

Aşağıdaki sorgu, her gün bir uygulama kullanan toplam ayrı kullanıcı sayısını, haftalık olarak değişen yedi günlük bir pencerede döndürür.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> Bau/MAU hesaplanırken, son verileri ve hareketli pencere dönemini (OuterActivityWindow) değiştirin.

### <a name="activity_metrics-plugin"></a>activity_metrics eklentisi

[**activity_metrics eklentisi**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): Geçerli dönem penceresine ve önceki dönem penceresine bağlı olarak, yararlı etkinlik ölçümlerini (farklı sayım değerleri, farklı yeni değer sayısı, bekletme oranı ve karmaşıklık oranı) hesaplar.

Aşağıdaki sorgu, belirli bir veri kümesi için karmaşıklığı ve bekletme oranını hesaplar.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="new_activity_metrics-plugin"></a>new_activity_metrics eklentisi

[**new_activity_metrics eklentisi**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): Yeni kullanıcıların kohortu için kullanışlı etkinlik ölçümlerini (ayrı sayı değerleri, farklı yeni değer sayısı, bekletme hızı ve karmaşıklık oranı) hesaplar. Bu eklentinin kavramı [**activity_metrics eklentisine**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin)benzerdir, ancak yeni kullanıcılara odaklanır.

Aşağıdaki sorgu, yeni kullanıcılar kohortu (ilk hafta gelen kullanıcılar) için haftalık bir hafta penceresinde bir bekletme ve değişim oranı hesaplar.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="session_count-plugin"></a>session_count eklentisi

[**session_count eklentisi**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): Bir zaman çizelgesi üzerinden KIMLIK sütununa göre oturum sayısını hesaplar.

Aşağıdaki sorgu, oturum sayısını döndürür. Kullanıcı KIMLIĞI, 100-saat yuvalarında en az bir kez görünürse, oturum geri arama penceresi 41-Time yuvadayken oturum etkin kabul edilir.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d) **\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnel_sequence-plugin"></a>funnel_sequence eklentisi

[**funnel_sequence eklentisi**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Bir durum dizisi almış olan kullanıcıların ayrı sayısını hesaplar; önceki ve sonraki durumların, sıranın sonunda veya ardından gelen dağılımını gösterir.

Aşağıdaki sorguda, 2007 ' deki tüm Tornado olayları ne kadar önce ve sonrasında gerçekleşen olay gösterilmektedir.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA) **\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion eklentisi

[**funnel_sequence_completion eklentisi**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): Farklı zaman aralıklarında tamamlanan dizi adımlarının huni sayısını hesaplar.

Aşağıdaki sorgu, sıranın tamamlanma pirayini denetler: `Hail -> Tornado -> Thunderstorm -> Wind` bir saatin, dört saatin ve bir günün (`[1h, 4h, 1d]`) "genel" saatlerinde.

**\[** [**Sorguyu çalıştırmak için tıklayın**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA) **\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>İşlevler

Bu bölüm, sunucuda depolanan yeniden kullanılabilir sorgular olan [**işlevleri**](https://docs.microsoft.com/azure/kusto/query/functions)içerir. İşlevler, sorgular ve diğer işlevler tarafından çağrılabilir (Özyinelemeli işlevler desteklenmez).

> [!NOTE]
> Yardım kümesinde, salt okunurdur olan işlevler oluşturamazsınız. Bu bölüm için kendi test kümenizi kullanın.

Aşağıdaki örnek, bir durum adı (`MyState`) bağımsız değişken olarak alan bir işlev oluşturur.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

Aşağıdaki örnek, Texas durumu için veri alan bir işlevi çağırır.

```Kusto
MyFunction ("Texas")
| summarize count()
```

Aşağıdaki örnek, ilk adımda oluşturulan işlevi siler.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Sonraki adımlar

[Kusto sorgu dili başvurusu](https://aka.ms/kustolangref)
