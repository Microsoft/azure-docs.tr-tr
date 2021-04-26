---
title: Öğretici-Azure IoT Hub cihaz sağlama hizmeti 'ni kullanarak cihaz sağlama (.NET)
description: Bu öğreticide, .NET kullanarak Azure IoT Hub cihaz sağlama hizmeti 'ni (DPS) kullanarak cihazınızı tek bir IoT Hub 'ına nasıl sağlayabileceğiniz gösterilmektedir.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 84fe935c1122d3d5c65423341b8760643257f992
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868580"
---
# <a name="tutorial-enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Öğretici: Azure IoT Hub sağlama hizmeti Istemcisini (.NET) kullanarak bir IoT Hub 'ına cihaz kaydetme

Önceki öğreticide, Cihaz Sağlama hizmetine bağlanmak için bir cihazın nasıl ayarlanacağını öğrendiniz. Bu öğreticide, hem **_Tek Kayıt_** hem de **_Kayıt Grupları_** kullanarak cihazınızı tek bir IoT hub’a sağlamak için bu hizmetin nasıl kullanılacağını öğreneceksiniz. Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> * Cihazı kaydedin
> * Cihazı başlatın
> * Cihaz kayıtlı olduğunu doğrulayın

## <a name="prerequisites"></a>Önkoşullar

Devam etmeden önce, [Azure IoT Hub Cihazı Sağlama Hizmeti’ni kullanarak bir cihazı sağlamak üzere ayarlama](./tutorial-set-up-device.md) öğreticisinde açıklandığı gibi cihazınızı ve *Donanım Güvenliği Modülünü* yapılandırdığınızdan emin olun.

* Visual Studio

> [!NOTE]
> Visual Studio gerekli değildir. [.NET](https://dotnet.microsoft.com) yüklemesi yeterlidir ve geliştiriciler Windows veya Linux üzerinde tercih ettikleri düzenleyiciyi kullanabilir.  

Bu öğretici, cihaz bilgilerinin sağlama hizmetine eklendiği donanım üretim işlemi sırasındaki veya hemen sonrasındaki dönemin benzetimini yapar. Bu kod genellikle .NET kod çalıştırabilen bir PC veya fabrika cihazı üzerinde çalışır ve cihazlara eklenmemelidir.


## <a name="enroll-the-device"></a>Cihazı kaydedin

Bu adım, cihazın benzersiz güvenlik yapılarının Cihaz Sağlama Hizmeti’ne eklenmesini kapsar. Bu güvenlik yapıtları aşağıdaki gibidir:

- TPM tabanlı cihazlar için:
    - Her bir TPM yongası veya benzetimi için benzersiz olan *Onay Anahtarı*. Daha fazla bilgi için [TPM Onay Anahtarını Anlama](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11)) bölümünü okuyun.
    - Ad alanındaki/kapsamdaki bir cihazı benzersiz şekilde tanımlamak için kullanılan *Kayıt Kimliği*. Bu anahtar, cihaz kimliğiyle aynı olabilir veya olmayabilir. Kimlik her cihaz için zorunludur. TPM tabanlı cihazlar için kayıt kimliği, TPM’den türetilebilir; örneğin, TPM Onay Anahtarının SHA-256 karması.

- X.509 tabanlı cihazlar için:
    - Bir *.pem* veya *.cer* dosyası biçiminde [cihaza verilen X.509 sertifikası](/windows/win32/seccertenroll/about-x-509-public-key-certificates). Tek kayıtta, X.509 sisteminiz için *yaprak sertifikayı* kullanmanız, kayıt grupları içinse *kök sertifika* veya eşdeğer bir *imzalayan sertifikası* kullanmanız gerekir.
    - Ad alanındaki/kapsamdaki bir cihazı benzersiz şekilde tanımlamak için kullanılan *Kayıt Kimliği*. Bu anahtar, cihaz kimliğiyle aynı olabilir veya olmayabilir. Kimlik her cihaz için zorunludur. X.509 tabanlı cihazlar için kayıt kimliği, sertifikanın ortak adından (CN) türetilir. Bu gereksinimler hakkında daha fazla bilgi için bkz. [Cihaz kavramları](./concepts-service.md).

Cihaz Sağlama Hizmeti’ne cihazı kaydetmenin iki yolu vardır:

- **Bireysel Kayıtlar** Bu, Cihaz Sağlama Hizmeti’ne kaydolabilecek tek bir cihaz için bir girdiyi temsil eder. Bireysel kayıtlar, kanıtlama mekanizması olarak X.509 sertifikalarını veya SAS belirteçlerini (gerçek ya da sanal TPM’de) kullanabilir. Benzersiz ilk yapılandırma gerektiren cihazlar için veya kanıtlama mekanizması olarak TPM aracılığıyla yalnızca SAS belirteçlerini kullanabilen cihazlar için bireysel kayıtların kullanılmasını öneririz. Bireysel kayıtlar için istenen IoT hub cihazı kimliği belirtilmiş olabilir.

- **Kayıt Grupları** Bu, belirli bir kanıtlama mekanizmasını paylaşan bir aygıt grubunu temsil eder. İstenen bir ilk yapılandırmayı paylaşan çok sayıda cihaz için veya aynı kiracıya giden tüm cihazlar için bir kayıt grubu kullanılmasını öneririz. Kayıt grupları yalnızca X.509’dur ve tümü X.509 sertifika zincirinde bir imzalama sertifikasını paylaşır.

