---
title: Azure CLı kullanarak IoT Hub oluşturma | Microsoft Docs
description: Azure CLı komutlarını kullanarak bir kaynak grubu oluşturup kaynak grubunda bir IoT Hub 'ı oluşturmayı öğrenin. Ayrıca, hub 'ı nasıl kaldıracağınızı öğrenin.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889511"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Azure CLı kullanarak IoT Hub 'ı oluşturma

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Bu makalede, Azure CLı kullanarak IoT Hub 'ı oluşturma konusu gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar

Bu nasıl yapılır? için bir Azure aboneliğine ihtiyacınız vardır. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Oturum açın ve Azure hesabınızı ayarlayın

Cloud Shell kullanmak yerine Azure CLı 'yi yerel olarak çalıştırıyorsanız Azure hesabınızda oturum açmanız gerekir.

Komut isteminde, [oturum açma komutunu](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)çalıştırın:

   ```azurecli
   az login
   ```

Kodu kullanarak kimlik doğrulaması gerçekleştirmek için yönergeleri uygulayın ve bir web tarayıcısı üzerinden Azure hesabınızda oturum açın.

## <a name="create-an-iot-hub"></a>IoT Hub'ı oluşturma

Azure CLı kullanarak bir kaynak grubu oluşturun ve ardından bir IoT Hub 'ı ekleyin.

1. Bir IoT Hub 'ı oluşturduğunuzda bir kaynak grubunda oluşturmanız gerekir. Mevcut bir kaynak grubunu kullanın veya [bir kaynak grubu oluşturmak için aşağıdaki komutu](https://docs.microsoft.com/cli/azure/resource)çalıştırın:
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > Önceki örnekte kaynak grubu Batı ABD konumunda oluşturulur. Şu komutu çalıştırarak kullanılabilir konumların bir listesini görebilirsiniz: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. IoT Hub 'ınız için genel olarak benzersiz bir ad kullanarak kaynak grubunuzda [bir IoT Hub 'ı oluşturmak için aşağıdaki komutu](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) çalıştırın:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Önceki komut, faturalandırdığınız S1 fiyatlandırma katmanında bir IoT Hub 'ı oluşturur. Daha fazla bilgi için bkz. [Azure IoT Hub fiyatlandırması](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>IoT Hub kaldırma

Azure CLı kullanarak IoT Hub gibi [tek bir kaynağı silebilir](https://docs.microsoft.com/cli/azure/resource)veya bir kaynak grubunu ve tüm IoT Hub 'ları dahil tüm kaynaklarını silebilirsiniz.

[Bir IoT Hub 'ını silmek](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete)için şu komutu çalıştırın:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

[Bir kaynak grubunu](https://docs.microsoft.com/cli/azure/group#az-group-delete) ve tüm kaynaklarını silmek için aşağıdaki komutu çalıştırın:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub 'ı kullanma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)
* [IoT Hub yönetmek için Azure portal kullanma](iot-hub-create-through-portal.md)
