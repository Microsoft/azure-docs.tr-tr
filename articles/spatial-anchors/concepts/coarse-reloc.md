---
title: Kaba yeniden yerelleştirme | Microsoft Docs
description: Kaba yeniden yerelleştirme hızlı başlangıç kılavuzu.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 1d9e58f2d7eda818665a6253a8d0508104b17405
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093884"
---
# <a name="coarse-relocalization"></a>Kaba yeniden yerelleştirme

Kaba yeniden yerelleştirme, soruya bir başlangıç yanıtı sağlayan bir özelliktir: *cihazım Şu anda nerede, ne kadar hangi içerikleri gözlemliyim?* Yanıt kesin değil, ancak bunun yerine şu biçimdedir: *Bu Tutturucuların yakınına yakınız, bunlardan birini konumlandırmayı deneyin*.

Farklı cihaz algılayıcı okumaları, hem oluşturma hem de Tutturucuların sorgulanması ile ilişkilendirerek etkili bir şekilde yeniden yerelleştirme çalışmaktadır. Açık Hava senaryolarında, algılayıcı verileri genellikle cihazın GPS (Global Konumlandırma Sistemi) konumudur. GPS kullanılabilir olmadığında veya güvenilir olmadığında (ınkapılar gibi), algılayıcı verileri WiFi erişim noktaları ve zaman içindeki Bluetooth işaretleriyle oluşur. Tüm toplanan algılayıcı verileri, bir uzamsal dizinin sürdürülmesi için katkıda bulunur. Uzamsal dizin, cihazınızın yaklaşık 100 ölçümlerinde bulunan bağlantıları hızlıca belirleyebilmek için tutturucu hizmeti tarafından yararlanılabilir.

Kaba reyerelleştirme tarafından etkinleştirilen bağlayıcıların hızlı bakış özelliği, dünya ölçeğinde (milyonlarca coğrafi olarak dağıtılmış) çıpası tarafından desteklenen uygulamaların geliştirilmesini basitleştirir. Bağlantı yönetiminin karmaşıklığı tamamen gizlenir ve bu da başar uygulama mantığınıza daha fazla odaklanmanızı sağlar. Tüm bağlantı ağır kaldırma işlemi, hizmet tarafından arka planda sizin için yapılır!

## <a name="collected-sensor-data"></a>Toplanan algılayıcı verileri

Bağlantı hizmetine gönderebilmeniz için sensör verileri aşağıdakilerden biridir:

* GPS konumu: enlem, Boylam, yükseklik.
* Aralıktaki WiFi erişim noktalarının sinyal gücü.
* Aralıktaki Bluetooth işaretlerinin sinyal gücü.

Genel olarak, uygulamanızın GPS, WiFi veya BLE verilerine erişmek için cihaza özgü izinleri edinmeniz gerekir. Ayrıca, yukarıdaki bazı algılayıcı verileri belirli platformlarda tasarlanarak kullanılamaz. Bu durumları hesaba eklemek için, algılayıcı verisi koleksiyonu isteğe bağlıdır ve varsayılan olarak kapalıdır.

## <a name="set-up-the-sensor-data-collection"></a>Algılayıcı veri toplamayı ayarlama

