---
title: Azure dijital TWINS ve Time Series Insights arasında model eşitleme | Microsoft Docs
description: ADT 'deki varlık modelini Azure TSI 'daki varlık modeline çevirmek için kullanılan en iyi uygulamalar ve araçlar
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/19/2021
ms.custom: dpalled
ms.openlocfilehash: 5e8e5367daa532fa0cc36a18cd28b382e7cd8f78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682911"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Azure Digital Twins ve Time Series Insights 2. Nesil arasında model eşitlemesi

Bu makalede, Azure dijital TWINS 'teki (ADT) varlık modelini Azure Time Series Insights (TSI) ' deki varlık modeline dönüştürmek için kullanılan en iyi uygulamalar ve araçlar açıklanmaktadır.  Bu makale, Azure dijital TWINS 'in Azure Time Series Insights ile tümleştirilmesini açıklayan iki bölümlü bir öğretici serisinin ikinci parçasıdır. Azure dijital TWINS 'in Time Series Insights ile tümleştirilmesi, dijital TWINS 'in Telemetriler ve hesaplanmış özelliklerinin geçmişini arşivlemeyi ve izlemeyi mümkün. Bu öğretici serisi, Azure dijital TWINS ile Time Series Insights tümleştirmeye çalışan geliştiricilere yönelik olarak tasarlanmıştır. 1. bölüm,  [Azure dijital TWINS 'den gerçek zamanlı seri verileri Time Series Insights ve buna getiren veri işlem hattının](../digital-twins/how-to-integrate-time-series-insights.md) oluşturulmasına ve Bu öğreticinin ikinci bölümünde, Azure Digital TWINS ve Time Series Insights arasındaki varlık modeli eşitlemesini açıklamaktadır. Bu öğreticide, zaman serisi KIMLIĞI (TS KIMLIĞI) için adlandırma kuralını seçme ve oluşturma ve zaman serisi modelinde (TSD) hiyerarşileri el ile oluşturma konusunda en iyi yöntemler açıklanmaktadır.

## <a name="choosing-a-time-series-id"></a>Zaman serisi KIMLIĞI seçme

Zaman serisi KIMLIĞI, Time Series Insights Varlıkları tanımlamak için kullanılan benzersiz bir tanımlayıcıdır. Zaman serisi verileri (saat-değer çiftleri olan alandan Telemetriler), TSıD altında listelenen değişkenler kullanılarak temsil edilir. Azure dijital TWINS 'de, ikizi özellikleri ve Telemetriler, sırasıyla ikizi tarafından üretilen ikizi ve ölçümlerin durumunu temsil etmek için kullanılır. TSD 'nin geçerli tasarımının itibariyle, TSID 'lerin benzersiz olması gerekir. Azure dijital TWINS 'de TWINS 'in ikizi kimliklerini kullanma veya WITH özelliği ya da telemetri adı ile birlikte, her zaman TSD 'de benzersiz TS KIMLIĞI oluşturacak. Tipik bir durumda, TSıD olur **_`<Twin ID>`_** ve özellik ve telemetri adları TSD 'daki değişkenler olacaktır. Ancak, Time Series Insights ' deki varlık hiyerarşileri, gibi bileşik anahtarlar biçimi kullanılarak düzleştirildiğinde tercih edilen kullanım durumları vardır **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** . Daha sonraki bir durumu açıklamak için bir örnek alalım. İkizi olarak modellenen bir bina ve ikizi ID Room22 içeren bir oda düşünün. Sıcaklık ayarı özelliği, TSD 'de **_Room22_TempMea_** çevrilecek olan **_TSID Room22_TempSetting_** ve sıcaklık ölçümü olarak çevrilmelidir.

