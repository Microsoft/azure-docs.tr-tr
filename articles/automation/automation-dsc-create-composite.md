---
title: Durum yapılandırması için yapılandırmaları bileşik kaynaklara dönüştürme-Azure Otomasyonu
description: Azure Automation 'da durum yapılandırması için yapılandırmaları bileşik kaynaklara dönüştürmeyi öğrenin.
keywords: DSC, PowerShell, yapılandırma, kurulum
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d830b8e27bb6f66a533b8106cbec53eeca4ca139
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970710"
---
# <a name="convert-configurations-to-composite-resources"></a>Yapılandırmaları bileşik kaynaklara dönüştürme

> Uygulama hedefi: Windows PowerShell 5.1

Yazma yapılandırmasına başladıktan sonra, ayar gruplarını yönetmek için hızlı bir şekilde "senaryolar" oluşturabilirsiniz.
Örnekler şöyle olabilir:

- Web sunucusu oluşturma
- DNS sunucusu oluşturma
- SharePoint sunucusu oluşturma
- SQL kümesi yapılandırma
- Güvenlik Duvarı ayarlarını yönetme
- parola ayarlarını yönetme

Bu çalışmayı başkalarıyla paylaşmak istiyorsanız en iyi seçenek, yapılandırmayı bir [bileşik kaynak](/powershell/dsc/resources/authoringresourcecomposite)olarak paketlemenize olanak sağlar.
İlk kez bileşik kaynakların oluşturulması çok zor olabilir.

> [!NOTE]
> Bu makale, açık kaynak topluluğu tarafından tutulan bir çözüme başvurur.
> Destek, Microsoft 'tan değil yalnızca GitHub işbirliği biçiminde kullanılabilir.

## <a name="community-project-compositeresource"></a>Topluluk projesi: Kompozisyon ve kaynak

Bu sorunu çözmek için, [Oluşturucu](https://github.com/microsoft/compositeresource) tarafından tutulan bir çözüm oluşturulmuştur.

Kompozisyonun, yapılandırmadan yeni bir modül oluşturma sürecini otomatikleştirir.
İş istasyonunuzda (veya yapı sunucusunda) yapılandırma betiğini kaynak olarak yüklendiği için [noktayla](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) ayırarak başlatın.
Sonra, bir MOF dosyası oluşturmak için yapılandırmayı çalıştırmak yerine, bir dönüştürmeyi otomatik hale getirmek için, Oluşturucu tarafından sunulan işlevini kullanın.
Cmdlet 'i yapılandırmanızın içeriğini yükleyecek, parametrelerin listesini alacak ve ihtiyacınız olan her şeyi içeren yeni bir modül oluşturacak.

Bir modül oluşturduktan sonra, her değişiklik yaptığınızda sürümü arttırın ve sürüm notları ekleyebilir ve bunu kendi [PowerShellGet deponuzda](https://kevinmarquette.github.io/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)yayımlayabilirsiniz.

Yapılandırmanızı (veya birden çok yapılandırmayı) içeren bir bileşik kaynak modülü oluşturduktan sonra bunları Azure 'da [birleştirilebilir yazma deneyiminde](/azure/automation/compose-configurationwithcompositeresources) KULLANABILIR veya MOF dosyaları oluşturmak Için bunları [DSC yapılandırma betiklerine](/powershell/dsc/configurations/configurations) ekleyebilirsiniz [MOF dosyalarını Azure Otomasyonu 'na yükleyin](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Daha sonra, yapılandırma çekmek için [Şirket](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) Içinden veya [Azure 'da](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) sunucularınızı kaydettirin.
Projenin en son güncelleştirmesi, Azure Otomasyonu için [runbook 'ları](https://www.powershellgallery.com/packages?q=DscGallerySamples) , PowerShell Galerisi yapılandırmaların içeri aktarma işlemini otomatik hale getirmek için de yayımladı.

DSC için bileşik kaynakların oluşturulmasını otomatik hale getirmek için [PowerShell Galerisi](https://www.powershellgallery.com/packages/compositeresource/) ziyaret edin ve çözümü indirin veya "proje sitesi" ' ne tıklayarak [belgeleri](https://github.com/microsoft/compositeresource)görüntüleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Windows PowerShell Istenen durum yapılandırmasına genel bakış](/powershell/dsc/overview/overview)
- [DSC kaynakları](/powershell/dsc/resources/resources)
- [Yerel Configuration Manager yapılandırma](/powershell/dsc/managing-nodes/metaconfig)
