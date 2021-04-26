---
title: Öğretici-Azure IoT Hub için otomatik olarak imzalanan sertifikalar oluşturmak için OpenSSL kullanma | Microsoft Docs
description: Öğretici-Azure IoT Hub için otomatik olarak imzalanan X. 509.440 sertifikaları oluşturmak için OpenSSL kullanma
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
ms.openlocfilehash: 982e402946cbd71d946bc1e316cef99621c536a3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378202"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Öğretici: otomatik olarak imzalanan sertifikalar oluşturmak için OpenSSL kullanma

İki otomatik olarak imzalanan cihaz sertifikası kullanarak IoT Hub bir cihazın kimliğini doğrulayabilirsiniz. Bu, bazen parmak izi kimlik doğrulaması olarak adlandırılır çünkü sertifikalar, IoT Hub 'ına gönderdiğiniz parmak izleri (karma değerler) içerir. Aşağıdaki adımlarda, iki otomatik olarak imzalanan sertifika oluşturma hakkında bilgi verilmiştir.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>1. adım-ilk sertifika için bir anahtar oluşturun

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>2. adım-ilk sertifika için bir CSR oluşturun

İstendiğinde cihaz KIMLIĞINI belirttiğinizden emin olun.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>3. adım-CSR 'yi denetleme

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>4. adım-otomatik imza sertifikası 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>5. adım-sertifika 2 için bir anahtar oluşturma

İstendiğinde, sertifika 1 için kullandığınız cihaz KIMLIĞINI belirtin.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>6. adım-otomatik imza sertifikası 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>7. adım-sertifika için parmak izini alma 1

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>8. adım-sertifika için parmak izini alma 2

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>9. adım-yeni bir IoT cihazı oluşturma

Azure portal IoT Hub gidin ve aşağıdaki özelliklerle yeni bir IoT cihaz kimliği oluşturun:

* İki sertifikanın konu adıyla eşleşen **CIHAZ kimliğini** sağlayın.
* **X. 509.440 otomatik imzalı** kimlik doğrulama türünü seçin.
* Cihazınızın birincil ve ikincil sertifikalarından kopyaladığınız onaltılı dize parmak izlerini yapıştırın. Onaltılı dizelerin iki nokta ayırıcısı olmadığından emin olun.

## <a name="next-steps"></a>Sonraki Adımlar

Sertifikanızın, cihazınızın IoT Hub kimlik doğrulaması yapamadığını öğrenmek için [sertifika kimlik doğrulamasını test etme](tutorial-x509-test-certificate.md) bölümüne gidin.
