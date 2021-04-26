---
title: Hızlı başlangıç-Azure Resource Manager şablonu kullanarak bir Azure IoT Hub cihaz sağlama hizmeti (DPS) oluşturma (ARM şablonu)
description: Azure hızlı başlangıç-Azure Resource Manager şablonu (ARM şablonu) kullanarak Azure IoT Hub cihaz sağlama hizmeti (DPS) oluşturmayı öğrenin.
author: wesmc7777
ms.author: wesmc
ms.date: 01/27/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.openlocfilehash: bcb37c624f53d961020de022569a621ca1dfaba1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789006"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-dps-with-an-arm-template"></a>Hızlı başlangıç: ARM şablonuyla IoT Hub cihaz sağlama hizmeti 'ni (DPS) ayarlama

Cihazlarınızı sağlamak için gerekli olan Azure bulut kaynaklarını programlı bir şekilde ayarlamak için bir [Azure Resource Manager](../azure-resource-manager/management/overview.md) şablonu (ARM şablonu) kullanabilirsiniz. Bu adımlarda, bir IoT Hub 'ı ve ARM şablonuyla yeni bir IoT Hub cihaz sağlama hizmeti oluşturma işlemi gösterilmektedir. IoT Hub 'ı, şablon kullanılarak DPS kaynağına da bağlanır. Bu bağlantı, DPS 'in yapılandırdığınız ayırma ilkelerine göre cihazları hub 'a atamasını sağlar.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Bu hızlı başlangıç, bir kaynak grubu oluşturmak ve şablonu dağıtmak için gereken programlama adımlarını gerçekleştirmek üzere [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)ve [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) kullanır, ancak bu adımları gerçekleştirmek ve şablonunuzu dağıtmak için kolayca [PowerShell](../azure-resource-manager/templates/deploy-powershell.md), .net, Ruby veya diğer programlama dillerini kullanabilirsiniz. 

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma konusunda zaten bilgi sahibiyseniz, aşağıdaki **Azure 'A dağıt** düğmesine tıklarsanız, Azure Portal dağıtım şablonu açılır.

