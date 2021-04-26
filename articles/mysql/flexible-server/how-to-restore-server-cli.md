---
title: MySQL için Azure veritabanı 'nı geri yükleme-Azure CLı ile esnek sunucu
description: Bu makalede, Azure CLı aracılığıyla MySQL için Azure veritabanı 'nda geri yükleme işlemlerinin nasıl gerçekleştirileceği açıklanır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 61a8439b770d8909e04d1b80a5219810dc72aa34
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509160"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-with-azure-cli"></a>MySQL için Azure veritabanı-Azure CLı ile esnek sunucu için bir noktadan noktaya geri yükleme


> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu makalede, yedeklemeleri kullanarak esnek sunucu 'da zaman içinde kurtarma gerçekleştirmek için adım adım yordamlar sunulmaktadır.

## <a name="prerequisites"></a>Önkoşullar
- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.
- Azure CLı 'yı en son sürüme yükler veya yükseltin. Bkz. [Azure CLI 'Yi yüklemeyi](/cli/azure/install-azure-cli).
-  [Az Login](/cli/azure/reference-index#az-login) komutunu kullanarak Azure hesabında oturum açın. Azure hesabınızın **ABONELIK kimliğini** ifade eden **ID** özelliğine göz önüne alın.

    ```azurecli-interactive
    az login
    ````

- Birden çok aboneliğiniz varsa, komutunu kullanarak sunucuyu oluşturmak istediğiniz uygun aboneliği seçin ```az account set``` .
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Komutu kullanarak henüz bir tane oluşturmadıysanız, MySQL esnek sunucusu oluşturun ```az mysql flexible-server create``` .

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Bir sunucuyu yedekten yeni bir sunucuya geri yükleme

Bir sunucuyu var olan en eski yedeklemeye geri yüklemek için aşağıdaki komutu çalıştırabilirsiniz.

**Kullanım**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**Örnek:** Bu yedekleme anlık görüntüsünden bir sunucuyu geri yükleyin ```2021-03-03T13:10:00Z``` .

```azurecli
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
Geri yükleme için geçen süre, sunucuda depolanan verilerin boyutuna bağlı olarak değişir.

## <a name="perform-post-restore-tasks"></a>Geri yükleme sonrası görevleri gerçekleştirme
Geri yükleme tamamlandıktan sonra kullanıcılarınızın ve uygulamalarınızın yedeklemesini ve çalışmasını sağlamak için aşağıdaki görevleri gerçekleştirmeniz gerekir:

- Yeni sunucu özgün sunucunun yerini alacak şekilde, istemcileri ve istemci uygulamalarını yeni sunucuya yeniden yönlendirin
- Kullanıcıların bağlanabilmesi için uygun VNet kurallarının yerinde olduğundan emin olun. Bu kurallar, özgün sunucudan üzerine kopyalanmaz.
- Uygun oturum açma ve veritabanı düzeyi izinlerinin yerinde olduğundan emin olun
- Yeni geri yükleme sunucusu için uygun olan uyarıları yapılandırın

## <a name="next-steps"></a>Sonraki adımlar
[İş sürekliliği](concepts-business-continuity.md) hakkında daha fazla bilgi edinin

