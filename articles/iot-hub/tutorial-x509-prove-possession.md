---
title: Öğretici-Azure IoT Hub CA sertifikalarının sahipliğini kanıtlayın | Microsoft Docs
description: Öğretici-Azure IoT Hub CA sertifikanız olduğunu kanıtlayın
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
ms.openlocfilehash: b7740fa1f6a54dcfcc1181dddedcdd5fdb50402c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378236"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Öğretici: CA sertifikasını yerine döndürme

Kök sertifika yetkilisi (CA) sertifikanızı veya alt CA sertifikanızı IoT Hub 'ınıza yükledikten sonra, sertifikaya sahip olduğunuzu kanıtlamanız gerekir:

1. Azure portal, ıothub adresine gidin ve **ayarlar > sertifikalar**' ı seçin.

2. Yeni bir CA sertifikası eklemek için **Ekle** ' yi seçin.

3. **Sertifika adı** alanına bir görünen ad girin ve eklenecek pek sertifikasını seçin.

4. **Kaydet**’i seçin. Sertifikanız, sertifika listesinde **doğrulanmamış** durumuyla gösterilir. Bu doğrulama işlemi, sertifikaya sahip olduğunuzu kanıtlayacaktır.

5. **Sertifika ayrıntıları** iletişim kutusunu görüntülemek için sertifikayı seçin.

6. İletişim kutusunda **doğrulama kodu oluştur** ' u seçin.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Sertifika ayrıntıları iletişim kutusu}":::

7. Doğrulama kodunu panoya kopyalayın. Doğrulama kodunu sertifika konusu olarak ayarlamanız gerekir. Örneğin, doğrulama kodu 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3 ise, bir sonraki adımda gösterildiği gibi bunu sertifikanızın konusu olarak ekleyin.

8. Doğrulama sertifikası oluşturmak için üç yol vardır:

    * Microsoft tarafından sağlanan PowerShell betiğini kullanıyorsanız `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` adlı bir sertifika oluşturmak için öğesini çalıştırın `VerifyCert4.cer` . Daha fazla bilgi için bkz. [Microsoft tarafından sağlanan betikleri kullanma](tutorial-x509-scripts.md).

    * Microsoft tarafından sağlanan Bash betiğini kullanıyorsanız `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` adlı bir sertifika oluşturmak için öğesini çalıştırın `verification-code.cert.pem` . Daha fazla bilgi için bkz. [Microsoft tarafından sağlanan betikleri kullanma](tutorial-x509-scripts.md).

    * Sertifikalarınızı oluşturmak için OpenSSL kullanıyorsanız, öncelikle bir özel anahtar ve ardından bir sertifika imzalama isteği (CSR) oluşturmanız gerekir:

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Ardından, kök CA Yapılandırma dosyasını (aşağıda gösterilmiştir) veya alt CA Yapılandırma dosyasını ve CSR 'yi kullanarak bir sertifika oluşturun.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Daha fazla bilgi için bkz. [Test sertifikaları oluşturmak Için OpenSSL kullanma](tutorial-x509-openssl.md).

10. **Sertifika ayrıntıları** görünümündeki yeni sertifikayı seçin.

11. Sertifika karşıya yüklendikten sonra **Doğrula**' yı seçin. CA sertifikası durumunun **doğrulandı** olarak değiştirilmesi gerekir.
