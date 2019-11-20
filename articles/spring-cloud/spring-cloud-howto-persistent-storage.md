---
title: Azure yay bulutu 'nda kalıcı depolamayı kullanma | Microsoft Docs
description: Azure yay bulutu 'nda kalıcı depolamayı kullanma
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: d70e7ff747b80b661e848f1c208f0d1c2c928248
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607782"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Azure yay bulutu 'nda kalıcı depolamayı kullanma

Azure yay bulutu, uygulamanız için iki tür depolama alanı sağlar: kalıcı ve geçici.  Azure yay bulutu, her uygulama örneği için varsayılan olarak geçici depolama alanı sunar. Geçici depolama, varsayılan bağlama yoluyla 5 GB ile sınırlıdır: `/tmp`.

> [!WARNING]
> Bir uygulama örneğini yeniden başlatmak, ilişkili geçici depolamayı kalıcı olarak siler.

Kalıcı depolama, uygulama başına ayrılan Azure tarafından yönetilen bir dosya paylaşma kapsayıcısıdır. Kalıcı depolamada depolanan veriler tüm uygulamanın örnekleri arasında paylaşılır. Azure yay bulut hizmeti örneği, kalıcı disk etkin olan en fazla 10 uygulama içerebilir. Her uygulama 50 GB kalıcı depolama alanı alır. Kalıcı depolama için varsayılan bağlama yolu `/persistent`.

> [!WARNING]
> Kalıcı depolamayı *devre dışı bırakmak* , bu uygulama için depolamayı serbest bırakır.  Bu depolama hesabındaki tüm veriler kaybedilecek. 

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Azure portal kullanarak kalıcı depolamayı etkinleştirme

1. Azure portal ana ekranından **tüm kaynaklar**' ı seçin.

     >![Tüm kaynaklar simgesini bulma](media/portal-all-resources.jpg)

1. Kalıcı depolama gerektiren Azure Spring Cloud kaynağını bulup seçin.  Bu örnekte, uygulama *jpspring*olarak adlandırılır.

    > ![Applicationb 'nizi bulun](media/select-service.jpg)

1. **Ayarlar** başlığı altında **uygulamalar**' ı seçin.

1. Spring Cloud Services, tablosunda görünür.  Kalıcı depolama eklemek istediğiniz hizmeti seçin.  Bu örnekte, **ağ geçidi** hizmetimizi seçeceğiz.

    > ![Hizmetinizi seçin](media/select-gateway.jpg)

1. Hizmetin yapılandırma dikey penceresinde **yapılandırma** ' yı seçin.

1. **Kalıcı depolama** sekmesini seçin ve kalıcı depolamayı etkinleştirin.

    > ![Kalıcı depolamayı etkinleştir](media/enable-persistent-storage.jpg)

Kalıcı depolama etkinleştirildiğinde, boyutu ve yolu bu sayfada her ikisi de gösterilir.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Kalıcı depolamayı değiştirmek için Azure CLı 'yi kullanma

Gerekirse, Azure CLı için yay bulutu uzantısını yüklerken:

```azurecli
az extension add --name spring-cloud
```

Kalıcı disk etkin olan bir uygulama oluşturun:
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Mevcut bir uygulamada kalıcı depolamayı etkinleştir:

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
``` 

Mevcut bir uygulamada kalıcı depolamayı devre dışı bırak:

> [!WARNING]
> Kalıcı depolamayı devre dışı bırakmak, bu uygulamanın depolama alanını serbest bırakır ve burada depolanan tüm verileri kalıcı olarak kaybedilir. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>Sonraki adımlar

[Uygulama ve hizmet kotaları](spring-cloud-quotas.md)hakkında bilgi edinin ya da [uygulamanızı el ile ölçeklendirmenin](spring-cloud-tutorial-scale-manual.md)nasıl yapılacağını öğrenin.