---
title: Cihaz benzetimi ile protokol arabellekleri kullanma-Azure | Microsoft Docs
description: Bu nasıl yapılır kılavuzunda, cihaz benzetimi çözüm hızlandırıcısında gönderilen Telemetriyi seri hale getirmek için protokol arabelleklerini nasıl kullanacağınızı öğreneceksiniz.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 2d2c33d0b6f86bc1a779361b86d242cde4c5df38
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873676"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Protokol arabellekleri kullanarak telemetri serileştirme

Protokol arabellekleri (Protobellek), yapılandırılmış veriler için ikili bir serileştirme biçimidir. Prototip, XML 'den daha küçük ve daha hızlı bir amaç ile basitliği ve performansı vurgulamak için tasarlanmıştır.

Cihaz benzetimi, protokol arabellekleri dilinin **proto3** sürümünü destekler.

Protoarabellek verileri seri hale getirmek için derlenmiş kod gerektirdiğinden, cihaz benzetiminin özel bir sürümünü derlemeniz gerekir.

Bu nasıl yapılır kılavuzundaki adımlarda şu adımları nasıl kullanabileceğiniz gösterilmektedir:

1. Geliştirme ortamı hazırlama
1. Bir cihaz modelinde prototip biçiminin kullanımını belirtme
1. Prototip biçiminizi tanımlayın
1. Prototip sınıfları oluşturma
1. Yerel olarak test etme

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzundaki adımları izlemek için şunlar gerekir:

