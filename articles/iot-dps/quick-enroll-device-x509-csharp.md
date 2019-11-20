---
title: "Hızlı başlangıç: kullanarak X. 509.952 cihazını Azure cihaz sağlama hizmeti 'ne kaydetmeC#"
description: Bu hızlı başlangıçta grup kayıtları kullanılmaktadır. Bu hızlı başlangıçta, kullanarak C#X. 509.952 cihazlarını Azure IoT Hub cihaz sağlama hizmetine kaydedin.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: e43448337f787115c479f2f53ca57b7a20120108
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903431"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Hızlı başlangıç: C# kullanarak X.509 cihazlarını Cihaz Sağlama Hizmeti'ne kaydetme

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Bu hızlı başlangıçta C# programlama özelliklerini kullanarak ara veya kök CA X.509 sertifikalarını kullanan bir [Kayıt grubu](concepts-service.md#enrollment-group) oluşturmayı öğreneceksiniz. Kayıt grubu, [.NET için Microsoft Azure ıOT SDK](https://github.com/Azure/azure-iot-sdk-csharp) ve örnek C# bir .NET Core uygulaması kullanılarak oluşturulur. Kayıt grubu, sertifika zincirlerinde ortak imzalama sertifikasını paylaşan cihazlar için sağlama hizmetine erişimi denetler. Daha fazla bilgi edinmek için bkz. [X.509 sertifikalarıyla sağlama hizmetine cihaz erişimini denetleme](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Azure IoT Hub ve Cihaz Sağlama Hizmeti ile X.509 sertifikası tabanlı Ortak Anahtar Altyapısı'nı (PKI) kullanma hakkında daha fazla bilgi için bkz. [X.509 CA sertifikası güvenliğine genel bakış](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Bu hızlı başlangıçta zaten bir IoT Hub ve cihaz sağlama hizmeti örneği oluşturmuş olmanız beklenir. Bu kaynakları daha önce oluşturmadıysanız, bu makaleye devam etmeden önce Azure portal hızlı başlangıç [ile IoT Hub cihaz sağlama hizmetini ayarla](./quick-setup-auto-provision.md) ' yı doldurun.

