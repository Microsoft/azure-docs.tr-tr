---
title: Ağ Ara sunucuları - Azure IOT Edge cihazları yapılandırma | Microsoft Docs
description: Azure IOT Edge çalışma zamanı ve bir proxy sunucu üzerinden iletişim kurmak için herhangi bir internet'e yönelik IOT Edge modüllerini nasıl yapılandırılacağı.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 47d3018015d05d0587e841c216a5eb89f2a0ae20
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414556"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Bir proxy sunucu üzerinden iletişim kurmak için IOT Edge cihazı yapılandırma

IOT Edge cihazları, IOT Hub ile iletişim kurmak için HTTPS isteği gönderir. Cihazınızı bir ara sunucu kullanıyorsa bir ağa bağlıysa, sunucu üzerinden iletişim kurmak için IOT Edge çalışma zamanı'nı yapılandırmanız gerekir. Proxy sunucular Ayrıca, IoT Edge hub 'ı üzerinden yönlendirilmeyen HTTP veya HTTPS isteklerini IoT Edge bağımsız modülleri etkileyebilir. 

Bu makalede, bir proxy sunucusunun arkasında bir IoT Edge cihazını yapılandırmak ve yönetmek için aşağıdaki dört adım izlenecek yol gösterilmektedir: 

1. **IoT Edge çalışma zamanını cihazınıza yükler.**

   IoT Edge yükleme betikleri, paketleri ve dosyaları internet 'ten çeker, böylece cihazınızın bu istekleri yapması için proxy sunucusu üzerinden iletişim kurması gerekir. Ayrıntılı adımlar için, bu makalenin [bir ara sunucu aracılığıyla çalışma zamanını Install](#install-the-runtime-through-a-proxy) bölümüne bakın. Windows cihazlarında, yükleme betiği de [çevrimdışı bir yükleme](how-to-install-iot-edge-windows.md#offline-installation) seçeneği sağlar. 

   Bu adım, ilk kez ayarladığınızda IoT Edge cihazında gerçekleştirilen tek seferlik bir işlemdir. IoT Edge çalışma zamanını güncelleştirdiğinizde aynı bağlantılar da gereklidir. 

