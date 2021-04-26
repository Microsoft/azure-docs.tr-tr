---
title: Silinmiş bir depolama hesabını kurtarma
titleSuffix: Azure Storage
description: Azure portal içinde silinen bir depolama hesabını nasıl kurtaracağınızı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57cd3361d7888d9d7f747955257d96282274fd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357355"
---
# <a name="recover-a-deleted-storage-account"></a>Silinmiş bir depolama hesabını kurtarma

Silinen bir depolama hesabı, Azure portal içinden bazı durumlarda kurtarılabilir. Bir depolama hesabını kurtarmak için aşağıdaki koşulların doğru olması gerekir:

- Depolama hesabı son 14 gün içinde silindi.
- Depolama hesabı, Azure Resource Manager dağıtım modeliyle oluşturulmuştur.
- Özgün hesap silindiğinden aynı ada sahip yeni bir depolama hesabı oluşturulmamış.

Silinen bir depolama hesabını kurtarmayı denemeden önce, bu hesap için kaynak grubunun mevcut olduğundan emin olun. Kaynak grubu silinmişse, yeniden oluşturmanız gerekir. Kaynak grubunu kurtarma mümkün değildir. Hakkında daha fazla bilgi için bkz. [kaynak gruplarını yönetme](../../azure-resource-manager/management/manage-resource-groups-portal.md).

Silinen depolama hesabı, Azure Key Vault ile müşteri tarafından yönetilen anahtarları kullandıysanız ve anahtar kasası da silinmişse, depolama hesabını geri yüklemeden önce anahtar kasasını geri yüklemeniz gerekir. Daha fazla bilgi için bkz. [Azure Key Vault kurtarmaya genel bakış](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> Silinen bir depolama hesabının kurtarılması garanti edilmez. Kurtarma en iyi çaba girişimdir. Microsoft, yanlışlıkla hesap silmeyi engellemek için kaynakları kilitlemeyi önerir. Kaynak kilitleri hakkında daha fazla bilgi için bkz. [değişiklikleri engellemek için kaynakları kilitleme](../../azure-resource-manager/management/lock-resources.md).
>
> Yanlışlıkla hesap silmeyi önlemek için bir en iyi uygulama, rol tabanlı erişim denetimi (Azure RBAC) aracılığıyla bir hesabı silme iznine sahip olan kullanıcı sayısını sınırlandırmaktır. Daha fazla bilgi için bkz. [Azure RBAC Için en iyi uygulamalar](../../role-based-access-control/best-practices.md).

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Silinen bir hesabı Azure portal kurtarma

Silinen bir depolama hesabını başka bir depolama hesabından kurtarmak için şu adımları izleyin:

1. Azure portal var olan bir depolama hesabının genel bakış sayfasına gidin.
1. **Destek + sorun giderme** bölümünde, **Silinen hesabı kurtar**' ı seçin.
1. Açılan listeden, aşağıdaki görüntüde gösterildiği gibi kurtarılacak hesabı seçin. Kurtarmak istediğiniz depolama hesabı, açılan listede değilse kurtarılamaz.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Azure portal 'de depolama hesabının nasıl kurtarılacağı gösteren ekran görüntüsü":::

1. Hesabı geri yüklemek için **kurtar** düğmesini seçin. Portal, kurtarmanın devam ettiğini belirten bir bildirim görüntüler.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>Bir destek bileti aracılığıyla silinen bir hesabı kurtarma

1. Azure portal **Yardım + Destek**' e gidin.
1. **Yeni destek isteği**’ni seçin.
1. **Temel bilgiler** sekmesinde, **sorun türü** alanında **Teknik**' i seçin.
1. **Abonelik** alanında, silinen depolama hesabını içeren aboneliği seçin.
1. **Hizmet** alanında **depolama hesabı yönetimi**' ni seçin.
1. **Kaynak** alanında tüm depolama hesabı kaynakları ' nı seçin. Silinen depolama hesabı listede görünmez.
1. Sorunun kısa bir özetini ekleyin.
1. **Sorun türü** alanında, **silme ve kurtarma**' yı seçin.
1. **Sorun alt türü** alanında, **silinen depolama hesabını kurtar**' ı seçin. Aşağıdaki görüntüde, Doldurulmakta olan **temel bilgiler** sekmesinin bir örneği gösterilmektedir:

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="Destek bileti ile bir depolama hesabının nasıl kurtarılacağı gösteren ekran görüntüsü-temel kavramlar sekmesi":::

1. Ardından, **çözümler** sekmesine gidin ve aşağıdaki görüntüde gösterildiği gibi **Müşteri denetimli depolama hesabı kurtarma**' yı seçin:

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="Destek bileti-çözümler sekmesi aracılığıyla bir depolama hesabının nasıl kurtarılacağı gösteren ekran görüntüsü":::

1. Açılan listeden, aşağıdaki görüntüde gösterildiği gibi kurtarılacak hesabı seçin. Kurtarmak istediğiniz depolama hesabı, açılan listede değilse kurtarılamaz.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="Destek bileti aracılığıyla bir depolama hesabının nasıl kurtarılacağı gösteren ekran görüntüsü":::

1. Hesabı geri yüklemek için **kurtar** düğmesini seçin. Portal, kurtarmanın devam ettiğini belirten bir bildirim görüntüler.

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesabına genel bakış](storage-account-overview.md)
- [Depolama hesabı oluşturma](storage-account-create.md)
- [Genel amaçlı v2 depolama hesabına yükseltme](storage-account-upgrade.md)
- [Bir Azure Depolama hesabını başka bir bölgeye taşıma](storage-account-move.md)