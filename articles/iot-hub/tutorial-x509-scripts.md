---
title: Öğretici-Azure IoT Hub x. 509.440 test sertifikaları oluşturmak için Microsoft betikleri kullanma | Microsoft Docs
description: Öğretici-Azure IoT Hub CA ve cihaz sertifikaları oluşturmak için özel betikler kullanın
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: ff4b63f49a87dd9ca6b0ef458bdcf1c285a34a18
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378219"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Öğretici: test sertifikaları oluşturmak için Microsoft tarafından sağlanan betikleri kullanma

Microsoft, kendi X. 509.440 sertifikalarınızı oluşturmayı ve IoT Hub kimlik doğrulamasını anlamanıza yardımcı olmak için PowerShell ve Bash betikleri sağlar. Betikler bir GitHub [deposunda](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)bulunur. Yalnızca tanıtım amaçlı olarak sağlanır. Tarafından oluşturulan sertifikalar, üretim için kullanılmamalıdır. Sertifikalar sabit kodlanmış parolalar ("1234") içerir ve 30 gün sonra sona erer. Bir üretim ortamında, sertifika oluşturma ve ömür yönetimi için kendi en iyi uygulamalarınızı kullanmanız gerekir.

## <a name="powershell-scripts"></a>PowerShell komut dosyaları

### <a name="step-1---setup"></a>1. adım-kurulum

Windows için OpenSSL alın. <https://www.openssl.org/docs/faq.html#MISC4>İndirme veya kaynaktan derleme yerleri için bkz <https://www.openssl.org/source/> .. Ardından ön betikleri çalıştırın:

1. Bu GitHub [deposundan](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) betikleri, çalışmak istediğiniz yerel dizine kopyalayın. Tüm dosyalar, bu dizinin alt öğesi olarak oluşturulacak.

1. PowerShell 'i yönetici olarak başlatın.

1. Betikleri yüklediğiniz dizine geçin.

1. Komut satırında, ortam değişkenini `$ENV:OPENSSL_CONF` OpenSSL yapılandırma dosyasının (OpenSSL. CNF) bulunduğu dizine ayarlayın.

1. `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`PowerShell 'in betikleri çalıştırabilmesi için ' i çalıştırın.

1. `. .\ca-certs.ps1` öğesini çalıştırın. Bu, betiğin işlevlerini PowerShell genel ad alanına getirir.

1. `Test-CACertsPrerequisites` öğesini çalıştırın. PowerShell, sertifikaları yönetmek için Windows sertifika deposunu kullanır. Bu komut, daha sonra mevcut sertifikalarla ad çarpışmalarını ve OpenSSL 'in doğru şekilde yüklendiğini doğrular.

### <a name="step-2---create-certificates"></a>2. adım-sertifika oluşturma

`New-CACertsCertChain [ecc|rsa]` öğesini çalıştırın. CA sertifikaları için ECC önerilir, ancak gerekli değildir. Bu betik, aşağıdaki CA ve ara sertifikalarla dizin ve Windows sertifika deponuzi güncelleştirir:

* intermediate1. pek
* intermediate2. pek
* intermediate3. pek
* RootCA. cer
* RootCA. pek

Betiği çalıştırdıktan sonra, yeni CA sertifikasını (RootCA. ped) IoT Hub ekleyin:

1. IoT Hub gidin ve sertifikalara gidin.

1. **Add (Ekle)** seçeneğini belirleyin.

1. CA sertifikası için bir görünen ad girin.

1. CA sertifikasını karşıya yükleyin.

1. **Kaydet**’i seçin.

### <a name="step-3---prove-possession"></a>3. adım-kanıtlayan

CA sertifikanızı IoT Hub yüklediğinize göre gerçekten sahip olduğunuzu kanıtlamanız gerekir:

1. Yeni CA sertifikasını seçin.

1. **Sertifika ayrıntıları** Iletişim kutusunda **doğrulama kodu oluştur** ' u seçin. Daha fazla bilgi için bkz. [CA sertifikasını kanıtlayın](tutorial-x509-prove-possession.md).

1. Doğrulama kodunu içeren bir sertifika oluşturun. Örneğin, doğrulama kodu ise, `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` çalışma dizininizde konuyu içeren yeni bir sertifika oluşturmak için aşağıdakileri çalıştırın `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . Betik adlı bir sertifika oluşturur `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. `VerifyCert4.cer` **Sertifika ayrıntıları** iletişim kutusunda IoT Hub yükleyin.

1. **Doğrula** seçeneğini belirleyin.

### <a name="step-4---create-a-new-device"></a>4. adım-yeni cihaz oluşturma

IoT Hub için bir cihaz oluşturun:

1. IoT Hub **IoT cihazları** bölümüne gidin.

1. KIMLIĞI olan yeni bir cihaz ekleyin `mydevice` .

