---
title: Azure Batch havuzundaki işlem düğümlerini otomatik ölçeklendirme
description: Havuzdaki işlem düğümlerinin sayısını dinamik olarak ayarlamak için bir bulut havuzunda otomatik ölçeklendirmeyi etkinleştirin.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: f70c29f0e8a313233991c7363dc4b8a41a1b1cd5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389375"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Batch havuzundaki işlem düğümlerini ölçeklemek için otomatik formül oluşturma

Azure Batch tanımladığınız parametrelere göre havuzları otomatik olarak ölçeklendirebilir, zaman ve para tasarrufu sağlayabilirsiniz. Otomatik ölçeklendirmeyle, görev talep artdıkça bir havuza dinamik olarak düğüm ekler ve görev talepleri azaldıkça işlem düğümlerini kaldırır.

İşlem düğümleri havuzunda otomatik ölçeklendirmeyi etkinleştirmek için, havuzu tanımladığınız bir *Otomatik ölçeklendirme formülüyle* ilişkilendirirsiniz. Batch hizmeti, iş yükünüzü yürütmek için kaç düğüm gerektiğini belirlemekte otomatik ölçeklendirme formülünü kullanır. Bu düğümler ayrılmış düğümler veya [düşük öncelikli düğümler](batch-low-pri-vms.md)olabilir. Toplu işlem, hizmet ölçümleri verilerini düzenli aralıklarla gözden geçirir ve formülünüzü ve tanımladığınız bir aralığa göre havuzdaki düğümlerin sayısını ayarlamak için kullanır.

Bir havuz oluşturduğunuzda otomatik ölçeklendirmeyi etkinleştirebilir veya mevcut bir havuza uygularsınız. Batch, formülleri havuzlara atamadan önce değerlendirmenize ve otomatik ölçeklendirme çalıştırmalarının durumunu izlemenize olanak sağlar. Otomatik ölçeklendirmeyle bir havuz yapılandırdıktan sonra formülde daha sonra değişiklikler yapabilirsiniz.

> [!IMPORTANT]
> Bir Batch hesabı oluşturduğunuzda, havuzların bir Batch hizmeti aboneliğine mi (varsayılan) yoksa Kullanıcı aboneliğinizde mi ayrılacağını belirleyen [havuz ayırma modunu](accounts.md)belirtebilirsiniz. Batch hesabınızı varsayılan Batch hizmeti yapılandırmasıyla oluşturduysanız, hesabınız, işlenmek üzere kullanılabilecek en fazla çekirdek sayısıyla sınırlıdır. Batch hizmeti işlem düğümlerini yalnızca bu çekirdek sınırına kadar ölçeklendirir. Bu nedenle, Batch hizmeti bir otomatik ölçeklendirme formülü tarafından belirtilen işlem düğümlerinin hedef sayısına ulaşmayabilir. Hesap kotalarınızı görüntüleme ve artırma hakkında bilgi için bkz. [Azure Batch hizmetine yönelik kotalar ve sınırlar](batch-quota-limit.md) .
>
>Hesabınızı Kullanıcı aboneliği moduyla oluşturduysanız, hesabınız abonelik için çekirdek kotasında paylaşımdadır. Daha fazla bilgi için [Azure aboneliği ve hizmet limitleri, kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md) sayfasındaki [Sanal Makine limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) bölümüne bakın.

## <a name="autoscale-formulas"></a>Otomatik ölçeklendirme formülleri

Bir otomatik ölçeklendirme formülü, bir veya daha fazla deyim içeren tanımladığınız bir dize değeridir. Otomatik ölçeklendirme formülü bir havuzun [Otomatik scaleformula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) öğesine (Batch REST) veya [Cloudpool. otomatik Scaleformula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) özelliğine (Batch .net) atandı. Batch hizmeti, bir sonraki işleme aralığı için havuzdaki işlem düğümlerinin hedef sayısını belirlemede formül kullanır. Formül dizesi 8 KB 'ı geçemez, noktalı virgülle ayrılmış en fazla 100 deyim içerebilir ve satır sonlarını ve açıklamalarını içerebilir.

Otomatik ölçeklendirme formüllerini, toplu otomatik ölçeklendirme "dili" olarak düşünebilirsiniz. Formül deyimleri, hizmet tanımlı değişkenleri (Batch hizmeti tarafından tanımlanan) ve Kullanıcı tanımlı değişkenleri içerebilen, serbest biçimli ifadelerdir. Formüller, yerleşik türler, işleçler ve işlevler kullanarak bu değerler üzerinde çeşitli işlemler gerçekleştirebilir. Örneğin, bir ifade aşağıdaki biçimde değişebilir:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formüller genellikle önceki deyimlerde elde edilen değerler üzerinde işlem gerçekleştiren birden çok deyim içerir. Örneğin, ilk olarak için bir değer alırız `variable1` ve sonra doldurmak üzere bir işleve ilettik `variable2` :

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Bir hedef işlem düğümü sayısına ulaşmak için bu ifadeleri otomatik ölçeklendirme Formülünüzde ekleyin. Adanmış düğümler ve düşük öncelikli düğümlerin her biri kendi hedef ayarlarına sahiptir. Bir otomatik ölçeklendirme formülü, adanmış düğümler için bir hedef değeri, düşük öncelikli düğümler için hedef değeri veya her ikisini içerebilir.