[![Zaman serisi KIMLIĞI seçme](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Bir zaman serisini al

Time Series Insights içindeki verilerin (çoğunlukla doğası açısından uzamsal) şeması, varlık hiyerarşileri aracılığıyla elde edilir ve aynı şekilde, Time Series Insights Gezgini 'ndeki ağaç görünümü aracılığıyla verilerin kolay bir şekilde gezinilmesi için kullanılır. Zaman serisi türleri ve Hiyerarşiler, Time Series Insights zaman serisi modeli (TSD) kullanılarak tanımlanır. TSD 'daki türler, ağaç görünümünü Time Series Insights Gezgini 'nde oluşturmak için hiyerarşi düzeyleri ve örnek alan değerleri kullanıldığında değişkenleri tanımlamaya yardımcı olur. TSD hakkında daha fazla bilgi için [çevrimiçi Time Series Insights belgelerine](./concepts-model-overview.md)bakın.

Azure dijital TWINS 'de, varlıklar arasındaki bağlantı, ikizi ilişkiler kullanılarak ifade edilir. İkizi ilişkiler yalnızca bağlı varlıkların bir grafiktir. Ancak zaman serisi öngörüleri içinde, varlıklar arasındaki ilişkiler doğası gereği hiyerarşik bir şekilde yapılır. Diğer bir deyişle, varlıklar bir üst-alt tür od ilişkisini paylaşır ve bir ağaç yapısı kullanılarak temsil edilir. Azure dijital TWINS 'deki ilişki bilgilerini Time Series Insights hiyerarşilere dönüştürmek için Azure dijital TWINS 'den ilgili hiyerarşik ilişkileri seçmemiz gerekir. Azure dijital TWINS, dijital Ikizi tanım dili (DTDL) adlı bir açık standart, modelleme dili kullanır. DTDL modellerinde JSON-LD adlı bir JSON değişkeni kullanılarak açıklanmıştır. Belirtile ilgili tüm ayrıntılar için [Dtdl belgelerine](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) bakın.

[![Varlıklar arasında bağlantı](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Azure dijital TWINS 'teki grafik gösterimini Time Series Insights ağaç yapısına çevirme

Öğreticinin aşağıdaki bölümleri, Azure dijital TWINS 'teki grafik yapısını Time Series Insights içindeki ağaç yapısına el ile çevirmeye yönelik birkaç temel senaryoyu yakalar.

Örnek sistem: Bu öğretici aşağıda açıklanan kavramları açıklamak için aşağıdaki örneği kullanır. Tek bir kat ve iki oda bulunan basit, kurgusal bir yönetim sistemidir. Her bir odadan biri ve tabanda bir tane olmak üzere üç adet termostats vardır. Ayrıca, Room21 ' den Room22 ' ye kadar olan su akışını ölçen bir su akışı ölçer de vardır. TWINS arasındaki uzamsal ilişkiye bakarak her iki ilişki türü de vardır.

1. En yaygın, hiyerarşik ilişki. Örneğin, Building40-> Floor01-> FloorTS *-> sıcaklığı
1. Bu yüzden yaygın değil, dairesel ilişki. Örneğin, Building40 'den başlayarak FlowMtr iki farklı yoldan izlenebilir.

   1. Building40-> Floor01-> Room21-> FlowMtr *-> Flow
   1. Building40-> Floor01-> Room22-> FlowMtr *-> Flow  
      Burada "Flow" Room21 ile Room22 arasındaki su akışını ölçen gerçek telemetri

> [!Note]
>
> `*` FloorTS, Floortersel stat ve FlowMtr için akış ölçer için ve genellikle TSıD olarak seçilmiş anlamına gelir. Sıcaklık ve akış, Time Series Insights değişken olarak adlandırılan ham telemetridir.

Bir varlığın birden çok dalda temsil edimeyeceği Time Series Insights geçerli sınırlaması verildiğinde, aşağıdaki bölümlerde Time Series Insights hiyerarşik ve dairesel ilişkilerin modellenmesi açıklanmaktadır.

## <a name="case-1-hierarchical-parent-child-relationship"></a>Durum 1: hiyerarşik (üst-alt) ilişki

Bu, TWINS arasındaki en yaygın ilişki türüdür. , saf üst-alt ilişkisinin modellenmesi aşağıdaki çizimde açıklanmıştır. Örnek alanları ve TSıD, aşağıda gösterildiği gibi ikizi kimliklerinden türetilir. Örnek alanları Time Series Insights Explorer kullanılarak el ile güncelleştirileken, "API 'Leri kullanarak örnek alanlarını güncelleştirme" adlı bölüm, Azure dijital TWINS 'de değişiklikleri modelleyen ve Azure işlevleri kullanılarak Time Series Insights örnek alanlarını güncelleştiren bir makalede açıklanmıştır.

[![İkizi kimliklerini eşleme](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![Eşleme ikizi kimlikleri 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>Durum 2: dairesel ilişki

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Azure dijital TWINS 'de Time Series Insights ' de tek hiyerarşi ilişkisine döngüsel ilişki

TSıD 'nin benzersiz olması ve yalnızca bir hiyerarşide temsil edilebilmesi için, bu durum yalnızca ikizi _' Room21 '_ altında _' Flow '_ adlı bir telemetri Içeren _' flowmtr '_ öğesini temsil eder. Time Series Insights, TSD 'deki zaman serisinin çoklu temsilini destekleyebildiğini destekliyorsa, telemetri _' Flow_ ' _' Oda 21 '_ ve _' Oda 22 '_ altında temsil edilir

Aşağıdaki ekran görüntüsünde, ikizi kimliklerini TSD 'daki örnek alanına ve Time Series Insights elde edilen hiyerarşiyi el ile eşleme gösterilmektedir.

[![Azure dijital TWINS 'de ikizi kimliklerini eşleme](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Azure dijital TWINS 'de Time Series Insights ' deki birden çok hiyerarşilere, yinelemeleri kullanarak dairesel ilişki

Öğreticinin 1. bölümünde, bir istemci programı (bir Azure işlevi), telemetri verilerini IoT Hub veya diğer olay kaynaklarından Azure dijital TWINS 'e aktarma konusunda nasıl yardımcı olduğunu açıklar. Bu yaklaşım, üst TWINS 'in ilgili özelliklerinde güncelleştirme yapmak için aynı istemci programını kullanmanızı önerir. Verilen örnekte, FlowMtr Telemetriyi okurken IoT Hub ve FlowMtr ikizi içinde "Flow" özelliği güncelleştirilerek, program kaynağın tüm olası üst ve ilgili özelliklerini de güncelleştirebilir. Bizim örneğimizde, "outflowmea" (Room21 öğesinin ' outflow ' ilişkisinin kullanımı) özelliği ve Room22 'nin "inflowmea" özelliği kullanılarak tanımlanır.  Aşağıdaki ekran görüntüsünde, Time Series Insights Explorer 'da Son Kullanıcı deneyimi gösterilmektedir. Bu yaklaşımı gerçekleştirerek veri yinelememiz olduğunu Not etmemiz gerekir.

[![Time Series Insights Gezgini](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

Aşağıdaki kod parçacığında, istemci uygulamasının Azure dijital TWINS API 'Leri kullanarak ikizi ilişkisinde nasıl gezinilme gösterilmektedir.

> [!Note]
>
> Bu kod parçacığı örneği, okuyucuların öğreticinin [01 bölümünü](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) öğrenmekte olduğunu ve bu kod değişikliğinin "ProcessHubToDTEvents&quot; işlevi içinde yapıldığını varsayar.

```csharp
if (propertyPath.Equals(&quot;/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>API 'Leri kullanarak örnek alanlarını güncelleştirme

Öğreticinin bu bölümünde, Azure dijital TWINS 'te oluşturma, TWINS silme veya TWINS arasındaki ilişkilerde değişiklik yapmak ve Time Series Insights modeli API 'Lerini kullanarak örnek alanları ve hiyerarşileri güncelleştirmek gibi değişiklikleri modelleyerek nasıl yapılacağı açıklanmaktadır. Time Series Insights modeli güncelleştirme yöntemi genellikle Azure işlevleri aracılığıyla gerçekleştirilir. Azure dijital TWINS 'de, ikizi ekleme veya silme gibi olay bildirimleri, Azure işlevlerine beslenebilir Event Hubs gibi bir aşağı akış hizmeti olabilir. Olay Yönlendirme ve filtreleme hakkında daha fazla ayrıntı [burada](../digital-twins/how-to-manage-routes-portal.md)açıklanmıştır.  Bu bölümün geri kalanında, Azure Digital TWINS 'te ikizi ekleme (bir tür model değişikliği) karşılığında Time Series Insights modeli güncelleştirmek için Azure işlevlerinde Time Series Insights modeli API 'Lerinin kullanılması açıklanmaktadır.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>İkizi ek olay bildirimi alma ve tanımlama

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Time Series Insights Istemcisi oluşturuluyor ve örnek ayrıntıları ekleniyor

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>Örneğe hiyerarşi bilgisi uygulanıyor

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>Sonraki adımlar

Üçüncü öğreticilerde, Time Series Insights API 'Lerini kullanarak Azure dijital TWINS 'ten geçmiş verilerin nasıl sorgulanalınacağını gösterirsiniz. Sürmekte olan bir çalışmadır ve bölüm hazırlanmaya devam eder. Bu sırada, okuyucuların [Time Series Insights veri sorgusu API 'si belgelerine](./concepts-query-overview.md)başvurması önerilir.
