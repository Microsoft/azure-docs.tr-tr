---
title: Azure SYNAPSE Analytics çalışma alanları için veri ayıklanma koruması
description: Bu makalede, Azure SYNAPSE Analytics 'te veri alma koruması açıklanmaktadır
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 5fed7649353c0ef6cb222c0b0c91a5203665e9fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598057"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Azure SYNAPSE Analytics çalışma alanları için veri ayıklanma koruması
Bu makalede, Azure SYNAPSE Analytics 'te veri alma koruması açıklanmaktadır

## <a name="securing-data-egress-from-synapse-workspaces"></a>SYNAPSE çalışma alanlarından veri çıkışı güvenliğini sağlama
Azure SYNAPSE Analytics çalışma alanları, çalışma alanları için veri şifreleme korumasını etkinleştirmeyi destekler. Azure kaynaklarınıza erişen kötü amaçlı öngörülere karşı koruma sayesinde, hassas verileri kuruluşunuzun kapsamı dışındaki konumlara karşı izleyebilirsiniz. Çalışma alanı oluşturma sırasında, çalışma alanını yönetilen bir sanal ağla yapılandırmayı ve verilerin veri taşmasıyla ilgili ek korumayı seçebilirsiniz. [Yönetilen bir sanal ağla](./synapse-workspace-managed-vnet.md)bir çalışma alanı oluşturulduğunda, veri tümleştirme ve Spark kaynakları yönetilen sanal ağda dağıtılır. Çalışma alanının adanmış SQL havuzları ve sunucusuz SQL havuzları çok kiracılı yeteneklere sahiptir ve bu nedenle, yönetilen sanal ağın dışında mevcut olması gerekir. Veri alma koruması olan çalışma alanları için, yönetilen sanal ağ içindeki kaynaklar her zaman [yönetilen özel uç noktalar](./synapse-workspace-managed-private-endpoints.md) üzerinden iletişim kurar ve SYNAPSE SQL kaynakları yalnızca yetkili Azure kaynaklarına bağlanabilir (çalışma alanından onaylanan yönetilen özel uç nokta bağlantılarının hedefleri). 

> [!Note]
> Çalışma alanı oluşturulduktan sonra yönetilen sanal ağ ve veri savunma koruması için çalışma alanı yapılandırmasını değiştiremezsiniz.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>SYNAPSE çalışma alanı veri çıkışı 'nı onaylanan hedeflere yönetme
Çalışma alanı veri ayıklanma koruması etkinken oluşturulduktan sonra çalışma alanı kaynağının sahipleri, çalışma alanı için onaylanan Azure AD kiracılarının listesini yönetebilir. Çalışma alanı üzerinde [doğru izinlere](./synapse-workspace-access-control-overview.md) sahip kullanıcılar, çalışma alanının onaylanan Azure AD kiracılarındaki kaynaklara yönetilen özel uç nokta bağlantı istekleri oluşturmak Için SYNAPSE Studio 'yu kullanabilir. Kullanıcı onaylanmamış bir Kiracıdaki bir kaynağa özel bir uç nokta bağlantısı oluşturmayı denerse yönetilen özel uç nokta oluşturma engellenir.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>Veri dışlanan koruma özellikli örnek çalışma alanı
SYNAPSE çalışma alanları için veri dışlanan korumasını göstermek üzere bir örnek kullanalım. Contoso, kiracı A ve B kiracısında Azure kaynaklarına sahiptir ve bu kaynakların güvenli bir şekilde bağlanmasına gerek vardır. Kiracıda bir Synapse çalışma alanı, onaylı bir Azure AD kiracısı olarak B kiracısı eklenmiş şekilde oluşturulmuştur. Diyagramda, kiracı A ve kiracı B 'de depolama hesabı sahipleri tarafından onaylanan Azure depolama hesaplarına yönelik özel uç nokta bağlantıları gösterilmektedir. Diyagram ayrıca engellenen özel uç nokta oluşturmayı gösterir. Bu özel uç noktanın oluşturulması, contoso çalışma alanı için onaylanan bir Azure AD kiracısı olmayan fabrikam Azure AD kiracısında bir Azure Depolama hesabını hedeflediğinden engellendi.

:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="Bu diyagramda, SYNAPSE çalışma alanları için veri exfiltratıon korumasının nasıl uygulandığı gösterilmektedir":::

>[!IMPORTANT]
>
> - Çalışma alanının kiracısından başka kiracılardaki kaynakların, SQL havuzlarının bunlara bağlanabilmesi için yerinde güvenlik duvarı kuralları engellenmemelidir. Çalışma alanının yönetilen sanal ağı içindeki Spark kümeleri gibi kaynaklar, güvenlik duvarı korumalı kaynaklara yönetilen özel bağlantılar üzerinden bağlanabilir.
>
> - Meta veri deposu koruması etkin olan yönetilen sanal ağa sahip SYNAPSE çalışma alanlarında metassyon devre dışı bırakıldı. Spark SQL 'i bu çalışma alanlarında kullanamazsınız.
> >

## <a name="next-steps"></a>Sonraki Adımlar

[Veri ayıklanmasıyla koruma etkin bir çalışma alanı oluşturmayı](./how-to-create-a-workspace-with-data-exfiltration-protection.md) öğrenin

[Yönetilen çalışma alanı sanal ağı](./synapse-workspace-managed-vnet.md) hakkında daha fazla bilgi edinin

[Yönetilen özel uç noktalar](./synapse-workspace-managed-private-endpoints.md) hakkında daha fazla bilgi edinin

[Veri kaynaklarınızda yönetilen özel uç noktalar oluşturun](./how-to-create-managed-private-endpoints.md)
