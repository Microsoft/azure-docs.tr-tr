---
title: Azure Stream Analytics için olay sıralama ilkelerini yapılandırma
description: Bu makalede, Stream Analytics içinde çift sıralama ayarlarını yapılandırma hakkında nasıl gidebileceğinizi açıklanmaktadır
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: f7ec6f32b48a93a29210311c7ba6747eb2e2d066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014304"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Azure Stream Analytics için olay sıralama ilkelerini yapılandırma

Bu makalede, Azure Stream Analytics ' de geç ve sıra dışı olay ilkelerinin nasıl ayarlanacağı ve kullanılacağı açıklanır. Bu ilkeler yalnızca sorgunuzda [zaman DAMGASı by](/stream-analytics-query/timestamp-by-azure-stream-analytics) yan tümcesini kullandığınızda uygulanır ve yalnızca bulut giriş kaynakları için geçerlidir.

## <a name="event-time-and-arrival-time"></a>Olay saati ve geliş zamanı

Stream Analytics işiniz olayları *Olay saati* veya *varış zamanına* göre işleyebilir. Olay **/uygulama saati** olay yükünde (olay oluşturulduğunda) zaman damgasıdır. Giriş **saati** , olay giriş kaynağında (Event Hubs/IoT Hub/BLOB depolama) alındığında zaman damgasıdır. 

Varsayılan olarak, olayları *varış süresine* göre işler Stream Analytics, sorgunuzda [zaman damgası by](/stream-analytics-query/timestamp-by-azure-stream-analytics) yan tümcesini kullanarak olayları *Olay zamanına* göre işlemeyi tercih edebilirsiniz. Geç varış ve sıra dışı ilkeler yalnızca olayları Olay zamanına göre işetmeniz durumunda geçerlidir. Bu ayarları yapılandırırken senaryonuzun gecikme süresi ve doğruluk gereksinimlerini göz önünde bulundurun. 

## <a name="what-is-late-arrival-policy"></a>Geç varış ilkesi nedir?

Bazen olaylar, çeşitli nedenlerden dolayı geç ulaşır. Örneğin, 40 saniye geç ulaşan bir olay olay saati = 00:10:00 ve varış zamanı = 00:10:40 olur. Geç varış ilkesini 15 saniyeye ayarlarsanız, 15 saniyeden daha sonra gelen herhangi bir olay bırakılır (Stream Analytics tarafından işlenmez) veya olay saati ayarlanacaktır. Yukarıdaki örnekte, olay 40 saniye geç (ilke kümesinden daha fazla) ulaştığı için olay saati, en fazla geç varış ilkesi 00:10:25 (varış zamanı-geç varış ilkesi değeri) olarak ayarlanır. Varsayılan geç varışı ilkesi 5 saniyedir.

## <a name="what-is-out-of-order-policy"></a>Sıra dışı ilkesi nedir? 

Olay da sıra dışında gelebilir. Olay saati, geç varış ilkesi temel alınarak ayarlandıktan sonra otomatik olarak bırakmayı veya sıra dışı olayları ayarlamayı da tercih edebilirsiniz. Bu ilkeyi 8 saniye olarak ayarlarsanız, sıra dışında gelen, ancak 8 saniyelik pencere içindeki tüm olaylar olay zamanına göre yeniden sıralanabilir. Daha sonra gelen olaylar bırakılır veya en fazla sıra dışı ilke değerine ayarlanır. Varsayılan sıra dışı ilkesi 0 saniyedir. 

## <a name="adjust-or-drop-events"></a>Olayları ayarlama veya bırakma

Olayları, yapılandırdığınız ilkelere göre geç veya sıraya çıkarak, bu tür olayları (Stream Analytics tarafından işlenmeyecektir) bırakabilir veya olay zamanının ayarlanmasını sağlayabilirsiniz.

Bu ilkelerin eylemde bir örneğini görmemize izin verin.
<br> **Geç varış ilkesi:** 15 saniye
<br> **Sıra dışı ilkesi:** 8 saniye

