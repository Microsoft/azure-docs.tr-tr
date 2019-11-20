---
title: Azure Otomasyonu durum yapılandırmasına genel bakış
description: Azure Otomasyonu durum yapılandırması (DSC), koşullarına ve bilinen sorunlara genel bakış
keywords: PowerShell DSC, istenen durum yapılandırması, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b0b5e02009ddbb72bb062d341e7d233acfb0ceb3
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429393"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Otomasyonu durum yapılandırmasına genel bakış

Azure Otomasyonu durum yapılandırması, PowerShell Istenen durum yapılandırması (DSC) [yapılandırmalarını](/powershell/scripting/dsc/configurations/configurations)yazmanızı, yönetmenizi ve derlemenize, [DSC kaynaklarını](/powershell/scripting/dsc/resources/resources)içeri aktarmanıza ve tüm una.

## <a name="why-use-azure-automation-state-configuration"></a>Azure Otomasyonu durum yapılandırması neden kullanılmalıdır?

Azure Otomasyonu durum yapılandırması, Azure dışında DSC kullanmaya kıyasla çeşitli avantajlar sağlar.

### <a name="built-in-pull-server"></a>Yerleşik çekme sunucusu

Azure Otomasyonu durum yapılandırması, [Windows ÖZELLIĞI DSC hizmetine](/powershell/scripting/dsc/pull-server/pullserver) benzer bir DSC çekme sunucusu sağlar. böylece hedef düğümler yapılandırmaları otomatik olarak alır, istenen duruma uymalıdır ve uyumluluğuna geri rapor verir. Azure Otomasyonu 'ndaki yerleşik çekme sunucusu, kendi çekme sunucunuzu ayarlama ve sürdürme gereksinimini ortadan kaldırır. Azure Otomasyonu, sanal veya fiziksel Windows veya Linux makinelerini bulutta veya şirket içinde hedefleyebilir.

### <a name="management-of-all-your-dsc-artifacts"></a>Tüm DSC yapıtlarınızın yönetimi

Azure Otomasyonu durum yapılandırması, PowerShell komut dosyası oluşturma için Azure Otomasyonu teklifleri olarak [PowerShell Istenen durum yapılandırması](/powershell/scripting/dsc/overview/overview) için aynı yönetim katmanını getirir.

Azure portal veya PowerShell 'den, tüm DSC yapılandırmalarınızı, kaynaklarınızı ve hedef düğümlerinizi yönetebilirsiniz.