[![Azure 'a genel bakış 'a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-iothub-device-provisioning/) alınmıştır.

> [!NOTE]
> Şu anda yeni DPS kaynaklarıyla kayıt oluşturmaya yönelik bir ARM şablonu desteği yoktur. Bu, uygulama için kabul edilen yaygın ve anlaşılmış bir istek.

:::code language="json" source="~/quickstart-templates/101-iothub-device-provisioning/azuredeploy.json":::

Yukarıdaki şablonda iki Azure kaynağı tanımlanmıştır:

* [**Microsoft. Devices/iothubs**](/azure/templates/microsoft.devices/iothubs): yeni bir Azure IoT Hub oluşturur.
* [**Microsoft. Devices/provisioningservices**](/azure/templates/microsoft.devices/provisioningservices): yeni IoT Hub zaten bağlantılı yeni bir Azure IoT Hub cihaz sağlama hizmeti oluşturur.


## <a name="deploy-the-template"></a>Şablonu dağıtma

#### <a name="deploy-with-the-portal"></a>Portal ile dağıtma

1. Azure 'da oturum açmak için aşağıdaki görüntüyü seçin ve dağıtım için şablonu açın. Şablon yeni bir IoT Hub ve DPS kaynağı oluşturur. Hub, DPS kaynağına bağlanır.

    [![Portal 'da Azure 'a dağıtma adımları](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

2. Aşağıdaki değerleri seçin veya girin ve **gözden geçir + oluştur**' a tıklayın.

    ![Portalda ARM şablonu dağıtım parametreleri](./media/quick-setup-auto-provision-rm/arm-template-deployment-parameters-portal.png)    

    Aşağıda belirtilmedikçe, IoT Hub ve DPS kaynağını oluşturmak için varsayılan değeri kullanın.

    | Alan | Açıklama |
    | :---- | :---------- |
    | **Abonelik** | Azure aboneliğinizi seçin. |
    | **Kaynak grubu** | **Yeni oluştur**' a tıklayın, kaynak grubu için benzersiz bir ad girin ve ardından **Tamam**' a tıklayın. |
    | **Bölge** | Kaynaklarınız için bir bölge seçin. Örneğin, **Doğu ABD**. |
    | **IoT Hub adı** | *. Azure-Devices.net* ad alanı içinde genel olarak benzersiz olması gereken IoT Hub için bir ad girin. Dağıtımı doğruladığınızda, sonraki bölümde hub adı gerekir. |
    | **Sağlama hizmeti adı** | Yeni cihaz sağlama hizmeti (DPS) kaynağı için bir ad girin. Ad, *. Azure-Devices-Provisioning.net* ad alanı içinde genel olarak benzersiz olmalıdır. Dağıtımı doğruladığınızda, sonraki bölümde DPS adının olması gerekir. |
    
3. Sonraki ekranda, koşulları okuyun. Tüm koşulları kabul ediyorsanız, **Oluştur**' a tıklayın. 

    Dağıtımın tamamlanması birkaç dakika sürer. 

    Azure portal ek olarak, Azure PowerShell, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-powershell.md).


#### <a name="deploy-with-the-azure-cli"></a>Azure CLı ile dağıtma

Azure CLı 'nin kullanılması için sürüm 2,6 veya üzeri gerekir. Azure CLı 'yi yerel olarak çalıştırıyorsanız, şunu çalıştırarak sürümünüzü doğrulayın: `az --version`

Azure hesabınızda oturum açın ve aboneliğinizi seçin.

1. Azure CLı 'yı portalda çalıştırmak yerine yerel olarak çalıştırıyorsanız, oturum açmanız gerekir. Komut isteminde oturum açmak için [login komutunu](/cli/azure/get-started-with-az-cli2)çalıştırın:
    
    ```azurecli
    az login
    ```

    Kodu kullanarak kimlik doğrulaması gerçekleştirmek için yönergeleri uygulayın ve bir web tarayıcısı üzerinden Azure hesabınızda oturum açın.

2. Birden fazla Azure aboneliğiniz varsa Azure’da oturum açtığınızda, kimlik bilgilerinizle ilişkili tüm Azure hesaplarınıza erişim izni elde edersiniz. Kullanabileceğiniz [Azure hesaplarını listelemek için aşağıdaki komutu](/cli/azure/account) kullanın:
    
    ```azurecli
    az account list -o table
    ```

    IoT Hub ve DPS kaynaklarınızı oluşturmak üzere komutları çalıştırmak için kullanmak istediğiniz aboneliği seçmek için aşağıdaki komutu kullanın. Önceki komutun çıkışında yer alan abonelik adını veya kimliği kullanabilirsiniz:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Aşağıdaki komutları kopyalayıp CLı komut istemine yapıştırın. Ardından **ENTER** tuşuna basarak komutları yürütün.
   
    > [!TIP]
    > Komutlar, bir kaynak grubu konumu ister. Önce komutu çalıştırarak kullanılabilir konumların bir listesini görüntüleyebilirsiniz:
    >
    > `az account list-locations -o table`
    >
    >
    
    ```azurecli-interactive
    read -p "Enter a project name that is used for generating resource names:" projectName &&
    read -p "Enter the location (i.e. centralus):" location &&
    templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-device-provisioning/azuredeploy.json" &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
    echo "Press [ENTER] to continue ..." &&
    read
    ```

4. Aşağıdaki bilgileri komut isteminde bulabilirsiniz. Her bir değeri girip **ENTER** tuşuna basın.

    | Parametre | Açıklama |
    | :-------- | :---------- |
    | **Proje adı** | Bu parametrenin değeri, tüm kaynakları barındıracak bir kaynak grubu oluşturmak için kullanılır. Dize, `rg` kaynak grubu adınızın değerinin sonuna eklenecektir. |
    | **konumuna** | Bu değer, tüm kaynakların bulunacağı bölgedir. |
    | **iotHubName** | *. Azure-Devices.net* ad alanı içinde genel olarak benzersiz olması gereken IoT Hub için bir ad girin. Dağıtımı doğruladığınızda, sonraki bölümde hub adı gerekir. |
    | **provisioningServiceName** | Yeni cihaz sağlama hizmeti (DPS) kaynağı için bir ad girin. Ad, *. Azure-Devices-Provisioning.net* ad alanı içinde genel olarak benzersiz olmalıdır. Dağıtımı doğruladığınızda, sonraki bölümde DPS adının olması gerekir. |

    AzureCLI, şablonu dağıtmak için kullanılır. Azure CLı 'ya ek olarak, Azure PowerShell, Azure portal ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-powershell.md).


## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

1. Dağıtımı doğrulamak için, [kaynakları listelemek](/cli/azure/resource#az_resource_list) ve çıktıda yeni sağlama hizmeti ve IoT Hub 'ını aramak için aşağıdaki komutu çalıştırın:

    ```azurecli
     az resource list -g "${projectName}rg"
    ```

2. Hub 'ın zaten DPS kaynağına bağlı olduğunu doğrulamak için aşağıdaki [DPS uzantı göster komutunu](/cli/azure/iot/dps#az_iot_dps_show)çalıştırın.

    ```azurecli
     az iot dps show --name <Your provisioningServiceName>
    ```

    Üyeye bağlı olan hublara dikkat edin `iotHubs` .


## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu koleksiyondaki diğer hızlı başlangıçlar, bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıçlar veya öğreticilerle devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, kaynak grubunu ve tüm kaynaklarını silmek için Azure portal veya Azure CLı 'yi kullanabilirsiniz.

Bir kaynak grubunu ve tüm kaynaklarını Azure portal silmek için, kaynak grubunu açın ve **kaynak grubunu sil** ' e ve üst kısmına tıklayın.

Azure CLı kullanılarak dağıtılan kaynak grubunu silmek için:

```azurecli
az group delete --name "${projectName}rg"
```

Ayrıca, Azure portal, PowerShell veya REST API 'Lerini kullanarak kaynak gruplarını ve tek tek kaynakları silebilir ve Azure Resource Manager veya IoT Hub cihaz sağlama hizmeti için yayımlanmış desteklenen platform SDK 'larını kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ve bir cihaz sağlama hizmeti örneği dağıttık ve iki kaynağı bağladınız. Bir cihaz sağlamak üzere bu kurulumu nasıl kullanacağınızı öğrenmek için bir cihaz oluşturmaya yönelik hızlı başlangıç 'a geçin.

> [!div class="nextstepaction"]
> [Bir cihaz sağlamak için hızlı başlangıç](./quick-create-simulated-device-symm-key.md)
