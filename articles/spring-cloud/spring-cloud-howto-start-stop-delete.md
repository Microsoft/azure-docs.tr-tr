---
title: Azure Spring Cloud uygulamanızı başlatma, durdurma ve silme | Microsoft Docs
description: Azure Spring Cloud uygulamanızı başlatma, durdurma ve silme
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607718"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Azure Spring Cloud uygulamanızı başlatma, durdurma ve silme

Bu kılavuzda, Azure portal veya CLı kullanarak Azure Spring Cloud 'da bir uygulamanın durumunun nasıl değiştirileceği açıklanmaktadır.

## <a name="using-the-azure-portal"></a>Azure portalını kullanma

Uygulamanızı dağıttıktan sonra, Azure portal kullanarak **başlatabilir**, **durdurabilir**ve **silebilirsiniz** .

1. Azure portal Azure Spring Cloud Service örneğine gidin.
1. **Uygulama panosu** sekmesini seçin.
1. Durumunu değiştirmek istediğiniz uygulamayı seçin.
2. Bu uygulamanın **genel bakış** sayfasında, uygulamayı **başlatmak/durdurmak**, **yeniden başlatmak**ve **silmek** için düğmeler bulun.

## <a name="using-the-azure-cli"></a>Azure CLI kullanma

> [!NOTE]
> İsteğe bağlı parametreleri kullanabilir ve Varsayılanları Azure CLı ile yapılandırabilirsiniz. [Başvuru belgelerimizi](spring-cloud-cli-reference.md)okuyarak hakkında daha fazla bilgi edinin.  

Azure CLı için yay bulutu uzantısını yükler:

```azurecli
az extension add --name spring-cloud
```

* Uygulamanızı başlatmak için:
    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Uygulamanızı durdurmak için:
    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Uygulamanızı yeniden başlatmak için:
    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Uygulamanızı silmek için:
    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
