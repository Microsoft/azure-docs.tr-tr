---
title: Azure Batch havuzundaki işlem düğümlerini otomatik olarak ölçeklendirme | Microsoft Docs
description: Havuzdaki işlem düğümlerinin sayısını dinamik olarak ayarlamak için bir bulut havuzunda otomatik ölçeklendirmeyi etkinleştirin.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 10/24/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017,fasttrack-edit
ms.openlocfilehash: ab16fc959a332076cac1d615b86d37e8c66e2f67
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933691"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Batch havuzundaki işlem düğümlerini ölçeklemek için otomatik formül oluşturma

Azure Batch, tanımladığınız parametrelere göre havuzları otomatik olarak ölçeklendirebilir. Otomatik ölçeklendirmeyle, görev talep artdıkça bir havuza dinamik olarak düğüm ekler ve bu işlem düğümleri azaldıkça kaldırır. Batch uygulamanız tarafından kullanılan işlem düğümlerinin sayısını otomatik olarak ayarlayarak zaman ve para tasarrufu yapabilirsiniz.

Bir işlem düğümleri havuzunda, tanımladığınız bir *Otomatik ölçeklendirme formülüyle* ilişkilendirerek otomatik ölçeklendirmeyi etkinleştirirsiniz. Batch hizmeti, iş yükünüzü yürütmek için gereken işlem düğümlerinin sayısını belirlemede otomatik ölçeklendirme formülünü kullanır. İşlem düğümleri ayrılmış düğümler veya [düşük öncelikli düğümler](batch-low-pri-vms.md)olabilir. Toplu işlem, düzenli olarak toplanan Service ölçüm verilerine yanıt verir. Toplu Işlem, bu ölçüm verilerini kullanarak havuzdaki işlem düğümlerinin sayısını Formülünüze ve yapılandırılabilir bir aralığa göre ayarlar.

Bir havuz oluşturulduğunda ya da mevcut bir havuzda otomatik ölçeklendirmeyi etkinleştirebilirsiniz. Ayrıca, otomatik ölçeklendirme için yapılandırılmış bir havuzda var olan bir formülü değiştirebilirsiniz. Batch, formülleri havuzlara atamadan önce değerlendirmenize ve otomatik ölçeklendirme çalıştırmalarının durumunu izlemenize olanak sağlar.

Bu makalede değişkenler, işleçler, işlemler ve işlevler de dahil olmak üzere otomatik ölçeklendirme formüllerinizi oluşturan çeşitli varlıklar ele alınmaktadır. Batch içinde çeşitli işlem kaynağı ve görev ölçümlerinin nasıl alınacağını anladık. Bu ölçümleri, havuzun düğüm sayısını kaynak kullanımı ve görev durumuna göre ayarlamak için kullanabilirsiniz. Daha sonra, Batch REST ve .NET API 'Lerini kullanarak bir havuzda otomatik ölçeklendirmeyi nasıl bir formül oluşturup etkinleştirebiliriz. Son olarak, birkaç örnek formülle bitiyoruz.

