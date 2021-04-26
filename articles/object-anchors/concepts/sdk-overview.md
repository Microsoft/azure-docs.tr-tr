---
title: Çalışma zamanı SDK 'sına genel bakış
description: Nesne bağlantıları çalışma zamanı SDK 'sını öğrenin.
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 551374824610c0257aaf52c45768d31849026524
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047550"
---
# <a name="runtime-sdk-overview"></a>Çalışma zamanı SDK 'sına genel bakış

Bu bölüm, nesne tutturucular modeli kullanarak nesneleri algılamak için kullanılan nesne tutturucular çalışma zamanı SDK 'sına yönelik üst düzey bir genel bakış sağlar. Bir nesnenin nasıl temsil edileceğini ve çeşitli bileşenlerin ne şekilde kullanıldığını anlayacaksınız.

Aşağıda açıklanan türlerin tümü **Microsoft. MixedReality. ObjectAnchors** ad alanında bulunabilir.

## <a name="types"></a>Türler

### <a name="objectmodel"></a>ObjectModel

[ObjectModel](/dotnet/api/microsoft.azure.objectanchors.objectmodel) bir fiziksel nesnenin geometrisini temsil eder ve algılama ve poz tahmini için gerekli parametreleri kodlar. [Nesne bağlayıcıları hizmeti](../quickstarts/get-started-model-conversion.md)kullanılarak oluşturulması gerekir. Ardından bir uygulama, nesne bağlantıları API 'sini kullanarak oluşturulan model dosyasını yükleyebilir ve görselleştirme için bu modele gömülü olan ağı sorgulayabilir.

### <a name="objectsearcharea"></a>ObjectSearchArea

[Objectsearcharea](/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) bir veya daha fazla nesne aramak için alanı belirtir. Uzamsal grafik düğüm KIMLIĞI ile temsil edilen koordinat sisteminde uzamsal bir grafik düğüm KIMLIĞI ve uzamsal sınırlar tarafından tanımlanır. Nesne tutturucular çalışma zamanı SDK 'Sı dört tür sınır destekler, yani **Görünüm alanı**, **sınırlayıcı kutusu**, **Sphere** ve bir **konum**.

### <a name="objectquery"></a>ObjectQuery

Bir [ObjectQuery](/dotnet/api/microsoft.azure.objectanchors.objectquery) , bir **nesneye gözlemciye** belirli bir modelin nesnelerinin nasıl bulunacağını söyler. Varsayılan değerleri bir nesne modelinden alınabilecek aşağıdaki ayarlanabilir parametrelerini sağlar.

#### <a name="minsurfacecoverage"></a>Minsurçok yönlü kapsam

[Minsurcecoverage](/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) özelliği, algılanan bir örneği kabul etmek için değeri gösterir.

Her bir nesne adayı için, bir **gözlemci** , dönüştürülmüş nesne modeli ve sahne arasındaki örtüşen yüzeylerin oranını hesaplar ve ardından, yalnızca kapsam oranının belirli bir eşiğin üstünde olduğu durumlarda bu aday uygulamayı uygulamaya bildirir.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

[IsExpectedToBeStandingOnGroundPlane](/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) özelliği, hedef nesnenin taban düzlemde tek başına beklenip beklenmediğini belirtir.

Zemin düzlemi, arama alanındaki en düşük yatay tabandır. Olası nesne pozları üzerinde iyi bir kısıtlama sağlar. Bu bayrağı açmak, **gözlemciye** sınırlı bir alanda pozu tahmin etmeye kılavuzluk eder ve doğruluğu iyileştirebilir. Modelin taban düzlede tek olması gerekiyorsa bu parametre yok sayılır.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

[ExpectedMaxVerticalOrientationInDegrees](/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) özelliği, bir nesne örneğinin yukarı yönü ve yerçekimi arasındaki derece cinsinden beklenen maksimum açıyı gösterir.

Bu parametre, tahmini bir poz için yukarı yönde başka bir kısıtlama sağlar. Örneğin, bir nesne sağ, bu parametre 0 olabilir. Nesne çıpası modelden farklı olan nesneleri algılamamalıdır. Bir model yukarı doğru ise, açılan bir örneği algılamaz. Yan yana düzen için yeni bir model kullanılacak. İfade için aynı kural geçerlidir.

#### <a name="maxscalechange"></a>MaxScaleChange

