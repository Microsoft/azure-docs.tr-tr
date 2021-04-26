---
title: Depolama hesabı erişim anahtarlarını değiştirme
titleSuffix: Azure Machine Learning
description: Çalışma alanınız tarafından kullanılan Azure depolama hesabı için erişim anahtarlarını değiştirme hakkında bilgi edinin. Azure Machine Learning, verileri ve modelleri depolamak için bir Azure depolama hesabı kullanır.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: 787f7727e18b7055055ed2b764ddce5be20f5afc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503445"
---
# <a name="regenerate-storage-account-access-keys"></a>Depolama hesabı erişim anahtarlarını yeniden oluştur


Azure Machine Learning tarafından kullanılan Azure depolama hesapları için erişim anahtarlarını değiştirme hakkında bilgi edinin. Azure Machine Learning, verileri veya eğitilen modelleri depolamak için depolama hesaplarını kullanabilir.

Güvenlik nedeniyle, bir Azure depolama hesabının erişim anahtarlarını değiştirmeniz gerekebilir. Erişim anahtarını yeniden oluşturduğunuzda Azure Machine Learning yeni anahtarı kullanmak için güncelleştirilmeleri gerekir. Azure Machine Learning, hem model depolama hem de bir veri deposu olarak depolama hesabı kullanıyor olabilir.

> [!IMPORTANT]

> Veri depolarıyla kaydedilen kimlik bilgileri, çalışma alanıyla ilişkili Azure Key Vault kaydedilir. Key Vault için [geçici silme](../key-vault/general/soft-delete-overview.md) özelliği varsa, bu makalede kimlik bilgilerini güncelleştirmek için yönergeler sağlanmaktadır. Veri deposunun kaydını siler ve aynı adla yeniden kaydetmeyi denerseniz, bu eylem başarısız olur. Bkz. Bu senaryoda geçici silme özelliğini etkinleştirme için [mevcut bir Anahtar Kasası Için geçici silme özelliğini]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault) etkinleştirme.

## <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning çalışma alanı. Daha fazla bilgi için [çalışma alanı oluşturma](how-to-manage-workspace.md) makalesine bakın.

* [Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/install).

