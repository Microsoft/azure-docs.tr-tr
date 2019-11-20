---
title: Geliştirmeyi basitleştirmek için Azure IoT Hub cihaz sağlama hizmeti SDK 'lerinde sunulan araçları kullanın
description: Bu belgede, geliştirme için Azure IoT Hub cihaz sağlama hizmeti SDK 'lerinde sunulan araçlar incelenir
author: robinsh
ms.author: robinsh
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 4fd4913776a7d21405f62a28d452bd50cd22f046
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882999"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Sağlama geliştirmeyi basitleştirmek için SDK 'larda sunulan araçları kullanma
Cihaz sağlama hizmeti IoT Hub, güvenli ve ölçeklenebilir bir şekilde, tam zamanında [Otomatik sağlama](concepts-auto-provisioning.md) sağlayan, sağlama sürecini basitleştirir.  X. 509.440 sertifikası veya Güvenilir Platform Modülü (TPM) biçiminde güvenlik kanıtlama gereklidir.  Microsoft, IoT dağıtımını güvenli hale getirmek için [diğer güvenlik donanımı ortaklarıyla](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) de işbirliği yapıyor. Donanım güvenlik gereksinimini anlamak geliştiriciler için oldukça zor olabilir. Geliştiricilerin sağlama hizmeti ile iletişim kuran istemcileri yazmak için kullanışlı bir katman kullanabilmesi için bir Azure IoT sağlama hizmeti SDK 'Sı kümesi sunulmaktadır. SDK 'lar Ayrıca yaygın senaryolar için örnekler ve geliştirmede güvenlik kanıtlamasını basitleştirecek bir araç kümesi sağlar.

## <a name="trusted-platform-module-tpm-simulator"></a>Güvenilir Platform Modülü (TPM) simülatör
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) , platformun kimliğini doğrulamak için anahtarları güvenli bir şekilde depolamak üzere bir standarda başvurabilir veya standart uygulayan modüllerle etkileşim kurmak için kullanılan g/ç arabirimine başvurabilir. TPMs ayrık donanım, tümleşik donanım, bellenim tabanlı veya yazılım tabanlı olarak bulunabilir.  Üretimde TPM, ayrı donanım, tümleşik donanım ya da bellenim tabanlı olarak cihazda bulunur. Test aşamasında, geliştiricilere yazılım tabanlı bir TPM simülatörü sağlanır.  Bu Benzetici, şimdilik yalnızca Windows platformunda geliştirme için kullanılabilir.

TPM simülatörünü kullanma adımları şunlardır:
1. [Geliştirme ortamını hazırlayın](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) ve GitHub deposunu kopyalayın:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Altındaki ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```TPM simülatörü klasörüne gidin.
3. Cihaz sağlamak için herhangi bir istemci uygulamasını çalıştırmadan önce simülatör. exe ' yi çalıştırın.
4. Kayıt KIMLIĞI ve onay anahtarı almak için sağlama işlemi boyunca benzeticinin arka planda çalışmasına izin verin.  Her iki değer de yalnızca bir çalıştırmanın bir örneği için geçerlidir.

## <a name="x509-certificate-generator"></a>X. 509.440 sertifika Oluşturucu
[X. 509.440 sertifikaları](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) , üretimi ölçeklendirmek ve cihaz sağlamayı basitleştirmek için bir kanıtlama mekanizması olarak kullanılabilir.  X. 509.440 sertifikası almanın [birkaç yolu](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) vardır:
* Üretim ortamında, bir ortak kök sertifika yetkilisinden bir X. 509.440 CA sertifikası satın almayı öneririz.
* Test ortamı için şunu kullanarak bir X. 509.952 kök sertifikası veya X. 509.440 sertifika zinciri oluşturabilirsiniz:
    * OpenSSL Sertifika oluşturma için betikleri kullanabilirsiniz:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell veya bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Cihaz kimliği bileşim altyapısı (zar) öykünücüsü: ZAR, TLS protokolüne ve X. 509.440 istemci sertifikalarına dayalı şifreleme cihaz kimliği ve kanıtlama için kullanılabilir.  ZAR ile cihaz kimliği hakkında daha fazla [bilgi edinin](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) .

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>ZAR öykünücüsü ile X. 509.440 sertifika oluşturucuyu kullanma
SDK 'lar, [Java SDK 'sında](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator)yer alan bir X. 509.440 sertifika oluşturucuyu, bir zar öykünücüsünü sağlar.  Bu Oluşturucu platformlar arası bir şekilde çalışmaktadır.  Oluşturulan sertifika diğer dillerde geliştirme için kullanılabilir.

Şu anda, zar öykünücüsü bir kök sertifika, bir ara sertifika, yaprak sertifikası ve ilişkili özel anahtar verir.  Ancak, kök sertifika veya ara sertifika ayrı bir yaprak sertifikayı imzalamak için kullanılamaz.  Birden çok cihazın yaprak sertifikalarını imzalamak için bir imzalama sertifikasının kullanıldığı grup kaydı senaryosunu test etmek istiyorsanız, bir sertifika zinciri oluşturmak için OpenSSL kullanabilirsiniz.

Bu oluşturucuyu kullanarak X. 509.440 sertifikası oluşturmak için:
1. [Geliştirme ortamını hazırlayın](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) ve GitHub deposunu kopyalayın:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Kökü Azure-IoT-SDK-Java olarak değiştirin.
3. Tüm ```mvn install -DskipTests=true``` gerekli paketleri indirmek ve SDK 'yı derlemek için Çalıştır
4. ' De ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```X. 509.440 sertifika Oluşturucu köküne gidin.
5. İle derleme```mvn clean install```
6. Aşağıdaki komutları kullanarak aracı çalıştırın:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. İstendiğinde sertifikalarınız için _Common Name_ (Ortak Ad) girebilirsiniz.
8. Araç yerel olarak bir **Istemci sertifikası**, **Istemci sertifikası özel anahtarı**, **Ara Sertifika**ve **kök sertifika**oluşturur.

**Istemci sertifikası** , cihazdaki yaprak sertifikadır.  **Istemci sertifikası** ve Ilişkili **Istemci sertifikası özel anahtarı** cihaz istemcisinde gereklidir. Seçtiğiniz dile bağlı olarak, bunu istemci uygulamasına yerleştirme mekanizması farklı olabilir.  Daha fazla bilgi için bkz. daha fazla bilgi için bkz. sanal cihaz oluşturma ile sanal cihaz oluşturma sırasında [hızlı başlangıç](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) .

Kök sertifika veya ara, [programlama yoluyla](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) veya [portalı](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments)kullanarak bir kayıt grubu ya da bireysel kayıt oluşturmak için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar
* Azure IoT Hub ve Azure IoT Hub cihaz sağlama hizmeti için [Azure ıOT SDK 'sını]( https://github.com/Azure/azure-iot-sdks) kullanarak geliştirme
