---
title: 'Hızlı başlangıç: Azure CLı kullanarak cihaz sağlama hizmeti kurma'
description: Azure Hızlı Başlangıç - Azure CLI kullanarak Azure IoT Hub Cihazı Sağlama Hizmeti’ni ayarlama
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 20ad503582a35c1b47e90ca1c8c53af2c10fc6f8
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151958"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Hızlı başlangıç: Azure CLı ile IoT Hub cihaz sağlama hizmetini ayarlama

Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıç, bir IoT Hub 'ı ve bir IoT Hub cihaz sağlama hizmeti oluşturmak ve iki hizmeti birbirine bağlamak için Azure CLı kullanarak ayrıntıları sağlar. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!IMPORTANT]
> Bu hızlı başlangıçta oluşturduğunuz IoT Hub ve sağlama hizmeti, DNS uç noktaları olarak herkese açık bir şekilde keşfedilir. Bu kaynaklar için kullanılan adları değiştirmeye karar verirseniz, hassas bilgiler kullanmaktan kaçının.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group#az-group-create) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

Aşağıdaki örnek *westus* konumunda *my-sample-resource-group* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Örnekte kaynak grubu Batı ABD konumunda oluşturulur. `az account list-locations -o table` komutunu çalıştırarak kullanılabilir konumların listesini görüntüleyebilirsiniz.
>
>

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) komutuyla bir IoT hub oluşturun.

Aşağıdaki örnekte, *westus* konumunda *my-sample-hub* adlı bir IoT hub oluşturulur. IoT Hub adı Azure 'da genel olarak benzersiz olmalıdır; bu nedenle örnek adına benzersiz bir ön ek veya sonek eklemek ya da tamamen yeni bir ad seçmek isteyebilirsiniz. Adınızın bir IoT Hub 'ı için doğru adlandırma kurallarına uyduğundan emin olun: 3-50 karakter uzunluğunda olmalıdır ve yalnızca büyük veya küçük harf alfasayısal karakterlerini veya kısa çizgileri ('-') içerebilir. 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Cihaz sağlama hizmeti oluşturma

[Az IoT DPS Create](/cli/azure/iot/dps#az-iot-dps-create) komutuyla bir cihaz sağlama hizmeti oluşturun. 

Aşağıdaki örnek *westus* konumunda *My-Sample-DPS* adlı bir sağlama hizmeti oluşturur. Ayrıca, kendi sağlama hizmetiniz için genel olarak benzersiz bir ad seçmeniz gerekir. IoT Hub cihaz sağlama hizmeti için doğru adlandırma kurallarına uyduğundan emin olun: 3-64 karakter uzunluğunda olmalıdır ve yalnızca büyük veya küçük harf alfasayısal karakterlerini veya kısa çizgileri ('-') içerebilir.

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Örnekte sağlama hizmeti Batı ABD konumunda oluşturulur. `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` komutunu çalıştırarak veya [Azure Durumu](https://azure.microsoft.com/status/) sayfasına gidip "Cihaz Sağlama Hizmeti" için arama yaparak kullanılabilir konumların listesini görüntüleyebilirsiniz. Komutlarda, konumlar tek bir sözcük veya çok sözcüklü biçimde belirtilebilir; Örneğin: westus, Batı ABD, Batı ABD, vb. Değer büyük/küçük harfe duyarlı değildir. Konumu belirtirken birden çok sözcük biçimini kullanırsanız, değeri çift tırnak içine alın; örneğin, `-- location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>IoT hub için bağlantı dizesini alma

Cihaz Sağlama Hizmeti ile arasında bağlantı kurmak için IoT hub'ınızın bağlantı dizesine ihtiyacınız vardır. Bağlantı dizesini almak için [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) komutunu kullanın ve komutunu çıkışını kullanarak iki kaynağı bağlarken kullanacağınız değişkeni ayarlayın. 

Aşağıdaki örnek, *Hubconnectionstring* değişkenini, hub 'ın *ıothubowner* ilkesinin birincil anahtarı için bağlantı dizesinin değerine ayarlar (`--policy-name` parametresi farklı bir ilke belirtmek için kullanılabilir). Daha önce seçtiğiniz benzersiz IoT Hub adı için *My-Sample-hub ' ı* ticareti yapın. Komut, komut çıkışından bağlantı dizesini ayıklamak için Azure CLI [query](/cli/azure/query-azure-cli) ve [output](/cli/azure/format-output-azure-cli#tsv-output-format) seçeneklerini kullanır.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Bağlantı dizesini görmek için `echo` komutunu kullanabilirsiniz.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Bu iki komut, Bash altında çalışan konaklar için geçerlidir. Yerel bir Windows/CMD kabuğu veya PowerShell konağı kullanıyorsanız, söz konusu ortama yönelik doğru söz dizimini kullanacak şekilde komutları değiştirmeniz gerekir.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>IoT hub ile cihaz sağlama hizmetini bağlama

IoT hub ile sağlama hizmetinizi bağlamak için [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create) komutunu kullanın. 

Aşağıdaki örnek, *westus* konumunda *My-Sample-hub* adlı bir IoT Hub 'ını ve *My-Sample-DPS*adlı bir cihaz sağlama hizmetini bağlar. Bu adları, daha önce seçtiğiniz benzersiz IoT Hub ve cihaz sağlama hizmeti adları için ticari olarak yapın. Bu komut, önceki adımda *Hubconnectionstring* değişkeninde depolanan IoT Hub 'ınız için bağlantı dizesini kullanır.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Komutun tamamlanması birkaç dakika sürebilir.

## <a name="verify-the-provisioning-service"></a>Sağlama hizmetini doğrulama

[az iot dps show](/cli/azure/iot/dps#az-iot-dps-show) komutuyla sağlama hizmetinizin ayrıntılarını alın.

Aşağıdaki örnekte *my-sample-dps* adlı sağlama hizmetinin ayrıntıları alınır. Kendi cihaz sağlama hizmeti adınız için bu adı dengeleyebilirsiniz.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Bağlı IoT hub, *properties.iotHubs* koleksiyonunda gösterilir.

![Sağlama hizmetini doğrula](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu koleksiyondaki diğer hızlı başlangıçlar, bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıçlar veya öğreticilerle devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, aşağıdaki komutları kullanarak sağlama hizmetini, IoT hub'ı veya kaynak grubuyla bu grubun tüm kaynaklarını silebilirsiniz. Aşağıda yazılan kaynakların adlarını kendi kaynaklarınızın adlarıyla değiştirin.

Sağlama hizmetini silmek için [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete) komutunu çalıştırın:

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
IoT hub'ı silmek için [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete) komutunu çalıştırın:

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Kaynak grubuyla birlikte bu kaynak grubunun tüm kaynaklarını silmek için [az group delete](/cli/azure/group#az-group-delete) komutunu çalıştırın:

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ve bir cihaz sağlama hizmeti örneği dağıttık ve iki kaynağı bağladınız. Sanal bir cihaz sağlamak üzere bu kurulumu nasıl kullanacağınızı öğrenmek için, sanal cihaz oluşturma hızlı başlangıç ile devam edin.

> [!div class="nextstepaction"]
> [Sanal cihaz oluşturmak için hızlı başlangıç](./quick-create-simulated-device.md)
