---
title: Azure Spring Cloud uygulamanızı başlatma, durdurma ve silme | Microsoft Docs
description: Azure Spring Cloud uygulamanızı başlatma, durdurma ve silme
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 914325aba3d123fb1b700f0eff8ddb17119c5d12
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863218"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Azure Spring Cloud uygulamanızı başlatma, durdurma ve silme

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Bu kılavuzda, Azure Spring Cloud 'da Azure portal veya Azure CLı kullanarak bir uygulamanın durumunun nasıl değiştirileceği açıklanmaktadır.

## <a name="using-the-azure-portal"></a>Azure portalını kullanma

Bir uygulamayı dağıttıktan sonra, Azure portal kullanarak başlatabilir, durdurabilir ve silebilirsiniz.

1. Azure portal Azure Spring Cloud Service örneğine gidin.
1. **Uygulama panosu** sekmesini seçin.
1. Durumunu değiştirmek istediğiniz uygulamayı seçin.
1. Bu uygulamanın **genel bakış** sayfasında **Başlat/Durdur**, **Yeniden Başlat** veya **Sil**' i seçin.

## <a name="using-the-azure-cli"></a>Azure CLI'yı kullanma

> [!NOTE]
> İsteğe bağlı parametreleri kullanabilir ve Varsayılanları Azure CLı ile yapılandırabilirsiniz. [Başvuru belgelerimizi](/cli/azure/spring-cloud)okuyarak Azure CLI hakkında daha fazla bilgi edinin.  

İlk olarak, Azure CLı için Azure yay bulutu uzantısını aşağıdaki şekilde yüklemeniz gerekir:

```azurecli
az extension add --name spring-cloud
```

Ardından, şu Azure CLı işlemlerinden birini seçin:

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