> [!IMPORTANT]
> Bir Batch hesabı oluşturduğunuzda, havuzların bir Batch hizmeti aboneliğine mi (varsayılan) yoksa Kullanıcı aboneliğinizde mi ayrılacağını belirleyen [hesap yapılandırmasını](batch-api-basics.md#account)belirtebilirsiniz. Batch hesabınızı varsayılan Batch hizmeti yapılandırmasıyla oluşturduysanız, hesabınız, işlenmek üzere kullanılabilecek en fazla çekirdek sayısıyla sınırlıdır. Batch hizmeti işlem düğümlerini yalnızca bu çekirdek sınırına kadar ölçeklendirir. Bu nedenle, Batch hizmeti bir otomatik ölçeklendirme formülü tarafından belirtilen işlem düğümlerinin hedef sayısına ulaşmayabilir. Hesap kotalarınızı görüntüleme ve artırma hakkında bilgi için bkz. [Azure Batch hizmetine yönelik kotalar ve sınırlar](batch-quota-limit.md) .
>
>Hesabınızı Kullanıcı aboneliği yapılandırmasıyla oluşturduysanız, hesabınız abonelik için çekirdek kotasında paylaşımınızdaymış olursunuz. Daha fazla bilgi için [Azure aboneliği ve hizmet limitleri, kotalar ve kısıtlamalar](../azure-subscription-service-limits.md) sayfasındaki [Sanal Makine limitleri](../azure-subscription-service-limits.md#virtual-machines-limits) bölümüne bakın.
>
>

## <a name="automatic-scaling-formulas"></a>Otomatik ölçeklendirme formülleri

Bir otomatik ölçeklendirme formülü, bir veya daha fazla deyim içeren tanımladığınız bir dize değeridir. Otomatik ölçeklendirme formülü bir havuzun [Otomatik scaleformula][rest_autoscaleformula] öğesine (Batch REST) veya [Cloudpool. otomatik Scaleformula][net_cloudpool_autoscaleformula] özelliğine (Batch .net) atandı. Batch hizmeti, bir sonraki işleme aralığı için havuzdaki işlem düğümlerinin hedef sayısını belirlemede formül kullanır. Formül dizesi 8 KB 'ı aşamaz, noktalı virgülle ayrılmış en fazla 100 deyim içerebilir ve satır sonlarını ve açıklamalarını içerebilir.

Otomatik ölçeklendirme formüllerini, toplu otomatik ölçeklendirme "dili" olarak düşünebilirsiniz. Formül deyimleri, hem hizmet tanımlı değişkenleri (Batch hizmeti tarafından tanımlanan değişkenler), hem de Kullanıcı tanımlı değişkenleri (tanımladığınız değişkenler) içerebilen serbest biçimli ifadelerdir. Yerleşik türler, işleçler ve işlevler kullanarak bu değerler üzerinde çeşitli işlemler gerçekleştirebilir. Örneğin, bir ifade aşağıdaki biçimde değişebilir:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formüller genellikle önceki deyimlerde elde edilen değerler üzerinde işlem gerçekleştiren birden çok deyim içerir. Örneğin, ilk olarak `variable1` ' a bir değer alırız ve sonra `variable2` ' i doldurmak için bir işleve ilettik:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Bir hedef işlem düğümü sayısına ulaşmak için bu ifadeleri otomatik ölçeklendirme Formülünüzde ekleyin. Her düğüm türü için bir hedef tanımlayabilmeniz için adanmış düğümler ve düşük öncelikli düğümlerin her biri kendi hedef ayarlarına sahiptir. Bir otomatik ölçeklendirme formülü, adanmış düğümler için bir hedef değeri, düşük öncelikli düğümler için hedef değeri veya her ikisini içerebilir.

Hedef düğüm sayısı daha yüksek, daha düşük veya havuzdaki bu türdeki düğüm sayısı ile aynı olabilir. Batch, bir havuzun otomatik ölçeklendirme formülünü belirli bir aralıkta değerlendirir (bkz. [Otomatik ölçeklendirme aralıkları](#automatic-scaling-interval)). Batch, havuzdaki her düğüm türünün hedef sayısını, otomatik ölçeklendirme formülünüzün değerlendirme sırasında belirttiği sayı olarak ayarlar.

### <a name="sample-autoscale-formulas"></a>Örnek otomatik ölçeklendirme formülleri

Aşağıda, çoğu senaryo için çalışacak şekilde ayarlanabilecek iki otomatik ölçeklendirme formülü örnekleri verilmiştir. Örnek formüllerde `startingNumberOfVMs` ve `maxNumberofVMs` değişkenleri gereksinimlerinize göre ayarlanabilir.

#### <a name="pending-tasks"></a>Bekleyen görevler

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

Bu otomatik ölçeklendirme formülüyle, havuz başlangıçta tek bir VM ile oluşturulur. `$PendingTasks` ölçümü, çalıştıran veya kuyruğa alınan görevlerin sayısını tanımlar. Formül, son 180 saniye içinde bekleyen görevlerin ortalama sayısını bulur ve `$TargetDedicatedNodes` değişkenini uygun şekilde ayarlar. Formül, ayrılmış düğümlerin hedef sayısının 25 VM 'yi hiçbir şekilde aşmamasını sağlar. Yeni görevler gönderildiğinde havuz otomatik olarak büyür. Görevler tamamlantıkça, VM 'Ler tek tek ve otomatik ölçeklendirme formülü havuzu küçültür.

Bu formül, ayrılmış düğümleri ölçeklendirir, ancak düşük öncelikli düğümleri de ölçeklendirmek için uygulanabilir.

#### <a name="preempted-nodes"></a>Önden düğümler 

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Bu örnek 25 düşük öncelikli düğümlerle başlayan bir havuz oluşturur. Düşük öncelikli bir düğüm geçersiz hale getirilmiştir her seferinde ayrılmış bir düğümle değiştirilmiştir. İlk örnekte olduğu gibi `maxNumberofVMs` değişkeni, havuzun 25 VM 'yi aşmasını önler. Bu örnek, havuzun kullanım ömrü boyunca yalnızca sabit sayıda preemptions oluşmasını sağlayan düşük öncelikli VM 'lerden yararlanmak için yararlıdır.

## <a name="variables"></a>Değişkenler

Otomatik ölçeklendirme formüllerinizde hem **hizmet tanımlı** hem de **Kullanıcı tanımlı** değişkenleri kullanabilirsiniz. Hizmet tanımlı değişkenler Batch hizmetinde yerleşiktir. Bazı hizmet tanımlı değişkenler okuma-yazma ve bazıları salt okunurdur. Kullanıcı tanımlı değişkenler, tanımladığınız değişkenlerdir. Önceki bölümde gösterilen örnek formülde, `$TargetDedicatedNodes` ve `$PendingTasks`, hizmet tanımlı değişkenlerdir. `startingNumberOfVMs` ve `maxNumberofVMs` değişkenleri Kullanıcı tanımlı değişkenlerdir.

> [!NOTE]
> Hizmet tanımlı değişkenlerin başına her zaman bir dolar işareti ($) gelir. Kullanıcı tanımlı değişkenler için dolar işareti isteğe bağlıdır.
>
>

Aşağıdaki tablolarda, Batch hizmeti tarafından tanımlanan okuma-yazma ve salt okuma değişkenleri gösterilmektedir.

Bir havuzdaki işlem düğümlerinin sayısını yönetmek için hizmet tanımlı bu değişkenlerin değerlerini alabilir ve ayarlayabilirsiniz:

| Okuma/yazma hizmeti tanımlı değişkenler | Açıklama |
| --- | --- |
| $TargetDedicatedNodes |Havuz için ayrılmış işlem düğümlerinin hedef sayısı. Ayrılmış düğümlerin sayısı bir hedef olarak belirtilir çünkü bir havuz istenen sayıda düğüm için her zaman ulaşamayabilir. Örneğin, ayrılmış düğümlerin hedef sayısı, havuz ilk hedefe ulaşmadan önce bir otomatik ölçeklendirme değerlendirmesi tarafından değiştirilirse, havuz hedefe ulaşamamış olabilir. <br /><br /> Batch hizmeti yapılandırmasıyla oluşturulan bir hesaptaki havuz, hedef bir Batch hesabı düğümünü veya çekirdek kotayı aşarsa hedefini elde edebilir. Hedef, aboneliğin paylaşılan çekirdek kotasını aşarsa, kullanıcı aboneliği yapılandırmasıyla oluşturulan bir hesaptaki havuz hedefine ulaşamayabilir.|
| $TargetLowPriorityNodes |Havuz için düşük öncelikli işlem düğümlerinin hedef sayısı. Düşük öncelikli düğümlerin sayısı bir hedef olarak belirtilir çünkü bir havuz istenen sayıda düğüm için her zaman ulaşamayabilir. Örneğin, havuz ilk hedefe ulaşmadan önce düşük öncelikli düğümlerin hedef sayısı bir otomatik ölçeklendirme değerlendirmesi tarafından değiştirilirse, havuz hedefe ulaşamamış olabilir. Hedef bir Batch hesabı düğümünü veya çekirdek kotayı aşarsa, bir havuz hedefi de elde edebilir. <br /><br /> Düşük öncelikli işlem düğümleri hakkında daha fazla bilgi için bkz. [Batch ile düşük öncelikli VM 'Ler kullanma](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Bir havuzdan işlem düğümleri kaldırıldığında oluşan eylem. Olası değerler şunlardır:<ul><li>**yeniden kuyruğa alma**--varsayılan değer. Görevleri hemen sonlandırır ve yeniden zamanlanabilmeleri için iş kuyruğuna geri koyar. Bu eylem, hedef sayısının mümkün olduğunca hızlı bir şekilde ulaşmasını sağlar, ancak çalışan tüm görevler kesintiye uğradığında ve yeniden başlatılması gerektiği için daha az etkili olabilir. <li>**Sonlandır**--görevleri hemen sonlandırır ve iş kuyruğundan kaldırır.<li>**taskcompletion**--Şu anda çalışan görevlerin bitmesini bekler ve düğümü havuzdan kaldırır. Görevlerin kesintiye uğratılmasını ve yeniden kuyruğa önlemek için bu seçeneği kullanın. <li>**retaineddata**--düğüm havuzdan kaldırılmadan önce, düğümdeki tüm yerel görev ile korunan verilerin temizlenmesi için bekler.</ul> |

> [!NOTE]
> `$TargetDedicatedNodes` değişkeni, `$TargetDedicated`diğer adı kullanılarak da belirtilebilir. Benzer şekilde, `$TargetLowPriorityNodes` değişkeni, `$TargetLowPriority` diğer adı kullanılarak belirtilebilir. Hem tam olarak adlandırılmış değişken hem de diğer adı formül tarafından ayarlandıysa, tam olarak adlandırılmış değişkene atanan değer öncelikli olur.
>
>

Batch hizmetindeki ölçümleri temel alan ayarlamalar yapmak için, bu hizmet tanımlı değişkenlerin değerini alabilirsiniz:

| Salt okunurdur hizmet tanımlı değişkenler | Açıklama |
| --- | --- |
| $CPUPercent |CPU kullanımının ortalama yüzdesi. |
| $WallClockSeconds |Tüketilen saniye sayısı. |
| $MemoryBytes |Kullanılan ortalama megabayt sayısı. |
| $DiskBytes |Yerel disklerde kullanılan ortalama gigabayt sayısı. |
| $DiskReadBytes |Okunan bayt sayısı. |
| $DiskWriteBytes |Yazılan bayt sayısı. |
| $DiskReadOps |Gerçekleştirilen disk okuma işlemlerinin sayısı. |
| $DiskWriteOps |Gerçekleştirilen yazma diski işlemlerinin sayısı. |
| $NetworkInBytes |Gelen bayt sayısı. |
| $NetworkOutBytes |Giden bayt sayısı. |
| $SampleNodeCount |İşlem düğümlerinin sayısı. |
| $ActiveTasks |Yürütülmeye hazırlanma ancak henüz yürütülmemiş görevlerin sayısı. $ActiveTasks sayısı, etkin durumdaki ve bağımlılıkları karşılanan tüm görevleri içerir. Etkin durumdaki ancak bağımlılıkları karşılanmamış olan tüm görevler $ActiveTasks sayısından çıkarılır.|
| $RunningTasks |Çalışma durumundaki görevlerin sayısı. |
| $PendingTasks |$ActiveTasks ve $RunningTasks toplamı. |
| $SucceededTasks |Başarıyla tamamlanan görevlerin sayısı. |
| $FailedTasks |Başarısız olan görevlerin sayısı. |
| $CurrentDedicatedNodes |Ayrılmış işlem düğümlerinin geçerli sayısı. |
| $CurrentLowPriorityNodes |Önceden yayımlanan tüm düğümler dahil olmak üzere, düşük öncelikli işlem düğümlerinin geçerli sayısı. |
| $PreemptedNodeCount | Havuzdaki ön onay durumundaki düğüm sayısı. |

> [!TIP]
> Önceki tabloda gösterilen salt okunurdur, hizmet tanımlı değişkenler, her biriyle ilişkili verilere erişmek için çeşitli yöntemler sağlayan *nesnelerdir* . Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [örnek veri alma](#getsampledata) bölümüne bakın.
>
>

## <a name="types"></a>Türü

Bu türler bir formülde desteklenir:

* double
* doubleVec
* doubleVecList
* string
* zaman damgası--zaman damgası, aşağıdaki üyeleri içeren bileşik bir yapıdır:

  * yıl
  * ay (1-12)
  * gün (1-31)
  * hafta içi (sayı biçiminde; Örneğin, Pazartesi için 1)
  * Saat (24 saatlik sayı biçiminde; Örneğin, 13, 1 PM anlamına gelir)
  * dakika (00-59)
  * saniye (00-59)
* TimeInterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Operations

Bu işlemlere, önceki bölümde listelenen türlerde izin verilir.

| İşlem | Desteklenen işleçler | Sonuç türü |
| --- | --- | --- |
| Çift *işleç* Double |+, -, *, / |double |
| Double *işleci* TimeInterval |* |TimeInterval |
| Double *Işleci* doubleVec |+, -, *, / |doubleVec |
| doubleVec *işleci* doubleVec |+, -, *, / |doubleVec |
| TimeInterval *işleci* Double |*, / |TimeInterval |
| TimeInterval *işleci* TimeInterval |+, - |TimeInterval |
| TimeInterval *işleci* zaman damgası |+ |timestamp |
| zaman damgası *işleci* TimeInterval |+ |timestamp |
| zaman damgası *işleci* zaman damgası |- |TimeInterval |
| Double *işleci* |-, ! |double |
| *operatör*TimeInterval |- |TimeInterval |
| Çift *işleç* Double |<, < =, = =, > =, >,! = |double |
| dize *işleci* dizesi |<, < =, = =, > =, >,! = |double |
| zaman damgası *işleci* zaman damgası |<, < =, = =, > =, >,! = |double |
| TimeInterval *işleci* TimeInterval |<, < =, = =, > =, >,! = |double |
| Çift *işleç* Double |& &,&#124;&#124; |double |

Üçlü işleç (`double ? statement1 : statement2`) ile bir Double test edilirken, sıfır dışında bir değer **true**ve sıfır **false 'tur**.

## <a name="functions"></a>İşlevler
Bu önceden tanımlanmış **işlevler** , bir otomatik ölçeklendirme formülü tanımlarken kullanabileceğiniz şekilde kullanılabilir.

| İşlev | Dönüş türü | Açıklama |
| --- | --- | --- |
| Ort (doubleVecList) |double |DoubleVecList içindeki tüm değerlerin ortalama değerini döndürür. |
| Len (doubleVecList) |double |DoubleVecList 'ten oluşturulan vector öğesinin uzunluğunu döndürür. |
| LG (çift) |double |Double 'un günlük taban 2 değerini döndürür. |
| LG (doubleVecList) |doubleVec |DoubleVecList 'in bileşen tabanlı günlük tabanı 2 ' i döndürür. Parametresi için bir VEC (Double) açıkça geçirilmesi gerekir. Aksi halde, çift LG (Double) sürümü varsayılır. |
| LN (Double) |double |Double 'un doğal günlüğünü döndürür. |
| LN (doubleVecList) |doubleVec |Double 'un doğal günlüğünü döndürür. |
| günlük (çift) |double |Double 'un günlük tabanı 10 ' ı döndürür. |
| günlük (doubleVecList) |doubleVec |DoubleVecList 'in bileşen temelinde günlük tabanı 10 ' ü döndürür. Tek Çift parametresi için bir VEC (Double) açıkça geçirilmesi gerekir. Aksi takdirde, Çift günlük (Double) sürümü varsayılır. |
| Max (doubleVecList) |double |DoubleVecList içindeki maksimum değeri döndürür. |
| Min (doubleVecList) |double |DoubleVecList içindeki minimum değeri döndürür. |
| norm (doubleVecList) |double |DoubleVecList 'ten oluşturulan vektörün iki normini döndürür. |
| yüzdebirlik (doubleVec v, Double p) |double |Vektör v 'nin yüzdebirlik öğesini döndürür. |
| S_SAYI_ÜRET () |double |0,0 ile 1,0 arasında rastgele bir değer döndürür. |
| Aralık (doubleVecList) |double |DoubleVecList içindeki min ve Max değerleri arasındaki farkı döndürür. |
| STD (doubleVecList) |double |DoubleVecList içindeki değerlerin örnek standart sapmasını döndürür. |
| Stop () | |Otomatik ölçeklendirme ifadesinin değerlendirmesini sonlandırır. |
| Sum (doubleVecList) |double |DoubleVecList 'in tüm bileşenlerinin toplamını döndürür. |
| Time (dize dateTime = "") |timestamp |Hiçbir parametre geçirilmemişse, geçerli zamanın zaman damgasını veya geçirilse de dateTime dizesinin zaman damgasını döndürür. Desteklenen dateTime biçimleri W3C-DTF ve RFC 1123 ' dir. |
| Val (doubleVec v, Double ı) |double |Bir başlangıç dizini olan, vektör v 'de i konumunda olan öğenin değerini döndürür. |

Önceki tabloda açıklanan işlevlerden bazıları bağımsız değişken olarak bir liste kabul edebilir. Virgülle ayrılmış liste, *Double* ve *doubleVec*'ın herhangi bir birleşimidir. Örnek:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*Doubleveclist* değeri, değerlendirmeden önce tek bir *doubleVec* dönüştürülür. Örneğin, `v = [1,2,3]`, `avg(v)` çağrısı `avg(1,2,3)`çağırma ile eşdeğerdir. `avg(v, 7)` çağırmak, `avg(1,2,3,7)`çağırma ile eşdeğerdir.

## <a name="getsampledata"></a>Örnek verileri al

Otomatik ölçeklendirme formülleri, Batch hizmeti tarafından sağlanan ölçüm verileri (örnekler) üzerinde çalışır. Bir formül, hizmetten aldığı değerlere göre havuz boyutunu büyürken veya küçültür. Daha önce açıklanan hizmet tanımlı değişkenler, bu nesneyle ilişkili verilere erişmek için çeşitli yöntemler sağlayan nesnelerdir. Örneğin, aşağıdaki ifade, son beş dakikalık CPU kullanımını almak için bir istek gösterir:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Yöntem | Açıklama |
| --- | --- |
| GetSample () |`GetSample()` yöntemi, veri örneklerinin bir vektörünü döndürür.<br/><br/>Örnek, ölçüm verilerinin 30 saniye değerinde değeridir. Diğer bir deyişle, her 30 saniyede bir örnek elde edilir. Ancak aşağıda belirtildiği gibi, bir örneğin toplanması ve formül için kullanılabilir olduğu zaman arasında bir gecikme vardır. Bu nedenle, belirli bir süre için tüm örnekler, bir formülün değerlendirmesi için kullanılabilir olabilir.<ul><li>`doubleVec GetSample(double count)`<br/>Toplanan en son örneklerden elde edilecek örneklerin sayısını belirtir.<br/><br/>`GetSample(1)`, kullanılabilir son örneği döndürür. Ancak, `$CPUPercent` gibi ölçümler için, örneğin *ne zaman* toplandığını öğrenmek imkansız olduğundan bu kullanılmamalıdır. Bu, son zamanlarda veya sistem sorunları nedeniyle daha eski olabilir. Bu tür durumlarda aşağıda gösterildiği gibi bir zaman aralığı kullanılması daha iyidir.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Örnek verilerin toplanması için bir zaman çerçevesi belirtir. İsteğe bağlı olarak, istenen zaman çerçevesinde kullanılabilir olması gereken örneklerin yüzdesini de belirtir.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`, son 10 dakikalık tüm örneklerin CPUPercent geçmişine mevcutsa 20 örnek döndürür. Geçmişin son dakikası kullanılabilir değilse, ancak yalnızca 18 örnek döndürülür. Bu durumda:<br/><br/>örneklerin yalnızca yüzde 90 ' unun kullanılabilir olması nedeniyle `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` başarısız olur.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` başarılı olur.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Veri toplamaya yönelik bir zaman çerçevesini, hem başlangıç saati hem de bitiş saati ile belirtir.<br/><br/>Yukarıda belirtildiği gibi, bir örneğin toplanması ve bir formül için kullanılabilir olduğu zaman arasında bir gecikme vardır. `GetSample` yöntemini kullandığınızda bu gecikmeyi göz önünde bulundurun. Aşağıya `GetSamplePercent` ' a bakın. |
| GetSamplePeriod () |Bir geçmiş örnek veri kümesinde alınan örneklerin dönemini döndürür. |
| Count () |Ölçüm geçmişindeki toplam örnek sayısını döndürür. |
| Geçmiş BeginTime () |Ölçüm için kullanılabilir en eski veri örneğinin zaman damgasını döndürür. |
| GetSamplePercent () |Belirli bir zaman aralığı için kullanılabilen örneklerin yüzdesini döndürür. Örnek:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Döndürülen örneklerin yüzdesi belirtilen `samplePercent` ' den az olduğunda `GetSample` yöntemi başarısız olduğundan, ilk olarak denetlemek için `GetSamplePercent` yöntemini kullanabilirsiniz. Daha sonra, otomatik ölçeklendirme değerlendirmesini durdurmadan, yetersiz örnek varsa, alternatif bir eylem yapabilirsiniz. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Örnekler, örnek yüzdesi ve *Getsample ()* yöntemi
Otomatik ölçeklendirme formülünün temel işlemi, görev ve kaynak ölçümü verilerini almak ve ardından bu verilere göre havuz boyutunu ayarlamasıdır. Bu nedenle, otomatik ölçeklendirme formüllerinin ölçüm verileri (örnekler) ile nasıl etkileşime gireceğini net bir şekilde anlamak önemlidir.

**Örnekler**

Batch hizmeti düzenli aralıklarla görev ve kaynak ölçümlerinin örneklerini alır ve bunları otomatik ölçeklendirme formülleriniz için kullanılabilir hale getirir. Bu örnekler Batch hizmeti tarafından her 30 saniyede bir kaydedilir. Ancak, bu örneklerin ne zaman kaydedildiği ve ne zaman otomatik ölçeklendirme formülleriniz için kullanılabilir hale getirildikleri (ve tarafından okunabilir) arasında bir gecikme vardır. Ayrıca, ağ veya diğer altyapı sorunları gibi çeşitli faktörlerden dolayı, örnekler belirli bir aralığa kaydedilmeyebilir.

**Örnek yüzdesi**

`samplePercent` `GetSample()` yöntemine geçirildiğinde veya `GetSamplePercent()` yöntemi çağrıldığında, _yüzde_ , Batch hizmeti tarafından kaydedilen toplam olası örnek sayısı ve sizin için kullanılabilir örnek sayısı arasında bir karşılaştırmaya başvurur. Otomatik ölçeklendirme formülü.

Örnek olarak 10 dakikalık bir TimeSpan bölümüne bakalım. Örnekler 10 dakikalık bir TimeSpan içinde her 30 saniyede bir kaydedildiğinden, Batch tarafından kaydedilen en fazla örnek sayısı 20 örnek (dakika başına 2) olacaktır. Ancak, raporlama mekanizmasından ve Azure 'daki diğer sorunların devralınan gecikmesi nedeniyle, okuma için otomatik ölçeklendirme formülünüzün kullanabildiği yalnızca 15 örnek olabilir. Bu nedenle, örneğin, bu 10 dakikalık dönem için, Formülünüzde kaydedilen toplam örnek sayısının yalnızca %75 ' u kullanılabilir olabilir.

**GetSample () ve örnek aralıklar**

Otomatik ölçeklendirme formülleriniz, havuzlarınızın büyümesini ve daraltılması &mdash; düğüm ekleme veya düğümleri kaldırma. Düğümlerin maliyeti parasal olduğundan, formüllerin yeterli verileri temel alan akıllı bir analiz yöntemi kullandığından emin olmak istersiniz. Bu nedenle, formüllerinizde popüler bir tür Analizi kullanmanızı öneririz. Bu tür, toplanan örnek aralığına göre havuzlarınızı büyür ve küçültür.

Bunu yapmak için `GetSample(interval look-back start, interval look-back end)` kullanarak bir örnek vektörü döndürün:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Yukarıdaki satır Batch tarafından değerlendirildiğinde, değerlerin vektörü olarak bir dizi örnek döndürür. Örnek:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Örneklerin vektörünü topladıktan sonra, toplanan aralıktan anlamlı değerler türetmek için `min()`, `max()` ve `avg()` gibi işlevleri kullanabilirsiniz.

Belirli bir süre için belirli bir örnek yüzdeden daha az kullanılabilir olduğunda, ek güvenlik için bir formül değerlendirmesini başarısız olarak zorlayabilirsiniz. Bir formül değerlendirmesinin başarısız olmasına zorlarsanız, belirtilen örnek yüzdesi yoksa, toplu Işin formülün daha fazla değerlendirmesini durdurmasını söyleyebilirsiniz. Bu durumda, havuz boyutu üzerinde değişiklik yapılmaz. Değerlendirmenin başarılı olması için gerekli bir örnek yüzdesi belirtmek için, `GetSample()` ' a üçüncü parametre olarak belirtin. Burada, örnek yüzde 75 ' lik bir gereksinim belirtilir:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Örnek kullanılabilirliğinde bir gecikme olabileceğinden, bir dakikadan daha eski bir geri arama başlangıç zamanına sahip bir zaman aralığı belirlemek önemlidir. Örneklerin sistem aracılığıyla yayılması yaklaşık bir dakika sürer, bu nedenle aralıktaki örnekler `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` kullanılamıyor olabilir. Yine, belirli bir örnek yüzdesi gereksinimini zorlamak için `GetSample()` ' ın yüzde parametresini kullanabilirsiniz.

> [!IMPORTANT]
> **Otomatik ölçeklendirme formüllerinizde *yalnızca* `GetSample(1)` ' e bağlı kalmamak** **kesinlikle önerilir** . Bunun nedeni `GetSample(1)` ' ın, toplu Iş hizmetine ne kadar önce elde ettiğinize bakılmaksızın "sahip olduğunuz son örneği vermesini" sağlar. Yalnızca tek bir örnek olduğundan ve bu, eski bir örnek olabileceğinden, son görevin veya kaynak durumunun daha büyük resmini temsil edemeyebilir. `GetSample(1)`kullanıyorsanız, formülün bağımlı olduğu tek veri noktası değil, daha büyük bir deyimin parçası olduğundan emin olun.
>
>

## <a name="metrics"></a>Ölçümler

Bir formül tanımlarken hem kaynak hem de görev ölçümlerini kullanabilirsiniz. Havuzdaki ayrılmış düğümlerin hedef sayısını, elde ettiğiniz ve değerlendirdiğiniz ölçüm verilerine göre ayarlayabilirsiniz. Her ölçüm hakkında daha fazla bilgi için yukarıdaki [değişkenler](#variables) bölümüne bakın.

<table>
  <tr>
    <th>Ölçüm</th>
    <th>Açıklama</th>
  </tr>
  <tr>
    <td><b>Kaynak</b></td>
    <td><p>Kaynak ölçümleri CPU, bant genişliği, işlem düğümlerinin bellek kullanımı ve düğüm sayısını temel alır.</p>
        <p> Bu hizmet tanımlı değişkenler, düğüm sayısına göre ayarlamalar yapmak için faydalıdır:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Bu hizmet tanımlı değişkenler, düğüm kaynak kullanımına göre ayarlamalar yapmak için yararlıdır:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Görev</b></td>
    <td><p>Görev ölçümleri, etkin, beklemede ve tamamlandı gibi görevlerin durumuna göre yapılır. Aşağıdaki hizmet tanımlı değişkenler, görev ölçümlerine göre havuz boyutu ayarlamaları yapmak için yararlıdır:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Bir otomatik ölçeklendirme formülü yaz

Yukarıdaki bileşenleri kullanan deyimler oluşturarak bir otomatik ölçeklendirme formülü oluşturursunuz ve ardından bu deyimleri bir bütün formülde birleştirir. Bu bölümde, bazı gerçek dünyada ölçekleme kararları gerçekleştirebilen örnek bir otomatik ölçeklendirme formülü oluşturacağız.

İlk olarak, yeni otomatik ölçeklendirme formülünüzün gereksinimlerini tanımlayalim. Formül şu şekilde olmalıdır:

1. CPU kullanımı yüksekse, bir havuzdaki adanmış işlem düğümlerinin hedef sayısını artırın.
1. CPU kullanımı düşük olduğunda bir havuzdaki adanmış işlem düğümlerinin hedef sayısını azaltın.
1. En fazla adanmış düğüm sayısını 400 olarak kısıtla.
1. Düğüm sayısını azaltırken, görevleri çalıştıran düğümleri kaldırmayın; gerekirse, düğümleri kaldırmak için görevler bitene kadar bekleyin.

Yüksek CPU kullanımı sırasında düğümlerin sayısını artırmak için, Kullanıcı tanımlı bir değişkeni (`$totalDedicatedNodes`), ayrılmış düğümlerin geçerli hedef sayısı% 110 olan bir değerle dolduran, ancak yalnızca son 10 ' da en düşük ortalama CPU kullanımı olan bir değere sahip olan ifadeyi tanımlayın dakikalar yüzde 70 ' den fazla. Aksi takdirde, geçerli ayrılmış düğüm sayısı için değerini kullanın.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Düşük CPU kullanımı sırasında adanmış düğümlerin sayısını *azaltmak* için, önceki 60 dakika IÇINDE ortalama CPU kullanımı yüzde 20 ' nin altında olduğunda, formülümüzdeki sonraki ifade aynı `$totalDedicatedNodes` değişkenini geçerli hedef sayısı için yüzde 90 olarak ayarlar. Aksi takdirde, yukarıdaki ifadede doldurulduğumuz `$totalDedicatedNodes` ' ın geçerli değerini kullanın.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Artık ayrılmış işlem düğümlerinin hedef sayısını en fazla 400 olarak sınırlandırın:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Formülün tamamı aşağıda verilmiştir:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Batch SDK 'Ları ile otomatik ölçeklendirme özellikli havuz oluşturma

Havuz otomatik ölçeklendirme, Batch [SDK 'ları](batch-apis-tools.md#azure-accounts-for-batch-development), Batch [REST API](https://docs.microsoft.com/rest/api/batchservice/) [Batch POWERSHELL cmdlet 'leri](batch-powershell-cmdlets-get-started.md)ve [Batch CLI](batch-cli-get-started.md)kullanılarak yapılandırılabilir. Bu bölümde hem .NET hem de Python örnekleri görebilirsiniz.

### <a name="net"></a>.NET

.NET ' te otomatik ölçeklendirme özelliği etkinleştirilmiş bir havuz oluşturmak için aşağıdaki adımları izleyin:

1. [Batchclient. PoolOperations. createpool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool)ile havuzu oluşturun.
1. [Cloudpool. oto Scaleenabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) özelliğini `true` olarak ayarlayın.
1. [Cloudpool. otomatik scaleformula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) özelliğini otomatik ölçeklendirme formülünüz ile ayarlayın.
1. Seçim [Cloudpool. AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) özelliğini ayarlayın (varsayılan değer 15 dakikadır).
1. Havuzu [Cloudpool. COMMIT](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) veya [commınsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync)ile işleyin.

Aşağıdaki kod parçacığı, .NET 'te otomatik ölçeklendirme özellikli bir havuz oluşturur. Havuzun otomatik ölçeklendirme formülü, ayrılmış düğümlerin hedef sayısını Mondays üzerinde 5 ve haftanın her gününde 1 olarak ayarlar. [Otomatik ölçeklendirme aralığı](#automatic-scaling-interval) 30 dakikaya ayarlanır. Bu makalede ve bu makaledeki C# diğer kod parçacıklarında `myBatchClient`, [batchclient][net_batchclient] sınıfının düzgün başlatılmış bir örneğidir.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Otomatik ölçeklendirme etkinleştirilmiş bir havuz oluşturduğunuzda, **createpool**çağrısında targetlowlııo _düğümler_ parametresini veya _targetlowprioritynodes_ parametresini belirtmeyin. Bunun yerine, havuzda **etkin** ve **oto scaleformula** özelliklerini belirtin. Bu özelliklerin değerleri her düğüm türünün hedef sayısını tespit. Ayrıca, otomatik ölçeklendirme etkin bir havuzu el ile yeniden boyutlandırmak için (örneğin, [Batchclient. PoolOperations. ResizePoolAsync][net_poolops_resizepoolasync]ile), önce havuzda otomatik ölçeklendirmeyi **devre dışı bırakın** , sonra yeniden boyutlandırın.
>
>

#### <a name="automatic-scaling-interval"></a>Otomatik ölçeklendirme aralığı

Varsayılan olarak, Batch hizmeti bir havuzun boyutunu 15 dakikada bir otomatik ölçeklendirme formülüne göre ayarlar. Bu Aralık, aşağıdaki havuz özellikleri kullanılarak yapılandırılabilir:

* [Cloudpool. AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .net)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

Minimum Aralık beş dakikadır ve en fazla 168 saat olur. Bu aralığın dışında bir Aralık belirtilmişse, Batch hizmeti hatalı bir Istek (400) hatası döndürür.

> [!NOTE]
> Otomatik ölçeklendirme Şu anda bir dakikadan kısa bir sürede değişikliklere yanıt vermeye yönelik değildir, ancak bunun yerine iş yükünü çalıştırırken havuzunuzun boyutunu kademeli olarak ayarlamayı amaçlanır.
>
>

### <a name="python"></a>Python

Benzer şekilde, Python SDK ile otomatik ölçeklendirme özellikli bir havuzu şu şekilde yapabilirsiniz:

1. Bir havuz oluşturun ve yapılandırmasını belirtin.
1. Havuzu hizmet istemcisine ekleyin.
1. Yazdığınız bir formülle havuzda otomatik ölçeklendirmeyi etkinleştirin.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18; 
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5; 
             $isWorkingWeekdayHour = $workHours && $isWeekday; 
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10), 
                                            pool_enable_auto_scale_options=None, 
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Python SDK 'Yı kullanmaya yönelik daha fazla örnek, GitHub 'daki [Batch Python hızlı başlangıç deposunda](https://github.com/Azure-Samples/batch-python-quickstart) bulunabilir.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Mevcut bir havuzda otomatik ölçeklendirmeyi etkinleştir

Her Batch SDK 'Sı otomatik ölçeklendirmeyi etkinleştirmek için bir yol sağlar. Örnek:

* [Batchclient. PoolOperations. Enableoto Scaleasync][net_enableautoscaleasync] (Batch .net)
* [Bir havuzda otomatik ölçeklendirmeyi etkinleştir][rest_enableautoscale] (REST API)

Varolan bir havuzda otomatik ölçeklendirmeyi etkinleştirdiğinizde aşağıdaki noktaları göz önünde bulundurun:

* Otomatik ölçeklendirmeyi etkinleştirme isteği bu havuzda devre dışı bırakılmışsa, isteği keserken geçerli bir otomatik ölçeklendirme formülü belirtmeniz gerekir. İsteğe bağlı olarak otomatik ölçek değerlendirme aralığı belirtebilirsiniz. Bir Aralık belirtmezseniz, varsayılan 15 dakikalık bir değer kullanılır.
* Otomatik ölçeklendirme Şu anda havuzda etkinse, bir otomatik ölçeklendirme formülü, bir değerlendirme aralığı veya her ikisini de belirtebilirsiniz. Bu özelliklerden en az birini belirtmeniz gerekir.

  * Yeni bir otomatik ölçek değerlendirme aralığı belirtirseniz, var olan değerlendirme zamanlaması durdurulur ve yeni bir zamanlama başlatılır. Yeni zamanlamanın başlangıç zamanı, otomatik ölçeklendirmeyi etkinleştirme isteğinin verildiği süredir.
  * Otomatik ölçeklendirme formülünü veya değerlendirme aralığını atlarsanız, Batch hizmeti bu ayarın geçerli değerini kullanmaya devam eder.

> [!NOTE]
> .NET 'te havuz oluştururken veya başka bir dildeki karşılaştırılabilir parametreler için, **createpool** yönteminin *targetlowlıo düğümleri* veya *targetlowprioritynodes* parametreleri için değerler belirttiyseniz, bu değerler Otomatik ölçeklendirme formülü değerlendirildiğinde yok sayılır.
>
>

Bu C# kod parçacığı, mevcut bir havuzda otomatik ölçeklendirmeyi etkinleştirmek için [Batch .net][net_api] kitaplığını kullanır:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Otomatik ölçeklendirme formülünü güncelleştirme

Varolan bir otomatik ölçeklendirme havuzundaki formülü güncelleştirmek için, yeni formülle tekrar ölçeklendirmeyi etkinleştirmek üzere işlemi çağırın. Örneğin, otomatik ölçeklendirme ' de zaten etkinse `myexistingpool` aşağıdaki .NET kodu yürütüldüğünde, otomatik ölçeklendirme formülü `myNewFormula` ' in içeriğiyle değiştirilmiştir.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Otomatik ölçeklendirme aralığını güncelleştirme

Varolan bir otomatik ölçeklendirme havuzunun otomatik ölçeklendirme değerlendirme aralığını güncelleştirmek için, yeni aralığa göre otomatik ölçeklendirmeyi yeniden etkinleştirmek üzere işlemi çağırın. Örneğin, .NET 'te zaten otomatik ölçeklendirme özellikli bir havuz için otomatik ölçeklendirme değerlendirme aralığını 60 dakika olarak ayarlamak için:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Otomatik ölçeklendirme formülünü değerlendir

Bir formülü bir havuza uygulamadan önce değerlendirebilirsiniz. Bu şekilde, formülü üretime eklemeden önce deyimlerinin nasıl değerlendirmekte olduğunu görmek için formülü test edebilirsiniz.

Bir otomatik ölçeklendirme formülünü değerlendirmek için, önce geçerli bir formülle havuzda otomatik ölçeklendirmeyi etkinleştirmeniz gerekir. Henüz otomatik ölçeklendirmeyi etkin olmayan bir havuzda bir formülü test etmek için, otomatik ölçeklendirmeyi etkinleştirdiğinizde, tek satırlık formül `$TargetDedicatedNodes = 0` ' ı kullanın. Ardından, test etmek istediğiniz formülü değerlendirmek için aşağıdakilerden birini kullanın:

* [Batchclient. PoolOperations. EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) veya [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Bu Batch .NET yöntemleri, var olan bir havuzun KIMLIĞINI ve değerlendirmek için otomatik ölçeklendirme formülünü içeren bir dizeyi gerektirir.

* [Otomatik ölçeklendirme formülünü değerlendir](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Bu REST API isteğinde, URI 'de havuz KIMLIĞINI ve istek gövdesinin *Otomatik Scaleformula* öğesindeki otomatik ölçeklendirme formülünü belirtin. İşlemin yanıtı, formülle ilişkili olabilecek hata bilgilerini içerir.

Bu [Batch .net][net_api] kod parçacığında, bir otomatik ölçeklendirme formülünü değerlendiririz. Havuzun otomatik ölçeklendirilmesi etkin değilse, önce bu ayarı etkinleştireceğiz.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Bu kod parçacığında gösterilen formülün başarılı değerlendirmesi şuna benzer sonuçlar üretir:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Otomatik ölçeklendirme çalıştırmaları hakkında bilgi alın

Formülünüzün beklendiği gibi çalıştığından emin olmak için, toplu Işin havuzunuzdaki gerçekleştirdiği otomatik ölçeklendirme çalıştırmalarının sonuçlarını düzenli olarak kontrol etmenizi öneririz. Bunu yapmak için, havuzun başvurusunu alın (veya yenileyin) ve son otomatik ölçeklendirme çalıştırmasının özelliklerini inceleyin.

Batch .NET 'te [cloudpool. otomatik Scalerun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) özelliğinin, havuzda gerçekleştirilen en son otomatik ölçeklendirme çalıştırması hakkında bilgi sağlayan birkaç özelliği vardır:

* [Otomatik Scalerun. zaman damgası](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [Otomatik Scalerun. sonuçları](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [Otomatik Scalerun. hatası](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

REST API, [bir havuz hakkında bilgi alma](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) isteği, otomatik ölçeklendirme çalıştırma bilgilerini [Otomatik](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) olarak otomatik ölçeklendirme özelliğini içeren havuz hakkındaki bilgileri döndürür.

Aşağıdaki C# kod parçacığı, havuz _mypool_üzerinde çalışan son otomatik ölçeklendirme hakkında bilgi yazdırmak için Batch .net kitaplığını kullanır:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Önceki kod parçacığının örnek çıktısı:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Örnek otomatik ölçeklendirme formülleri

Bir havuzdaki işlem kaynakları miktarını ayarlamak için farklı yollar gösteren birkaç formüle göz atalım.

### <a name="example-1-time-based-adjustment"></a>Örnek 1: zamana dayalı ayarlama

Havuz boyutunu haftanın günü ve günün saati temelinde ayarlamak istediğinizi varsayalım. Bu örnek, havuzdaki düğümlerin sayısını uygun şekilde artırma veya azaltma işlemlerinin nasıl yapılacağını gösterir.

Formül ilk olarak geçerli saati edinir. Bu haftanın günü (1-5) ve çalışma saatleri (8-6 ila 6) ise, hedef havuz boyutu 20 düğüm olarak ayarlanır. Aksi takdirde, 10 düğüm olarak ayarlanır.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-2-task-based-adjustment"></a>Örnek 2: görev tabanlı ayarlama

Bu örnekte, havuz boyutu kuyruktaki görev sayısına göre ayarlanır. Açıklamalar ve satır sonları formül dizelerinde kabul edilebilir.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Örnek 3: paralel görevler için hesaplama

Bu örnek, görev sayısına göre havuz boyutunu ayarlar. Bu formül Ayrıca, havuz için ayarlanmış olan [MaxTasksPerComputeNode][net_maxtasks] değerini de hesaba girer. Bu yaklaşım, havuzunuzdaki [paralel görev yürütmenin](batch-parallel-node-tasks.md) etkinleştirildiği durumlarda faydalıdır.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Örnek 4: ilk havuz boyutunu ayarlama

Bu örnek, bir C# ilk zaman aralığı için havuz boyutunu belirtilen sayıda düğüm olarak ayarlayan bir otomatik ölçeklendirme formülüne sahip bir kod parçacığını gösterir. Ardından, ilk süre geçtikten sonra, çalışan ve etkin görev sayısına göre havuz boyutunu ayarlar.

Aşağıdaki kod parçacığındaki formül:

* İlk havuz boyutunu dört düğüme ayarlar.
* Havuzun yaşam döngüsünün ilk 10 dakikası içinde havuz boyutunu ayarlamaz.
* 10 dakika sonra, son 60 dakika içinde çalışan ve etkin görev sayısının en büyük değerini alır.
  * Her iki değer de 0 ise (son 60 dakika içinde hiçbir görevin çalışmadığını veya etkin olmadığını belirten), havuz boyutu 0 olarak ayarlanır.
  * Her iki değer sıfırdan büyükse hiçbir değişiklik yapılmaz.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Sonraki adımlar

* [Eşzamanlı düğüm görevleriyle Azure Batch işlem kaynak kullanımını en üst düzeye çıkarmak](batch-parallel-node-tasks.md) , havuzunuzdaki işlem düğümlerinde birden çok görevi eşzamanlı olarak nasıl yürütebileceğinizi gösteren ayrıntılar içerir. Otomatik ölçeklendirmeye ek olarak, bu özellik bazı iş yükleri için iş süresini azaltmaya yardımcı olabilir, böylece paradan tasarruf sağlayabilirsiniz.
* Başka bir verimlilik güçlendirici için Batch uygulamanızın Batch hizmetini en iyi şekilde sorguladığı için emin olun. Potansiyel olarak binlerce işlem düğümü veya görev durumunu sorguladığınızda, Tel kesen veri miktarını sınırlamayı öğrenmek için [Azure Batch hizmetini verimli bir şekilde sorgulama](batch-efficient-list-queries.md) bölümüne bakın.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
