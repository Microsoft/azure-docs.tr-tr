---
title: Windows Sanal Masaüstü nedir? - Azure
description: Windows sanal masaüstü 'ne genel bakış.
author: Heidilohr
ms.topic: overview
ms.date: 09/14/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: a6d98f827c39f973714441e308dcc4f2bd061c9b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835587"
---
# <a name="what-is-windows-virtual-desktop"></a>Windows Sanal Masaüstü nedir?

Windows sanal masaüstü, bulutta çalışan bir masaüstü ve uygulama sanallaştırma hizmetidir.

Azure 'da Windows sanal masaüstü 'Nü çalıştırdığınızda şunları yapabilirsiniz:

* Ölçeklenebilirlik ile tam bir Windows 10 sunan çoklu oturum Windows 10 dağıtımı ayarlama
* Microsoft 365 uygulamalarını kuruluşa sanallaştırın ve bunu çok kullanıcılı sanal senaryolarda çalışacak şekilde iyileştirin
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
* Havuza alınmış, çoklu oturum kaynaklarıyla maliyetleri azaltın. Windows Server 'da Windows sanal masaüstü ve Uzak Masaüstü Oturumu Ana Bilgisayarı (RDSH) rolü için özel Windows 10 Enterprise çoklu oturum özelliği sayesinde, kullanıcılarınıza aynı kaynakları sağlarken sanal makine sayısını ve işletim sistemi (OS) ek yükünü büyük ölçüde azaltabilirsiniz.
* Kişisel (kalıcı) masaüstleri aracılığıyla tek bir sahiplik sağlayın.

Sanal masaüstlerini dağıtabilir ve yönetebilirsiniz:

* Konak havuzlarını yapılandırmak, uygulama grupları oluşturmak, kullanıcı atamak ve kaynakları yayımlamak için Azure portal, Windows sanal masaüstü PowerShell ve REST arabirimlerini kullanın.
* Tek bir konak havuzundan tam masaüstü veya ayrı ayrı uzak uygulamalar yayımlayın, farklı Kullanıcı kümeleri için ayrı ayrı uygulama grupları oluşturun, hatta görüntü sayısını azaltmak için kullanıcıları birden çok uygulama grubuna atayın.
* Ortamınızı yönetirken, rol atamak ve çeşitli yapılandırma veya kullanıcı hatalarını anlamak için tanılama toplamak üzere yerleşik olarak atanmış erişimi kullanın.
* Hataları gidermek için yeni tanılama hizmetini kullanın.
* Altyapıyı değil, yalnızca görüntüyü ve sanal makineleri yönetin. Yalnızca Azure aboneliğinizdeki sanal makineler Uzak Masaüstü Hizmetleri gibi uzak masaüstü rollerini kişisel olarak yönetmeniz gerekmez.

Ayrıca, sanal masaüstlerine Kullanıcı atayabilir ve bunları bağlayabilirsiniz:

* Kullanıcılar, atandıktan sonra kullanıcıları yayınlanan Windows Masaüstü ve uygulamalarına bağlamak için herhangi bir Windows sanal masaüstü istemcisini başlatabilir. Cihazınızdaki veya Windows sanal masaüstü HTML5 Web istemcisinde bulunan yerel bir uygulama aracılığıyla herhangi bir cihazdan bağlantı yapın.
* Hizmete ters bağlantılar aracılığıyla kullanıcıları güvenle oluşturun, bu nedenle herhangi bir gelen bağlantı noktasını açık bırakmanız gerekmez.

## <a name="requirements"></a>Gereksinimler

Windows sanal masaüstü 'Nü kurmak ve kullanıcılarınızı Windows Masaüstü ve uygulamalarına başarıyla bağlamak için gereken birkaç nokta vardır.

