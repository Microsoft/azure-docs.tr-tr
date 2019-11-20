---
title: Aşağı akış cihazların kimliğini doğrulama-Azure IoT Edge | Microsoft Docs
description: IoT Hub için aşağı akış cihazlarının veya yaprak cihazların kimliğini doğrulama ve bağlantı Azure IoT Edge ağ geçidi cihazları aracılığıyla yönlendirme.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/23/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1e184691ebbd34de0f69e93419d9c34ab18edbe6
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025945"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Azure IoT Hub’da bir aşağı akış cihazının kimliğini doğrulama

Saydam bir ağ geçidi senaryosunda, aşağı akış cihazları (bazen yaprak cihazlar veya alt cihazlar olarak adlandırılır), diğer tüm cihazlar gibi IoT Hub kimliklere ihtiyaç duyar. Bu makalede, IoT Hub için bir aşağı akış cihazının kimliğini doğrulama seçenekleri anlatılır ve sonra Ağ Geçidi bağlantısının nasıl bildirildiği gösterilmektedir.

Başarılı bir saydam ağ geçidi bağlantısı kurmak için üç genel adım vardır. Bu makalede ikinci adım ele alınmaktadır:

1. Ağ Geçidi cihazının aşağı akış cihazlarına güvenli bir şekilde bağlanabilmesi, aşağı akış cihazlarından iletişim alabilmesi ve iletileri uygun hedefe yönlendirmesi gerekir. Daha fazla bilgi için bkz. bir [IoT Edge cihazını saydam bir ağ geçidi olarak davranacak şekilde yapılandırma](how-to-create-transparent-gateway.md).
2. **Aşağı akış cihazının IoT Hub kimlik doğrulaması yapabilmesi ve ağ geçidi cihazından iletişim kurmayı bilmesi için bir cihaz kimliği olması gerekir.**
3. Aşağı akış cihazının ağ geçidi cihazına güvenli bir şekilde bağlanabiliyor olması gerekir. Daha fazla bilgi için bkz. bir [aşağı akış cihazını Azure IoT Edge bir ağ geçidine bağlama](how-to-connect-downstream-device.md).

Aşağı akış cihazları şu üç yöntemden birini kullanarak IoT Hub kimlik doğrulaması yapabilir: simetrik anahtarlar (bazen paylaşılan erişim anahtarları olarak adlandırılır), X. 509.440 otomatik olarak imzalanan sertifikalar veya X. 509.440 sertifika yetkilisi (CA) imzalı sertifikalar. Kimlik doğrulama adımları, ağ geçidi ilişkisini bildirmek için küçük farklılıklar ile, IoT Edge olmayan bir cihazı IoT Hub kurmak için kullanılan adımlara benzerdir.

Bu makaledeki adımlarda, Azure IoT Hub cihaz sağlama hizmeti ile otomatik sağlama değil el ile cihaz sağlama gösterilmektedir. 

## <a name="prerequisites"></a>Önkoşullar

[Bir IoT Edge cihazını, saydam bir ağ geçidi olarak davranacak şekilde yapılandırma](how-to-create-transparent-gateway.md)adımlarını uygulayın. Aşağı akış cihazınız için X. 509.440 kimlik doğrulaması kullanıyorsanız, saydam ağ geçidi makalesinde ayarladığınız betiği üreten sertifikayı kullanmanız gerekir. 

Bu makale, *ağ geçidi ana bilgisayar adına* birkaç noktada başvurur. Ağ geçidi ana bilgisayar adı, IoT Edge ağ geçidi cihazında config. YAML dosyasının **hostname** parametresinde bildirilmiştir. Bu makaledeki sertifikaları oluşturmak için kullanılır ve aşağı akış cihazlarının bağlantı dizesinde öğesine başvurulur. Ağ geçidi ana bilgisayar adının DNS veya ana bilgisayar dosya girişi kullanılarak bir IP adresi ile çözümlenebilmelidir.

## <a name="symmetric-key-authentication"></a>Simetrik anahtar kimlik doğrulaması

