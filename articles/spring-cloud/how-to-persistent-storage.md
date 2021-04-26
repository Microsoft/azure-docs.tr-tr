---
title: Azure yay bulutu 'nda kalıcı depolamayı kullanma | Microsoft Docs
description: Azure yay bulutu 'nda kalıcı depolamayı kullanma
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 8811a30d2d0d6a31ff722e7f470b7e7bc38c4158
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878779"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Azure Spring Cloud'da kalıcı depolama kullanma

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Azure yay bulutu, uygulamanız için iki tür depolama alanı sağlar: kalıcı ve geçici.

Azure yay bulutu, varsayılan olarak her uygulama örneği için geçici depolama sağlar. Geçici depolama, varsayılan bağlama yolu olan/t MPa ile örnek başına 5 GB ile sınırlıdır.

> [!WARNING]
> Bir uygulama örneğini yeniden başlatırsanız, ilişkili geçici depolama kalıcı olarak silinir.

Kalıcı depolama, Azure tarafından yönetilen ve uygulama başına ayrılan bir dosya paylaşma kapsayıcısıdır. Kalıcı depolamada depolanan veriler, bir uygulamanın tüm örnekleri tarafından paylaşılır. Azure yay bulutu örneği, kalıcı depolama özellikli en fazla 10 uygulama içerebilir. Her uygulamaya 50 GB kalıcı depolama alanı ayrılır. Kalıcı depolama için varsayılan bağlama yolu/persistent'dir.

> [!WARNING]
> Bir uygulamanın kalıcı depolama alanını devre dışı bırakırsanız, bu depolama alanı serbest bırakılır ve tüm depolanan veriler kaybolur.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Kalıcı depolamayı etkinleştirmek için Azure portal kullanma

1. Azure portal **ana** sayfasından **tüm kaynaklar**' ı seçin.

    >![Tüm kaynaklar simgesini bulma](media/portal-all-resources.jpg)

1. Kalıcı depolama gerektiren Azure Spring Cloud kaynağını seçin. Bu örnekte, seçilen uygulamaya **upspring** adı verilir.

    > ![Uygulamanızı seçin](media/select-service.jpg)

1. **Ayarlar** başlığı altında **uygulamalar**' ı seçin.

1. Azure Spring Cloud Services bir tabloda görüntülenir.  Kalıcı depolama eklemek istediğiniz hizmeti seçin. Bu örnekte, **ağ geçidi** hizmeti seçilidir.

    > ![Hizmetinizi seçin](media/select-gateway.jpg)

1. Hizmetin yapılandırma sayfasında **yapılandırma** ' yı seçin.

1. **Kalıcı depolama** sekmesini seçin ve **Etkinleştir**' i seçin.

    > ![Kalıcı depolamayı etkinleştir](media/enable-persistent-storage.jpg)

Kalıcı depolama etkinleştirildikten sonra yapılandırma sayfasında boyut ve yol gösterilir.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Kalıcı depolamayı değiştirmek için Azure CLı 'yi kullanma

Gerekirse, Azure CLı için yay bulutu uzantısını yüklerken:

```azurecli
az extension add --name spring-cloud
```
Diğer işlemler:

* Kalıcı depolama özellikli bir uygulama oluşturmak için:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Mevcut bir uygulama için kalıcı depolamayı etkinleştirmek için:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Mevcut bir uygulamada kalıcı depolamayı devre dışı bırakmak için:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Bir uygulamanın kalıcı depolama alanını devre dışı bırakırsanız, bu depolama alanı serbest bırakılır ve tüm depolanan veriler kalıcı olarak kaybedilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Uygulama ve hizmet kotaları](spring-cloud-quotas.md)hakkında bilgi edinin.
* [Uygulamanızı el ile ölçeklendirmeye](spring-cloud-howto-scale-manual.md)yönelik bilgi edinin.
