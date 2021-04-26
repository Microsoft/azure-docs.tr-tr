---
title: Azure IoT Central ile X. 509.440 sertifikalarını al
description: IoT Central uygulamanızla X. 509.440 sertifikalarını alma
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c25af944b4c748307f4f974ca8616ecc9f7b28c3
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714533"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>IoT Central uygulamasında X. 509.440 cihaz sertifikalarını alma

IoT çözümünüzün yaşam döngüsü boyunca sertifikaları almanız gerekir. Sıralı sertifikaların temel nedenlerinden ikisi bir güvenlik ihlali ve sertifika süre sonları olacaktır.

Güvenlik ihlaline sahipseniz, çalışan sertifikalar sisteminizin güvenliğini sağlamaya yardımcı olmak için en iyi güvenlik uygulamasıdır. [Ihlal metodolojinin](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)bir parçası olarak Microsoft, önleyici ölçülerle birlikte reaktif güvenlik işlemlerine sahip olma gereksinimini ortadan kaldırır. Cihaz sertifikalarınızın, bu güvenlik işlemlerinin bir parçası olarak eklenmesi gerekir. Sertifikalarınızı alacağınız sıklık, çözümünüzün güvenlik ihtiyaçlarına bağlı olarak değişir. Son derece hassas verileri içeren çözümleri olan müşteriler, sertifikaları her iki yılda bir kez sunarken, her gün sertifika alabilir.


## <a name="obtain-new-x509-certificates"></a>Yeni X. 509.952 sertifikaları edinin

OpenSSL gibi bir araç kullanarak kendi X. 509.952 sertifikalarınızı oluşturabilirsiniz. Bu yaklaşım, X. 509.440 sertifikalarını test etmek için idealdir, ancak güvenlik konusunda birkaç güvence sağlar. Bu yaklaşımı yalnızca kendi CA sağlayıcınız olarak davranmaya hazırlanmadığınız sürece test için kullanın.

## <a name="enrollment-groups-and-security-breaches"></a>Kayıt grupları ve güvenlik ihlaller

Bir güvenlik ihlaline yanıt olarak bir grup kaydını güncelleştirmek için, geçerli sertifikayı hemen güncelleştiren aşağıdaki yaklaşımı kullanmanız gerekir:

1. Sol bölmedeki **Yönetim**  ' e gidin ve **cihaz bağlantısı**' nı seçin.

2. **Kayıt grupları**' nı seçin ve listeden Grup adını seçin.

3. Sertifika güncelleştirmesi için **birincil Yönet** ' i veya **ikincili Yönet**' i seçin.

4. Kayıt grubuna kök X. 509.440 sertifikası ekleyin ve doğrulayın.

   Her ikisi de tehlikeye girerse, birincil ve ikincil sertifikalar için bu adımları uygulayın.

## <a name="enrollment-groups-and-certificate-expiration"></a>Kayıt grupları ve sertifika süre sonu

Sertifika süre sonlarını işlemek için bir sertifika alıyorsanız, geçerli sertifikayı hemen güncelleştirmek için aşağıdaki yaklaşımı kullanın:

1. Sol bölmedeki **Yönetim**  ' e gidin ve **cihaz bağlantısı**' nı seçin.

2. **Kayıt grupları**' nı seçin ve listeden Grup adını seçin.

3. Sertifika güncelleştirmesi için, **birincil Yönet**' i seçin.

4. Kayıt grubuna kök X. 509.440 sertifikası ekleyin ve doğrulayın.

5. Daha sonra ikincil sertifikanın süresi dolduğunda, geri dönüp ikincil sertifikayı güncelleştirin.

## <a name="individual-enrollments-and-security-breaches"></a>Bireysel kayıtlar ve güvenlik ihlalleri

Bir güvenlik ihlaline yanıt olarak sertifika alıyorsanız, geçerli sertifikayı hemen güncelleştirmek için aşağıdaki yaklaşımı kullanın:

1. **Cihazlar**' ı seçin ve cihazı seçin.

2. **Bağlan**' ı seçin ve yöntemi **bireysel kayıt** olarak bağla ' yı seçin

3. Bir mekanizma olarak **Sertifikalar (X. 509.440)** seçeneğini belirleyin.

    ![Bireysel kayıtları yönetme](./media/how-to-roll-x509-certificates/certificate-update.png)

4. Sertifika güncelleştirmesi için, kayıt girişi için yüklenecek yeni sertifikayı seçmek üzere klasör simgesini seçin. **Kaydet**’i seçin.

    Her ikisi de tehlikeye girerse, birincil ve ikincil sertifikalar için bu adımları izleyin

## <a name="individual-enrollments-and-certificate-expiration"></a>Bireysel kayıtlar ve sertifika süre sonu

Sertifika süre sonlarını işlemek için bir sertifika alıyorsanız, sağlama girişiminde bulunan cihazların kapalı kalma süresini azaltmak için ikincil sertifika yapılandırmasını aşağıdaki şekilde kullanmanız gerekir.

İkincil sertifikanın süre sonu yaklaştığında ve alınması gerektiğinde, birincil yapılandırmayı kullanarak döndürebilirsiniz. Bu şekilde birincil ve ikincil Sertifikalar arasında döndürme, sağlamaya çalışan cihazların kapalı kalma süresini azaltır.

1. **Cihazlar**' ı seçin ve cihazı seçin.

2. **Bağlan**' ı seçin ve yöntemi **bireysel kayıt** olarak bağla ' yı seçin

3. Bir mekanizma olarak **Sertifikalar (X. 509.440)** seçeneğini belirleyin.

    ![Bireysel kayıtları yönetme](./media/how-to-roll-x509-certificates/certificate-update.png)

4. İkincil sertifika güncelleştirmesi için, kayıt girişi için yüklenecek yeni sertifikayı seçmek üzere klasör simgesini seçin. **Kaydet**’i seçin.

5. Daha sonra birincil sertifikanın süresi dolduğunda, geri dönüp ilgili birincil sertifikayı güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure IoT Central uygulamanızda X. 509.440 sertifikalarının nasıl alınacağını öğrendiğinize göre [azure IoT Central 'ye](concepts-get-connected.md)bağlanabilirsiniz.


