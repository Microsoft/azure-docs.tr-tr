---
title: 'Öğretici: Azure IoT Edge üzerinde Machine Learning saydam ağ geçidi aracılığıyla cihaz verileri gönderme'
description: 'Öğretici: saydam bir ağ geçidi olarak yapılandırılmış bir cihaz aracılığıyla IoT Hub verileri göndermek için geliştirme makinenizi sanal bir IoT Edge cihazı olarak kullanın.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1a1974a9d9e635bad4094e38c2c194ff49993611
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113952"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Öğretici: saydam ağ geçidi aracılığıyla veri gönderme

> [!NOTE]
> Bu makale, IoT Edge Azure Machine Learning kullanımı hakkında öğretici için bir serinin bir parçasıdır. Bu makaleye doğrudan ulaşdıysanız, en iyi sonuçlar için serideki [ilk makaleyle](tutorial-machine-learning-edge-01-intro.md) başlamanızı öneririz.

Bu makalede, bir kez geliştirme makinesini sanal cihaz olarak kullanmaya, ancak cihazın verileri saydam bir ağ geçidi olarak yapılandırılmış IoT Edge cihazına göndermesi yerine, doğrudan IoT Hub.

Sanal cihaz veri gönderirken IoT Edge cihazının işlemini izliyoruz. Cihazın çalışması tamamlandığında, her şeyin beklendiği gibi çalıştığını doğrulamak için depolama hesabımızda bulunan verilere baktık.

Bu adım genellikle bir bulut veya cihaz geliştiricisi tarafından gerçekleştirilir.

## <a name="review-device-harness"></a>Cihaz bandı gözden geçirme

Aşağı akış (veya yaprak) cihazının benzetimini yapmak için [Devicebandı projesini](tutorial-machine-learning-edge-03-generate-data.md) yeniden kullanın. Saydam ağ geçidine bağlanmak için iki ek şey gerekir:

* Aşağı akış cihazını (Bu durumda geliştirme makinemiz) IoT Edge çalışma zamanı tarafından kullanılan sertifika yetkilisine güvenmesini sağlamak için sertifikayı kaydedin.
* Sınır Ağ Geçidi tam etki alanı adını (FQDN) cihaz bağlantı dizesine ekleyin.

Bu iki öğenin nasıl uygulandığını görmek için koda bakın.

1. Geliştirme makinenizde Visual Studio Code açın.

2. **Dosya** > **klasörü aç...** ' ı açmak için,\\kaynak\\ıotedgeandmlsample\\devicecontrol kullanın.

3. Program.cs ' de InstallCertificate () yöntemine bakın.

4. Kod sertifika yolunu bulursa, sertifikayı makineye yüklemek için CertificateManager. ınstallcacert yöntemini çağırır.

5. Şimdi TurbofanDevice sınıfında Getımpl Ubdevice yöntemine bakın.

6. Kullanıcı, "-g" seçeneğini kullanarak ağ geçidinin FQDN 'sini belirttiğinde, bu değer bu yönteme cihaz bağlantı dizesine eklenmiş olan gatewayFqdn olarak geçirilir.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Yaprak cihaz oluşturma ve çalıştırma

1. Devicebir Project Visual Studio Code hala açıkken, projeyi derleyin (Ctrl + Shift + B veya **Terminal** > **Derleme görevi çalıştır...** ) ve iletişim kutusundan **Oluştur** ' u seçin.

2. Portalda IoT Edge cihaz sanal makinenize giderek ve **DNS adı** değerini genel bakış ' dan kopyalayarak kenar ağ geçidinizin için tam etki alanı adını (FQDN) bulun.

3. Visual Studio Code terminali (**terminal** > **yeni Terminal**) açın ve aşağıdaki komutu çalıştırarak `<edge_device_fqdn>`, sanal makineden kopyaladığınız DNS adıyla değiştirin:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. Uygulama, sertifikayı geliştirme makinenize yüklemeye çalışır. Bunu yaptığınızda güvenlik uyarısını kabul edin.

5. IoT Hub bağlantı dizesi istendiğinde, Azure IoT Hub cihazlar panelinde üç nokta ( **...** ) simgesine tıklayın ve **IoT Hub bağlantı dizesini Kopyala**' yı seçin. Değeri terminale yapıştırın.

6. Aşağıdakine benzer bir çıktı görürsünüz:

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

2. Diske yazılan dosyaları arayın.

   ```bash
   find /data/avrofiles -type f
   ```

3. Komutun çıktısı aşağıdaki örneğe benzer şekilde görünür:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Çalıştırmanın zamanlamasına bağlı olarak birden fazla tek dosyanız olabilir.