Simetrik anahtar kimlik doğrulaması veya paylaşılan erişim anahtarı kimlik doğrulaması, IoT Hub kimlik doğrulamasının en kolay yoludur. Simetrik anahtar kimlik doğrulamasıyla, IoT Hub ' deki IoT cihaz KIMLIĞINIZLE bir Base64 anahtarı ilişkilendirilir. Cihazınızın IoT Hub bağlandığı sırada sunabilmesi için bu anahtarı IoT uygulamalarınıza dahil edersiniz. 

### <a name="create-the-device-identity"></a>Cihaz kimliği oluşturma 

Visual Studio Code için Azure portal, Azure CLı veya IoT uzantısını kullanarak IoT Hub 'ınıza yeni bir IoT cihazı ekleyin. Aşağı akış cihazlarının, IoT Edge cihazlar değil, normal IoT cihazı olarak IoT Hub tanımlanması gerektiğini unutmayın. 

Yeni cihaz kimliğini oluşturduğunuzda, aşağıdaki bilgileri sağlayın: 

* Cihazınız için bir KIMLIK oluşturun.

* Kimlik doğrulama türü olarak **simetrik anahtar** ' ı seçin. 

* İsteğe bağlı olarak, **bir üst cihaz ayarlamayı** seçin ve bu aşağı akış cihazının bağlanacağı IoT Edge ağ geçidi cihazını seçin. Bu adım simetrik anahtar kimlik doğrulaması için isteğe bağlıdır, ancak bir üst cihaz ayarlandığında, aşağı akış cihazınız için [çevrimdışı yetenekler](offline-capabilities.md) sağladığından önerilir. Üst öğeyi daha sonra eklemek veya değiştirmek için cihaz ayrıntılarını her zaman güncelleştirebilirsiniz. 

   ![Portalda simetrik anahtar kimlik doğrulaması ile cihaz KIMLIĞI oluşturma](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Aynı işlemi gerçekleştirmek için [Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension) 'nı kullanabilirsiniz. Aşağıdaki örnek, simetrik anahtar kimlik doğrulaması ile yeni bir IoT cihazı oluşturur ve bir üst cihaz atar: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Cihaz oluşturma ve üst/alt yönetim için Azure CLı komutları hakkında daha fazla bilgi için bkz. [az IoT Hub cihaz kimliği](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) komutları için başvuru içeriği.

### <a name="connect-to-iot-hub-through-a-gateway"></a>Bir ağ geçidi üzerinden IoT Hub bağlanma

Aynı işlem, simetrik anahtarlarla IoT Hub için normal IoT cihazlarının kimlik doğrulaması için kullanılır ve ayrıca aşağı akış cihazları için de geçerlidir. Tek fark, bağlantıyı yönlendirmek için ağ geçidi cihazına bir işaretçi eklemeniz ya da çevrimdışı senaryolarda IoT Hub adına kimlik doğrulamasını işlemek için gerekli olacaktır. 

Simetrik anahtar kimlik doğrulaması için cihazınızda IoT Hub kimlik doğrulaması yapmak üzere yapmanız gereken ek adımlar yoktur. [Bir aşağı akış cihazını bir Azure IoT Edge ağ geçidine bağlama](how-to-connect-downstream-device.md)bölümünde açıklandığı gibi, aşağı akış cihazınızın ağ geçidi cihazına bağlanabilmesi için yine de sertifikalara ihtiyacınız vardır.

Portalda IoT cihaz kimliği oluşturduktan sonra, birincil veya ikincil anahtarlarını alabilirsiniz. Bu anahtarlardan birinin, IoT Hub ile iletişim kuran herhangi bir uygulamaya dahil ettiğiniz bağlantı dizesine eklenmesi gerekir. Simetrik anahtar kimlik doğrulaması için IoT Hub, kolaylık sağlamak üzere cihaz ayrıntılarında tamamen biçimlendirilmiş bağlantı dizesi sağlar. Ağ geçidi cihazı hakkında bağlantı dizesine ek bilgi eklemeniz gerekir. 

Aşağı akış cihazları için simetrik anahtar bağlantı dizeleri aşağıdaki bileşenlere ihtiyaç duyar: 

* Cihazın bağlandığı IoT Hub 'ı: `Hostname={iothub name}.azure-devices.net`
* Hub 'a kayıtlı cihaz KIMLIĞI: `DeviceID={device ID}`
* Birincil veya ikincil anahtar: `SharedAccessKey={key}`
* Cihazın bağlandığı ağ geçidi cihazı. IoT Edge ağ geçidi cihazının config. YAML dosyasından **konak adı** değerini sağlayın: `GatewayHostName={gateway hostname}`

Hepsi birlikte, bir bağlantı dizesinin tamamı şöyle görünür:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Bu aşağı akış cihazı için bir üst/alt ilişki kurduysanız, ağ geçidini doğrudan bağlantı ana bilgisayarı olarak çağırarak bağlantı dizesini basitleştirebilirsiniz. Örnek: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X. 509.440 kimlik doğrulaması 

X. 509.440 sertifikalarını kullanarak IoT cihazının kimlik doğrulamasının iki yolu vardır. Kimlik doğrulamayı seçtiğiniz şekilde, cihazınızı IoT Hub bağlama adımları aynıdır. Kimlik doğrulaması için otomatik olarak imzalanan veya CA imzalı sertifikalar ' ı seçin, sonra IoT Hub nasıl bağlanacağınızı öğrenmeye devam edin. 

IoT Hub X. 509.440 kimlik doğrulamasını kullanma hakkında daha fazla bilgi için aşağıdaki makalelere bakın: 
* [X. 509.440 CA sertifikalarını kullanarak cihaz kimlik doğrulaması](../iot-hub/iot-hub-x509ca-overview.md)
* [IoT sektöründe X. 509.440 CA sertifikalarının kavramsal olarak anlaşılmasına](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>X. 509.440 otomatik olarak imzalanan sertifikalarla cihaz kimliği oluşturma

X. 509.440 otomatik imzalı kimlik doğrulaması için bazen parmak izi kimlik doğrulaması olarak da adlandırılan, IoT cihazınıza yerleştirilecek yeni sertifikalar oluşturmanız gerekir. Bu sertifikaların kimlik doğrulaması için IoT Hub paylaştığınız bir parmak izi vardır. 

Bu senaryoyu test etmenin en kolay yolu, bir [IoT Edge cihazını, saydam bir ağ geçidi olarak davranacak şekilde yapılandırma](how-to-create-transparent-gateway.md)bölümünde sertifika oluşturmak için kullandığınız makinenin aynısını kullanmaktır. Bu makine, IoT cihaz sertifikalarını oluşturmak için doğru araç, kök CA sertifikası ve ara CA sertifikası ile önceden ayarlanmalıdır. Son sertifikaları ve bunların özel anahtarlarını, daha sonra aşağı akış cihazınıza kopyalayabilirsiniz. Ağ Geçidi makalesindeki adımları izleyerek, makinenizde OpenSSL 'yi ayarlar ve sonra sertifika oluşturma betiklerine erişmek için IoT Edge depoyu klonlanır. Daha sonra, sertifikaları tutmak için **\<WRKDIR >** çağırdığımız bir çalışma dizini yaptınız. Varsayılan sertifikalar geliştirme ve test için, bu nedenle yalnızca son 30 gün için tasarlanmıştır. Bir kök CA sertifikası ve ara sertifika oluşturmuş olmanız gerekir. 

1. Çalışma dizininize bir bash veya PowerShell penceresinde gidin. 

2. Aşağı akış cihazı için iki sertifika (birincil ve ikincil) oluşturun. Cihazınızın adını ve ardından birincil veya ikincil etiketi sağlayın. Bu bilgiler, birden çok cihaza yönelik sertifikaların izini tutabilmeniz için dosyaları adlandırmak üzere kullanılır. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. 40 onaltılık bir karakter dizesi olan her bir sertifikadan SHA1 parmak izini (IoT Hub arabiriminde parmak izi denir) alın. Sertifikayı görüntülemek ve parmak izini bulmak için aşağıdaki OpenSSL komutunu kullanın:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Azure portal IoT Hub 'ınıza gidin ve aşağıdaki değerlerle yeni bir IoT cihaz kimliği oluşturun: 

   * Kimlik doğrulama türü olarak **X. 509.440 otomatik olarak imzalanan** ' ı seçin.
   * Cihazınızın birincil ve ikincil sertifikalarından kopyaladığınız onaltılık dizeleri yapıştırın.
   * **Bir üst cihaz ayarla** ' yı seçin ve bu aşağı akış cihazının bağlanacağı IoT Edge ağ geçidi cihazını seçin. Bir aşağı akış cihazının X. 509.440 kimlik doğrulaması için bir üst cihaz gereklidir. 

   ![Portalda X. 509.440 otomatik imzalı kimlik doğrulaması ile cihaz KIMLIĞI oluşturma](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Aşağıdaki dosyaları aşağı akış cihazınızdaki herhangi bir dizine kopyalayın:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Bu dosyalara IoT Hub bağlanan yaprak cihaz uygulamalarında başvuracağız. Sertifika dosyalarını taşımak için [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) veya [Güvenli kopya Protokolü](https://www.ssh.com/ssh/scp/) gibi bir işlev gibi bir hizmet kullanabilirsiniz.

Aynı cihaz oluşturma işlemini gerçekleştirmek için [Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension) 'nı kullanabilirsiniz. Aşağıdaki örnek, X. 509.440 otomatik imzalı kimlik doğrulaması ile yeni bir IoT cihazı oluşturur ve bir üst cihaz atar: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Cihaz oluşturma, sertifika oluşturma ve üst ve alt yönetim için Azure CLı komutları hakkında daha fazla bilgi için bkz. [az IoT Hub cihaz kimliği](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) komutları için başvuru içeriği.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>X. 509.440 CA imzalı sertifikalarla cihaz kimliği oluşturma

X. 509.440 sertifika yetkilisi (CA) imzalı kimlik doğrulaması için, IoT cihazınız için sertifikaları imzalamak üzere kullandığınız IoT Hub kayıtlı bir kök CA sertifikasına ihtiyacınız vardır. Kök CA sertifikası veya ara sertifikalarının herhangi biri tarafından karşılaşılan bir sertifikayı kullanan herhangi bir cihazın kimlik doğrulamasına izin verilir. 

Bu bölüm, [Azure IoT Hub 'ınızda X. 509.440 güvenliğini ayarlama](../iot-hub/iot-hub-security-x509-get-started.md)IoT Hub makalesinde açıklanan yönergeleri temel alır. Bir ağ geçidi üzerinden bağlanan bir aşağı akış cihazını ayarlamak için hangi değerlerin kullanılacağını bilmek için bu bölümdeki adımları izleyin. 

Bu senaryoyu test etmenin en kolay yolu, bir [IoT Edge cihazını, saydam bir ağ geçidi olarak davranacak şekilde yapılandırma](how-to-create-transparent-gateway.md)bölümünde sertifika oluşturmak için kullandığınız makinenin aynısını kullanmaktır. Bu makine, IoT cihaz sertifikalarını oluşturmak için doğru araç, kök CA sertifikası ve ara CA sertifikası ile önceden ayarlanmalıdır. Son sertifikaları ve bunların özel anahtarlarını, daha sonra aşağı akış cihazınıza kopyalayabilirsiniz. Ağ Geçidi makalesindeki adımları izleyerek, makinenizde OpenSSL 'yi ayarlar ve sonra sertifika oluşturma betiklerine erişmek için IoT Edge depoyu klonlanır. Daha sonra, sertifikaları tutmak için **\<WRKDIR >** çağırdığımız bir çalışma dizini yaptınız. Varsayılan sertifikalar geliştirme ve test için, bu nedenle yalnızca son 30 gün için tasarlanmıştır. Bir kök CA sertifikası ve ara sertifika oluşturmuş olmanız gerekir. 

1. *Azure IoT Hub 'ınızda x. 509.952 güvenliğini ayarlama*konusunun [IoT Hub 'ınıza x. 509.952 CA sertifikalarını kaydetme](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) bölümündeki yönergeleri izleyin. Bu bölümde, aşağıdaki adımları gerçekleştirirsiniz: 

   1. Kök CA sertifikasını karşıya yükleyin. Saydam ağ geçidi makalesinde oluşturduğunuz sertifikaları kullanıyorsanız, kök sertifika dosyası olarak **\<WRKDIR >/certs/Azure-iot-test-only.root.ca.cert.pem** yükleyin. 
   2. Bu kök CA sertifikasına sahip olduğunuzu doğrulayın. \<WRKDIR > sertifika araçlarıyla birlikte bulunan ' i doğrulayabilirsiniz. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. *Azure IoT Hub 'ınızda x. 509.440 güvenliği ayarlama*konusunun [IoT Hub 'ınız için x. 509.952 cihazı oluşturma](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) bölümündeki yönergeleri izleyin. Bu bölümde, aşağıdaki adımları gerçekleştirirsiniz: 

   1. Yeni bir cihaz ekleyin. **CIHAZ kimliği**için küçük harfli bir ad girin ve **X. 509.440 CA imzalı**kimlik doğrulama türünü seçin. 
   2. Bir üst cihaz ayarlayın. Aşağı akış cihazları için **bir üst cihaz ayarla** ' yı seçin ve IoT Hub bağlantısını sağlayacak IoT Edge ağ geçidi cihazını seçin. 

3. Aşağı akış cihazınız için bir sertifika zinciri oluşturun. IoT Hub için karşıya yüklediğiniz aynı kök CA sertifikasını kullanın ve bu zinciri oluşturun. Portalda cihaz kimliğinize verdiğiniz küçük harfli cihaz KIMLIĞINI kullanın.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Aşağıdaki dosyaları aşağı akış cihazınızdaki herhangi bir dizine kopyalayın: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Bu dosyalara IoT Hub bağlanan yaprak cihaz uygulamalarında başvuracağız. Sertifika dosyalarını taşımak için [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) veya [Güvenli kopya Protokolü](https://www.ssh.com/ssh/scp/) gibi bir işlev gibi bir hizmet kullanabilirsiniz.

Aynı cihaz oluşturma işlemini gerçekleştirmek için [Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension) 'nı kullanabilirsiniz. Aşağıdaki örnek, X. 509.952 CA imzalı kimlik doğrulaması ile yeni bir IoT cihazı oluşturur ve bir üst cihaz atar: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Cihaz oluşturma ve üst/alt yönetim için Azure CLı komutları hakkında daha fazla bilgi için bkz. [az IoT Hub cihaz kimliği](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) komutları için başvuru içeriği.


### <a name="connect-to-iot-hub-through-a-gateway"></a>Bir ağ geçidi üzerinden IoT Hub bağlanma

Her Azure IoT SDK 'Sı X. 509.440 kimlik doğrulamasını biraz farklı işler. Ancak, aynı işlem, X ile IoT Hub için düzenli IoT cihazlarının kimlik doğrulaması yapmak için kullanılır. 509.440 sertifikaları da aşağı akış cihazları için de geçerlidir. Tek fark, bağlantıyı yönlendirmek için ağ geçidi cihazına bir işaretçi eklemeniz ya da çevrimdışı senaryolarda IoT Hub adına kimlik doğrulamasını işlemek için gerekli olacaktır. Genel olarak, tüm IoT Hub cihazlar için aynı X. 509.952 kimlik doğrulama adımlarını izleyebilir, sonra bağlantı dizesindeki **ana bilgisayar adının** değerini ağ geçidi cihazınızın ana bilgisayar adı olacak şekilde değiştirebilirsiniz. 

Aşağıdaki bölümlerde, farklı SDK dilleri için bazı örnekler gösterilmektedir. 

>[!IMPORTANT]
>Aşağıdaki örneklerde IoT Hub SDK 'larının cihazların kimliğini doğrulamak için sertifikaları nasıl kullanacağı gösterilmektedir. Bir üretim dağıtımında, özel veya SAS anahtarları gibi tüm gizli dizileri bir donanım güvenli modülünde (HSM) depolamanız gerekir. 

#### <a name="net"></a>.NET

X. 509.440 sertifikalarıyla C# IoT Hub kimlik doğrulaması yapan bir program örneği için bkz. [Azure IoT Hub 'ınızda x. 509.440 güvenliğini ayarlama](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Bu örneğin önemli satırlarından bazıları, kimlik doğrulama işlemini göstermek için buraya eklenmiştir.

DeviceClient örneğiniz için ana bilgisayar adını bildirirken, IoT Edge ağ geçidi cihazının ana bilgisayar adını kullanın. Ana bilgisayar adı, ağ geçidi cihazının config. YAML dosyasında bulunabilir. 

IoT Edge git deposu tarafından sunulan test sertifikalarını kullanıyorsanız, sertifikaların anahtarı **1234**olur.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

X. 509.440 sertifikalarıyla IoT Hub kimlik doğrulaması yapan bir C programı örneği için bkz. C IoT SDK 'sının [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) örneği. Bu örneğin önemli satırlarından bazıları, kimlik doğrulama işlemini göstermek için buraya eklenmiştir.

Aşağı akış cihazınız için bağlantı dizesi tanımlarken, **ana bilgisayar adı** parametresi için IoT Edge Gateway cihazının ana bilgisayar adını kullanın. Ana bilgisayar adı, ağ geçidi cihazının config. YAML dosyasında bulunabilir. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

X. 509.440 sertifikalarıyla IoT Hub kimlik doğrulaması yapan bir Node. js programı örneği için bkz. Node. js IoT SDK 'nın [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) örneği. Bu örneğin önemli satırlarından bazıları, kimlik doğrulama işlemini göstermek için buraya eklenmiştir.

Aşağı akış cihazınız için bağlantı dizesi tanımlarken, **ana bilgisayar adı** parametresi için IoT Edge Gateway cihazının ana bilgisayar adını kullanın. Ana bilgisayar adı, ağ geçidi cihazının config. YAML dosyasında bulunabilir. 

IoT Edge git deposu tarafından sunulan test sertifikalarını kullanıyorsanız, sertifikaların anahtarı **1234**olur.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

Python SDK Şu anda yalnızca x509 sertifikalarını ve dosyalarını kullanarak satır içi tanımlı olmayan dosyalardan değil, dosyaları destekler. Aşağıdaki örnekte, ilgili dosya yolları ortam değişkenlerinde depolanır.

Aşağı akış cihazınız için ana bilgisayar adını tanımlarken, **ana bilgisayar** adı parametresi için IoT Edge Gateway cihazının ana bilgisayar adını kullanın. Ana bilgisayar adı, ağ geçidi cihazının config. YAML dosyasında bulunabilir. 

```python
import os
from azure.iot.device import IoTHubDeviceClient, X509

HOSTNAME = "[IoT Edge Gateway Hostname]"
DEVICE_ID = "[Device ID]"

def iothub_client_init():
    x509 = X509(
        cert_file=os.getenv("X509_CERT_FILE"),
        key_file=os.getenv("X509_KEY_FILE")
    )

    client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=HOSTNAME,
        device_id=DEVICE_ID
    )
)

if __name__ == '__main__':
    iothub_client_init()
```

#### <a name="java"></a>Java

X. 509.440 sertifikalarıyla IoT Hub kimlik doğrulaması yapan bir Java programı örneği için bkz. Java IoT SDK 'nın [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) örneği. Bu örneğin önemli satırlarından bazıları, kimlik doğrulama işlemini göstermek için buraya eklenmiştir.

Aşağı akış cihazınız için bağlantı dizesi tanımlarken, **ana bilgisayar adı** parametresi için IoT Edge Gateway cihazının ana bilgisayar adını kullanın. Ana bilgisayar adı, ağ geçidi cihazının config. YAML dosyasında bulunabilir. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makaleyi tamamlayarak, saydam bir ağ geçidi olarak çalışan bir IoT Edge cihazının ve IoT Hub ile kaydedilmiş bir aşağı akış cihazının olması gerekir. Sonra, ağ geçidi cihazına güvenmek ve ona ileti göndermek için aşağı akış cihazlarınızı yapılandırmanız gerekir. Daha fazla bilgi için bkz. bir [aşağı akış cihazını Azure IoT Edge bir ağ geçidine bağlama](how-to-connect-downstream-device.md).
