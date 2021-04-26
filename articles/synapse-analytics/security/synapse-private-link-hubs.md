---
title: Özel bağlantıları kullanarak bir Synapse Studio 'ya bağlanma
description: Bu makalede, özel bağlantılar kullanılarak Azure SYNAPSE Studio 'ya nasıl bağlanabileceğiniz anlatılmaktadır
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: d39beca60264023c8eb7c1bc78cd1ac15c3b45dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586633"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Azure özel bağlantı hub 'Larını kullanarak Azure SYNAPSE Studio 'ya bağlanma 

Bu makalede, Azure SYNAPSE Analytics özel bağlantı hub 'Larının özel bağlantılarının SYNAPSE Studio 'ya güvenli bir şekilde bağlanmak için nasıl kullanıldığı açıklanmaktadır. 

## <a name="azure-private-link-hubs"></a>Azure özel bağlantı hub 'Ları 
Özel bağlantıları kullanarak Azure sanal ağınızdan Azure SYNAPSE Studio 'ya güvenli bir şekilde bağlanabilirsiniz. Azure SYNAPSE Analytics özel bağlantı hub 'ları, güvenli ağınız ve SYNAPSE Studio Web deneyimi arasında bağlayıcı görevi gören Azure kaynaklarıdır. 

Özel bağlantılar kullanılarak SYNAPSE Studio 'ya bağlanmak için iki adım vardır. İlk olarak, bir özel bağlantı hub 'ı kaynağı oluşturmanız gerekir. İkinci olarak, Azure sanal ağınızdan bu özel bağlantı merkezine özel bir uç nokta oluşturmanız gerekir. Daha sonra SYNAPSE Studio ile güvenli bir şekilde iletişim kurmak için özel uç noktaları kullanabilirsiniz. Özel uç noktaları, şirket içi çözümünüz veya Azure Özel DNS olan DNS çözümünüz ile tümleştirmeniz gerekir. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Azure özel bağlantılar hub 'Ları ve Azure SYNAPSE Studio
Azure SYNAPSE Studio kullanarak tüm Azure SYNAPSE Analytics çalışma alanlarınızı özel olarak bağlamak için tek bir Azure SYNAPSE özel bağlantı merkezi kaynağı kullanabilirsiniz. Çalışma alanlarının Azure SYNAPSE özel bağlantı hub 'ı ile aynı bölgede olması gerekmez. Azure SYNAPSE özel bağlantı merkezi kaynağı, farklı aboneliklerdeki veya Azure AD kiracılarındaki SYNAPSE çalışma alanlarına bağlantılar için de kullanılabilir.

Özel bağlantı hub 'ınızı, Azure portal *SYNAPSE özel bağlantı hub 'larını* arayarak ve hizmetlerden **Azure SYNAPSE Analytics (özel bağlantı hub 'ları)** seçeneğini belirleyerek oluşturabilirsiniz. Ayrıntılar için, [kısıtlı bir ağdan çalışma alanı kaynaklarına bağlanma](./how-to-connect-to-workspace-from-restricted-network.md) kılavuzundaki adımları izleyin.

>[!NOTE]
>Özel bağlantı hub 'ları, statik içerik SYNAPSE Studio 'Yu özel bağlantılar üzerinden güvenli bir şekilde yüklemek için tasarlanmıştır. Sağlanan/adanmış SQL havuzları veya Spark havuzları gibi, çalışma alanı içinde bağlamak istediğiniz kaynaklara **ayrı, Özel uç noktalar** oluşturmanız gerekir. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Azure özel bağlantı hub 'Ları ve Azure sanal ağı
SYNAPSE Studio ile uçtan uca bağlantıyı güvenli hale getirmek için Azure Sanal ağınızı SYNAPSE özel bağlantı hub kaynağına bağlamanız gerekir. Bunun için, sanal ağınızdan oluşturduğunuz özel bağlantı hub 'ına özel bir uç nokta oluşturmanız gerekir. Özel bağlantı hub 'ınız için Azure portal kullanabilir ve özel uç nokta bölümüne gidebilirsiniz. Özel bağlantı hub 'ınıza bağlanan yeni bir özel uç nokta oluşturmak için "+ özel uç nokta" seçeneğini belirleyin.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="Özel uç nokta bağlantıları sayfasını gösteren ekran görüntüsü.":::

"Kaynak" sekmesinde "Microsoft. SYNAPSE/Privatelinkhub" kaynak türünü seçtiğinizden emin olun.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="' Kaynak türü ' vurgulanmış ' özel uç nokta oluştur ' sayfasını gösteren ekran görüntüsü.":::

"Yapılandırma" sekmesinde, sanal ağınızla ve özel DNS bölgesiyle tümleştirilirken Özel DNS bölgeler için "privatelink.azuresynapse.net" öğesini seçin.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="Özel bağlantı merkezine özel bir uç nokta oluşturma":::

## <a name="next-steps"></a>Sonraki adımlar

[Kısıtlanmış bir ağdan çalışma alanı kaynaklarına bağlanma](./how-to-connect-to-workspace-from-restricted-network.md)

[Yönetilen çalışma alanı sanal ağı](./synapse-workspace-managed-vnet.md) hakkında daha fazla bilgi edinin

[Yönetilen özel uç noktalar](./synapse-workspace-managed-private-endpoints.md) hakkında daha fazla bilgi edinin

[Veri kaynaklarınızda yönetilen özel uç noktalar oluşturun](./how-to-create-managed-private-endpoints.md)

[Özel uç noktaları kullanarak SYNAPSE çalışma alanına bağlanma](./how-to-connect-to-workspace-with-private-links.md)