[Maxscalechange](/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) özelliği, uzamsal eşlemeye göre en fazla nesne ölçeği değişikliğini (0 ~ 1 içinde) gösterir. Tahmini ölçek, kaynak ve eksen hizalı olarak ortalanmış dönüştürülmüş nesne köşelerine uygulanır. Tahmini ölçekler, bir CAD modeli ve fiziksel temsili arasındaki gerçek ölçek olamaz, ancak uygulamanın bir nesne modelini fiziksel nesne üzerinde uzamsal eşlemeye yakın bir şekilde işlemesine izin veren bazı değerler olabilir.

#### <a name="searchareas"></a>SearchAreas

[Searchareas](/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) özelliği, nesne (ler) in bulunacağı bir uzamsal sınır dizisini gösterir.

**Gözlemci** , bir sorguda belirtilen tüm arama alanlarının birleşim alanındaki nesneleri arayacaktır. Bu sürümde, gecikme süresini azaltmak için en yüksek güvenle bir nesne döndürüyoruz.

### <a name="objectinstance"></a>ObjectInstance

Bir [ObjectInstance](/dotnet/api/microsoft.azure.objectanchors.objectinstance) , belirli bir modelin bir örneğinin HoloLens koordinat sisteminde bulunabileceği kuramsal bir konumu temsil eder. Her örnek `SurfaceCoverage` , tahmini pozun ne kadar iyi olduğunu göstermek için bir özellik ile birlikte gelir.

Bir örnek yöntemi çağırarak oluşturulur `ObjectObserver.DetectAsync` ve sonra etkin olduğunda arka planda otomatik olarak güncelleştirilir. Bir uygulama belirli bir örnekteki durum değişikliği olayını dinleyebilir veya güncelleştirmeyi duraklatmak/devam etmek için izleme modunu değiştirebilir. İzleme kaybolduğunda bir örnek otomatik olarak **gözlemci** 'den kaldırılacaktır.

### <a name="objectobserver"></a>ObjectObserver

Bir [Objectobserver](/dotnet/api/microsoft.azure.objectanchors.objectobserver) nesne modellerini yükler, örneklerini algılar ve, Hololens koordinat sisteminde her bir örneğe ait 6-DOF pozlarını algılar.

Bir **gözlemci** içinden herhangi bir nesne modeli veya örnek oluşturulsa da, bunların yaşam süreleri bağımsızdır. Bir uygulama bir gözlemci elden çıkarıp nesne modelini veya örneğini kullanmaya devam edebilir.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

[Objectdiagnosticsession](/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) , tanılamayı kaydeder ve verileri bir arşive yazar.

Bir tanılama Arşivi, sahne noktası bulutu, gözlemcinin durumunu ve modeller hakkındaki bilgileri içerir. Bu bilgiler, olası çalışma zamanı sorunlarını belirlemek için faydalıdır. Daha fazla bilgi için bkz. [SSS](../faq.md).

## <a name="runtime-sdk-usage-and-details"></a>Çalışma zamanı SDK kullanımı ve ayrıntıları

Bu bölüm, çalışma zamanı SDK 'sının nasıl kullanılacağına ilişkin temel bilgileri sağlamalıdır. Nesne çıpasının nasıl kullanıldığını görmek için örnek uygulamalara göz atabilmek üzere size yeterli bağlam sunmalıdır.

### <a name="initialization"></a>Başlatma

Uygulamaların kullanılmadan `ObjectObserver.IsSupported()` önce cihazda desteklenip desteklenmediğini anlamak için API 'yi çağırması gerekir. API döndürülürse `ObjectObserver.IsSupported()` `false` , uygulamanın **Spatialperception** özelliğini ve \ ' yi etkinleştirmiştir ve en son HoloLens işletim sistemine yükseltin.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

Ardından, uygulama bir gözlemci oluşturur ve [nesne bağlayıcı modeli dönüştürme hizmeti](../quickstarts/get-started-model-conversion.md)tarafından oluşturulan gerekli modelleri yükler.

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

Uygulama, bir alan içindeki bu modelin örneklerini algılamak için bir sorgu oluşturur.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

Varsayılan olarak, algılanan her örnek **gözlemci** tarafından otomatik olarak izlenir. İsteğe bağlı olarak bu örnekleri, izleme modunu değiştirerek veya durum değiştirilen olayına dinleyerek işleyebilir.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

Durum değişti olayında, en son durumu sorgulayabilir veya izlemeyi kaybettiyseniz bir örneği atabilirsiniz.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

Ayrıca, bir uygulama çevrimdışı hata ayıklama için isteğe bağlı olarak bir veya birden çok Tanılama oturumu kaydedebilir.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Son olarak, tüm nesneleri elden ayırarak kaynakları serbest bıraktık.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```