---
title: Windows Sanal Masaüstü nedir? - Azure
description: Windows sanal masaüstü 'ne genel bakış.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 08/07/2019
ms.author: helohr
ms.openlocfilehash: 4db9e1e812390f173da89a508c1dbc6782f4454f
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74120006"
---
# <a name="what-is-windows-virtual-desktop"></a>Windows Sanal Masaüstü nedir? 

Windows sanal masaüstü, bulutta çalışan bir masaüstü ve uygulama sanallaştırma hizmetidir.

Azure 'da Windows sanal masaüstü 'Nü çalıştırdığınızda şunları yapabilirsiniz:

* Ölçeklenebilirlik ile tam bir Windows 10 sunan çoklu oturum Windows 10 dağıtımı ayarlama
* Office 365 ProPlus 'ı sanallaştırın ve çoklu Kullanıcı sanal senaryolarında çalıştırmak için iyileştirin
* Ücretsiz genişletilmiş güvenlik güncelleştirmeleri ile Windows 7 sanal masaüstlerini sağlama
* Mevcut Uzak Masaüstü Hizmetleri (RDS) ve Windows Server Masaüstlerinizi ve uygulamalarınızı herhangi bir bilgisayara getirme
* Masaüstlerini ve uygulamaları sanallaştırın
* Windows 10, Windows Server ve Windows 7 Masaüstü ve uygulamalarını birleştirilmiş bir yönetim deneyimiyle yönetin

## <a name="introductory-video"></a>Tanıtım Videosu

Windows sanal masaüstü hakkında, neden benzersiz olduğunu ve bu videodaki yenilikler hakkında bilgi edinin:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Windows sanal masaüstü hakkında daha fazla video için [çalma listemize](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)bakın.

## <a name="key-capabilities"></a>Temel işlevler

Windows sanal masaüstü ile ölçeklenebilir ve esnek bir ortam ayarlayabilirsiniz:

* Ek ağ geçidi sunucuları çalıştırmak zorunda kalmadan Azure aboneliğinizde tam bir masaüstü sanallaştırma ortamı oluşturun.
* Farklı iş yüklerinize uyum sağlamak için ihtiyacınız olan sayıda konak havuzu yayımlayın.
* Azure Galerisi 'nden üretim iş yükleri veya test için kendi görüntünüzü getirin.
* Havuza alınmış, çoklu oturum kaynaklarıyla maliyetleri azaltın. Windows Server 'da Windows sanal masaüstü ve Uzak Masaüstü Oturumu Ana Bilgisayarı (RDSH) rolü için özel Windows 10 Kurumsal Çoklu oturum özelliği sayesinde, hala sanal makine sayısını ve işletim sistemi (OS) ek yükünü bir arada da azaltabilirsiniz Kullanıcılarınıza aynı kaynakları sağlama.
* Kişisel (kalıcı) masaüstleri aracılığıyla tek bir sahiplik sağlayın.

Sanal masaüstlerini dağıtabilir ve yönetebilirsiniz:

* Konak havuzlarını yapılandırmak, uygulama grupları oluşturmak, kullanıcı atamak ve kaynakları yayımlamak için Windows sanal masaüstü PowerShell ve REST arabirimlerini kullanın.
* Tek bir konak havuzundan tam masaüstü veya ayrı ayrı uzak uygulamalar yayımlayın, farklı Kullanıcı kümeleri için ayrı ayrı uygulama grupları oluşturun, hatta görüntü sayısını azaltmak için kullanıcıları birden çok uygulama grubuna atayın.
* Ortamınızı yönetirken, rol atamak ve çeşitli yapılandırma veya kullanıcı hatalarını anlamak için tanılama toplamak üzere yerleşik olarak atanmış erişimi kullanın.
* Hataları gidermek için yeni tanılama hizmetini kullanın.
* Altyapıyı değil, yalnızca görüntüyü ve sanal makineleri yönetin. Yalnızca Azure aboneliğinizdeki sanal makineler Uzak Masaüstü Hizmetleri gibi uzak masaüstü rollerini kişisel olarak yönetmeniz gerekmez.

