---
title: "Hızlı başlangıç: PowerShell kullanarak bir olay hub 'ı oluşturma-Azure Event Hubs"
description: Bu hızlı başlangıçta Azure PowerShell'i kullanarak olay hub'ı oluşturma ve ardından .NET Standard SDK'sını kullanarak olayları gönderip alma adımları anlatılmaktadır.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: be2761bedd3198d25cb279e6a8ff188db6a51de2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873784"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell'i kullanarak olay hub'ı oluşturma

Azure Event Hubs saniyede milyonlarca olay alıp işleme kapasitesine sahip olan bir Büyük Veri akış platformu ve olay alma hizmetidir. Event Hubs dağıtılan yazılımlar ve cihazlar tarafından oluşturulan olayları, verileri ve telemetrileri işleyebilir ve depolayabilir. Bir olay hub’ına gönderilen veriler, herhangi bir gerçek zamanlı analiz sağlayıcısı ve işlem grubu oluşturma/depolama bağdaştırıcıları kullanılarak dönüştürülüp depolanabilir. Olay Hub’larının ayrıntılı genel bakışı için bkz. [Olay Hub’larına genel bakış](event-hubs-about.md) ve [Olay Hub’ları özellikleri](event-hubs-features.md).

Bu hızlı başlangıçta Azure PowerShell'i kullanarak olay hub'ı oluşturacaksınız.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğreticiyi tamamlamak için şunlar sahip olduğunuzdan emin olun:

- Azure aboneliği. Bir tane yoksa, başlamadan önce [ücretsiz bir hesap oluşturun][] .
- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [.NET Core SDK](https://dotnet.microsoft.com/download), sürüm 2,0 veya üzeri.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell'i yerel ortamda kullanıyorsanız bu hızlı başlangıcı tamamlamak için PowerShell'in en son sürümüne sahip olmanız gerekir. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure PowerShell’i Yükleme ve Yapılandırma](/powershell/azure/install-az-ps).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure kaynaklarından oluşan mantıksal bir koleksiyondur ve olay hub'ı oluşturmak için bir kaynak grubuna ihtiyacınız vardır. 

Aşağıdaki örnekte Doğu ABD bölgesinde bir kaynak grubu oluşturulmaktadır. `myResourceGroup` değerini kullanmak istediğiniz kaynak grubu adıyla değiştirin:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Event Hubs ad alanı oluşturma

Kaynak grubunuzu oluşturduktan sonra bu kaynak grubu içinde bir Event Hubs ad alanı oluşturun. Event Hubs ad alanı, içinde olay hub'ınızı oluşturabileceğiniz benzersiz bir tam etki alanı adı sağlar. `namespace_name` değerini ad alanınız için benzersiz bir adla değiştirin:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

Bir Event Hubs ad alanı oluşturduğunuza göre şimdi bu ad alanının içinde bir olay hub'ı oluşturabilirsiniz:  
İçin izin verilen süre `MessageRetentionInDays` 1 ile 7 gün arasındadır.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Tebrikler! Azure PowerShell’i kullanarak bir Event Hubs ad alanı ve bu ad alanının içinde bir olay hub'ı oluşturdunuz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Event Hubs ad alanını oluşturdunuz ve olay hub'ınızdan olay gönderip almak için örnek uygulamaları kullandınız. Olay Hub 'ından olay alma (veya) olayları gönderme hakkında adım adım yönergeler için, bkz. **olayları gönderme ve alma** öğreticileri: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Git](event-hubs-go-get-started-send.md)
- [C (yalnızca gönderme)](event-hubs-c-getstarted-send.md)
- [Apache Storm (yalnızca alma)](event-hubs-storm-getstarted-receive.md)


[Ücretsiz hesap oluşturun]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: /powershell/azure/install-az-ps
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
