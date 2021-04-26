---
title: Veri ayıklanmasıyla koruma etkin bir çalışma alanı oluşturma
description: Bu makalede, Azure SYNAPSE Analytics 'te veri ayıklanma koruması ile çalışma alanı oluşturma açıklanmaktadır
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 59c2f5e5738b29aa11e9227b157f8b11d53f2b25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598091"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>Veri ayıklanmasıyla koruma etkin bir çalışma alanı oluşturma
Bu makalede, veri alma koruması etkin olan bir çalışma alanı oluşturma ve bu çalışma alanı için onaylanan Azure AD kiracılarının nasıl yönetileceği açıklanır.

>[!Note]
>Çalışma alanı oluşturulduktan sonra yönetilen sanal ağ ve veri savunma koruması için çalışma alanı yapılandırmasını değiştiremezsiniz.

## <a name="prerequisites"></a>Önkoşullar
- Azure 'da çalışma alanı kaynağı oluşturma izinleri.
- Yönetilen özel uç noktalar oluşturmak için çalışma alanı izinlerini SYNAPSE.
- Ağ kaynak sağlayıcısı için kaydedilen abonelikler. [Daha fazla bilgi edinin.](../../azure-resource-manager/management/resource-providers-and-types.md)

Çalışma alanınızı oluşturmaya başlamak için [hızlı başlangıç: bir Synapse çalışma alanı oluşturma](../quickstart-create-workspace.md) bölümünde listelenen adımları izleyin. Çalışma alanınızı oluşturmadan önce, çalışma alanınıza veri ayıklanma koruması eklemek için aşağıdaki bilgileri kullanın.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>Çalışma alanınızı oluştururken veri alma koruması ekleme
1. Ağ sekmesinde "yönetilen sanal ağı etkinleştir" onay kutusunu seçin.
1. "Yalnızca onaylanan hedeflere giden veri trafiğine Izin ver" seçeneğini belirleyin.
1. Bu çalışma alanı için onaylanan Azure AD kiracılarını seçin.
1. Yapılandırmayı gözden geçirin ve çalışma alanını oluşturun.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="' Sanal ağ yönetimini etkinleştir ' seçiliyken bir create SYNAPSE çalışma alanı gösteren ekran görüntüsü.":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>Çalışma alanı için onaylanan Azure Active Directory kiracılarını yönetme
1. Çalışma alanının Azure portal, "onaylanan Azure AD kiracılar" bölümüne gidin. Çalışma alanı için onaylanan Azure AD kiracılarının listesi burada listelenir. Çalışma alanının kiracısı varsayılan olarak dahil edilmiştir ve listelenmez.
1. Onaylanan listeye yeni kiracılar eklemek için "+ Ekle" kullanın.
1. Bir Azure AD kiracısını onaylanan listeden kaldırmak için, kiracıyı seçin ve "Sil" ve ardından "Kaydet" i seçin.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="Veri taşmasıyla koruma koruması ile çalışma alanı oluşturma":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>Onaylanan Azure AD kiracılarında Azure kaynaklarına bağlanma

Azure AD kiracılarında bulunan Azure kaynaklarına bağlanmak için, bir çalışma alanı için onaylanan yönetilen özel uç noktalar oluşturabilirsiniz. [Yönetilen özel uç noktalar oluşturma](./how-to-create-managed-private-endpoints.md)kılavuzunda listelenen adımları izleyin.

>[!IMPORTANT]
>Çalışma alanının kiracısından başka kiracılardaki kaynakların, SQL havuzlarının bunlara bağlanabilmesi için yerinde güvenlik duvarı kuralları engellenmemelidir. Çalışma alanının yönetilen sanal ağı içindeki Spark kümeleri gibi kaynaklar, güvenlik duvarı korumalı kaynaklara yönetilen özel bağlantılar üzerinden bağlanabilir.

## <a name="known-limitations"></a>Bilinen sınırlamalar
Kullanıcılar, PyPI gibi genel depolardan Python paketlerini yüklemek için bir ortam yapılandırma dosyası sağlayabilir. Veri ayıklanma korumalı çalışma alanlarında, giden depolara bağlantılar engellenir. Sonuç olarak, PyPI gibi genel depolardan yüklenen Python kitaplığı desteklenmez. 

Alternatif olarak, kullanıcılar, birincil Azure Data Lake Storage hesabıyla çalışma alanı paketlerini karşıya yükleyebilir veya özel bir kanal oluşturabilir. Daha fazla bilgi için [Azure SYNAPSE Analytics 'Te paket yönetimini](./spark/../../spark/apache-spark-azure-portal-add-libraries.md) ziyaret edin 
  
## <a name="next-steps"></a>Sonraki adımlar

[SYNAPSE çalışma alanlarında veri ayıklanma koruması](./workspace-data-exfiltration-protection.md) hakkında daha fazla bilgi edinin

[Yönetilen çalışma alanı sanal ağı](./synapse-workspace-managed-vnet.md) hakkında daha fazla bilgi edinin

[Yönetilen özel uç noktalar](./synapse-workspace-managed-private-endpoints.md) hakkında daha fazla bilgi edinin

[Veri kaynaklarınızda yönetilen özel uç noktalar oluşturun](./how-to-create-managed-private-endpoints.md)
