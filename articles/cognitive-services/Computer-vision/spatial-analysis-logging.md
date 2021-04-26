---
title: Uzamsal analiz kapsayıcıları için telemetri ve günlüğe kaydetme
titleSuffix: Azure Cognitive Services
description: Uzamsal analiz, her kapsayıcıyı ortak bir Configuration Framework Insights, günlüğe kaydetme ve güvenlik ayarları sağlar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 901e857a346b0955726c5755e23595efefbc2ca1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589508"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetri ve sorun giderme

Uzamsal analizler sistemin sistem durumunu izlemeye yönelik bir dizi özellik içerir ve sorunları tanılamanıza yardımcı olur.

## <a name="enable-visualizations"></a>Görselleştirmeleri etkinleştir

Bir video çerçevesindeki bir AI Insights etkinlikleri görselleştirmesini etkinleştirmek için, `.debug` bir masaüstü makinesinde bir [uzamsal analiz işleminin](spatial-analysis-operations.md) sürümünü kullanmanız gerekir. Görselleştirme Azure Stack Edge cihazlarında mümkün değildir. Dört hata ayıklama işlemi kullanılabilir.

Cihazınız Azure Stack Edge aygıtı değilse, [Masaüstü makineleri](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) için dağıtım bildirim dosyasını ortam değişkeni için doğru değeri kullanacak şekilde düzenleyin `DISPLAY` . `$DISPLAY`Ana bilgisayardaki değişkenle eşleşmesi gerekir. Dağıtım bildirimini güncelleştirdikten sonra kapsayıcıyı yeniden dağıtın.

Dağıtım tamamlandıktan sonra, `.Xauthority` dosyayı ana bilgisayardan kapsayıcıya kopyalamanız ve yeniden başlatmanız gerekebilir. Aşağıdaki örnekte, `peopleanalytics` ana bilgisayardaki kapsayıcının adıdır.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Sistem durumu telemetrisini topla

Telegraf, uzamsal analiz ile birlikte çalışarak açık kaynaklı bir görüntüdür ve Microsoft Container Registry kullanılabilir. Aşağıdaki girişleri alır ve Azure Izleyici 'ye gönderir. Telegraf modülü, istenen özel girişler ve çıktılar ile oluşturulabilir. Uzamsal analizde telegraf Module yapılandırması, dağıtım bildiriminin bir parçasıdır (yukarıda bağlantılı). Bu modül isteğe bağlıdır ve ihtiyacınız yoksa bildirimden kaldırılabilir. 

Girişi 
1. Uzamsal analiz ölçümleri
2. Disk Ölçümleri
3. CPU ölçümleri
4. Docker ölçümleri
5. GPU ölçümleri

Çıkışı
1. Azure İzleyici

Sağlanan uzamsal analiz telegraf modülü, uzamsal analiz kapsayıcısı tarafından sunulan tüm telemetri verilerini Azure Izleyici 'ye yayımlar. Aboneliğinize Azure Izleyici ekleme hakkında bilgi için bkz. [Azure izleyici](../../azure-monitor/overview.md) .

Azure Izleyici 'yi ayarladıktan sonra, modülün telemetri göndermesini sağlayan kimlik bilgileri oluşturmanız gerekir. Yeni bir hizmet sorumlusu oluşturmak için Azure portal kullanabilir veya bir tane oluşturmak için aşağıdaki Azure CLı komutunu kullanabilirsiniz.

> [!NOTE] 
> Bu komut, abonelikte sahip ayrıcalıklarına sahip olmanızı gerektirir. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

