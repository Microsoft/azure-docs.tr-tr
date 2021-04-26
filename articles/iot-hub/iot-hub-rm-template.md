---
title: Şablon kullanarak Azure IoT Hub oluşturma (.NET) | Microsoft Docs
description: C# programıyla bir IoT Hub oluşturmak için Azure Resource Manager şablonu kullanma.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: db4b676e65d36a9476fd72b66cc8ccfa38af4d85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144499"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Azure Resource Manager şablonu kullanarak IoT Hub 'ı oluşturma (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure IoT Hub 'larını programlı bir şekilde oluşturmak ve yönetmek için Azure Resource Manager kullanabilirsiniz. Bu öğreticide, bir C# programından IoT Hub 'ı oluşturmak için bir Azure Resource Manager şablonunun nasıl kullanılacağı gösterilmektedir.

> [!NOTE]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır:  [Azure Resource Manager ve klasik](../azure-resource-manager/management/deployment-models.md).  Bu makalede Azure Resource Manager dağıtım modelinin kullanımı ele alınmaktadır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Visual Studio.
* Etkin bir Azure hesabı. <br/>Hesabınız yoksa, yalnızca birkaç dakika içinde [ücretsiz bir hesap][lnk-free-trial] oluşturabilirsiniz.
* Azure Resource Manager şablon dosyalarınızı depolayabileceğiniz bir [Azure depolama hesabı][lnk-storage-account] .
* [Azure PowerShell 1,0][lnk-powershell-install] veya üzeri.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Visual Studio projenizi hazırlama

1. Visual Studio 'da **konsol uygulaması (.NET Framework)** proje şablonunu kullanarak bir Visual C# Windows Klasik Masaüstü projesi oluşturun. Projeyi **CreateIoTHub** olarak adlandırın.

2. Çözüm Gezgini, projenize sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' e tıklayın.

3. NuGet Paket Yöneticisi ' nde, **ön sürümü dahil** et ' i Işaretleyin ve **Microsoft. Azure. Management. ResourceManager** için **göz at** sayfasında arama yapın. Paketi seçin, ardından, **Gözden geçirme değişiklikleri** **' nde** **Tamam**' ı tıklatın ve lisansları kabul etmek için **kabul ediyorum** ' a tıklayın.

4. NuGet Paket Yöneticisi ' nde **Microsoft. IdentityModel. clients. ActiveDirectory** için arama yapın.  **Tamam**' a tıklayın, **değişiklikleri gözden geçir** **' e** tıklayın, ardından **kabul ediyorum** ' a tıklayarak lisansı kabul edin.

5. Program. cs ' de, var olan **using** deyimlerini aşağıdaki kodla değiştirin:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Program. cs ' de, yer tutucu değerlerini değiştirerek aşağıdaki statik değişkenleri ekleyin. Bu öğreticide, **ApplicationId**, **SubscriptionID**, **tenantıd** ve **parola** hakkında daha önce bir değişiklik yaptınız. **Azure depolama hesabınızın adı** , Azure Resource Manager şablon dosyalarınızı depoladığınız Azure depolama hesabının adıdır. **Kaynak grubu adı** , IoT Hub 'ını oluştururken kullandığınız kaynak grubunun adıdır. Ad, önceden var olan veya yeni bir kaynak grubu olabilir. **Dağıtım adı** , dağıtım için **Deployment_01** gibi bir addır.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>IoT Hub 'ı oluşturmak için Şablon gönderme

Kaynak grubunuzda bir IoT Hub 'ı oluşturmak için bir JSON şablonu ve parametre dosyası kullanın. Ayrıca, var olan bir IoT Hub 'ında değişiklik yapmak için bir Azure Resource Manager şablonu da kullanabilirsiniz.

1. Çözüm Gezgini, projenize sağ tıklayın, **Ekle**' ye tıklayın ve ardından **Yeni öğe**' ye tıklayın. Projenize **template.js** ADLı bir JSON dosyası ekleyin.

