---
title: 'Öğretici: Java ve kayıt grupları kullanarak Azure IoT Hub sanal bir X. 509.952 cihazı sağlama'
description: Azure Öğreticisi - IoT Hub Cihazı Sağlama Hizmeti için Java cihaz ve hizmet SDK’sı ile kayıt grupları kullanarak sanal bir X.509 cihazı oluşturma ve sağlama
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: b3cb506b241adab44df490e2fe7f363d35f0f747
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112438"
---
# <a name="tutorial-create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Öğretici: IoT Hub cihaz sağlama hizmeti için Java cihaz ve hizmet SDK 'Sı ve grup kayıtlarını kullanarak sanal bir X. 509.952 cihazı oluşturma ve sağlama

Bu adımlar, Windows işletim sistemi çalıştıran geliştirme makinenizde X.509 cihazının simülasyonunu yapmayı ve kayıt gruplarıyla bir kod örneği kullanarak bu sanal cihazı Cihaz Sağlama Hizmeti ve IoT hub’ınızla bağlamayı gösterir. 

Devam etmeden önce [IoT Hub Cihazı Sağlama Hizmetini Azure portalıyla ayarlama](./quick-setup-auto-provision.md) bölümünde bulunan adımları tamamladığınızdan emin olun.


## <a name="prepare-the-environment"></a>Ortamı hazırlama 