1. Kimlik doğrulaması için, **X. 509.440 CA imzalı** öğesini seçin.

1. `New-CACertsDevice mydevice`Yeni bir cihaz sertifikası oluşturmak için ' i çalıştırın. Bu, çalışma dizininizde aşağıdaki dosyaları oluşturur:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>5. adım-cihaz sertifikanızı test etme

Cihaz sertifikanızın IoT Hub kimlik doğrulaması yapamadığını öğrenmek için [sertifika kimlik doğrulamasını test etme](tutorial-x509-test-certificate.md) bölümüne gidin. Sertifikanızın PFX sürümüne ihtiyacınız olacak `mydevice.pfx` .

### <a name="step-6---cleanup"></a>6. adım-Temizleme

Başlat menüsünde **bilgisayar sertifikalarını Yönet** ' i açın ve  **Sertifikalar-Yerel bilgisayar > kişisel**' e gidin. "Azure IoT CA TestOnly *" tarafından verilen sertifikaları kaldırın. Benzer şekilde, **Güvenilen kök sertifika yetkilisi > sertifikaları ve >ara sertifika yetkilileri sertifikaları >>** uygun sertifikaları kaldırın.

## <a name="bash-scripts"></a>Bash betikleri

### <a name="step-1---setup"></a>1. adım-kurulum

1. Bash 'i başlatın.

1. Üzerinde çalışmak istediğiniz dizine geçin. Tüm dosyalar bu dizinde oluşturulacak.

1. `*.cnf` `*.sh` Çalışma dizininize ve ' a kopyalayın.

### <a name="step-2---create-certificates"></a>2. adım-sertifika oluşturma

1. `./certGen.sh create_root_and_intermediate` öğesini çalıştırın. Bu, **Sertifikalar** dizininde aşağıdaki dosyaları oluşturur:

    * Azure-iot-test-only. zincir. ca. cert. ped
    * Azure-iot-test-only. ara. cert. ped
    * Azure-iot-test-only. root. ca. cert. ped

1. IoT Hub gidin ve **sertifikalara** gidin.

1. **Add (Ekle)** seçeneğini belirleyin.

1. CA sertifikası için bir görünen ad girin.

1. IoT Hub yalnızca CA sertifikasını karşıya yükleyin. Sertifikanın adı `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. **Kaydet**’i seçin.

### <a name="step-3---prove-possession"></a>3. adım-kanıtlayan

1. Önceki adımda oluşturulan yeni CA sertifikasını seçin.

1. **Sertifika ayrıntıları** Iletişim kutusunda **doğrulama kodu oluştur** ' u seçin. Daha fazla bilgi için bkz. [CA sertifikasını kanıtlayın](tutorial-x509-prove-possession.md).

1. Doğrulama kodunu içeren bir sertifika oluşturun. Örneğin, doğrulama kodu ise, `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` çalışma dizininizde konuyu içeren yeni bir sertifika oluşturmak için aşağıdakileri çalıştırın `verification-code.cert.pem` `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` .

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Sertifikayı, **sertifika ayrıntıları** Iletişim kutusunda IoT Hub 'ınıza yükleyin.

1. **Doğrula** seçeneğini belirleyin.

### <a name="step-4---create-a-new-device"></a>4. adım-yeni cihaz oluşturma

IoT Hub 'ınız için bir cihaz oluşturun:

1. IoT Hub IoT cihazları bölümüne gidin.

1. KIMLIĞI olan yeni bir cihaz ekleyin `mydevice` .

1. Kimlik doğrulaması için, **X. 509.440 CA imzalı** öğesini seçin.

1. `./certGen.sh create_device_certificate mydevice`Yeni bir cihaz sertifikası oluşturmak için ' i çalıştırın. Bu `new-device.cert.pem` , çalışma dizininizde dosya adlı iki dosya oluşturur `new-device.cert.pfx` .

### <a name="step-5---test-your-device-certificate"></a>5. adım-cihaz sertifikanızı test etme

Cihaz sertifikanızın IoT Hub kimlik doğrulaması yapamadığını öğrenmek için [sertifika kimlik doğrulamasını test etme](tutorial-x509-test-certificate.md) bölümüne gidin. Sertifikanızın PFX sürümüne ihtiyacınız olacak `new-device.cert.pfx` .

### <a name="step-6---cleanup"></a>6. adım-Temizleme

Bash betiği, çalışma dizininizde yalnızca sertifikalar oluşturduğundan, testi tamamladıktan sonra bunları silmeniz yeterlidir.

## <a name="next-steps"></a>Sonraki Adımlar

Sertifikanızın test edilmesi için sertifika [kimlik doğrulaması](tutorial-x509-test-certificate.md) ' na gidin ve sertifikanızın IoT Hub cihazınızın kimlik doğrulamasını yapabilir.
