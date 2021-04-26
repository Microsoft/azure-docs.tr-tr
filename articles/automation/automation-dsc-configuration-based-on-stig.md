---
title: Azure Otomasyonu durum yapılandırması için STIG tabanlı verileri yapılandırma
description: Bu makalede, Azure Otomasyonu durum yapılandırması için DoD STIG temelinde verilerin nasıl yapılandırılacağı açıklanır.
keywords: DSC, PowerShell, yapılandırma, kurulum
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dab0cd7f7d660808b4ed7a91318baad55f80928c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87015145"
---
# <a name="configure-data-based-on-security-technical-information-guide-stig"></a>Güvenlik teknik bilgileri Kılavuzu 'Na (STIG) göre verileri yapılandırma

> Uygulama hedefi: Windows PowerShell 5,1

İlk kez yapılandırma içeriği oluşturma zor olabilir.
Çoğu durumda, amaç bir sektör önerisini tamamen hizalayan bir "taban çizgisi" takip eden sunucuların yapılandırılmasını otomatik hale getirmektir.

> [!NOTE]
> Bu makale, açık kaynak topluluğu tarafından tutulan bir çözüme başvurur.
> Destek, Microsoft 'tan değil yalnızca GitHub işbirliği biçiminde kullanılabilir.

## <a name="community-project-powerstig"></a>Topluluk projesi: PowerSTIG

Bu sorunu çözmek için [powerstig](https://github.com/microsoft/powerstig) amaçlar adlı bir topluluk projesi, Stig (güvenlik teknik uygulama kılavuzu) hakkında sunulan [genel bilgilere](https://public.cyber.mil/stigs/) dayanarak DSC içeriği oluşturarak bu sorunu çözer.

Taban çizgileri ile ilgilenirken bu seslerden daha karmaşıktır.
Birçok kuruluşun [özel durumları kurallara göre belgelenmesi](https://github.com/microsoft/powerstig#powerstigdata) ve bu verileri ölçeklendirerek yönetmesi gerekir.
PowerSTIG, tek bir büyük dosyadaki tüm ayar aralığını ele almak yerine, yapılandırmanın her bir alanını ele almak için [bileşik kaynaklar](https://github.com/microsoft/powerstig#powerstigdsc) sağlayarak sorunu giderir.

Yapılandırmalar oluşturulduktan sonra, MOF dosyaları oluşturmak ve [MOF dosyalarını Azure Otomasyonu 'na yüklemek](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation)Için [DSC yapılandırma betikleri](/powershell/scripting/dsc/configurations/configurations) ' ni kullanabilirsiniz.
Daha sonra, yapılandırma çekmek için [Şirket](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) Içinden veya [Azure 'da](./automation-dsc-onboarding.md#enable-azure-vms) sunucularınızı kaydettirin.

PowerSTIG 'yi denemek için [PowerShell Galerisi](https://www.powershellgallery.com) ziyaret edin ve çözümü indirin veya "proje sitesi" ne tıklayarak [belgeleri](https://github.com/microsoft/powerstig)görüntüleyin.

## <a name="next-steps"></a>Sonraki adımlar

- PowerShell DSC 'yi anlamak için bkz. [Windows PowerShell Istenen durum yapılandırmasına genel bakış](/powershell/scripting/dsc/overview/overview).
- [DSC kaynaklarında](/powershell/scripting/dsc/resources/resources)PowerShell DSC kaynakları hakkında bilgi edinin.
- Yerel Configuration Manager yapılandırması hakkında daha fazla bilgi için bkz. [yerel Configuration Manager yapılandırma](/powershell/scripting/dsc/managing-nodes/metaconfig).