Bir algılayıcı parmak izi sağlayıcısı oluşturup oturum bunu fark ederek başlayalım:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrttabcppwinrt"></a>[C++Wınrt](#tab/cppwinrt)
```cpp
// Create the ASA factory
SpatialAnchorsFactory m_asaFactory { nullptr };
// . . .

// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider;
sensorProvider = m_asaFactory.CreatePlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Daha sonra, ne kadar kaba yeniden yerelleştirme için kullanmak istediğiniz sensöre karar vermeniz gerekir. Bu karar, genel olarak, geliştirmekte olduğunuz uygulamaya özeldir, ancak aşağıdaki tablodaki önerilerin size iyi bir başlangıç noktası vermesi gerekir:


|             | Inkapıların | Dış kapılar |
|-------------|---------|----------|
| YUVASı         | Kapalı | Açık |
| WiFi        | Açık | Açık (isteğe bağlı) |
| BLO işaretleri | Açık (uyarılar ile isteğe bağlı olarak aşağıya bakın) | Kapalı |


### <a name="enabling-gps"></a>GPS etkinleştiriliyor

Uygulamanızın cihazın GPS konumuna erişim izni olduğu varsayılırsa, Azure uzamsal bağlayıcılarını kullanmak için yapılandırabilirsiniz:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++Wınrt](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Uygulamanızda GPS kullanırken, donanım tarafından sunulan okumalar tipik olarak şunları göz önünde bulundurun:

* zaman uyumsuz ve düşük sıklık (1 Hz 'den az).
* güvenilir olmayan/gürültülü (ortalama 7-b standart sapması).

Genel olarak, hem cihaz işletim sistemi hem de Azure uzamsal bağlantıları, bu sorunları azaltmak için ham GPS sinyalinde bazı filtreleme ve tahmin işlemi yapar. Bu ek işleme yakınsama için ek zaman gerektirir, bu nedenle en iyi sonuçlar için şunu deneyin:

* bir algılayıcı parmak izi sağlayıcısını uygulamanızda olabildiğince erken oluşturun
* algılayıcı parmak izi sağlayıcısını canlı tutun ve birden çok oturum arasında paylaşma

Algılayıcı parmak izi sağlayıcısını bir bağlantı oturumu dışında kullanmayı planlıyorsanız, algılayıcı tahminleri yapmadan önce bu hizmeti başlattığınızdan emin olun. Örneğin, aşağıdaki kod, cihazınızın haritadaki konumunu gerçek zamanlı olarak güncelleştirmekte olacaktır:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrttabcppwinrt"></a>[C++Wınrt](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>WiFi etkinleştiriliyor

Uygulamanızın cihazın WiFi durumuna erişim izni olduğu varsayılırsa, Azure uzamsal bağlayıcılarını kullanmak için yapılandırabilirsiniz:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++Wınrt](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Uygulamanızda WiFi kullanırken, donanım tarafından sunulan okumalar genellikle şunları göz önünde bulundurun:

* zaman uyumsuz ve düşük sıklık (0,1 Hz 'den az).
* işletim sistemi düzeyinde kısıtlanmış olabilir.
* güvenilmez/gürültülü (ortalama 3 dBm standart sapma üzerinde).

Azure uzamsal bağlantıları, bu sorunları azaltmak için bir oturum sırasında filtrelenmiş bir WiFi sinyali gücü eşlemesi oluşturmayı dener. En iyi sonuçlar için şunu denemeniz gerekir:

* ilk tutturucu yerleştirmekten önce oturum iyi oluşturun.
* oturumu mümkün olduğunca uzun tutun (yani, tüm tutturucuları ve tek bir oturumda sorgu oluşturun).

### <a name="enabling-bluetooth-beacons"></a>Bluetooth işaretlerini etkinleştirme

Uygulamanızın cihazın Bluetooth durumuna erişim izni olduğu varsayılırsa, Azure uzamsal bağlayıcılarını kullanmak için yapılandırabilirsiniz:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++Wınrt](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

İşaretlerin genellikle, UUID 'ler ve MAC adresleri gibi her şeyin yapılandırılabileceği çok yönlü cihazlardır. Bu esneklik, işaretleri UUID 'ler tarafından benzersiz şekilde tanımlanabilecek Azure uzamsal çıpası için sorunlu olabilir. Bu benzersizlik 'in büyük olasılıkla uzamsal solucan delikleri içine çevirdiğinden emin olunması başarısız. En iyi sonuçlar için şunları yapmanız gerekir:

* işaretlerine benzersiz UUID 'ler atayın.
* Bunları, genellikle kılavuz gibi normal bir düzende dağıtın.
* benzersiz işaret UUID 'ler listesini algılayıcı parmak izi sağlayıcısına geçirin:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrttabcppwinrt"></a>[C++Wınrt](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure uzamsal bağlantıları yalnızca listedeki Bluetooth işaretlerini izler. Beyaz listelenmiş UUID 'ler için programlı olarak programlanmış kötü amaçlı işaretleri, hizmetin kalitesini de olumsuz yönde etkileyebilir. Bu nedenle, işaretlerini yalnızca, dağıtımını denetleyebileceğiniz, yalnızca seçkin alanlarda kullanmanız gerekir.

## <a name="querying-with-sensor-data"></a>Algılayıcı verileriyle sorgulama

İlişkili algılayıcı verileriyle bağlayıcı oluşturduktan sonra, cihazınızın bildirdiği algılayıcı ayarlarını kullanarak bunları almaya başlayabilirsiniz. Artık, bulmayı beklediğiniz bilinen Tutturucuların bir listesini sağlamanız gerekmez. bunun yerine, yalnızca hizmetin, kendi ekleme algılayıcılarının bildirdiği şekilde cihazınızın konumunu bilmesini sağlayabilirsiniz. Daha sonra uzamsal bağlayıcı hizmeti, cihazınıza yakın olan bağlayıcı kümesini şekillendirilecektir ve bunları görsel olarak eşleştirmeye çalışır.

Sorguların algılayıcı verilerini kullanması için, bulma ölçütü oluşturarak başlayın:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++Wınrt](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = m_asaFactory.CreateNearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = m_asaFactory.CreateAnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

`DistanceInMeters` parametresi, içerik almak için tutturucu grafiği ne kadar keşfedeceğiz denetler. Her ölçünün 2. sabitinde yer alan bağlayıcılarla bir boşluk doldurmuş olduğunuz örneği varsayalım. Ayrıca, cihazınızdaki kamera tek bir tutturucu gözlemleyerek hizmet tarafından başarıyla bulunur. En büyük olasılıkla, şu anda gözlemlediğiniz tek yer yerine, yerleştirdiğiniz tüm Tutturucuların alınması çok büyük olabilir. Yerleştirdiğiniz Tutturucuların bir grafiğe bağlı olduğu varsayıldığında, hizmet grafikteki kenarları izleyerek sizin için tüm yakın bağlantıları alabilir. Gerçekleştirilen grafik geçişi miktarı `DistanceInMeters`tarafından denetlenir; bulduğunuz birine bağlı olan tüm Tutturucuların `DistanceInMeters`daha yakınına vermiş olursunuz.

`MaxResultCount` için büyük değerlerin performansı olumsuz yönde etkileyebileceğini aklınızda bulundurun. Uygulamanızı anlamlı hale getiren bir değere ayarlamayı deneyin.

Son olarak, oturuma algılayıcı tabanlı arama 'yı kullanmayı söylemeniz gerekir:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++Wınrt](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Beklenen sonuçlar

Tüketici sınıfı GPS cihazları genellikle oldukça kesin. [Zandenbergen ve Barbeau][6] tarafından yapılan bir inceleme (2011), destekli cep telefonlarını (A-GPS) 7 ölçümle (oldukça büyük bir değer yok sayılır) bir şekilde raporlar. Bu ölçüm hatalarına yönelik hesaba geçmek için hizmet, bağlantıları GPS alanında olasılık dağıtımları olarak değerlendirir. Bu nedenle, bir tutturucu, büyük olasılıkla en olası alan bölgesidir (yani %95 ' ten fazla güven), gerçek, bilinmeyen GPS konumunu içerir.

GPS ile sorgulama yapılırken aynı düşünme uygulanır. Cihaz, doğru, bilinmeyen GPS konumu etrafında başka bir uzamsal güven bölgesi olarak temsil edilir. Yakın olan Tutturucuların bulunması, aşağıdaki görüntüde gösterildiği gibi, güvenilir bölgelere sahip olan bağlantıları cihazın güven bölgesine *yeterince yakın* bir şekilde bulmak için çeviri yapar:

![GPS ile bağlayıcı adayları seçimi](media/coarse-reloc-gps-separation-distance.png)

Her ikisi de, hem bağlayıcı oluşturma hem de sorgular sırasında, döndürülen bağlantı kümesi üzerinde büyük bir etkisi olan GPS sinyalinin doğruluğu. Buna karşılık, WiFi/işaret tabanlı sorgular, sorguyla ortak olarak en az bir erişim noktası/işaret olan tüm bağlantıları kabul eder. Bu anlamda, WiFi/işaretlerine dayalı bir sorgunun sonucu genellikle erişim noktalarının/işaretlerinin ve çevre engellerin fiziksel aralığına göre belirlenir.

Aşağıdaki tabloda her algılayıcı türü için beklenen arama alanı tahmin edilecek:

| Algılayıcısı      | Arama alanı yarıçapı (yaklaşık) | Ayrıntılar |
|-------------|:-------:|---------|
| YUVASı         | 20 milyon-30 milyon | Diğer faktörler arasındaki GPS tarafından belirlenir. Raporlanan sayılar, bu 7 ölçüm olan,-GPS ile cep telefonlarına ait ortanca GPS doğruluğu için tahmin edilir. |
| WiFi        | 50 milyon-100 milyon | Kablosuz erişim noktalarının aralığına göre belirlenir. Sıklık, verici kuvveti, fiziksel engelleri, girişim vb. bağlıdır. |
| BLO işaretleri |  70 milyon | İşaret aralığına göre belirlenir. Sıklık, iletim gücüne, fiziksel engelleri, girişim vb. bağlıdır. |

## <a name="per-platform-support"></a>Platform başına destek

Aşağıdaki tabloda, desteklenen her platformda toplanan algılayıcı verileri, platforma özel uyarılarla birlikte özetlenmektedir:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| YUVASı         | Yok | [LocationManager][3] API 'LERI (GPS ve ağ) aracılığıyla desteklenir | [Cllocationmanager][4] API 'leri aracılığıyla desteklenir |
| WiFi        | Her 3 saniyede bir taramanın yaklaşık bir hızda desteklenir | Destekleniyor. WiFi taramaları, API düzeyi 28 ile başlayarak 2 dakikada bir 4 çağrı ile kısıtlanır. Android 10 ' dan azaltma, Geliştirici ayarları menüsünden devre dışı bırakılabilir. Daha fazla bilgi için bkz. [Android belgeleri][5]. | Yok-ortak API yok |
| BLO işaretleri | [Eddystone][1] ve [ıişaret][2] ile sınırlı | [Eddystone][1] ve [ıişaret][2] ile sınırlı | [Eddystone][1] ve [ıişaret][2] ile sınırlı |

## <a name="next-steps"></a>Sonraki adımlar

Bir uygulamada kaba yeniden yerelleştirme kullanın.

> [!div class="nextstepaction"]
> ['Yi](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/Wınrt](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9