---
title: Hızlı başlangıç-Service Bus kuyruğu oluşturmak için Azure CLı 'yi kullanma | Microsoft Docs
description: Bu hızlı başlangıçta, Azure CLı kullanarak bir Service Bus ad alanı ve sonra bu ad alanındaki bir kuyruğu oluşturma hakkında bilgi edineceksiniz.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 14892b114f3bb8a0bb6c07f7cdd01a174b931cf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95810621"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>Azure CLı kullanarak Service Bus bir ad alanı ve kuyruk oluşturma
Bu hızlı başlangıçta, Azure CLı kullanarak bir Service Bus ad alanı ve kuyruk oluşturma gösterilmektedir. Ayrıca, bir istemci uygulamanın sıraya/kuyruğa ileti göndermek/almak için kullanabileceği yetkilendirme kimlik bilgilerinin nasıl alınacağını gösterir. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Önkoşullar
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap][free account] oluşturabilirsiniz.

Bu hızlı başlangıçta, Azure portal oturum açtıktan sonra başlatabilmeniz Azure Cloud Shell kullanırsınız. Azure Cloud Shell hakkındaki ayrıntılar için bkz. [Azure Cloud Shell Genel Bakış](../cloud-shell/overview.md). Ayrıca, makinenizde Azure PowerShell [yükleyip](/cli/azure/install-azure-cli) kullanabilirsiniz. 

## <a name="provision-resources"></a>Kaynak sağlama
1. [Azure portal](https://portal.azure.com) oturum açın.
2. Aşağıdaki görüntüde gösterilen simgeyi seçerek Azure Cloud Shell başlatın. Cloud Shell **PowerShell** modundaysa **Bash** moduna geçin. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell Başlat":::
3. Bir Azure Kaynak grubu oluşturmak için aşağıdaki komutu çalıştırın. İsterseniz kaynak grubu adını ve konumunu güncelleştirin. 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. Service Bus mesajlaşma ad alanı oluşturmak için aşağıdaki komutu çalıştırın.

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. Önceki adımda oluşturduğunuz ad alanında bir sıra oluşturmak için aşağıdaki komutu çalıştırın. Bu örnekte, `ContosoRG` önceki adımda oluşturduğunuz kaynak grubudur. `ContosoSBusNS` , bu kaynak grubunda oluşturulan Service Bus ad alanının adıdır. 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. Ad alanı için birincil bağlantı dizesini almak için aşağıdaki komutu çalıştırın. Bu bağlantı dizesini kuyruğa bağlanmak ve ileti göndermek ve almak için kullanırsınız. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Bağlantı dizesini ve sıra adını aklınızda edin. Bunları ileti göndermek ve almak için kullanırsınız. 


## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, ad alanında bir Service Bus ad alanı ve bir sıra oluşturdunuz. Sıraya/kuyruğa ileti gönderme/alma hakkında bilgi edinmek için **Ileti gönderme ve alma** bölümünde aşağıdaki hızlı başlangıçlardan birine bakın. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

