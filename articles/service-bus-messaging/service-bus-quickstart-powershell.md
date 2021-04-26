---
title: Service Bus kuyruğu oluşturmak için Azure PowerShell kullanma
description: Bu hızlı başlangıçta, Azure PowerShell kullanarak ad alanı ve bir sırayı Service Bus nasıl oluşturacağınızı öğreneceksiniz.
author: spelluru
ms.author: spelluru
ms.date: 08/12/2020
ms.topic: quickstart
ms.devlang: dotnet
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: f7bf9e5435b00ee3076422cccbe689038051499d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537101"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Bir Service Bus ad alanı ve sıra oluşturmak için Azure PowerShell kullanma
Bu hızlı başlangıçta, Azure PowerShell kullanarak bir Service Bus ad alanı ve kuyruğun nasıl oluşturulacağı gösterilmektedir. Ayrıca, bir istemci uygulamanın sıraya/kuyruğa ileti göndermek/almak için kullanabileceği yetkilendirme kimlik bilgilerinin nasıl alınacağını gösterir. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlayabilmeniz için bir Azure aboneliğiniz olduğundan emin olun. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap][] oluşturabilirsiniz. 

Bu hızlı başlangıçta, Azure portal oturum açtıktan sonra başlatabilmeniz Azure Cloud Shell kullanırsınız. Azure Cloud Shell hakkındaki ayrıntılar için bkz. [Azure Cloud Shell Genel Bakış](../cloud-shell/overview.md). Ayrıca, makinenizde Azure PowerShell [yükleyip](/powershell/azure/install-Az-ps) kullanabilirsiniz. 


## <a name="provision-resources"></a>Kaynak sağlama
1. [Azure portal](https://portal.azure.com) oturum açın.
2. Aşağıdaki görüntüde gösterilen simgeyi seçerek Azure Cloud Shell başlatın: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell Başlat":::
3. Alt Cloud Shell penceresinde, **Bash** 'ten **PowerShell**'e geçin. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="PowerShell moduna geç":::    
4. Bir Azure Kaynak grubu oluşturmak için aşağıdaki komutu çalıştırın. İsterseniz kaynak grubu adını ve konumunu güncelleştirin. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Service Bus mesajlaşma ad alanı oluşturmak için aşağıdaki komutu çalıştırın. Bu örnekte, `ContosoRG` önceki adımda oluşturduğunuz kaynak grubudur. `ContosoSBusNS` , bu kaynak grubunda oluşturulan Service Bus ad alanının adıdır. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Önceki adımda oluşturduğunuz ad alanında bir sıra oluşturmak için aşağıdakileri çalıştırın. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Ad alanı için birincil bağlantı dizesini alın. Bu bağlantı dizesini kuyruğa bağlanmak ve ileti göndermek ve almak için kullanırsınız. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
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

[Ücretsiz hesap]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
