---
title: Azure Izleyici günlüklerinde metin verilerini ayrıştırma | Microsoft Docs
description: Verilerin alındığı ve bir sorguda alındığı zaman, her biri için göreli avantajları karşılaştıran Azure Izleyici kayıtlarında günlük verilerinin ayrıştırılmasına yönelik farklı seçenekler açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: 5a3b6852563955bfac940073bdda7d0afa02e77f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900257"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Azure Izleyici günlüklerinde metin verilerini ayrıştırma
Azure Izleyici tarafından toplanan bazı günlük verileri, tek bir özellikte birden fazla bilgi parçası içerecektir. Bu verilerin birden çok özelliğe çözümlenmesi, sorgularda kullanmayı kolaylaştırır. Ortak bir örnek, tek bir özellikte birden fazla değeri olan bir günlük girişinin tamamını toplayan [özel bir günlüğlük](../../log-analytics/log-analytics-data-sources-custom-logs.md) örneğidir. Farklı değerler için ayrı özellikler oluşturarak, her biri üzerinde arama ve toplama yapabilirsiniz.

Bu makalede, verilerin alındığı ve bir sorguda alındığı zaman, her biri için göreli avantajları karşılaştıran Azure Izleyici 'de günlük verilerinin ayrıştırılmasına yönelik farklı seçenekler açıklanmaktadır.


## <a name="parsing-methods"></a>Yöntemleri ayrıştırma
Verileri bir sorgu ile analiz edilirken veri toplandığında ya da sorgu zamanında veri alma sırasında verileri ayrıştırabilirsiniz. Her strateji aşağıda açıklandığı gibi benzersiz avantajlara sahiptir.

### <a name="parse-data-at-collection-time"></a>Toplama sırasında verileri Ayrıştır
Verileri toplama zamanında ayrıştırdığınızda, tabloda yeni özellikler oluşturan [özel alanları](../../log-analytics/log-analytics-custom-fields.md) yapılandırırsınız. Sorguların herhangi bir ayrıştırma mantığı içermesi gerekmez ve bu özellikleri tablodaki diğer herhangi bir alan olarak kullanmanız yeterlidir.

Bu yöntemin avantajları şunlardır:

- Sorguya ayrıştırma komutları dahil etmek zorunda olmadığınızdan toplanan verileri sorgulamak daha kolay.
- Sorgunun ayrıştırma gerçekleştirmesi gerekli olmadığından daha iyi sorgu performansı.
 
Bu yöntemin dezavantajları şunlardır:

- Önceden tanımlanmış olmalıdır. Zaten toplanmış olan veriler dahil olamaz.
- Ayrıştırma mantığını değiştirirseniz, yalnızca yeni verilere uygulanır.
- Sorgularda bulunandan daha az ayrıştırma seçeneği var.
- Verilerin toplanması için gecikme süresini artırır.
- Hataların işlenmesi zor olabilir.


### <a name="parse-data-at-query-time"></a>Sorgu zamanında verileri Ayrıştır
Sorgu zamanında verileri ayrıştırdığınızda, verileri birden çok alana ayrıştırmak için sorgunuzun mantığını dahil edersiniz. Gerçek tablo değiştirilmez.

Bu yöntemin avantajları şunlardır:

- Zaten toplanmış olan veriler de dahil olmak üzere tüm veriler için geçerlidir.
- Mantığdaki değişiklikler tüm verilere hemen uygulanabilir.
- Belirli veri yapıları için önceden tanımlanmış mantık dahil esnek ayrıştırma seçenekleri.
 
Bu yöntemin dezavantajları şunlardır:

