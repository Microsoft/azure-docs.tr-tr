---
title: "Hızlı başlangıç: Python kullanarak TPM cihazını Azure cihaz sağlama hizmeti 'ne kaydetme"
description: Azure Hızlı Başlangıç - Python sağlama hizmeti SDK'sını kullanarak TPM cihazını Azure IoT Hub Cihazı Sağlama Hizmeti'ne kaydetme. Bu hızlı başlangıçta bireysel kayıtlar kullanılmaktadır.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 49e7961200a2ab523997350fffa6606d65f60c8c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903393"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Hızlı başlangıç: Python sağlama hizmeti SDK 'sını kullanarak cihaz sağlama hizmeti 'ne IoT Hub TPM cihazı kaydetme
[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Bu adımlar, [Python Sağlama Hizmeti SDK'sını](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) ve örnek Python uygulamasını kullanarak bir TPM cihazı için Azure IoT Hub Cihazı Sağlama Hizmeti'nde programlı bireysel kayıt oluşturmayı gösterir. Python Hizmeti SDK'sı hem Windows hem de Linux makinelerinde çalışır, ancak bu makalede, kayıt işlemlerinin gösterilmesi için Windows geliştirme makinesi kullanılır.

Devam etmeden önce [IoT Hub Cihaz Sağlama Hizmetini Azure portalıyla ayarlama](./quick-setup-auto-provision.md) adımlarını tamamladığınızdan emin olun.


<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Ortamı hazırlama 

1. [Python 2.x veya 3.x](https://www.python.org/downloads/) sürümünü indirip yükleyin. Kurulumunuzun gereksinimine uygun olarak 32 bit veya 64 bit yüklemeyi kullanmaya dikkat edin. Yükleme sırasında istendiğinde, platforma özgü ortam değişkeninize Python'u eklediğinizden emin olun. 

1. Aşağıdaki seçeneklerden birini belirleyin:

    - **Azure IoT Python SDK'sını** oluşturup derleyin. Python paketlerini derlemek için [bu yönergeleri](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) uygulayın. Windows işletim sistemi kullanıyorsanız, Python’dan yerel DLL’lerin kullanımına olanak tanımak için [Visual C++ yeniden dağıtılabilir paketini](https://www.microsoft.com/download/confirmation.aspx?id=48145) ayrıca yükleyin.

    - [Python paket yönetim sistemi olan *pip*’i yükleyin](https://pip.pypa.io/en/stable/installing/) veya yükseltin ve şu komut aracılığıyla paketi yükleyin:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Cihazınız için onay anahtarı gerekir. [Sanal cihaz oluşturma ve sağlama](quick-create-simulated-device.md) hızlı başlangıcını izleyerek sanal bir TPM cihazı oluşturduysanız bu cihaz için oluşturulan anahtarı kullanın. Aksi takdirde, SDK ile birlikte verilen aşağıdaki onay anahtarını kullanabilirsiniz:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>Python örnek kodunu değiştirme

Bu bölümde örnek koda TPM cihazınızın sağlama ayrıntılarını nasıl ekleyeceğiniz gösterilir. 

1. Metin düzenleyicisi kullanarak yeni bir **TpmEnrollment.py** dosyası oluşturun.

1. Aşağıdaki `import` deyimlerini ve değişkenlerini **TpmEnrollment.py** dosyasının başlangıcına ekleyin. `dpsConnectionString` öğesini, **Azure portal** üzerinde **Cihaz Sağlama Hizmeti**’nizdeki **Paylaşılan erişim ilkeleri** altında bulunan bağlantı dizenizle değiştirin. `endorsementKey` öğesini, daha önce [Ortamı hazırlama](quick-enroll-device-tpm-python.md#prepareenvironment) konusunda belirtilen değerle değiştirin. Son olarak, benzersiz bir `registrationid` oluşturun ve yalnızca küçük harf alfasayısal karakterler ve kısa çizgiler içerdiğinden emin olun.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. Grup kaydı oluşturmasını uygulamak için aşağıdaki işlevi ve işlev çağrısını ekleyin:
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. **TpmEnrollment.py** dosyasını kaydedip kapatın.
 

## <a name="run-the-sample-tpm-enrollment"></a>Örnek TPM kaydını çalıştırma

1. Bir komut istemi açın ve betiği çalıştırın.

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. Kaydın başarılı olup olmadığını görmek için çıktıyı gözlemleyin.

1. Azure portalında sağlama hizmetinize gidin. **Kayıtları yönetme**'ye tıklayın. TPM cihazınızın, daha önce oluşturulmuş **adıyla**Bireysel Kayıtlar`registrationid` sekmesi altında göründüğüne dikkat edin. 

    ![Portalda TPM kaydının başarılı olup olmadığını doğrulama](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Kaynakları temizleme
Java hizmeti örneğini keşfetmeye devam etmeyi planlıyorsanız, bu Hızlı Başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç ile oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın:

1. Makinenizdeki Python örnek çıkış penceresini kapatın.
1. Sanal TPM cihazı oluşturduysanız, TPM simülatörü penceresini kapatın.
1. Azure portal cihaz sağlama hizmetine gidin, kayıtları **Yönet**' e tıklayın ve sonra **bireysel** kayıtlar sekmesini seçin. bu hızlı başlangıç Ile oluşturduğunuz kayıt girişinin *kayıt kimliğini* seçin ve dikey pencerenin en üstündeki **Sil** düğmesine tıklayın.  


## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta bir TPM cihazı için programlı olarak bireysel kayıt girişi ve isteğe bağlı olarak makinenizde bir TPM sanal cihazı oluşturdunuz ve Azure IOT Hub cihaz sağlama hizmeti ile IOT hub'ınıza sağladınız. Cihaz sağlama hakkında ayrıntılı bilgi edinmek için Azure portalında Cihaz Sağlama Hizmeti ayarları öğreticisine geçin.

> [!div class="nextstepaction"]
> [Azure IoT Hub Cihazı Sağlama Hizmeti öğreticileri](./tutorial-set-up-cloud.md)
