---
title: Aşağı akış cihazlarını bağlama-Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge ağ geçidi cihazlarına bağlanmak için aşağı akış veya yaprak cihazları yapılandırma.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: dc2d2d3e92435c7a028b43a095f456c2c383ecb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199628"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Aşağı akış cihazını Azure IoT Edge ağ geçidine bağlama

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Bu makalede, aşağı akış cihazları arasında güvenilir bir bağlantı kurmak ve saydam ağ geçitleri IoT Edge için yönergeler sağlanmaktadır. Saydam bir ağ geçidi senaryosunda, bir veya daha fazla cihaz iletilerini IoT Hub bağlantısını tutan tek bir ağ geçidi cihazından geçirebilir.

Başarılı bir saydam ağ geçidi bağlantısı kurmak için üç genel adım vardır. Bu makalede üçüncü adım ele alınmaktadır:

1. Aşağı akış cihazlarının güvenli bir şekilde bağlanabilmesi için ağ geçidi cihazını sunucu olarak yapılandırın. Ağ geçidini, aşağı akış aygıtlarından ileti alacak şekilde ayarlayın ve bunları uygun hedefe yönlendirin. Bu adımlar için bkz. bir [IoT Edge cihazı, saydam bir ağ geçidi olarak davranacak şekilde yapılandırma](how-to-create-transparent-gateway.md).
2. IoT Hub ile kimlik doğrulayabilmesi için aşağı akış cihazı için bir cihaz kimliği oluşturun. Ağ Geçidi cihazından ileti göndermek için aşağı akış cihazını yapılandırın. Bu adımlar için bkz. [Azure 'da bir aşağı akış cihazının kimliğini doğrulama IoT Hub](how-to-authenticate-downstream-device.md).
3. **Aşağı akış cihazını ağ geçidi cihazına bağlayın ve ileti göndermeye başlayın.**

Bu makalede aşağı akış cihaz bağlantıları için temel kavramlar ve aşağı akış cihazlarınızı ayarlama konusunda kullanabileceğiniz kılavuzlar açıklanmaktadır:

* Aktarım Katmanı Güvenliği (TLS) ve sertifika temelleri açıklanır.
* TLS kitaplıklarının farklı işletim sistemlerinde nasıl çalıştığını ve her bir işletim sisteminin sertifikalarla nasıl uğramasını açıklayan.
* Başlamanıza yardımcı olmak için birkaç dilde Azure IoT örnekleri ile gezinirsiniz.

Bu makalede, terimler *ağ geçidi* ve *IoT Edge Ağ* geçidi, saydam bir ağ geçidi olarak yapılandırılmış bir IoT Edge cihazına başvurur.

## <a name="prerequisites"></a>Önkoşullar

* IoT Edge cihazı yapılandırmak için kullanılan kök CA sertifika dosyasını, aşağı akış cihazınızda bulunan [saydam bir ağ geçidi olarak davranacak şekilde yapılandırın](how-to-create-transparent-gateway.md) . Aşağı akış cihazınız, ağ geçidi cihazının kimliğini doğrulamak için bu sertifikayı kullanır. Tanıtım sertifikalarını kullandıysanız, kök CA sertifikasına **Azure-iot-test-only. root. ca. cert. ped** adı verilir.
* [Bir aşağı akış cihazının Azure IoT Hub kimlik doğrulaması](how-to-authenticate-downstream-device.md)bölümünde açıklandığı gibi ağ geçidi cihazını işaret eden değiştirilmiş bağlantı dizesine sahip olmalıdır.

## <a name="prepare-a-downstream-device"></a>Aşağı akış cihazı hazırlama

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Bir aşağı akış cihazı, Azure IoT Hub bulut hizmeti ile oluşturulmuş bir kimliği olan herhangi bir uygulama veya platform olabilir. Çoğu durumda bu uygulamalar [Azure IoT cihaz SDK 'sını](../iot-hub/iot-hub-devguide-sdks.md)kullanır. Bir aşağı akış cihazı, IoT Edge ağ geçidi cihazının kendisi üzerinde çalışan bir uygulama bile olabilir. Ancak, başka bir IoT Edge cihaz IoT Edge bir ağ geçidinin bir aşağı akış olamaz.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Bir aşağı akış cihazı, Azure IoT Hub bulut hizmeti ile oluşturulmuş bir kimliği olan herhangi bir uygulama veya platform olabilir. Çoğu durumda bu uygulamalar [Azure IoT cihaz SDK 'sını](../iot-hub/iot-hub-devguide-sdks.md)kullanır. Bir aşağı akış cihazı, IoT Edge ağ geçidi cihazının kendisi üzerinde çalışan bir uygulama bile olabilir.

Bu makalede, IoT cihazını bir aşağı akış cihazı olarak bağlama adımları sağlanmaktadır. Aşağı akış cihazı olarak bir IoT Edge cihazınız varsa bkz. bir [aşağı akış IoT Edge cihazını bir Azure IoT Edge ağ geçidine bağlama](how-to-connect-downstream-iot-edge-device.md).
:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>IoT Hub kayıtlı IoT cihazları, tek bir cihazdaki farklı işlemleri, donanımları veya işlevleri yalıtmak için [Modül TWINS](../iot-hub/iot-hub-devguide-module-twins.md) kullanabilir. IoT Edge ağ geçitleri, simetrik anahtar kimlik doğrulaması kullanan ancak X. 509.440 sertifikası kimlik doğrulaması olmadan aşağı akış modülü bağlantılarını destekler

Bir aşağı akış cihazını IoT Edge bir ağ geçidine bağlamak için iki şey olması gerekir:

* Bir IoT Hub cihaz bağlantı dizesiyle yapılandırılmış bir cihaz veya uygulama, ağ geçidine bağlamak için bilgilerle birlikte eklenir.

    Bu adım önceki makalede tamamlanmıştı ve [Azure IoT Hub bir aşağı akış cihazının kimliğini doğrular](how-to-authenticate-downstream-device.md#retrieve-and-modify-connection-string).

* Ağ Geçidi cihazına Aktarım Katmanı Güvenliği (TLS) bağlantılarını doğrulamak için cihazın veya uygulamanın ağ geçidinin **kök CA sertifikasına** güvenmesi gerekebilir.

    Bu adım, makalenin geri kalanında ayrıntılı olarak açıklanmaktadır. Bu adım iki şekilde gerçekleştirilebilir: CA sertifikasını işletim sisteminin sertifika deposuna yükleyerek veya (belirli diller için) Azure IoT SDK 'larını kullanarak uygulamalar içindeki sertifikaya başvurarak.

## <a name="tls-and-certificate-fundamentals"></a>TLS ve sertifika temelleri

Aşağı akış cihazlarını IoT Edge 'e güvenli bir şekilde bağlama zorluğu, internet üzerinden oluşan diğer tüm güvenli istemci/sunucu iletişimi gibidir. İstemci ve sunucu, [Aktarım Katmanı Güvenliği (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)kullanarak internet üzerinden güvenli bir şekilde iletişim kurar. TLS, sertifikalar adlı standart [ortak anahtar altyapısı (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) yapıları kullanılarak oluşturulmuştur. TLS oldukça ilgili bir belirtimdir ve iki uç noktanın güvenliğini sağlamaya yönelik çok çeşitli konuları ele alır. Bu bölümde, cihazları bir IoT Edge ağ geçidine güvenli bir şekilde bağlamak için ilgili kavramlar özetlenmektedir.

İstemci bir sunucuya bağlandığı zaman sunucu, *sunucu sertifika zinciri* olarak adlandırılan bir sertifika zinciri sunar. Bir sertifika zinciri tipik olarak bir kök sertifika yetkilisi (CA) sertifikası, bir veya daha fazla ara CA sertifikası ve son olarak sunucunun sertifikası oluşur. İstemci, tüm sunucu sertifikası zincirini şifreli olarak doğrulayarak sunucu ile güven oluşturur. Sunucu sertifikası zincirinin bu istemci doğrulaması, *sunucu zinciri doğrulaması* olarak adlandırılır. İstemci, sunucuyu, sahip olma *kanıtı* olarak adlandırılan bir işlemde sunucu sertifikasıyla ilişkili özel anahtarı kanıtlamaya yönelik olarak ele alır. Sunucu zinciri doğrulama ve sahip olma kanıtı 'nın birleşimi *sunucu kimlik doğrulaması* olarak adlandırılır. Bir sunucu sertifika zincirini doğrulamak için, bir istemcinin, sunucunun sertifikasını oluşturmak için kullanılan kök CA sertifikasının bir kopyasına ihtiyacı vardır. Normalde web sitelerine bağlanırken, bir tarayıcı yaygın olarak kullanılan CA sertifikalarıyla önceden yapılandırılmış olarak sunulur, böylece istemci sorunsuz bir işlemdir.

Bir cihaz Azure IoT Hub bağlandığı zaman, cihaz istemcdir ve IoT Hub bulut hizmeti sunucu olur. IoT Hub bulut hizmeti, genel olarak kullanılabilen ve yaygın olarak kullanılan, **Baltimore CyberTrust kökü** adlı BIR kök CA sertifikası tarafından desteklenir. IoT Hub CA sertifikası çoğu cihazda zaten yüklü olduğundan, çok sayıda TLS uygulaması (OpenSSL, Schannel, LibreSSL) otomatik olarak sunucu sertifikası doğrulama sırasında kullanır. Ancak, IoT Hub başarıyla bağlanan bir cihaz, bir IoT Edge ağ geçidine bağlanmaya çalışırken sorunlara sahip olabilir.

Bir cihaz bir IoT Edge ağ geçidine bağlanırsa, aşağı akış cihazı istemcdir ve ağ geçidi cihazı sunucu olur. Azure IoT Edge, ağ geçidi sertifika zincirlerini derlemenize olanak tanır, ancak bunları görür. Baltimore gibi bir genel CA sertifikası kullanmayı veya otomatik olarak imzalanan (veya şirket içi) bir kök CA sertifikası kullanmayı tercih edebilirsiniz. Genel CA sertifikalarına genellikle bunlarla ilişkili bir maliyet bulunur, bu nedenle genellikle üretim senaryolarında kullanılır. Otomatik olarak imzalanan CA sertifikaları geliştirme ve test için tercih edilir. Tanıtım sertifikalarını kullanıyorsanız, bunlar otomatik olarak imzalanan kök CA sertifikalardır.

IoT Edge ağ geçidi için otomatik olarak imzalanan bir kök CA sertifikası kullandığınızda, ağ geçidine bağlanmaya çalışan tüm aşağı akış cihazlarına yüklenmesi veya sağlanması gerekir.

![Ağ Geçidi sertifikası kurulumu](./media/how-to-create-transparent-gateway/gateway-setup.png)

IoT Edge sertifikaları ve bazı üretim etkileri hakkında daha fazla bilgi edinmek için bkz. [IoT Edge sertifikası kullanım ayrıntıları](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Kök CA sertifikasını sağlama

Ağ Geçidi cihazının sertifikalarını doğrulamak için, aşağı akış cihazının kök CA sertifikasının kendine ait bir kopyasına ihtiyacı vardır. Test sertifikaları oluşturmak için IoT Edge git deposunda verilen betikleri kullandıysanız, kök CA sertifikasına **Azure-iot-test-only. root. ca. cert. ped** adı verilir. Zaten diğer aşağı akış cihazı hazırlama adımlarının bir parçası değilseniz, bu sertifika dosyasını aşağı akış cihazınızdaki herhangi bir dizine taşıyın. Sertifika dosyasını taşımak için [Azure Key Vault](../key-vault/index.yml) veya [Güvenli kopya Protokolü](https://www.ssh.com/ssh/scp/) gibi bir işlev gibi bir hizmet kullanabilirsiniz.

## <a name="install-certificates-in-the-os"></a>İşletim sistemine sertifika yükler

Kök CA sertifikası, aşağı akış cihazından olduktan sonra, ağ geçidine bağlanan uygulamaların sertifikaya erişebildiğinizden emin olmanız gerekir.

Kök CA sertifikasını işletim sisteminin sertifika deposuna yüklemek genellikle çoğu uygulamanın kök CA sertifikasını kullanmasına izin verir. OS sertifika deposunu kullanmayan, ancak düğüm çalışma zamanının iç sertifika deposunu kullanan NodeJS uygulamaları gibi bazı özel durumlar vardır. Sertifikayı işletim sistemi düzeyinde yükleyemezseniz [Azure IoT SDK 'larını kullanarak sertifikaları kullanmaya](#use-certificates-with-azure-iot-sdks)devam edin.

### <a name="ubuntu"></a>Ubuntu

Aşağıdaki komutlar bir Ubuntu konağına CA sertifikasının nasıl yükleneceğine bir örnektir. Bu örnekte, ön koşullar makalelerindeki **Azure-iot-test-only. root. ca. cert. ped** sertifikasını kullandığınızı ve sertifikayı aşağı akış cihazında bir konuma kopyaladığınızı varsaymaktadır.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

"/Etc/SSL/certs..içindeki sertifikaları güncelleştirme" adlı bir ileti görmeniz gerekir. 1 eklenen, 0 kaldırıldı; bitti. "

### <a name="windows"></a>Windows

Aşağıdaki adımlar, bir Windows ana bilgisayarına CA sertifikasının nasıl yükleneceğine bir örnektir. Bu örnekte, ön koşullar makalelerindeki **Azure-iot-test-only. root. ca. cert. ped** sertifikasını kullandığınızı ve sertifikayı aşağı akış cihazında bir konuma kopyaladığınızı varsaymaktadır.

PowerShell 'in [Import-Certificate](/powershell/module/pkiclient/import-certificate) ' i kullanarak sertifikaları yönetici olarak yükleyebilirsiniz:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Ayrıca, **Certlm** yardımcı programını kullanarak da sertifikalar yükleyebilirsiniz:

1. Başlat menüsünde **bilgisayar sertifikalarını Yönet**' i arayın ve seçin. **Certlm** adlı bir yardımcı program açılır.
2. **Sertifikalar-Yerel bilgisayar**  >  **Güvenilen kök sertifika yetkilileri**' ne gidin.
3. **Sertifikalar** ' a sağ tıklayın ve **Tüm görevler**  >  **içeri aktar**' ı seçin. Sertifika Alma Sihirbazı 'nı başlatması gerekir.
4. Adımları yönlendirilmiş ve içeri aktarma sertifika dosyası olarak izleyin `<path>/azure-iot-test-only.root.ca.cert.pem` . Tamamlandığında, "başarıyla içeri aktarıldı" iletisini görmeniz gerekir.

Ayrıca, bu makalenin ilerleyen kısımlarında .NET örneğinde gösterildiği gibi, sertifikaları .NET API 'Leri kullanarak programlı şekilde yükleyebilirsiniz.

Genellikle uygulamalar, TLS üzerinden güvenli bir şekilde bağlanmak için [Schannel](/windows/desktop/com/schannel) adlı Windows tarafından sunulan TLS yığınını kullanır. Schannel, bir TLS bağlantısı kurmayı denemeden önce Windows sertifika deposunda tüm sertifikaların yüklenmesini *gerektirir* .

## <a name="use-certificates-with-azure-iot-sdks"></a>Azure IoT SDK 'Ları ile sertifika kullanma

Bu bölümde, Azure IoT SDK 'larının basit örnek uygulamalar kullanarak bir IoT Edge cihazına nasıl bağlanacağı açıklanmaktadır. Tüm örneklerin hedefi, cihaz istemcisini bağlamak ve ağ geçidine telemetri iletileri göndermek, ardından bağlantıyı kapatmak ve çıkmak olur.

Uygulama düzeyi örnekleri kullanılmadan önce iki şey hazırlayın:

* Aşağı akış cihazınızın IoT Hub bağlantı dizesi, ağ geçidi cihazını işaret etmek üzere değiştirilmiştir ve aşağı akış cihazınızın kimliğini doğrulamak için gereken tüm sertifikaları IoT Hub. Daha fazla bilgi için bkz. [Azure 'da bir aşağı akış cihazının kimliğini doğrulama IoT Hub](how-to-authenticate-downstream-device.md).

* Kopyaladığınız ve bir yere aşağı akış cihazınızda kaydettiğiniz kök CA sertifikasının tam yolu.

    Örneğin, `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Bu bölüm, bir Azure IoT NodeJS cihaz istemcisini IoT Edge bir ağ geçidine bağlamak için örnek bir uygulama sağlar. NodeJS uygulamaları için, kök CA sertifikasını burada gösterildiği gibi uygulama düzeyinde yüklemelisiniz. NodeJS uygulamaları sistemin sertifika deposunu kullanmaz.

1. [Azure IoT CIHAZ SDK 'sinden Node.js örnek deposu için](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) **edge_downstream_device.js** örneğini alın.
2. **README.MD** dosyasını inceleyerek örneği çalıştırmak için tüm önkoşullara sahip olduğunuzdan emin olun.
3. edge_downstream_device.js dosyasında, **ConnectionString** ve **edge_ca_cert_path** değişkenlerini güncelleştirin.
4. Örneğin cihazınızda nasıl çalıştırılacağınız hakkında yönergeler için SDK belgelerine bakın.

Çalıştırdığınız örneği anlamak için aşağıdaki kod parçacığı, istemci SDK 'sının sertifika dosyasını okumasıdır ve güvenli bir TLS bağlantısı kurmak için onu kullanır:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Bu bölüm, bir Azure IoT .NET cihaz istemcisini IoT Edge bir ağ geçidine bağlamak için örnek bir uygulama tanıtır. Ancak, .NET uygulamaları, hem Linux hem de Windows konaklarındaki sistem sertifika deposunda yüklü olan sertifikaları otomatik olarak kullanabilir.

1. [IoT Edge .NET örnekleri klasöründen](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice) **Edgedownstreamdevice** örneğine yönelik örneği alın.
2. **README.MD** dosyasını inceleyerek örneği çalıştırmak için tüm önkoşullara sahip olduğunuzdan emin olun.
3. Dosyadaki **Özellikler/launchSettings.js** **DEVICE_CONNECTION_STRING** ve **CA_CERTIFICATE_PATH** değişkenlerini güncelleştirin. Konak sistemindeki güvenilen sertifika deposunda yüklü olan sertifikayı kullanmak istiyorsanız, bu değişkeni boş bırakın.
4. Örneğin cihazınızda nasıl çalıştırılacağınız hakkında yönergeler için SDK belgelerine bakın.

Bir .NET uygulaması aracılığıyla sertifika deposuna programlı olarak güvenilir bir sertifika yüklemek için, **Edgedownstreamdevice/program. cs** dosyasındaki **ınstallcacert ()** işlevine bakın. Bu işlem ıdempotent olduğundan, ek bir etki olmadan aynı değerlerle birden çok kez çalıştırılabilir.

### <a name="c"></a>C

Bu bölüm, bir Azure IoT C cihaz istemcisini IoT Edge bir ağ geçidine bağlamak için örnek bir uygulama tanıtır. C SDK 'Sı, OpenSSL, WolfSSL ve Schannel dahil olmak üzere çok sayıda TLS kitaplığı ile çalışabilir. Daha fazla bilgi için bkz. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

1. [C örnekleri Için Azure IoT CIHAZ SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)'sinden **iotedge_downstream_device_sample** uygulamayı alın.
2. **README.MD** dosyasını inceleyerek örneği çalıştırmak için tüm önkoşullara sahip olduğunuzdan emin olun.
3. İotedge_downstream_device_sample. c dosyasında, **ConnectionString** ve **edge_ca_cert_path** değişkenlerini güncelleştirin.
4. Örneğin cihazınızda nasıl çalıştırılacağınız hakkında yönergeler için SDK belgelerine bakın.


C için Azure IoT cihaz SDK 'sı, istemciyi ayarlarken bir CA sertifikasını kaydetme seçeneği sunar. Bu işlem, sertifikayı her yere yüklemez, bunun yerine bellekte bir sertifikanın dize biçimini kullanır. Kaydedilen sertifika, bağlantı kurulurken temeldeki TLS yığınına sağlanır.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

>[!NOTE]
> İstemci ayarlanırken bir CA sertifikasını kaydetme yöntemi, [yönetilen](https://github.com/Azure/azure-iot-sdk-c#packages-and-libraries) bir paket veya kitaplık kullanılıyorsa değişebilir. Örneğin, [Arduino IDE tabanlı kitaplığı](https://github.com/azure/azure-iot-arduino) , işlem yerine bir genel [CERT. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) dosyasında tanımlanan bir sertifika dizisine CA sertifikası eklenmesini gerektirir `IoTHubDeviceClient_LL_SetOption` .  

Windows Konakları ' nde, OpenSSL veya başka bir TLS kitaplığı kullanmıyorsanız, SDK varsayılan olarak SChannel ' ı kullanmaktır. Schannel 'nin çalışması için, IoT Edge kök CA sertifikasının, işlem kullanılarak ayarlanmayan Windows sertifika deposuna yüklenmesi gerekir `IoTHubDeviceClient_SetOption` .

### <a name="java"></a>Java

Bu bölüm, bir Azure IoT Java cihaz istemcisini IoT Edge bir ağ geçidine bağlamak için örnek bir uygulama tanıtır.

1. [Java örnekleri Için Azure IoT CIHAZ SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)'dan **Send-Event** örneğini alın.
2. **README.MD** dosyasını inceleyerek örneği çalıştırmak için tüm önkoşullara sahip olduğunuzdan emin olun.
3. Örneğin cihazınızda nasıl çalıştırılacağınız hakkında yönergeler için SDK belgelerine bakın.

### <a name="python"></a>Python

Bu bölüm, bir Azure IoT Python cihaz istemcisini IoT Edge bir ağ geçidine bağlamak için örnek bir uygulama tanıtır.

1. [Python örnekleri Için Azure IoT CIHAZ SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)'sından **send_message_downstream** örneğini alın.
2. `IOTHUB_DEVICE_CONNECTION_STRING`Ve `IOTEDGE_ROOT_CA_CERT_PATH` ortam değişkenlerini Python betik açıklamalarında belirtilen şekilde ayarlayın.
3. Örneğin cihazınızda nasıl çalıştırılacağınız hakkında ek yönergeler için SDK belgelerine bakın.

## <a name="test-the-gateway-connection"></a>Ağ Geçidi bağlantısını test etme

Bu örnek komutu, ağ geçidi cihazına bağlanabildiğinden emin olmak için aşağı akış cihazında kullanın:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Bu komut MQTTS üzerinden bağlantıları sınar (bağlantı noktası 8883). Farklı bir protokol kullanıyorsanız, AMQPS (5671) veya HTTPS (433) için gereken komutu ayarlayın

Bu komutun çıktısı, zincirdeki tüm sertifikalarla ilgili bilgiler de dahil olmak üzere uzun olabilir. Bağlantınız başarılı olursa, veya gibi bir çizgi görürsünüz `Verification: OK` `Verify return code: 0 (ok)` .

![Ağ Geçidi bağlantısını doğrulama](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Ağ Geçidi bağlantısı sorunlarını giderme

Yaprak cihazınızın ağ geçidi cihazına aralıklı olarak bağlantısı varsa, çözüm için aşağıdaki adımları deneyin.

1. Bağlantı dizesindeki ağ geçidi ana bilgisayar adı, ağ geçidi cihazındaki IoT Edge yapılandırma dosyasında ana bilgisayar adı değeriyle aynı mı?
2. Ağ geçidi ana bilgisayar adı bir IP adresi olarak çözülebilir mi? Aralıklı bağlantıları DNS kullanarak ya da yaprak cihaza bir konak dosyası girişi ekleyerek çözebilirsiniz.
3. İletişim bağlantı noktaları güvenlik duvarınızdan açık mı? Kullanılan protokolü temel alan iletişim (MQTTS: 8883/AMQPS: 5671/HTTPS: 433), aşağı akış cihazı ile saydam IoT Edge arasında olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

IoT Edge, [çevrimdışı özellikleri](offline-capabilities.md) aşağı akış cihazlarına nasıl genişletebileceğinizi öğrenin.