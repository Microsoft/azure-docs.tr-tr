---
title: 'Hızlı başlangıç: Azure CLı kullanarak SYNAPSE çalışma alanı oluşturma'
description: Bu kılavuzdaki adımları izleyerek Azure CLı kullanarak bir Azure SYNAPSE çalışma alanı oluşturun.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 8a56b325dd5e1180b1229465965167241fab76a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676453"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Hızlı başlangıç: Azure CLı ile Azure SYNAPSE çalışma alanı oluşturma

Azure CLI, Azure kaynaklarını yönetmek için Azure tarafından sunulan komut satırı deneyimidir. Azure Cloud Shell ile kendi tarayıcınızda da kullanabilirsiniz. Dilerseniz macOS, Linux veya Windows’a yükleyebilir ve komut satırından çalıştırabilirsiniz.

Bu hızlı başlangıçta, Azure CLı kullanarak bir Synapse çalışma alanı oluşturmayı öğreneceksiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

- Hafif ve esnek bir komut satırı JSON işlemcisi olan [JQ](https://stedolan.github.io/jq/download/)'yi indirin ve yükleyin
- [Azure Data Lake Storage 2. depolama hesabı](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Azure SYNAPSE çalışma alanının seçili ADLS 2. hesabını okuyup yazabilbilmesi gerekir. Bunlara ek olarak, birincil depolama hesabı olarak bağlanan herhangi bir depolama hesabı için depolama hesabı [oluşturma](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) bölümünde açıklandığı gibi depolama hesabı oluşturulurken **hiyerarşik ad alanını** etkinleştirmiş olmanız gerekir. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Azure CLı kullanarak Azure SYNAPSE çalışma alanı oluşturma

1. Azure SYNAPSE çalışma alanı için kaynak oluşturmak üzere gerekli ortam değişkenlerini tanımlayın.

    | Ortam Değişkeni Adı | Description |
    |---|---|---|
    |StorageAccountName| Mevcut ADLS 2. depolama hesabınızın adı.|
    |StorageAccountResourceGroup| Mevcut ADLS 2. depolama hesabı kaynak grubunuzun adı. |
    |FileShareName| Mevcut depolama dosya sisteminizin adı.|
    |SynapseResourceGroup| Azure SYNAPSE kaynak grubunuz için yeni bir ad seçin. |
    |Region| [Azure bölgelerinden](https://azure.microsoft.com/global-infrastructure/geographies/#overview)birini seçin. |
    |SynapseWorkspaceName| Yeni Azure SYNAPSE çalışma alanınız için benzersiz bir ad seçin. |
    |SqlUser| Yeni Kullanıcı adı için bir değer seçin.|
    |SQLPassword de belirtilmelidir| Güvenli bir parola seçin.|
    |||

1. Azure SYNAPSE çalışma alanınız için kapsayıcı olarak bir kaynak grubu oluşturun:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```

1. Azure SYNAPSE çalışma alanı oluşturma:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

1. Azure SYNAPSE çalışma alanı için Web ve dev URL 'sini alın:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

1. Makinenizden Azure SYNAPSE çalışma alanına erişmenize izin vermek için bir güvenlik duvarı kuralı oluşturun:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

1. Çalışma alanınıza erişmek için ortam değişkeninde depolanan Azure SYNAPSE Workspace Web URL adresini açın `WorkspaceWeb` :

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [![Azure SYNAPSE çalışma alanı Web ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png)](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure SYNAPSE çalışma alanını silmek için aşağıdaki adımları izleyin.
> [!WARNING]
> Azure SYNAPSE çalışma alanını silmek, bulunan SQL havuzlarının ve çalışma alanı meta verilerinin veritabanında depolanan analiz altyapılarını ve verileri kaldırır. SQL veya Apache Spark uç noktalarına bağlanmak artık mümkün olmayacaktır. Tüm kod yapıtları silinecek (sorgular, Not defterleri, iş tanımları ve işlem hatları).
>
> Çalışma alanını silmek, çalışma alanına bağlı Data Lake Store Gen2 **verileri etkilemez.**

Azure SYNAPSE çalışma alanını silmek istiyorsanız aşağıdaki komutu doldurun:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, verilerinizi analiz etmeye ve keşfetmeye başlamak için [SQL havuzları oluşturabilir](quickstart-create-sql-pool-studio.md) veya [Apache Spark havuzları oluşturabilirsiniz](quickstart-create-apache-spark-pool-studio.md) .