1. Makinenizde [Java SE Development Kit 8](https://aka.ms/azure-jdks) uygulamasının yüklü olduğundan emin olun.

1. [Maven](https://maven.apache.org/install.html)'ı indirip yükleyin.

1. `git` uygulamasının makinenizde yüklü olduğundan ve komut penceresinden erişilebilir ortam değişkenlerine eklendiğinden emin olun. Yüklenecek [ araçlarının son sürümleri için ](https://git-scm.com/download/)Software Freedom Conservancy’nin Git istemci araçlarına`git` bakın. Bunlara yerel Git deponuzla etkileşim kurmak için kullanabileceğiniz bir komut satırı uygulaması olan **Git Bash** dahildir. 

1. Test sertifikalarınızı oluşturmak için aşağıdaki [sertifikaya genel bakış ' ı](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) kullanın.

    > [!NOTE]
    > Bu adım, kaynaktan oluşturulup yüklenebilecek veya [bunun](https://sourceforge.net/projects/openssl/)gibi bir [üçüncü taraf](https://wiki.openssl.org/index.php/Binaries) tarafından yüklenip yüklenebilecek bir [OpenSSL](https://www.openssl.org/)gerektirir. _Kök_, _ara_ ve _cihaz_ sertifikalarınızı önceden oluşturduysanız bu adımı atlayabilirsiniz.
    >

    1. _Kök_ ve _ara_ sertifikalarınızı oluşturmak için ilk iki adımı izleyin.

    1. Azure portala giriş yapın, sol taraftaki menüden **Tüm kaynaklar**’a tıklayın ve sağlama hizmetinizi açın.

        1. Cihaz Sağlama Hizmeti özet dikey penceresinde **Sertifikalar**'ı seçip üstteki **Ekle** düğmesine tıklayın.

        1. **Sertifika Ekle** bölümüne aşağıdaki bilgileri girin:
            - Benzersiz bir sertifika adı girin.
            - Oluşturduğunuz **_RootCA. ped_** dosyasını seçin.
            - Tamamlandığında **Kaydet** düğmesine tıklayın.

           ![Sertifika ekleme](./media/tutorial-group-enrollments/add-certificate.png)

        1. Yeni oluşturulan sertifikayı seçin:
            - **Doğrulama Kodu Oluştur**'a tıklayın. Oluşturulan kodu kopyalayın.
            - Doğrulama adımını çalıştırın. Çalışan PowerShell pencerenize _doğrulama kodunu_ girin veya sağ tıklayarak yapıştırın.  Tuşuna **girin**.
            - Azure portalında yeni oluşturulmuş **_verifyCert4.pem_** dosyasını seçin. **Doğrula**’ya tıklayın.

              ![Sertifika doğrulama](./media/tutorial-group-enrollments/validate-certificate.png)

    1. Cihaz sertifikalarınızı oluşturma ve kaynakları temizleme adımlarını çalıştırarak işlemi tamamlayın.

       > [!NOTE]
       > Cihaz sertifikaları oluştururken, cihazınızın adında yalnızca küçük harf alfasayısal karakterler ve kısa çizgi kullanmaya özen gösterin.
       >


## <a name="create-a-device-enrollment-entry"></a>Cihaz kaydı girişi oluşturma

1. Bir komut istemi açın. Java SDK kod örnekleri için GitHub deposunu kopyalayın:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. İndirilen kaynak kodunda örnek klasörüne gidin: **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** . **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** adlı dosyayı istediğiniz düzenleyicide açıp aşağıdaki ayrıntıları ekleyin:

    1. Portaldan sağlama hizmetinize ait `[Provisioning Connection String]` bilgisini aşağıdaki şekilde ekleyin:

        1. [Azure portalında](https://portal.azure.com) sağlama hizmetinize gidin.

        1. **Paylaşılan erişim ilkeleri**'ni açıp *EnrollmentWrite* iznine sahip bir ilke seçin.

        1. **Birincil anahtar bağlantı dizesini** kopyalayın.

            ![Sağlama bağlantısı dizesini portaldan alma](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. **_ServiceEnrollmentGroupSample.java_** adlı örnek kod dosyasında `[Provisioning Connection String]` yerine **Birincil anahtar bağlantı dizesini** yazın.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Ara imza sertifika dosyanızı bir metin düzenleyicisinde açın. `PUBLIC_KEY_CERTIFICATE_STRING` değerini ara imzalama sertifikanızın değeriyle güncelleştirin.

        Cihaz sertifikalarınızı Bash Shell ile oluşturduysanız, *./certs/Azure-iot-test-only.intermediate.cert.pem* ara sertifika anahtarını içerir. Sertifikalarınız PowerShell ile oluşturulduysa, *./Intermediate1.pem* ara sertifika dosyanız olacaktır.

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
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
        ```

    1. [Azure portalında](https://portal.azure.com) sağlama hizmetinizle bağlantılı IoT hub sayfasına gidin. Hub'ın **Özet** sayfasını açıp **Ana bilgisayar adı**'nı kopyalayın. Bu **Ana bilgisayar adı** değerini *IOTHUB_HOST_NAME* parametresine atayın.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Örnek kodu inceleyin. Bu kod, X.509 cihazları için bir grup kaydı oluşturup bu kaydı güncelleştirir, sorgular ve siler. Portal kaydının başarılı olduğundan emin olmak için _ServiceEnrollmentGroupSample.java_ dosyasının en sonunda bulunan aşağıdaki kod satırlarını geçici olarak açıklama satırı yapın:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. _ServiceEnrollmentGroupSample.java_ dosyasını kaydedin.

1. Bir komut penceresi açıp **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** klasörüne gidin.

1. Şu komutu kullanarak örnek kodu derleyin:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Komut penceresinde aşağıdaki komutları çalıştırarak örneği çalıştırın:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. Kaydın başarılı olup olmadığını görmek için çıktı penceresini izleyin.

    ![Başarılı kayıt](./media/tutorial-group-enrollments/enrollment.png) 

1. Azure portalında sağlama hizmetinize gidin. **Kayıtları yönetme**'ye tıklayın. X.509 cihaz grubunun **Kayıt Grupları** bölümünde, otomatik olarak oluşturulmuş bir *GRUP ADI* altında göründüğüne dikkat edin.

## <a name="simulate-the-device"></a>Cihazı benzetme

1. Cihaz Sağlama Hizmeti özet dikey penceresinde **Genel Bakış**'ı seçip _Kimlik Kapsamı_ ve _Sağlama Hizmeti Genel Uç Noktası_ değerlerini not edin.

    ![Hizmet bilgileri](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Bir komut istemi açın. Örnek proje klasörüne gidin.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`, daha önce not ettiğiniz _kimlik kapsamınızı_ ve _sağlama hizmeti genel uç noktasını_ içerecek şekilde düzenleyin.

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. `leafPublicPem` ve `leafPrivateKey` değişkenlerini ortak ve özel cihaz sertifikalarınız ile güncelleştirin.

    Cihaz sertifikalarınızı PowerShell ile oluşturduysanız, mydevice * dosyaları cihaz için ortak anahtar, özel anahtar ve PFX 'yi içerir.

    Cihaz sertifikalarınızı Bash Shell ile oluşturduysanız,./certs/New-Device.cert.pem ortak anahtarı içerir. Cihazın özel anahtarı./Private/New-Device.Key.pem dosyasında olacaktır.

    Ortak anahtar dosyanızı açın ve `leafPublicPem` değişkenini bu değerle güncelleştirin. Metni _-----özel anahtar-----başlat_ _-----son özel anahtar-----_ kopyalayın.

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    Özel anahtar dosyanızı açın ve `leafPrivatePem` değişkenini bu değerle güncelleştirin. _-----RSA PRIVATE key-----_ 'den _-----son rsa özel anahtar-----_ kadar metni kopyalayın.

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. Ara sertifikanızın hemen altına `leafPrivateKey` yeni bir değişken ekleyin. Bu yeni değişkeni `intermediateKey`adlandırın. Ara imzalama sertifikanızın değerini verin.

    Cihaz sertifikalarınızı Bash Shell ile oluşturduysanız, *./certs/Azure-iot-test-only.intermediate.cert.pem* ara sertifika anahtarını içerir. Sertifikalarınız PowerShell ile oluşturulduysa, *./Intermediate1.pem* ara sertifika dosyanız olacaktır.

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. `main` işlevinde, `securityProviderX509`başlatılmadan önce `intermediateKey` `signerCertificates` koleksiyonuna ekleyin.

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. Değişikliklerinizi kaydedin ve örneği derleyin. Hedef klasöre gidin ve oluşturulan jar dosyasını ayıklayın.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Başarılı kayıt](./media/tutorial-group-enrollments/registration.png)

1. Portalda, sağlama hizmetinize bağlı olan IoT hub'ına gidin ve **Device Explorer** dikey penceresini açın. X.509 sanal cihazının hub'a başarıyla sağlanması durumunda, cihaz kimliği **Device Explorer** dikey penceresinde *DURUM* değeri **etkinleştirildi** olarak gösterilir. Unutmayın;dikey pencereyi, örnek cihaz uygulamasını çalıştırmadan önce açtıysanız en üstteki **Yenile** düğmesine tıklamanız gerekebilir. 

    ![Cihaz IOT hub'da kayıtlı](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Kaynakları temizleme

Cihaz istemci örneği üzerinde çalışmaya ve inceleme yapmaya devam etmeyi planlıyorsanız bu Hızlı Başlangıç’ta oluşturulan kaynakları silmeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç ile oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın:

1. Makinenizde cihaz istemci örnek çıktı penceresini kapatın.
1. Azure portalında sol taraftaki menüden **Tüm kaynaklar**’ı ve ardından Cihaz Sağlama hizmetini seçin. Hizmetinizin kayıtları **yönetme** dikey penceresini açın ve sonra **bireysel** Kayıtlar sekmesine tıklayın. bu hızlı BAŞLANGıÇTA kaydettiğiniz cihazın *kayıt kimliği* ' ni seçin ve üstteki **Sil** düğmesine tıklayın. 
1. Azure portalında sol taraftaki menüden **Tüm kaynaklar**’ı ve ardından IoT hub’ınızı seçin. Hub'ınızın **IoT Cihazları** dikey penceresini açıp bu Hızlı Başlangıç adımlarını kullanarak kaydettiğiniz cihazın *CİHAZ KİMLİĞİ* değerini seçip en üstte bulunan **Sil** düğmesine tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Windows makinenizde bir X.509 sanal cihazı oluşturdunuz ve Azure IoT Hub Cihazı Sağlama Hizmeti ve kayıt grupları ile IoT hub'ınıza sağladınız. X.509 cihazınız hakkında daha fazla bilgi edinmek için cihaz kavramlarıyla devam edin. 

> [!div class="nextstepaction"]
> [IoT Hub Cihazı Sağlama Hizmeti cihaz kavramları](concepts-device.md)
