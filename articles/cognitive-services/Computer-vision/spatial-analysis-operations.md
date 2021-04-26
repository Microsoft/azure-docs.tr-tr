---
title: Uzamsal analiz işlemleri
titleSuffix: Azure Cognitive Services
description: Uzamsal analiz işlemleri.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 37ac7573a1794c97c81fe5364204f85ff14d9fa6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538075"
---
# <a name="spatial-analysis-operations"></a>Uzamsal analiz işlemleri

Uzamsal analiz, kamera cihazlarından gerçek zamanlı akış videosunun analizine izin vermez. Yapılandırdığınız her kamera cihazında, uzamsal analize yönelik işlemler, Azure IoT Hub örneğinizle gönderilen JSON iletilerinin çıktı akışını oluşturacaktır. 

Uzamsal analiz kapsayıcısı aşağıdaki işlemleri uygular:

| İşlem tanımlayıcısı| Açıklama|
|---------|---------|
| biliveservices. Vision. spatialanalysis-PersonCount | Kameranın görünüm alanındaki belirli bir bölgedeki kişileri sayar. PersonCount 'un doğru toplamı kaydedebilmesi için bölgenin tek bir kamera tarafından tam olarak kapsanmış olması gerekir. <br> Sayı değiştiğinde ilk bir _personcountevent_ olayını ve sonra da _personcountevent_ olaylarını yayar.  |
| biliveservices. Vision. spatialanalysis-personcrossingline | Bir kişinin, kameranın görünüm alanındaki belirli bir satırı ne zaman kesişdiğini izler. <br>Kişi çizgiyi kesen ve yön bilgisi sağlayan bir _Personlineevent_ olayı yayar. 
| biliveservices. Vision. spatialanalysis-personcrossingçokgen | Bir kişi bölgeden girdiğinde ya da çıktığında ve çapraz bölgenin numaralı tarafıyla yönlü bilgi sağlayan bir _Personzoneentertevent_ olayı yayar. Kişi bölgeden çıktığında bir _Personzonedwelltimeevent_ yayar ve bir kişinin bölge içinde harcadığı milisaniye sayısını ve yönlü bilgi sağlar. |
| biliveservices. Vision. spatialanalysis-persondistance | İnsanlar bir uzaklık kuralını ihlal ediyor. <br> Her uzaklık ihlalinin konumuyla düzenli aralıklarla bir _personDistanceEvent_ yayar. |
| biliveservices. Vision. spatialanalysis | Yukarıda bahsedilen tüm senaryoları çalıştırmak için kullanılabilen genel işlem. Bu seçenek, aynı kamerada birden çok senaryo çalıştırmak veya sistem kaynaklarını (ör. GPU) daha verimli bir şekilde kullanmak istediğinizde daha yararlıdır. |

Tüm yukarıdaki işlemler, `.debug` işlenmekte oldukları gibi video çerçevelerini görselleştirme özelliğine sahip olan sürümde de mevcuttur. `xhost +`Video çerçevelerinin ve olaylarının görselleştirilmesini sağlamak için ana bilgisayarda çalıştırmanız gerekir.

| İşlem tanımlayıcısı| Açıklama|
|---------|---------|
| biliveservices. Vision. spatialanalysis-PersonCount. Debug | Kameranın görünüm alanındaki belirli bir bölgedeki kişileri sayar. <br> Sayı değiştiğinde ilk bir _personcountevent_ olayını ve sonra da _personcountevent_ olaylarını yayar.  |
| biliveservices. Vision. spatialanalysis-personcrossingline. Debug | Bir kişinin, kameranın görünüm alanındaki belirli bir satırı ne zaman kesişdiğini izler. <br>Kişi çizgiyi kesen ve yön bilgisi sağlayan bir _Personlineevent_ olayı yayar. 
| biliveservices. Vision. spatialanalysis-personcrossingçokgen. Debug | Bir kişi bölgeden girdiğinde ya da çıktığında ve çapraz bölgenin numaralı tarafıyla yönlü bilgi sağlayan bir _Personzoneentertevent_ olayı yayar. Kişi bölgeden çıktığında bir _Personzonedwelltimeevent_ yayar ve bir kişinin bölge içinde harcadığı milisaniye sayısını ve yönlü bilgi sağlar. |
| biliveservices. Vision. spatialanalysis-persondistance. Debug | İnsanlar bir uzaklık kuralını ihlal ediyor. <br> Her uzaklık ihlalinin konumuyla düzenli aralıklarla bir _personDistanceEvent_ yayar. |
| biliveservices. Vision. spatialanalysis. Debug | Yukarıda bahsedilen tüm senaryoları çalıştırmak için kullanılabilen genel işlem. Bu seçenek, aynı kamerada birden çok senaryo çalıştırmak veya sistem kaynaklarını (ör. GPU) daha verimli bir şekilde kullanmak istediğinizde daha yararlıdır. |

