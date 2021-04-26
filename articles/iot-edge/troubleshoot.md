---
title: Sorun giderme-Azure IoT Edge | Microsoft Docs
description: Bileşen durumu ve günlüklerini alma gibi Azure IoT Edge için standart tanılama becerileri öğrenmek için bu makaleyi kullanın
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fa49af946a1e5fc631eeb1ee9b9c7c99d3adff8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308277"
---
# <a name="troubleshoot-your-iot-edge-device"></a>IoT Edge cihazınızda sorun giderme

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Ortamınızda Azure IoT Edge çalışan sorunlarla karşılaşırsanız, sorun giderme ve Tanılama için bu makaleyi kılavuz olarak kullanın.

## <a name="run-the-check-command"></a>' Check ' komutunu çalıştırın

Sorun giderme IoT Edge ilk adımlarınız `check` , genel sorunlar için yapılandırma ve bağlantı testlerinin bir koleksiyonunu çalıştıran komutunu kullanmaktır. `check`Komut, [Release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) ve üzeri sürümlerde kullanılabilir.

>[!NOTE]
>IoT Edge cihaz bir proxy sunucusunun arkasındaysa sorun giderme aracı bağlantı denetimlerini çalıştıramıyor.

`check`Komutu aşağıdaki gibi çalıştırabilirsiniz veya `--help` seçeneklerin tamamı listesini görmek için bayrağını dahil edebilirsiniz:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Linux 'ta:

```bash
sudo iotedge check
```

Windows'da:

```powershell
iotedge check
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.1 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge check
```

:::moniker-end
<!-- end 1.2 -->

Sorun giderme aracı, bu üç kategoride sıralanan birçok denetimi çalıştırır:

* *Yapılandırma denetimleri* , yapılandırma dosyası ve kapsayıcı altyapısı ile ilgili sorunlar da dahil olmak üzere IoT Edge cihazların buluta bağlanmasını engelleyebilecek ayrıntıları inceler.
* *Bağlantı denetimleri* , IoT Edge çalışma zamanının konak cihazdaki bağlantı noktalarına erişebileceğini ve tüm IoT Edge bileşenlerinin IoT Hub bağlanabildiğini doğrular. IoT Edge cihaz bir proxy 'nin arkasındaysa bu denetim kümesi hataları döndürür.
* *Üretime hazırlık denetimleri* , cihaz sertifika YETKILISI (CA) sertifikalarının durumu ve modül günlük dosyası yapılandırması gibi önerilen üretim en iyi uygulamalarına bakar.