### <a name="enroll-the-device-using-individual-enrollments"></a>Tek Kayıtları kullanarak cihaz kaydetme

1. Visual Studio'da, **Konsol Uygulaması** proje şablonunu kullanarak yeni bir Visual C# Konsol Uygulaması projesi oluşturun. Projeyi **DeviceProvisioning** olarak adlandırın.
    
1. Çözüm Gezgini'nde **DeviceProvisioning** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet...** seçeneğine tıklayın.

1. **NuGet Paket Yöneticisi** penceresinde **Göz At**’ı seçin ve **microsoft.azure.devices.provisioning.service** araması yapın. Girişi seçin ve **Yükle**’ye tıklayarak **Microsoft.Azure.Devices.Provisioning.Service** paketini yükleyin ve kullanım koşullarını kabul edin. Bu yordam, [Azure IoT cihazı sağlama hizmeti SDK 'sı](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet paketi ve bağımlılıklarını indirir, yükler ve buna bir başvuru ekler.

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. Önceki bölümde not edilen yer tutucu değerini Cihaz Sağlama Hizmeti bağlantı dizesiyle değiştirin.
   
    ```csharp
    static readonly string ServiceConnectionString = "{Device Provisioning Service connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Cihaz kaydını uygulamak için aşağıdakileri ekleyin:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Son olarak, aşağıdaki kodu **Main** yöntemine ekleyerek IoT hub’ınızla bağlantıyı açın ve kayda başlayın:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.ReadLine();
    ```
        
1. Visual Studio Çözüm Gezgini çözümünüze sağ tıklayın ve ardından **Başlangıç projelerini ayarla...** öğesine tıklayın. **Tek bir başlangıç projesi**' ni seçin ve ardından açılan menüden **devicesağlamasını** proje ' yi seçin.  

1. **DeviceProvisiong** adlı .NET cihaz uygulamasını çalıştırın. Cihaz sağlamasını ayarlaması gerekir: 

    ![Tek kayıt çalıştırma](./media/tutorial-net-provision-device-to-hub/individual.png)

Cihaz başarıyla kaydedildiğinde cihazın portalda şu şekilde görüntülendiğini görmeniz gerekir:

   ![Portalda başarılı kayıt](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Kayıt Gruplarını kullanarak cihaz kaydetme

> [!NOTE]
> Kayıt grubu örneği bir X.509 sertifikası gerektirir.

1. Visual Studio Çözüm Gezgini'nde, yukarıda oluşturduğunuz **DeviceProvisioning** projesini açın. 

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucu değerini X509 sertifika konumu ile değiştirin.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Grup kaydını uygulamak için **Program.cs** dosyasına aşağıdakileri ekleyin:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Son olarak, aşağıdaki kodu **Main** yöntemiyle değiştirerek IoT hub’ınızla bağlantıyı açın ve grup kaydına başlayın:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. **DeviceProvisiong** adlı .NET cihaz uygulamasını çalıştırın. Cihaz için grup sağlamasını ayarlaması gerekir: 

    ![Grup kaydı çalıştırma](./media/tutorial-net-provision-device-to-hub/group.png)

    Cihaz grubu başarıyla kaydedildiğinde cihazın portalda şu şekilde görüntülendiğini görmeniz gerekir:

   ![Portalda başarılı grup kaydı](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Cihazı başlatın

Bu noktada, aşağıdaki kurulum cihaz kaydı için hazırdır:

1. Cihazınız veya cihaz grubunuz, Cihaz Sağlama hizmetinize kaydolur ve 
2. Cihazınız, güvenliği yapılandırılmış olarak hazır olur ve Cihaz Sağlama Hizmeti istemci SDK’sı kullanılarak uygulamadan cihaza erişilebilir.

İstemci uygulamanızın, Cihaz Sağlama hizmetinize kaydı başlatmasını sağlamak için cihazı başlatın.  


## <a name="verify-the-device-is-registered"></a>Cihaz kayıtlı olduğunu doğrulayın

Cihazınız önyüklendikten sonra aşağıdaki eylemler gerçekleşmelidir. Daha fazla bilgi için bkz. [Cihaz İstemci Örneğini Sağlama](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device). 

1. Cihaz, Cihaz Sağlama hizmetinize bir kayıt isteği gönderir.
2. TPM cihazları için Cihaz Sağlama Hizmeti bir kayıt sınaması gönderir ve cihazınız da buna yanıt verir. 
3. Kayıt başarılı olduğunda Cihaz Sağlama hizmeti, IoT hub URI’sini, cihaz kimliğini ve şifrelenmiş anahtarı cihaza geri gönderir. 
4. Sonra cihazdaki IoT Hub istemci uygulaması, hub’ınıza bağlanır. 
5. Hub’a başarılı bağlantı gerçekleştiğinde, IoT hub’ın **Cihaz Gezgini**’nde cihazın görünmesi gerekir. 

    ![Portalda hub ile başarılı bağlantı](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Cihazı kaydedin
> * Cihazı başlatın
> * Cihaz kayıtlı olduğunu doğrulayın

Yük dengeli hublar arasında birden çok cihaz sağlamayı öğrenmek için sonraki öğreticiye ilerleyin. 

> [!div class="nextstepaction"]
> [Yük dengeli IoT Hub'larında cihazları sağlama](./tutorial-provision-multiple-hubs.md)