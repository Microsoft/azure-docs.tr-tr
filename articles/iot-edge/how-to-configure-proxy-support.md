---
title: Cihazları ağ proxy 'leri için Yapılandırma-Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge çalışma zamanını ve internet 'e yönelik IoT Edge modüllerini bir ara sunucu üzerinden iletişim kuracak şekilde yapılandırma.
author: kgremban
ms.author: kgremban
ms.date: 09/03/2020
ms.topic: how-to
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- contperf-fy21q1
ms.openlocfilehash: 9f2ca089a6d885227bd61940d71ec7bb7960fbd6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043164"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>IoT Edge cihazını ara sunucu üzerinden iletişim kuracak şekilde yapılandırma

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge cihazlar, IoT Hub iletişim kurmak için HTTPS istekleri gönderir. Cihazınız bir ara sunucu kullanan bir ağa bağlıysa, IoT Edge çalışma zamanını sunucu üzerinden iletişim kuracak şekilde yapılandırmanız gerekir. Proxy sunucular Ayrıca, IoT Edge hub 'ı üzerinden yönlendirilmeyen HTTP veya HTTPS isteklerini IoT Edge bağımsız modülleri etkileyebilir.

Bu makalede, bir proxy sunucusunun arkasında bir IoT Edge cihazını yapılandırmak ve yönetmek için aşağıdaki dört adım izlenecek yol gösterilmektedir:

