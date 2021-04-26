---
title: Azure CLı 'dan IoT Central yönetme | Microsoft Docs
description: Bu makalede, CLı kullanarak IoT Central uygulamanızın nasıl oluşturulacağı ve yönetileceği açıklanmaktadır. CLı kullanarak uygulamayı görüntüleyebilir, değiştirebilir ve kaldırabilirsiniz.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: c30781cb83436e15a217a1d43c0e39facae9f52d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770444"
---
# <a name="manage-iot-central-from-azure-cli"></a>Azure CLı 'dan IoT Central yönetme

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) web sitesinde IoT Central uygulamaları oluşturup yönetmek yerine uygulamalarınızı yönetmek IÇIN [Azure CLI](/cli/azure/) kullanabilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - CLı komutlarınızı farklı bir Azure aboneliğinde çalıştırmanız gerekiyorsa bkz. [etkin aboneliği değiştirme](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

## <a name="create-an-application"></a>Uygulama oluşturma

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Azure aboneliğinizde bir IoT Central uygulaması oluşturmak için [az IoT Central App Create](/cli/azure/iot/central/app#az_iot_central_app_create) komutunu kullanın. Örnek:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Bu komutlar öncelikle uygulamanın Doğu ABD bölgesinde bir kaynak grubu oluşturur. Aşağıdaki tabloda, **az IoT Central App Create** komutuyla kullanılan parametreler açıklanmaktadır:

| Parametre         | Açıklama |
| ----------------- | ----------- |
| resource-group    | Uygulamayı içeren kaynak grubu. Bu kaynak grubu aboneliğinizde zaten var olmalıdır. |
| location          | Varsayılan olarak, bu komut kaynak grubundaki konumu kullanır. Şu anda **Avustralya**, **Asya Pasifik**, **Avrupa**, **Birleşik Devletler**, **Birleşik Krallık** ve **Japonya** coğrafi graflarını IoT Central bir uygulama oluşturabilirsiniz. |
| name              | Azure portal uygulamanın adı. |
| alanınızın         | Uygulamanın URL 'sindeki alt etki alanı. Örnekte, uygulama URL 'SI `https://mysubdomain.azureiotcentral.com` . |
| isteyin               | Şu anda, **ST1** ya da **ST2** kullanabilirsiniz. Bkz. [Azure IoT Central fiyatlandırması](https://azure.microsoft.com/pricing/details/iot-central/). |
| şablon          | Kullanılacak uygulama şablonu. Daha fazla bilgi için aşağıdaki tabloya bakın. |
| görünen ad      | Kullanıcı arabiriminde gösterildiği şekilde uygulamanın adı. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Uygulamalarınızı görüntüleyin

IoT Central uygulamalarınızı listelemek ve meta verileri görüntülemek için [az IoT Central App List](/cli/azure/iot/central/app#az_iot_central_app_list) komutunu kullanın.

## <a name="modify-an-application"></a>Bir uygulamayı değiştirme

Bir IoT Central uygulamasının meta verilerini güncelleştirmek için [az IoT Central App Update](/cli/azure/iot/central/app#az_iot_central_app_update) komutunu kullanın. Örneğin, uygulamanızın görünen adını değiştirmek için:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Uygulamayı kaldırma

Bir IoT Central uygulamasını silmek için [az IoT Central App Delete](/cli/azure/iot/central/app#az_iot_central_app_delete) komutunu kullanın. Örnek:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamalarını Azure CLı 'dan nasıl yönetebileceğinizi öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Uygulamanızı yönetme](howto-administer.md)