2. **Cihazınızda Docker Daemon 'u ve IoT Edge arka plan programını yapılandırın.**

   IoT Edge, cihazda iki Daemon 'ları kullanır ve bunların her ikisi de proxy sunucusu üzerinden Web istekleri yapması gerekir. IoT Edge Daemon, IoT Hub iletişiminden sorumludur. Moby arka plan programı kapsayıcı yönetiminden sorumludur, bu nedenle kapsayıcı kayıt defterleri ile iletişim kurar. Ayrıntılı adımlar için, bu makalenin [Daemon 'ları yapılandırma](#configure-the-daemons) bölümüne bakın. 

   Bu adım, ilk kez ayarladığınızda IoT Edge cihazında gerçekleştirilen tek seferlik bir işlemdir.

3. **Cihazınızdaki config. YAML dosyasında IoT Edge Aracısı özelliklerini yapılandırın.**

   IoT Edge Daemon, başlangıçta edgeAgent modülünü başlatır, ancak edgeAgent modülü, IoT Hub ' dan dağıtım bildirimini almaktan ve diğer tüm modüllerin başlatılmasına karşı sorumludur. IoT Edge aracısının ilk bağlantıyı IoT Hub hale getirmek için, edgeAgent modülü ortam değişkenlerini cihazın kendisinde el ile yapılandırın. İlk bağlantıdan sonra, edgeAgent modülünü uzaktan yapılandırabilirsiniz. Ayrıntılı adımlar için, bu makalenin [IoT Edge aracısını yapılandırma](#configure-the-iot-edge-agent) bölümüne bakın.

   Bu adım, ilk kez ayarladığınızda IoT Edge cihazında gerçekleştirilen tek seferlik bir işlemdir.

4. **Tüm gelecek modül dağıtımları için, proxy üzerinden iletişim kuran herhangi bir modülün ortam değişkenlerini ayarlayın.**

   IoT Edge cihazınız kurulduktan ve proxy sunucusu üzerinden IoT Hub bağlandıktan sonra, bağlantıyı gelecekteki tüm modül dağıtımlarında korumanız gerekir. Ayrıntılı adımlar için, bu makalenin [dağıtım bildirimlerini yapılandırma](#configure-deployment-manifests) bölümüne bakın. 

   Bu adım, her yeni modül veya dağıtım güncelleştirmesinin, cihazın ara sunucu üzerinden iletişim kurma yeteneğini sakladığı şekilde uzaktan gerçekleştirilen bir işlemdir. 

## <a name="know-your-proxy-url"></a>Proxy URL'nizi bildirin

Bu makaledeki adımlardan herhangi birine başlamadan önce, proxy URL 'nizi bilmeniz gerekir.

Ara sunucu URL'leri şu biçimde olması: **Protokolü**://**proxy_host**:**proxy_port**.

* **Protokolü** HTTP veya HTTPS. Docker Daemon, kapsayıcı kayıt defteri ayarlarınıza bağlı olarak her iki protokolü de kullanabilir, ancak IoT Edge Daemon ve Runtime kapsayıcıları, proxy 'ye bağlanmak için her zaman HTTP 'yi kullanmalıdır.

* **Proxy_host** bir proxy sunucusunun adresidir. Proxy sunucunuz kimlik doğrulaması gerektiriyorsa, kimlik bilgilerinizi proxy konağının bir parçası olarak aşağıdaki biçimde sağlayabilirsiniz: **User**:**Password**\@**proxy_host**.

* **Proxy_port** proxy ağ trafiğini yanıt ağ bağlantı noktası.

## <a name="install-the-runtime-through-a-proxy"></a>Çalışma zamanını bir ara sunucu aracılığıyla yükler

IoT Edge cihazınızın Windows veya Linux üzerinde çalışıp çalışmadığını, yükleme paketlerine proxy sunucusu üzerinden erişmeniz gerekir. İşletim sisteminize bağlı olarak, IoT Edge çalışma zamanını bir ara sunucuya yüklemek için adımları izleyin. 

### <a name="linux"></a>Linux

Bir Linux cihaza IOT Edge çalışma zamanı'nı yüklüyorsanız, yükleme paketi erişmek için Ara sunucu üzerinden gitmesi için Paket Yöneticisi'ni yapılandırın. Örneğin, [apt-get bir http Ara sunucusunu kullanacak şekilde](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Paket yöneticiniz yapılandırıldıktan sonra, [Linux üzerinde Azure IoT Edge Runtime 'ı](how-to-install-iot-edge-linux.md) her zamanki gibi Install ' daki yönergeleri izleyin.

### <a name="windows"></a>Windows

IoT Edge çalışma zamanını bir Windows cihazına yüklüyorsanız, proxy sunucusu üzerinden iki kez gitmeniz gerekir. İlk bağlantı yükleyici betik dosyasını indirir ve ikinci bağlantı yükleme sırasında gerekli bileşenleri indirmek için kullanılır. Windows ayarları 'nda ara sunucu bilgilerini yapılandırabilir veya proxy bilgilerinizi doğrudan PowerShell komutlarına dahil edebilirsiniz. 

Aşağıdaki adımlarda, `-proxy` bağımsız değişkenini kullanarak bir Windows yüklemesinin örneği gösterilmektedir:

1. Invoke-WebRequest komutu, yükleyici betiğine erişmek için ara sunucu bilgilerine sahip olmalıdır. Ardından, dağıtım-ıotedge komutunun yükleme dosyalarını indirmesi için proxy bilgilerine ihtiyacı vardır. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Initialize-ıotedge komutunun ara sunucu üzerinden gitmesi gerekmez, bu nedenle ikinci adım yalnızca Invoke-WebRequest için proxy bilgileri gerektirir.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Proxy sunucusu için URL 'ye dahil olmayan karmaşık kimlik bilgileriniz varsa, içindeki `-ProxyCredential` `-InvokeWebRequestParameters`parametresini kullanın. Örneğin,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Ara sunucu parametreleri hakkında daha fazla bilgi için bkz. [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Çevrimdışı yükleme dahil Windows yükleme seçenekleri hakkında daha fazla bilgi için bkz. [Windows üzerinde Azure IoT Edge çalışma zamanı yükleme](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Daemon'ları yapılandırma

IoT Edge, IoT Edge cihazında çalışan iki Daemon 'ları dayanır. Moby cini, Web isteklerini kapsayıcı kayıt defterlerinden kapsayıcı görüntüleri çekmek için yapar. IOT Edge arka plan programı, IOT Hub ile iletişim kurmak için web isteği yapar.

Hem Moby hem de IoT Edge Daemon 'ları, devam eden cihaz işlevselliği için proxy sunucusunu kullanacak şekilde yapılandırılmalıdır. Bu adım ilk cihaz kurulumu sırasında IoT Edge cihazında gerçekleşir. 

### <a name="moby-daemon"></a>Moby Daemon

Moby Docker üzerine inşa edildiğinden, Moby cini ortam değişkenleriyle yapılandırmak için Docker belgelerine bakın. Çoğu kapsayıcı kayıt defterleri (DockerHub ve Azure kapsayıcı kayıt defterleri dahil) ayarlamalısınız parametredir HTTPS istekleri desteklediğimizden **erişmek**. Aktarım Katmanı Güvenliği (TLS) desteklemeyen bir kayıt defterinden görüntüleri çekmek sonra ayarlamalısınız **HTTP_PROXY** parametresi. 

IoT Edge cihaz işletim sisteminiz için geçerli olan makaleyi seçin: 

* [Linux 'ta Docker Daemon 'ı yapılandırma](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Linux cihazlarındaki Moby Daemon, Docker adını tutar.
* [Windows 'da Docker Daemon yapılandırma](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Windows cihazlarındaki Moby Daemon, ıotedge-Moby olarak adlandırılır. Bir Windows cihazında hem Docker Desktop hem de Moby paralel olarak çalıştırılabilmesini sağladığından adlar farklıdır. 

### <a name="iot-edge-daemon"></a>IOT Edge arka plan programı

IoT Edge Daemon, Moby cinine benzer bir şekilde yapılandırılmıştır. İşletim sisteminize bağlı hizmeti için bir ortam değişkenini ayarlamak için aşağıdaki adımları kullanın. 

IoT Edge Daemon, istekleri IoT Hub göndermek için her zaman HTTPS kullanır.

#### <a name="linux"></a>Linux

IOT Edge daemon'ı yapılandırmak için terminalde Düzenleyicisi. 

```bash
sudo systemctl edit iotedge
```

Aşağıdaki metni girin değiştirerek  **\<proxy URL'si >** proxy sunucu adresi ve bağlantı noktası. Ardından, kaydedin ve çıkın. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

IoT Edge için yeni yapılandırmayı seçmek üzere Service Manager 'ı yenileyin.

```bash
sudo systemctl daemon-reload
```

IOT Edge değişikliklerin etkili olması için yeniden başlatın.

```bash
sudo systemctl restart iotedge
```

Ortam değişkeninize oluşturuldu ve yeni yapılandırmayı yüklenip yüklenmediğini doğrulayın. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Yönetici olarak bir PowerShell penceresi açın ve yeni ortam değişkeniyle kayıt defterini düzenlemek için aşağıdaki komutu çalıştırın. Değiştirin  **\<proxy URL'si >** proxy sunucu adresi ve bağlantı noktası. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

IOT Edge değişikliklerin etkili olması için yeniden başlatın.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>IoT Edge aracısını yapılandırma

IoT Edge Aracısı, herhangi bir IoT Edge cihazında başlatılacak ilk modüldür. IOT Edge config.yaml dosyanın içindeki bilgileri temel alan ilk kez başlatılır. IoT Edge Aracısı daha sonra dağıtım bildirimlerini almak için IoT Hub bağlanır, bu da cihaza hangi modüllerin dağıtılması gerektiğini bildirir.

Bu adım ilk cihaz kurulumu sırasında IoT Edge cihaza bir kez gerçekleşir. 

1. IOT Edge Cihazınızda config.yaml dosyasını açın. Linux sistemlerinde, bu dosya şu konumdadır **/etc/iotedge/config.yaml**. Windows sistemlerde, bu dosya şu konumdadır **C:\ProgramData\iotedge\config.yaml**. Yapılandırma dosyası korunur ve ona erişmek için yönetici ayrıcalıkları gerekir. Linux sistemlerinde, dosyayı tercih ettiğiniz `sudo` metin düzenleyicisinde açmadan önce komutunu kullanın. Windows 'ta, Not Defteri gibi bir metin düzenleyicisini yönetici olarak açın ve dosyayı açın. 

2. Config.yaml dosyasında **Edge Aracısı modülü spec** bölümü. IoT Edge Aracısı tanımı, ortam değişkenleri ekleyebileceğiniz bir **env** parametresi içerir. 

3. Env parametresi için yer tutucular ve yeni bir satıra yeni bir değişken ekleyin, küme parantezleri kaldırın. YAML içinde girintileri iki boşluk olduğunu unutmayın. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. IOT Edge çalışma zamanı, IOT Hub'kurmak, varsayılan olarak AMQP kullanır. Bazı Ara sunucuları AMQP bağlantı noktalarını engelleyin. Bu durumda, daha sonra ayrıca edgeAgent WebSocket üzerinden AMQP kullanmak için yapılandırmanız gerekir. İkinci bir ortam değişkenine ekleyin.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![ortam değişkenleriyle edgeAgent tanımı](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Config.yaml için değişiklikleri kaydedin ve düzenleyiciyi kapatın. IOT Edge değişikliklerin etkili olması için yeniden başlatın. 

   * Linux: 

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Dağıtım bildirimleri yapılandırma  

IoT Edge cihazınız ara sunucu ile çalışacak şekilde yapılandırıldıktan sonra, gelecekteki dağıtım bildirimlerinde ortam değişkenlerini bildirmeye devam etmeniz gerekir. Azure portal sihirbazını kullanarak veya bir dağıtım bildirimi JSON dosyasını düzenleyerek dağıtım bildirimlerini düzenleyebilirsiniz. 

İki çalışma zamanı modülünü, edgeAgent ve edgeHub 'ı her zaman, IoT Hub ile bir bağlantı sürdürmek için proxy sunucu üzerinden iletişim kurmak üzere yapılandırın. EdgeAgent modülünden proxy bilgilerini kaldırırsanız, bağlantıyı yeniden kurmak için tek yol, önceki bölümde açıklandığı gibi, cihazdaki config. YAML dosyasını düzenlemedir. 

İnternet 'e bağlanan diğer IoT Edge modülleri de ara sunucu üzerinden iletişim kuracak şekilde yapılandırılmalıdır. Ancak, iletilerini edgeHub aracılığıyla yönlendiren veya yalnızca cihazdaki diğer modüllerle iletişim kuran modüller ara sunucu ayrıntılarına gerek kalmaz. 

Bu adım IoT Edge cihazının ömrü boyunca devam etmektedir. 

### <a name="azure-portal"></a>Azure portal

Kullanırken **modülleri ayarlama** cihazları IOT Edge dağıtımlarını oluşturmak için Sihirbazı her bir modüle sahip bir **ortam değişkenlerini** proxy sunucu bağlantılarını yapılandırmak için kullanabileceğiniz bir bölüm. 

IoT Edge Aracısı ve IoT Edge hub modüllerini yapılandırmak için sihirbazın ilk adımında **Gelişmiş Edge çalışma zamanı ayarlarını yapılandır** ' ı seçin. 

![Gelişmiş Edge Çalışma Zamanı ayarlarını yapılandırma](./media/how-to-configure-proxy-support/configure-runtime.png)

**Https_proxy** ortam değişkenini hem IoT Edge Aracısı hem de IoT Edge hub modülünün tanımına ekleyin. IoT Edge cihazınızdaki config. YAML dosyasına **Upstreamprotocol** ortam değişkenini eklediyseniz, bunu IoT Edge aracı modül tanımına da ekleyin. 

![Erişmek ortam değişken Ayarla](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Bir dağıtım bildirimine eklediğiniz tüm diğer modülleri aynı düzeni uygular. Modül adı ve görüntü ayarlandığı sayfasında, bir ortam değişkenleri bölümü yoktur.

### <a name="json-deployment-manifest-files"></a>JSON dağıtım bildirim dosyaları

Visual Studio code'da veya JSON dosyalarını el ile oluşturarak şablonları kullanarak cihazları IOT Edge dağıtımlarını oluşturursanız, doğrudan her modül tanımı için ortam değişkenlerini ekleyebilirsiniz. 

Aşağıdaki JSON biçimi kullanın: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Ortam değişkenleri dahil, modül tanımınızı edgeHub aşağıdaki örnekteki gibi görünmelidir:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

IoT Edge cihazınızdaki confige. YAML dosyasına **Upstreamprotocol** ortam değişkenini eklediyseniz, bunu IoT Edge aracı modül tanımına da ekleyin. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Rolleri hakkında daha fazla bilgi [IOT Edge çalışma zamanı](iot-edge-runtime.md).

Yükleme ve yapılandırma hataları ile ilgili sorunları giderme [genel sorunlar ve çözümleri için Azure IOT Edge](troubleshoot.md)