Uzamsal analiz, video AI modülü olarak [canlı video analizi](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) ile de çalıştırılabilir. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| İşlem tanımlayıcısı| Açıklama|
|---------|---------|
| biliveservices. Vision. spatialanalysis-PersonCount. livevideoanalytics | Kameranın görünüm alanındaki belirli bir bölgedeki kişileri sayar. <br> Sayı değiştiğinde ilk bir _personcountevent_ olayını ve sonra da _personcountevent_ olaylarını yayar.  |
| biliveservices. Vision. spatialanalysis-personcrossingline. livevideoanalytics | Bir kişinin, kameranın görünüm alanındaki belirli bir satırı ne zaman kesişdiğini izler. <br>Kişi çizgiyi kesen ve yön bilgisi sağlayan bir _Personlineevent_ olayı yayar. 
| biliveservices. Vision. spatialanalysis-personcrossingçokgen. livevideoanalytics | Bir kişi bölgeden girdiğinde ya da çıktığında ve çapraz bölgenin numaralı tarafıyla yönlü bilgi sağlayan bir _Personzoneentertevent_ olayı yayar. Kişi bölgeden çıktığında bir _Personzonedwelltimeevent_ yayar ve bir kişinin bölge içinde harcadığı milisaniye sayısını ve yönlü bilgi sağlar.  |
| biliveservices. Vision. spatialanalysis-persondistance. livevideoanalytics | İnsanlar bir uzaklık kuralını ihlal ediyor. <br> Her uzaklık ihlalinin konumuyla düzenli aralıklarla bir _personDistanceEvent_ yayar. |
| biliveservices. Vision. spatialanalysis. livevideoanalytics | Yukarıda bahsedilen tüm senaryoları çalıştırmak için kullanılabilen genel işlem. Bu seçenek, aynı kamerada birden çok senaryo çalıştırmak veya sistem kaynaklarını (ör. GPU) daha verimli bir şekilde kullanmak istediğinizde daha yararlıdır. |

Canlı video analizi işlemleri de `.debug` (örneğin, biliveservices. Vision. spatialanalysis-PersonCount. livevideoanalytics. Debug) sürümünde de mevcuttur. Bu, video çerçevelerini işlenen olarak görselleştirme özelliğine sahiptir. `xhost +`Video çerçevelerini ve olaylarını görselleştirmeyi etkinleştirmek için ana bilgisayarda çalıştırmanız gerekir

> [!IMPORTANT]
> Görüntü işleme AI modelleri, insan gövdenin etrafında bir sınırlayıcı kutu kullanarak video filmlerindeki ve çıkışındaki insan varlığını algılar ve bulur. AI modelleri, kişilerin kimliklerini veya demograflarını bulmaya çalışmaz.

Bunlar, bu uzamsal analiz işlemlerinin her biri için gereken parametrelerdir.

| İşlem parametreleri| Açıklama|
|---------|---------|
| İşlem KIMLIĞI | Yukarıdaki tablodan Işlem tanımlayıcısı.|
| enabled | Boolean: true veya false|
| VIDEO_URL| Kamera cihazının RTSP URL 'si (örnek: `rtsp://username:password@url` ). Uzamsal analiz, RTSP, http veya MP4 aracılığıyla H., kodlanmış akışı destekler. Video_URL, AES şifrelemesi kullanılarak karıştırılmış bir Base64 dizesi değeri olarak sağlanır ve video URL 'si karıştırılmış `KEY_ENV` ve `IV_ENV` ortam değişkenleri olarak sağlanması gerekiyorsa. Anahtar ve şifreleme oluşturmak için örnek yardımcı program [burada](/dotnet/api/system.security.cryptography.aesmanaged)bulunabilir. |
| VIDEO_SOURCE_ID | Kamera cihazı veya video akışı için kolay bir ad. Bu, olay JSON çıkışıyla döndürülür.|
| VIDEO_IS_LIVE| Kamera aygıtları için doğru; Kayıtlı videolar için false.|
| VIDEO_DECODE_GPU_INDEX| Video çerçevesinin kodunu çözecek GPU. Varsayılan olarak 0 ' dır. `gpu_index`, Gibi diğer düğüm yapılandırması ile aynı olmalıdır `VICA_NODE_CONFIG` `DETECTOR_NODE_CONFIG` .|
| INPUT_VIDEO_WIDTH | Giriş videosu/akışının çerçeve genişliği (ör. 1920). Bu, isteğe bağlı bir alandır ve sağlanmışsa, en boy oranı korunarak çerçeve bu boyuta ölçeklenmelidir.|
| DETECTOR_NODE_CONFIG | Üzerinde algılayıcı düğümünün çalıştırılacağı GPU 'YU gösteren JSON. Aşağıdaki biçimde olmalıdır: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | Aşağıda özetlenen bölge ve satır için JSON yapılandırması.|
| ENABLE_FACE_MASK_CLASSIFIER | `True` videoyu devre dışı bırakmak için video akışında yüz maskeleri takanlar `False` . Varsayılan olarak devre dışıdır. Yüz maskesini algılama için giriş video genişliği parametresinin 1920 olması gerekir `"INPUT_VIDEO_WIDTH": 1920` . Algılanan insanlar kamerayı karşılamayamıyorsa veya bu bilgisayardan çok uzakta olduğunda yüz maskesi özniteliği döndürülmez. Daha fazla bilgi için [kamera yerleştirme](spatial-analysis-camera-placement.md) kılavuzuna bakın |

