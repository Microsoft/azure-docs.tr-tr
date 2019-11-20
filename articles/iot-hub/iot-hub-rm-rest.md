---
title: Kaynak sağlayıcısını kullanarak bir Azure IoT Hub 'ı oluşturun REST API | Microsoft Docs
description: Bir IoT Hub program aracılığıyla oluşturmak ve yönetmek C# için REST API kaynak sağlayıcısı 'nın nasıl kullanılacağını öğrenin.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 7d5e38e2ecfa2406ff0f58f73d828aa45d84c512
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890474"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Kaynak sağlayıcısı REST API (.NET) kullanarak IoT Hub 'ı oluşturma

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure IoT Hub 'larını programlı bir şekilde oluşturmak ve yönetmek için [IoT Hub kaynak sağlayıcısı REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) kullanabilirsiniz. Bu öğretici, bir C# programdan IoT Hub 'ı oluşturmak için IoT Hub kaynak sağlayıcısı REST API nasıl kullanacağınızı gösterir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Visual Studio.

* Etkin bir Azure hesabı. Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

* [Azure PowerShell 1,0](https://docs.microsoft.com/powershell/azure/install-Az-ps) veya üzeri.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Visual Studio projenizi hazırlama

1. Visual Studio 'da C# **konsol uygulaması (.NET Framework)** proje şablonunu kullanarak bir Visual Windows Klasik Masaüstü projesi oluşturun. Projeyi **Createiothubrest**olarak adlandırın.

2. Çözüm Gezgini, projenize sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' e tıklayın.

3. NuGet Paket Yöneticisi ' nde, **ön sürümü dahil**et ' i Işaretleyin ve **Microsoft. Azure. Management. ResourceManager**için **göz at** sayfasında arama yapın. Paketi seçin, ardından, **Gözden geçirme değişiklikleri** **' nde** **Tamam**' ı tıklatın ve lisansları kabul etmek için **kabul ediyorum** ' a tıklayın.

4. NuGet Paket Yöneticisi ' nde **Microsoft. IdentityModel. clients. ActiveDirectory**için arama yapın.  **Tamam**' a tıklayın, **değişiklikleri gözden geçir** **' e**tıklayın, ardından **kabul ediyorum** ' a tıklayarak lisansı kabul edin.

5. Program.cs ' de, var olan **using** deyimlerini aşağıdaki kodla değiştirin:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. Program.cs ' de, yer tutucu değerlerini değiştirerek aşağıdaki statik değişkenleri ekleyin. Bu öğreticide, **ApplicationId**, **SubscriptionID**, **tenantıd**ve **parola** hakkında daha önce bir değişiklik yaptınız. **Kaynak grubu adı** , IoT Hub 'ını oluştururken kullandığınız kaynak grubunun adıdır. Önceden var olan veya yeni bir kaynak grubu kullanabilirsiniz. **IoT Hub ad** , oluşturduğunuz IoT Hub (örneğin, **MyIoTHub**) adıdır. IoT Hub 'ınızın adı genel olarak benzersiz olmalıdır. **Dağıtım adı** , dağıtım için **Deployment_01**gibi bir addır.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>IoT Hub 'ı oluşturmak için REST API kaynak sağlayıcısını kullanma

Kaynak grubunuzda bir IoT Hub 'ı oluşturmak için [REST API IoT Hub kaynak sağlayıcısını](https://docs.microsoft.com/rest/api/iothub/iothubresource) kullanın. Ayrıca, var olan bir IoT Hub 'ında değişiklik yapmak için kaynak sağlayıcısı REST API de kullanabilirsiniz.

1. Program.cs 'e aşağıdaki yöntemi ekleyin:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. **CreateIoTHub** yöntemine aşağıdaki kodu ekleyin. Bu kod, üst bilgilerde kimlik doğrulama belirtecine sahip bir **HttpClient** nesnesi oluşturur:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. **CreateIoTHub** yöntemine aşağıdaki kodu ekleyin. Bu kod, bir JSON temsili oluşturmak ve oluşturmak için IoT Hub 'ını açıklar. IoT Hub destekleyen konumların geçerli listesi için bkz. [Azure durumu](https://azure.microsoft.com/status/):

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. **CreateIoTHub** yöntemine aşağıdaki kodu ekleyin. Bu kod, REST isteğini Azure 'a gönderir. Daha sonra kod yanıtı denetler ve dağıtım görevinin durumunu izlemek için kullanabileceğiniz URL 'YI alır:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. **CreateIoTHub** yönteminin sonuna aşağıdaki kodu ekleyin. Bu kod, dağıtımın tamamlanmasını beklemek için önceki adımda alınan **Asyncstatusuri** adresini kullanır:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. **CreateIoTHub** yönteminin sonuna aşağıdaki kodu ekleyin. Bu kod, oluşturduğunuz IoT Hub 'ın anahtarlarını alır ve konsola yazdırır:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Uygulamayı tamamlayıp çalıştırın

Artık uygulamayı oluşturmadan ve çalıştırmadan önce **CreateIoTHub** yöntemini çağırarak uygulamayı tamamlayabilirsiniz.

1. **Main** yönteminin sonuna aşağıdaki kodu ekleyin:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. **Oluştur** ve sonra **çözüm oluştur**' a tıklayın. Hataları düzeltin.

3. **Hata Ayıkla** ' ya tıklayın ve uygulamayı çalıştırmak Için **hata ayıklamaya başlayın** . Dağıtımın çalışması birkaç dakika sürebilir.

4. Uygulamanızın yeni IoT Hub 'ını eklediğini doğrulamak için [Azure Portal](https://portal.azure.com/) ziyaret edin ve kaynaklarınızın listesini görüntüleyin. Alternatif olarak, **Get-AzResource** PowerShell cmdlet 'ini kullanın.

> [!NOTE]
> Bu örnek uygulama, faturalandırıldığınız bir S1 standart IoT Hub ekler. İşiniz bittiğinde, [Azure Portal](https://portal.azure.com/) aracılığıyla IoT Hub 'ını silebilir veya Işiniz bittiğinde **Remove-azresource** PowerShell cmdlet 'ini kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık REST API kaynak sağlayıcısını kullanarak bir IoT Hub 'ı dağıttıysanız, daha fazla incelemek isteyebilirsiniz:

* [IoT Hub kaynak sağlayıcısı REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource)özellikleri hakkında bilgi edinin.

* Azure Resource Manager özellikleri hakkında daha fazla bilgi edinmek için [Azure Resource Manager genel bakış](../azure-resource-manager/resource-group-overview.md) konusunu okuyun.

IoT Hub için geliştirme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [C SDK 'ya giriş](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK’ları](iot-hub-devguide-sdks.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [Azure IoT Edge ile uç cihazlara AI dağıtma](../iot-edge/tutorial-simulate-device-linux.md)