---
title: Azure Service Fabric CLı-sfctl Chaos | Microsoft Docs
description: CLı sfctl Chaos komutlarını Service Fabric açıklar.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: f48ef260ab05f98da99c3ae317d0c350d018119f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901768"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Chaos test hizmetini başlatın, durdurun ve rapor edin.

## <a name="subgroups"></a>Alt
|Atama|Açıklama|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | Chaos zamanlamasını alın ve ayarlayın. |
## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| etkinlikler | Devamlılık belirtecine veya zaman aralığına göre Chaos olaylarının sonraki segmentini alır. |
| Al | Chaos durumunu alır. |
| start | Kümede Chaos 'ı başlatır. |
| durdurulması | Kümede çalışıyorsa, Chaos 'yi durdurulur ve Chaos zamanlamasını durdurulmuş bir duruma koyar. |

## <a name="sfctl-chaos-events"></a>sfctl Chaos olayları
Devamlılık belirtecine veya zaman aralığına göre Chaos olaylarının sonraki segmentini alır.

Chaos olaylarının bir sonraki segmentini almak için ContinuationToken ' ı belirtebilirsiniz. Yeni bir Chaos olayları segmentinin başlamasını sağlamak için StartTimeUtc ve EndTimeUtc aracılığıyla zaman aralığını belirtebilirsiniz. Aynı çağrıda ContinuationToken ve zaman aralığı belirtemezsiniz. 100 ' den fazla Chaos olayı olduğunda, Chaos olayları, bir segmentin en fazla 100 Chaos olayı içerdiği ve sonraki segmenti devam belirteciyle bu API 'ye bir çağrı yaptığınız birden fazla kesimde döndürülür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Devamlılık-belirteç | Devam belirteci parametresi, sonraki sonuç kümesini almak için kullanılır. Boş olmayan bir değere sahip devamlılık belirteci, sistemin sonuçları tek bir yanıta sığmıyor olduğunda API 'nin yanıtına dahil edilir. Bu değer sonraki API çağrısına geçirildiğinde, API sonraki sonuç kümesini döndürür. Başka sonuç yoksa devamlılık belirteci bir değer içermez. Bu parametrenin değeri, URL kodlamalı olmamalıdır. |
| --bitiş-saat-UTC | Bir Chaos raporu oluşturulacak zaman aralığının bitiş saatini temsil eden Windows dosya zamanı. Ayrıntılar için [DateTime. ToFileTimeUtc yöntemine](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) başvurun. |
| --en fazla sonuç | Disk belleği sorgularının bir parçası olarak döndürülecek en fazla sonuç sayısı. Bu parametre döndürülen sonuç sayısı üzerinde üst sınırı tanımlar. Döndürülen sonuçlar, yapılandırmada tanımlanan en fazla ileti boyutu kısıtlamalarına göre iletiye sığmıyor durumunda belirtilen en fazla sonuçtan daha az olabilir. Bu parametre sıfırsa veya belirtilmemişse, disk belleğine alınan sorgu, dönüş iletisine sığan mümkün olduğunca fazla sonuç içerir. |
| --başlangıç-saat-UTC | Bir Chaos raporu oluşturulacak zaman aralığının başlangıç saatini temsil eden Windows dosya süresi. Ayrıntılar için [DateTime. ToFileTimeUtc yöntemine](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) başvurun. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-chaos-get"></a>sfctl Chaos Get
Chaos durumunu alır.

Chaos 'ın çalışıp çalışmadığını gösteren Chaos 'in durumunu, Chaos 'i çalıştırmak için kullanılan Chaos parametrelerini ve Chaos zamanlamasının durumunu öğrenin.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-chaos-start"></a>sfctl Chaos başlatması
Kümede Chaos 'ı başlatır.

