---
title: Şirket içinde barındırılan tümleştirme çalışma zamanı için otomatik güncelleştirme ve süre sonu bildirimi
description: Şirket içinde barındırılan tümleştirme çalışma zamanı otomatik güncelleştirmesi ve süre sonu bildirimi hakkında bilgi edinin
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376267"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı için otomatik güncelleştirme ve süre sonu bildirimi

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, şirket içinde barındırılan tümleştirme çalışma zamanı 'nın en son sürüme otomatik güncelleştirmesine ve ADF 'nin şirket içinde barındırılan tümleştirme çalışma zamanının sürümlerini nasıl yönettiği açıklanır.

## <a name="self-hosted-integration-runtime-auto-update"></a>Kendiliğinden konak Integration Runtime otomatik güncelleştirme
Genellikle, yerel makinenize veya bir Azure VM 'ye şirket içinde barındırılan bir tümleştirme çalışma zamanı yüklediğinizde, şirket içinde barındırılan tümleştirme çalışma zamanının sürümünü yönetmek için iki seçeneğiniz vardır: otomatik güncelleştirme veya el ile bakım. Genellikle ADF, yeni özellik sürümü, hata onarımı veya geliştirme içeren her ay otomatik olarak barındırılan tümleştirme çalışma zamanının iki yeni sürümünü yayınlar. Bu nedenle, en yeni özellik ve geliştirmeyi sağlamak için kullanıcıların en son sürüme güncelleştirilmesini öneririz.

En kolay yol, şirket içinde barındırılan tümleştirme çalışma zamanı oluştururken veya düzenlerken otomatik güncelleştirmeyi etkinleştirmektir. Bu durumda, en son sürüme otomatik olarak güncelleştirilecek. Güncelleştirmeyi istediğiniz zaman dilimine göre de zamanlayabilirsiniz.

![Otomatik güncelleştirmeyi etkinleştir](media/create-self-hosted-integration-runtime/shir-auto-update.png)

Şirket içinde barındırılan tümleştirme çalışma zamanı istemcinizdeki son güncelleştirme tarih/saati ' ni kontrol edebilirsiniz.

![Güncelleştirme süresini denetleme ekran görüntüsü](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> Şirket içinde barındırılan tümleştirme çalışma zamanının kararlılığını sağlamak için, iki sürüm yayınlıyoruz, ancak her ay yalnızca bir kez otomatik olarak güncelleştireceğiz. Bu nedenle, bazen otomatik güncellenen sürümün gerçek en son sürümünün önceki sürümü olduğunu fark edersiniz. En son sürümü almak istiyorsanız, [indirme merkezi](https://www.microsoft.com/download/details.aspx?id=39717)' ne gidebilirsiniz.

## <a name="self-hosted-integration-runtime-expire-notification"></a>Şirket içinde barındırılan Integration Runtime süre sonu bildirimi
Şirket içinde barındırılan tümleştirme çalışma zamanının hangi sürümünü el ile denetlemek istiyorsanız otomatik güncelleştirme ayarını devre dışı bırakabilir ve el ile yükleyebilirsiniz. Şirket içinde barındırılan tümleştirme çalışma zamanının her sürümü bir yılda süresi dolacak. Süresi dolan ileti, ADF portalında ve şirket içinde barındırılan tümleştirme çalışma zamanı istemci **90 gün** öncesine göre gösterilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory tümleştirme çalışma zamanı kavramlarını](./concepts-integration-runtime.md)gözden geçirin.

- [Azure Portal şirket içinde barındırılan tümleştirme çalışma zamanı oluşturmayı](./create-self-hosted-integration-runtime.md)öğrenin.