[Azure Stack Edge cihazınız](https://go.microsoft.com/fwlink/?linkid=2142179), [Masaüstü makineniz](https://go.microsoft.com/fwlink/?linkid=2152270)veya [GPU ile Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189)'nizin dağıtım bildiriminde, *telegraf* modülünü arayın ve aşağıdaki değerleri önceki adımda bulunan hizmet sorumlusu bilgileriyle değiştirin ve yeniden dağıtın.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

Telegraf modülü dağıtıldıktan sonra, bildirilen ölçümlere Azure Izleyici hizmeti aracılığıyla ya da Azure portal IoT Hub **izleme** seçilerek erişilebilir.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Izleyici telemetri raporu":::

### <a name="system-health-events"></a>Sistem durumu olayları

| Olay Adı                  | Description    |
|-----------------------------|-------------------------------------------------------------------------------------------|
| archon_exit                 | Kullanıcı, uzamsal analiz modülü durumunun *çalışmayı* *durduruldu* olarak değiştirdiğinde gönderilir.  |
| archon_error                | Kapsayıcının içindeki işlemlerden herhangi biri kilitleniyorsa gönderilir. Bu kritik bir hatadır.      |
| Inputrate                   | Grafiğin video girişini işleme hızıdır. Her 5 dakikada bir raporlanır.              |
| OutputRate                  | Grafiğin AI öngörülerini çıkış hızı. Her 5 dakikada bir raporlanır.                |
| archon_allGraphsStarted     | Tüm grafiklerin başlaması bittiğinde gönderilir.                                           |
| archon_configchange         | Grafik yapılandırması değiştirildiğinde gönderilir.                                              |
| archon_graphCreationFailed  | Raporlandığı grafik başlatıldığında gönderilir `graphId` .                           |
| archon_graphCreationSuccess | Raporlanan grafik `graphId` başarıyla başladığında gönderilir.                      |
| archon_graphCleanup         | Bildirilen grafik `graphId` Temizleme ve çıkış olduğunda gönderilir.                      |
| archon_graphHeartbeat       | Bir yeteneğin her bir grafiği için her dakikada bir sinyal gönderilir.                                   |
| archon_apiKeyAuthFail       | Görüntü İşleme kaynak anahtarı, aşağıdaki nedenlerden dolayı, kapsayıcıyı 24 saatten uzun bir süre için doğrulayamazsa gönderilir: kota dışı, geçersiz, çevrimdışı. |
| Videoıngestersinyal      | Videonun video kaynağından akışını ve bu saatin hata sayısını gösteren her saat için gönderilir. Her bir grafik için bildirildi. |
| Videoıngesterstate          | Video akışı için raporlar *durduruldu* veya *başlatıldı* . Her bir grafik için bildirildi.              |

##  <a name="troubleshooting-an-iot-edge-device"></a>IoT Edge cihaz sorunlarını giderme

`iotedge`Çalışan modüllerin durumunu ve günlüklerini denetlemek için komut satırı aracını kullanabilirsiniz. Örnek:
* `iotedge list`: Çalışan modüllerin listesini raporlar. 
  İle ilgili hataları daha fazla kontrol edebilirsiniz `iotedge logs edgeAgent` . `iotedge`Takıldığında, ile yeniden başlatmayı deneyebilirsiniz`iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` belirli bir modülü yeniden başlatmak için 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Tanılama kapsayıcısı ile günlük dosyalarını toplama

Uzamsal analiz, çalışma zamanı sorunlarını tanılamak için kullanabileceğiniz veya destek biletlerinin dahil olduğu Docker hata ayıklama günlüklerini oluşturur. Uzamsal analiz tanılama modülü, Microsoft Container Registry indirileceği için kullanılabilir. [Azure Stack Edge cihazınız](https://go.microsoft.com/fwlink/?linkid=2142179), [Masaüstü makineniz](https://go.microsoft.com/fwlink/?linkid=2152270)veya [GPU ile Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) için bildirim dağıtım dosyasında, *Tanılama* modülünü bulun.

"Env" bölümünde aşağıdaki yapılandırmayı ekleyin:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Azure Blob depolama gibi uzak bir uç noktaya yüklenen günlükleri iyileştirmek için küçük bir dosya boyutu sürdürülmesi önerilir. Önerilen Docker günlükleri yapılandırması için aşağıdaki örneğe bakın.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>Günlük düzeyini yapılandırma

Günlük düzeyi yapılandırması, oluşturulan günlüklerin ayrıntı düzeyini denetlemenize olanak tanır. Desteklenen günlük düzeyleri şunlardır: `none` , `verbose` , `info` , `warning` ve `error` . Her iki düğüm ve platform için varsayılan günlük ayrıntı düzeyi `info` . 

`ARCHON_LOG_LEVEL`Ortam değişkeni, izin verilen değerlerden birine ayarlanarak, günlük düzeyleri genel olarak değiştirilebilir.
Ayrıca, tüm dağıtılan yetenekler için IoT Edge modülü Ikizi belgesi aracılığıyla ya da değerleri `platformLogLevel` aşağıda gösterildiği gibi ayarlayarak her belirli yetenek için de ayarlanabilir `nodesLogLevel` .

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodesLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Günlükleri toplama

> [!NOTE]
> `diagnostics`Modül, günlüğe kaydetme içeriğini etkilemez, yalnızca mevcut günlükleri toplama, filtreleme ve karşıya yükleme konusunda yardımcı olur.
> Bu modülü kullanmak için Docker API sürüm 1,40 veya sonraki bir sürüme sahip olmanız gerekir.

[Azure Stack Edge cihazınız](https://go.microsoft.com/fwlink/?linkid=2142179), [Masaüstü makineniz](https://go.microsoft.com/fwlink/?linkid=2152270)veya [GPU ile Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) 'niz için örnek dağıtım bildirim dosyası, `diagnostics` günlükleri toplayıp karşıya yükleyen adlı bir modül içerir. Bu modül varsayılan olarak devre dışıdır ve günlüklere erişmeniz gerektiğinde IoT Edge modülü yapılandırması aracılığıyla etkinleştirilmelidir. 

`diagnostics`Koleksiyon isteğe bağlıdır ve bir IoT Edge doğrudan yöntemi aracılığıyla denetlenir ve Azure Blob depolama alanına Günlükler gönderebilir.

### <a name="configure-diagnostics-upload-targets"></a>Tanılama karşıya yükleme hedeflerini yapılandırma

IoT Edge portalından cihazınızı ve ardından **Tanılama** modülünü seçin. [Azure Stack Edge cihazınız](https://go.microsoft.com/fwlink/?linkid=2142179), [Masaüstü makineleri](https://go.microsoft.com/fwlink/?linkid=2152270)veya [GPU ile Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) 'niz için örnek dağıtım bildirim dosyasında, adlı Tanılamalar için **ortam değişkenleri** bölümüne bakın `env` ve aşağıdaki bilgileri ekleyin:

**Azure Blob depolamaya yükleme yapılandırma**

1. Henüz yapmadıysanız, kendi Azure Blob depolama hesabınızı oluşturun.
2. Azure portal depolama hesabınızın **bağlantı dizesini** alın. **Erişim anahtarlarına** yer alır.
3. Uzamsal analiz günlükleri şu dosya adı biçimiyle *rtcvlogs* adlı bir BLOB depolama kapsayıcısına otomatik olarak yüklenir: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Uzamsal analiz günlüklerini karşıya yükleme

Günlükler, modüldeki IoT Edge yöntemi ile isteğe bağlı olarak karşıya yüklenir `getRTCVLogs` `diagnostics` . 


1. IoT Hub Portal sayfanıza gidin, **Edge cihazları**' nı seçin, sonra cihazınızı ve tanılama modülünüzü seçin. 
2. Modülün ayrıntılar sayfasına gidin ve ***doğrudan yöntem*** sekmesine tıklayın.
3. `getRTCVLogs`Yöntem adı üzerine tür ve yük içindeki bir JSON biçim dizesi. `{}`Boş bir yük olan yazabilirsiniz. 
4. Bağlantı ve yöntem zaman aşımlarını ayarlayın ve **yöntemi çağır**' a tıklayın.
5. Hedef kapsayıcınızı seçin ve **günlük sözdizimi** bölümünde açıklanan parametreleri kullanarak bir yük JSON dizesi oluşturun. İsteği gerçekleştirmek için **Invoke yöntemi** ' ne tıklayın.

>[!NOTE]
> `getRTCVLogs`Yöntemi boş bir yük ile çağırmak, cihaza dağıtılan tüm kapsayıcıların bir listesini döndürür. Yöntem adı büyük/küçük harfe duyarlıdır. Yanlış bir yöntem adı verildiyse 501 hatası alırsınız.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="GetRTCVLogs yöntemini çağırma ":::
![getRTCVLogs doğrudan yöntem sayfası](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Günlük sözdizimi

Aşağıdaki tabloda, günlükleri sorgularken kullanabileceğiniz parametreler listelenmektedir.

| Sözcükle | Description | Varsayılan değer |
|--|--|--|
| StartTime | İstenen günlük başlangıç zamanı, milisaniye cinsinden UTC. | `-1`, kapsayıcının çalışma zamanının başlangıcı. `[-1.-1]`Zaman aralığı olarak kullanıldığında, API son bir saatten günlükleri döndürür.|
| EndTime | İstenen günlük bitiş saati (milisaniye olarak UTC). | `-1`, geçerli saat. `[-1.-1]`Zaman aralığı kullanıldığında, API son bir saatten günlükleri döndürür. |
| Kimliği | Günlükleri getirmeye yönelik hedef kapsayıcı.| `null`bir kapsayıcı KIMLIĞI olmadığında. API, tüm kullanılabilir kapsayıcılar bilgilerini kimlikler ile döndürür.|
| DoPost | Karşıya yükleme işlemini gerçekleştirin. Bu olarak ayarlandığında `false` , istenen işlemi gerçekleştirir ve karşıya yükleme yapmadan karşıya yükleme boyutunu döndürür. Olarak ayarlandığında `true` , seçilen günlüklerin zaman uyumsuz karşıya yüklemesi başlatılır | `false`, karşıya yüklemeyin.|
| Kısıtlama | Toplu iş başına karşıya yüklenecek günlük satır sayısını belirtin | `1000`, Post hızını ayarlamak için bu parametreyi kullanın. |
| Filtreler | Karşıya yüklenecek filtre günlükleri | `null`, bir filtre, uzamsal analiz günlüğü yapısına göre anahtar değer çiftleri olarak belirtilebilir: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Örnek: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

Aşağıdaki tabloda sorgu yanıtında öznitelikler listelenmiştir.

| Sözcükle | Description|
|--|--|
|DoPost| *True* ya da *false*. Günlüklerin karşıya yüklenip yüklenmediğini belirtir. Günlükleri karşıya yüklememe seçeneğini belirlediğinizde, API bilgileri "**zaman uyumlu** _" döndürür. Günlükleri karşıya yüklemeyi seçtiğinizde, API, istek geçerliyse 200 döndürür ve günlükleri karşıya yüklemeye başlar _ *_zaman uyumsuz_* *.|
|TimeFilter| Günlüklere uygulanan zaman filtresi.|
|ValueFilters| Günlüklere uygulanan anahtar sözcük filtreleri. |
|Ilişkin| Yöntem yürütme başlangıç zamanı. |
|Kimliği| Hedef kapsayıcı KIMLIĞI. |
|FetchCounter| Toplam günlük satırı sayısı. |
|Fetchsizeınbyte| Toplam günlük verisi miktarı (bayt). |
|MatchCounter| Geçerli günlük satırı sayısı. |
|Matchsizeınbyte| Bayt cinsinden geçerli günlük verisi miktarı. |
|FilterCount| Filtre uygulandıktan sonra toplam günlük satırı sayısı. |
|Filtersizeınbyte| Filtre uygulandıktan sonra bayt cinsinden toplam günlük verisi miktarı. |
|FetchLogsDurationInMiliSec| İşlem süresini getir. |
|PaseLogsDurationInMiliSec| Filtre işlemi süresi. |
|PostLogsDurationInMiliSec| Son işlem süresi. |

#### <a name="example-request"></a>Örnek istek 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>Örnek yanıt 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Bu ayarların daha iyi görünmesi ve günlükleri aynı filtreye sahip günlüklere itmesi için, bu ayarların ' ı ' ye doğru görünmesi durumunda getirme günlüğü satırları, zamanları ve boyutları ' nı işaretleyin  `true` . 

Sorunları giderirken Azure Blob depolamadan günlükleri dışarı aktarabilirsiniz. 

## <a name="common-issues"></a>Genel sorunlar

Modül günlüklerinde aşağıdaki iletiyi görürseniz, Azure aboneliğinizin onaylanması gerektiği anlamına gelebilir: 

"Kapsayıcı geçerli bir durumda değil. Abonelik doğrulaması ' uyuşmazlık ' durumuyla başarısız oldu. API anahtarı, verilen kapsayıcı türü için tasarlanmamıştır. "

Daha fazla bilgi için bkz. [kapsayıcıyı çalıştırmak için onay iste](spatial-analysis-container.md#request-approval-to-run-the-container).

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Azure Stack Edge cihazında sorun giderme

Azure Stack Edge cihazınızın durumunu hata ayıklama ve doğrulamaya yönelik yardım için aşağıdaki bölüm sunulmaktadır.

### <a name="access-the-kubernetes-api-endpoint"></a>Kubernetes API uç noktasına erişin. 

1. Cihazınızın yerel kullanıcı arabiriminde, **cihazlar** sayfasına gidin. 
2. **Cihaz uç noktaları** altında Kubernetes API hizmet uç noktasını kopyalayın. Bu uç nokta aşağıdaki biçimde bir dizedir: `https://compute..[device-IP-address]` .
3. Uç nokta dizesini kaydedin. Bunu daha sonra `kubectl` Kubernetes kümesine erişmek üzere yapılandırırken kullanacaksınız.

### <a name="connect-to-powershell-interface"></a>PowerShell arabirimine Bağlan

Uzaktan, bir Windows istemcisinden bağlanın. Kubernetes kümesi oluşturulduktan sonra, bu küme aracılığıyla uygulamaları yönetebilirsiniz. Cihazın PowerShell arabirimine bağlanmanız gerekir. İstemci işletim sistemine bağlı olarak, cihaza uzaktan bağlanma yordamları farklı olabilir. Aşağıdaki adımlar PowerShell çalıştıran bir Windows istemcisi içindir.

> [!TIP]
> * Başlamadan önce, Windows istemcinizin Windows PowerShell 5,0 veya sonraki bir sürümünü çalıştırdığından emin olun.
> * PowerShell, [Linux üzerinde de kullanılabilir](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Yönetici olarak bir Windows PowerShell oturumu çalıştırın. 
    1. Windows Uzaktan Yönetimi hizmetinin istemciniz üzerinde çalıştığından emin olun. Komut istemine yazın `winrm quickconfig` .

2. Cihaz IP adresi için bir değişken atayın. Örneğin, `$ip = "<device-ip-address>"`.

3. Cihazınızın IP adresini istemcinin güvenilen konaklar listesine eklemek için aşağıdaki komutu kullanın. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Cihazda bir Windows PowerShell oturumu başlatın. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. İstendiğinde parolayı girin. Yerel Web arabiriminde oturum açmak için kullanılan parolayı kullanın. Varsayılan yerel Web arabirimi parolası `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Kubernetes kümesine erişme

Kubernetes kümesi oluşturulduktan sonra, `kubectl` kümeye erişmek için komut satırı aracını kullanabilirsiniz.

1. Yeni bir ad alanı oluşturun. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Bir kullanıcı oluşturun ve bir yapılandırma dosyası alın. Bu komut, Kubernetes kümesi için yapılandırma bilgilerini çıktısını alacak. Bu bilgileri kopyalayın ve *config* adlı bir dosyaya kaydedin. Dosyayı bir dosya uzantısı olarak kaydetmeyin.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. *Yapılandırma* dosyasını yerel makinedeki kullanıcı profilinize *. kuin* klasörüne ekleyin.    

4. Ad alanını oluşturduğunuz kullanıcıyla ilişkilendirin.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. `kubectl`Aşağıdaki komutu kullanarak Windows istemciniz üzerine yüklenir:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Sisteminizdeki Hosts dosyasına bir DNS girişi ekleyin. 
    1. Not defteri 'Ni yönetici olarak çalıştırın ve konumunda bulunan *Hosts* dosyasını açın `C:\windows\system32\drivers\etc\hosts` . 
    2. Hosts dosyasında, yerel kullanıcı arabirimindeki **cihaz** sayfasından ALDıĞıNıZ cihaz IP adresı ve DNS etki alanı ile bir giriş oluşturun. Kullanmanız gereken uç nokta şuna benzer: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Kubernetes pods 'ye bağlanabildiğinizi doğrulayın.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Kapsayıcı günlüklerini almak için aşağıdaki komutu çalıştırın:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Faydalı komutlar

|Komut  |Açıklama  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Bir Kubernetes yapılandırma dosyası oluşturur. Komutunu kullanırken, bilgileri *config* adlı bir dosyaya kopyalayın. Dosyayı dosya uzantısıyla kaydetme.        |
| `Get-HcsApplianceInfo` | Cihazınız hakkında bilgi döndürür. |
| `Enable-HcsSupportAccess` | Destek oturumu başlatmak için erişim kimlik bilgileri oluşturur. |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>Uzamsal analiz için destek bileti dosyası oluşturma 

Uzamsal analiz kapsayıcısına sahip olduğunuz bir soruna çözüm bulma konusunda daha fazla desteğe ihtiyacınız varsa, bir destek bileti almak ve göndermek için aşağıdaki adımları izleyin. Ekibimiz size ek rehberlik vererek sizi geri alacak. 

### <a name="fill-out-the-basics"></a>Temel bilgileri doldurun 
[Yeni destek isteği](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sayfasında yeni bir destek bileti oluşturun. Aşağıdaki parametreleri doldurmanız için istemleri izleyin:

![Destek temelleri](./media/support-ticket-page-1-final.png)

1. **Sorun türünü** olarak ayarlayın `Technical` .
2. Uzamsal analiz kapsayıcısını dağıtmak için kullandığınız aboneliği seçin.
3. `My services`Hizmet olarak seçin ve seçin `Cognitive Services` .
4. Uzamsal analiz kapsayıcısını dağıtmak için kullandığınız kaynağı seçin.
5. Karşılaştığınız sorunun ayrıntılarını açıklayan kısa bir açıklama yazın. 
6. `Spatial Analysis`Sorun türü olarak öğesini seçin.
7. Açılan listeden uygun alt türü seçin.
8. Ileri ' yi seçin: sonraki sayfada geçiş yapılacak **çözümler** .

### <a name="recommended-solutions"></a>Önerilen çözümler
Sonraki aşamada, seçtiğiniz sorun türü için önerilen çözümler sunulacaktır. Bu çözümler en sık karşılaşılan sorunları çözebilir, ancak çözümünüz için yararlı değilse, Ileri ' yi seçin. **Ayrıntılar** için sonraki adıma gidin.

### <a name="details"></a>Ayrıntılar
Bu sayfada, karşılaştığınız sorun hakkında bazı ek ayrıntılar ekleyin. Mühendisimizin sorunu daha iyi daraltabilmesini sağlamak için mümkün olduğunca fazla ayrıntı eklediğinizden emin olun. Size uygun iletişim kurabilmemiz için tercih ettiğiniz iletişim yöntemini ve sorunun önem derecesini ekleyin ve **İleri** ' yi seçin. sonraki adıma geçmek için bir sonraki adıma geçin. 

### <a name="review-and-create"></a>Gözden geçir ve oluştur 
Her şeyin doğru olduğundan emin olmak için destek isteğinizin ayrıntılarını gözden geçirin ve sorunu etkili bir şekilde temsil edin. Hazırsanız, bileti ekibimize göndermek için **Oluştur** ' u seçin! Bilet alındıktan sonra ekibimiz bir e-posta onayı alacaksınız ve takımımız en kısa sürede size geri dönmek için çalışacaktır. Azure portal biletinin durumunu görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Web uygulaması sayımını yapan bir kişi dağıtın](spatial-analysis-web-app.md)
* [Uzamsal analiz işlemlerini yapılandırma](./spatial-analysis-operations.md)
* [Kamera Yerleştirme Kılavuzu](spatial-analysis-camera-placement.md)
* [Bölge ve satır yerleştirme Kılavuzu](spatial-analysis-zone-line-placement.md)
