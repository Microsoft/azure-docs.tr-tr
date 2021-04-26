---
title: Veri yerleşimi
description: Veri yerleşimi ve Azure Arc etkin sunucuları hakkında bilgiler.
ms.topic: reference
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: d4764772473bbf2e5aafe2607a9462c9a6a15203
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559507"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure Arc etkin sunucular: veri yerleşimi

Bu makalede, veri yerleşimi kavramı ve Azure Arc özellikli sunucular için nasıl uygulandığı açıklanmaktadır.

Azure yay özellikli sunucular **Birleşik Devletler, Avrupa, Birleşik Krallık, Avustralya ve Asya Pasifik** **[kullanılabilir](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** .

## <a name="data-residency"></a>Veri yerleşimi

Azure Arc etkin sunucular, [Azure VM uzantı](manage-vm-extensions.md) yapılandırma ayarlarını (diğer bir deyişle, özellik değerlerini), uzantının bağlı makinede etkinleştirmeyi denemeden önce belirtilmesini gerektirir. Örneğin, Log Analytics VM uzantısını etkinleştirdiğinizde, Log Analytics **çalışma alanı kimliği** ve **birincil anahtar** ister.

Bağlı makine ile ilgili meta veri bilgileri de toplanır. Özellikle:

* İşletim sistemi adı, türü ve sürümü
* Bilgisayar adı
* Bilgisayar tam etki alanı adı (FQDN)
* Bağlı makine Aracısı sürümü
* Active Directory ve DNS tam etki alanı adı (FQDN)
* UUıD (BıOS KIMLIĞI)
* Bağlı makine Aracısı sinyali
* Bağlı makine Aracısı sürümü
* Yönetilen kimlik için ortak anahtar
* İlke uyumluluk durumu ve ayrıntıları (Azure Ilke Konuk yapılandırma ilkelerini kullanıyorsanız)

Yay özellikli sunucular, verilerinizin depolanacağı bölgeyi belirtmenize olanak tanır. Microsoft, veri dayanıklılığı için diğer bölgelere çoğaltılabilir, ancak Microsoft, Coğrafya dışında verileri çoğaltmaz veya taşımaz. Bu veriler, Azure Arc makinesi kaynağının yapılandırıldığı bölgede depolanır. Örneğin, makine Doğu ABD bölgesinde yaya kayıtlıysa, bu veriler ABD bölgesinde saklanır.

Bölgesel dayanıklılık ve uyumluluk desteğiniz hakkında daha fazla bilgi için bkz. [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Sonraki adımlar

[Azure dayanıklılığı](/azure/architecture/reliability/architect)için tasarlama hakkında daha fazla bilgi edinin.
