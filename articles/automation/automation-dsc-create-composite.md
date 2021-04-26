---
title: Azure Otomasyonu durum yapılandırması için yapılandırmaları bileşik kaynaklara dönüştürme
description: Bu makalede, Azure Otomasyonu durum yapılandırması için yapılandırmaların bileşik kaynaklara nasıl dönüştürüleceği açıklanır.
keywords: DSC, PowerShell, yapılandırma, kurulum
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5833308f03d328d988ac818d83ff5ee618a4c79c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563581"
---
# <a name="convert-configurations-to-composite-resources"></a>Yapılandırmaları bileşik kaynaklara dönüştürme

> Uygulama hedefi: Windows PowerShell 5,1

Yazma yapılandırmasına başladıktan sonra, ayar gruplarını yönetmek için hızlı bir şekilde "senaryolar" oluşturabilirsiniz.
Örnekler şöyle olabilir:

- Web sunucusu oluşturma
- DNS sunucusu oluşturma
- SharePoint sunucusu oluşturma
- SQL kümesi yapılandırma
- Güvenlik Duvarı ayarlarını yönetme
- parola ayarlarını yönetme

Bu çalışmayı başkalarıyla paylaşmak istiyorsanız en iyi seçenek, yapılandırmayı bir [bileşik kaynak](/powershell/scripting/dsc/resources/authoringresourcecomposite)olarak paketlemenize olanak sağlar.
İlk kez bileşik kaynakların oluşturulması çok zor olabilir.

> [!NOTE]
> Bu makale, açık kaynak topluluğu tarafından tutulan bir çözüme başvurur.
> Destek, Microsoft 'tan değil yalnızca GitHub işbirliği biçiminde kullanılabilir.

## <a name="community-project-compositeresource"></a>Topluluk projesi: Kompozisyonteresource

Bu sorunu çözmek için, [Oluşturucu](https://github.com/microsoft/compositeresource) tarafından tutulan bir çözüm oluşturulmuştur.

Kompozisyonun, yapılandırmadan yeni bir modül oluşturma sürecini otomatikleştirir.
İş istasyonunuzda (veya yapı sunucusunda) yapılandırma betiğini kaynak olarak yüklendiği için [noktayla](https://devblogs.microsoft.com/scripting/how-to-reuse-windows-powershell-functions-in-scripts/) ayırarak başlatın.
Sonra, bir MOF dosyası oluşturmak için yapılandırmayı çalıştırmak yerine, bir dönüştürmeyi otomatik hale getirmek için, Oluşturucu tarafından sunulan işlevini kullanın.
Cmdlet 'i yapılandırmanızın içeriğini yükleyecek, parametrelerin listesini alacak ve ihtiyacınız olan her şeyi içeren yeni bir modül oluşturacak.

Bir modül oluşturduktan sonra, her değişiklik yaptığınızda sürümü arttırın ve sürüm notları ekleyebilir ve bunu kendi [PowerShellGet deponuzda](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)yayımlayabilirsiniz.

Yapılandırmanızı (veya birden çok yapılandırmayı) içeren bir bileşik kaynak modülü oluşturduktan sonra bunları Azure 'da [birleştirilebilir yazma deneyiminde](./compose-configurationwithcompositeresources.md) KULLANABILIR veya MOF dosyaları oluşturmak ve [MOF dosyalarını Azure Otomasyonu 'na yüklemek](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation)için bunları [DSC yapılandırma betiklerine](/powershell/scripting/dsc/configurations/configurations) ekleyebilirsiniz.
Daha sonra, yapılandırma çekmek için [Şirket](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) Içinden veya [Azure 'da](./automation-dsc-onboarding.md#enable-azure-vms) sunucularınızı kaydettirin.
Projenin en son güncelleştirmesi, Azure Otomasyonu için [runbook 'ları](https://www.powershellgallery.com/packages?q=DscGallerySamples) , PowerShell Galerisi yapılandırmaların içeri aktarma işlemini otomatik hale getirmek için de yayımladı.

DSC için bileşik kaynakların oluşturulmasını otomatik hale getirmek için [PowerShell Galerisi](https://www.powershellgallery.com/packages/compositeresource/) ziyaret edin ve çözümü indirin veya "proje sitesi" ' ne tıklayarak [belgeleri](https://github.com/microsoft/compositeresource)görüntüleyin.

## <a name="next-steps"></a>Sonraki adımlar

- PowerShell DSC 'yi anlamak için bkz. [Windows PowerShell Istenen durum yapılandırmasına genel bakış](/powershell/scripting/dsc/overview/overview).
- [DSC kaynaklarında](/powershell/scripting/dsc/resources/resources)PowerShell DSC kaynakları hakkında bilgi edinin.
- Yerel Configuration Manager yapılandırması hakkında daha fazla bilgi için bkz. [yerel Configuration Manager yapılandırma](/powershell/scripting/dsc/managing-nodes/metaconfig).
