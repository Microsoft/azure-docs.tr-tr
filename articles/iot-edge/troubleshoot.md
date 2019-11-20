---
title: Sorun giderme - Azure IOT Edge | Microsoft Docs
description: Bu makalede standart tanılama yetenekleri için Azure IOT Edge, bileşen durumu ve günlükleri alma gibi öğrenin ve yaygın sorunları çözme
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 61c75c011ce25c3c7238ec75cf5ed579e677531f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091385"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge için genel sorunlar ve çözümler

Ortamınızda Azure IoT Edge’i kullanma konusunda sorun yaşarsanız, sorun giderme ve çözümleme için kılavuz olarak bu makaleden yararlanın.

## <a name="run-the-iotedge-check-command"></a>İotedge ' Check ' komutunu çalıştırın

Sorun giderme IoT Edge ilk adımlarınız, yaygın sorunlar için yapılandırma `check` ve bağlantı testlerinin bir koleksiyonunu gerçekleştiren komutunu kullanmaktır. Komut `check` , [Release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) ve üzeri sürümlerde kullanılabilir.

`check` Komutu aşağıdaki gibi çalıştırabilirsiniz veya seçeneklerin tamamı listesini görmek için `--help` bayrağını dahil edebilirsiniz:

* Linux üzerinde:

  ```bash
  sudo iotedge check
  ```

* Windows'da:

  ```powershell
  iotedge check
  ```

Araç tarafından çalıştırılan denetim türleri şöyle sınıflandırılabilirler:

* Yapılandırma denetimleri: *Config. YAML* ve kapsayıcı altyapısı ile ilgili sorunlar da dahil olmak üzere uç cihazların buluta bağlanmasını engelleyebilecek ayrıntıları inceler.
* Bağlantı denetimleri: IoT Edge çalışma zamanının konak cihazdaki bağlantı noktalarına erişebileceğini ve tüm IoT Edge bileşenlerinin IoT Hub bağlanabildiğini doğrular.
* Üretime hazırlık denetimleri: Cihaz sertifika yetkilisi (CA) sertifikalarının durumu ve modül günlük dosyası yapılandırması gibi önerilen üretim en iyi uygulamalarına bakar.