Chaos kümede zaten çalıştırılmamakta değilse, geçirilen Chaos parametreleriyle birlikte Chaos başlatılır. Bu çağrı yapıldığında Chaos zaten çalışıyorsa, çağrı FABRIC_E_CHAOS_ALREADY_RUNNING hata kodu ile başarısız olur. Daha fazla ayrıntı için [Service Fabric kümelerinde yer alarak denetlenen Chaos](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) makalesine başvurun.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama-türü-sistem durumu-ilke-eşleme | Belirli uygulama türleri için en fazla sağlıksız uygulama olan JSON kodlu liste. Her giriş, uygulama türü adı ve değer olarak belirtilen uygulama türünün uygulamalarını değerlendirmek için kullanılan Maxyüztunhealthyapplications yüzdesini temsil eden bir tamsayı olarak belirtir. <br><br> Belirli uygulama türleri için en fazla sağlıksız uygulama olan bir eşlemeyi tanımlar. Her giriş, belirtilen uygulama türünün uygulamalarını değerlendirmek için kullanılan Maxyüztunhealthyapplications yüzdesini temsil eden bir tamsayı olarak uygulama türü adı ve değer olarak belirtilir. Uygulama türü sistem durumu ilkesi eşlemesi, özel uygulama türlerini belirtmek için küme durumu değerlendirmesi sırasında kullanılabilir. Haritaya dahil olan uygulama türleri, küme sistem durumu ilkesinde tanımlanan genel Maxyüztunhealthyapplications ile değil, haritada belirtilen yüzdeye göre değerlendirilir. Haritada belirtilen uygulama türleri uygulamaları, genel uygulama havuzunda hesaba katılmaz. Örneğin, bir türden bazı uygulamalar önemliyse, Küme Yöneticisi bu uygulama türü için haritaya bir giriş ekleyebilir ve %0 değeri atayabilir (diğer bir deyişle, herhangi bir hatayla ilgilenmez). Diğer tüm uygulamalar, binlerce uygulama örneğinin dışına çıkan bazı hatalara sızmak için Maxyüztunhealthtoplications ile birlikte %20 olarak ayarlanabilir. Uygulama türü sistem durumu ilkesi eşlemesi yalnızca, küme bildirimi HealthManager/EnableApplicationTypeHealthEvaluation yapılandırma girişini kullanarak uygulama türü sistem durumu değerlendirmesi etkinleştirayarlanırsa kullanılır. |
| --Chaos-Target-Filter | İki dize türü anahtarlı JSON kodlu sözlük. İki anahtar NodeType, ıonlist ve Applicationınary ıonlist ' dir. Bu anahtarların her ikisi için de değerler dize listesidir. chaos_target_filter hedeflenen Chaos hatalarının tüm filtrelerini tanımlar, örneğin, yalnızca belirli düğüm türleri veya hatalı yalnızca belirli uygulamalarda hatalı. <br><br> Chaos_target_filter kullanılmazsa, Chaos tüm küme varlıklarını hata etmez. Chaos_target_filter kullanılırsa, Chaos yalnızca chaos_target_filter belirtimini karşılayan varlıkları hataları. Nodetypeınary ıonlist ve Applicationınary ıonlist yalnızca birleşim semantiğine izin veriyor. Nodetypeınary ıonlist ve Applicationınary ıonlist ' i bir kesişmesi belirtmek mümkün değildir. Örneğin, "Bu uygulama yalnızca bu düğüm türünde olduğunda hata." belirtmek mümkün değildir. Bir varlık Nodetypeary ıonlist veya Applicationary ıonlist öğesine eklendikten sonra, bu varlık ChaosTargetFilter kullanılarak dışlanamaz. ApplicationX, Applicationlarionlist içinde görünmese de, bazı Chaos yineleme applicationX içinde, Nodetypelarionlist içinde yer alan nodeTypeY düğümünde yer aldığı için hata oluşabilir. Hem NodeType, hem de Applicationınary ıonlist boşsa, bir ArgumentException atılır. Tüm hata türleri (düğümü yeniden Başlat, kod paketini yeniden Başlat, çoğaltmayı kaldır, çoğaltmayı yeniden Başlat, birincil taşı ve ikincil taşı) bu düğüm türlerinin düğümleri için etkinleştirilir. Düğüm türü (deyin NodeTypeX) Nodetypeary ıonlist içinde görünmezse, düğüm düzeyi hataları (NodeRestart gibi) NodeTypeX düğümleri için hiçbir şekilde etkinleştirilmez, ancak bu uygulamadaki bir uygulama için kod paketi ve çoğaltma hataları yine de NodeTypeX için etkinleştirilebilir. Applicationlarionlist, NodeTypeX düğümü üzerinde yer alır. En çok 100 düğüm türü adı bu listeye dahil edilebilir, bu sayıyı artırmak için MaxNumberOfNodeTypesInChaosEntityFilter yapılandırması için bir yapılandırma yükseltmesi gerekir. Bu uygulamaların hizmetlerine ait olan tüm çoğaltmalar, Chaos tarafından çoğaltma hatalarını (çoğaltmayı yeniden Başlat, çoğaltmayı kaldırma, birincil taşıma ve ikincil taşıma) düzeltiyor. Chaos yalnızca kod paketi yalnızca bu uygulamaların çoğaltmalarını barındırıyorsa bir kod paketini yeniden başlatabilir. Bir uygulama bu listede görünmezse, uygulama Nodetypeary ıonlist içine dahil edilen düğüm türünün bir düğümünde sonlanıyorsa, bazı Chaos yinelemeyle yine de hatalı olabilir. Ancak, applicationX, yerleştirme kısıtlamaları aracılığıyla nodeTypeY 'e bağlıysa ve applicationX 'in Applicationary ıonlist öğesinden ve nodeTypeY 'in Nodetypeary ıonlist öğesinden Ikisi de yoksa, applicationX hiçbir şekilde hata olmayacaktır. En fazla 1000 uygulama adı bu listeye eklenebilir, bu sayıyı artırmak için MaxNumberOfApplicationsInChaosEntityFilter yapılandırması için bir yapılandırma yükseltmesi gerekir. |
| --bağlam | JSON kodlamalı eşlemesi (dize, dize) tür anahtar-değer çiftleri. Eşleme, Chaos çalıştırması hakkında bilgi kaydetmek için kullanılabilir. Bu tür çiftler üzerinde 100 daha fazla olamaz ve her dize (anahtar veya değer) en fazla 4095 karakter uzunluğunda olabilir. Bu harita, yönetim paketi tarafından, isteğe bağlı olarak belirli bir çalıştırma hakkındaki bağlamı depolamak için bir başlangıç tarafından ayarlanır. |
| --Disable-Move-Replication-faults | Birincil taşıma ve ikincil hataları taşıma ' yı devre dışı bırakır. |
| --Max-küme sabitlemesi | Tüm küme varlıklarının kararlı ve sağlıklı hale gelmesi için beklenecek en uzun süre.  Varsayılan\: 60. <br><br> Chaos, yinelemelerde yürütülür ve her yinelemenin başlangıcında, küme varlıklarının sistem durumunu doğrular. Bir küme varlığı Maxclusteritizationtimeoutınseconds içinde kararlı ve sağlıklı değilse, Chaos doğrulama başarısız bir olay oluşturur. |
| --en fazla-eşzamanlı-hatalar | Yineleme başına en fazla eş zamanlı hata sayısıdır. Yinelemelerdeki Chaos yürütülür ve birbirini izleyen iki yineleme doğrulama aşamasına ayrılmıştır. Eşzamanlılık arttıkça, hata ekleme daha agresif olur ve hataları ortaya çıkarmak için daha karmaşık durumlardan oluşan dizi. Öneri, 2 veya 3 değeri ile başlamalı ve hareket ederken dikkatli bir sorun yaratmaktır.  Varsayılan\: 1. |
| --en fazla% sağlıksız-uygulamalar | Chaos sırasında küme durumunu değerlendirirken, bir hata bildirilmeden önce sistem durumu için izin verilen en yüksek uygulama yüzdesi. <br><br> Bir hata raporlanmadan önce, sağlıksız uygulamaların izin verilen en büyük yüzdesi. Örneğin, uygulamaların %10 ' un sağlıksız olmasını sağlamak için bu değer 10 olur. Yüzde değeri, küme hatalı olarak değerlendirilmeden önce sağlıksız olabilecek uygulamaların en yüksek toleranslı yüzdesini temsil eder. Yüzde oranı dikkate alınmaz ancak en az bir sağlıksız uygulama varsa, sistem durumu uyarı olarak değerlendirilir. Bu, ApplicationTypeHealthPolicyMap 'e dahil edilen uygulama türlerinin uygulamaları hariç, sağlıksız uygulama sayısının kümedeki Toplam uygulama örneği sayısına bölünerek hesaplanır. Hesaplama, küçük sayıda uygulamada bir hata olduğunu kabul etmek için yukarı yuvarlar. Varsayılan yüzde sıfırdır. |
| --en fazla% sağlıksız-düğümler | Chaos sırasında küme durumunu değerlendirirken, bir hata bildirilmeden önce, sağlıksız düğümlerin izin verilen en büyük yüzdesi. <br><br> Bir hata raporlanmadan önce, sağlıksız düğümlerin izin verilen en büyük yüzdesi. Örneğin, düğümlerin %10 ' un sağlıksız olmasını sağlamak için bu değer 10 olur. Yüzde, küme hatalı olarak değerlendirilmeden önce sağlıksız olabilecek düğümlerin maksimum toleranslı yüzdesini temsil eder. Yüzde oranı kabul edilir ancak en az bir sağlıksız düğüm varsa, sistem durumu uyarı olarak değerlendirilir. Yüzde, sağlıksız düğümlerin sayısı kümedeki toplam düğüm sayısına bölünerek hesaplanır. Hesaplama, küçük sayıda düğümde bir hata olduğunu tolerans için yukarı yuvarlar. Varsayılan yüzde sıfırdır. Büyük kümelerde, bazı düğümler her zaman onarım için kapatılacaktır ve bu nedenle bu yüzdenin bu yüzdeyi kabul edecek şekilde yapılandırılması gerekir. |
| --çalıştırılacak süre | Chaos 'ın otomatik olarak durdurulmadan önce çalışacağı toplam süre (saniye cinsinden). İzin verilen en büyük değer 4.294.967.295 ' dir (System. UInt32. MaxValue).  Varsayılan\: 4294967295. |
| --timeout-t | Varsayılan\: 60. |
| --wait-hataları-arasında-hatalar | Tek bir yineleme içindeki ardışık hatalar arasındaki bekleme süresi (saniye cinsinden).  Varsayılan değer 20\:. <br><br> Değer arttıkça, hatalar arasındaki çakışma ve kümenin gittiği durum geçişleri sırası daha basit olur. Öneri, 1 ile 5 arasında bir değer ile başlamalı ve hareket ederken dikkatli davranmaktır. |
| --bekleme-zaman arasında-yinelemeler | Chaos 'nin art arda iki yinelemesi arasındaki zaman ayrımı (saniye cinsinden). Değer arttıkça hata ekleme hızı düşüktür.  Varsayılan değer 30 '\:. |
| --hata olarak uyarı | Uyarıların hatalarla aynı önem derecesine sahip olup olmadığını gösterir. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-chaos-stop"></a>sfctl Chaos durdur
Kümede çalışıyorsa, Chaos 'yi durdurulur ve Chaos zamanlamasını durdurulmuş bir duruma koyar.

Chaos 'in yeni hata yürütmesini durduruyor. Uçuş hatası, tamamlanana kadar yürütmeye devam eder. Geçerli Chaos zamanlaması durdurulmuş bir duruma konur. Bir zamanlama durdurulduktan sonra, durdurulmuş durumda kalır ve Chaos 'in yeni çalıştırmalarını çizelgelemek için kullanılmaz. Zamanlamayı sürdürmesini sağlamak için yeni bir Chaos zamanlaması ayarlanmalıdır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |


## <a name="next-steps"></a>Sonraki adımlar
- Service Fabric CLı 'yi [ayarlayın](service-fabric-cli.md) .
- [Örnek betikleri](/azure/service-fabric/scripts/sfctl-upgrade-application)kullanarak Service Fabric CLI 'nın nasıl kullanılacağını öğrenin.