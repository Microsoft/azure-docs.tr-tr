---
title: "Hızlı başlangıç: kullanarak TPM cihazını Azure cihaz sağlama hizmeti 'ne kaydetmeC#"
description: Azure hızlı başlangıç-Service SDK kullanarak C# TPM cihazını Azure IoT Hub cihaz sağlama hizmeti 'ne kaydedin. Bu hızlı başlangıçta bireysel kayıtlar kullanılmaktadır.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 70f9c9d2ec488854a1b386b872f10e4f54c45a1c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904750"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Hızlı başlangıç: Service SDK kullanarak C# cihaz sağlama HIZMETI IoT Hub TPM cihazı kaydetme

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Bu makalede, [ C# hizmet SDK 'sını](https://github.com/Azure/azure-iot-sdk-csharp) ve örnek C# .NET Core uygulamasını kullanarak Azure IoT Hub cihaz sağlama hizmeti 'nde bir TPM cihazı için tek bir kayıt oluşturma işlemi gösterilmektedir. İsteğe bağlı olarak, bu bireysel kayıt girişini kullanarak sanal bir TPM cihazını sağlama hizmetine kaydedebilirsiniz. Bu adımlar hem Windows hem de Linux bilgisayarlarda çalışır, ancak bu makalede bir Windows geliştirme bilgisayarı kullanılmaktadır.

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

1. Bilgisayarınızda [Visual Studio 2019](https://www.visualstudio.com/vs/) yüklü olduğunu doğrulayın.

1. Bilgisayarınızda yüklü [.NET Core SDK](https://www.microsoft.com/net/download/windows) olduğunu doğrulayın.

1. Devam etmeden önce [Azure portal IoT Hub cihaz sağlama hizmetini ayarlama](./quick-setup-auto-provision.md) bölümündeki adımları uygulayın.

1. Seçim Bu hızlı başlangıç sonunda sanal bir cihaz kaydetmek istiyorsanız cihaz [SDK 'sını kullanarak C# sanal bir TPM cihazı oluşturma ve sağlama](quick-create-simulated-device-tpm-csharp.md) bölümündeki yordamı, cihaz için bir onay anahtarı aldığınız adıma kadar izleyin. Daha sonra bu hızlı başlangıçta kullanmanız gerektiğinden, onay anahtarını, kayıt KIMLIĞINI ve isteğe bağlı olarak cihaz KIMLIĞINI kaydedin.

   > [!NOTE]
   > Azure portal kullanarak bireysel kayıt oluşturma adımlarını izleyin.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Sağlama hizmetinizin bağlantı dizesini alma

Bu hızlı başlangıçtaki örnek için sağlama hizmetinizin bağlantı dizesine ihtiyacınız vardır.

1. Azure portal oturum açın, **tüm kaynaklar**' ı ve ardından cihaz sağlama hizmeti ' ni seçin.

1. **Paylaşılan erişim ilkeleri**' ni seçin ve ardından özelliklerini açmak için kullanmak istediğiniz erişim ilkesini seçin. **Erişim ilkesi**' nde, birincil anahtar bağlantı dizesini kopyalayın ve kaydedin.

    ![Portaldan sağlama hizmeti bağlantı dizesini alma](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Bireysel kayıt örneğini oluşturma

Bu bölümde, sağlama hizmetinize TPM cihazı için tek bir kayıt ekleyen bir .NET Core konsol uygulamasının nasıl oluşturulacağı gösterilmektedir. Biraz değişiklikle, bireysel kayıt eklemek üzere bir [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) konsol uygulaması oluşturmak için de bu adımları izleyebilirsiniz. IoT Core ile geliştirme hakkında daha fazla bilgi için bkz. [Windows IoT Core geliştirici belgeleri](https://docs.microsoft.com/windows/iot-core/).

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin. **Yeni proje oluştur**' da, Için C# **konsol uygulaması (.NET Core)** proje şablonunu seçin ve **İleri**' yi seçin.

1. Projeyi *Createtpmenrollment*olarak adlandırın ve **Oluştur**' u seçin.

    ![Visual C# Windows Klasik Masaüstü projesini yapılandırma](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. **Çözüm Gezgini**, **Createtpmenrollment** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paket Yöneticisi**' nde, **Araştır**' ı seçin, **Microsoft. Azure. Devices. sağlama. hizmeti**' ni arayın ve ardından **Install**' ı seçin.

   ![NuGet Paket Yöneticisi penceresi](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Bu adım, [Azure IoT sağlama hizmeti istemci SDK 'sı](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet paketi ve bağımlılıklarını indirir, yükler ve buna bir başvuru ekler.

1. Aşağıdaki `using` deyimlerini `Program.cs`en üstündeki diğer `using` deyimlerinden sonra ekleyin:
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Aşağıdaki alanları `Program` sınıfına ekleyin ve listelenen değişiklikleri yapın.

   ```csharp
   private static string ProvisioningConnectionString = "{Your provisioning service connection string}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```

   * `ProvisioningConnectionString` yer tutucu değerini, kaydını oluşturmak istediğiniz sağlama hizmetinin bağlantı dizesiyle değiştirin.

   * İsteğe bağlı olarak kayıt kimliğini, onay anahtarını, cihaz kimliğini ve sağlama durumunu değiştirebilirsiniz.

   * Bu hızlı başlangıcı [cihaz SDK 'sını kullanarak C# sanal bir cihaz oluşturma ve sağlama](quick-create-simulated-device-tpm-csharp.md) hızlı başlangıcı ile birlikte kullanıyorsanız, onay anahtarını ve kayıt kimliğini bu hızlı başlangıçta not ettiğiniz değerlerle değiştirin. Cihaz KIMLIĞINI bu hızlı başlangıçta önerilen değer ile değiştirebilir, kendi değerini kullanabilir veya bu örnekteki varsayılan değeri kullanabilirsiniz.

1. Aşağıdaki yöntemi `Program` sınıfına ekleyin.  Bu kod, bireysel kayıt girişi oluşturur ve sonra tek bir kaydı sağlama hizmetine eklemek için `ProvisioningServiceClient` `CreateOrUpdateIndividualEnrollmentAsync` yöntemini çağırır.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```

1. Son olarak, `Main` yönteminin gövdesini aşağıdaki satırlarla değiştirin:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Çözümü derleyin.

## <a name="run-the-individual-enrollment-sample"></a>Bireysel kayıt örneğini çalıştırma
  
Örneği Visual Studio'da çalıştırarak TPM cihazınızın bireysel kaydını oluşturun.

Başarılı bir şekilde oluşturulduğunda, komut Istemi penceresinde yeni bireysel kaydın özellikleri görüntülenir.

Bireysel kaydın oluşturulduğunu doğrulayabilirsiniz. Cihaz sağlama hizmeti özetine gidin ve kayıtları **Yönet**' i seçin ve **bireysel**kayıtlar ' ı seçin. Örnekte kullandığınız kayıt kimliğine karşılık gelen yeni bir kayıt girdisi görmelisiniz.

![Portaldaki kayıt özellikleri](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Onay anahtarını ve girişin diğer özelliklerini doğrulamak için girişi seçin.

[Cihaz SDK 'sını kullanarak C# sanal bir TPM cihazı oluşturma ve sağlama](quick-create-simulated-device-tpm-csharp.md) ile ilgili adımları izlediyseniz, sanal cihazınızı kaydetmek için bu hızlı başlangıçta kalan adımlara devam edebilirsiniz. Azure Portal'ı kullanarak bireysel kayıt oluşturma adımlarını atladığınızdan emin olun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

C# Hizmet örneğini keşfetmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Aksi takdirde, bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Bilgisayarınızda C# örnek çıkış penceresini kapatın.

1. Azure portal cihaz sağlama hizmetine gidin, kayıtları **Yönet**' i seçin ve sonra **bireysel** kayıtlar sekmesini seçin. bu hızlı başlangıç Ile oluşturduğunuz kayıt girişinin *kayıt kimliği* ' ni seçin ve **Sil**' i seçin.

1. [Cihaz SDK 'sını kullanarak C# ](quick-create-simulated-device-tpm-csharp.md) sanal bir TPM cihazı oluşturma ve sağlama bölümündeki adımları IZLEDIYSENIZ, sanal bir TPM cihazı oluşturmak için aşağıdaki adımları uygulayın:

    1. TPM simülatör penceresini ve simülasyon cihazının örnek çıkış penceresini kapatın.

    1. Azure Portal'da, cihazınızın sağlandığı IoT Hub'ına gidin. Araştırıcılar altındaki menüde **IoT cihazları**' nı seçin, cihazınızın yanındaki onay kutusunu işaretleyin ve ardından **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir TPM cihazı için program aracılığıyla tek bir kayıt girişi oluşturdunuz. İsteğe bağlı olarak, bilgisayarınızda bir TPM sanal cihazı oluşturdunuz ve Azure IoT Hub cihaz sağlama hizmeti 'ni kullanarak IoT Hub 'ınıza sağladınız. Cihaz sağlama hakkında ayrıntılı bilgi edinmek için Azure portalında Cihaz Sağlama Hizmeti ayarları öğreticisine geçin.

> [!div class="nextstepaction"]
> [Azure IoT Hub Cihazı Sağlama Hizmeti öğreticileri](./tutorial-set-up-cloud.md)