2. **Doğu ABD** bölgesine standart bir IoT Hub 'ı eklemek için, **template.js** içeriğini aşağıdaki kaynak tanımıyla değiştirin. IoT Hub destekleyen bölgelerin geçerli listesi için bkz. [Azure durumu][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. Çözüm Gezgini, projenize sağ tıklayın, **Ekle**' ye tıklayın ve ardından **Yeni öğe**' ye tıklayın. Projenize **parameters.js** ADLı bir JSON dosyası ekleyin.

4. **parameters.js** içeriğini, yeni IoT Hub için bir adı ayarlayan aşağıdaki parametre bilgileriyle değiştirin. Örneğin, **{ınıtıals} mynewiotub**. IoT Hub adı, genel olarak benzersiz olmalıdır, bu nedenle adınızı veya adınızın baş harflerini içermesi gerekir:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. **Sunucu Gezgini**' de Azure aboneliğinize bağlanın ve Azure depolama hesabınızda **Şablonlar** adlı bir kapsayıcı oluşturun. **Özellikler** panelinde, **Şablonlar** kapsayıcısı için **Genel okuma erişimi** izinlerini **BLOB** olarak ayarlayın.

6. **Sunucu Gezgini**' de, **Şablonlar** kapsayıcısına sağ tıklayıp **BLOB kapsayıcısını görüntüle**' ye tıklayın. **Blobu karşıya yükle** düğmesine tıklayın, iki dosyayı seçin, **üzerindeparameters.js** ve **templates.js** açın ve ardından **Aç** ' a tıklayarak JSON dosyalarını **Şablonlar** kapsayıcısına yükleyin. JSON verilerini içeren Blobların URL 'Leri şunlardır:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Program. cs ' ye aşağıdaki yöntemi ekleyin:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Şablon ve parametre dosyalarını Azure Resource Manager göndermek için aşağıdaki kodu **CreateIoTHub** yöntemine ekleyin:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Aşağıdaki kodu, yeni IoT Hub için durum ve anahtarları görüntüleyen **CreateIoTHub** yöntemine ekleyin:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Uygulamayı tamamlayıp çalıştırın

Artık uygulamayı oluşturmadan ve çalıştırmadan önce **CreateIoTHub** yöntemini çağırarak uygulamayı tamamlayabilirsiniz.

1. **Main** yönteminin sonuna aşağıdaki kodu ekleyin:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. **Oluştur** ve sonra **çözüm oluştur**' a tıklayın. Hataları düzeltin.

3. **Hata Ayıkla** ' ya tıklayın ve uygulamayı çalıştırmak Için **hata ayıklamaya başlayın** . Dağıtımın çalışması birkaç dakika sürebilir.

4. Uygulamanızın yeni IoT Hub 'ını eklediğini doğrulamak için [Azure Portal][lnk-azure-portal] ziyaret edin ve kaynak listenizi görüntüleyin. Alternatif olarak, **Get-AzResource** PowerShell cmdlet 'ini kullanın.

> [!NOTE]
> Bu örnek uygulama, faturalandırıldığınız bir S1 standart IoT Hub ekler. IoT Hub 'ı [Azure Portal][lnk-azure-portal] aracılığıyla veya Işiniz bittiğinde **Remove-azresource** PowerShell cmdlet 'ini kullanarak silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Artık bir C# programıyla Azure Resource Manager şablonu kullanarak IoT Hub 'ı dağıttıysanız, daha fazla araştırma yapmak isteyebilirsiniz:

* [IoT Hub kaynak sağlayıcısı REST API][lnk-rest-api]özellikleri hakkında bilgi edinin.
* Azure Resource Manager özellikleri hakkında daha fazla bilgi edinmek için [Azure Resource Manager genel bakış][lnk-azure-rm-overview] konusunu okuyun.
* Şablonlarda kullanılacak JSON sözdizimi ve özellikler için bkz. [Microsoft. Devices kaynak türleri](/azure/templates/microsoft.devices/iothub-allversions).

IoT Hub için geliştirme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [C SDK 'ya giriş][lnk-c-sdk]
* [Azure IoT SDK’ları][lnk-sdks]

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [Azure IOT Edge ile sınır cihazlarına Al dağıtma][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md