4. Zaman damgalarına dikkat edin. AvroFileWriter modülü, son değiştirilme zamanı 10 dakikadan daha uzun bir süre (bkz. DEĞIŞTIRILEN\_dosyası\_zaman AŞıMı), avroFileWriter modülünde, dosyaları buluta yükler.

5. 10 dakika geçtikten sonra modülün dosyaları karşıya yüklemesi gerekir. Karşıya yükleme başarılı olursa, dosyaları diskten siler.

### <a name="azure-storage"></a>Azure Storage

Verilerin yönlendirilmesini beklediğimiz depolama hesaplarına bakarak yaprak cihazımızın sonuçlarını gözlemleyebiliriz.

1. Geliştirme makinesinde Visual Studio Code açın.

2. Bul penceresindeki "AZURE STORAGE" panelinde, depolama hesabınızı bulmak için ağaca gidin.

3. **BLOB kapsayıcıları** düğümünü genişletin.

4. Öğreticinin önceki bölümünde yaptığımız iş sayesinde, **ruldata** kapsayıcısının rul ile ileti içermesi gerektiğini umuz. **Ruldata** düğümünü genişletin.

5. Şu şekilde adlandırılan bir veya daha fazla BLOB dosyası görürsünüz: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

6. Dosyalardan birine sağ tıklayın ve dosyayı geliştirme makinenize kaydetmek için **blobu indir** ' i seçin.

7. Daha sonra **uploadturbofanfiles** düğümünü genişletin. Önceki makalede, bu konumu avroFileWriter modülü tarafından karşıya yüklenen dosyalar için hedef olarak ayarlayacağız.

8. Dosyalara sağ tıklayıp **blobu indir** ' i seçerek geliştirme makinenize kaydedin.

### <a name="read-avro-file-contents"></a>Avro dosya içeriğini oku

Avro dosyasını okumak ve dosyadaki iletilerin JSON dizesini döndürmek için basit bir komut satırı yardımcı programı ekledik. Bu bölümde, yükleyeceğiz ve çalıştıracağız.

1. Visual Studio Code bir Terminal açın (**terminal** > **yeni Terminal**).

2. Kubavroreader 'ı Install:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. **Rulum verilerinden**indirdiğiniz avro dosyasını okumak için hubavroreader 'ı kullanın.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. İleti gövdesinin cihaz KIMLIĞI ve tahmin edilen RUL ile beklendiğimiz gibi göründüğünü unutmayın.

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

5. **Uploadturbofanfiles**'Tan indirdiğiniz avro dosyasını geçirerek aynı komutu çalıştırın.

6. Bu iletiler beklendiği gibi, özgün iletideki tüm algılayıcı verilerini ve işletimsel ayarları içerir. Bu veriler, Edge cihazımızda RUL modelini geliştirmek için kullanılabilir.

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

Bu uçtan uca öğretici tarafından kullanılan kaynakları keşfetmeye çalışırsanız, oluşturduğunuz kaynakları temizlemek için bitene kadar bekleyin. Devam etmeyi planlamıyorsanız, bunları silmek için aşağıdaki adımları kullanın:

1. Geliştirme VM 'si, IoT Edge VM, IoT Hub, depolama hesabı, Machine Learning çalışma alanı hizmeti (ve oluşturulan kaynaklar: kapsayıcı kayıt defteri, Application Insights, Anahtar Kasası, depolama hesabı) tutmak üzere oluşturulan kaynak gruplarını silin.

2. [Azure not defterlerinde](https://notebooks.azure.com)makine öğrenimi projesini silin.

3. Depoyu yerel olarak klondıysanız, yerel depoya başvuran tüm PowerShell veya VS Code pencerelerini kapatın ve sonra depo dizinini silin.

4. Sertifikaları yerel olarak oluşturduysanız, c:\\Edgecercertificate klasörünü silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, uç cihazımızı algılayıcı ve işletimsel veriler gönderen bir yaprak cihazın benzetimini yapmak için geliştirme makinemizi kullandık. Uç cihazın gerçek zamanlı işlemini inceleyerek ve sonra depolama hesabına yüklenen dosyalara bakarak, cihazdaki modüllerin yönlendirdiğini, sınıflandırıldığını, kalıcı hale getirerek ve karşıya verileri yüklediğini doğruladı.

Aşağıdaki sayfalarda daha fazla bilgi bulabilirsiniz:

* [Aşağı akış cihazını Azure IoT Edge ağ geçidine bağlama](how-to-connect-downstream-device.md)
* [IoT Edge Azure Blob Storage ile verileri kenarda depolayın (Önizleme)](how-to-store-data-blob.md)
