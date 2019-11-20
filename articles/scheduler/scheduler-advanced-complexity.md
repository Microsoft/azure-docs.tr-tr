---
title: Gelişmiş iş zamanlamaları ve tekrarları oluşturma-Azure Zamanlayıcı
description: Azure Scheduler 'da işler için gelişmiş zamanlamalar ve Yinelenmeler oluşturmayı öğrenin
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.suite: infrastructure-services
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 386284543cd8fb00cc49fea9a29d9eaee4ca4963
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300974"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Azure Scheduler 'da işler için gelişmiş zamanlamalar ve Yinelenmeler oluşturma

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , [devre dışı bırakılmakta](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)olan Azure Scheduler 'ı değiştiriyor. Zamanlayıcı 'da ayarladığınız işlerle çalışmaya devam etmek için lütfen en kısa sürede [Azure Logic Apps geçirin](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

Bir [Azure Scheduler](../scheduler/scheduler-intro.md) işi içinde zamanlama, Zamanlayıcı hizmetinin işi ne zaman ve nasıl çalıştıracağını belirleyen çekirdekdir. Zamanlayıcı ile bir iş için birden fazla tek seferlik ve yinelenen zamanlamalar ayarlayabilirsiniz. Tek seferlik zamanlamalar, belirli bir zamanda yalnızca bir kez çalışır ve temelde yalnızca bir kez çalışan bir yinelenen zamanlamalardır. Yinelenen zamanlamalar belirtilen sıklıkta çalışır. Bu esneklikle Zamanlayıcı 'yı çeşitli iş senaryoları için kullanabilirsiniz, örneğin:

* **Verileri düzenli olarak temizleyin**: Üç aydan daha eski olan tüm fazla alanı silen günlük bir iş oluşturun.

* **Arşiv verileri**: Bir yedekleme hizmetine fatura geçmişi veren bir aylık iş oluşturun.

* **Dış veri iste**: 15 dakikada bir çalışan ve NOAA 'den yeni bir hava durumu raporu çeken bir iş oluşturun.

* **İşlem görüntüleri**: Yoğun olmayan saatlerde çalışan bir iş günü işi oluşturun ve gün içinde karşıya yüklenen görüntüleri sıkıştırmak için bulut bilgi işlem kullanır.

Bu makale Zamanlayıcı 'Yı ve [Azure zamanlayıcı REST API](/rest/api/scheduler)kullanarak oluşturabileceğiniz örnek işleri açıklar ve her bir zamanlama için JAVASCRIPT nesne GÖSTERIMI (JSON) tanımını içerir. 

## <a name="supported-scenarios"></a>Desteklenen senaryolar

Bu örnekler, Azure Scheduler 'ın desteklediği senaryoların aralığını ve çeşitli davranış desenleri için zamanlama oluşturmayı gösterir, örneğin:

* Belirli bir tarih ve saatte bir kez çalıştırın.
* ' İ çalıştırın ve belirli sayıda kez tekrar edin.
* Hemen çalıştırın ve tekrar edin.
* Belirli bir zamandan başlayarak her *n* dakikada bir, saat, gün, hafta veya ay çalıştırın.
* Yalnızca haftanın belirli günlerinde veya ayın belirli günlerinde, haftalık veya aylık olarak çalıştırın.
* Belirli bir dönemde çalıştırın ve birden çok kez tekrarlarsınız. Örneğin, her ay son Cuma ve Pazartesi günü ya da 5:15: Saat ve 5:15 PM 'de günlük olarak.

Bu makalede daha sonra bu senaryolar daha ayrıntılı olarak açıklanmaktadır.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>REST API zamanlama oluşturma

[Azure Scheduler REST API](/rest/api/scheduler)temel bir zamanlama oluşturmak için aşağıdaki adımları izleyin:

1. [Kaynak Yöneticisi REST API Kaydet işlemini](https://docs.microsoft.com/rest/api/resources/providers)kullanarak Azure aboneliğinizi bir kaynak sağlayıcısına kaydedin. Azure Zamanlayıcı hizmeti için sağlayıcı adı **Microsoft. Scheduler**' dır. 

1. Zamanlayıcı REST API [iş koleksiyonları Için oluşturma veya güncelleştirme işlemini](https://docs.microsoft.com/rest/api/scheduler/jobcollections) kullanarak bir iş koleksiyonu oluşturun. 

1. [İşler Için oluşturma veya güncelleştirme işlemini](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate)kullanarak bir iş oluşturun. 

## <a name="job-schema-elements"></a>İş şeması öğeleri

Bu tablo, işler için Yinelenmeler ve zamanlamalar ayarlarken kullanabileceğiniz büyük JSON öğeleri için üst düzey bir genel bakış sağlar. 

| Öğe | Gerekli | Açıklama | 
|---------|----------|-------------|
| **startTime** | Hayır | [Iso 8601 biçiminde](https://en.wikipedia.org/wiki/ISO_8601) , işin ilk olarak temel bir zamanlamaya göre ne zaman başlayacağını belirten bir tarih saat dizesi değeri. <p>Karmaşık zamanlamalar için iş **StartTime**değerinden önce başlamaz. | 
| **recurrence** | Hayır | İşin çalıştığı zaman için yineleme kuralları. **Yinelenme** nesnesi şu öğeleri destekler: **Sıklık**, **Interval**, **zamanlama**, **sayı**ve **bitişsaati**. <p>**Yineleme** öğesini kullanırsanız **Sıklık** öğesini de kullanmanız gerekir, diğer **yineleme** öğeleri isteğe bağlıdır. |
| **frequency** | Evet, **yinelenme** kullandığınızda | Oluşum arasındaki zaman birimi ve bu değerleri destekler: "Dakika", "saat", "gün", "hafta", "ay" ve "yıl" | 
| **interval** | Hayır | **Sıklık**temelinde oluşum arasındaki zaman birimi sayısını belirleyen pozitif bir tamsayı. <p>Örneğin, **Aralık** 10, **Sıklık** ise "Week" ise, iş her 10 haftada bir yinelenir. <p>Her bir sıklık için en fazla Aralık sayısı aşağıda verilmiştir: <p>-18 ay <br>-78 hafta <br>-548 gün <br>-Saat ve dakika için Aralık 1 < = <*aralığı*> < = 1000 ' dir. | 
| **schedule** | Hayır | Belirtilen dakika işaretleri, saat işaretleri, haftanın günleri ve ayın günleri temelinde tekrardaki değişiklikleri tanımlar | 
| **count** | Hayır | İşin bitmeden önce kaç kez çalışacağını belirten pozitif bir tamsayı. <p>Örneğin, günlük iş **sayısı** 7 olarak ayarlandığında ve başlangıç tarihi Pazartesi ise, iş Pazar günü çalışmayı sonlandırır. Başlangıç tarihi zaten geçmişse, ilk çalıştırma oluşturma zamanından hesaplanır. <p>İş, **bitişsaati** veya **sayı**olmadan sonsuz çalışır. Aynı işte hem **Count** hem de **bitişsaati** kullanamazsınız, ancak önce sona erme kuralı kabul edilir. | 
| **endTime** | Hayır | [Iso 8601 biçiminde](https://en.wikipedia.org/wiki/ISO_8601) , işin çalışmayı ne zaman durdurduğunu belirten bir tarih veya tarih saat dizesi değeri. Geçmişte olan **bitişsaati** için bir değer ayarlayabilirsiniz. <p>İş, **bitişsaati** veya **sayı**olmadan sonsuz çalışır. Aynı işte hem **Count** hem de **bitişsaati** kullanamazsınız, ancak önce sona erme kuralı kabul edilir. |
|||| 

Örneğin, bu JSON şeması bir iş için temel bir zamanlamayı ve yinelemeyi açıklar: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Tarihler ve tarih saat değerleri*

* Zamanlayıcı işlerinde tarihler yalnızca tarihi içerir ve [ıso 8601 belirtimini](https://en.wikipedia.org/wiki/ISO_8601)izler.

* Zamanlayıcı işlerinde tarih ve saat her ikisi de tarih ve saati içerir, [ıso 8601 belirtimini](https://en.wikipedia.org/wiki/ISO_8601)IZLEYIN ve UTC boşluğu belirtilmediğinde UTC olarak kabul edilir. 

Daha fazla bilgi için bkz. [Kavramlar, terminoloji ve varlıklar](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Ayrıntılar: başlangıçsaati

Bu tabloda, **StartTime** 'in bir işin çalışma biçimini nasıl denetlediği açıklanmaktadır:

| startTime | Yinelenme yok | Yinelenme, zamanlama yok | Zamanlama ile yinelenme |
|-----------|---------------|-------------------------|--------------------------|
| **Başlangıç saati yok** | Hemen çalıştırın. | Hemen çalıştırın. Son yürütme zamanından hesaplanan sonraki yürütmeleri çalıştırın. | Hemen çalıştırın. Daha sonraki yürütmeleri bir yinelenme zamanlaması temelinde çalıştırın. | 
| **Geçmişteki başlangıç zamanı** | Hemen çalıştırın. | Başlangıç zamanından sonraki ilk gelecek çalışma süresini hesaplayın ve o zaman çalıştırın. <p>Son yürütme zamanından hesaplanan sonraki yürütmeleri çalıştırın. <p>Bu tablodan sonraki örneğe bakın. | Başlangıç işi belirtilen başlangıç zamanından *önce değil* . İlk yinelenme, başlangıç zamanından hesaplanan zamanlamaya göre gerçekleştirilir. <p>Daha sonraki yürütmeleri bir yinelenme zamanlaması temelinde çalıştırın. | 
| **Gelecekte veya geçerli zamanda başlangıç zamanı** | Belirtilen başlangıç zamanında bir kez çalıştırın. | Belirtilen başlangıç zamanında bir kez çalıştırın. <p>Son yürütme zamanından hesaplanan sonraki yürütmeleri çalıştırın. | Başlangıç işi belirtilen başlangıç zamanından *önce değil* . İlk yinelenme, başlangıç zamanından hesaplanan zamanlamaya göre gerçekleştirilir. <p>Daha sonraki yürütmeleri bir yinelenme zamanlaması temelinde çalıştırın. |
||||| 

Şu koşullara sahip bir örnek olduğunu varsayalım: bir yinelenme ile geçmişteki bir başlangıç saati, ancak zamanlama yok.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Geçerli tarih ve saat, 08 Nisan 2015, 1:00 PM.

* Başlangıç tarihi ve saati, 07 Nisan 2015, geçerli tarih ve saatten önce olan 2:00 PM.

* Yinelenme her iki günde bir olur.

1. Bu koşullar altında, ilk yürütme 09 Nisan 2015, 2:00 PM 'de yer alır. 

   Zamanlayıcı, başlangıç zamanına göre yürütme tekrarlamalarını hesaplar, geçmişteki tüm örnekleri atar ve gelecekte bir sonraki örneği kullanır. 
   Bu durumda, **StartTime** , 07 Nisan 2015 ' de 2:00 pm tarihinde, sonraki örnek bu zamandan Itibaren 23 Nisan 2015, 2:00.

   İlk yürütme, **StartTime** 'in 2015-04-05 14:00 mi yoksa 2015-04-01 14:00 mi olduğu ile aynıdır. İlk yürütmeden sonra, sonraki yürütmeler zamanlamaya göre hesaplanır. 
   
1. Yürütmeler daha sonra şu sırada izler: 
   
   1. 2015-04-11:2:00 PM
   1. 2015-04-13:2:00 PM 
   1. 2015-04-15:2:00 PM
   1. vb...

1. Son olarak, bir işin zamanlaması olduğunda ancak belirtilen saat ve dakika olmadığında, bu değerler varsayılan olarak ilk yürütmede saat ve dakika olarak belirlenir.

<a name="schedule"></a>

## <a name="details-schedule"></a>Ayrıntılar: zamanlama

İş yürütmelerinin sayısını *sınırlandırmak* için **Schedule** ' i kullanabilirsiniz. Örneğin, **sıklığı** "month" olan bir işin yalnızca 31. gününde çalışan bir zamanlaması varsa, iş yalnızca 31 günü olan aylarda çalışır.

**Zamanlamayı** ayrıca iş yürütmelerinin sayısını *genişletmek* için de kullanabilirsiniz. Örneğin, **sıklığı** "month" olan bir iş, 1 ve 2. gün içinde çalışan bir zamanlamaya sahipse, iş ayda yalnızca bir kez değil ayın birinci ve ikinci günlerinde çalışır.

Birden fazla zamanlama öğesi belirtirseniz, değerlendirme sırası en büyükten en küçüğe: hafta numarası, ay günü, gün, saat ve dakika olur.

Aşağıdaki tabloda schedule öğeleri ayrıntılı bir şekilde açıklanmıştır:

| JSON adı | Açıklama | Geçerli değerler |
|:--- |:--- |:--- |
| **minutes** |İşin çalıştırıldığı saatin dakikası. |Tamsayılar dizisi. |
| **hours** |İşin çalıştırıldığı günün saati. |Tamsayılar dizisi. |
| **weekDays** |İşin çalıştırıldığı haftanın günleri. Yalnızca haftalık sıklık ile belirtilebilir. |Aşağıdaki değerlerden herhangi birinin dizisi (en fazla dizi boyutu 7 ' dir):<br />-"Pazartesi"<br />-"Salı"<br />-"Çarşamba"<br />-"Perşembe"<br />-"Cuma"<br />-"Cumartesi"<br />-"Pazar"<br /><br />Büyük/küçük harfe duyarlı değildir. |
| **monthlyOccurrences** |İşin ayın hangi günlerinde çalışacağını belirler. Yalnızca aylık bir sıklık ile belirtilebilir. |**Monthlyoccurrobjects** dizisi:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **gün** , işin çalıştığı haftanın günü. Örneğin, *{Pazar}* ayın her Pazar günü olur. Gerekli.<br /><br />**oluşum** , ay içinde günün oluşma sayısıdır. Örneğin, *{Pazar,-1}* ayın son Pazar günüdür. İsteğe bağlı. |
| **monthDays** |İşin çalıştırıldığı ayın günü. Yalnızca aylık bir sıklık ile belirtilebilir. |Aşağıdaki değerlerden oluşan bir dizi:<br />- <= -1 ve >= -31 koşullarına uyan herhangi bir değer<br />- >= 1 ve <= 31 koşullarına uyan herhangi bir değer|

## <a name="examples-recurrence-schedules"></a>Örnekler: Yinelenme zamanlamaları

Aşağıdaki örneklerde çeşitli yinelenme zamanlamaları gösterilmektedir. Örnekler zamanlama nesnesine ve alt öğelerine odaklanmaktadır.

Bu zamanlamalar, **aralığın** 1 olarak ayarlandığını varsayar\. Örnekler Ayrıca **zamanlamaya**göre değerler için doğru **Sıklık** değerlerini de varsayar. Örneğin, "gün" **sıklığını** kullanamazsınız ve **zamanlamaya**göre bir **monthdays** değişikliği yapamazsınız. Bu kısıtlamaları makalenin önceki kısımlarında anlatmaktadır.

| Örnek | Açıklama |
|:--- |:--- |
| `{"hours":[5]}` |Her gün 5 saat sonra çalıştırın.<br /><br />Zamanlayıcı, her bir değeri "dakikalar" içinde her bir değerle eşler, her bir değer, her bir değeri "dakika", her biri ile, işin çalıştığı tüm zamanların bir listesini oluşturur. |
| `{"minutes":[15], "hours":[5]}` |Her gün 05.15'te çalıştır. |
| `{"minutes":[15], "hours":[5,17]}` |Her gün 05.15 ve 17.15’te çalıştır. |
| `{"minutes":[15,45], "hours":[5,17]}` |Her gün 05.15, 05.45, 17.15 ve 17.45’te çalıştır. |
| `{"minutes":[0,15,30,45]}` |15 dakikada bir çalıştır. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Saatte bir çalıştır.<br /><br />Bu iş her saat çalışır. Belirtilen dakika, belirtilmişse **StartTime**değeri ile denetlenir. **StartTime** değeri belirtilmemişse, dakika oluşturma zamanı tarafından denetlenir. Örneğin, başlangıç saati veya oluşturma saati (hangisi geçerlidir) 12:25 PM ise, iş 00:25, 01:25, 02:25,..., 23:25 üzerinde çalışır.<br /><br />Zamanlama, **sıklığı** "saat", **Aralık** 1 olan ve **zamanlama** değeri olmayan bir işle aynıdır. Fark, bu zamanlamayı başka işler oluşturmak için farklı **Sıklık** ve **Aralık** değerleriyle birlikte kullanmanıza olanak sağlar. Örneğin **Sıklık** "month" ise, zamanlama her gün yerine yalnızca ayda bir kez çalışır ( **Sıklık** "gün" ise). |
| `{minutes:[0]}` |Her saat başı çalıştır.<br /><br />Bu iş aynı zamanda saatte bir çalışır, ancak saat üzerinde (12 har, 1 har, 2, vb.). Bu zamanlama **, sıklığı "** saat", **StartTime** değeri sıfır dakika ve sıklığı "gün" ise **zamanlama**olmadan aynıdır. Ancak **Sıklık** "Week" veya "month" ise, zamanlama sırasıyla yalnızca bir gün veya ayda bir gün yürütülür. |
| `{"minutes":[15]}` |Saatte bir saat sonra 15 dakikada bir çalıştırın.<br /><br />Her saat, 00:15, 1:15, 2:15, vb. ile başlayarak çalışır. 11:15 PM tarihinde sona erer. |
| `{"hours":[17], "weekDays":["saturday"]}` |Her hafta Cumartesi tarihinde 5 saat sonra çalıştırın. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Her hafta Pazartesi, Çarşamba ve Cuma günleri üzerinden 5 saat içinde çalıştırın. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Her hafta Pazartesi, Çarşamba ve Cuma günleri saat 17.15 ve 17.45'te çalıştır. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Her hafta Pazartesi, Çarşamba ve Cuma günleri saat 5 ve 5 ' te çalıştırın. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Her hafta Pazartesi, Çarşamba ve Cuma günleri saat 5:15, 5:45, 5:15 ve 5:45:00 ' da çalıştırın. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Haftanın her günü 15 dakikada bir çalıştır. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Haftanın her günü 15 dakikada bir Çalıştır, 9 saat ile 4:45 PM arasında. |
| `{"weekDays":["sunday"]}` |Başlangıç zamanında bir Pazar günü çalıştırın. |
| `{"weekDays":["tuesday", "thursday"]}` |Başlangıç zamanında Salı günleri ve Perşembe üzerinde çalıştırın. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Her ayın 28. gününde 6 ' da çalıştırın ("month" **sıklığının** olduğu varsayılarak). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Ayın son gününde 6 ' da Çalıştır.<br /><br />Bir işi ayın son gününde çalıştırmak istiyorsanız, 28, 29, 30 veya 31 gün yerine-1 kullanın. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Her ayın ilk ve son gününde 6 ' da çalışır. |
| `{monthDays":[1,-1]}` |Başlangıç zamanında her ayın ilk ve son gününde çalıştırın. |
| `{monthDays":[1,14]}` |Başlangıç zamanında her ayın ilk ve 14on gününde çalıştırın. |
| `{monthDays":[2]}` |Başlangıç zamanında ayın ikinci gününde çalıştırın. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Her ayın ilk Cuma günü 5 ' te çalışır. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Başlangıç zamanında her ayın ilk Cuma gününde çalıştırın. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Ayın sonundaki üçüncü Cuma günü, Başlangıç zamanında çalıştırın. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Her ayın ilk ve son Cuma günü saat 05.15'te çalıştır. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Başlangıç zamanında her ayın ilk ve son Cuma gününde çalıştırın. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Başlangıç zamanında her ayın beşinci Cuma günü çalıştırın.<br /><br />Ayda beşinci Cuma yoksa, iş çalıştırılmaz. İşi ayın son Cuma günü üzerinde çalıştırmak istiyorsanız, yineleme için 5 yerine-1 kullanabilirsiniz. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Ayın son Cuma günü 15 dakikada bir çalıştır. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Her ayın üçüncü Çarşamba günü 05.15, 05.45, 17.15 ve 17.45’te çalıştır. |

## <a name="see-also"></a>Ayrıca bkz.

* [Azure Scheduler nedir?](scheduler-intro.md)
* [Azure Scheduler kavramları, terminolojisi ve varlık hiyerarşisi](scheduler-concepts-terms.md)
* [Azure Scheduler sınırları, varsayılanları ve hata kodları](scheduler-limits-defaults-errors.md)