Bu makaledeki adımlar hem Windows hem de Linux bilgisayarlarda çalışır, ancak bu makalede bir Windows geliştirme bilgisayarı kullanılmaktadır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Visual Studio 2019](https://www.visualstudio.com/vs/)' i yükler.
* [.NET Core SDK](https://www.microsoft.com/net/download/windows)'i yükler.
* [Git](https://git-scm.com/download/)'i yükleyin.

## <a name="prepare-test-certificates"></a>Test sertifikalarını hazırlama

Bu hızlı başlangıç için ara veya kök CA X.509 sertifikasının ortak bölümünü içeren bir .pem veya .cer dosyasına sahip olmanız gerekir. Bu sertifikanın sağlama hizmetinize yüklenmesi ve hizmet tarafından doğrulanması gerekir.

[Azure IoT C SDK 'sı](https://github.com/Azure/azure-iot-sdk-c) , bir X. 509.952 sertifika zinciri oluşturmanıza, bu zincirdeki bir kök veya ara sertifikayı yüklemeye ve sertifikayı doğrulamak için hizmetle birlikte sahip olmaya başlamanıza yardımcı olabilecek test araçları içerir.

> [!CAUTION]
> Yalnızca geliştirme testi için SDK araçları ile oluşturulan sertifikaları kullanın.
> Bu sertifikaları üretimde kullanmayın.
> 30 gün sonra sona ereceği *1234*gibi sabit kodlanmış parolalar içerirler.
> Üretim ortamında kullanıma uygun sertifikalar almayı öğrenmek için, Azure IoT Hub belgelerinde [X.509 CA sertifikası alma](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) konusuna bakın.
>

Sertifika oluşturmak için bu test araçlarını kullanmak üzere aşağıdaki adımları uygulayın:

1. Bir komut Istemi penceresi veya git bash kabuğu açın ve bilgisayarınızdaki çalışan bir klasöre değiştirin. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunu kopyalamak için aşağıdaki komutu çalıştırın:

   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

   Test araçları kopyaladığınız deponun *azure-iot-sdk-c/tools/CACertificates* dizininde bulunur.

1. [Örnekler ve öğreticiler için test amaçlı CA sertifikalarını yönetme](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) adımlarını izleyin.

C SDK 'sindeki araca ek olarak, *.NET için Microsoft Azure ıOT SDK* 'daki C# [Grup sertifikası doğrulama örneği](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) , var olan bir X. 509.952 ara veya kök CA sertifikası ile birlikte sahip olma konusunda nasıl bir kanıt yapılacağını gösterir.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Sağlama hizmetinizin bağlantı dizesini alma

Bu hızlı başlangıçtaki örnek için sağlama hizmetinizin bağlantı dizesine ihtiyacınız vardır.

1. Azure portal oturum açın, **tüm kaynaklar**' ı ve ardından cihaz sağlama hizmeti ' ni seçin.

1. **Paylaşılan erişim ilkeleri**' ni seçin ve ardından özelliklerini açmak için kullanmak istediğiniz erişim ilkesini seçin. **Erişim ilkesi**' nde, birincil anahtar bağlantı dizesini kopyalayın ve kaydedin.

    ![Portaldan sağlama hizmeti bağlantı dizesini alma](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Kayıt grubu örneğini oluşturma 

Bu bölümde, sağlama hizmetinize bir kayıt grubu ekleyen bir .NET Core konsol uygulamasının nasıl oluşturulacağı gösterilmektedir. Biraz değişiklikle, kayıt grubunu eklemek üzere bir [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) konsol uygulaması oluşturmak için de bu adımları izleyebilirsiniz. IoT Core ile geliştirme hakkında daha fazla bilgi edinmek için bkz. [Windows IoT Core geliştirici belgeleri](https://docs.microsoft.com/windows/iot-core/).

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin. **Yeni proje oluştur**' da, proje şablonu Için C# **konsol uygulamasını (.NET Core)** seçin ve ileri ' **yi**seçin.

1. Projeyi *Createkayıtmentgroup*olarak adlandırın ve **Oluştur**' u seçin.

    ![Visual C# Windows Klasik Masaüstü projesini yapılandırma](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. **Çözüm Gezgini**, **createkayıtgrubu** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paket Yöneticisi**' nde, **Araştır**' ı seçin, **Microsoft. Azure. Devices. sağlama. hizmeti**' ni arayın ve ardından **Install**' ı seçin.

    ![NuGet Paket Yöneticisi penceresi](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Bu adım, [Azure IoT sağlama hizmeti istemci SDK 'sı](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet paketi ve bağımlılıklarını indirir, yükler ve buna bir başvuru ekler.

1. Aşağıdaki `using` deyimlerini `Program.cs`en üstündeki diğer `using` deyimlerinden sonra ekleyin:

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Aşağıdaki alanları `Program` sınıfına ekleyin ve listelenen değişiklikleri yapın.  

   ```csharp
   private static string ProvisioningConnectionString = "{Your provisioning service connection string}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * `ProvisioningConnectionString` yer tutucu değerini, kaydını oluşturmak istediğiniz sağlama hizmetinin bağlantı dizesiyle değiştirin.

   * `X509RootCertPath` yer tutucu değerini bir. pek veya. cer dosyasının yoluyla değiştirin. Bu dosya, daha önce önceden yüklenmiş ve sağlama hizmetinize doğrulanan bir ara veya kök CA X. 509.440 sertifikasının genel bölümünü temsil eder.

   * İsteğe bağlı olarak `EnrollmentGroupId` değerini değiştirebilirsiniz. Dize yalnızca küçük harflerden ve kısa çizgilerden oluşabilir.

   > [!IMPORTANT]
   > Üretim kodunda, güvenlikle ilgili aşağıdaki noktalara dikkat edin:
   >
   > * Sağlama hizmeti yöneticisi için bağlantı dizesinin sabit kodlanması en iyi güvenlik yöntemlerine uygun değildir. Bunun yerine, bağlantı dizesi güvenli bir şekilde, örneğin güvenli yapılandırma dosyasının içinde veya kayıt defterinin içinde tutulmalıdır.
   > * İmzalama sertifikasının yalnızca ortak bölümünü karşıya yüklediğinizden emin olun. Özel anahtarları içeren .pfx (PKCS12) veya .pem dosyalarını asla sağlama hizmetine yüklemeyin.

1. Aşağıdaki yöntemi `Program` sınıfına ekleyin. Bu kod bir kayıt grubu girişi oluşturur ve ardından kayıt grubunu sağlama hizmetine eklemek için `ProvisioningServiceClient` `CreateOrUpdateEnrollmentGroupAsync` yöntemini çağırır.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
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

## <a name="run-the-enrollment-group-sample"></a>Kayıt grubu örneğini çalıştırma
  
Örneği Visual Studio'da çalıştırarak kayıt grubunu oluşturun. Başarılı bir şekilde oluşturulduğunda, komut Istemi penceresi yeni kayıt grubunun özelliklerini görüntüler.

Kayıt grubunun oluşturulduğunu doğrulayabilirsiniz. Cihaz sağlama hizmeti özetine gidin ve kayıtları **Yönet**' i seçip **kayıt grupları**' nı seçin. Örnekte kullandığınız kayıt kimliğine karşılık gelen yeni bir kayıt girdisi görmelisiniz.

![Portaldaki kayıt özellikleri](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Sertifika parmak izini ve girdinin diğer özelliklerini doğrulamak için girişi seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

C# Hizmet örneğini keşfetmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Aksi takdirde, bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Bilgisayarınızda C# örnek çıkış penceresini kapatın.

1. Azure portal cihaz sağlama hizmetine gidin, kayıtları **Yönet**' i ve ardından **kayıt grupları**' nı seçin. Bu hızlı başlangıcı kullanarak oluşturduğunuz kayıt girişinin *kayıt kimliği* ' ni seçin ve **Sil**' i seçin.

1. Azure portal cihaz sağlama hizmetinizden **Sertifikalar**' ı seçin, bu hızlı başlangıç için karşıya yüklediğiniz sertifikayı seçin ve **sertifika ayrıntılarının**en üstünde **Sil** ' i seçin.  

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure IoT Hub cihaz sağlama hizmeti 'ni kullanarak X. 509.952 ara veya kök CA sertifikası için bir kayıt grubu oluşturdunuz. Cihaz sağlama hakkında ayrıntılı bilgi edinmek için Azure portalında Cihaz Sağlama Hizmeti ayarları öğreticisine geçin.

> [!div class="nextstepaction"]
> [Azure IoT Hub Cihazı Sağlama Hizmeti öğreticileri](./tutorial-set-up-cloud.md)