- Daha karmaşık sorgular gerektirir. Bu, [bir tablonun benzetimini yapmak için işlevleri](#use-function-to-simulate-a-table)kullanılarak azaltılabilir.
- Ayrıştırma mantığı birden çok sorguya çoğaltılmalıdır. İşlevleri aracılığıyla bazı mantık paylaşabilir.
- , Çok büyük kayıt kümelerinde (milyarlarca kayıt) karmaşık mantık çalıştırırken ek yük oluşturabilir.

## <a name="parse-data-as-its-collected"></a>Toplanan verileri ayrıştırın
Toplanan verileri ayrıştırma hakkında ayrıntılı bilgi için bkz. [Azure izleyici 'de özel alanlar oluşturma](../platform/custom-fields.md) . Bu, tabloda, diğer herhangi bir özellik gibi sorgular tarafından kullanılabilecek özel özellikler oluşturur.

## <a name="parse-data-in-query-using-patterns"></a>Desenleri kullanarak sorgudaki verileri ayrıştırma
Ayrıştırmak istediğiniz veriler kayıtlar arasında yinelenen bir Düzenle tanımlanabildiğinde, belirli veri parçasını bir veya daha fazla yeni özelliğe ayıklamak için [kusto sorgu dilinde](/azure/kusto/query/) farklı işleçler kullanabilirsiniz.

### <a name="simple-text-patterns"></a>Basit metin desenleri

Bir dize ifadesinden ayıklanabilen bir veya daha fazla özel özellik oluşturmak için sorguınızdan [ayrıştırma](/azure/kusto/query/parseoperator) işlecini kullanın. Tanımlanabilecek kalıbı ve oluşturulacak özelliklerin adlarını belirtirsiniz. Bu, anahtar-değer dizelerine sahip veriler için _anahtar = değere_benzer bir biçimde oldukça yararlıdır.

Aşağıdaki biçimdeki verilerle özel bir günlük düşünün.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Aşağıdaki sorgu, bu verileri tekil özelliklerde ayrıştırır. _Proje_ içeren satır yalnızca hesaplanmış özellikleri döndürmek için _eklenir, bu, tüm_girişi özel günlükte tutan tek özelliktir.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Aşağıda, _AzureActivity_ TABLOSUNDAKI bir UPN 'nin Kullanıcı adını kesen bir örnek verilmiştir.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Normal ifadeler
Verileriniz bir normal ifadeyle tanımlanabilmelidir, tek tek değerleri ayıklamak için [normal ifadeler kullanan işlevleri](/azure/kusto/query/re2) kullanabilirsiniz. Aşağıdaki örnek, _AzureActivity_ kayıtlarından _UPN_ alanını çıkarmak için [Ayıkla](/azure/kusto/query/extractfunction) kullanır ve sonra farklı kullanıcılar döndürür.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Azure Izleyici, büyük ölçekte verimli bir şekilde ayrıştırma sağlamak için, benzer ancak diğer normal ifade çeşitleriyle aynı olmayan normal Ifadelerin RE2 sürümünü kullanır. Ayrıntılar için [RE2 Expression söz dizimini](https://aka.ms/kql_re2syntax) inceleyin.


## <a name="parse-delimited-data-in-a-query"></a>Sorgudaki sınırlandırılmış verileri ayrıştırma
Sınırlandırılmış veriler, alanları CSV dosyasında virgül gibi ortak bir karakterle ayırır. Belirlediğiniz sınırlayıcıyı kullanarak sınırlandırılmış verileri ayrıştırmak için [split](/azure/kusto/query/splitfunction) işlevini kullanın. Verilerdeki tüm alanları döndürmek veya çıkışa dahil edilecek ayrı alanları belirtmek için bunu [genişletme](/azure/kusto/query/extendoperator) işleciyle birlikte kullanabilirsiniz.

> [!NOTE]
> Split dinamik bir nesne döndürdüğünden, sonuçların operatörler ve filtrelerde kullanılacak dize gibi veri türlerine açıkça dönüştürülmesi gerekebilir.

Aşağıdaki CSV biçimindeki verilerle özel bir günlük düşünün.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Aşağıdaki sorgu, bu verileri ayrıştırarak hesaplanan özelliklerden ikisi tarafından özetlemeye neden olur. İlk satır, _rawData_ özelliğini bir dize dizisine böler. Sonraki satırların her biri, tek tek özelliklere bir ad verir ve bunları uygun veri türüne dönüştürmek için işlevleri kullanarak çıktıya ekler.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Sorguda önceden tanımlanmış yapıları ayrıştırma
Verileriniz bilinen bir yapıda biçimlendirildiyse, önceden tanımlı yapıları ayrıştırmak için [kusto sorgu dilinde](/azure/kusto/query/) işlevlerden birini kullanabilirsiniz:

- [JSON](/azure/kusto/query/parsejsonfunction)
- ['SINI](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [URL sorgusu](/azure/kusto/query/parseurlqueryfunction)
- [Dosya yolu](/azure/kusto/query/parsepathfunction)
- [Kullanıcı Aracısı](/azure/kusto/query/parse-useragentfunction)
- [Sürüm dizesi](/azure/kusto/query/parse-versionfunction)

Aşağıdaki örnek sorgu, JSON içinde yapılandırılmış olan _AzureActivity_ tablosunun _Özellikler_ alanını ayrıştırır. Sonuçları, JSON içindeki bağımsız adlandırılmış değeri içeren _Parsedprop_adlı dinamik bir özelliğe kaydeder. Bu değerler sorgu sonuçlarını filtrelemekte ve özetlemekte kullanılır.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Bu ayrıştırma işlevleri işlemci açısından yoğun olabilir, bu nedenle yalnızca sorgunuz biçimlendirilen verilerden birden çok özellik kullandığında kullanılmalıdır. Aksi takdirde, basit kalıp eşleme işleme daha hızlı olur.

Aşağıdaki örnek, etki alanı denetleyicisi TGT ön kimlik doğrulama türünün dökümünü gösterir. Tür yalnızca bir XML dizesi olan EventData alanında bulunur, ancak bu alandan başka bir veri gerekmez. Bu durumda, gereken veri parçasını seçmek için [ayrıştırma](/azure/kusto/query/parseoperator) kullanılır.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Bir tablonun benzetimini yapmak için fonksiyonunu kullanın
Belirli bir tablonun aynı ayrıştırmasını gerçekleştiren birden çok sorgunuz olabilir. Bu durumda, her sorguda ayrıştırma mantığını çoğaltmak yerine ayrıştırılmış verileri döndüren [bir işlev oluşturun](functions.md) . Daha sonra diğer sorgularda özgün tablonun yerine işlev diğer adını kullanabilirsiniz.

Yukarıdaki virgülle ayrılmış özel günlük örneğini göz önünde bulundurun. Ayrıştırılmış verileri birden çok sorguda kullanmak için aşağıdaki sorguyu kullanarak bir işlev oluşturun ve _Mycustomcsvlog_diğer adıyla kaydedin.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Artık, aşağıdaki gibi sorgularda gerçek tablo adının yerine _Mycustomcsvlog_ diğer adını kullanabilirsiniz.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Sonraki adımlar
* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](log-query-overview.md) hakkında bilgi edinin.