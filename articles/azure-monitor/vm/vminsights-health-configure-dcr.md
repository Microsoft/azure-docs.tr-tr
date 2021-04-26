---
title: Veri toplama kurallarını kullanarak VM öngörüleri Konuk durumunda izlemeyi yapılandırma (Önizleme)
description: Kaynak Yöneticisi şablonları kullanarak, sanal makine öngörüleri Konuk sistem durumu ölçeğinde varsayılan izlemenin nasıl değiştirileceğini açıklar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 889a04d68de45a6270ae0c38615d841a526ad86a
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490701"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-using-data-collection-rules-preview"></a>Veri toplama kurallarını kullanarak VM öngörüleri Konuk durumunda izlemeyi yapılandırma (Önizleme)
[VM öngörüleri Konuk durumu](vminsights-health-overview.md) , bir sanal makinenin durumunu, düzenli aralıklarla örneklendiği bir dizi performans ölçümlerine göre tanımlanan şekilde görüntülemenize olanak sağlar. Bu makalede, veri toplama kurallarını kullanarak birden çok sanal makine genelinde varsayılan izlemenin nasıl değiştirileceği açıklanır.


## <a name="monitors"></a>İzleyiciler
Bir sanal makinenin sistem durumu, izleyicilerinin her birinden [sistem durumu toplamasına](vminsights-health-overview.md#health-rollup-policy) göre belirlenir. Aşağıdaki tabloda gösterildiği gibi, VM öngörüleri Konuk durumu 'nda iki tür izleyici vardır.

| İzleyici | Açıklama |
|:---|:---|
| Birim izleyicisi | Bir kaynağın veya uygulamanın bazı özelliklerini ölçer. Bu kapsamda kaynağın performansını veya kullanılabilirliğini belirlemek için bir performans sayacı denetlenebilir. |
| Toplam Değer İzleyicisi | Birden çok izleyiciyi gruplayarak tek ve toplu bir sistem durumu bilgisi sağlar. Bir toplam değer izleyicisinde bir veya daha fazla birim izleyicisi ve diğer toplam değer izleyicileri bulunabilir. |

VM öngörüleri Konuk durumu tarafından kullanılan izleyici kümesi ve yapılandırmaları doğrudan değiştirilemez. Varsayılan yapılandırmanın davranışını değiştirmek için [geçersiz kılmalar](#overrides) oluşturabilirsiniz. Geçersiz kılmalar, veri toplama kurallarında tanımlanmıştır. Gerekli izleme yapılandırmanızı elde etmek için, her biri birden çok geçersiz kılma içeren birden çok veri toplama kuralı oluşturabilirsiniz.

## <a name="monitor-properties"></a>İzleme özellikleri
Aşağıdaki tabloda, her monitörde yapılandırılabilecek özellikler açıklanmaktadır.

| Özellik | İzleyiciler | Açıklama |
|:---|:---|:---|
| Etkin | Toplama<br>Birim | Doğru ise, durum İzleyicisi hesaplanır ve sanal makinenin durumuna katkıda bulunur. Uyarı uyarısının etkin olduğunu tetikleyebilirler. |
| Uyarı | Toplama<br>Birim | True ise, uygun olmayan bir duruma geçirildiğinde izleyici için bir uyarı tetiklenir. Yanlışsa, izleyicinin durumu yine de bir uyarı tetikleyebilen sanal makinenin sistem durumuna katkıda bulunur. |
| Uyarı | Birim | Uyarı durumu için ölçütler. Hiçbiri yoksa, izleyici hiçbir şekilde bir uyarı durumu girmeyecektir. |
| Kritik | Birim | Kritik durum için ölçütler. Hiçbiri yoksa, izleyici hiçbir zamana kritik bir durum girmeyecektir. |
| Değerlendirme sıklığı | Birim | Sağlık durumunun değerlendirilme sıklığı. |
| Geriye doğru | Birim | Geriye doğru geri dönme penceresinin boyutu (saniye). Ayrıntılı açıklama için bkz. [Monitorconfiguration öğesi](#monitorconfiguration-element) . |
| Değerlendirme türü | Birim | Örnek kümesinden hangi değerin kullanılacağını tanımlar. Ayrıntılı açıklama için bkz. [Monitorconfiguration öğesi](#monitorconfiguration-element) . |
| Minimum örnek | Birim | Değeri hesaplamak için kullanılacak minimum değer sayısı. |
| En büyük örnek | Birim | Değeri hesaplamak için kullanılacak maksimum değer sayısı. |


## <a name="default-configuration"></a>Varsayılan yapılandırma
Aşağıdaki tabloda her izleyici için varsayılan yapılandırma listelenmektedir. Bu varsayılan yapılandırma doğrudan değiştirilemez, ancak belirli sanal makineler için İzleyici yapılandırmasını değiştirecek [geçersiz kılmalar](#overrides) tanımlayabilirsiniz.


| İzleyici | Etkin | Uyarı | Uyarı | Kritik | Değerlendirme sıklığı | Geriye doğru | Değerlendirme türü | Minimum örnek | En fazla örnek |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| CPU kullanımı  | Doğru | Yanlış | Yok | \> %90    | 60 sn | 240 sn | Min | 2 | 3 |
| Kullanılabilir bellek | Doğru | Yanlış | Yok | \< 100 MB | 60 sn | 240 sn | En yüksek değer | 2 | 3 |
| Dosya sistemi      | Doğru | Yanlış | Yok | \< 100 MB | 60 sn | 120 sn | En yüksek değer | 1 | 1 |


## <a name="overrides"></a>Geçersiz Kılmalar
Bir *geçersiz kılma* , izleyicinin bir veya daha fazla özelliğini değiştirir. Örneğin, bir geçersiz kılma varsayılan olarak etkinleştirilen bir izleyiciyi devre dışı bırakabilir, izleyici için uyarı ölçütlerini tanımlayabilir veya izleyicinin kritik eşiğini değiştirebilir. 

Geçersiz kılmalar bir [veri toplama kuralında (DCR)](../agents/data-collection-rule-overview.md)tanımlanır. Farklı geçersiz kılmalar kümesiyle birden çok DTU oluşturabilir ve bunları birden çok sanal makineye uygulayabilirsiniz. [Azure izleyici Aracısı (Önizleme) için veri toplamayı yapılandırma](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations)bölümünde açıklandığı gibi bir ilişki oluşturarak sanal MAKINEYE bir DCR uygularsınız.


## <a name="multiple-overrides"></a>Birden çok geçersiz kılma

Tek bir izleyicinin birden çok geçersiz kılma işlemleri olabilir. Geçersiz kılmalar farklı özellikler tanımladığında, elde edilen yapılandırma tüm geçersiz kılmaların bir birleşimidir.

Örneğin, `memory|available` izleyici bir uyarı eşiği belirtmez veya varsayılan olarak uyarı vermez. Bu monitöre uygulanan aşağıdaki geçersiz kılmaları göz önünde bulundurun:

- Override 1 `alertConfiguration.isEnabled` özellik değerini şöyle tanımlar `true`
- Geçersiz kılma 2 ' `monitorConfiguration.warningCondition` nin eşik koşuluyla ile birlikte tanımlar `< 250` .

Elde edilen yapılandırma, 250 MB 'tan az bellek olduğunda bir uyarı sistem durumuna geçen ve önem derecesi 2 uyarısı oluşturan ve ayrıca 100 MB 'tan az kullanılabilir bellek olduğunda kritik sağlık durumunda olan bir izleyici olur (veya zaten varsa, önem derecesi 2 ' den 1 ' e geçer).

İki geçersiz kılma aynı izleyici üzerinde aynı özelliği tanımladığında, bir değer önceliğe sahip olur. Geçersiz kılmalar [kapsamlarına](#scopes-element)göre, en genel olan en belirgin şekilde uygulanır. Bu, en belirgin geçersiz kılmaların en büyük uygulanma ihtimaline sahip olacağı anlamına gelir. Belirli bir sıra aşağıdaki gibidir:

1. Genel 
2. Abonelik
3. Kaynak grubu
4. Sanal makine. 

Aynı kapsam düzeyinde birden çok geçersiz kılma aynı özelliği aynı monitörde tanımladıktan sonra, bunlar DCR içinde göründükleri sırada uygulanır. Geçersiz kılmalar farklı CCR 'ler halinde ise, bu değerler, DCR kaynak kimliklerinin alfabetik sırasına göre uygulanır.


## <a name="data-collection-rule-configuration"></a>Veri toplama kuralı yapılandırması
Veri toplama kuralındaki geçersiz kılmaları tanımlayan JSON öğeleri aşağıdaki bölümlerde açıklanmıştır. [Örnek veri toplama kuralında](#sample-data-collection-rule), tamamlanmış bir örnek verilmiştir.

## <a name="extensions-structure"></a>Uzantı yapısı
Konuk sistem durumu, Azure Izleyici aracısına bir uzantı olarak uygulanır, dolayısıyla `extensions` veri toplama kuralının öğesinde geçersiz kılmalar tanımlanmıştır. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| Öğe | Gerekli | Açıklama |
|:---|:---|:---|
| `name` | Evet | Uzantı için Kullanıcı tanımlı dize. |
| `streams` | Yes | Konuk sistem durumu verilerinin gönderileceği akışların listesi. Bu, **Microsoft-HealthStateChange** içermelidir.  |
| `extensionName` | Yes | Uzantının adı. Bu, **Healthextension** olmalıdır. |
| `extensionSettings` | Yes | `healthRuleOverride`Varsayılan yapılandırmaya uygulanacak öğelerin dizisi. |


## <a name="extensionsettings-element"></a>extensionSettings öğesi
Uzantı ayarlarını içerir.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| Öğe | Gerekli | Açıklama |
|:---|:---|:---|
| `schemaVersion` | Evet | Microsoft tarafından tanımlanan ve öğenin beklenen şemasını temsil eden dize. Şu anda 1,0 olarak ayarlanması gerekir |
| `contentVersion` | Hayır | Gerektiğinde, sistem durumu yapılandırmasının farklı sürümlerini izlemek için Kullanıcı tarafından tanımlanan dize. |
| `healthRuleOverrides` | Yes | `healthRuleOverride`Varsayılan yapılandırmaya uygulanacak öğelerin dizisi. |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides öğesi
`healthRuleOverride`Her birinin bir geçersiz kılma tanımlayan bir veya daha fazla öğe içeriyor.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| Öğe | Gerekli | Açıklama |
|:---|:---|:---|
| `scopes` | Evet | Bu geçersiz kılmanın geçerli olduğu sanal makineleri belirten bir veya daha fazla kapsam listesi. DCR bir sanal makineyle ilişkili olsa da, sanal makinenin, geçersiz kılmanın uygulanması için bir kapsam içinde olması gerekir. |
| `monitors` | Yes | Hangi izleyicilerin bu geçersiz kılmayı alacağını tanımlayan bir veya daha fazla dizenin listesi.  |
| `monitorConfiguration` | Hayır | İzleyici için sistem sağlığı durumları ve nasıl hesaplandıkları gibi yapılandırma. |
| `alertConfiguration` | Hayır | İzleyici için uyarı yapılandırması. |
| `isEnabled` | Hayır | İzlemenin etkin olup olmadığını denetler. Devre dışı bırakılan izleyici, yeniden etkinleştirilmediği takdirde devre dışı *bırakılan özel durum* durumuna geçer. Atlanırsa, izleyici hiyerarşideki üst izleyiciden durumunu alır. |


## <a name="scopes-element"></a>Scopes öğesi
Her geçersiz kılmaların bir veya daha fazla kapsamı, geçersiz kılmanın hangi sanal makineleri uygulanacağını tanımlar. Kapsam bir abonelik, kaynak grubu veya tek bir sanal makine olabilir. Geçersiz kılma belirli bir sanal makineyle ilişkilendirilmiş bir DCR içinde olsa bile, sanal makine yalnızca geçersiz kılmanın kapsamlarından biri içindeyse bu sanal makineye uygulanır. Bu, daha az sayıda DTU 'ları bir VM kümesiyle ilişkilendirebilir, ancak her bir geçersiz kılmanın kendisi içinde atanması üzerinde ayrıntılı denetim sağlamanıza olanak tanır. Kapsam öğesi kullanan sanal makinelerin farklı alt kümeleri için sistem durumu İzleyicisi geçersiz kılmaları belirtirken ilkeyi kullanarak bir sanal makine kümesiyle ilgili küçük bir dizi derleme ve ilişkilendirme oluşturmak isteyebilirsiniz.

Aşağıdaki tabloda farklı kapsamların örnekleri gösterilmektedir.

| Kapsam | Örnek |
|:---|:---|
| Tek sanal makine | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| Bir kaynak grubundaki tüm sanal makineler | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| Bir abonelikteki tüm sanal makineler | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| Veri toplama kuralının ilişkilendirildiği tüm sanal makineler | `*` |


## <a name="monitors-element"></a>izleyiciler öğesi
Sistem durumu hiyerarşisinde hangi izleyicilerin bu geçersiz kılmayı alacağını tanımlayan bir veya daha fazla dizenin listesi. Her öğe, bu geçersiz kılmayı alacak bir veya daha fazla monitörle eşleşen bir izleyici adı veya tür adı olabilir. 

```json
"monitors": [
    "<monitor name>"
 ],
```



Aşağıdaki tabloda geçerli kullanılabilir izleyici adları listelenmektedir.

| Tür adı | Ad | Açıklama |
|:----------|:-----|:------------|
| kök | kök | Sanal makine durumunu temsil eden üst düzey izleyici. |
| CPU kullanımı | CPU kullanımı | CPU kullanımı İzleyicisi. |
| mantıksal diskler | mantıksal diskler | Windows sanal makinesindeki tüm izlenen disklerin sistem durumu için toplam izleyici. |
| mantıksal diskler\|\* | mantıksal diskler \| C:<br>mantıksal diskler \| D: | Windows sanal makinesinde belirli bir diskin toplam izleyici izleme durumu. |
| mantıksal disk \| \* \| boş alanı | mantıksal diskler \| C: \| boş alan<br>mantıksal diskler \| D: \| boş alan | Windows sanal makinesinde disk boş alan İzleyicisi. |
| dosya sistemleri | dosya sistemleri | Linux sanal makinesindeki tüm dosya sistemlerinin sistem durumu için toplam izleyici. |
| dosya sistemleri\|\* | dosya sistemleri\|/<br>dosya sistemleri \| /mnt | Linux sanal makinesinin bir FileSystem 'ın toplam izleyici izleme durumu. |
| dosya sistemleri \| \* \| boş alanı | dosya sistemleri \| / \| boş alanı<br>dosya sistemleri \| /mnt \| boş alan | Linux sanal makine FileSystem üzerinde disk boş alan İzleyicisi. |
| bellek | bellek | Sanal makine belleğinin sistem durumu için toplam izleyici. |
| \|kullanılabilir bellek | \|kullanılabilir bellek | İzleme, sanal makinede kullanılabilir belleği izler. |


## <a name="alertconfiguration-element"></a>alertConfiguration öğesi
İzleyiciden bir uyarının oluşturulup oluşturulmayacağını belirtir.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| Öğe | Zorunlu | Açıklama | 
|:---|:---|:---|
| `isEnabled` | Hayır | True olarak ayarlanırsa, İzleyici kritik veya uyarı durumuna geçiş yaparken uyarı oluşturur ve sağlıklı duruma dönünce uyarıyı çözer. Yanlış veya atlanırsa, hiçbir uyarı oluşturulmaz.  |


## <a name="monitorconfiguration-element"></a>monitorConfiguration öğesi
Yalnızca birim izleyicileri için geçerlidir. Sistem sağlığı durumları ve bunların nasıl hesaplandıkları dahil olmak üzere izleyicinin yapılandırmasını tanımlar.

Parametreler, eşiklere göre Karşılaştırılacak ölçüm değerini hesaplamak için algoritmayı tanımlar. İzleyici, temel alınan ölçümden bir veri örneğine işlem yapmak yerine, bir pencere içinde alınan birkaç ölçüm örneğini değerlendirme zamanından ve önce değerlendirir `lookbackSec` . Bu zaman çerçevesi içinde alınan tüm örnekler göz önünde bulundurulmaktadır ve örnek sayısı şundan büyükse `maxSamples` , yukarıdaki eski örnekler `maxSamples` yok sayılır. 

Geriye doğru geri alma aralığında daha az örnek olması durumunda `minSamples` , izleme temeldeki ölçümlerin sağlık durumu hakkında bilinçli bir karar vermek için yeterli veri olmadığını belirten *Bilinmeyen* sistem durumu ' na geçiş yapar. Daha fazla sayıda örnek varsa `minSamples` , evaluationType parametresi tarafından belirtilen bir toplama işlevi, tek bir değeri hesaplamak için küme üzerinde çalışır.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| Öğe | Zorunlu | Açıklama | 
|:---|:---|:---|
| `evaluationFrequencySecs` | Hayır | Sistem durumu değerlendirmesi için sıklığı tanımlar. Her izleyici, aracının başladığı sırada ve bundan sonra bu parametre tarafından tanımlanan düzenli bir aralıkta değerlendirilir. |
| `lookbackSecs`   | Hayır | Geriye doğru geri dönme penceresinin boyutu (saniye). |
| `evaluationType` | Hayır | `min` – Tüm örnek kümesinden en küçük değeri Al<br>`max` -Tüm örnek kümesinden en büyük değeri Al<br>`avg` – örnek kümesi değerlerini ortalama alın<br>`all` – küme içindeki her tek değeri eşiklere göre karşılaştırın. Anahtar durumunu yalnızca, küme içindeki tüm örnekler eşik koşulunu karşıdığında izler. |
| `minSamples`     | Hayır | Değeri hesaplamak için kullanılacak minimum değer sayısı. |
| `maxSamples`     | Hayır | Değeri hesaplamak için kullanılacak maksimum değer sayısı. |
| `warningCondition`  | Hayır | Uyarı koşulu için eşik ve karşılaştırma mantığı. |
| `criticalCondition` | Hayır | Kritik koşul için eşik ve karşılaştırma mantığı. |


## <a name="warningcondition-element"></a>warningCondition öğesi
Uyarı koşulunun eşiğini ve karşılaştırma mantığını tanımlar. Bu öğe dahil edilmemişse, izleyici hiçbir şekilde uyarı sistem durumuna geçmeyecektir.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Özellik | Zorunlu | Açıklama | 
|:---|:---|:---|
| `isEnabled` | Hayır | Koşulun etkinleştirilip etkinleştirilmeyeceğini belirtir. **False** olarak ayarlanırsa, eşik ve işleç özellikleri ayarlanmasa bile koşul devre dışı bırakılır. |
| `threshold` | Hayır | Değerlendirilen değeri karşılaştırmak için eşiği tanımlar. |
| `operator`  | Hayır | Eşik ifadesinde kullanılacak karşılaştırma işlecini tanımlar. Olası değerler: >, <, >=, <=, = =. |


## <a name="criticalcondition-element"></a>Kritikcondition öğesi
Kritik koşul için eşiği ve karşılaştırma mantığını tanımlar. Bu öğe dahil edilmemişse, izleyici hiçbir şekilde kritik sistem durumuna geçmeyecektir.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Özellik | Zorunlu | Açıklama | 
|:---|:---|:---|
| `isEnabled` | Hayır | Koşulun etkinleştirilip etkinleştirilmeyeceğini belirtir. **False** olarak ayarlanırsa, eşik ve işleç özellikleri ayarlanmasa bile koşul devre dışı bırakılır. |
| `threshold` | Hayır | Değerlendirilen değeri karşılaştırmak için eşiği tanımlar. |
| `operator`  | Hayır | Eşik ifadesinde kullanılacak karşılaştırma işlecini tanımlar. Olası değerler: >, <, >=, <=, = =. |

## <a name="sample-data-collection-rule"></a>Örnek veri toplama kuralı
Konuk izlemeyi etkinleştiren örnek bir veri toplama kuralı için bkz. [Kaynak Yöneticisi şablonu kullanarak sanal makineyi etkinleştirme](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template).


## <a name="next-steps"></a>Sonraki adımlar

- [Veri toplama kuralları](../agents/data-collection-rule-overview.md)hakkında daha fazla bilgi edinin.
