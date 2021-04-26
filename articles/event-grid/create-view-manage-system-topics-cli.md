---
title: CLı kullanarak Azure Event Grid sistem konuları oluşturma, görüntüleme ve yönetme
description: Bu makalede, Azure CLı kullanarak sistem konuları oluşturma, görüntüleme ve silme işlemlerinin nasıl yapılacağı gösterilmektedir.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34a098406762fd57dc9dc4b58fc375286f5d5b13
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874306"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Azure CLı kullanarak Event Grid sistem konuları oluşturma, görüntüleme ve yönetme
Bu makalede, Azure CLı kullanarak sistem konuları oluşturma ve yönetme işlemlerinin nasıl yapılacağı gösterilir. Sistem konularına genel bakış için bkz. [sistem konuları](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Azure CLı için uzantıyı yükler
Azure CLı için [Event Grid uzantıya](/cli/azure/azure-cli-extensions-list)ihtiyacınız vardır.

Cloud Shell:

- Uzantıyı daha önce yüklediyseniz, güncelleştirin: `az extension update -n eventgrid`
- Uzantıyı daha önce yüklemediyseniz, yükleme:  `az extension add -n eventgrid`

Yerel yükleme için:

1. [Azure CLI 'Yı yükler](/cli/azure/install-azure-cli). İle kontrol ederek en son sürüme sahip olduğunuzdan emin olun `az --version` .
2. Uzantının önceki sürümlerini kaldırın: `az extension remove -n eventgrid`
3. Eventgrid uzantısını şu şekilde yükler `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Bir sistem konusu oluşturun

- Önce bir Azure kaynağı üzerinde bir sistem konusu oluşturmak ve ardından bu konu için bir olay aboneliği oluşturmak için aşağıdaki başvuru konularına bakın:
    - [az eventgrid sistem-konu oluştur](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        `topic-type`Bir sistem konusu oluşturmak için kullanabileceğiniz değerlerin listesi için aşağıdaki komutu çalıştırın. Bu konu türü değerleri, sistem konuları oluşturmayı destekleyen olay kaynaklarını temsil eder. Lütfen `Microsoft.EventGrid.Topics` listeden ve listeden yoksayın `Microsoft.EventGrid.Domains` . 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid System-topic olayı-abonelik oluşturma](/cli/azure/eventgrid/system-topic/event-subscription#az_eventgrid_system_topic_event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Bir Azure kaynağı için bir olay aboneliği oluştururken bir sistem konusu (örtük) oluşturmak için [az eventgrid Event-Subscription Create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) metodunu kullanın. Aşağıda bir örnek verilmiştir:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    Adım adım yönergeler içeren bir öğretici için bkz. [depolama hesabına abone olma](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account).

## <a name="view-all-system-topics"></a>Tüm sistem konularını görüntüleme
Seçilen bir sistem konusunun tüm sistem konularını ve ayrıntılarını görüntülemek için aşağıdaki komutları kullanın:

- [az eventgrid sistem-konu listesi](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid System-topic Show](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Bir sistem konusunu silme
Bir sistem konusunu silmek için aşağıdaki komutu kullanın: 

- [az eventgrid sistem-topic Delete](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Sonraki adımlar
Azure Event Grid tarafından desteklenen sistem konuları ve konu türleri hakkında daha fazla bilgi edinmek için [Azure Event Grid bölümündeki sistem konularına](system-topics.md) bakın. 