| Olay No. | Olay saati | Varış saati | System.Timestamp | Açıklama |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Olay, geç ve dış dayanıklılık düzeyine ulaştı. Bu nedenle olay saati, en fazla geç varış toleransı olarak ayarlanır.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Olay geç ulaştı, ancak tolerans düzeyi içinde. Bu nedenle olay saati düzeltilmez.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Olay zamanında geldi. Hiçbir ayarlama gerekmiyor.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Olay, bir sıra dışı geldi, ancak 8 saniyelik tolerans dahilinde. Bu nedenle, olay zamanı düzeltilmez. Analiz amaçları için bu olay, önceki olay numarası 4 olarak değerlendirilir.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Olay, 8 saniyelik sıra dışı ve toleransı dışında geldi. Bu nedenle, olay saati maksimum sıra toleransı olarak ayarlanır. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Bu ayarlar, işimin çıkışını erteleyebilir mi? 

Evet. Varsayılan olarak, sipariş dışı ilkesi sıfır (00 dakika ve 00 saniye) olarak ayarlanır. Varsayılanı değiştirirseniz, işinizin ilk çıktısı bu değer (veya daha fazla) ile gecikilir. 

Girişlerinizin bölümlerinin biri olayları almadıysanız, çıkışlarınızın geç varış ilkesi değeri tarafından geciktirileceğini beklemelisiniz. Nedenini öğrenmek için aşağıdaki ınputpartition hata bölümünü okuyun. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Etkinlik günlüğünden LateInputEvents iletileri görüyorum

Bu iletiler, olayların geç geldiğini ve yapılandırmanıza göre bırakılacağını veya ayarlandığını bilgilendirmek için gösterilir. Geç varış ilkesini uygun şekilde yapılandırdıysanız, bu iletileri yoksayabilirsiniz. 

Bu iletinin örneği: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Etkinlik günlüğimde ınputpartitionnotilerim görüyorum

Giriş kaynağınız (Olay Hub 'ı/IoT Hub) muhtemelen birden çok bölüme sahip. Azure Stream Analytics zaman damgası için yalnızca birleştirilen tüm bölümler T1 sırasında en az T1 sırasında çıkış üretir. Örneğin, sorgunun iki bölüme sahip bir olay hub 'ı bölümünden okuduğunu varsayalım. Bölümlerden birinde, P1, T1 zamanına kadar olayları vardır. P2, diğer bölümde T1 + x zamanına kadar olaylar vardır. Daha sonra çıktı, T1 zamanına kadar üretilir. Ancak PartitionID yan tümcesi tarafından açık bir bölüm varsa, her iki bölüm de bağımsız olarak ilerleme durumu. 

Aynı giriş akışından birden çok bölüm birleştirildiğinde, geç varış toleransı her bölümün yeni veriler için beklediği maksimum süredir. Olay Hub 'ınızda bir bölüm varsa veya IoT Hub giriş almazsa, bu bölümün zaman çizelgesi, geç varış toleransı eşiğine ulaşıncaya kadar ilerlememez. Bu, çıktıyı geç varış toleransı eşiğine göre geciktir. Böyle durumlarda, aşağıdaki iletiyi görebilirsiniz:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Bu ileti, girişte en az bir bölümün boş olduğunu ve çıktıyı geç varış eşiğine göre erteleyip geciktiğini bilgilendirecektir. Bunu aşmak için aşağıdakilerden birini yapmanız önerilir:  
1. Olay Hub 'ınızın/IoT Hub tüm bölümlerinin giriş aldığından emin olun. 
2. Sorgunuzda Partition by PartitionID yan tümcesini kullanın. 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>Geç varış ilkeniz 0 olarak ayarlandığında, neden 5 saniyelik bir gecikme görüyorum?
Hiçbir girişi hiç almamış bir giriş bölümü olduğunda bu oluşur. Bu davranışı doğrulamak için bölüm ile giriş ölçümlerini doğrulayabilirsiniz. 

Bir bölüm, yapılandırılan geç eşiğini aşan bir veri içermiyorsa, Stream Analytics, olay sırası konuları bölümünde açıklandığı gibi uygulama zaman damgasını ilerletir. Bu, tahmini varış süresi gerektirir. Bölüm hiçbir zaman veri almadıysanız, Stream Analytics varış süresini *Yerel Saat-5 saniye* olarak tahmin eder. Hiçbir veri olmayan bu bölümler nedeniyle 5 saniyelik bir eşik gecikmesi gösterebilir.  

## <a name="next-steps"></a>Sonraki adımlar
* [Zaman işleme konusunda dikkat edilmesi gerekenler](stream-analytics-time-handling.md)
* [Stream Analytics kullanılabilen ölçümler](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)