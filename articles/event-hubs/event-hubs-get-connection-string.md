---
title: Bağlantı dizesi al-Azure Event Hubs | Microsoft Docs
description: Bu makalede, istemcilerin Azure Event Hubs bağlanmak için kullanabileceği bir bağlantı dizesi alma yönergeleri sağlanır.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5ae6c66ddbbf4b9946e7037e1a7723043bf60507
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86537199"
---
# <a name="get-an-event-hubs-connection-string"></a>Event Hubs bağlantı dizesi al

Event Hubs kullanmak için bir Event Hubs ad alanı oluşturmanız gerekir. Ad alanı, birden fazla olay hub 'ı veya Kafka konu başlığı için kapsam kapsayıcısı olur. Bu ad alanı size benzersiz bir [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)sağlar. Bir ad alanı oluşturulduktan sonra, Event Hubs iletişim kurmak için gereken bağlantı dizesini elde edebilirsiniz.

Azure Event Hubs için bağlantı dizesi, aşağıdaki bileşenleri içine katıştırmıştır.

* FQDN = oluşturduğunuz EventHubs ad alanının FQDN 'SI (EventHubs ad alanı adını ve ardından servicebus.windows.net) içerir
* SharedAccessKeyName = uygulamanızın SAS anahtarları için seçtiğiniz ad
* SharedAccessKey = oluşturulan anahtarın değeri.

Bağlantı dizesi şablonu şöyle görünür
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Örnek bir bağlantı dizesi şöyle görünebilir `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Bu makale, bağlantı dizesini elde etmenin çeşitli yollarını gösterir.

## <a name="get-connection-string-from-the-portal"></a>Portaldan bağlantı dizesi al
1. [Azure Portal](https://portal.azure.com)oturum açın. 
2. Sol gezinti menüsünde **tüm hizmetler** ' i seçin. 
3. **Analiz** bölümünde **Event Hubs** ' yi seçin. 
4. Olay Hub 'ları listesinde, Olay Hub 'ınızı seçin.
6. **Event Hubs ad alanı** sayfasında sol menüdeki **paylaşılan erişim ilkeleri** ' ni seçin.

    ![Paylaşılan erişim Ilkeleri menü öğesi](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. İlke listesinde bir **paylaşılan erişim ilkesi** seçin. Varsayılan bir ad: **RootManageSharedAccessPolicy**. Uygun izinlere sahip bir ilke ekleyebilirsiniz (okuma, yazma) ve bu ilkeyi kullanabilirsiniz. 

    ![Event Hubs paylaşılan erişim ilkeleri](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. **Bağlantı dizesi-birincil anahtar** alanının yanındaki **Kopyala** düğmesini seçin. 

    ![Event Hubs-bağlantı dizesi al](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Bağlantı dizesini Azure PowerShell alma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıda gösterildiği gibi, belirli bir ilke/kural adı için bağlantı dizesini almak üzere [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) komutunu kullanabilirsiniz:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Azure CLı ile bağlantı dizesini alma
Ad alanı için bağlantı dizesini almak için aşağıdakileri kullanabilirsiniz:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Ya da bir EventHub varlığına yönelik bağlantı dizesini almak için aşağıdakileri kullanabilirsiniz:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Event Hubs için Azure CLı komutları hakkında daha fazla bilgi için bkz. [Azure clı Event Hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki bağlantıları inceleyerek Event Hubs hakkında daha fazla bilgi edinebilirsiniz:

* [Event Hubs genel bakış](./event-hubs-about.md)
* [Olay Hub'ı oluşturma](event-hubs-create.md)