* Visual Studio Code. [Mac, Linux ve Windows için Visual Studio Code](https://code.visualstudio.com/download)indirebilirsiniz.
* .NET Core. [Mac, Linux ve Windows için .NET Core 'u](https://dotnet.microsoft.com/download)indirebilirsiniz.
* Postman. [Mac, Windows veya Linux Için Postman](https://www.getpostman.com/apps)indirebilirsiniz.
* [Azure aboneliğinize dağıtılan bir IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md). Bu kılavuzdaki adımları tamamlayabilmeniz için IoT Hub 'ın bağlantı dizesine ihtiyacınız vardır. Bağlantı dizesini Azure portal alabilirsiniz.
* SQL API 'sini kullanan ve [güçlü tutarlılık](../cosmos-db/how-to-manage-database-account.md)Için yapılandırılmış [Azure aboneliğinize dağıtılan bir Cosmos DB veritabanı](../cosmos-db/create-sql-api-dotnet.md#create-account) . Bu kılavuzdaki adımları tamamlayabilmeniz için Cosmos DB veritabanının bağlantı dizesine ihtiyacınız vardır. Bağlantı dizesini Azure portal alabilirsiniz.
* [Azure aboneliğinize dağıtılan bir Azure depolama hesabı](../storage/common/storage-account-create.md). Bu kılavuzdaki adımları tamamlayabilmeniz için depolama hesabının bağlantı dizesine ihtiyacınız vardır. Bağlantı dizesini Azure portal alabilirsiniz.

## <a name="prepare-your-development-environment"></a>Geliştirme ortamınızı hazırlama

Geliştirme ortamınızı hazırlamak için aşağıdaki görevleri doldurun:

* Cihaz benzetimi mikro hizmeti için kaynağı indirin.
* Depolama bağdaştırıcısı mikro hizmeti için kaynağı indirin.
* Depolama bağdaştırıcısı mikro hizmetini yerel olarak çalıştırın.

Bu makaledeki yönergeler Windows kullandığınızı varsayar. Başka bir işletim sistemi kullanıyorsanız, ortamınıza uyacak şekilde bazı dosya yollarını ve komutları ayarlamanız gerekebilir.

### <a name="download-the-microservices"></a>Mikro hizmetleri indirin

GitHub 'dan [Uzaktan Izleme mikro hizmetlerini](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) indirin ve yerel makinenizde uygun bir konuma ayıklayın. Bu depo, bu nasıl yapılır için ihtiyacınız olan depolama bağdaştırıcısı mikro hizmetini içerir.

[Cihaz benzetimi mikro hizmetini](https://github.com/Azure/azure-iot-pcs-device-simulation/archive/master.zip) GitHub 'dan yerel makinenizde uygun bir konuma indirip sıkıştırmasını açın.

### <a name="run-the-storage-adapter-microservice"></a>Depolama bağdaştırıcısı mikro hizmetini çalıştırma

Visual Studio Code, **Remote-Monitoring-Services-DotNet-master\storage-Adapter** klasörünü açın. Çözümlenmemiş bağımlılıkları onarmak için **geri yükleme** düğmelerine tıklayın.

Dosya **üzerinde. vscode/launch.js** açın ve Cosmos DB Bağlantı dizenizi **PC \_ storageadapter \_ DOCUMENTDB \_ connString** ortam değişkenine atayın.

> [!NOTE]
> Mikro hizmeti makinenizde yerel olarak çalıştırdığınızda yine de Azure 'da bir Cosmos DB örneğinin düzgün şekilde çalışmasını gerektirir.

Depolama bağdaştırıcısı mikro hizmetini yerel olarak çalıştırmak için hata ayıklama **\> başlatma hata ayıkla**' ya tıklayın.

Visual Studio Code **Terminal** penceresinde, çalışan mikro hizmetin Web hizmeti sistem durumu denetimi URL 'si de dahil olmak üzere bir çıktı gösterilmektedir: <http://127.0.0.1:9022/v1/status> . Bu adrese gittiğinizde, durumun "Tamam: canlı ve iyi" olması gerekir.

Aşağıdaki adımları tamamladıktan sonra, depolama bağdaştırıcısı mikro hizmetini bu Visual Studio Code örneğinde çalışır durumda bırakın.

## <a name="define-your-device-model"></a>Cihaz modelinizi tanımlama

Yeni bir Visual Studio Code örneğinde GitHub 'dan indirdiğiniz **cihaz benzetimi-DotNet-ana** klasörünü açın. Çözümlenmemiş bağımlılıkları onarmak için **geri yükleme** düğmelerine tıklayın.

Bu nasıl yapılır kılavuzunda, bir varlık İzleyicisi için yeni bir cihaz modeli oluşturursunuz:

1. **Services\data\devicemodeller** klasöründe **assettracker-01.js** adlı yeni bir cihaz modeli dosyası oluşturun.

1. Cihaz modeli **assettracker-01.js** dosyadaki cihaz işlevselliğini tanımlayın. Prototipli cihaz modelinin telemetri bölümü şu şekilde olmalıdır:

   * Cihazınız için oluşturduğunuz Protoarabellek sınıfının adını ekleyin. Aşağıdaki bölümde bu sınıfın nasıl oluşturulacağı gösterilmektedir.
   * İleti biçimi olarak Protoarabellek belirtin.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Cihaz davranışları betiği oluştur

Cihazınızın nasıl davranacağını tanımlayan davranış betiğini yazın. Daha fazla bilgi için bkz. [Gelişmiş sanal cihaz oluşturma](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Prototip biçiminizi tanımlayın

Bir cihaz modeliniz varsa ve ileti biçiminizi belirledikten sonra bir **proto** dosyası oluşturabilirsiniz. **Proto** dosyasında şunu ekleyin:

* `csharp_namespace`Cihaz modelinizdeki **ClassName** özelliği ile eşleşen bir.
* Seri hale getirilecek her veri yapısına yönelik bir ileti.
* İletideki her alan için bir ad ve tür.

1. **Services\models\protobuf\proto** klasöründe **assettracker. proto** adlı yeni bir dosya oluşturun.

1. **Proto** dosyasında sözdizimi, ad alanı ve ileti şemasını aşağıdaki gibi tanımlayın:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

`=1` `=2` Her öğe üzerindeki işaretçiler, alanın ikili kodlamada kullandığı benzersiz bir etiket belirtir. 1-15 numaraları, daha fazla sayıdan kodlamak için bir daha az bayt gerektirir.

## <a name="generate-the-protobuf-class"></a>Prototiparabelleği oluşturma

bir **proto** dosyanız olduğunda, sonraki adım iletileri okumak ve yazmak için gereken sınıfları oluşturmak için kullanılır. Bu adımı gerçekleştirmek için **Protoc** prototipsiz derleyicisine ihtiyacınız vardır.

1. [GitHub 'dan Protoarabellek derleyicisini indirin](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Kaynak dizinini, hedef dizini ve **proto** dosyanızın adını belirterek derleyiciyi çalıştırın. Örnek:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Bu komut, **Services\models\protoarabellek** klasöründe bir **assettracker. cs** dosyası oluşturur.

## <a name="test-protobuf-locally"></a>Yerel olarak test proto

Bu bölümde, önceki bölümlerde oluşturduğunuz varlık izleyici cihazını yerel olarak test edersiniz.

### <a name="run-the-device-simulation-microservice"></a>Cihaz benzetimi mikro hizmetini çalıştırma

Dosyasında **. vscode/launch.js** açın ve aşağıdakileri atayın:

* **Bilgisayar \_ ıothub \_ connString** ortam değişkenine bağlantı dizesi IoT Hub.
* **Bilgisayar \_ Azure \_ depolama \_ hesabı** ortam değişkenine depolama hesabı bağlantı dizesi.
* **Bilgisayar \_ storageadapter \_ DOCUMENTDB \_ connString** ortam değişkenine bağlantı dizesi Cosmos DB.

Dosya **üzerinde Web hizmeti/Özellikler/launchSettings.js** açın ve aşağıdakileri atayın:

* **Bilgisayar \_ ıothub \_ connString** ortam değişkenine bağlantı dizesi IoT Hub.
* **Bilgisayar \_ Azure \_ depolama \_ hesabı** ortam değişkenine depolama hesabı bağlantı dizesi.
* **Bilgisayar \_ storageadapter \_ DOCUMENTDB \_ connString** ortam değişkenine bağlantı dizesi Cosmos DB.

**WebService\appsettings.ini** dosyasını açın ve ayarları aşağıdaki gibi değiştirin:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Çözümü yeni cihaz modeli dosyalarınızı içerecek şekilde yapılandırın

Varsayılan olarak, yeni cihaz modeliniz JSON ve JS dosyalarınız, yerleşik çözüme kopyalanmaz. Bunları açıkça dahil etmeniz gerekir.

Dahil etmek istediğiniz her dosya için **services\services.csproj** dosyasına bir giriş ekleyin. Örnek:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Mikro hizmeti yerel olarak çalıştırmak için hata ayıklama **\> başlatma hata ayıkla**' ya tıklayın.

Visual Studio Code **Terminal** penceresinde, çalışan mikro hizmetten alınan çıkış gösterilmektedir.

Sonraki adımları tamamladıktan sonra cihaz benzetimi mikro hizmetini bu Visual Studio Code örneğinde çalışır durumda bırakın.

### <a name="set-up-a-monitor-for-device-events"></a>Cihaz olayları için izleyici ayarlama

Bu bölümde, IoT Hub 'ınıza bağlı cihazlardan gönderilen telemetrinin görüntüleneceği bir olay izleyicisi ayarlamak için Azure CLı 'yi kullanırsınız.

Aşağıdaki betik, IoT Hub 'ınızın adının **cihaz benzetimi-test** olduğunu varsayar.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Sanal cihazları test ederken olay izleyicisini çalışır durumda bırakın.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Varlık izleyici cihaz türüyle simülasyon oluşturma

Bu bölümde, bir benzetim çalıştırmak için, varlık izleyici cihaz türünü kullanarak cihaz benzetimi mikro hizmetini istemek üzere Postman aracını kullanacaksınız. Postman, bir Web hizmetine REST istekleri göndermenize olanak sağlayan bir araçtır.

Postman 'yi ayarlamak için:

1. Yerel makinenizde Postman 'yi açın.

1. **Dosya \> içeri aktar**' a tıklayın. Ardından **dosyaları seç**' e tıklayın.

1. **Azure IoT cihaz simülasyonu Çözüm Hızlandırıcısı. Postman \_ koleksiyonu** ve **Azure IoT cihaz benzetimi Çözüm Hızlandırıcısı. Postman \_ ortamı** ' nı seçin ve **Aç**' a tıklayın.

1. Gönderebileceğiniz istekleri görüntülemek için **Azure IoT cihaz benzetimi çözüm Hızlandırıcısını** genişletin.

1. **Ortam yok** ' a tıklayın ve **Azure IoT cihaz benzetimi Çözüm Hızlandırıcısı**' nı seçin.

Artık Postman çalışma alanınızda yüklü olan ve cihaz benzetimi mikro hizmeti ile etkileşim kurmak için kullanabileceğiniz bir koleksiyon ve ortamınız vardır.

Benzetimi yapılandırmak ve çalıştırmak için:

1. Postman koleksiyonunda, **varlık izleyici simülasyonu oluştur** ' u seçin ve **Gönder**' e tıklayın. Bu istek, benzetimli varlık izleyici cihaz türünün dört örneğini oluşturur.

1. Azure CLı penceresindeki olay izleyici çıktısı, sanal cihazlardan telemetri gösterir.

Benzetimi durdurmak için Postman 'daki **Benzetim Isteğini durdur** ' u seçin ve **Gönder**' e tıklayın.

### <a name="clean-up-resources"></a>Kaynakları temizleme

Visual Studio Code örneklerinde yerel olarak çalışan iki mikro hizmeti durdurabilirsiniz (**hata ayıklama \> durdurma hata ayıklaması**).

Artık IoT Hub ve Cosmos DB örneklerine ihtiyacınız yoksa, gereksiz ücretlerden kaçınmak için Azure aboneliğinizden silin.

## <a name="iot-hub-support"></a>IoT Hub desteği

Birçok IoT Hub özellik, prototip veya diğer ikili biçimleri yerel olarak desteklemez. Örneğin, IoT Hub ileti yükünü işleyemediği için ileti yüküne göre yönlendirilemez. Ancak, ileti üst bilgilerine göre yönlendirme yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Telemetri göndermek için cihaz benzetiminin prototip kullanmak üzere nasıl özelleştirileceğini öğrendiniz bir sonraki adım, daha fazla [cihaz benzetimi](https://github.com/Azure/azure-iot-pcs-device-simulation)öğrenmek için GitHub deposunu ziyaret etmektir.