Tanılama denetimlerinin tam listesi için bkz. [yerleşik sorun giderme işlevselliği](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Standart tanılama adımları

Bir sorunla karşılaşırsanız, kapsayıcı günlüklerini ve cihazdan geçen iletileri inceleyerek IoT Edge cihazınızın durumu hakkında daha fazla bilgi edinebilirsiniz. Bilgi toplamak için bu bölümdeki komutları ve araçları kullanın.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>IoT Edge Güvenlik Yöneticisi ve günlüklerinin durumunu kontrol edin

Linux üzerinde:
- IOT Edge Güvenlik Yöneticisi'nin durumunu görüntülemek için:

   ```bash
   sudo systemctl status iotedge
   ```

- IOT Edge Güvenlik Yöneticisi'nin günlükleri görüntülemek için:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Ayrıntılı günlükleri, IOT Edge Güvenlik Yöneticisi'ni daha fazla bilgi görüntülemek için:

   - İotedge arka plan programı ayarları düzenleyin:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Aşağıdaki satırları güncelleştirin:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - IOT Edge güvenlik Daemon'unu yeniden başlatın:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Windows'da:
- IOT Edge Güvenlik Yöneticisi'nin durumunu görüntülemek için:

   ```powershell
   Get-Service iotedge
   ```

- IOT Edge Güvenlik Yöneticisi'nin günlükleri görüntülemek için:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>IOT Edge Güvenlik Yöneticisi çalışmıyor, yaml yapılandırma dosyanızı doğrulayın.

> [!WARNING]
> YAML dosyaları girintileme olarak sekme içeremez. Bunun yerine 2 alanları kullanın.

Linux üzerinde:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows'da:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Kapsayıcı günlüklerini sorunlar için denetleyin

IOT Edge güvenlik arka plan programı çalışır duruma geçtikten sonra sorunları algılamak için kapsayıcılarının günlüklerine bakın. Dağıtılan kapsayıcılarınız ile başlayın ve ardından IoT Edge çalışma zamanını oluşturan kapsayıcılara bakın: edgeAgent ve edgeHub. IoT Edge Aracısı günlükleri genellikle her kapsayıcının yaşam döngüsü hakkında bilgi sağlar. IoT Edge hub günlükleri mesajlaşma ve yönlendirme hakkında bilgi sağlar. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>IoT Edge hub 'ından giden iletileri görüntüleme

IoT Edge hub 'ından gelen iletileri görüntüleyebilir ve çalışma zamanı kapsayıcılarından ayrıntılı günlüklerden Öngörüler toplayabilirsiniz. Bu kapsayıcıların ayrıntılı günlüklerini etkinleştirmek için ayarlanmış `RuntimeLogLevel` yaml yapılandırma dosyanızdaki. Dosyayı açmak için:

Linux üzerinde:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows'da:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Varsayılan olarak, `agent` öğesi, aşağıdaki örnekteki gibi görünür:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Değiştirin `env: {}` ile:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML dosyaları sekmeler identation içeremez. Bunun yerine 2 alanları kullanın.

Dosyayı kaydedin ve IOT Edge Güvenlik Yöneticisi'ni yeniden başlatın.

IoT Hub ile IoT Edge cihazları arasında gönderilmekte olan iletileri de denetleyebilirsiniz. Bu iletileri, Visual Studio Code için [azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) uzantısı 'nı (eskiden Azure IoT araç seti uzantısı) kullanarak görüntüleyin. Daha fazla bilgi için [Azure IOT ile geliştirme sürecinde kullanışlı araç](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Kapsayıcılar'ı yeniden başlatın
Bilgi iletilerini ve günlükleri araştırdıktan sonra kapsayıcıları yeniden başlatmayı deneyebilirsiniz:

```
iotedge restart <container name>
```

IOT Edge çalışma zamanı kapsayıcılarındaki yeniden başlatın:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>IOT Edge Güvenlik Yöneticisi'ni yeniden başlatın

Sorunu hala kalıcı yapma, IOT Edge Güvenlik Yöneticisi'ni yeniden başlatmayı deneyebilirsiniz.

Linux üzerinde:

   ```cmd
   sudo systemctl restart iotedge
   ```

Windows'da:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge aracı bir dakika sonra durdu

EdgeAgent modülü yaklaşık bir dakika boyunca başarıyla başlatılır ve çalışır ve sonra duraklar. Günlükler IoT Edge aracısının AMQP üzerinden IoT Hub bağlanmaya çalıştığı ve sonra da WebSocket üzerinden AMQP kullanarak bağlanmaya çalıştığı anlamına gelebilir. Bu başarısız olduğunda IoT Edge Aracısı çıkar. 

Örnek edgeAgent günlükleri:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

**Kök nedeni**

Konak ağındaki bir ağ yapılandırması, IoT Edge aracısının ağa ulaşmasını engellemektedir. Aracı ilk olarak AMQP (5671 numaralı bağlantı noktası) üzerinden bağlanma girişiminde bulunur. Bağlantı başarısız olursa Websockets'i (443 numaralı bağlantı noktası) dener.

IoT Edge çalışma zamanı, her bir modül için iletişim kurulacak bir ağ ayarlar. Linux’ta bu ağ bir köprü ağıdır. Windows’da NAT kullanır. Bu sorun, NAT ağını kullanan Windows kapsayıcılarının kullanıldığı Windows cihazlarında daha yaygın olarak görülür. 

**Çözümleme**

Bu köprüye/NAT ağına atanan IP adresleri için bir İnternet rotası olduğundan emin olun. Bazen konaktaki VPN yapılandırması, IoT Edge ağını geçersiz kılar. 

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub 'ı başlatılamıyor

EdgeHub modülü başlatılamıyor ve günlüklere şu iletiyi yazdırır: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

**Kök nedeni**

Konak makinedeki başka bir işlem, 443 numaralı bağlantı noktasına bağlıdır. IoT Edge hub, 5671 ve 443 bağlantı noktalarını ağ geçidi senaryolarında kullanılmak üzere eşleştirir. Başka bir işlem zaten bu bağlantı noktasına bağlıysa bu bağlantı noktası eşlemesi başarısız olur. 

**Çözümleme**

443 numaralı bağlantı noktasını kullanan işlemi bulup durdurun. Bu işlem genellikle bir web sunucusudur.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge Aracısı bir modülün görüntüsüne erişemiyor (403)
Bir kapsayıcı çalıştırılamaz ve edgeAgent günlükleri bir 403 hatası gösterir. 

**Kök nedeni**

IoT Edge aracısının bir modülün görüntüsüne erişme izni yok. 

**Çözümleme**

Kayıt defteri kimlik bilgileriniz, dağıtım bildiriminde doğru belirtildiğinden emin olun

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IOT Edge güvenlik arka plan programı, geçersiz bir ana bilgisayar adı ile başarısız oluyor

Komut `sudo journalctl -u iotedge` başarısız oluyor ve aşağıdaki iletiyi yazdırmaz: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Kök nedeni**

IOT Edge çalışma zamanı yalnızca ana bilgisayar adları 64 karakterden kısa destekleyebilir. Fiziksel makineler genellikle uzun konak adı yok, ancak bir sanal makineye daha yaygın bir sorundur. Ana bilgisayar adları, Azure'da barındırılan Windows sanal makineleri için otomatik olarak oluşturulan, özellikle uzun olma eğilimindedir. 

**Çözümleme**

Bu hatayı gördüğünüzde, sanal makinenin DNS adını yapılandırarak ve ardından Kurulum komutu, konak adı olarak DNS adını ayarlar çözebilirsiniz.

1. Azure portalında, sanal makinenizin Genel Bakış sayfasına gidin. 
2. Seçin **yapılandırma** DNS adı altında. Sanal makineniz yapılandırılan DNS adı zaten varsa, yeni bir yapılandırma gerekmez. 

   ![Sanal makinenin DNS adını yapılandırma](./media/troubleshoot/configure-dns.png)

3. İçin bir değer girin **DNS ad etiketi** seçip **Kaydet**.
4. Biçiminde olmalıdır yeni bir DNS adı kopyalamanız  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.azure.com**.
5. Sanal makinenin içinde IOT Edge çalışma zamanı, DNS adı ile ayarlamak için aşağıdaki komutu kullanın:

   - Linux üzerinde:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - Windows'da:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Kaynaktaki kararlılık sorunlarını cihazları kısıtlı 
Özellikle, ağ geçidi olarak kullanıldığında Raspberry Pi gibi kısıtlı cihazlarda kararlılık sorunlarla karşılaşabilirsiniz. Edge hub'ı modülü bellek durumlar dışında belirtileri içerir, aşağı akış cihazları bağlanılamıyor veya cihazın telemetri gönderme birkaç saat sonra durdurur.

**Kök nedeni**

IoT Edge çalışma zamanının parçası olan IoT Edge hub, varsayılan olarak performans için en iyi duruma getirilmiştir ve büyük bellek öbeklerini ayırmaya çalışır. Bu iyileştirme kısıtlanmış edge cihazlar için ideal değildir ve kararlılık sorunlara neden olabilir.

**Çözümleme**

IoT Edge hub 'ı için **Optimizeforperformance** ortam değişkenini **false**olarak ayarlayın. Bunu yapmanın iki yolu vardır:

Kullanıcı Arabiriminde: 

Portalda **cihaz ayrıntıları** > **kümesi modülleri** > **Gelişmiş uç çalışma zamanı ayarlarını yapılandırın**' a gidin. Edge hub modülü için, *false*olarak ayarlanan *Optimizeforperformance* adlı bir ortam değişkeni oluşturun.

![OptimizeForPerformance false olarak ayarlayın.](./media/troubleshoot/optimizeforperformance-false.png)

**VEYA**

Dağıtım bildirimi içinde:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>IOT Edge üzerinde Windows arka plan programının günlüklerini alınamıyor
Kullanırken bir EventLogException alırsanız `Get-WinEvent` Windows üzerinde kayıt defteri girişlerini denetleyin.

**Kök nedeni**

`Get-WinEvent` PowerShell komutu tarafından belirli bir günlükleri bulmak için mevcut olması için bir kayıt defteri girişi dayanır `ProviderName`.

**Çözümleme**

IOT Edge arka plan programı bir kayıt defteri anahtarını ayarlayın. Oluşturma bir **iotedge.reg** dosya aşağıdaki içeriğe ve alma Windows kayıt defterine çift veya kullanarak `reg import iotedge.reg` komutu:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IOT Edge modülü ileti göndermek için edgeHub 404 hatası ile başarısız olur.

IOT Edge modülü başarısız bir 404 ile edgeHub bir ileti göndermek için özel bir `Module not found` hata. IOT Edge arka plan programı günlüklere şu iletiyi yazdırmaz: 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

**Kök nedeni**

IOT Edge arka plan programı, güvenlik nedenleriyle edgeHub bağlanan tüm modüller için işlem kimliği zorlar. Bir modül tarafından gönderilen tüm iletiler modülü ana işlem Kimliğinden geldiğini doğrular. Başlangıçta kurulan olandan farklı bir işlem Kimliğinden modülü tarafından ileti gönderiliyor ise bir 404 hatası ileti iletinin reddeder.

**Çözümleme**

Sürüm 1.0.7 itibariyle, tüm modül işlemlerinin bağlanmasına izin verilir. 1\.0.7 sürümüne yükseltme yapılamıyorsa, aşağıdaki adımları izleyin. Daha fazla bilgi için bkz. [1.0.7 Release changelog](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Aynı işlem kimliği her zaman özel bir IOT Edge modülü tarafından ileti göndermek için edgeHub için kullanılmadığından emin olun. Örneğin, `ENTRYPOINT` Docker dosyanızda `CMD` komutunu kullanın, çünkü `CMD` modül için bir işlem kimliği ve ana programı `ENTRYPOINT` çalıştıran Bash komutu için başka bir işlem kimliği, bir tek işlem KIMLIĞI.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>IOT Edge dağıtımı için güvenlik duvarı ve bağlantı noktası yapılandırma kuralları
Azure IoT Edge, desteklenen IoT Hub protokolleri kullanılarak şirket içi bir sunucudan Azure bulutuna iletişimin iletişimine izin veriyor, bkz. [iletişim protokolü seçme](../iot-hub/iot-hub-devguide-protocols.md). Gelişmiş güvenlik için Azure IOT Edge ile Azure IOT Hub arasındaki iletişim kanallarını her zaman giden olacak şekilde yapılandırılır. Bu yapılandırma dayanır [hizmet destekli iletişim düzeni](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), keşfetmek kötü amaçlı bir varlık için saldırı yüzeyini en aza. Gelen iletişim istekleri, yalnızca Azure IOT Edge cihazına iletileri göndermek için Azure IOT hub'ı gereken yere belirli senaryolar için gereklidir. Bulut-cihaz iletilerini TLS güvenli kanalı kullanılarak korunur ve daha fazla X.509 sertifikaları ve TPM cihaz modülleri kullanılarak güvenli hale getirilebilir. Azure IOT Edge Güvenlik Yöneticisi bu iletişimin nasıl olabileceğini yöneten kurulduktan bkz [IOT Edge Güvenlik Yöneticisi](../iot-edge/iot-edge-security-manager.md).

IOT Edge modülleri dağıtılır ve Azure IOT Edge çalışma zamanı güvenliğini sağlamak için Gelişmiş yapılandırma sağlar, ancak temel alınan makine ve ağ yapılandırmasına yine de bağlıdır. Bu nedenle, güvenli uç için bulut iletişimine doğru ağ ve Güvenlik Duvarı kurallarının ayarlandığından emin olmanız zorunludur. Aşağıdaki tablo, Azure IoT Edge çalışma zamanının barındırıldığı temel sunucular için yapılandırma güvenlik duvarı kuralları olduğunda bir kılavuz olarak kullanılabilir:

|Protocol|Bağlantı noktası|gelen|Giden|Rehber|
|--|--|--|--|--|
|MQTT|8883|ENGELLENEN (varsayılan)|ENGELLENEN (varsayılan)|<ul> <li>Giden (giden) MQTT iletişim protokolü olarak kullanılırken açık olacak şekilde yapılandırın.<li>MQTT için 1883 IOT Edge tarafından desteklenmiyor. <li>Gelen (gelen) bağlantıları engellenmesi gerekir.</ul>|
|AMQP|5671|ENGELLENEN (varsayılan)|Açık (varsayılan)|<ul> <li>IOT Edge için varsayılan iletişim protokolü. <li> Azure IOT Edge, diğer Desteklenen protokoller için yapılandırılmadı veya AMQP istenen iletişim protokolü açık olarak yapılandırılmış olmalıdır.<li>AMQP için 5672, IOT Edge tarafından desteklenmiyor.<li>Azure IOT Edge kullanan farklı bir IOT Hub protokol desteklendiğinde, bu bağlantı noktası engelleyin.<li>Gelen (gelen) bağlantıları engellenmesi gerekir.</ul></ul>|
|HTTPS|443|ENGELLENEN (varsayılan)|Açık (varsayılan)|<ul> <li>Giden (giden) açık olmasını sağlama IOT Edge için 443 numaralı yapılandırın. Bu yapılandırma, el ile komut dosyaları veya Azure IOT cihaz sağlama hizmeti (DPS) kullanılırken gereklidir. <li>Gelen (gelen) bağlantının açık olmalıdır yalnızca belirli senaryoları için: <ul> <li>  Yöntem isteği gönderebilir yaprak cihazlar ile saydam bir ağ geçidi varsa. Bu durumda, bağlantı noktası 443'ü dış ağlara bağlanmak için IoTHub ya da Azure IOT Edge üzerinden IoTHub hizmetleri sağlamak için açık olması gerekmez. Bu nedenle gelen kuralı yalnızca iç ağdan gelen (gelen) açmak için kısıtlı olabilir. <li> Cihaz (C2D) senaryoları için daha fazla istemci için.</ul><li>HTTP için 80, IOT Edge tarafından desteklenmiyor.<li>HTTP olmayan protokolleri (örneğin, AMQP veya MQTT), Kurumsal yapılandırılamıyorsa; iletileri WebSockets üzerinden gönderilebilir. 443 numaralı bağlantı noktası için iletişim WebSocket durumlarda kullanılır.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge Aracısı modülü sürekli olarak ' boş yapılandırma dosyası ' bildiriyor ve cihazda hiçbir modül başlamıyor

Cihazda dağıtımda tanımlanan modüller başlatılırken sorun yaşanıyor. Yalnızca edgeAgent çalışıyor ancak sürekli olarak ' boş yapılandırma dosyası... ' bildiriyor.

**Kök nedeni**

Varsayılan olarak, IoT Edge kendi yalıtılmış kapsayıcı ağında modüller başlatır. Bu özel ağ içinde, bu cihaz DNS ad çözümlemesi ile ilgili sorun yaşıyor olabilir.

**Çözümleme**


**Seçenek 1: Kapsayıcı altyapısı ayarlarındaki DNS sunucusunu ayarla**

Altyapı tarafından başlatılan tüm kapsayıcı modülleri için uygulanacak kapsayıcı altyapısı ayarlarında ortamınız için DNS sunucusu belirtin. Kullanılacak DNS sunucusunu belirten `daemon.json` adlı bir dosya oluşturun. Örneğin:

```
{
    "dns": ["1.1.1.1"]
}
```

Yukarıdaki örnek, DNS sunucusunu genel olarak erişilebilen bir DNS hizmetine ayarlar. Sınır cihazı bu IP ortamından erişemezse, erişilebilir olan DNS sunucusu adresi ile değiştirin.

Platformunuzun doğru konumunu yerleştirin `daemon.json` : 

| Platform | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows kapsayıcıları ile Windows ana bilgisayarı | `C:\ProgramData\iotedge-moby\config` |

Konum zaten dosya içeriyorsa `daemon.json` , **DNS** anahtarını buna ekleyin ve dosyayı kaydedin.

*Güncelleştirmelerin etkili olabilmesi için kapsayıcı altyapısını yeniden başlatın*

| Platform | Komut |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (yönetici PowerShell) | `Restart-Service iotedge-moby -Force` |

**Seçenek 2: Modül başına IoT Edge dağıtımında DNS sunucusunu ayarla**

Her modülün *createOptions* için DNS sunucusunu IoT Edge dağıtımında ayarlayabilirsiniz. Örneğin:

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Bunu *Edgeagent* ve *edgehub* modülleri için de ayarladığınızdan emin olun. 

## <a name="next-steps"></a>Sonraki adımlar
IoT Edge platformunda bir hata bulduğunuzu düşünüyor musunuz? [Sorun bildir](https://github.com/Azure/iotedge/issues) böylece biz geliştirmeye devam. 

Başka sorularım varsa, oluşturun bir [destek isteği](https://portal.azure.com/#create/Microsoft.Support) Yardım. 