Ayrıca, sanal masaüstlerine Kullanıcı atayabilir ve bunları bağlayabilirsiniz:

* Kullanıcılar, atandıktan sonra kullanıcıları yayınlanan Windows Masaüstü ve uygulamalarına bağlamak için herhangi bir Windows sanal masaüstü istemcisini başlatabilir. Cihazınızdaki veya Windows sanal masaüstü HTML5 Web istemcisinde bulunan yerel bir uygulama aracılığıyla herhangi bir cihazdan bağlantı yapın.
* Hizmete ters bağlantılar aracılığıyla kullanıcıları güvenle oluşturun, bu nedenle herhangi bir gelen bağlantı noktasını açık bırakmanız gerekmez.

## <a name="requirements"></a>Gereksinimler

Windows sanal masaüstü 'Nü kurmak ve kullanıcılarınızı Windows Masaüstü ve uygulamalarına başarıyla bağlamak için gereken birkaç nokta vardır.

Aşağıdaki Işletim sistemleri için destek eklemeyi planlıyoruz; bu nedenle, dağıtmayı planladığınız masaüstü ve uygulamalara bağlı olarak kullanıcılarınız için [uygun lisanslara](https://azure.microsoft.com/pricing/details/virtual-desktop/) sahip olduğunuzdan emin olun:

|İşletim Sistemi|Gerekli lisans|
|---|---|
|Windows 10 Enterprise çoklu oturum veya Windows 10 Enterprise|Microsoft 365 E3, E5, a3, A5, F1, Iş<br>Windows E3, E5, a3, a5|
|Windows 7 kurumsal |Microsoft 365 E3, E5, a3, A5, F1, Iş<br>Windows E3, E5, a3, a5|
|Windows Server 2012 R2, 2016, 2019|Yazılım güvencesi içeren RDS Istemci erişim lisansı (CAL)|

Altyapınız Windows sanal masaüstünü desteklemek için aşağıdaki şeylere ihtiyaç duyuyor:

* Bir [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Bir Windows Server, Azure Active Directory eşitlenmiş Active Directory. Bunu, aşağıdakilerden biriyle yapılandırabilirsiniz:
  * Azure AD Connect (Hibrit kuruluşlar için)
  * Azure AD Domain Services (karma veya bulut kurumları için)
* Windows Server 'a bağlı olan veya içeren bir sanal ağ içeren bir Azure aboneliği Active Directory
  
Windows sanal masaüstü için oluşturduğunuz Azure sanal makineleri şu şekilde olmalıdır:

* [Standart etki alanına katılmış](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) veya [karma ad 'ye katılmış](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Sanal makineler Azure AD 'ye katılmış olamaz.
* Aşağıdaki [desteklenen işletim sistemi görüntülerinden](#supported-virtual-machine-os-images)birini çalıştırın.

>[!NOTE]
>Bir Azure aboneliğine ihtiyacınız varsa, bir [aylık ücretsiz deneme için kaydolabilirsiniz](https://azure.microsoft.com/free/). Azure 'un ücretsiz deneme sürümünü kullanıyorsanız, Windows Server Active Directory Azure Active Directory ile eşitlenmiş halde tutmak için Azure AD Domain Services kullanmanız gerekir.

Windows sanal masaüstü için oluşturduğunuz Azure sanal makineleri aşağıdaki URL 'Lere giden TCP 443 erişimine sahip olmalıdır:

* *. wvd.microsoft.com
* *.blob.core.windows.net
* *. core.windows.net
* *.servicebus.windows.net
* prod.warmpath.msftcloudes.com
* catalogartifact.azureedge.net

>[!NOTE]
>Bu URL 'Leri açmak, güvenilir bir Windows sanal masaüstü dağıtımı için gereklidir. Bu URL 'lere erişimin engellenmesi desteklenmez ve hizmet işlevlerini etkiler. Bu URL 'Ler yalnızca Windows sanal masaüstü siteleri ve kaynaklarına karşılık gelir ve Azure AD gibi diğer hizmetlere yönelik URL 'LERI içermez.

Windows sanal masaüstü, kullanıcılara ve Microsoft tarafından Azure 'da bir hizmet olarak barındırılan yönetim çözümüne ait olan Windows Masaüstü ve uygulamalarını içerir. Masaüstleri ve uygulamalar, herhangi bir Azure bölgesindeki sanal makinelerde (VM) dağıtılabilir ve bu VM 'Lerin yönetim çözümü ve verileri Birleşik Devletler yer alır. Bu, Birleşik Devletler veri aktarımına neden olabilir.

En iyi performans için, ağınızın aşağıdaki gereksinimleri karşıladığından emin olun:

* İstemci ağından, ana bilgisayar havuzlarının dağıtıldığı Azure bölgesine gidiş dönüş (RTT) gecikmesi 150 MS 'den az olmalıdır.
* Masaüstleri ve uygulamalar barındıran VM 'Ler Yönetim hizmetine bağlandığında ağ trafiği ülke/bölge kenarlıklarının dışına akabilir.
* Ağ performansını iyileştirmek için, oturum ana bilgisayarının VM 'lerinin yönetim hizmeti ile aynı Azure bölgesinde birlikte bulunmasını öneririz.

## <a name="supported-remote-desktop-clients"></a>Desteklenen uzak masaüstü istemcileri

Aşağıdaki uzak masaüstü istemcileri Windows sanal masaüstünü destekler:

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)

## <a name="supported-virtual-machine-os-images"></a>Desteklenen sanal makine işletim sistemi görüntüleri

Windows sanal masaüstü aşağıdaki x64 işletim sistemi görüntülerini destekler:

* Windows 10 Enterprise multi-session, sürüm 1809 veya üzeri
* Windows 10 Enterprise, sürüm 1809 veya üzeri
* Windows 7 kurumsal
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows sanal masaüstü, x86 (32 bit) işletim sistemi görüntülerini desteklemez.

Kullanılabilir Otomasyon ve dağıtım seçenekleri, aşağıdaki tabloda gösterildiği gibi, hangi işletim sistemi ve sürümü seçdiğinize bağlıdır: 

|İşletim sistemi|Azure görüntü Galerisi|El ile VM dağıtımı|Azure Resource Manager şablonu tümleştirmesi|Azure Market 'te konak havuzları sağlama|Windows sanal masaüstü Aracısı güncelleştirmeleri|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 çoklu oturum, sürüm 1903|Yes|Yes|Yes|Yes|Otomatik|
|Windows 10 çoklu oturum, sürüm 1809|Yes|Yes|Hayır|Hayır|Otomatik|
|Windows 10 Enterprise, sürüm 1903|Yes|Yes|Yes|Yes|Otomatik|
|Windows 10 Enterprise, sürüm 1809|Yes|Yes|Hayır|Hayır|Otomatik|
|Windows 7 kurumsal|Yes|Yes|Hayır|Hayır|El ile|
|Windows Server 2019|Yes|Yes|Hayır|Hayır|Otomatik|
|Windows Server 2016|Yes|Yes|Yes|Yes|Otomatik|
|Windows Server 2012 R2|Yes|Yes|Hayır|Hayır|Otomatik|

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için bir kiracı oluşturmanız gerekir. Kiracı oluşturma hakkında daha fazla bilgi edinmek için kiracı oluşturma öğreticisine devam edin.

> [!div class="nextstepaction"]
> [Windows Sanal Masaüstü'nde kiracı oluşturma](tenant-setup-azure-active-directory.md)