### <a name="detector-node-parameter-settings"></a>Algılayıcı düğüm parametre ayarları
Bu, tüm uzamsal analiz işlemleri için DETECTOR_NODE_CONFIG parametrelerine bir örnektir.

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000
}
```

| Ad | Tür| Description|
|---------|---------|---------|
| `gpu_index` | dize| Bu işlemin çalıştırılacağı GPU dizini.|
| `do_calibration` | string | Ayarlama özelliğinin açık olduğunu gösterir. `do_calibration`**biliveservices. Vision. spatialanalysis-persondistance** 'ın düzgün şekilde çalışması için true olması gerekir. do_calibration varsayılan olarak true olarak ayarlanır. |
| `enable_recalibration` | bool | Otomatik yeniden renklendirme özelliğinin açılıp açılmadığını gösterir. `true` varsayılan değerdir.|
| `calibration_quality_check_frequency_seconds` | int | Her kalite denetimi arasındaki en az saniye sayısının, gerekli olup olmadığını belirleme gerekliliği. Varsayılan değer `86400` (24 saat). Yalnızca ne zaman kullanılır `enable_recalibration=True` .|
| `calibration_quality_check_sample_collect_frequency_seconds` | int | Yeni veri örneklerinin toplanması ve kalite denetimi için en az saniye sayısı. Varsayılan değer `300` (5 dakikadır). Yalnızca ne zaman kullanılır `enable_recalibration=True` .|
| `calibration_quality_check_one_round_sample_collect_num` | int | Örnek toplamanın hepsini başına toplanacak en az yeni veri örneği sayısı. `10` varsayılan değerdir. Yalnızca ne zaman kullanılır `enable_recalibration=True` .|
| `calibration_quality_check_queue_max_size` | int | Kamera modeli kalibre edildiğinde depolanacak en fazla veri örneği sayısı. `1000` varsayılan değerdir. Yalnızca ne zaman kullanılır `enable_recalibration=True` .|
| `enable_breakpad`| bool | Hata ayıklama kullanımı için kilitlenme dökümünü oluşturmak için kullanılan BreakPad ' i etkinleştirmek isteyip istemediğinizi belirtir. `false`Varsayılan olarak. Öğesini olarak ayarlarsanız `true` kapsayıcının bölümüne de eklemeniz gerekir `"CapAdd": ["SYS_PTRACE"]` `HostConfig` `createOptions` . Varsayılan olarak, kilitlenme dökümü [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter uygulamasına yüklenir, kilitlenme dökümlerinin kendi AppCenter uygulamanıza yüklenmesini isterseniz, ortam değişkenini `RTPT_APPCENTER_APP_SECRET` uygulamanızın uygulama gizli anahtarı ile geçersiz kılabilirsiniz.

## <a name="spatial-analysis-operations-configuration-and-output"></a>Uzamsal analiz işlemleri yapılandırması ve çıktısı
### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Biliveservices. Vision. spatialanalysis-PersonCount için bölge yapılandırması

 Bu, bir bölgeyi yapılandıran SPACEANALYTICS_CONFIG parametresine yönelik JSON girişi örneğidir. Bu işlem için birden çok bölge yapılandırabilirsiniz.

```json
{
"zones":[{
       "name": "lobbycamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
              "type": "count",
              "config":{
                     "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
       }]
}
```

| Ad | Tür| Açıklama|
|---------|---------|---------|
| `zones` | list| Bölgelerin listesi. |
| `name` | string| Bu bölge için kolay ad.|
| `polygon` | list| Her bir değer çifti, çokgenin köşeleri için x, y 'yi temsil eder. Çokgen, insanların izlenen veya sayılan olduğu alanı temsil eder ve çokgen noktaları, sol üst köşenin (0,0, 0,0) ve sağ alt köşenin (1,0, 1,0) olduğu normalleştirilmiş koordinatları (0-1) temel alır.   
| `threshold` | float| Kişi, bölge içindeki bu pikselden daha büyük olduğunda olaylar olur. |
| `type` | string| **Biliveservices. Vision. spatialanalysis-PersonCount** için bu olmalıdır `count` .|
| `trigger` | string| Olay gönderme tetikleyicisinin türü. Desteklenen değerler, sayımdan `event` bağımsız olarak değişiklik yapılıp yapılmayacağını fark edildiğinde, sayı değiştiğinde ya da `interval` olayların düzenli aralıklarla gönderilmesi için olay göndermeye yöneliktir.
| `output_frequency` | int | Olayların yumurdaki oranı. `output_frequency`= X olduğunda, her X olayı ise, EX olur. `output_frequency` = 2, diğer tüm olayların çıkış olduğu anlamına gelir. , `output_frequency` Ve için geçerlidir `event` `interval` . |
| `focus` | string| Olayları hesaplamak için kullanılan öğenin sınırlayıcı kutusundaki nokta konumu. Odağın değeri `footprint` (kişinin parmak izi), (kişinin sınırlayıcı `bottom_center` kutusunun alt Merkezi), `center` (kişinin sınırlayıcı kutusunun merkezi) olabilir.|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Biliveservices. Vision. spatialanalysis-personcrossingline için satır yapılandırması

Bu, bir satırı yapılandıran SPACEANALYTICS_CONFIG parametresi için bir JSON girişi örneğidir. Bu işlem için birden çok kesişen çizgi yapılandırabilirsiniz.

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| Ad | Tür| Açıklama|
|---------|---------|---------|
| `lines` | list| Satır listesi.|
| `name` | string| Bu satır için kolay ad.|
| `line` | list| Çizginin tanımı. Bu bir yön çizgisi, "giriş" ve "çıkış" ile anlamanızı sağlar.|
| `start` | değer çifti| çizginin başlangıç noktası için x, y koordinatları. Float değerleri, üst, sol köşeye göre köşe konumunu temsil eder. Mutlak x, y değerlerini hesaplamak için, bu değerleri çerçeve boyutuyla çarpmanız gerekir. |
| `end` | değer çifti| çizginin bitiş noktası için x, y koordinatları. Float değerleri, üst, sol köşeye göre köşe konumunu temsil eder. Mutlak x, y değerlerini hesaplamak için, bu değerleri çerçeve boyutuyla çarpmanız gerekir. |
| `threshold` | float| Kişi, bölge içindeki bu pikselden daha büyük olduğunda olaylar olur. Varsayılan değer 16'dır. Bu, en fazla doğruluğu sağlamak için önerilen değerdir. |
| `type` | string| **Biliveservices. Vision. spatialanalysis-personcrossingline** için bu olmalıdır `linecrossing` .|
|`trigger`|string|Olay gönderme tetikleyicisinin türü.<br>Desteklenen değerler: "olay": bir kişi çizgiyi kesiştiğinde yangın.|
| `focus` | string| Olayları hesaplamak için kullanılan öğenin sınırlayıcı kutusundaki nokta konumu. Odağın değeri `footprint` (kişinin parmak izi), (kişinin sınırlayıcı `bottom_center` kutusunun alt Merkezi), `center` (kişinin sınırlayıcı kutusunun merkezi) olabilir. Varsayılan değer, parmak izdir.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Biliveservices. Vision. spatialanalysis-personcrossingçokgen için bölge yapılandırması

Bu, bir bölgeyi yapılandıran SPACEANALYTICS_CONFIG parametresine yönelik JSON girişi örneğidir. Bu işlem için birden çok bölge yapılandırabilirsiniz.

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| Ad | Tür| Açıklama|
|---------|---------|---------|
| `zones` | list| Bölgelerin listesi. |
| `name` | string| Bu bölge için kolay ad.|
| `polygon` | list| Her bir değer çifti, poligonun köşeleri için x, y 'yi temsil eder. Çokgen, insanların izlenmekte veya dikkate alındığı alanı temsil eder. Float değerleri, üst, sol köşeye göre köşe konumunu temsil eder. Mutlak x, y değerlerini hesaplamak için, bu değerleri çerçeve boyutuyla çarpmanız gerekir. 
| `threshold` | float| Kişi, bölge içindeki bu pikselden daha büyük olduğunda olaylar olur. Tür zonecrossing olduğunda varsayılan değer 48, saat DwellTime olduğunda 16 ' dır. Bunlar, en fazla doğruluğu elde etmek için önerilen değerlerdir.  |
| `type` | string| **Biliveservices. Vision. spatialanalysis-personcrossingçokgen** için bu veya olmalıdır `zonecrossing` `zonedwelltime` .|
| `trigger`|string|Olay gönderme tetikleyicisinin türü<br>Desteklenen değerler: "olay": birisi bölgeden girdiğinde veya uygulamadan çıktığında yangın.|
| `focus` | string| Olayları hesaplamak için kullanılan öğenin sınırlayıcı kutusundaki nokta konumu. Odağın değeri `footprint` (kişinin parmak izi), (kişinin sınırlayıcı `bottom_center` kutusunun alt Merkezi), `center` (kişinin sınırlayıcı kutusunun merkezi) olabilir. Varsayılan değer, parmak izdir.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Biliveservices. Vision. spatialanalysis-persondistance için bölge yapılandırması

Bu, **biliveservices. Vision. spatialanalysis-persondistance** için bir bölgeyi yapılandıran SPACEANALYTICS_CONFIG parametresi IÇIN bir JSON girişi örneğidir. Bu işlem için birden çok bölge yapılandırabilirsiniz.

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
       "type": "persondistance",
       "config":{
           "trigger": "event",
           "output_frequency":1,
           "minimum_distance_threshold":6.0,
           "maximum_distance_threshold":35.0,
        "aggregation_method": "average"
           "threshold": 16.00,
           "focus": "footprint"
                   }
          }]
   }]
}
```

| Ad | Tür| Açıklama|
|---------|---------|---------|
| `zones` | list| Bölgelerin listesi. |
| `name` | string| Bu bölge için kolay ad.|
| `polygon` | list| Her bir değer çifti, poligonun köşeleri için x, y 'yi temsil eder. Çokgen, insanların sayıldığı ve kişiler arasındaki mesafe ölçülen alanı temsil eder. Float değerleri, üst, sol köşeye göre köşe konumunu temsil eder. Mutlak x, y değerlerini hesaplamak için, bu değerleri çerçeve boyutuyla çarpmanız gerekir. 
| `threshold` | float| Kişi, bölge içindeki bu pikselden daha büyük olduğunda olaylar olur. |
| `type` | string| **Biliveservices. Vision. spatialanalysis-persondistance** için olmalıdır `people_distance` .|
| `trigger` | string| Olay gönderme tetikleyicisinin türü. Desteklenen değerler, sayımdan `event` bağımsız olarak değişiklik yapılıp yapılmayacağını fark edildiğinde, sayı değiştiğinde ya da `interval` olayların düzenli aralıklarla gönderilmesi için olay göndermeye yöneliktir.
| `output_frequency` | int | Olayların yumurdaki oranı. `output_frequency`= X olduğunda, her X olayı ise, EX olur. `output_frequency` = 2, diğer tüm olayların çıkış olduğu anlamına gelir. , `output_frequency` Ve için geçerlidir `event` `interval` .|
| `minimum_distance_threshold` | float| İnsanlar bu uzaklığa göre daha az olduğunda "Toockaybetme" olayını tetikleyen bir uzaklık.|
| `maximum_distance_threshold` | float| İnsanlar bu uzaklıktan daha büyük olduğunda bir "TooFar" olayı tetikleyen bir uzaklık.|
| `aggregation_method` | string| Persondistance sonucunu topla yöntemi. Aggregation_method hem hem de için geçerlidir `mode` `average` .|
| `focus` | string| Olayları hesaplamak için kullanılan öğenin sınırlayıcı kutusundaki nokta konumu. Odağın değeri `footprint` (kişinin parmak izi), (kişinin sınırlayıcı `bottom_center` kutusunun alt Merkezi), `center` (kişinin sınırlayıcı kutusunun merkezi) olabilir.|

### <a name="configuration-for-cognitiveservicesvisionspatialanalysis"></a>Biliveservices. Vision. spatialanalysis için yapılandırma
Bu, **biliveservices. Vision. spatialanalysis** için bir satır ve bölge yapılandıran SPACEANALYTICS_CONFIG parametresi IÇIN bir JSON girişi örneğidir. Bu işlem için birden çok satır/bölge yapılandırabilirsiniz ve her bir satır/bölge farklı olaylara sahip olabilir.

 ```
{
  "lines": [
    {
      "name": "doorcamera",
      "line": {
        "start": {
          "x": 0,
          "y": 0.5
        },
        "end": {
          "x": 1,
          "y": 0.5
        }
      },
      "events": [
        {
          "type": "linecrossing",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ],
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3, 0.3],[0.3, 0.9],[0.6, 0.9],[0.6, 0.3],[0.3, 0.3]],
      "events": [
        {
          "type": "persondistance",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "minimum_distance_threshold": 6.0,
            "maximum_distance_threshold": 35.0,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonecrossing",
          "config": {
            "threshold": 48.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonedwelltime",
          "config": {
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```
## <a name="camera-configuration"></a>Kamera yapılandırması

Bölgeleri ve satırları yapılandırma hakkında daha fazla bilgi edinmek için [kamera yerleştirme](spatial-analysis-camera-placement.md) yönergelerine bakın.

## <a name="spatial-analysis-operation-output"></a>Uzamsal analiz Işlemi çıkışı

Her bir işlemin olayları, JSON biçiminde Azure IoT Hub için de kullanılır.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Biliveservices. Vision. spatialanalysis-PersonCount AI Insights için JSON biçimi

Bu işlem tarafından bir olay çıkışı için örnek JSON.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata":{
            "attributes": {
            "face_nomask": 0.99
            }
            }
       }
    ],
    "schemaVersion": "1.0"
}
```

| Olay alanı adı | Tür| Description|
|---------|---------|---------|
| `id` | dize| Olay Kimliği|
| `type` | string| Olay türü|
| `detectionsId` | array| Bu olayı tetikleyen kişinin benzersiz tanımlayıcısının 1 boyutlu dizisi|
| `properties` |  koleksiyonu| Değer koleksiyonu|
| `trackinId` | string| Algılanan kişinin benzersiz tanıtıcısı|
| `zone` | string | Çapraz olan bölgeyi temsil eden çokgenin "ad" alanı|
| `trigger` | string| Tetikleyici türü, SPACEANALYTICS_CONFIG değerine bağlı olarak ' Event ' veya ' Interval ' `trigger`|

| Algılama alanı adı | Tür| Description|
|---------|---------|---------|
| `id` | dize| Algılama KIMLIĞI|
| `type` | string| Algılama türü|
| `region` |  koleksiyonu| Değer koleksiyonu|
| `type` | string| Bölgenin türü|
| `points` |  koleksiyonu| Bölge türü DIKDÖRTGEN olduğunda sol üst ve sağ alt noktaları |
| `confidence` | float| Algoritma güveni|
| `face_mask` | float | Aralık (0-1) ile öznitelik güvenirlik değeri, algılanan kişinin bir yüz maskesini takdiğini belirtir |
| `face_nomask` | float | Aralık (0-1) ile öznitelik güvenirlik değeri, algılanan kişinin bir yüz maskesini **takmadığını** belirtir |

| SourceInfo alan adı | Tür| Description|
|---------|---------|---------|
| `id` | dize| Kamera KIMLIĞI|
| `timestamp` | date| JSON yükünün ne zaman yayıldığını UTC tarihi|
| `width` | int | Video çerçevesi genişliği|
| `height` | int | Video çerçeve yüksekliği|
| `frameId` | int | Çerçeve tanımlayıcısı|
| `cameraCallibrationInfo` |  koleksiyonu | Değer koleksiyonu|
| `status` | string | Biçiminin biçimindeki durumu `state[;progress description]` . Durum `Calibrating` , `Recalibrating` (Eğer yeniden renklendirme etkinse), veya olabilir `Calibrated` . İlerleme açıklaması bölümü yalnızca ve durumunda olduğunda geçerlidir. Bu, `Calibrating` `Recalibrating` geçerli ayarlama işleminin ilerlemesini göstermek için kullanılır.|
| `cameraHeight` | float | Kameranın, fit 'teki üstündeki yüksekliği. Bu, otomatik ayarlama tarafından algılanır. |
| `focalLength` | float | Kameranın piksel cinsinden odak uzunluğu. Bu, otomatik ayarlama tarafından algılanır. |
| `tiltUpAngle` | float | Kameranın eğim açısı Dikey. Bu, otomatik ayarlama tarafından algılanır.|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Biliveservices. Vision. spatialanalysis-personcrossingline AI öngörüleri için JSON biçimi

Bu işlem tarafından algılanan algılamalar için örnek JSON.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| Olay alanı adı | Tür| Description|
|---------|---------|---------|
| `id` | dize| Olay Kimliği|
| `type` | string| Olay türü|
| `detectionsId` | array| Bu olayı tetikleyen kişinin benzersiz tanımlayıcısının 1 boyutlu dizisi|
| `properties` |  koleksiyonu| Değer koleksiyonu|
| `trackinId` | string| Algılanan kişinin benzersiz tanıtıcısı|
| `status` | string| Çizgi çapraz yönlerinin yönü, ' CrossLeft ' veya ' CrossRight '. Yön, "Başlat" çizgisinin "bitiş" haline gelecek şekilde "başlangıç" ile oluşan görüntü oluşturma ' yı temel alır. Çapraz sağ, soldan sağa doğru kesişme. Çapraz sol, sağdan sola kesişme.|
| `zone` | string | Çapraz çizginin "ad" alanı|

| Algılama alanı adı | Tür| Description|
|---------|---------|---------|
| `id` | dize| Algılama KIMLIĞI|
| `type` | string| Algılama türü|
| `region` |  koleksiyonu| Değer koleksiyonu|
| `type` | string| Bölgenin türü|
| `points` |  koleksiyonu| Bölge türü DIKDÖRTGEN olduğunda sol üst ve sağ alt noktaları |
| `confidence` | float| Algoritma güveni|
| `face_mask` | float | Aralık (0-1) ile öznitelik güvenirlik değeri, algılanan kişinin bir yüz maskesini takdiğini belirtir |
| `face_nomask` | float | Aralık (0-1) ile öznitelik güvenirlik değeri, algılanan kişinin bir yüz maskesini **takmadığını** belirtir |

| SourceInfo alan adı | Tür| Description|
|---------|---------|---------|
| `id` | dize| Kamera KIMLIĞI|
| `timestamp` | date| JSON yükünün ne zaman yayıldığını UTC tarihi|
| `width` | int | Video çerçevesi genişliği|
| `height` | int | Video çerçeve yüksekliği|
| `frameId` | int | Çerçeve tanımlayıcısı|


> [!IMPORTANT]
> AI modeli, kişinin kameraya veya uzaklaşmaya bağlı olup olmadığına bakılmaksızın bir kişi algılar. AI modeli, yüz tanıma 'yı çalıştırmaz ve biyometrik bilgiler oluşturmaz. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Biliveservices. Vision. spatialanalysis-personcrossingçokgen AI öngörüleri için JSON biçimi

Bu işlem tarafından SPACEANALYTICS_CONFIG türünde algılanan algılamalar için örnek JSON `zonecrossing` .

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
        "metadata": {
        "attributes": {
        "face_mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

Bu işlem tarafından SPACEANALYTICS_CONFIG türünde algılanan algılamalar için örnek JSON `zonedwelltime` .

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
              "durationMs": 7132.0
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Olay alanı adı | Tür| Description|
|---------|---------|---------|
| `id` | dize| Olay Kimliği|
| `type` | string| Olay türü. Değer, _Personzonedwelltimeevent_ ya da _Personzoneenterexitevent_ olabilir|
| `detectionsId` | array| Bu olayı tetikleyen kişinin benzersiz tanımlayıcısının 1 boyutlu dizisi|
| `properties` |  koleksiyonu| Değer koleksiyonu|
| `trackinId` | string| Algılanan kişinin benzersiz tanıtıcısı|
| `status` | string| Çokgen çapraz yönlerinin yönü, ' Enter ' veya ' Exit '|
| `side` | int| Kişinin çapraz olduğu Çokgen tarafının numarası. Her kenar, kendi diliminizi temsil eden iki köşe arasında numaralandırılmış bir kenar. Poligonun ilk iki köşesi arasındaki kenar ilk tarafı temsil eder. ' SIDE ', bir olay belirli bir kenar ile ilişkili olmadığında bir hata nedeniyle boş. Örneğin, bir kişi kaybolduğunda, ancak bölgenin bir tarafında geçen bir zaman ya da bir kişi bölgede göründüğünde ancak bir tarafında geçen bir bir giriş görüldüğünde bir çıkış meydana geldi.|
| `durationMs` | float | Kişinin bölgede harcadığı süreyi temsil eden milisaniye sayısı. Bu alan, olay türü _Personzonedwelltimeevent_ olduğunda sağlanır|
| `zone` | string | Çapraz olan bölgeyi temsil eden çokgenin "ad" alanı|

| Algılama alanı adı | Tür| Description|
|---------|---------|---------|
| `id` | dize| Algılama KIMLIĞI|
| `type` | string| Algılama türü|
| `region` |  koleksiyonu| Değer koleksiyonu|
| `type` | string| Bölgenin türü|
| `points` |  koleksiyonu| Bölge türü DIKDÖRTGEN olduğunda sol üst ve sağ alt noktaları |
| `confidence` | float| Algoritma güveni|
| `face_mask` | float | Aralık (0-1) ile öznitelik güvenirlik değeri, algılanan kişinin bir yüz maskesini takdiğini belirtir |
| `face_nomask` | float | Aralık (0-1) ile öznitelik güvenirlik değeri, algılanan kişinin bir yüz maskesini **takmadığını** belirtir |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Biliveservices. Vision. spatialanalysis-persondistance AI öngörüleri için JSON biçimi

Bu işlem tarafından algılanan algılamalar için örnek JSON.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Olay alanı adı | Tür| Description|
|---------|---------|---------|
| `id` | dize| Olay Kimliği|
| `type` | string| Olay türü|
| `detectionsId` | array| Bu olayı tetikleyen kişinin benzersiz tanımlayıcısının 1 boyutlu dizisi|
| `properties` |  koleksiyonu| Değer koleksiyonu|
| `personCount` | int| Olay yayınlandığında algılanan kişilerin sayısı|
| `averageDistance` | float| Tüm algılanan kişilerin feet içinde ortalama uzaklığı|
| `minimumDistanceThreshold` | float| İnsanlar bu uzaklıktan daha az olduğunda bir "Toockaybetme" olayı tetiklenecek mesafe uzaklığı.|
| `maximumDistanceThreshold` | float| İnsanlar birbirinden uzaklıktan daha büyük olduğunda bir "TooFar" olayı tetiklenecek mesafe uzaklığı.|
| `eventName` | string| Olay adı `TooClose` `minimumDistanceThreshold` ihlal edildi, `TooFar` ne zaman ihlal edildiğinde `maximumDistanceThreshold` veya `unknown` otomatik ayarlama tamamlanmamışsa|
| `distanceViolationPersonCount` | int| Veya ihlaline göre algılanan kişilerin sayısı `minimumDistanceThreshold``maximumDistanceThreshold`|
| `zone` | string | Kişiler arasında distancing için izlenen bölgeyi temsil eden çokgenin "ad" alanı|
| `trigger` | string| Tetikleyici türü, SPACEANALYTICS_CONFIG değerine bağlı olarak ' Event ' veya ' Interval ' `trigger`|

| Algılama alanı adı | Tür| Description|
|---------|---------|---------|
| `id` | dize| Algılama KIMLIĞI|
| `type` | string| Algılama türü|
| `region` |  koleksiyonu| Değer koleksiyonu|
| `type` | string| Bölgenin türü|
| `points` |  koleksiyonu| Bölge türü DIKDÖRTGEN olduğunda sol üst ve sağ alt noktaları |
| `confidence` | float| Algoritma güveni|
| `centerGroundPoint` | 2 float değeri| `x`, `y` kişinin yüz içinde çıkarılan konumunun koordinatlarıyla birlikte değerleri. `x` ve taban `y` düzledeki koordinatların ortasının düzeyi olduğunu varsayar. Kameranın konumu kaynak. |

Hesaplama sırasında `centerGroundPoint` , kameradan `x` kameraya, kamera görüntü düzlemine dikey bir çizgi arasındaki mesafe uzaklığı. `y` , kameradan kameraya, kamera görüntü düzlemine paralel bir çizgi ile uzaklığı. 

![Örnek orta zemin noktası](./media/spatial-analysis/x-y-chart.png) 

Bu örnekte, `centerGroundPoint` `{x: 4, y: 5}` . Bu, kameradan 4 metre uzakta olan ve oda üst alta bakarak 5 metreye kadar bir kişi olduğu anlamına gelir.


| SourceInfo alan adı | Tür| Description|
|---------|---------|---------|
| `id` | dize| Kamera KIMLIĞI|
| `timestamp` | date| JSON yükünün ne zaman yayıldığını UTC tarihi|
| `width` | int | Video çerçevesi genişliği|
| `height` | int | Video çerçeve yüksekliği|
| `frameId` | int | Çerçeve tanımlayıcısı|
| `cameraCallibrationInfo` |  koleksiyonu | Değer koleksiyonu|
| `status` | string | Biçiminin biçimindeki durumu `state[;progress description]` . Durum `Calibrating` , `Recalibrating` (Eğer yeniden renklendirme etkinse), veya olabilir `Calibrated` . İlerleme açıklaması bölümü yalnızca ve durumunda olduğunda geçerlidir. Bu, `Calibrating` `Recalibrating` geçerli ayarlama işleminin ilerlemesini göstermek için kullanılır.|
| `cameraHeight` | float | Kameranın, fit 'teki üstündeki yüksekliği. Bu, otomatik ayarlama tarafından algılanır. |
| `focalLength` | float | Kameranın piksel cinsinden odak uzunluğu. Bu, otomatik ayarlama tarafından algılanır. |
| `tiltUpAngle` | float | Kameranın eğim açısı Dikey. Bu, otomatik ayarlama tarafından algılanır.|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-ai-insights"></a>Biliveservices. Vision. spatialanalysis AI öngörüleri için JSON biçimi

Bu işlemin çıktısı yapılandırıldığına bağlıdır `events` , örneğin, `zonecrossing` Bu işlem için yapılandırılmış bir olay varsa, çıkış ile aynı olacaktır `cognitiveservices.vision.spatialanalysis-personcrossingpolygon` .

## <a name="use-the-output-generated-by-the-container"></a>Kapsayıcı tarafından oluşturulan çıktıyı kullanın

Uzamsal analiz algılama veya olaylarını uygulamanızla tümleştirmek isteyebilirsiniz. Göz önünde bulundurmanız gereken birkaç yaklaşım aşağıda verilmiştir: 

* Azure IoT Hub uç noktasına bağlanmak ve olayları almak için seçtiğiniz programlama diline yönelik Azure Event hub SDK 'sını kullanın. Daha fazla bilgi için bkz. [yerleşik uç noktadan cihazdan buluta Iletileri okuma](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) . 
* Olayları diğer uç noktalara göndermek veya olayları veri depolamaya kaydetmek için Azure IoT Hub **Ileti yönlendirmeyi** ayarlayın. Daha fazla bilgi için bkz. [IoT Hub Ileti yönlendirme](../../iot-hub/iot-hub-devguide-messages-d2c.md) . 
* Olayları geldikçe ve görselleştirmeler oluştururken gerçek zamanlı olarak işlemek için bir Azure Stream Analytics işi ayarlayın. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Uzamsal analiz işlemlerini ölçekte dağıtma (birden çok kamera)

GPU 'ların en iyi performansını ve kullanımını sağlamak için, grafik örnekleri kullanarak birden fazla kamera üzerinde herhangi bir uzamsal analiz işlemini dağıtabilirsiniz. İşlemin on beş kamera üzerinde çalıştırılması için bir örnek aşağıda verilmiştir `cognitiveservices.vision.spatialanalysis-personcrossingline` .

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
                }
            }
        },
        "parameters": {
            "VIDEO_DECODE_GPU_INDEX": 0,
            "VIDEO_IS_LIVE": true
        },
        "instances": {
            "1": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| Ad | Tür| Açıklama|
|---------|---------|---------|
| `batch_size` | int | Tüm kameralar aynı çözünürlüğe sahip ise, `batch_size` Bu işlemde kullanılacak olan kamera sayısına ayarlayın, aksi takdirde 1 olarak ayarlayın `batch_size` veya varsayılan olarak (1) olarak bırakın. Bu, hiçbir toplu işin desteklenmediğini gösterir. |

## <a name="next-steps"></a>Sonraki adımlar

* [Web uygulaması sayımını yapan bir kişi dağıtın](spatial-analysis-web-app.md)
* [Günlüğe kaydetme ve sorun giderme](spatial-analysis-logging.md)
* [Kamera Yerleştirme Kılavuzu](spatial-analysis-camera-placement.md)
* [Bölge ve satır yerleştirme Kılavuzu](spatial-analysis-zone-line-placement.md)