Hedef düğüm sayısı daha yüksek, daha düşük veya havuzdaki bu türdeki düğüm sayısı ile aynı olabilir. Batch, belirli bir [Otomatik ölçeklendirme aralıklarında](#automatic-scaling-interval)bir havuzun otomatik ölçeklendirme formülünü değerlendirir. Batch, havuzdaki her düğüm türünün hedef sayısını, otomatik ölçeklendirme formülünüzün değerlendirme sırasında belirttiği sayı olarak ayarlar.

### <a name="sample-autoscale-formulas"></a>Örnek otomatik ölçeklendirme formülleri

Aşağıda, çoğu senaryo için çalışacak şekilde ayarlanabilecek iki otomatik ölçeklendirme formülü örnekleri verilmiştir. Değişkenler `startingNumberOfVMs` ve `maxNumberofVMs` örnek formüllerde gereksinimlerinize göre ayarlanabilir.

#### <a name="pending-tasks"></a>Bekleyen görevler

Bu otomatik ölçeklendirme formülüyle, havuz başlangıçta tek bir VM ile oluşturulur. `$PendingTasks`Ölçüm, çalıştıran veya kuyruğa alınan görevlerin sayısını tanımlar. Formül, son 180 saniye içinde bekleyen görevlerin ortalama sayısını bulur ve `$TargetDedicatedNodes` değişkeni uygun şekilde ayarlar. Formül, ayrılmış düğümlerin hedef sayısının 25 VM 'yi hiçbir şekilde aşmamasını sağlar. Yeni görevler gönderildiğinde havuz otomatik olarak büyür. Görevler tamamlantıkça, VM 'Ler ücretsizdir ve otomatik ölçeklendirme formülü havuzu küçültür.

Bu formül, ayrılmış düğümleri ölçeklendirir, ancak düşük öncelikli düğümleri de ölçeklendirmek için uygulanabilir.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Önden düğümler

Bu örnek 25 düşük öncelikli düğümlerle başlayan bir havuz oluşturur. Düşük öncelikli bir düğüm geçersiz hale getirilmiştir her seferinde ayrılmış bir düğümle değiştirilmiştir. İlk örnekte olduğu gibi, değişken, `maxNumberofVMs` havuzun 25 VM 'yi aşmasını önler. Bu örnek, havuzun kullanım ömrü boyunca yalnızca sabit sayıda preemptions oluşmasını sağlayan düşük öncelikli VM 'lerden yararlanmak için yararlıdır.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

[Otomatik ölçeklendirme formülleri oluşturma](#write-an-autoscale-formula) ve bu konunun ilerleyen kısımlarında ek [örnek otomatik ölçeklendirme formülleri](#example-autoscale-formulas) hakkında daha fazla bilgi edineceksiniz.

## <a name="variables"></a>Değişkenler

Otomatik ölçeklendirme formüllerinizde hem **hizmet tanımlı** hem de **Kullanıcı tanımlı** değişkenleri kullanabilirsiniz.

Hizmet tanımlı değişkenler Batch hizmetinde yerleşiktir. Bazı hizmet tanımlı değişkenler okuma-yazma ve bazıları salt okunurdur.

Kullanıcı tanımlı değişkenler, tanımladığınız değişkenlerdir. Yukarıda gösterilen örnek formülde `$TargetDedicatedNodes` ve `$PendingTasks` hizmet tanımlı değişkenlerdir ve `startingNumberOfVMs` `maxNumberofVMs` Kullanıcı tanımlı değişkenlerdir.

> [!NOTE]
> Hizmet tanımlı değişkenlerin başına her zaman bir dolar işareti ($) gelir. Kullanıcı tanımlı değişkenler için dolar işareti isteğe bağlıdır.

Aşağıdaki tablolarda Batch hizmeti tarafından tanımlanan okuma-yazma ve salt okuma değişkenleri gösterilmektedir.

### <a name="read-write-service-defined-variables"></a>Okuma/yazma hizmeti tanımlı değişkenler

Bir havuzdaki işlem düğümlerinin sayısını yönetmek için bu hizmet tanımlı değişkenlerin değerlerini alabilir ve ayarlayabilirsiniz.

| Değişken | Açıklama |
| --- | --- |
| $TargetDedicatedNodes |Havuz için ayrılmış işlem düğümlerinin hedef sayısı. Bir havuz istenen sayıda düğüm için her zaman ulaşamadığından, bu bir hedef olarak belirtilir. Örneğin, ayrılmış düğümlerin hedef sayısı, havuz ilk hedefe ulaşmadan önce bir otomatik ölçeklendirme değerlendirmesi tarafından değiştirilirse, havuz hedefe ulaşamamış olabilir. <br /><br /> Batch hizmeti modunda oluşturulan bir hesaptaki havuz, hedef bir Batch hesabı düğümünü veya çekirdek kotayı aşarsa hedefine ulaşamayabilir. Hedef, aboneliğin paylaşılan çekirdek kotasını aşarsa, kullanıcı aboneliği modunda oluşturulan bir hesaptaki havuz hedefine ulaşamayabilir.|
| $TargetLowPriorityNodes |Havuz için düşük öncelikli işlem düğümlerinin hedef sayısı. Bu, bir havuz istenen sayıda düğüm için her zaman ulaşamadığı için bir hedef olarak belirtilir. Örneğin, havuz ilk hedefe ulaşmadan önce düşük öncelikli düğümlerin hedef sayısı bir otomatik ölçeklendirme değerlendirmesi tarafından değiştirilirse, havuz hedefe ulaşamamış olabilir. Hedef bir Batch hesabı düğümünü veya çekirdek kotayı aşarsa, bir havuz hedefi de elde edebilir. <br /><br /> Düşük öncelikli işlem düğümleri hakkında daha fazla bilgi için bkz. [Batch ile düşük öncelikli VM 'Ler kullanma](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Bir havuzdan işlem düğümleri kaldırıldığında oluşan eylem. Olası değerler şunlardır:<ul><li>**yeniden kuyruğa alma**: varsayılan değer. Görevleri hemen sonlandırır ve yeniden zamanlanabilmeleri için iş kuyruğuna geri koyar. Bu eylem, hedef sayıda düğüme mümkün olduğunca çabuk ulaşılmasını sağlar. Ancak, çalışan tüm görevler kesintiye uğradığında ve tamamen yeniden başlatılması gerektiği için, bu daha az verimli olabilir. <li>**Sonlandır**: görevleri hemen sonlandırır ve iş kuyruğundan kaldırır.<li>**taskcompletion**: Şu anda çalışan görevlerin bitmesini bekler ve düğümü havuzdan kaldırır. Görevlerin kesintiye uğratılmasını ve yeniden kuyruğa karşı çalışmasını önlemek için bu seçeneği kullanın.<li>**retaineddata**: düğüm havuzdan kaldırılmadan önce düğümdeki tüm yerel görev ile korunan verilerin temizlenmesi bekler.</ul> |

> [!NOTE]
> `$TargetDedicatedNodes`Değişken, diğer ad kullanılarak da belirtilebilir `$TargetDedicated` . Benzer şekilde, `$TargetLowPriorityNodes` değişken diğer ad kullanılarak belirtilebilir `$TargetLowPriority` . Hem tam olarak adlandırılmış değişken hem de diğer adı formül tarafından ayarlandıysa, tam olarak adlandırılmış değişkene atanan değer öncelikli olur.

### <a name="read-only-service-defined-variables"></a>Salt okunurdur hizmet tanımlı değişkenler

Batch hizmetindeki ölçümleri temel alan ayarlamalar yapmak için, bu hizmet tanımlı değişkenlerin değerini alabilirsiniz.

> [!IMPORTANT]
> İş bırakma görevleri şu anda $ActiveTasks ve $PendingTasks gibi görev sayıları sağlayan değişkenlere dahil edilmez. Otomatik ölçeklendirme Formülünüze bağlı olarak, bu, iş bırakma görevlerinin çalıştırılabilmesi için kullanılabilir düğüm olmadan düğümlerin kaldırılmasına neden olabilir.

| Değişken | Açıklama |
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
| $ActiveTasks |Yürütülmeye hazırlanma ancak henüz yürütülmemiş görevlerin sayısı. Bu, etkin durumdaki ve bağımlılıkları karşılanan tüm görevleri içerir. Etkin durumdaki ancak bağımlılıkları karşılanmamış olan tüm görevler $ActiveTasks sayısından çıkarılır. Çok örnekli bir görev için $ActiveTasks, görevde ayarlanan örnek sayısını içerecektir.|
| $RunningTasks |Çalışma durumundaki görevlerin sayısı. |
| $PendingTasks |$ActiveTasks ve $RunningTasks toplamı. |
| $SucceededTasks |Başarıyla tamamlanan görevlerin sayısı. |
| $FailedTasks |Başarısız olan görevlerin sayısı. |
| $TaskSlotsPerNode |Havuzdaki tek bir işlem düğümünde eşzamanlı görevleri çalıştırmak için kullanılabilen görev Yuvaları sayısı. |
| $CurrentDedicatedNodes |Ayrılmış işlem düğümlerinin geçerli sayısı. |
| $CurrentLowPriorityNodes |Etkin olmayan düğümler de dahil olmak üzere, düşük öncelikli işlem düğümlerinin geçerli sayısı. |
| $PreemptedNodeCount | Havuzdaki bir durumda olan düğüm sayısı. |

> [!TIP]
> Bu salt okunurdur hizmet tanımlı değişkenler, her biriyle ilişkili verilere erişmek için çeşitli yöntemler sağlayan *nesnelerdir* . Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [örnek veri alma](#obtain-sample-data) bölümüne bakın.

> [!NOTE]
> `$RunningTasks`Bir zaman noktasında çalışan görev sayısına göre ölçeklendirirken ve `$ActiveTasks` çalışmak üzere kuyruğa alınan görev sayısına göre ölçeklendirilirken kullanın.

## <a name="types"></a>Türler

Otomatik ölçeklendirme formülleri aşağıdaki türleri destekler:

- double
- doubleVec
- doubleVecList
- string
- zaman damgası--aşağıdaki üyeleri içeren bileşik bir yapı:
  - yıl
  - ay (1-12)
  - gün (1-31)
  - hafta içi (sayı biçiminde; Örneğin, Pazartesi için 1)
  - Saat (24 saatlik sayı biçiminde; Örneğin, 13, 1 PM anlamına gelir)
  - dakika (00-59)
  - saniye (00-59)
- TimeInterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

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
| *operatör* TimeInterval |- |TimeInterval |
| Çift *işleç* Double |<, <=, = =, >=, >,! = |double |
| dize *işleci* dizesi |<, <=, = =, >=, >,! = |double |
| zaman damgası *işleci* zaman damgası |<, <=, = =, >=, >,! = |double |
| TimeInterval *işleci* TimeInterval |<, <=, = =, >=, >,! = |double |
| Çift *işleç* Double |&&,  &#124;&#124; |double |

Üçlü işleç () ile bir Double test edilirken `double ? statement1 : statement2` , sıfır dışında bir değer **true** ve sıfır **false 'tur**.

## <a name="functions"></a>İşlevler

Bir otomatik ölçeklendirme formülü tanımlarken, önceden tanımlanmış bu **işlevleri** kullanabilirsiniz.

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
| rand() |double |0,0 ile 1,0 arasında rastgele bir değer döndürür. |
| Aralık (doubleVecList) |double |DoubleVecList içindeki min ve Max değerleri arasındaki farkı döndürür. |
| STD (doubleVecList) |double |DoubleVecList içindeki değerlerin örnek standart sapmasını döndürür. |
| Stop () | |Otomatik ölçeklendirme ifadesinin değerlendirmesini sonlandırır. |
| Sum (doubleVecList) |double |DoubleVecList 'in tüm bileşenlerinin toplamını döndürür. |
| Time (dize dateTime = "") |timestamp |Hiçbir parametre geçirilmemişse, geçerli zamanın zaman damgasını veya varsa dateTime dizesinin zaman damgasını döndürür. Desteklenen dateTime biçimleri W3C-DTF ve RFC 1123 ' dir. |
| Val (doubleVec v, Double ı) |double |Bir başlangıç dizini olan, vektör v 'de i konumunda olan öğenin değerini döndürür. |

Önceki tabloda açıklanan işlevlerden bazıları bağımsız değişken olarak bir liste kabul edebilir. Virgülle ayrılmış liste, *Double* ve *doubleVec*'ın herhangi bir birleşimidir. Örnek:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*Doubleveclist* değeri, değerlendirmeden önce tek bir *doubleVec* dönüştürülür. Örneğin, `v = [1,2,3]` daha sonra çağrısı, `avg(v)` çağırma ile eşdeğerdir `avg(1,2,3)` . Çağırma `avg(v, 7)` , çağırma ile eşdeğerdir `avg(1,2,3,7)` .

## <a name="metrics"></a>Ölçümler

Bir formül tanımlarken hem kaynak hem de görev ölçümlerini kullanabilirsiniz. Havuzdaki ayrılmış düğümlerin hedef sayısını, elde ettiğiniz ve değerlendirdiğiniz ölçüm verilerine göre ayarlayabilirsiniz. Her ölçüm hakkında daha fazla bilgi için yukarıdaki [değişkenler](#variables) bölümüne bakın.

<table>
  <tr>
    <th>Metric</th>
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

## <a name="obtain-sample-data"></a>Örnek verileri al

Otomatik ölçeklendirme formülünün temel işlemi, görev ve kaynak ölçüm verileri (örnekler) almak ve ardından bu verilere göre havuz boyutunu ayarlamasıdır. Bu nedenle, otomatik ölçeklendirme formüllerin örneklerle nasıl etkileşime gireceğini net bir şekilde anlamak önemlidir.

### <a name="methods"></a>Yöntemler

Otomatik ölçeklendirme formülleri, Batch hizmeti tarafından sağlanan ölçüm verilerinin örneklerine göre davranır. Bir formül, aldığı değerlere göre havuz boyutunu büyüyerek veya küçültecektir. Hizmet tanımlı değişkenler, bu nesneyle ilişkili verilere erişmek için yöntemler sağlayan nesnelerdir. Örneğin, aşağıdaki ifade, son beş dakikalık CPU kullanımını almak için bir istek gösterir:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

Aşağıdaki yöntemler, hizmet tanımlı değişkenlerle ilgili örnek verileri elde etmek için kullanılabilir.

| Yöntem | Açıklama |
| --- | --- |
| GetSample () |`GetSample()`Yöntemi, veri örneklerinin bir vektörünü döndürür.<br/><br/>Örnek, ölçüm verilerinin 30 saniye değerinde değeridir. Diğer bir deyişle, her 30 saniyede bir örnek elde edilir. Ancak aşağıda belirtildiği gibi, bir örneğin toplanması ve formül için kullanılabilir olduğu zaman arasında bir gecikme vardır. Bu nedenle, belirli bir süre için tüm örnekler, bir formülün değerlendirmesi için kullanılabilir olabilir.<ul><li>`doubleVec GetSample(double count)`: Toplanan en son örneklerden elde edilecek örneklerin sayısını belirtir. `GetSample(1)` kullanılabilir son örneği döndürür. Ancak, gibi ölçümler için, örneğin `$CPUPercent` `GetSample(1)` *ne zaman* toplandığını bildirmek imkansız olduğundan, kullanılmamalıdır. Bu, son zamanlarda veya sistem sorunları nedeniyle daha eski olabilir. Böyle durumlarda, aşağıda gösterildiği gibi bir zaman aralığı kullanmak daha iyidir.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Örnek veri toplamak için bir zaman çerçevesi belirtir. İsteğe bağlı olarak, istenen zaman çerçevesinde kullanılabilir olması gereken örneklerin yüzdesini de belirtir. Örneğin, `$CPUPercent.GetSample(TimeInterval_Minute * 10)` geçmişte son 10 dakikalık tüm örnekler varsa 20 örnek döndürür `CPUPercent` . Geçmişin son dakikası kullanılabilir değilse, yalnızca 18 örnek döndürülür. Bu durumda `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` , örneklerin yalnızca yüzde 90 ' unun kullanılabilir olduğundan, ancak başarılı olması nedeniyle başarısız olur `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` .<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Bir başlangıç saati ve bitiş saati ile veri toplamaya yönelik zaman çerçevesini belirtir. Yukarıda belirtildiği gibi, bir örneğin toplanması ve bir formül için kullanılabilir hale gelmesi arasında bir gecikme vardır. Yöntemini kullandığınızda bu gecikmeyi göz önünde bulundurun `GetSample` . `GetSamplePercent`Aşağıya bakın. |
| GetSamplePeriod () |Bir geçmiş örnek veri kümesinde alınan örneklerin dönemini döndürür. |
| Count () |Ölçüm geçmişindeki toplam örnek sayısını döndürür. |
| Geçmiş BeginTime () |Ölçüm için kullanılabilir en eski veri örneğinin zaman damgasını döndürür. |
| GetSamplePercent () |Belirli bir zaman aralığı için kullanılabilen örneklerin yüzdesini döndürür. Örneğin, `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. `GetSample`Döndürülen örnek yüzdesi belirtilen değerden küçükse yöntem başarısız olduğundan `samplePercent` , `GetSamplePercent` ilk olarak denetlemek için yöntemini kullanabilirsiniz. Daha sonra, otomatik ölçeklendirme değerlendirmesini durdurmadan, yetersiz örnek varsa, alternatif bir eylem yapabilirsiniz. |

### <a name="samples"></a>Örnekler

Batch hizmeti düzenli aralıklarla görev ve kaynak ölçümlerinin örneklerini alır ve bunları otomatik ölçeklendirme formülleriniz için kullanılabilir hale getirir. Bu örnekler Batch hizmeti tarafından her 30 saniyede bir kaydedilir. Ancak, bu örneklerin ne zaman kaydedildiği ve ne zaman otomatik ölçeklendirme formülleriniz için kullanılabilir hale getirildikleri (ve tarafından okunabilir) arasında bir gecikme vardır. Ayrıca, ağ veya diğer altyapı sorunları gibi faktörlerden dolayı belirli bir Aralık için örnekler kaydedilmeyebilir.

### <a name="sample-percentage"></a>Örnek yüzdesi

`samplePercent` `GetSample()` Yöntemine geçirildiğinde veya `GetSamplePercent()` yöntemi çağrıldığında _yüzde_ , Batch hizmeti tarafından kaydedilen toplam olası örnek sayısı ve otomatik ölçeklendirme formülünüzün kullanabildiği örnek sayısı arasındaki bir karşılaştırmaya başvurur.

Örnek olarak 10 dakikalık bir TimeSpan bölümüne bakalım. Bu 10 dakikalık zaman aralığında her 30 saniyede bir kaydedildiğinden, toplu Iş tarafından kaydedilen en fazla örnek sayısı 20 örnek (dakika başına 2) olacaktır. Ancak, raporlama mekanizmasından ve Azure 'daki diğer sorunların devralınan gecikmesi nedeniyle, okuma için otomatik ölçeklendirme formülünüzün kullanabildiği yalnızca 15 örnek olabilir. Bu nedenle, örneğin, bu 10 dakikalık dönem için, Formülünüzde kaydedilen toplam örnek sayısının yalnızca %75 ' u kullanılabilir olabilir.

### <a name="getsample-and-sample-ranges"></a>GetSample () ve örnek aralıklar

Otomatik ölçeklendirme formülleriniz, düğüm ekleyerek veya kaldırarak havuzlarınızı büyüyerek daraltabilir. Düğümlerin maliyeti paradan geldiğinden, formüllerinizin yeterli verileri temel alan akıllı bir analiz yöntemi kullandığından emin olun. Formüllerinizde popüler bir tür Analizi kullanmanızı öneririz. Bu tür, toplanan örnek aralığına göre havuzlarınızı büyür ve küçültür.

Bunu yapmak için, `GetSample(interval look-back start, interval look-back end)` bir örnek vektörü döndürmek üzere kullanın:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Yukarıdaki satır Batch tarafından değerlendirildiğinde, değerlerin vektörü olarak bir dizi örnek döndürür. Örnek:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Örneklerin vektörünü topladıktan sonra,, ve gibi işlevleri kullanarak `min()` `max()` `avg()` toplanan aralıktan anlamlı değerler türetebilirsiniz.

Belirli bir süre için belirli bir örnek yüzdeden daha az kullanılabilir olduğunda, ek güvenlik için bir formül değerlendirmesini başarısız olarak zorlayabilirsiniz. Bir formül değerlendirmesinin başarısız olmasına zorlarsanız, belirtilen örnek yüzdesi yoksa, toplu Işin formülün daha fazla değerlendirmesini durdurmasını söyleyebilirsiniz. Bu durumda, havuz boyutu üzerinde değişiklik yapılmaz. Değerlendirmenin başarılı olması için gerekli bir örnek yüzdesi belirtmek için, bunu üçüncü parametresi olarak belirtin `GetSample()` . Burada, örnek yüzde 75 ' lik bir gereksinim belirtilir:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Örnek kullanılabilirliğinde bir gecikme olabileceğinden, her zaman bir dakikadan daha eski bir geri arama başlangıç saatine sahip bir zaman aralığı belirtmeniz gerekir. Örneklerin sistem aracılığıyla yayılması yaklaşık bir dakika sürer, bu nedenle aralıktaki örnekler `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` kullanılamayabilir. Yine, `GetSample()` belirli bir örnek yüzdesi gereksinimini zorlamak için Percentage parametresini kullanabilirsiniz.

> [!IMPORTANT]
> ***Yalnızca* `GetSample(1)` Otomatik ölçeklendirme formüllerinizde bağlı olmasını önlemenize** kesinlikle tavsiye ederiz. Bunun nedeni, `GetSample(1)` temel olarak Batch hizmetine, "sizin ne kadar süre önce ne kadar süreyle elde ettiğinize bakılmaksızın" sahip olduğunuz son örneği bana vermektir. Yalnızca tek bir örnek olduğundan ve bu, eski bir örnek olabileceğinden, son görevin veya kaynak durumunun daha büyük resmini temsil edemeyebilir. Kullanıyorsanız `GetSample(1)` , formülün bağımlı olduğu tek veri noktası değil daha büyük bir deyimin parçası olduğundan emin olun.

## <a name="write-an-autoscale-formula"></a>Bir otomatik ölçeklendirme formülü yaz

Yukarıdaki bileşenleri kullanan deyimler oluşturarak bir otomatik ölçeklendirme formülü oluşturursunuz ve ardından bu deyimleri bir bütün formülde birleştirir. Bu bölümde, gerçek dünyada ölçekleme kararları gerçekleştirebilen ve ayarlamalar yapabileceği örnek bir otomatik ölçeklendirme formülü oluşturacağız.

İlk olarak, yeni otomatik ölçeklendirme formülünüzün gereksinimlerini tanımlayalim. Formül şu şekilde olmalıdır:

- CPU kullanımı yüksekse, bir havuzdaki adanmış işlem düğümlerinin hedef sayısını artırın.
- CPU kullanımı düşük olduğunda bir havuzdaki adanmış işlem düğümlerinin hedef sayısını azaltın.
- En fazla adanmış düğüm sayısını 400 olarak kısıtla.
- Düğüm sayısını azaltırken, görevleri çalıştıran düğümleri kaldırmayın; gerekirse, düğümleri kaldırmadan önce görevler bitene kadar bekleyin.

Formülümüzdeki ilk ifade, yüksek CPU kullanımı sırasında düğüm sayısını artırır. Kullanıcı tanımlı bir değişkeni ( `$totalDedicatedNodes` ) ayrılmış düğümlerin geçerli hedef sayısının yüzde 110 değeri ile dolduran bir ifade tanımlayacağız, ancak yalnızca son 10 dakika boyunca minimum ortalama CPU kullanımı yüzde 70 ' den fazla olursa. Aksi takdirde, geçerli ayrılmış düğüm sayısı için değeri kullanır.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Düşük CPU kullanımı sırasında adanmış düğümlerin sayısını azaltmak için, formülümüzdeki bir sonraki ifade, `$totalDedicatedNodes` son 60 dakika içinde ortalama CPU kullanımı yüzde 20 ' nin altındaysa, aynı değişkeni ayrılmış düğümlerin geçerli hedef sayısının yüzde 90 ' si olarak ayarlar. Aksi takdirde, `$totalDedicatedNodes` Yukarıdaki ifadede doldurulduğumuz geçerli değerini kullanır.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Artık, ayrılmış işlem düğümlerinin hedef sayısını maksimum 400 olarak sınırlayacağız.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Son olarak, görevleri tamamlanana kadar düğümlerin kaldırılmadığından emin olacaksınız.

```
$NodeDeallocationOption = taskcompletion;
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
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> ' I seçerseniz, formül dizelerinde hem açıklamaları hem de satır sonlarını ekleyebilirsiniz. Ayrıca, eksik noktalı virgüllerin, değerlendirme hatalarına neden olabileceğini unutmayın.

## <a name="automatic-scaling-interval"></a>Otomatik ölçeklendirme aralığı

Varsayılan olarak, Batch hizmeti bir havuzun boyutunu 15 dakikada bir otomatik ölçeklendirme formülüne göre ayarlar. Bu Aralık, aşağıdaki havuz özellikleri kullanılarak yapılandırılabilir:

- [Cloudpool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .net)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Minimum Aralık beş dakikadır ve en fazla 168 saat olur. Bu aralığın dışında bir Aralık belirtilmişse, Batch hizmeti hatalı bir Istek (400) hatası döndürür.

> [!NOTE]
> Otomatik ölçeklendirme Şu anda bir dakikadan kısa bir sürede değişikliklere yanıt vermeye yönelik değildir, ancak bunun yerine iş yükünü çalıştırırken havuzunuzun boyutunu kademeli olarak ayarlamayı amaçlanır.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Batch SDK 'Ları ile otomatik ölçeklendirme özellikli havuz oluşturma

Havuz otomatik ölçeklendirme, Batch [SDK 'ları](batch-apis-tools.md#azure-accounts-for-batch-development), Batch [REST API](/rest/api/batchservice/) [Batch POWERSHELL cmdlet 'leri](batch-powershell-cmdlets-get-started.md)ve [Batch CLI](batch-cli-get-started.md)kullanılarak yapılandırılabilir. Bu bölümde hem .NET hem de Python örnekleri görebilirsiniz.

### <a name="net"></a>.NET

.NET ' te otomatik ölçeklendirme özelliği etkinleştirilmiş bir havuz oluşturmak için aşağıdaki adımları izleyin:

1. [Batchclient. PoolOperations. createpool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool)ile havuzu oluşturun.
1. [Cloudpool. oto Scaleenabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) özelliğini olarak ayarlayın `true` .
1. [Cloudpool. otomatik scaleformula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) özelliğini otomatik ölçeklendirme formülünüz ile ayarlayın.
1. Seçim [Cloudpool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) özelliğini ayarlayın (varsayılan değer 15 dakikadır).
1. Havuzu [Cloudpool. COMMIT](/dotnet/api/microsoft.azure.batch.cloudpool.commit) veya [commınsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync)ile işleyin.

Aşağıdaki örnek .NET 'te otomatik ölçeklendirme özellikli bir havuz oluşturur. Havuzun otomatik ölçeklendirme formülü, ayrılmış düğümlerin hedef sayısını Mondays üzerinde 5 ve haftanın her gününde 1 olarak ayarlar. [Otomatik ölçeklendirme aralığı](#automatic-scaling-interval) 30 dakikaya ayarlanır. Bu makalede ve bu makaledeki diğer C# kod parçacıkları, `myBatchClient` [batchclient](/dotnet/api/microsoft.azure.batch.batchclient) sınıfının düzgün başlatılmış bir örneğidir.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    VirtualMachineConfiguration: new VirtualMachineConfiguration(
                        imageReference: new ImageReference(
                                            publisher: "MicrosoftWindowsServer",
                                            offer: "WindowsServer",
                                            sku: "2019-datacenter-core",
                                            version: "latest"),
                        nodeAgentSkuId: "batch.node.windows amd64");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Otomatik ölçeklendirme etkinleştirilmiş bir havuz oluşturduğunuzda, **createpool** çağrısında _targetlownodes_ parametresini veya _targetlowprioritynodes_ parametresini belirtmeyin. Bunun yerine, havuzda **etkin** ve **oto scaleformula** özelliklerini belirtin. Bu özelliklerin değerleri her düğüm türünün hedef sayısını tespit.
>
> Otomatik ölçeklendirme etkinleştirilmiş bir havuzu el ile yeniden boyutlandırmak için (örneğin, [Batchclient. PoolOperations. ResizePoolAsync](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)ile), önce havuzda otomatik ölçeklendirmeyi devre dışı bırakmanız, sonra yeniden boyutlandırılması gerekir.

### <a name="python"></a>Python

Python SDK ile otomatik ölçeklendirme özellikli havuz oluşturmak için:

1. Bir havuz oluşturun ve yapılandırmasını belirtin.
1. Havuzu hizmet istemcisine ekleyin.
1. Yazdığınız bir formülle havuzda otomatik ölçeklendirmeyi etkinleştirin.

Aşağıdaki örnekte bu adımlar gösterilmektedir.

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

## <a name="enable-autoscaling-on-an-existing-pool"></a>Mevcut bir havuzda otomatik ölçeklendirmeyi etkinleştir

Her Batch SDK 'Sı otomatik ölçeklendirmeyi etkinleştirmek için bir yol sağlar. Örnek:

- [Batchclient. PoolOperations. Enableoto Scaleasync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .net)
- [Bir havuzda otomatik ölçeklendirmeyi etkinleştir](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Varolan bir havuzda otomatik ölçeklendirmeyi etkinleştirdiğinizde aklınızda bulundurun:

- Otomatik ölçeklendirme Şu anda havuzda devre dışıysa, isteği keserken geçerli bir otomatik ölçeklendirme formülü belirtmeniz gerekir. İsteğe bağlı olarak otomatik ölçeklendirme aralığı belirtebilirsiniz. Bir Aralık belirtmezseniz, varsayılan 15 dakikalık bir değer kullanılır.
- Otomatik ölçeklendirme Şu anda havuzda etkinse, yeni bir formül, yeni bir Aralık veya her ikisini de belirtebilirsiniz. Bu özelliklerden en az birini belirtmeniz gerekir.
  - Yeni bir otomatik ölçeklendirme aralığı belirtirseniz, var olan zamanlama durdurulur ve yeni bir zamanlama başlatılır. Yeni zamanlamanın başlangıç zamanı, otomatik ölçeklendirmeyi etkinleştirme isteğinin verildiği süredir.
  - Otomatik ölçeklendirme formülünü veya aralığını atlarsanız, Batch hizmeti bu ayarın geçerli değerini kullanmaya devam eder.

> [!NOTE]
> .NET ' te havuz oluştururken veya başka bir dildeki karşılaştırılabilir parametreler için, **createpool** yönteminin *targetlowlıo düğümleri* veya *targetlowprioritynodes* parametreleri için değerler belirttiyseniz, otomatik ölçeklendirme formülü değerlendirildiğinde bu değerler yok sayılır.

Bu C# örneği, mevcut bir havuzda otomatik ölçeklendirmeyi etkinleştirmek için [Batch .net](/dotnet/api/microsoft.azure.batch) kitaplığını kullanır.

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

Varolan bir otomatik ölçeklendirme havuzundaki formülü güncelleştirmek için, yeni formülle tekrar ölçeklendirmeyi etkinleştirmek üzere işlemi çağırın. Örneğin, aşağıdaki .NET kodu yürütüldüğünde otomatik ölçeklendirme zaten etkinse `myexistingpool` , otomatik ölçeklendirme formülü içeriğiyle değiştirilmiştir `myNewFormula` .

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

Bir formülü bir havuza uygulamadan önce değerlendirebilirsiniz. Bu, formülün sonuçlarını üretime vermeden önce test etmenizi sağlar.

Bir otomatik ölçeklendirme formülünü değerlendirebilmeniz için önce, havuzda tek satırlık formül gibi geçerli bir formülle otomatik ölçeklendirmeyi etkinleştirmeniz gerekir `$TargetDedicatedNodes = 0` . Ardından, test etmek istediğiniz formülü değerlendirmek için aşağıdakilerden birini kullanın:

- [Batchclient. PoolOperations. EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) veya [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Bu Batch .NET yöntemleri, var olan bir havuzun KIMLIĞINI ve değerlendirmek için otomatik ölçeklendirme formülünü içeren bir dizeyi gerektirir.

- [Otomatik ölçeklendirme formülünü değerlendir](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Bu REST API isteğinde, URI 'de havuz KIMLIĞINI ve istek gövdesinin *Otomatik Scaleformula* öğesindeki otomatik ölçeklendirme formülünü belirtin. İşlemin yanıtı, formülle ilişkili olabilecek hata bilgilerini içerir.

Bu [Batch .net](/dotnet/api/microsoft.azure.batch) örneği, bir otomatik ölçeklendirme formülünü değerlendirir. Havuz zaten otomatik ölçeklendirmeyi kullanmıyorsa, ilk olarak etkinleştik.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
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

Formülünüzün beklendiği gibi çalıştığından emin olmak için, toplu Işin havuzunuzdaki gerçekleştirdiği otomatik ölçeklendirme çalıştırmalarının sonuçlarını düzenli olarak kontrol etmenizi öneririz. Bunu yapmak için, havuzun başvurusunu alın (veya yenileyin) ve ardından son otomatik ölçeklendirme çalıştırmasının özelliklerini inceleyin.

Batch .NET 'te [cloudpool. otomatik Scalerun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) özelliğinin, havuzda gerçekleştirilen en son otomatik ölçeklendirme çalıştırması hakkında bilgi sağlayan birkaç özelliği vardır:

- [Otomatik Scalerun. zaman damgası](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [Otomatik Scalerun. sonuçları](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [Otomatik Scalerun. hatası](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

REST API, [bir havuz hakkında bilgi alma](/rest/api/batchservice/get-information-about-a-pool) isteği, otomatik ölçeklendirme çalıştırma bilgilerini [Otomatik](/rest/api/batchservice/get-information-about-a-pool) olarak otomatik ölçeklendirme özelliğini içeren havuz hakkındaki bilgileri döndürür.

Aşağıdaki C# örneği, havuz _mypool_ üzerinde çalışan son otomatik ölçeklendirme hakkında bilgi yazdırmak için Batch .net kitaplığını kullanır.

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Önceki örnekteki örnek çıktı:

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

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Havuz otomatik ölçeklendirme olaylarını kullanarak otomatik ölçeklendirme çalıştırma geçmişi al
Ayrıca, [PoolAutoScaleEvent](batch-pool-autoscale-event.md)sorgulama yaparak otomatik ölçekleme geçmişini de denetleyebilirsiniz. Bu olay, bir otomatik ölçeklendirme formülü değerlendirmesi ve yürütmesinin her oluşumunu kaydetmek için Batch hizmeti tarafından yayınlanır ve bu da olası sorunları gidermeye yardımcı olabilir.

PoolAutoScaleEvent için örnek olay:
```json
{
    "id": "poolId",
    "timestamp": "2020-09-21T23:41:36.750Z",
    "formula": "...",
    "results": "$TargetDedicatedNodes=10;$NodeDeallocationOption=requeue;$curTime=2016-10-14T18:36:43.282Z;$isWeekday=1;$isWorkingWeekdayHour=0;$workHours=0",
    "error": {
        "code": "",
        "message": "",
        "values": []
    }
}
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

`$curTime` , `time()` ürüne `TimeZoneInterval_Hour` ve UTC denkcinize eklenerek yerel saat diliminizi yansıtacak şekilde ayarlanabilir. Örneğin, `$curTime = time() + (-6 * TimeInterval_Hour);` Sıradağlar Yaz saati (MDT) için kullanın. Günışığından yararlanma saatinin başlangıç ve bitişine (varsa) göre ayarlanması gerektiğini aklınızda bulundurun.

### <a name="example-2-task-based-adjustment"></a>Örnek 2: görev tabanlı ayarlama

Bu C# örneğinde, havuz boyutu kuyruktaki görev sayısına göre ayarlanır. Formül dizelerinde hem açıklama hem de satır sonları ekledik.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
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

Bu C# örneği, görev sayısına göre havuz boyutunu ayarlar. Bu formül Ayrıca havuz için ayarlanmış [Taskslotspernode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) değerini hesaba alır. Bu yaklaşım, havuzunuzdaki [paralel görev yürütmenin](batch-parallel-node-tasks.md) etkinleştirildiği durumlarda faydalıdır.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (the TaskSlotsPerNode property on this pool is set to 4, adjust
// this number for your use case)
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

Bu örnekte, bir ilk zaman aralığı için havuz boyutunu belirtilen düğüm sayısına ayarlayan bir otomatik ölçeklendirme formülüne sahip bir C# örneği gösterilmektedir. Bundan sonra, çalışan ve etkin görev sayısına göre havuz boyutunu ayarlar.

Özellikle, bu formül aşağıdakileri yapar:

- İlk havuz boyutunu dört düğüme ayarlar.
- Havuzun yaşam döngüsünün ilk 10 dakikası içinde havuz boyutunu ayarlamaz.
- 10 dakika sonra, son 60 dakika içinde çalışan ve etkin görev sayısının en büyük değerini alır.
  - Her iki değer de 0 ise (son 60 dakika içinde hiçbir görevin çalışmadığını veya etkin olmadığını belirten), havuz boyutu 0 olarak ayarlanır.
  - Her iki değer sıfırdan büyükse hiçbir değişiklik yapılmaz.

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

- [Havuzunuzdaki işlem düğümlerinde birden çok görevi aynı anda yürütmeyi](batch-parallel-node-tasks.md)öğrenin. Otomatik ölçeklendirmeyle birlikte bu, bazı iş yükleri için iş süresini düşürmenize yardımcı olabilir ve paradan tasarruf etmenizi sağlar.
- Azure Batch hizmetini daha fazla verimlilik için [verimli bir şekilde sorgulama](batch-efficient-list-queries.md) hakkında bilgi edinin.