IoT Edge Denetim Aracı, tanılamayı çalıştırmak için bir kapsayıcı kullanır. Kapsayıcı görüntüsü, `mcr.microsoft.com/azureiotedge-diagnostics:latest` [Microsoft Container Registry](https://github.com/microsoft/containerregistry)ile kullanılabilir. Bir cihazda İnternet 'e doğrudan erişim olmadan bir denetim çalıştırmanız gerekiyorsa, cihazlarınız kapsayıcı görüntüsüne erişmesi gerekir.

<!-- <1.2> -->
:::moniker range=">=iotedge-2020-11"

İç içe IoT Edge cihazları kullanan bir senaryoda, ana cihazlarda görüntü çekmeyi yönlendirerek alt cihazlarda tanılama görüntüsüne erişim sağlayabilirsiniz.

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:<port_for_api_proxy_module>/azureiotedge-diagnostics:1.2
```

<!-- </1.2> -->
:::moniker-end

Bu aracın çalıştığı her bir tanılama denetimi hakkında bilgi için, bir hata veya uyarı alırsanız, [IoT Edge sorun giderme denetimleri](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)bölümüne bakın.

## <a name="gather-debug-information-with-support-bundle-command"></a>' Support-demeti ' komutuyla hata ayıklama bilgilerini toplayın

IoT Edge cihazdan Günlükler toplamanız gerektiğinde, en uygun yöntem `support-bundle` komutunu kullanmaktır. Varsayılan olarak, bu komut modül, IoT Edge Güvenlik Yöneticisi ve kapsayıcı altyapısı günlükleri, `iotedge check` JSON çıktısı ve diğer yararlı hata ayıklama bilgilerini toplar. Bunları kolay paylaşım için tek bir dosya halinde sıkıştırır. `support-bundle`Komut, [Release 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) ve üzeri sürümlerde kullanılabilir.

`support-bundle` `--since` Geçmişteki günlüklere ne kadar süre almak istediğinizi belirtmek için komutunu bayrağıyla çalıştırın. Örneğin, son altı günden bu yana son altı dakikadan bu yana son altı `6h` saat sonra günlükleri alır `6d` `6m` . `--help`Seçeneklerin tamamı listesini görmek için bayrağını ekleyin.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Linux 'ta:

```bash
sudo iotedge support-bundle --since 6h
```

Windows'da:

```powershell
iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.2 -->

Destek-paket komutunun çıkışını Azure Blob depolama alanına yüklemek için cihazınıza [doğrudan yöntem](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics) çağrısı da kullanabilirsiniz.

> [!WARNING]
> `support-bundle`Komutun çıktısı ana bilgisayar, cihaz ve modül adları, modülleriniz tarafından günlüğe kaydedilen bilgiler içerebilir. Çıktıyı ortak bir forumunda paylaşımında lütfen unutmayın.

## <a name="check-your-iot-edge-version"></a>IoT Edge sürümünüzü denetleyin

IoT Edge'in eski bir sürümünü çalıştırıyorsanız, yükseltme yapmak sorununuzu çözebilir. `iotedge check`Araç, IoT Edge güvenlik arka plan programının en son sürümü olup olmadığını denetler, ancak IoT Edge hub ve aracı modüllerinin sürümlerini denetlemez. Cihazınızdaki çalışma zamanı modüllerinin sürümünü denetlemek için, ve komutlarını kullanın `iotedge logs edgeAgent` `iotedge logs edgeHub` . Modül başlatıldığında sürüm numarası günlüklerde bildirilir.

Cihazınızı güncelleştirme hakkında yönergeler için bkz. [IoT Edge güvenlik cini ve çalışma zamanını güncelleştirme](how-to-update-iot-edge.md).

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>Cihazlarınızda IoT Edge yüklemesini doğrulayın

[Ikizi edgeAgent modülünü izleyerek](./how-to-monitor-module-twins.md)cihazlarınızda IoT Edge yüklemesini doğrulayabilirsiniz.

En son edgeAgent modülünü ikizi almak için [Azure Cloud Shell](https://shell.azure.com/)adresinden aşağıdaki komutu çalıştırın:

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id $edgeAgent --hub-name <iot_hub_name>
   ```

Bu komut, bildirilen tüm edgeAgent [özelliklerini](./module-edgeagent-edgehub.md)çıktı olarak alır. Cihazın durumunu izleyen bazı yararlı bilgiler aşağıda verilmiştir:

* çalışma zamanı durumu
* çalışma zamanı başlangıç zamanı
* çalışma zamanının son çıkış zamanı
* çalışma zamanı yeniden başlatma sayısı

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>IoT Edge Güvenlik Yöneticisi ve günlüklerinin durumunu kontrol edin

[IoT Edge Güvenlik Yöneticisi](iot-edge-security-manager.md) , başlatma ve sağlama cihazlarında IoT Edge sistemi başlatma gibi işlemlerden sorumludur. IoT Edge başlatılamıyorsa, Güvenlik Yöneticisi günlükleri yararlı bilgiler sağlayabilir.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Linux 'ta:

* IoT Edge Security Manager 'ın durumunu görüntüleyin:

   ```bash
   sudo systemctl status iotedge
   ```

* IoT Edge Güvenlik Yöneticisi 'nin günlüklerini görüntüleyin:

   ```bash
   sudo journalctl -u iotedge -f
   ```

* IoT Edge Security Manager 'ın daha ayrıntılı günlüklerini görüntüleyin:

  1. IoT Edge Daemon ayarlarını düzenleyin:

     ```bash
     sudo systemctl edit iotedge.service
     ```

  2. Aşağıdaki satırları güncelleştirin:

     ```bash
     [Service]
     Environment=IOTEDGE_LOG=debug
     ```

  3. IoT Edge güvenlik cini 'nı yeniden başlatın:

     ```bash
     sudo systemctl cat iotedge.service
     sudo systemctl daemon-reload
     sudo systemctl restart iotedge
     ```

Windows'da:

* IoT Edge Security Manager 'ın durumunu görüntüleyin:

   ```powershell
   Get-Service iotedge
   ```

* IoT Edge Güvenlik Yöneticisi 'nin günlüklerini görüntüleyin:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* IoT Edge Security Manager günlüklerinin yalnızca son 5 dakikasını görüntüleyin:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* IoT Edge Security Manager 'ın daha ayrıntılı günlüklerini görüntüleyin:

  1. Sistem düzeyi ortam değişkeni ekleyin:

     ```powershell
     [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
     ```

  2. IoT Edge güvenlik cini 'nı yeniden başlatın:

     ```powershell
     Restart-Service iotedge
     ```

:::moniker-end
<!--end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* IoT Edge sistem hizmetlerinin durumunu görüntüleyin:

   ```bash
   sudo iotedge system status
   ```

* IoT Edge sistem hizmetlerinin günlüklerini görüntüleyin:

   ```bash
   sudo iotedge system logs -- -f
   ```

* IoT Edge sistem hizmetlerinin daha ayrıntılı günlüklerini görüntülemek için hata ayıklama düzeyi günlüklerini etkinleştirin:

  1. Hata ayıklama düzeyi günlüklerini etkinleştirin.

     ```bash
     sudo iotedge system set-log-level debug
     sudo iotedge system restart
     ```

  1. Hata ayıkladıktan sonra varsayılan bilgi düzeyi günlüklerine geri dönün.

     ```bash
     sudo iotedge system set-log-level info
     sudo iotedge system restart
     ```

:::moniker-end
<!-- end 1.2 -->

## <a name="check-container-logs-for-issues"></a>Sorunlar için kapsayıcı günlüklerine bakın

IoT Edge güvenlik arka plan programı çalışmaya başladıktan sonra, sorunları algılamak için kapsayıcıların günlüklerine bakın. Dağıtılan kapsayıcılarınız ile başlayın ve ardından IoT Edge çalışma zamanını oluşturan kapsayıcılara bakın: edgeAgent ve edgeHub. IoT Edge Aracısı günlükleri genellikle her kapsayıcının yaşam döngüsü hakkında bilgi sağlar. IoT Edge hub günlükleri mesajlaşma ve yönlendirme hakkında bilgi sağlar.

```cmd
iotedge logs <container name>
```

Ayrıca, Bu modülün günlüklerini Azure Blob depolama alanına yüklemek için cihazınızdaki bir modüle [doğrudan yöntem](how-to-retrieve-iot-edge-logs.md#upload-module-logs) çağrısı kullanabilirsiniz.

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>IoT Edge hub 'ından giden iletileri görüntüleme

<!--1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge hub 'ından gelen iletileri görüntüleyebilir ve çalışma zamanı kapsayıcılarından ayrıntılı günlüklerden Öngörüler toplayabilirsiniz. Bu kapsayıcılarda ayrıntılı günlükleri açmak için, `RuntimeLogLevel` YAML yapılandırma dosyanızda öğesini ayarlayın. Dosyayı açmak için:

Linux 'ta:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows'da:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Varsayılan olarak, `agent` öğesi aşağıdaki örneğe benzer şekilde görünür:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.1
       auth: {}
   ```

Değiştir `env: {}` :

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML dosyaları girintileme olarak sekme içeremez. Bunun yerine 2 boşluk kullanın. Üst düzey öğelerin başında boşluk bulunamaz.

Dosyayı kaydedin ve IoT Edge Güvenlik Yöneticisi ' ni yeniden başlatın.

<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge hub 'ı aracılığıyla giden iletileri görüntüleyebilir ve çalışma zamanı kapsayıcılarından ayrıntılı günlüklerden Öngörüler toplayabilirsiniz. Bu kapsayıcılarda ayrıntılı günlükleri açmak için `RuntimeLogLevel` dağıtım bildiriminde ortam değişkenini ayarlayın.

IoT Edge hub 'ından giden iletileri görüntülemek için, `RuntimeLogLevel` ortam değişkenini `debug` edgeHub modülü için olarak ayarlayın.

Hem edgeHub hem de edgeAgent modülleri bu çalışma zamanı günlüğü ortam değişkenine sahiptir ve varsayılan değer olarak ayarlanır `info` . Bu ortam değişkeni aşağıdaki değerleri alabilir:

* hataya
* error
* warning
* bilgiler
* hata ayıklama
* ayrıntılı

<!-- end 1.2 -->
:::moniker-end

Ayrıca, IoT Hub ile IoT cihazları arasında gönderilen iletileri de denetleyebilirsiniz. [Visual Studio Code Için Azure IoT Hub uzantısını](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)kullanarak bu iletileri görüntüleyin. Daha fazla bilgi için bkz. [Azure IoT ile geliştirme yaparken kullanışlı araç](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

## <a name="restart-containers"></a>Kapsayıcıları yeniden Başlat

Günlükler ve iletileri bilgi için araştırdıktan sonra kapsayıcıları yeniden başlatmayı deneyebilirsiniz:

```cmd
iotedge restart <container name>
```

IoT Edge çalışma zamanı kapsayıcılarını yeniden başlatın:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>Güvenlik Duvarı ve bağlantı noktası yapılandırma kurallarınızı denetleyin

Azure IoT Edge, desteklenen IoT Hub protokolleri kullanılarak şirket içi bir sunucudan Azure bulutuna iletişimin iletişimine izin veriyor, bkz. [iletişim protokolü seçme](../iot-hub/iot-hub-devguide-protocols.md). Gelişmiş güvenlik için Azure IoT Edge ile Azure IoT Hub arasındaki iletişim kanalları her zaman giden olarak yapılandırılır. Bu yapılandırma, kötü amaçlı bir varlığın araştırılacak saldırı yüzeyini en aza indiren [hizmet yardımlı iletişim düzenine](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices)dayanır. Gelen iletişim yalnızca Azure IoT Hub Azure IoT Edge cihaza ileti göndermek için gereken belirli senaryolar için gereklidir. Buluttan cihaza iletiler, güvenli TLS kanalları kullanılarak korunur ve X. 509.440 sertifikaları ve TPM cihaz modülleri kullanılarak daha da güvenli hale getirilir. Azure IoT Edge Güvenlik Yöneticisi bu iletişimin nasıl kurulamadığını yönetir, bkz. [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

IoT Edge, Azure IoT Edge çalışma zamanına ve dağıtılan modüllere güvenli hale getirmek için gelişmiş yapılandırma sağlarken, hala temel alınan makineye ve ağ yapılandırmasına bağımlıdır. Bu nedenle, güvenli uç için bulut iletişimine doğru ağ ve Güvenlik Duvarı kurallarının ayarlandığından emin olmanız zorunludur. Aşağıdaki tablo, Azure IoT Edge çalışma zamanının barındırıldığı temel sunucular için yapılandırma güvenlik duvarı kuralları olduğunda bir kılavuz olarak kullanılabilir:

|Protokol|Bağlantı noktası|Gelen|Tarafına|Rehber|
|--|--|--|--|--|
|MQTT|8883|ENGELLENDI (varsayılan)|ENGELLENDI (varsayılan)|<ul> <li>İletişim protokolü olarak MQTT kullanılırken açık olacak giden (giden) öğesini yapılandırın.<li>MQTT için 1883 IoT Edge desteklenmez. <li>Gelen (gelen) bağlantıların engellenmesi gerekir.</ul>|
|AMQP|5671|ENGELLENDI (varsayılan)|Açık (varsayılan)|<ul> <li>IoT Edge için varsayılan iletişim protokolü. <li> Desteklenen diğer protokoller için Azure IoT Edge yapılandırılmamışsa veya AMQP istenen iletişim protokollemiyorsa, bu açık olacak şekilde yapılandırılmış olmalıdır.<li>IoT Edge, AMQP için 5672 tarafından desteklenmiyor.<li>Azure IoT Edge farklı IoT Hub desteklenen bir protokol kullandığında bu bağlantı noktasını engelleyin.<li>Gelen (gelen) bağlantıların engellenmesi gerekir.</ul></ul>|
|HTTPS|443|ENGELLENDI (varsayılan)|Açık (varsayılan)|<ul> <li>IoT Edge sağlama için 443 tarihinde açık olan giden (giden) öğesini yapılandırın. El ile betik veya Azure IoT cihaz sağlama hizmeti (DPS) kullanılırken bu yapılandırma gereklidir. <li>Gelen (gelen) bağlantı yalnızca belirli senaryolar için açık olmalıdır: <ul> <li>  Yöntem istekleri gönderebilen yaprak cihazlara sahip saydam bir ağ geçidiniz varsa. Bu durumda, ıothub 'e bağlanmak veya Azure IoT Edge aracılığıyla ıothub hizmetleri sağlamak için bağlantı noktası 443 ' nin dış ağlara açık olması gerekmez. Bu nedenle gelen kural, iç ağdan yalnızca gelen (gelen) öğesini açmak üzere kısıtlanabilir. <li> Istemciden cihaza (C2D) senaryolar için.</ul><li>HTTP için 80 IoT Edge desteklenmez.<li>HTTP olmayan protokoller (örneğin, AMQP veya MQTT) kuruluşta yapılandırılamaz; iletiler WebSockets üzerinden gönderilebilir. Bağlantı noktası 443, bu durumda WebSocket iletişimi için kullanılacaktır.</ul>|

## <a name="next-steps"></a>Sonraki adımlar

IoT Edge platformunda bir hata bulduğunuzu düşünüyor musunuz? İyileşmeye devam edebilmemiz için [bir sorun gönderin](https://github.com/Azure/iotedge/issues) .

Daha fazla sorunuz varsa yardım için bir [destek isteği](https://portal.azure.com/#create/Microsoft.Support) oluşturun.