![Azure Otomasyonu sayfasının ekran görüntüsü](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Raporlama verilerini Azure Izleyici günlüklerine aktarma

Azure Otomasyonu durum yapılandırması ile yönetilen düğümler, yerleşik çekme sunucusuna ayrıntılı raporlama durum verileri gönderir. Azure Otomasyonu durum yapılandırması 'nı, bu verileri Log Analytics çalışma alanınıza gönderecek şekilde yapılandırabilirsiniz. Log Analytics çalışma alanınıza durum yapılandırma durumu verileri gönderme hakkında bilgi edinmek için bkz. Azure [Otomasyonu durum yapılandırması raporlama verilerini Azure izleyici günlüklerine iletme](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Önkoşullar

Lütfen Azure Otomasyonu durum yapılandırması 'nı (DSC) kullanırken aşağıdaki gereksinimleri göz önünde bulundurun.

### <a name="operating-system-requirements"></a>İşletim sistemi gereksinimleri

Windows çalıştıran düğümler için aşağıdaki sürümler desteklenir:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

[Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) tek başına Ürün SKU 'Su Istenen durum yapılandırması, PowerShell DSC veya Azure Otomasyonu durum yapılandırması tarafından yönetilebilmesi için bu uygulamayı içermiyor.

Linux çalıştıran düğümler için aşağıdaki distrolar/sürümler desteklenir:

DSC Linux uzantısı, [desteklenen Linux dağıtımları](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)altında listelenen tüm Linux dağıtımlarını destekler.

### <a name="dsc-requirements"></a>DSC gereksinimleri

Azure 'da çalışan tüm Windows düğümleri için, [WMF 5,1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) ekleme sırasında yüklenecektir.  Windows Server 2012 ve Windows 7 çalıştıran düğümler için [WinRM etkinleştirilecek](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Azure 'da çalışan tüm Linux düğümleri için ekleme sırasında [Linux Için POWERSHELL DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux) yüklenir.

### <a name="network-planning"></a>Özel ağları yapılandırma

Düğümleriniz özel bir ağda bulunuyorsa, otomasyon ile iletişim kurmak için durum yapılandırması (DSC) için aşağıdaki bağlantı noktası ve URL 'Ler gereklidir:

* Bağlantı noktası: giden internet erişimi için yalnızca TCP 443 gereklidir.
* Genel URL: *. azure-automation.net
* US Gov Virginia genel URL 'SI: *. azure-automation.us
* Aracı hizmeti: https://@no__t -0workspaceId\>.agentsvc.azure-automation.net

Bu, yönetilen düğüm için Azure Otomasyonu ile iletişim kurmak üzere ağ bağlantısı sağlar.
[Waitfor * kaynakları](https://docs.microsoft.com/powershell/dsc/reference/resources/windows/waitForAllResource)gibi düğümler arasında ILETIŞIM kuran DSC kaynakları kullanıyorsanız, düğümler arasında trafiğe de izin vermeniz gerekir.
Bu ağ gereksinimlerini anlamak için her DSC kaynağına yönelik belgelere bakın.

#### <a name="proxy-support"></a>Proxy desteği

DSC aracısına yönelik ara sunucu desteği Windows sürüm 1809 ve sonrasında kullanılabilir.
Bu seçeneği yapılandırmak için, **ProxyURL** ve **proxycredential** değerlerini, düğümleri kaydetmek için kullanılan [metaconfiguration betiğine](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) ayarlayın.
Windows 'un önceki sürümleri için DSC 'de ara sunucu kullanılamaz.

Linux düğümleri için DSC Aracısı proxy 'yi destekler ve URL 'yi öğrenmek için http_proxy değişkenini kullanır.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Azure Durum Yapılandırması ağ aralıkları ve ad alanı

Özel durumları tanımlarken listelenen adreslerin kullanılması önerilir. IP adresleri için [Microsoft Azure veri MERKEZI IP aralıklarını](https://www.microsoft.com/download/details.aspx?id=41653)indirebilirsiniz. Bu dosya haftalık olarak güncelleştirilir ve şu anda dağıtılmış aralıklar ve IP aralıklarında yaklaşan değişiklikler vardır.

Belirli bir bölge için tanımlanan bir Otomasyon hesabınız varsa, bu bölgesel veri merkezi ile iletişimi kısıtlayabilirsiniz. Aşağıdaki tabloda her bölge için DNS kaydı verilmiştir:

| **Bölge** | **DNS kaydı** |
| --- | --- |
| Orta Batı ABD | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Güney Orta ABD |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Doğu ABD   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Doğu ABD 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kanada Orta |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Batı Avrupa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Kuzey Avrupa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Güneydoğu Asya |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Orta Hindistan |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Doğu Japonya |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Avustralya Güneydoğu |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Birleşik Krallık, Güney | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| ABD Hükümeti Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Bölge adları yerine bölge IP adresleri listesi için, Microsoft Indirme Merkezi ' nden [Azure veri MERKEZI IP adresi](https://www.microsoft.com/download/details.aspx?id=41653) XML dosyasını indirin.

> [!NOTE]
> Azure veri merkezi IP adresi XML dosyası Microsoft Azure veri merkezlerinde kullanılan IP adresi aralıklarını listeler. Dosya işlem, SQL ve depolama aralıklarını içerir.
>
>Güncelleştirilmiş bir dosya haftalık olarak gönderilir. Dosya, şu anda dağıtılmış aralıkları ve IP aralıklarında yaklaşan değişiklikleri yansıtır. Dosyada görüntülenen yeni aralıklar, en az bir hafta için veri merkezlerinde kullanılmıyor.
>
> Her hafta yeni XML dosyasını indirmek iyi bir fikirdir. Daha sonra, sitenizi Azure 'da çalışan hizmetleri doğru şekilde belirlemek için güncelleştirin. Azure ExpressRoute kullanıcıları bu dosyanın, her ayın ilk haftasında Azure Space Sınır Ağ Geçidi Protokolü (BGP) tanıtımını güncelleştirmek için kullanıldığını unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [Azure Otomasyonu durum yapılandırması ile çalışmaya](automation-dsc-getting-started.md) başlama
- Düğümlerin nasıl ekleneceğini öğrenmek için bkz. [Azure Otomasyonu durum yapılandırmasına göre yönetim için makineleri ekleme](automation-dsc-onboarding.md)
- Hedef düğümlere atayabilmeniz için DSC yapılandırmalarını derleme hakkında bilgi edinmek için bkz. [Azure Otomasyonu durum yapılandırmasında yapılandırmaları derleme](automation-dsc-compile.md)
- PowerShell cmdlet başvurusu için bkz. [Azure Otomasyonu durum yapılandırması cmdlet 'leri](/powershell/module/azurerm.automation/#automation)
- Fiyatlandırma bilgileri için bkz. [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/)
- Azure Otomasyonu durum yapılandırması 'nı sürekli bir dağıtım ardışık düzeninde kullanmaya ilişkin bir örnek görmek için bkz. [Azure Otomasyonu durum yapılandırması ve Chocolatey kullanarak sürekli dağıtım](automation-dsc-cd-chocolatey.md)