Aşağıdaki işletim sistemlerini destekliyoruz, bu nedenle dağıtmayı planladığınız masaüstü ve uygulamalara bağlı olarak kullanıcılarınız için [uygun lisanslara](https://azure.microsoft.com/pricing/details/virtual-desktop/) sahip olduğunuzdan emin olun:

|İşletim Sistemi|Gerekli lisans|
|---|---|
|Windows 10 Enterprise çoklu oturum veya Windows 10 Enterprise|Microsoft 365 E3, E5, a3, A5, F3, Iş Premium<br>Windows E3, E5, a3, a5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, a3, A5, F3, Iş Premium<br>Windows E3, E5, a3, a5|
|Windows Server 2012 R2, 2016, 2019|Yazılım güvencesi içeren RDS Istemci erişim lisansı (CAL)|

Altyapınız Windows sanal masaüstünü desteklemek için aşağıdaki şeylere ihtiyaç duyuyor:

* Bir [Azure Active Directory](../active-directory/index.yml).
* Bir Windows Server, Azure Active Directory eşitlenmiş Active Directory. Bunu, Azure AD Connect (Hibrit kuruluşlar için) veya Azure AD Domain Services (karma veya bulut kurumları için) kullanarak yapılandırabilirsiniz.
  * Azure Active Directory ile eşitlenmiş bir Windows Server AD. Kullanıcı Windows Server AD 'den kaynaklıdır ve Windows sanal masaüstü VM 'si Windows Server AD etki alanına katılır.
  * Azure Active Directory ile eşitlenmiş bir Windows Server AD. Kullanıcı Windows Server AD 'den kaynaklıdır ve Windows sanal masaüstü VM 'si Azure AD Domain Services etki alanına katılır.
  * Azure AD Domain Services etki alanı. Kullanıcının kaynağı Azure Active Directory ve Windows sanal masaüstü VM 'si Azure AD Domain Services etki alanına katılır.
* Windows Server Active Directory veya Azure AD DS örneğine bağlı olan veya içeren bir sanal ağ içeren aynı Azure AD kiracısının üst öğesi olan bir Azure aboneliği.

Windows sanal masaüstüne bağlanmak için Kullanıcı gereksinimleri:

* Kullanıcının kaynağı Azure AD 'ye bağlı Active Directory aynı olmalıdır. Windows sanal masaüstü B2B veya MSA hesaplarını desteklemez.
* Windows sanal masaüstüne abone olmak için kullandığınız UPN, sanal makinenin katıldığı Active Directory etki alanında bulunmalıdır.

Windows sanal masaüstü için oluşturduğunuz Azure sanal makineleri şu şekilde olmalıdır:

* [Standart etki alanına katılmış](../active-directory-domain-services/compare-identity-solutions.md) veya [karma ad 'ye katılmış](../active-directory/devices/hybrid-azuread-join-plan.md). Sanal makineler Azure AD 'ye katılmış olamaz.
* Aşağıdaki [desteklenen işletim sistemi görüntülerinden](#supported-virtual-machine-os-images)birini çalıştırın.

>[!NOTE]
>Bir Azure aboneliğine ihtiyacınız varsa, bir [aylık ücretsiz deneme için kaydolabilirsiniz](https://azure.microsoft.com/free/). Azure 'un ücretsiz deneme sürümünü kullanıyorsanız, Windows Server Active Directory Azure Active Directory ile eşitlenmiş halde tutmak için Azure AD Domain Services kullanmanız gerekir.

Windows sanal masaüstü dağıtımınızın beklendiği gibi çalışması için engellemesini kaldırmanız gereken URL 'lerin bir listesi için, [gereklı URL listemize](safe-url-list.md)bakın.

Windows sanal masaüstü, kullanıcılara ve Microsoft tarafından Azure 'da bir hizmet olarak barındırılan yönetim çözümüne ait olan Windows Masaüstü ve uygulamalarını içerir. Masaüstleri ve uygulamalar, herhangi bir Azure bölgesindeki sanal makinelerde (VM) dağıtılabilir ve bu VM 'Lerin yönetim çözümü ve verileri Birleşik Devletler yer alır. Bu, Birleşik Devletler veri aktarımına neden olabilir.

En iyi performans için, ağınızın aşağıdaki gereksinimleri karşıladığından emin olun:

* İstemci ağından, ana bilgisayar havuzlarının dağıtıldığı Azure bölgesine gidiş dönüş (RTT) gecikmesi 150 MS 'den az olmalıdır. Bağlantı durumunu ve önerilen Azure bölgenizi görüntülemek için [deneyim Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment) ' nı kullanın.
* Masaüstleri ve uygulamalar barındıran VM 'Ler Yönetim hizmetine bağlandığında ağ trafiği ülke/bölge kenarlıklarının dışına akabilir.
* Ağ performansını iyileştirmek için, oturum ana bilgisayarının VM 'lerinin yönetim hizmeti ile aynı Azure bölgesinde birlikte bulunmasını öneririz.

[Mimari](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)belgelerimizin kurumsal Için Windows sanal masaüstü 'nün tipik bir mimari kurulumunu görebilirsiniz.

## <a name="supported-remote-desktop-clients"></a>Desteklenen uzak masaüstü istemcileri

Aşağıdaki uzak masaüstü istemcileri Windows sanal masaüstünü destekler:

* [Windows Masaüstü](connect-windows-7-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android](connect-android.md)
* Microsoft Store Istemcisi

> [!IMPORTANT]
> Windows sanal masaüstü, RemoteApp ve Masaüstü bağlantıları (ÇDC) istemcisini veya Uzak Masaüstü Bağlantısı (MSTSC) istemcisini desteklemez.

İstemcileri kullanmak için engellemesini kaldırmanız gereken URL 'Ler hakkında daha fazla bilgi edinmek için bkz. [GÜVENLI URL listesi](safe-url-list.md).

## <a name="supported-virtual-machine-os-images"></a>Desteklenen sanal makine işletim sistemi görüntüleri

Windows sanal masaüstü aşağıdaki x64 işletim sistemi görüntülerini destekler:

* Windows 10 Enterprise multi-session, sürüm 1809 veya üzeri
* Windows 10 Enterprise, sürüm 1809 veya üzeri (yalnızca yarı yıllık kanal)
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows sanal masaüstü, x86 (32-bit), Windows 10 Enterprise N, Windows 10 Pro veya Windows 10 Enterprise KN işletim sistemi görüntülerini desteklemez. Windows 7, bir sektör boyutu sınırlaması nedeniyle yönetilen Azure depolama üzerinde barındırılan VHD veya VHDX tabanlı profil çözümlerini de desteklemez.

Kullanılabilir Otomasyon ve dağıtım seçenekleri, aşağıdaki tabloda gösterildiği gibi, hangi işletim sistemi ve sürümü seçdiğinize bağlıdır:

|İşletim sistemi|Azure görüntü Galerisi|El ile VM dağıtımı|Azure Resource Manager şablonu tümleştirmesi|Azure Market 'te konak havuzları sağlama|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 Enterprise (çoklu oturum), sürüm 2004|Yes|Yes|Yes|Yes|
|Windows 10 Enterprise (çoklu oturum), sürüm 1909|Yes|Yes|Yes|Yes|
|Windows 10 Enterprise (çoklu oturum), sürüm 1903|Yes|Yes|Hayır|Hayır|
|Windows 10 Enterprise (çoklu oturum), sürüm 1809|Yes|Yes|Hayır|Hayır|
|Windows 7 Enterprise|Yes|Yes|Hayır|Hayır|
|Windows Server 2019|Yes|Yes|Hayır|Hayır|
|Windows Server 2016|Evet|Yes|Yes|Yes|
|Windows Server 2012 R2|Evet|Yes|Hayır|Hayır|

## <a name="next-steps"></a>Sonraki adımlar

Windows sanal masaüstü 'Nü (klasik) kullanıyorsanız, [Windows sanal masaüstü 'nde kiracı oluşturma bölümünde](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)öğreticimize başlamanızı sağlayabilirsiniz.

Windows sanal masaüstünü Azure Resource Manager tümleştirmede kullanıyorsanız, bunun yerine bir konak havuzu oluşturmanız gerekir. Kullanmaya başlamak için aşağıdaki Öğreticiye gidin.

> [!div class="nextstepaction"]
> [Azure portalıyla yeni bir konak havuzu oluşturma](create-host-pools-azure-marketplace.md)