1. [**IoT Edge çalışma zamanını cihazınıza yüklemesi**](#install-iot-edge-through-a-proxy)

   IoT Edge yükleme betikleri, paketleri ve dosyaları internet 'ten çeker, böylece cihazınızın bu istekleri yapması için proxy sunucusu üzerinden iletişim kurması gerekir. Windows cihazlarında, yükleme betiği de çevrimdışı bir yükleme seçeneği sağlar.

   Bu adım, ilk kez ayarladığınızda IoT Edge cihazı yapılandırmak için tek seferlik bir işlemdir. IoT Edge çalışma zamanını güncelleştirdiğinizde aynı bağlantılar da gereklidir.

2. [**Cihazınızda IoT Edge ve kapsayıcı çalışma zamanını yapılandırın**](#configure-iot-edge-and-moby)

   IoT Edge IoT Hub iletişiminden sorumludur. Kapsayıcı çalışma zamanı, kapsayıcı yönetiminden sorumludur, bu nedenle kapsayıcı kayıt defterleri ile iletişim kurar. Bu bileşenlerin her ikisinin de proxy sunucu üzerinden Web istekleri yapması gerekir.

   Bu adım, ilk kez ayarladığınızda IoT Edge cihazı yapılandırmak için tek seferlik bir işlemdir.

3. [**Cihazınızdaki yapılandırma dosyasında IoT Edge Aracısı özelliklerini yapılandırın**](#configure-the-iot-edge-agent)

   IoT Edge Daemon, başlangıçta edgeAgent modülünü başlatır. Ardından, edgeAgent modülü IoT Hub dağıtım bildirimini alır ve diğer tüm modülleri başlatır. IoT Edge aracısının ilk bağlantıyı IoT Hub hale getirmek için, edgeAgent modülü ortam değişkenlerini cihazın kendisinde el ile yapılandırın. İlk bağlantıdan sonra, edgeAgent modülünü uzaktan yapılandırabilirsiniz.

   Bu adım, ilk kez ayarladığınızda IoT Edge cihazı yapılandırmak için tek seferlik bir işlemdir.

4. [**Tüm gelecek modül dağıtımları için, ara sunucu üzerinden iletişim kuran herhangi bir modülün ortam değişkenlerini ayarlayın**](#configure-deployment-manifests)

   IoT Edge cihazınız kurulduktan ve proxy sunucusu üzerinden IoT Hub bağlandıktan sonra, bağlantıyı gelecekteki tüm modül dağıtımlarında korumanız gerekir.

   Bu adım, her yeni modül veya dağıtım güncelleştirmesinin, cihazın ara sunucu üzerinden iletişim kurma yeteneğini sakladığı şekilde uzaktan yapılan bir işlemdir.

## <a name="know-your-proxy-url"></a>Proxy URL 'nizi öğrenin

Bu makaledeki adımlardan herhangi birine başlamadan önce, proxy URL 'nizi bilmeniz gerekir.

Proxy URL 'Leri şu biçimi alır: **protokol**://**proxy_host**:**proxy_port**.

* **Protokol** http ya da https olur. Docker Daemon, kapsayıcı kayıt defteri ayarlarınıza bağlı olarak her iki protokolü de kullanabilir, ancak IoT Edge Daemon ve Runtime kapsayıcıları, proxy 'ye bağlanmak için her zaman HTTP 'yi kullanmalıdır.

* **Proxy_host** , proxy sunucu için bir adrestir. Proxy sunucunuz kimlik doğrulaması gerektiriyorsa, kimlik bilgilerinizi proxy konağının bir parçası olarak aşağıdaki biçimde sağlayabilirsiniz: **User**:**Password** \@ **proxy_host**.

* **Proxy_port** , proxy 'nin ağ trafiğine verdiği ağ bağlantı noktasıdır.

## <a name="install-iot-edge-through-a-proxy"></a>Proxy üzerinden IoT Edge yüklemesi

IoT Edge cihazınızın Windows veya Linux üzerinde çalışıp çalışmadığını, yükleme paketlerine proxy sunucusu üzerinden erişmeniz gerekir. İşletim sisteminize bağlı olarak, IoT Edge çalışma zamanını bir ara sunucuya yüklemek için adımları izleyin.

### <a name="linux-devices"></a>Linux cihazları

IoT Edge çalışma zamanını bir Linux cihazına yüklüyorsanız, paket yöneticisini, yükleme paketine erişmek için proxy sunucunuzun üzerinden gezinmek üzere yapılandırın. Örneğin, [bir http-proxy kullanmak için apt-get](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy)' i ayarlayın. Paket yöneticiniz yapılandırıldıktan sonra her zamanki gibi [Azure IoT Edge çalışma zamanını Install](how-to-install-iot-edge.md) ' daki yönergeleri izleyin.

### <a name="windows-devices-using-iot-edge-for-linux-on-windows"></a>Windows 'da Linux için IoT Edge kullanan Windows cihazları

Windows üzerinde Linux için IoT Edge kullanarak IoT Edge çalışma zamanını yüklüyorsanız, Linux sanal makinenizde varsayılan olarak IoT Edge yüklenir. Ek yükleme veya güncelleştirme adımı gerekmez.

### <a name="windows-devices-using-windows-containers"></a>Windows kapsayıcıları kullanan Windows cihazları

IoT Edge çalışma zamanını bir Windows cihazına yüklüyorsanız, proxy sunucusu üzerinden iki kez gitmeniz gerekir. İlk bağlantı yükleyici betik dosyasını indirir ve ikinci bağlantı yükleme sırasında gerekli bileşenleri indirmek için kullanılır. Windows ayarları 'nda ara sunucu bilgilerini yapılandırabilir veya proxy bilgilerinizi doğrudan PowerShell komutlarına dahil edebilirsiniz.

Aşağıdaki adımlarda, bağımsız değişkenini kullanarak bir Windows yüklemesinin örneği gösterilmektedir `-proxy` :

1. Invoke-WebRequest komutu, yükleyici betiğine erişmek için proxy bilgilerine ihtiyaç duyuyor. Ardından Deploy-IoTEdge komutu, yükleme dosyalarını indirmek için proxy bilgilerine ihtiyaç duyuyor.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Initialize-IoTEdge komutun ara sunucu üzerinden gitmesi gerekmez, bu nedenle ikinci adım yalnızca Invoke-WebRequest için proxy bilgileri gerektirir.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Proxy sunucusu için URL 'ye dahil olmayan karmaşık kimlik bilgileriniz varsa, `-ProxyCredential` içindeki parametresini kullanın `-InvokeWebRequestParameters` . Örneğin,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Ara sunucu parametreleri hakkında daha fazla bilgi için bkz. [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest). Windows yükleme parametreleri hakkında daha fazla bilgi için bkz. [Windows üzerinde IoT Edge Için PowerShell betikleri](reference-windows-scripts.md).

## <a name="configure-iot-edge-and-moby"></a>IoT Edge ve Moby 'yi yapılandırma

IoT Edge, IoT Edge cihazında çalışan iki Daemon 'ları dayanır. Moby cini, Web isteklerini kapsayıcı kayıt defterlerinden kapsayıcı görüntüleri çekmek için yapar. IoT Edge Daemon, Web isteklerinin IoT Hub iletişim kurmasını sağlar.

Hem Moby hem de IoT Edge Daemon 'ları, devam eden cihaz işlevselliği için proxy sunucusunu kullanacak şekilde yapılandırılmalıdır. Bu adım ilk cihaz kurulumu sırasında IoT Edge cihazında gerçekleşir.

### <a name="moby-daemon"></a>Moby Daemon

Moby Docker üzerine inşa edildiğinden, Moby cini ortam değişkenleriyle yapılandırmak için Docker belgelerine bakın. Çoğu kapsayıcı kayıt defterleri (DockerHub ve Azure Container Registry 'ler dahil) HTTPS isteklerini destekler, bu nedenle ayarlamanız gereken parametre **https_proxy**. Aktarım Katmanı Güvenliği 'ni (TLS) desteklemeyen bir kayıt defterindeki görüntüleri çekyorsanız, **http_proxy** parametresini ayarlamanız gerekir.

IoT Edge cihaz işletim sisteminiz için geçerli olan makaleyi seçin:

* [Linux 'Ta Docker Daemon 'ı yapılandırma](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Linux cihazlarındaki Moby Daemon, Docker adını tutar.
* [Windows 'Da Docker Daemon yapılandırma](/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Windows cihazlarındaki Moby Daemon, ıotedge-Moby olarak adlandırılır. Bir Windows cihazında hem Docker Desktop hem de Moby paralel olarak çalıştırılabilmesini sağladığından adlar farklıdır.

### <a name="iot-edge-daemon"></a>IoT Edge Daemon

IoT Edge Daemon, Moby cinine benzer bir şekilde yapılandırılmıştır. İşletim sisteminize bağlı olarak hizmet için bir ortam değişkeni ayarlamak için aşağıdaki adımları kullanın.

IoT Edge Daemon, istekleri IoT Hub göndermek için her zaman HTTPS kullanır.

#### <a name="linux"></a>Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge Daemon 'ı yapılandırmak için terminalde bir düzenleyici açın.

```bash
sudo systemctl edit iotedge
```

Aşağıdaki metni girerek **\<proxy URL>** proxy sunucu adresiniz ve bağlantı noktasıyla değiştirin. Sonra Kaydet ve çık.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

IoT Edge için yeni yapılandırmayı seçmek üzere Service Manager 'ı yenileyin.

```bash
sudo systemctl daemon-reload
```

Değişikliklerin etkili olması için IoT Edge yeniden başlatın.

```bash
sudo systemctl restart iotedge
```

Ortam değişkeninizin oluşturulduğunu ve yeni yapılandırmanın yüklendiğini doğrulayın.

```bash
systemctl show --property=Environment iotedge
```
:::moniker-end
<!--end 1.1-->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge Daemon 'ı yapılandırmak için terminalde bir düzenleyici açın.

```bash
sudo systemctl edit aziot-edged
```

Aşağıdaki metni girerek **\<proxy URL>** proxy sunucu adresiniz ve bağlantı noktasıyla değiştirin. Sonra Kaydet ve çık.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Sürüm 1,2 ' den başlayarak, IoT Edge IoT Hub veya IoT Hub cihaz sağlama hizmeti ile cihaz sağlamayı işlemek için IoT kimlik hizmetini kullanır. IoT kimlik hizmeti cinini yapılandırmak için terminalde bir düzenleyici açın.

```bash
sudo systemctl edit aziot-identityd
```

Aşağıdaki metni girerek **\<proxy URL>** proxy sunucu adresiniz ve bağlantı noktasıyla değiştirin. Sonra Kaydet ve çık.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Yeni yapılandırmaların çekilmesi için Service Manager 'ı yenileyin.

```bash
sudo systemctl daemon-reload
```

IoT Edge sistem hizmetlerini, her iki Daemon 'ları de etkili olması için yeniden başlatın.

```bash
sudo iotedge system restart
```

Ortam değişkenlerinizin oluşturulduğunu ve yeni yapılandırmanın yüklendiğini doğrulayın.

```bash
systemctl show --property=Environment aziot-edged
systemctl show --property=Environment aziot-identityd
```
:::moniker-end
<!--end 1.2-->

#### <a name="windows-using-iot-edge-for-linux-on-windows"></a>Windows 'da Linux için IoT Edge kullanan Windows

Windows sanal makinesinde Linux için IoT Edge oturum açın:

```azurepowershell-interactive
Ssh-EflowVm
```

IoT Edge Daemon 'ı yapılandırmak için yukarıdaki Linux bölümüyle aynı adımları izleyin.

#### <a name="windows-using-windows-containers"></a>Windows kapsayıcıları kullanan pencereler

Yönetici olarak bir PowerShell penceresi açın ve yeni ortam değişkeniyle kayıt defterini düzenlemek için aşağıdaki komutu çalıştırın. **\<proxy url>** Ara sunucu adresiniz ve bağlantı noktasıyla değiştirin.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Değişikliklerin etkili olması için IoT Edge yeniden başlatın.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>IoT Edge aracısını yapılandırma

IoT Edge Aracısı, herhangi bir IoT Edge cihazında başlatılacak ilk modüldür. IoT Edge config dosyasındaki bilgilere dayalı olarak ilk kez başlatılır. IoT Edge Aracısı daha sonra dağıtım bildirimlerini almak için IoT Hub bağlanır, bu da cihaza hangi modüllerin dağıtılması gerektiğini bildirir.

Bu adım ilk cihaz kurulumu sırasında IoT Edge cihaza bir kez gerçekleşir.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. IoT Edge cihazınızda config. YAML dosyasını açın. Linux sistemlerinde, bu dosya **/etc/iotedge/config.exe** yolunda bulunur. Windows sistemlerinde bu dosya **C:\programdata\iotedge\config.exe** yolunda bulunur. Yapılandırma dosyası korunuyor, bu nedenle ona erişmek için yönetim ayrıcalıklarına sahip olmanız gerekir. Linux sistemlerinde, `sudo` dosyayı tercih ettiğiniz metin düzenleyicisinde açmadan önce komutunu kullanın. Windows 'ta, Not Defteri gibi bir metin düzenleyicisini yönetici olarak açın ve dosyayı açın.

2. Config. YAML dosyasında **Edge Aracısı modülü belirtimi** bölümünü bulun. IoT Edge Aracısı tanımı, ortam değişkenleri ekleyebileceğiniz bir **env** parametresi içerir.

3. Env parametresi için yer tutucular olan süslü ayraçları kaldırın ve yeni değişkeni yeni bir satıra ekleyin. YAML 'deki girintiler iki boşluk olduğunu unutmayın.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. IoT Edge Runtime, IoT Hub konuşmak için AMQP 'yi varsayılan olarak kullanır. Bazı proxy sunucuları AMQP bağlantı noktalarını engeller. Bu durumda, Ayrıca, edgeAgent 'ı WebSocket üzerinden AMQP kullanacak şekilde yapılandırmanız gerekir. İkinci bir ortam değişkeni ekleyin.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![ortam değişkenleriyle edgeAgent tanımı](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Config. YAML içindeki değişiklikleri kaydedin ve düzenleyiciyi kapatın. Değişikliklerin etkili olması için IoT Edge yeniden başlatın.

   * Linux ve Windows üzerinde Linux için IoT Edge:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows kapsayıcıları kullanan pencereler:

      ```powershell
      Restart-Service iotedge
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge cihazınızda yapılandırma dosyasını açın: `/etc/aziot/config.toml` . Yapılandırma dosyası korunuyor, bu nedenle ona erişmek için yönetim ayrıcalıklarına sahip olmanız gerekir. Linux sistemlerinde, `sudo` dosyayı tercih ettiğiniz metin düzenleyicisinde açmadan önce komutunu kullanın.

2. Yapılandırma dosyasında, `[agent]` Başlangıçta kullanılacak edgeAgent modülünün tüm yapılandırma bilgilerini içeren bölümünü bulun. IoT Edge Aracısı tanımı, `[agent.env]` ortam değişkenleri ekleyebileceğiniz alt bölümü içerir.

3. **Https_proxy** parametresini ortam değişkenleri bölümüne ekleyin ve proxy URL 'nizi değeri olarak ayarlayın.

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   # "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

4. IoT Edge Runtime, IoT Hub konuşmak için AMQP 'yi varsayılan olarak kullanır. Bazı proxy sunucuları AMQP bağlantı noktalarını engeller. Bu durumda, Ayrıca, edgeAgent 'ı WebSocket üzerinden AMQP kullanacak şekilde yapılandırmanız gerekir. Parametrenin açıklamasını kaldırın `UpstreamProtocol` .

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

5. Değişiklikleri kaydedin ve düzenleyiciyi kapatın. En son Değişikliklerinizi uygulayın.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-deployment-manifests"></a>Dağıtım bildirimlerini yapılandırma  

IoT Edge cihazınız ara sunucu ile çalışacak şekilde yapılandırıldıktan sonra, gelecekteki dağıtım bildirimlerinde HTTPS_PROXY ortam değişkenini bildirmeye devam etmeniz gerekir. Azure portal sihirbazını kullanarak veya bir dağıtım bildirimi JSON dosyasını düzenleyerek dağıtım bildirimlerini düzenleyebilirsiniz.

İki çalışma zamanı modülünü, edgeAgent ve edgeHub 'ı her zaman, IoT Hub ile bir bağlantı sürdürmek için proxy sunucu üzerinden iletişim kurmak üzere yapılandırın. Proxy bilgilerini edgeAgent modülünden kaldırırsanız, bağlantıyı yeniden kurmak için tek yol, önceki bölümde açıklandığı gibi, cihazdaki yapılandırma dosyasını düzenlemedir.

EdgeAgent ve edgeHub modüllerine ek olarak, diğer modüllerin ara sunucu yapılandırmasına ihtiyacı olabilir. BLOB depolama gibi IoT Hub yanı sıra Azure kaynaklarına erişmesi gereken modüller, dağıtım bildirimi dosyasında belirtilen HTTPS_PROXY değişkenine sahip olmalıdır.

Aşağıdaki yordam IoT Edge cihazının ömrü boyunca geçerlidir.

### <a name="azure-portal"></a>Azure portalı

IoT Edge cihazları için dağıtımlar oluşturmak üzere **modülleri ayarlama** Sihirbazı 'nı kullandığınızda, her modülün proxy sunucu bağlantılarını yapılandırabileceğiniz bir **ortam değişkenleri** bölümü vardır.

IoT Edge Aracısı ve IoT Edge hub modüllerini yapılandırmak için, sihirbazın ilk adımında **çalışma zamanı ayarları** ' nı seçin.

![Gelişmiş Edge çalışma zamanı ayarlarını yapılandırma](./media/how-to-configure-proxy-support/configure-runtime.png)

**Https_proxy** ortam değişkenini hem IoT Edge Aracısı hem de IoT Edge hub modülü tanımlarına ekleyin. IoT Edge cihazınızdaki yapılandırma dosyasına **Upstreamprotocol** ortam değişkenini eklediyseniz, bunu IoT Edge aracı modül tanımına da ekleyin.

![Https_proxy ortam değişkenini ayarla](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Dağıtım bildirimine eklediğiniz tüm diğer modüller aynı kalıbı izler.

### <a name="json-deployment-manifest-files"></a>JSON dağıtım bildirim dosyaları

Visual Studio Code şablonları kullanarak veya JSON dosyalarını el ile oluşturarak IoT Edge cihazları için dağıtımlar oluşturursanız, ortam değişkenlerini her modül tanımına doğrudan ekleyebilirsiniz.

Aşağıdaki JSON biçimini kullanın:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Ortam değişkenleri dahil olmak üzere, modül tanımınızın aşağıdaki edgeHub örneği gibi görünmesi gerekir:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
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

## <a name="working-with-traffic-inspecting-proxies"></a>Trafikle çalışma-ara sunucu inceleniyor

Kullanmaya çalıştığınız ara sunucu, TLS ile güvenli bağlantılarda trafik incelemesi gerçekleştiriyorsa, X. 509.440 sertifikalarıyla kimlik doğrulamanın çalışmadığına dikkat etmeniz önemlidir. IoT Edge, belirtilen sertifikayla ve anahtarla sona erdirmek üzere şifrelenmiş bir TLS kanalı oluşturur. Bu kanal trafik incelemesi için bozulur, proxy, kanalı uygun kimlik bilgileriyle yeniden oluşturamıyor ve IoT Hub ve IoT Hub cihaz sağlama hizmeti bir `Unauthorized` hata döndürüyor.

Trafik incelemesi gerçekleştiren bir proxy kullanmak için, denetimi önlemek için, paylaşılan erişim imzası kimlik doğrulamasını kullanmanız veya IoT Hub ve IoT Hub cihaz sağlama hizmeti 'nin bir izin verilenler listesine eklenmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge çalışma zamanının](iot-edge-runtime.md)rolleri hakkında daha fazla bilgi edinin.

[Azure IoT Edge Için sık karşılaşılan sorunlar ve çözünürlüklerle](troubleshoot.md) yükleme ve yapılandırma hatalarını giderme
