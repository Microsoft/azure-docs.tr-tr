---
title: 'Öğretici: Azure IoT Edge üzerinde Machine Learning saydam ağ geçidi aracılığıyla cihaz verileri gönderme'
description: Bu öğreticide, saydam bir ağ geçidi olarak yapılandırılmış bir cihaz aracılığıyla IoT Hub verileri göndermek için geliştirme makinenizi sanal bir IoT Edge cihazı olarak nasıl kullanabileceğiniz gösterilmektedir.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a830c2cbbc16d5d5e93e6f2283af012e4412c0be
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310657"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Öğretici: saydam ağ geçidi aracılığıyla veri gönderme

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Bu makalede, bir kez sanal cihaz olarak geliştirme sanal makinesi olarak kullanıyoruz. Ancak, IoT Hub doğrudan veri göndermek yerine, cihaz verileri saydam bir ağ geçidi olarak yapılandırılmış IoT Edge cihazına gönderir.

Sanal cihaz veri gönderirken IoT Edge cihazının işlemini izliyoruz. Cihazın çalışması tamamlandığında, her şeyin beklendiği gibi çalıştığını doğrulamak için depolama hesabımızda bulunan verilere baktık.

Bu adım genellikle bir bulut veya cihaz geliştiricisi tarafından gerçekleştirilir.

Öğreticinin bu bölümünde şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
>
> * Bir yaprak cihaz oluşturup çalıştırın.
> * Oluşturulan verilerin Azure Blob depolama alanında depolandığından emin olun.
> * Machine Learning modelinin cihaz verilerini sınıflandırdığını doğrulayın.

## <a name="prerequisites"></a>Önkoşullar

Bu makale, IoT Edge Azure Machine Learning kullanımı hakkında öğretici için bir serinin bir parçasıdır. Serideki her makale, önceki makaledeki iş üzerinde oluşturulur. Bu makaleye doğrudan ulaşdıysanız, serideki [ilk makaleyi](tutorial-machine-learning-edge-01-intro.md) ziyaret edin.

## <a name="review-device-harness"></a>Cihaz bandı gözden geçirme

Aşağı akış (veya yaprak) cihazının benzetimini yapmak için [Devicebandı projesini](tutorial-machine-learning-edge-03-generate-data.md) yeniden kullanın. Saydam ağ geçidine bağlanmak için iki ek şey gerekir:

* Aşağı akış IoT cihazının IoT Edge çalışma zamanı tarafından kullanılan sertifika yetkilisine güvenmesini sağlamak için sertifikayı kaydedin. Bu durumda, bir aşağı akış cihazı geliştirme sanal makinesi.
* Sınır Ağ Geçidi tam etki alanı adını (FQDN) cihaz bağlantı dizesine ekleyin.

Bu iki öğenin nasıl uygulandığını görmek için koda bakın.

1. Geliştirme makinenizde Visual Studio Code açın.

1.   >  C: kaynak ıotedgeandmlsample Device, dosyasını açmak için dosya **klasörü aç...** öğesini kullanın \\ \\ \\ .

1. Program. cs içindeki InstallCertificate () yöntemine bakın.

1. Kod sertifika yolunu bulursa, sertifikayı makineye yüklemek için CertificateManager. ınstallcacert yöntemini çağırır.

1. Şimdi TurbofanDevice sınıfında Getımpl Ubdevice yöntemine bakın.

1. Kullanıcı, "-g" seçeneğini kullanarak ağ geçidinin FQDN 'sini belirttiğinde, bu değer bu yönteme `gatewayFqdn` Cihaz bağlantı dizesine eklenen değişken olarak geçirilir.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Yaprak cihaz oluşturma ve çalıştırma

1. Devicebir Project Visual Studio Code hala açıkken, projeyi derleyin. **Terminal** menüsünde **derleme görevini Çalıştır** ' ı seçin ve **Oluştur**' u seçin.

1. Uç ağ geçidinizin için tam etki alanı adını (FQDN), Azure portal IoT Edge cihazınıza (Linux VM) giderek ve **DNS adı** değerini genel bakış sayfasından kopyalayarak bulun.

1. Zaten çalışmıyorsa IoT cihazınızı (Linux VM) başlatın.

1. Visual Studio Code terminalini açın. **Terminal** menüsünden **yeni Terminal** ' ı seçin ve aşağıdaki komutu ÇALıŞTıRARAK `<edge_device_fqdn>` IoT Edge cihazdan kopyaladığınız DNS adıyla değiştirin (Linux VM):

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. Uygulama, sertifikayı geliştirme makinenize yüklemeye çalışır. Bunu yaptığınızda güvenlik uyarısını kabul edin.

1. IoT Hub bağlantı dizesi istendiğinde, Azure IoT Hub cihazlar panelinde üç nokta (**...**) simgesine tıklayın ve **IoT Hub bağlantı dizesini Kopyala**' yı seçin. Değeri terminale yapıştırın.

1. Aşağıdakine benzer bir çıktı görürsünüz:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Cihazın IoT Edge saydam ağ geçidiyle IoT Hub aracılığıyla iletişim kurmasına neden olan cihaz bağlantı dizesine "GatewayHostName" eklenmesi gerektiğini aklınızda yapın.

