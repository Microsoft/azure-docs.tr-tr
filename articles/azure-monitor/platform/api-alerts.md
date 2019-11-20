---
title: Log Analytics uyarı REST API kullanma
description: Log Analytics uyarı REST API, Log Analytics bir parçası olan Log Analytics uyarı oluşturmanıza ve yönetmenize olanak sağlar.  Bu makalede API ayrıntıları ve farklı işlemler gerçekleştirmeye yönelik birkaç örnek sağlanmaktadır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2018
ms.openlocfilehash: 9cc9c9db1438196190df38082f18d650eff38249
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932688"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>REST API ile Log Analytics uyarı kuralları oluşturma ve yönetme
Log Analytics uyarı REST API Log Analytics uyarı oluşturmanıza ve yönetmenize olanak sağlar.  Bu makalede API ayrıntıları ve farklı işlemler gerçekleştirmeye yönelik birkaç örnek sağlanmaktadır.

> [!IMPORTANT]
> [Daha önce duyurulduğu](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)gibi, *1 Haziran 2019* tarihinden sonra oluşturulan Log Analytics çalışma alanı (ler), **yalnızca** Azure Scheduledqueryrules [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/), [Azure Resource mananger şablonunu](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) [kullanarak uyarı kurallarını yönetebilecektir. PowerShell cmdlet 'i](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Müşteriler, Azure Izleyici scheduledQueryRules 'ı varsayılan olarak kullanabilmek ve yerel PowerShell cmdlet 'leri kullanma özelliği gibi birçok [yeni avantaj](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) kazanmak için, daha eski çalışma alanları için [kendi tercih edilen uyarı kuralı yönetimini](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) kolayca değiştirebilir. kuralların ayrı kaynak grubunda veya abonelikte ve çok daha fazlasına göre kuralları oluşturma sırasında geri dönüş süresi.

Log Analytics arama REST API yeniden yapılır ve Azure Resource Manager REST API aracılığıyla erişilebilir. Bu belgede, API 'nin, Azure Resource Manager API 'yi çağırmayı kolaylaştıran açık kaynaklı bir komut satırı aracı olan [Armclient](https://github.com/projectkudu/ARMClient)kullanarak bir PowerShell komut satırından erişildiği örnekleri bulacaksınız. ARMClient ve PowerShell kullanımı, Log Analytics arama API 'sine erişmek için birçok seçenekten biridir. Bu araçlarla, Log Analytics çalışma alanlarına çağrılar yapmak ve bunlar içinde arama komutları gerçekleştirmek için RestleAzure Resource Manager API 'sinden yararlanabilirsiniz. API, arama sonuçlarını size JSON biçiminde çıktı olarak, arama sonuçlarını programlama yoluyla birçok farklı şekilde kullanmanıza olanak sağlar.

## <a name="prerequisites"></a>Önkoşullar
Şu anda, uyarılar yalnızca Log Analytics bir kayıtlı aramayla oluşturulabilir.  Daha fazla bilgi için [günlük aramasına REST API](../../azure-monitor/log-query/log-query-overview.md) bakabilirsiniz.

## <a name="schedules"></a>Zamanlamalar
Kayıtlı aramada bir veya daha fazla zamanlama olabilir. Zamanlama, aramanın ne sıklıkla çalıştırılacağını ve ölçütlerin tanımlandığı zaman aralığını tanımlar.
Zamanlamalarda aşağıdaki tablodaki özellikler vardır.

| Özellik | Açıklama |
|:--- |:--- |
| Interval |Aramanın ne sıklıkta çalıştırıldığı. Dakikalar içinde ölçülür. |
| queryTimeSpan |Ölçütlerin değerlendirildiği zaman aralığı. Değere eşit veya ondan büyük olmalıdır. Dakikalar içinde ölçülür. |
| Sürüm |Kullanılmakta olan API sürümü.  Şu anda bu her zaman 1 olarak ayarlanmalıdır. |

Örneğin, 15 dakikalık bir zaman aralığı ve 30 dakikalık bir TimeSpan değeri ile bir olay sorgusu düşünün. Bu durumda, sorgu 15 dakikada bir çalıştırılır ve ölçütler 30 dakikalık bir Aralık üzerinde true olarak çözümlenmeye devam ederseniz bir uyarı tetiklenir.

### <a name="retrieving-schedules"></a>Zamanlamalar alınıyor
Kayıtlı bir aramanın tüm zamanlamalarını almak için get yöntemini kullanın.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Kayıtlı bir arama için belirli bir zamanlamayı almak üzere bir zamanlama KIMLIĞIYLE GET yöntemini kullanın.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Bir zamanlama için örnek yanıt aşağıda verilmiştir.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15,
            "Enabled": true,
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Zamanlama oluşturma
Yeni bir zamanlama oluşturmak için Put metodunu benzersiz bir zamanlama KIMLIĞIYLE kullanın.  Farklı kaydedilmiş aramalarla ilişkilendirilseler de iki zamanlama aynı KIMLIĞE sahip olamaz.  Log Analytics konsolunda bir zamanlama oluşturduğunuzda, zamanlama KIMLIĞI için bir GUID oluşturulur.

> [!NOTE]
> Log Analytics API 'SI ile oluşturulan tüm kayıtlı aramaların, zamanlamaların ve eylemlerin adı küçük harfle yazılmalıdır.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Zamanlamayı Düzenle
Bu zamanlamayı değiştirmek için aynı kayıtlı arama için mevcut bir zamanlama KIMLIĞIYLE put metodunu kullanın; Aşağıdaki örnekte zamanlamanın devre dışı bırakılması. İsteğin gövdesi, zamanlamanın *ETag* öğesini içermelidir.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Zamanlamaları silme
Zamanlamayı silmek için bir zamanlama KIMLIĞIYLE Delete metodunu kullanın.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Eylemler
Bir zamanlamada birden çok eylem olabilir. Bir eylem, e-posta gönderme veya Runbook başlatma gibi bir veya daha fazla işlemi gerçekleştirebilir veya bir aramanın sonuçlarının bazı ölçütlerle eşleştiğini belirleyen bir eşik tanımlayabilir.  Eşik karşılandığında işlemlerin gerçekleştirilmesi için bazı eylemler her ikisini de tanımlayacaktır.

Tüm eylemler aşağıdaki tablodaki özelliklere sahiptir.  Farklı Uyarı türleri, aşağıda açıklanan farklı ek özelliklere sahiptir.

| Özellik | Açıklama |
|:--- |:--- |
| `Type` |Eylemin türü.  Şu anda olası değerler uyarı ve Web kancası ' dir. |
| `Name` |Uyarı için görünen ad. |
| `Version` |Kullanılmakta olan API sürümü.  Şu anda bu her zaman 1 olarak ayarlanmalıdır. |

### <a name="retrieving-actions"></a>Eylemler alınıyor

Bir zamanlamaya yönelik tüm eylemleri almak için get yöntemini kullanın.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Bir zamanlamaya yönelik belirli bir eylemi almak için eylem KIMLIĞIYLE al yöntemini kullanın.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Eylemleri oluşturma veya Düzenle
Yeni bir eylem oluşturmak için, zamanlamaya özgü bir eylem KIMLIĞIYLE put metodunu kullanın.  Log Analytics konsolunda bir eylem oluşturduğunuzda, eylem KIMLIĞI için bir GUID olur.

> [!NOTE]
> Log Analytics API 'SI ile oluşturulan tüm kayıtlı aramaların, zamanlamaların ve eylemlerin adı küçük harfle yazılmalıdır.

Bu zamanlamayı değiştirmek için aynı kayıtlı arama için var olan bir eylem KIMLIĞIYLE put metodunu kullanın.  İsteğin gövdesi, zamanlamanın ETag öğesini içermelidir.

Yeni bir eylem oluşturmak için istek biçimi, bu örneklerin aşağıdaki bölümlerde sağlanması için eylem türüne göre farklılık gösterir.

### <a name="deleting-actions"></a>Eylemleri silme

Eylemi silmek için eylem KIMLIĞIYLE Delete yöntemini kullanın.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Uyarı eylemleri
Zamanlamanın bir ve yalnızca bir uyarı eylemi olması gerekir.  Uyarı eylemleri aşağıdaki tabloda yer aldığı bir veya daha fazla bölümden oluşur.  Her biri aşağıda daha ayrıntılı olarak açıklanmıştır.

| Section | Açıklama | Kullanım |
|:--- |:--- |:--- |
| Eşiği |Eylemin çalıştırılacağı ölçüt.| Her uyarı için, Azure 'a genişletildikleri tarihten önce veya sonra gereklidir. |
| Önem Derecesi |Tetiklendikten sonra uyarıyı sınıflandırmak için kullanılan etiket.| Her uyarı için, Azure 'a genişletildikleri tarihten önce veya sonra gereklidir. |
| Gizle |Uyarı bildirimlerini durdurma seçeneği. | Azure 'a genişletildiklerinde veya sonrasında her uyarı için isteğe bağlı. |
| Eylem Grupları |-E-postalar, SMSs, sesli çağrılar, Web kancaları, Otomasyon Runbook 'Ları, ıTSM bağlayıcıları vb. gibi eylemlerin gerekli olduğu Azure ActionGroup 'un kimlikleri.| Uyarılar Azure 'a genişletildiğinde gereklidir|
| Eylemleri özelleştirme|ActionGroup 'tan seçme eylemleri için Standart çıktıyı değiştirme| Her uyarı için isteğe bağlı, uyarılar Azure 'a uzatıldıktan sonra kullanılabilir. |

### <a name="thresholds"></a>Leriyle
Bir uyarı eylemi bir ve yalnızca bir eşiğe sahip olmalıdır.  Kayıtlı bir aramanın sonuçları, bu aramayla ilişkili bir Eylemdeki eşikle eşleşiyorsa, söz konusu Eylemdeki diğer tüm süreçler çalıştırılır.  Bir eylem, eşik içermeyen diğer türlerdeki eylemlerle kullanılabilmesi için yalnızca bir eşik de içerebilir.

Eşikler aşağıdaki tablodaki özelliklere sahiptir.

| Özellik | Açıklama |
|:--- |:--- |
| `Operator` |Eşik karşılaştırması için işleç. <br> gt = büyüktür <br> lt = küçüktür |
| `Value` |Eşiğin değeri. |

Örneğin, bir olay sorgusunu 15 dakika, 30 dakikalık bir zaman aralığı ve 10 ' dan büyük bir eşik değeri ile düşünün. Bu durumda, sorgu 15 dakikada bir çalıştırılır ve 30 dakikalık bir Aralık üzerinden oluşturulan 10 olay döndürülürse bir uyarı tetiklenir.

Yalnızca bir eşiğe sahip bir eylem için örnek yanıt aşağıda verilmiştir.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Bir zamanlamaya yönelik yeni bir eşik eylemi oluşturmak için, benzersiz bir eylem KIMLIĞIYLE put metodunu kullanın.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Bir zamanlama için eşik eylemini değiştirmek üzere mevcut eylem KIMLIĞIYLE Put yöntemini kullanın.  İsteğin gövdesi eylemin ETag öğesini içermelidir.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Önem Derecesi
Log Analytics, daha kolay yönetim ve önceliklendirme sağlamak için uyarılarınızı kategoriler halinde sınıflandırmanızı sağlar. Tanımlanan uyarı önem derecesi: bilgilendirici, uyarı ve kritik. Bunlar şu şekilde Azure uyarılarının normalleştirilmiş önem derecesine eşlenir:

|Log Analytics önem düzeyi  |Azure uyarıları önem düzeyi  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev 2|

Yalnızca eşik ve önem derecesine sahip bir eylem için örnek yanıt aşağıda verilmiştir. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

Önem derecesine sahip bir zamanlamaya yönelik yeni bir eylem oluşturmak için, özel bir eylem KIMLIĞIYLE put metodunu kullanın.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Bir zamanlamaya yönelik önem derecesini değiştirmek için, mevcut bir eylem KIMLIĞIYLE Put yöntemini kullanın.  İsteğin gövdesi eylemin ETag öğesini içermelidir.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Gizle
Log Analytics tabanlı sorgu uyarıları, her eşiğin karşılandığı veya aşıldığı her seferinde tetiklenir. Sorguda kapsanan mantığa göre, bu durum bir dizi Aralık için harekete geçirildiğinde ve bu bildirimin sürekli olarak gönderilmesine neden olabilir. Böyle bir senaryoyu önlemek için, bir Kullanıcı, uyarı kuralının ikinci kez tetikleneceği bir süre için beklemek üzere Log Analytics, engelleme seçeneğini ayarlayabilir. Bu nedenle, gizleme 30 dakika için ayarlandıysa ardından uyarı ilk kez başlatılır ve yapılandırılan bildirimleri gönderir. Ancak, uyarı kuralı için bildirim yeniden kullanılmadan önce 30 dakika bekleyin. Ara dönemde, uyarı kuralının bu dönemde kaç kez tetiklendiğine bakılmaksızın, uyarı kuralı yalnızca belirli bir süre için Log Analytics tarafından bastırılır.

Log Analytics uyarı kuralının özelliğini Gizle değeri, *daraltma* değeri ve gizleme dönemi kullanılarak *DurationInMinutes* değeri kullanılarak belirtilir.

Yalnızca eşik, önem derecesi ve gizleme özelliği olan bir eylem için örnek yanıt aşağıda verilmiştir

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Throttling": {
          "DurationInMinutes": 30
        },
        "Severity": "critical",
        "Version": 1    }

