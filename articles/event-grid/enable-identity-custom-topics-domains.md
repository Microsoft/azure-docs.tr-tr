---
title: Azure Event Grid özel konular ve etki alanlarında yönetilen kimliği etkinleştirin
description: Bu makalede, Azure Event Grid özel konu veya etki alanı için yönetilen hizmet kimliğinin nasıl etkinleştirileceği açıklanır.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: b93fd44282d9e19e7111dd52c73d8d4c01c67a10
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278227"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Event Grid özel bir konuya veya etki alanına sistem tarafından yönetilen bir kimlik atama 
Bu makalede, Event Grid özel konu veya etki alanı için sistem tarafından yönetilen bir kimliğin nasıl etkinleştirileceği gösterilmektedir. Yönetilen kimlikler hakkında bilgi edinmek için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="enable-identity-at-the-time-of-creation"></a>Oluşturma sırasında kimliği etkinleştir

### <a name="using-azure-portal"></a>Azure portalını kullanma
Azure portal oluştururken, özel bir konu veya etki alanı için sistem tarafından atanan kimliği etkinleştirebilirsiniz. Aşağıdaki görüntüde özel konu için sistem tarafından yönetilen bir kimliğin nasıl etkinleştirileceği gösterilmektedir. Temel olarak, konu oluşturma sihirbazının **Gelişmiş** sayfasında **sistem tarafından atanan kimliği etkinleştir** seçeneğini belirleyin. Bu seçeneği, etki alanı oluşturma sihirbazının **Gelişmiş** sayfasında de görürsünüz. 

![Özel konu oluştururken kimliği etkinleştirin](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Azure CLI’yı kullanma
Ayrıca, sistem tarafından atanan bir kimlikle özel bir konu veya etki alanı oluşturmak için Azure CLı 'yi de kullanabilirsiniz. `az eventgrid topic create`Komutunu `--identity` parametresi olarak ayarlanmış şekilde kullanın `systemassigned` . Bu parametre için bir değer belirtmezseniz, varsayılan değer `noidentity` kullanılır. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Benzer şekilde, `az eventgrid domain create` sistem tarafından yönetilen kimliğe sahip bir etki alanı oluşturmak için komutunu kullanabilirsiniz.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Mevcut bir özel konu veya etki alanı için kimliği etkinleştir
Bu bölümde, mevcut bir özel konu veya etki alanı için sistem tarafından yönetilen bir kimliği nasıl etkinleştireceğinizi öğreneceksiniz. 

### <a name="using-azure-portal"></a>Azure portalını kullanma
Aşağıdaki yordamda, özel bir konu için sistem tarafından yönetilen kimliğin nasıl etkinleştirileceği gösterilmektedir. Bir etki alanı için kimlik etkinleştirme adımları benzerdir. 

1. [Azure Portal](https://portal.azure.com) gidin.
2. Üstteki arama çubuğunda **olay Kılavuzu konularını** arayın.
3. Yönetilen kimliği etkinleştirmek istediğiniz **özel konuyu** seçin. 
4. **Kimlik** sekmesine geçin. 
5. Kimliği **etkinleştirmek için anahtarı açın.** 
1. Ayarları kaydetmek için araç çubuğunda **Kaydet** ' i seçin. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Özel konu için kimlik sayfası"::: 

Event Grid etki alanı için bir kimlik etkinleştirmek üzere benzer adımları kullanabilirsiniz.

### <a name="use-the-azure-cli"></a>Azure CLI kullanma
`az eventgrid topic update` `--identity` `systemassigned` Mevcut bir özel konu için sistem tarafından atanan kimliği etkinleştirmek üzere olarak ayarla komutunu kullanın. Kimliği devre dışı bırakmak istiyorsanız `noidentity` değer olarak belirtin. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Var olan bir etki alanını güncelleştirme komutu benzerdir ( `az eventgrid domain update` ).


## <a name="next-steps"></a>Sonraki adımlar
Kimliği, hedef üzerinde (örneğin, bir Service Bus kuyruğu) uygun bir role (örneğin, Service Bus veri gönderici) ekleyin. Ayrıntılı adımlar için bkz. [yönetilen kimlik Event Grid hedefe erişim verme](add-identity-roles.md). 