## <a name="check-output"></a>Çıktıyı denetle

### <a name="iot-edge-device-output"></a>IoT Edge cihaz çıkışı

AvroFileWriter modülünün çıktısı, IoT Edge cihazına bakarak kolayca gözlemlenebilir.

1. IoT Edge sanal makinenize SSH.

1. Diske yazılan dosyaları arayın.

   ```bash
   find /data/avrofiles -type f
   ```

1. Komutun çıktısı aşağıdaki örneğe benzer şekilde görünür:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Çalıştırmanın zamanlamasına bağlı olarak birden fazla tek dosyanız olabilir.

1. Zaman damgalarına dikkat edin. AvroFileWriter modülü, son değiştirilme zamanı 10 dakikadan uzun olduğunda dosyaları buluta yükler (bkz \_ \_ . avrofilewriter modülünde UPLOADER.py içinde değiştirilen dosya zaman aşımı).

1. 10 dakika geçtikten sonra modülün dosyaları karşıya yüklemesi gerekir. Karşıya yükleme başarılı olursa, dosyaları diskten siler.

### <a name="azure-storage"></a>Azure Depolama

Verilerin yönlendirilmesini beklediğimiz depolama hesaplarına bakarak yaprak cihazımızın sonuçlarını gözlemleyebiliriz.

1. Geliştirme makinesinde Visual Studio Code açın.

1. Bul penceresindeki "AZURE STORAGE" panelinde, depolama hesabınızı bulmak için ağaca gidin.

1. **BLOB kapsayıcıları** düğümünü genişletin.

1. Öğreticinin önceki bölümünde yaptığımız iş sayesinde, **ruldata** kapsayıcısının rul ile ileti içermesi gerektiğini umuz. **Ruldata** düğümünü genişletin.

1. Şöyle adlı bir veya daha fazla BLOB dosyası görürsünüz: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>` .

1. Dosyalardan birine sağ tıklayın ve dosyayı geliştirme makinenize kaydetmek için **blobu indir** ' i seçin.

1. Daha sonra **uploadturbofanfiles** düğümünü genişletin. Önceki makalede, bu konumu avroFileWriter modülü tarafından karşıya yüklenen dosyalar için hedef olarak ayarlayacağız.

1. Dosyalara sağ tıklayıp **blobu indir** ' i seçerek geliştirme makinenize kaydedin.

### <a name="read-avro-file-contents"></a>Avro dosya içeriğini oku

Avro dosyasını okumak ve dosyadaki iletilerin JSON dizesini döndürmek için basit bir komut satırı yardımcı programı ekledik. Bu bölümde, yükleyeceğiz ve çalıştıracağız.

1. Visual Studio Code (**terminalden**  >  **yeni Terminal**) ' de bir Terminal açın.

1. Kubavroreader 'ı Install:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. **Rulum verilerinden** indirdiğiniz avro dosyasını okumak için hubavroreader 'ı kullanın.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. İleti gövdesinin cihaz KIMLIĞI ve tahmin edilen RUL ile beklendiğimiz gibi göründüğünü unutmayın.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. **Uploadturbofanfiles**'Tan indirdiğiniz avro dosyasını geçirerek aynı komutu çalıştırın.

1. Bu iletiler beklendiği gibi, özgün iletideki tüm algılayıcı verilerini ve işletimsel ayarları içerir. Bu veriler, Edge cihazımızda RUL modelini geliştirmek için kullanılabilir.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uçtan uca öğretici tarafından kullanılan kaynakları keşfetmeye çalışırsanız, oluşturduğunuz kaynakları temizlemek için bitene kadar bekleyin. Aksi takdirde, bunları silmek için aşağıdaki adımları kullanın:

1. Geliştirme VM 'si, IoT Edge VM, IoT Hub, depolama hesabı, Machine Learning çalışma alanı hizmeti (ve oluşturulan kaynaklar: kapsayıcı kayıt defteri, Application Insights, Anahtar Kasası, depolama hesabı) tutmak üzere oluşturulan kaynak gruplarını silin.

1. Machine Learning projesini [Azure Notebooks](https://notebooks.azure.com)silin.

1. Depoyu yerel olarak klondıysanız, yerel depoya başvuran tüm PowerShell veya VS Code pencerelerini kapatın ve sonra depo dizinini silin.

1. Sertifikaları yerel olarak oluşturduysanız c: \\ edgecercertificate klasörünü silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, IoT Edge cihazımız algılayıcısı ve işletimsel verileri gönderen bir yaprak cihazın benzetimini yapmak için geliştirme sanal makinesini kullandık. Cihazdaki modüllerin, sınır cihazının gerçek zamanlı işlemini inceleyerek ve depolama hesabına yüklenen dosyalara bakarak verileri yönlendirdiğini, sınıflandırdığını, kalıcı ve karşıya yüklediğini doğruladı.

IoT Edge özellikleri hakkında öğrenmeye devam etmek için, sonraki öğreticiyi deneyin:

> [!div class="nextstepaction"]
> [IoT Edge cihaz hiyerarşisi oluşturma](tutorial-nested-iot-edge.md?view=iotedge-2020-11&preserve-view=true)