Önem derecesine sahip bir zamanlamaya yönelik yeni bir eylem oluşturmak için, özel bir eylem KIMLIĞIYLE put metodunu kullanın.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Bir zamanlamaya yönelik önem derecesini değiştirmek için, mevcut bir eylem KIMLIĞIYLE Put yöntemini kullanın.  İsteğin gövdesi eylemin ETag öğesini içermelidir.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Eylem Grupları
Tüm uyarılar Azure 'da, eylemleri işlemek için varsayılan mekanizma olarak eylem grubunu kullanın. Eylem grubuyla, eylemlerinizi bir kez belirtebilir ve ardından Eylem grubunu Azure 'da birden çok uyarı ile ilişkilendirebilirsiniz. Gerek olmadan, aynı eylemleri tekrar tekrar tekrar bildirmek için. Eylem grupları e-posta, SMS, sesli çağrı, ıTSM bağlantısı, Otomasyon Runbook 'u, Web kancası URI ve daha fazlasını içeren birden çok eylemi destekler 

Uyarıları Azure 'a genişletmiş olan kullanıcılar için bir zamanlama, bir uyarı oluşturabilmek için artık bir zamanlayıcı ile birlikte Işlem grubu ayrıntılarının geçirilmesini sağlamalıdır. E-posta ayrıntıları, Web kancası URL 'Leri, runbook Otomasyon ayrıntıları ve diğer eylemler, bir uyarı oluşturmadan önce bir eylem grubu içinde tanımlanmalıdır. Portal 'da [Azure izleyici 'den bir eylem grubu](../../azure-monitor/platform/action-groups.md) oluşturabilir veya [eylem grubu API 'sini](https://docs.microsoft.com/rest/api/monitor/actiongroups)kullanabilirsiniz.

Eylem grubunun bir uyarıya ilişkilendirmesini eklemek için, uyarı tanımındaki eylem grubunun benzersiz Azure Resource Manager KIMLIĞINI belirtin. Örnek bir çizim aşağıda verilmiştir:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Zaten var olan eylem grubunu bir zamanlama için ilişkilendirmek üzere, put metodunu benzersiz bir eylem KIMLIĞIYLE kullanın.  Aşağıda örnek bir kullanım çizimi verilmiştir.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Bir zamanlama ile ilişkili bir eylem grubunu değiştirmek için Put yöntemini mevcut bir eylem KIMLIĞIYLE birlikte kullanın.  İsteğin gövdesi eylemin ETag öğesini içermelidir.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Eylemleri özelleştirme
Varsayılan eylemler için standart şablonu ve bildirimler için biçimi izleyin. Ancak Kullanıcı, eylem grupları tarafından denetlenseler bile bazı eylemleri özelleştirebilir. Şu anda, e-posta konusu ve Web kancası yükü için özelleştirme mümkündür.

##### <a name="customize-e-mail-subject-for-action-group"></a>Eylem grubu için e-posta konusunu Özelleştir
Varsayılan olarak, uyarılar için e-posta konusu: uyarı bildirimi `<WorkspaceName>`için `<AlertName>`. Ancak bu, özel sözcüklere veya etiketlere yönelik olarak kolayca filtre kuralları kullanmanıza olanak tanımak için özelleştirilebilir. Aşağıdaki örnekte olduğu gibi, e-posta üst bilgisini Özelleştir ayrıntılarının ActionGroup ayrıntıları ile birlikte gönderilmesi gerekir.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Zaten var olan eylem grubunu bir zamanlama için özelleştirme ile ilişkilendirmek üzere put metodunu benzersiz bir eylem KIMLIĞIYLE birlikte kullanın.  Aşağıda örnek bir kullanım çizimi verilmiştir.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Bir zamanlama ile ilişkili bir eylem grubunu değiştirmek için Put yöntemini mevcut bir eylem KIMLIĞIYLE birlikte kullanın.  İsteğin gövdesi eylemin ETag öğesini içermelidir.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Eylem grubu için Web kancası yükünü özelleştirme
Varsayılan olarak, Log Analytics için eylem grubu aracılığıyla gönderilen Web kancasının sabit bir yapısı vardır. Ancak bir tane, Web kancası uç noktasının gereksinimlerini karşılamak için desteklenen belirli değişkenleri kullanarak JSON yükünü özelleştirebilir. Daha fazla bilgi için bkz. [günlük uyarısı kuralları Için Web kancası eylemi](../../azure-monitor/platform/alerts-log-webhook.md). 

Web kancası ayrıntılarının özelleştirilmesi, ActionGroup ayrıntıları ile birlikte gönderilmesi gerekir ve eylem grubu içinde belirtilen tüm Web kancası URI 'sine uygulanır; Aşağıdaki örnekte olduğu gibi.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Zaten var olan eylem grubunu bir zamanlama için özelleştirme ile ilişkilendirmek üzere put metodunu benzersiz bir eylem KIMLIĞIYLE birlikte kullanın.  Aşağıda örnek bir kullanım çizimi verilmiştir.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Bir zamanlama ile ilişkili bir eylem grubunu değiştirmek için Put yöntemini mevcut bir eylem KIMLIĞIYLE birlikte kullanın.  İsteğin gövdesi eylemin ETag öğesini içermelidir.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>Sonraki adımlar

* Log Analytics ' de [günlük aramalarını gerçekleştirmek için REST API](../../azure-monitor/log-query/log-query-overview.md) kullanın.
* [Azure izleyici 'de günlük uyarıları](../../azure-monitor/platform/alerts-unified-log.md) hakkında bilgi edinin
* [Azure izleyici 'de günlük uyarı kuralları oluşturma, düzenleme veya yönetme](../../azure-monitor/platform/alerts-log.md)