* [Azure MACHINE LEARNING CLI uzantısı](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Bu belgedeki kod parçacıkları, Python SDK 'sının 1.0.83 sürümü ile test edilmiştir.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Nelerin güncelleştirilmesi gerekiyor

Depolama hesapları, Azure Machine Learning çalışma alanı tarafından kullanılabilir (Günlükler, modeller, anlık görüntüler vb.) ve bir veri deposu olarak kullanılır. Çalışma alanını güncelleştirme işlemi tek bir Azure CLı komutu ve depolama anahtarı güncelleştirildikten sonra çalıştırılabilir. Veri depolarını güncelleştirme işlemi daha fazla yer alır ve şu anda depolama hesabını kullanan veri depolarının keşfedilmelerini ve sonra yeniden kaydettirmesini gerektirir.

> [!IMPORTANT]
> Azure CLı kullanarak çalışma alanını ve aynı zamanda Python kullanarak veri depolarını güncelleştirin. Yalnızca birini veya diğerini güncelleştirmek yeterli değildir ve her ikisi de güncelleştirilene kadar hatalara neden olabilir.

Veri depfederler tarafından kullanılan depolama hesaplarını öğrenmek için aşağıdaki kodu kullanın:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Bu kod, Azure Storage kullanan kayıtlı veri depolarına bakar ve aşağıdaki bilgileri listeler:

* Veri deposu adı: depolama hesabının kaydedildiği veri deposunun adı.
* Depolama hesabı adı: Azure depolama hesabının adı.
* Kapsayıcı: Bu kayıt tarafından kullanılan depolama hesabındaki kapsayıcı.

Ayrıca, her bir veri deposu türünü yeniden kaydetmek için farklı yöntemler olduğu için veri deposunun bir Azure Blobu veya bir Azure dosya paylaşımının olup olmadığını gösterir.

İçin erişim anahtarlarını yeniden üretmeyi planladığınız depolama hesabı için bir giriş varsa, veri deposu adını, depolama hesabı adını ve kapsayıcı adını kaydedin.

## <a name="update-the-access-key"></a>Erişim anahtarını güncelleştirme

Yeni anahtarı kullanmak üzere Azure Machine Learning güncelleştirmek için aşağıdaki adımları kullanın:

> [!IMPORTANT]
> Tüm adımları gerçekleştirin, CLı kullanarak çalışma alanını ve Python kullanarak veri depolarını güncelleştirme. Yalnızca bir veya diğerini güncelleştirmek, her ikisi de güncelleştirilene kadar hatalara neden olabilir.

1. Anahtarı yeniden oluşturun. Erişim anahtarı oluşturma hakkında bilgi için bkz. [depolama hesabı erişim anahtarlarını yönetme](../storage/common/storage-account-keys-manage.md). Yeni anahtarı kaydedin.

1. Azure Machine Learning çalışma alanı, yeni anahtarı otomatik olarak eşitler ve bir saatten sonra kullanmaya başlar. Çalışma alanını yeni anahtarla hemen eşitleme için zorlamak üzere aşağıdaki adımları kullanın:

    1. Aşağıdaki Azure CLı komutunu kullanarak çalışma alanınızı içeren Azure aboneliğinde oturum açın:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Yeni anahtarı kullanmak üzere çalışma alanını güncelleştirmek için aşağıdaki komutu kullanın. `myworkspace`Azure Machine Learning çalışma alanı adınızla değiştirin ve öğesini, `myresourcegroup` çalışma alanını içeren Azure Kaynak grubunun adıyla değiştirin.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Bu komut, çalışma alanı tarafından kullanılan Azure depolama hesabı için yeni anahtarları otomatik olarak eşitler.

1. Depolama hesabını kullanan veri depolarımı SDK veya [Azure Machine Learning Studio](https://ml.azure.com)aracılığıyla yeniden kaydedebilirsiniz.
    1. **Veri depolarını Python SDK aracılığıyla yeniden kaydetmek için**, [güncelleştirilmesi gereken](#whattoupdate) Özellikler bölümünde bulunan değerleri ve adım 1 ' deki anahtarı aşağıdaki kodla kullanın. 
    
        `overwrite=True`Belirtildiği için, bu kod var olan kaydın üzerine yazar ve yeni anahtarı kullanacak şekilde günceller.
    
        ```python
        # Re-register the blob container
        ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                                  datastore_name='your datastore name',
                                                  container_name='your container name',
                                                  account_name='your storage account name',
                                                  account_key='new storage account key',
                                                  overwrite=True)
        # Re-register file shares
        ds_file = Datastore.register_azure_file_share(workspace=ws,
                                              datastore_name='your datastore name',
                                              file_share_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
        
        ```
    
    1. **Veri depolarını Studio aracılığıyla yeniden kaydetmek için**, Studio 'nun sol bölmesindeki **veri depoları** ' nı seçin. 
        1. Güncelleştirmek istediğiniz veri deposunu seçin.
        1. Sol üstteki **kimlik bilgilerini güncelleştir** düğmesini seçin. 
        1. Formu doldurmak için adım 1 ' deki yeni erişim anahtarınızı kullanın ve **Kaydet**' e tıklayın.
        
            **Varsayılan veri deposu** için kimlik bilgilerini güncelleştiriyorsanız, bu adımı tamamlayarak yeni anahtarınızı çalışma alanının varsayılan veri deposu ile yeniden eşitleyin. 

## <a name="next-steps"></a>Sonraki adımlar

Veri depolarını kaydetme hakkında daha fazla bilgi için bkz [`Datastore`](/python/api/azureml-core/azureml.core.datastore%28class%29) . sınıf başvurusu.