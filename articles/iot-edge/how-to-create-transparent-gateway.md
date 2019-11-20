---
title: Saydam bir ağ geçidi cihazı - Azure IOT Edge oluştur | Microsoft Docs
description: Azure IOT Edge cihazı, aşağı akış cihazlardan bilgi işleyebilen saydam bir ağ geçidi olarak kullanma
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 467ec25bb9e41180da36f118094324e4fea48cf8
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266093"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Saydam bir ağ geçidi olarak görev yapacak bir IOT Edge cihazı yapılandırma

Bu makalede, bir IoT Edge cihazının diğer cihazların IoT Hub iletişim kurması için saydam bir ağ geçidi olarak çalışacak şekilde yapılandırılması için ayrıntılı yönergeler sağlanmaktadır. Bu makalede, terim *IOT Edge ağ geçidi* saydam bir ağ geçidi olarak kullanılan bir IOT Edge cihazı gösterir. Daha fazla bilgi için bkz. [bir IoT Edge cihazının ağ geçidi olarak nasıl kullanılabileceği](./iot-edge-as-gateway.md).

>[!NOTE]
>Şu anda:
> * Edge özellikli cihazlar IOT Edge ağ geçidi için bağlantı kurulamıyor. 
> * Aşağı Akış cihazları karşıya dosya yükleme kullanamazsınız.

Başarılı bir saydam ağ geçidi bağlantısı kurmak için üç genel adım vardır. Bu makalede ilk adım ele alınmaktadır:

1. **Ağ Geçidi cihazının aşağı akış cihazlarına güvenli bir şekilde bağlanabilmesi, aşağı akış cihazlarından iletişim alabilmesi ve iletileri uygun hedefe yönlendirmesi gerekir.**
2. Aşağı akış cihazının IoT Hub kimlik doğrulaması yapabilmesi ve ağ geçidi cihazından iletişim kurmayı bilmesi için bir cihaz kimliği olması gerekir. Daha fazla bilgi için bkz. [Azure 'da bir aşağı akış cihazının kimliğini doğrulama IoT Hub](how-to-authenticate-downstream-device.md).
3. Aşağı akış cihazının ağ geçidi cihazına güvenli bir şekilde bağlanabiliyor olması gerekir. Daha fazla bilgi için [bir Azure IOT Edge ağ geçidi için aşağı akış cihaz Bağlayamama](how-to-connect-downstream-device.md).


Bir cihazın ağ geçidi olarak çalışması için, onun aşağı akış cihazlarına güvenli bir şekilde bağlanabilmesi gerekir. Azure IOT Edge cihazları arasında güvenli bağlantılar kurmak için bir ortak anahtar altyapısı (PKI) kullanmanıza olanak tanır. Bu durumda, biz saydam bir ağ geçidi olarak görev yapan bir IOT Edge cihazına bağlamak için bir aşağı akış cihazı vermiş olursunuz. Makul güvenliği korumak için, aşağı akış cihazının ağ geçidi cihazının kimliğini onaylamasını gerekir. Bu kimlik denetimi, cihazlarınızın potansiyel olarak kötü amaçlı ağ geçitlerine bağlanmasını engeller.

Saydam bir ağ geçidi senaryosunda bir aşağı akış cihazı, [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) bulut hizmeti ile oluşturulmuş bir kimliği olan herhangi bir uygulama veya platform olabilir. Çoğu durumda, bu uygulamaları kullanma [Azure IOT cihaz SDK'sını](../iot-hub/iot-hub-devguide-sdks.md). Tüm pratik amacıyla bir aşağı akış cihazın IOT Edge ağ geçidi cihazın kendisinde çalışan bir uygulama bile olabilir. Ancak, bir IoT Edge cihaz IoT Edge bir ağ geçidinin bir aşağı akış olamaz. 

Ağ geçidi cihazı topolojiniz için gerekli güven sağlayan herhangi bir sertifika altyapısı oluşturabilirsiniz. Bu makalede, belirli bir IoT Hub (IoT Hub 'ı kök CA) ile ilişkili bir X. 509.952 CA sertifikası içeren ve bu CA ile imzalanan bir dizi sertifika olan IoT Hub [x. 509.952 CA güvenliğini](../iot-hub/iot-hub-x509ca-overview.md) etkinleştirmek için kullandığınız sertifika kurulumunu kabul ediyoruz. ve IoT Edge cihaz için bir CA.

![Ağ geçidi sertifikası Kurulumu](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Bu makale boyunca kullanılan "kök CA" terimi, bir dağıtılmış sertifika yetkilisinin sertifika kökü olması gerekmeyen PKI sertifika zincirinin en üst yetkili ortak sertifikasına başvurur. Çoğu durumda, aslında bir ara CA genel sertifikasıdır. 

Ağ Geçidi, bağlantının başlatılması sırasında IoT Edge cihaz CA sertifikasını aşağı akış cihazına sunar. Aşağı akış cihazı, IoT Edge cihaz CA sertifikasının kök CA sertifikası tarafından imzalandığından emin olmak için kontrol eder. Bu işlem, aşağı akış cihazının ağ geçidinin güvenilir bir kaynaktan geldiğini onaylamasını sağlar.

Aşağıdaki adımlar, sertifikaları oluşturma ve bunları ağ geçidine doğru yerlere yükleme sürecinde size yol gösterir. Herhangi bir makine sertifikalarını oluşturmak için kullanın ve ardından bunları IOT Edge cihazınıza kopyalayabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

* Sertifika oluşturmak için bir geliştirme makinesi. 
* Bir ağ geçidi olarak yapılandırmak için Azure IOT Edge cihazı. Aşağıdaki işletim sistemlerinden biri için IoT Edge yükleme adımlarını kullanın:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Windows ile sertifikalar oluşturma

Windows üzerinde test sertifikaları oluşturmak için bu bölümdeki adımları kullanın. Sertifikaları oluşturmak için bir Windows makinesi kullanabilir ve ardından bunları desteklenen herhangi bir işletim sisteminde çalışan herhangi bir IoT Edge cihaza kopyalayabilirsiniz. 

Bu bölümde oluşturulan sertifikaları yalnızca test amaçlarına yöneliktir. 

### <a name="install-openssl"></a>OpenSSL yükleyin

OpenSSL için Windows sertifikalarını oluşturmak için kullanmakta olduğunuz makineye yükleyin. Windows cihazınızda OpenSSL zaten yüklüyse, bu adımı atlayabilirsiniz, ancak PATH ortam değişkeninizdeki OpenSSL. exe ' nin kullanılabilir olduğundan emin olabilirsiniz. 

OpenSSL 'yi yüklemenin çeşitli yolları vardır; örneğin:

* **Kolay** [Üçüncü taraf OpenSSL ikililerini](https://wiki.openssl.org/index.php/Binaries)indirin ve yükleyin, örneğin, [SourceForge üzerinde OpenSSL](https://sourceforge.net/projects/openssl/). Tam yolunu openssl.exe, PATH ortam değişkenine ekleyin. 
   
* **Önerilen** OpenSSL kaynak kodunu indirin ve ikili dosyaları kendi makinenizde veya [vcpkg](https://github.com/Microsoft/vcpkg)aracılığıyla derleyin. Aşağıda listelenen yönergeleri vcpkg kaynak kodu, derleme ve yükleme OpenSSL kolay adımda Windows makinenize indirmek için kullanın.

   1. Vcpkg yüklemek istediğiniz bir dizine gidin. Bu dizine başvuracağınız  *\<VCPKGDIR >* . İndirme ve yükleme yönergelerini [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Vcpkg yüklendikten sonra, Windows x64 için OpenSSL paketini yüklemek üzere bir PowerShell isteminden aşağıdaki komutu çalıştırın. Yükleme, genellikle tamamlanması yaklaşık 5 dakika sürer.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Ekleme `<VCPKGDIR>\installed\x64-windows\tools\openssl` , PATH ortam değişkenine böylece openssl.exe dosya çağırma için kullanılabilir.

### <a name="prepare-creation-scripts"></a>Oluşturma betikleri hazırlama

Git deposu Azure IoT Edge test sertifikaları oluşturmak için kullanabileceğiniz betikleri içerir. Bu bölümde, IoT Edge depoyu klonlayın ve betikleri yürütün. 

1. Yönetici modunda bir PowerShell penceresi açın. 

2. Üretim dışı sertifikalarını oluşturmak için komut dosyalarını içeren bir git deposunu kopyalayın. Bu betikler, saydam bir ağ geçidini ayarlamak için gerekli sertifikaları oluşturmanıza yardımcı olur. Kullanım `git clone` komutu veya [ZIP indir](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Çalışmak istediğiniz dizine gidin. Bu makalenin tamamında, bu dizini  *\<WRKDIR >* çağıracağız. Tüm sertifikalar ve anahtarlar bu çalışma dizininde oluşturulacak.

4. Kopyalanan deponun yapılandırma ve betik dosyalarını çalışma dizininize kopyalayın. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Depoyu ZIP olarak indirdiyseniz, klasör adı olur `iotedge-master` ve yolun geri kalanı aynıdır. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. PowerShell komut dosyalarını çalıştırmak etkinleştirin.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Betikler tarafından kullanılan işlevleri PowerShell 'in genel ad alanına taşıyın.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell penceresi, bu komut dosyası tarafından oluşturulan sertifikaların yalnızca test amaçlı olduğunu ve üretim senaryolarında kullanılmamalıdır bir uyarı görüntüler.

8. OpenSSL 'nin doğru şekilde yüklendiğini doğrulayın ve var olan sertifikalarla ad çakışmaları olmadığından emin olun. İlgili sorun varsa betiği nasıl düzeltileceğini sisteminizde açıklamalıdır.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Sertifika oluşturma

Bu bölümde, üç sertifikaları oluşturma ve bunları bir zincirinde bağlayın. Sertifika zinciri dosyasında yerleştirme, bunları, IOT Edge ağ geçidi cihazı ve herhangi bir aşağı akış cihazlarda kolayca yüklemenize olanak tanır.  

1. Kök CA sertifikasını oluşturun ve bir ara sertifikayı imzalayıp imzalayın. Sertifikalar çalışma dizininize yerleştirilir.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Bu betik komutu birkaç sertifika ve anahtar dosyası oluşturur, ancak bu makalenin ilerleyen kısımlarında bir sonraki bölümüne başvuracağız:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Aşağıdaki komutla IoT Edge cihaz CA sertifikasını ve özel anahtarı oluşturun. CA sertifikası için bir ad sağlayın (örneğin, **Myedgedeviceca**). Ad, dosya adlandırmak için ve sertifika oluşturma sırasında kullanılır. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   Bu betik komutu, bu makalede daha sonra başvurabileceğiniz iki sertifika ve anahtar dosyası oluşturur:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >**Myedgedeviceca**dışında bir ad sağlarsanız, bu komut tarafından oluşturulan sertifikalar ve anahtarlar bu adı yansıtır. 

Artık sertifikalara sahip olduğunuza [göre, ağ geçidine sertifika yüklemeye](#install-certificates-on-the-gateway) Şimdi atlayın

## <a name="generate-certificates-with-linux"></a>Linux ile sertifikalar oluşturma

Linux 'ta test sertifikaları oluşturmak için bu bölümdeki adımları kullanın. Sertifikaları oluşturmak için bir Linux makinesi kullanabilir ve ardından bunları desteklenen herhangi bir işletim sisteminde çalışan herhangi bir IoT Edge cihaza kopyalayabilirsiniz. 

Bu bölümde oluşturulan sertifikaları yalnızca test amaçlarına yöneliktir. 

### <a name="prepare-creation-scripts"></a>Oluşturma betikleri hazırlama

Git deposu Azure IoT Edge test sertifikaları oluşturmak için kullanabileceğiniz betikleri içerir. Bu bölümde, IoT Edge depoyu klonlayın ve betikleri yürütün. 

1. Üretim dışı sertifikalarını oluşturmak için komut dosyalarını içeren bir git deposunu kopyalayın. Bu betikler, saydam bir ağ geçidini ayarlamak için gerekli sertifikaları oluşturmanıza yardımcı olur. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Çalışmak istediğiniz dizine gidin. *\<WRKDIR >* olarak makale genelinde bu dizine başvuracağız. Tüm sertifika ve anahtar dosyaları bu dizinde oluşturulacaktır.
  
3. Yapılandırma ve betik dosyalarını kopyalanan IoT Edge deposundan çalışma dizininize kopyalayın.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

### <a name="create-certificates"></a>Sertifika oluşturma

Bu bölümde, üç sertifikaları oluşturma ve bunları bir zincirinde bağlayın. IOT Edge ağ geçidi cihazınıza ve herhangi bir aşağı akış cihazlarda kolayca yüklemek için sertifika zinciri dosyasında yerleştirme sağlar.  

1. Kök CA sertifikasını ve bir ara sertifikayı oluşturun. Bu sertifikalar yerleştirilir  *\<WRKDIR >* .

   Bu çalışma dizininde zaten kök ve ara sertifikalar oluşturduysanız, bu betiği yeniden çalıştırmayın. Bu betiği yeniden çalıştırmak var olan sertifikaların üzerine yazacak. Bunun yerine, sonraki adıma geçin. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Betik birkaç sertifika ve anahtar oluşturur. Bir sonraki bölümde başvurabileceğiniz bir tane olduğunu unutmayın:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Aşağıdaki komutla IoT Edge cihaz CA sertifikasını ve özel anahtarı oluşturun. CA sertifikası için bir ad sağlayın (örneğin, **Myedgedeviceca**). Ad, dosya adlandırmak için ve sertifika oluşturma sırasında kullanılır. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   Betik birkaç sertifika ve anahtar oluşturur. Bir sonraki bölümde başvurabileceğiniz iki örneğini unutmayın: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >**Myedgedeviceca**dışında bir ad sağlarsanız, bu komut tarafından oluşturulan sertifikalar ve anahtarlar bu adı yansıtır. 

## <a name="install-certificates-on-the-gateway"></a>Sertifika ağ geçidi yükleme

Bir sertifika zinciri yaptığınız, IOT Edge ağ geçidi cihazında yükleyin ve yeni sertifikalar başvurmak için IOT Edge çalışma zamanı yapılandırma gerekir. 

1. Aşağıdaki dosyaları kopyalayın  *\<WRKDIR >* . Bunları herhangi bir IOT Edge cihazı kaydedin. IOT Edge cihazınız olarak hedef dizine başvuracağınız  *\<CERTDIR >* . 

   * Cihaz CA sertifikası-  `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Cihaz CA özel anahtarı- `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Kök CA-`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Sertifika dosyalarını taşımak için [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) veya [Güvenli kopya Protokolü](https://www.ssh.com/ssh/scp/) gibi bir işlev gibi bir hizmet kullanabilirsiniz.  Sertifikaları IoT Edge cihazında oluşturduysanız, bu adımı atlayabilir ve çalışma dizininin yolunu kullanabilirsiniz.

2. IOT Edge güvenlik daemon yapılandırma dosyasını açın. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Config. YAML dosyasındaki **sertifika** özelliklerini, IoT Edge cihazdaki sertifika ve anahtar dosyalarının tam yoluna ayarlayın. Dört satırın açıklamasını kaldırmak için, sertifika özelliklerinden önceki karakterikaldırın.`#` YAML 'deki girintiler iki boşluk olduğunu unutmayın.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Linux cihazlarda, kullanıcının **ıotedge** 'in sertifikaları tutan dizin için okuma izinlerine sahip olduğundan emin olun. 

## <a name="deploy-edgehub-to-the-gateway"></a>Ağ geçidine EdgeHub dağıtma

IoT Edge bir cihaza ilk kez yüklediğinizde, otomatik olarak yalnızca bir sistem modülü başlatılır: IoT Edge Aracısı. Cihazınızı bir ağ geçidi olarak çalışmak her iki sistem modüllerini gerekir. Daha önce ağ geçidinize herhangi bir modül dağıtmadıysanız ikinci sistem modülünü IoT Edge hub 'ı başlatmak için cihazınız için bir başlangıç dağıtımı oluşturun. Sihirbazda hiçbir modül eklemediğinizden, dağıtım boş görünür, ancak her iki sistem modüllerinin de çalıştığından emin olur. 

Komutu ile bir cihaz üzerinde çalışan modüllerine denetleyebilirsiniz `iotedge list`. Liste yalnızca **Edgehub**olmadan **edgeagent** modülünü döndürürse aşağıdaki adımları kullanın:

1. Azure portalında IoT Hub'ınıza gidin.

2. Git **IOT Edge** ve ağ geçidi olarak kullanacağınız IOT Edge Cihazınızı seçin.

3. **Modülleri Ayarlama**'yı seçin.

4. **İleri**’yi seçin.

5. İçinde **yolları belirtin** sayfasında, tüm iletileri tüm modüllerden IOT Hub'ına gönderir ve varsayılan bir yol olmalıdır. Yoksa aşağıdaki kodu ekleyip **İleri**'yi seçin.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. İçinde **gözden geçirme şablonu** sayfasında **Gönder**.

## <a name="open-ports-on-gateway-device"></a>Ağ Geçidi cihazında bağlantı noktalarını açma

Standart IoT Edge cihazların herhangi bir gelen bağlantısı olması gerekmez, çünkü IoT Hub tüm iletişimler giden bağlantılar aracılığıyla yapılır. Ağ Geçidi cihazları, akış aygıtlarından iletiler alması gerektiğinden farklıdır. Bir güvenlik duvarının aşağı akış cihazları ve ağ geçidi cihazı arasında olması durumunda, iletişimin de güvenlik duvarından alınması gerekir.

Bir ağ geçidi senaryosunun çalışması için, IoT Edge hub 'ın desteklenen protokollerinden en az birinin, aşağı akış cihazlarından gelen trafik için açık olması gerekir. Desteklenen protokoller MQTT, AMQP ve HTTPS 'DIR. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS | 

## <a name="route-messages-from-downstream-devices"></a>Aşağı Akış cihazlardan yönlendirme iletileri
IOT Edge çalışma zamanı yalnızca modülleri tarafından gönderilen iletiler gibi aşağı akış cihazlardan gönderilen iletiler yönlendirebilirsiniz. Bu özellik, buluta veri göndermeden önce ağ geçidinde çalışan bir modülde analiz gerçekleştirmenize olanak tanır. 

Şu anda, aşağı akış cihazlar tarafından gönderilen iletileri yönlendiren bunları modülleri tarafından gönderilen iletilerden farklılaştırılması tarafından yoludur. Tüm modüller tarafından gönderilen iletiler olarak adlandırılan bir sistem özelliği içeren **connectionModuleId** ancak aşağı akış cihazlar tarafından gönderilen iletileri yapın. Bu sistem özelliği içeren herhangi bir iletiyi hariç tutmak için rota WHERE yan tümcesini kullanabilirsiniz. 

Aşağıdaki yol, herhangi bir aşağı akış aygıtından, adlı `ai_insights`bir modüle ve `ai_insights` sonra IoT Hub ' ye ileti gönderen bir örnektir.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

İleti yönlendirme hakkında daha fazla bilgi için bkz. [modülleri dağıtma ve yollar'kurmak](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Genişletilmiş çevrimdışı işlemi etkinleştir

IoT Edge çalışma zamanının [v 1.0.4 sürümü](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) ile başlayarak, buna bağlanan ağ geçidi cihazı ve aşağı akış cihazları genişletilmiş çevrimdışı işlem için yapılandırılabilir. 

Bu özellik sayesinde, yerel modüller veya aşağı akış cihazları gerektiği gibi IoT Edge cihazla yeniden kimlik doğrulaması yapabilir ve IoT Hub 'ından bağlantısı kesilse bile ileti ve yöntemleri kullanarak birbirleriyle iletişim kurabilir. Daha fazla bilgi için [anlayın cihazları, modülleri ve alt cihazlar bu çevrimdışı özellikleri IOT Edge için Genişletilmiş](offline-capabilities.md).

Genişletilmiş çevrimdışı özellikleri etkinleştirmek için, bir IoT Edge ağ geçidi cihazı ile Bağlanılacak olan aşağı akış cihazları arasında bir üst-alt ilişkisi kurarsınız. Bu adımlar, [bir aşağı akış cihazının Azure IoT Hub kimlik doğrulaması](how-to-authenticate-downstream-device.md)konusunda daha ayrıntılı olarak açıklanmıştır.

## <a name="next-steps"></a>Sonraki adımlar

Saydam bir ağ geçidi olarak çalışan bir IOT Edge cihazına sahip olduğunuza göre ağ geçidi güven ve iletileri göndermek için aşağı akış cihazlarınızı yapılandırmak gerekir. Saydam ağ geçidi senaryonuzu ayarlamanın sonraki adımları için [bir aşağı akış cihazının kimliğini Azure IoT Hub doğrulamak](how-to-authenticate-downstream-device.md) için devam edin. 
