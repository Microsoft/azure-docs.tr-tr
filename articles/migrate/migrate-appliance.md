---
title: Azure Geçişi gereci
description: Azure geçişi gereci desteğinin bir özetini sağlar.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: d7fc04e65e2b79d43c48acd5a8c621f28d5c0403
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714677"
---
# <a name="azure-migrate-appliance"></a>Azure Geçişi gereci

Bu makale, Azure geçişi gereci için önkoşulları ve destek gereksinimlerini özetler.

## <a name="deployment-scenarios"></a>Dağıtım senaryoları

Azure geçişi gereci aşağıdaki senaryolarda kullanılır.

**Senaryo** | **Araç** | **Kullanıldığı yer**
--- | --- | ---
**VMware ortamında çalışan sunucuların keşfi ve değerlendirmesi** | Azure geçişi: bulma ve değerlendirme | VMware ortamınızda çalışan sunucuları bulma<br/><br/> Yüklü yazılım envanteri, aracısız bağımlılık Analizi ve SQL Server örnekleri ve veritabanlarını bulma işlemini gerçekleştirin.<br/><br/> Değerlendirmeler için sunucu yapılandırma ve performans meta verilerini toplayın.
**VMware ortamında çalışan sunucuların aracısız geçişi** | Azure geçişi: sunucu geçişi | VMware ortamınızda çalışan sunucuları bulun. <br/><br/> Herhangi bir aracı yüklemeden sunucuları çoğaltın.
**Hyper-V ortamında çalışan sunucuların keşfi ve değerlendirmesi** | Azure geçişi: bulma ve değerlendirme | Hyper-V ortamınızda çalışan sunucuları bulun.<br/><br/> Değerlendirmeler için sunucu yapılandırma ve performans meta verilerini toplayın.
**Şirket içi fiziksel veya sanallaştırılmış sunucuların bulunması ve değerlendirmesi** |  Azure geçişi: bulma ve değerlendirme |  Şirket içi fiziksel veya sanallaştırılmış sunucuları bulun.<br/><br/> Değerlendirmeler için sunucu yapılandırma ve performans meta verilerini toplayın.

## <a name="deployment-methods"></a>Dağıtım yöntemleri

Gereç birkaç yöntem kullanılarak dağıtılabilir:

- Gereç, VMware veya Hyper-V ortamında ([VMware Için ova şablonu](how-to-set-up-appliance-vmware.md) veya [Hyper-v için VHD](how-to-set-up-appliance-hyper-v.md)) çalışan sunucular için bir şablon kullanılarak dağıtılabilir.
- Bir şablon kullanmak istemiyorsanız, bir [PowerShell yükleyici betiği](deploy-appliance-script.md)kullanarak VMware veya Hyper-V ortamı için gereci dağıtabilirsiniz.
- Azure Kamu 'da, bir PowerShell yükleyici betiği kullanarak gereci dağıtmanız gerekir. [Burada](deploy-appliance-script-government.md)Dağıtım adımlarına bakın.
- Şirket içinde veya başka bir bulutta bulunan fiziksel veya sanallaştırılmış sunucular için, her zaman gereci bir PowerShell yükleyici betiği kullanarak dağıtırsınız. [Burada](how-to-set-up-appliance-physical.md)Dağıtım adımlarına bakın.
- İndirme bağlantıları aşağıdaki tablolarda bulunur.

## <a name="appliance---vmware"></a>Gereç-VMware

Aşağıdaki tabloda, VMware için Azure geçişi gereç gereksinimleri özetlenmektedir.

> [!Note]
> VMware ortamınızda çalışan SQL Server örnekleri ve veritabanlarının keşfi ve değerlendirmesi artık önizlemededir. Bu özelliği denemek için [**bu bağlantıyı**](https://aka.ms/AzureMigrate/SQL) kullanarak **Doğu Avustralya** bölgesinde bir proje oluşturun. Zaten Doğu Avustralya bölgesinde bir projeniz varsa ve bu özelliği denemek istiyorsanız, lütfen portalda bu [**önkoşulları**](how-to-discover-sql-existing-project.md) tamamladığınızdan emin olun.

**Gereksinim** | **VMware**
--- | ---
**İzinler** | Gereç Yapılandırma yöneticisine yerel olarak veya uzaktan erişmek için, Gereç sunucusunda yönetici ayrıcalıklarına sahip bir yerel veya etki alanı kullanıcı hesabına sahip olmanız gerekir.
**Gereç Hizmetleri** | Gereç aşağıdaki hizmetlere sahiptir:<br/><br/> - **Gereç Yapılandırma Yöneticisi**: Bu, sunucuların bulunmasını ve değerlendirmesini başlatmak üzere kaynak ayrıntıları ile yapılandırılabilecek bir Web uygulamasıdır.<br/> - **VMware bulma Aracısı**: Aracı, şirket içi değerlendirmeler olarak oluşturmak için kullanılabilecek sunucu yapılandırma meta verilerini toplar.<br/>- **VMware değerlendirme Aracısı**: Aracı, performans tabanlı değerlendirmeler oluşturmak için kullanılabilecek sunucu performansı meta verilerini toplar.<br/>- **Otomatik güncelleştirme hizmeti**: hizmet, Gereç üzerinde çalışan tüm aracıların güncel kalmasını sağlar. Her 24 saatte bir otomatik olarak çalışır.<br/>- **DRA Aracısı**: sunucu çoğaltmasını düzenleyin ve çoğaltılan sunucular ile Azure arasındaki iletişimi koordine edin. Yalnızca Azure 'a aracısız geçiş kullanılarak sunucular çoğaltılırken kullanılır.<br/>- **Ağ geçidi**: çoğaltılan verileri Azure 'a gönderir. Yalnızca Azure 'a aracısız geçiş kullanılarak sunucular çoğaltılırken kullanılır.<br/>- **SQL bulma ve değerlendirme Aracısı**: SQL Server örneklerinin ve veritabanlarının yapılandırma ve performans meta verilerini Azure 'a gönderir.
**Proje limitleri** |  Bir gereç yalnızca tek bir projeyle kaydedilebilir.<br/> Tek bir projede birden fazla kayıtlı gereç olabilir.
**Bulma sınırları** | Bir gereç, vCenter Server çalıştıran en fazla 10.000 sunucuyu bulabilir.<br/> Bir gereç, tek bir vCenter Server bağlanabilir.
**Desteklenen Dağıtım** | OVA şablonunu kullanarak vCenter Server çalıştıran yeni sunucu olarak dağıtın.<br/><br/> PowerShell yükleyici betiği kullanarak Windows Server 2016 çalıştıran var olan bir sunucuda dağıtın.
**OVA şablonu** | Projeden veya [buradan](https://go.microsoft.com/fwlink/?linkid=2140333) indirin<br/><br/> İndirme boyutu 11,9 GB 'dir.<br/><br/> İndirilen gereç şablonu, 180 gün için geçerli olan bir Windows Server 2016 değerlendirme lisansıyla birlikte gelir.<br/>Değerlendirme süresi sona erene kadar yakınsa, OVA şablonunu kullanarak yeni bir gereç indirmeniz ve dağıtmanız ya da gereç sunucusunun işletim sistemi lisansını etkinleştirmeniz önerilir.
**OVA doğrulaması** | Karma değerleri denetleyerek projeden indirilen OVA şablonunu [doğrulayın](tutorial-discover-vmware.md#verify-security) .
**PowerShell betiği** | PowerShell yükleyici betiği kullanarak bir gereci dağıtma hakkında bu [makaleye](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) başvurun.<br/><br/> 
**Donanım ve ağ gereksinimleri** |  Gereç, Windows Server 2016, 32-GB RAM, 8 vCPU ve 80 GB disk depolama ve dış sanal anahtar ile sunucu üzerinde çalışmalıdır.<br/> Gereç doğrudan veya bir ara sunucu üzerinden internet erişimi gerektirir.<br/><br/> Gereci OVA şablonu kullanarak dağıtırsanız, donanım gereksinimlerini karşılayan bir sunucu oluşturmak için vCenter Server yeterli kaynaklara sahip olmanız gerekir.<br/><br/> Gereci var olan bir sunucuda çalıştırırsanız, Windows Server 2016 ' in çalıştığından ve donanım gereksinimlerini karşıladığından emin olun.<br/>_(Şu anda gereç dağıtımı yalnızca Windows Server 2016 ' de desteklenir.)_
**VMware gereksinimleri** | Gereci vCenter Server bir sunucu olarak dağıtırsanız, 5,5, 6,0, 6,5 veya 6,7 çalıştıran bir vCenter Server ve sürüm 5,5 veya üzerini çalıştıran bir ESXi Konağı dağıtılmalıdır.<br/><br/> 
**VDDK (aracısız geçiş)** | Sunuculardan aracısız geçiş için gerecden yararlanmak üzere, VMware vSphere VDDK, Gereç sunucusunda yüklü olmalıdır.

## <a name="appliance---hyper-v"></a>Gereç-Hyper-V

**Gereksinim** | **Hyper-V**
--- | ---
**İzinler** | Gereç Yapılandırma yöneticisine yerel olarak veya uzaktan erişmek için, Gereç sunucusunda yönetici ayrıcalıklarına sahip bir yerel veya etki alanı kullanıcı hesabına sahip olmanız gerekir.
**Gereç Hizmetleri** | Gereç aşağıdaki hizmetlere sahiptir:<br/><br/> - **Gereç Yapılandırma Yöneticisi**: Bu, sunucuların bulunmasını ve değerlendirmesini başlatmak üzere kaynak ayrıntıları ile yapılandırılabilecek bir Web uygulamasıdır.<br/> - **Keşif Aracısı**: Aracı, şirket içi değerlendirmeler olarak oluşturmak için kullanılabilecek sunucu yapılandırma meta verilerini toplar.<br/>- **Değerlendirme Aracısı**: Aracı, performans tabanlı değerlendirmeler oluşturmak için kullanılabilecek sunucu performansı meta verilerini toplar.<br/>- **Otomatik güncelleştirme hizmeti**: hizmet, Gereç üzerinde çalışan tüm aracıların güncel kalmasını sağlar. Her 24 saatte bir otomatik olarak çalışır.
**Proje limitleri** |  Bir gereç yalnızca tek bir projeyle kaydedilebilir.<br/> Tek bir projede birden fazla kayıtlı gereç olabilir.
**Bulma sınırları** | Bir gereç, Hyper-V ortamında çalışan en fazla 5000 sunucuyu bulabilir.<br/> Bir gereç, 300 adede kadar Hyper-V konaklarına bağlanabilir.
**Desteklenen Dağıtım** | Bir VHD şablonu kullanarak bir Hyper-V konağında çalışan sunucu olarak dağıtın.<br/><br/> PowerShell yükleyici betiği kullanarak Windows Server 2016 çalıştıran var olan bir sunucuda dağıtın.
**VHD şablonu** | Bir VHD içeren ZIP dosyası. Projeden veya [buradan](https://go.microsoft.com/fwlink/?linkid=2140422)indirin.<br/><br/> İndirme boyutu 8,91 GB 'dir.<br/><br/> İndirilen gereç şablonu, 180 gün için geçerli olan bir Windows Server 2016 değerlendirme lisansıyla birlikte gelir.<br/> Değerlendirme süresi sona ermeden yakın ise, yeni bir gereç indirmeniz ve dağıtmanız ya da gereç sunucusunun işletim sistemi lisansını etkinleştirmenizi öneririz.
**VHD doğrulaması** | Karma değerleri denetleyerek projeden indirilen VHD şablonunu [doğrulayın](tutorial-discover-hyper-v.md#verify-security) .
**PowerShell betiği** | PowerShell yükleyici betiği kullanarak bir gereci dağıtma hakkında bu [makaleye](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) başvurun.<br/>
**Donanım ve ağ gereksinimleri**  |  Gereç, Windows Server 2016, 16 GB RAM, 8 vCPU, 80 GB disk depolaması ve harici bir sanal anahtar ile sunucu üzerinde çalışmalıdır.<br/> Gereç statik veya dinamik bir IP adresine gerek duyar ve doğrudan ya da bir proxy üzerinden internet erişimi gerektirir.<br/><br/> Gereci bir Hyper-V konağında çalışan bir sunucu olarak çalıştırırsanız, donanım gereksinimlerini karşılayan bir sunucu oluşturmak için konakta yeterli sayıda kaynak olması gerekir.<br/><br/> Gereci var olan bir sunucuda çalıştırırsanız, Windows Server 2016 ' in çalıştığından ve donanım gereksinimlerini karşıladığından emin olun.<br/>_(Şu anda gereç dağıtımı yalnızca Windows Server 2016 ' de desteklenir.)_
**Hyper-V gereksinimleri** | Gereci VHD şablonuyla dağıtırsanız, Azure geçişi tarafından sunulan gereç Hyper-V VM sürüm 5,0 ' dir.<br/><br/> Hyper-V konağı Windows Server 2012 R2 veya üstünü çalıştırmalıdır.

## <a name="appliance---physical"></a>Gereç-fiziksel

**Gereksinim** | **Fiziksel**
--- | ---
**İzinler** | Gereç Yapılandırma yöneticisine yerel olarak veya uzaktan erişmek için, Gereç sunucusunda yönetici ayrıcalıklarına sahip bir yerel veya etki alanı kullanıcı hesabına sahip olmanız gerekir.
**Gereç Hizmetleri** | Gereç aşağıdaki hizmetlere sahiptir:<br/><br/> - **Gereç Yapılandırma Yöneticisi**: Bu, sunucuların bulunmasını ve değerlendirmesini başlatmak üzere kaynak ayrıntıları ile yapılandırılabilecek bir Web uygulamasıdır.<br/> - **Keşif Aracısı**: Aracı, şirket içi değerlendirmeler olarak oluşturmak için kullanılabilecek sunucu yapılandırma meta verilerini toplar.<br/>- **Değerlendirme Aracısı**: Aracı, performans tabanlı değerlendirmeler oluşturmak için kullanılabilecek sunucu performansı meta verilerini toplar.<br/>- **Otomatik güncelleştirme hizmeti**: hizmet, Gereç üzerinde çalışan tüm aracıların güncel kalmasını sağlar. Her 24 saatte bir otomatik olarak çalışır.
**Proje limitleri** |  Bir gereç yalnızca tek bir projeyle kaydedilebilir.<br/> Tek bir projede birden fazla kayıtlı gereç olabilir.<br/>
**Bulma sınırları** | Bir gereç, en fazla 1000 fiziksel sunucu bulabilir.
**Desteklenen Dağıtım** | PowerShell yükleyici betiği kullanarak Windows Server 2016 çalıştıran var olan bir sunucuda dağıtın.
**PowerShell betiği** | Betiği (AzureMigrateInstaller.ps1) projedeki bir ZIP dosyasına veya [buradan](https://go.microsoft.com/fwlink/?linkid=2140334)indirin. [Daha fazla bilgi edinin](tutorial-discover-physical.md).<br/><br/> İndirme boyutu 85,8 MB 'tır.
**Betik doğrulama** | Karma değerleri denetleyerek projeden indirilen PowerShell yükleyici betiğini [doğrulayın](tutorial-discover-physical.md#verify-security) .
**Donanım ve ağ gereksinimleri** |  Gereç, Windows Server 2016, 16 GB RAM, 8 vCPU ile sunucuda çalışmalıdır ve bu da 80 GB disk depolama alanı.<br/> Gereç statik veya dinamik bir IP adresine gerek duyar ve doğrudan ya da bir proxy üzerinden internet erişimi gerektirir.<br/><br/> Gereci var olan bir sunucuda çalıştırırsanız, Windows Server 2016 ' in çalıştığından ve donanım gereksinimlerini karşıladığından emin olun.<br/>_(Şu anda gereç dağıtımı yalnızca Windows Server 2016 ' de desteklenir.)_

## <a name="url-access"></a>URL erişimi

Azure geçişi gereci internet bağlantısı gerektirir.

- Gereci dağıtırken, Azure geçişi gerekli URL 'lere bir bağlantı denetimi yapar.
- Listedeki tüm URL 'Lere erişim izni vermeniz gerekir. Yalnızca değerlendirme yapıyorsanız, VMware aracısız geçişi için gerekli olarak işaretlenen URL 'Leri atlayabilirsiniz.
- İnternet 'e bağlanmak için URL tabanlı bir ara sunucu kullanıyorsanız, proxy 'nin URL 'Leri ararken alınan CNAME kayıtlarını çözümlediği emin olun.

### <a name="public-cloud-urls"></a>Genel bulut URL 'Leri

**URL** | **Ayrıntılar**  
--- | --- |
*.portal.azure.com  | Azure portalına gidin.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *. office.com | Azure aboneliğinizde oturum açın.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Gereç için Azure geçişi ile iletişim kurmak üzere Azure Active Directory (AD) uygulamalar oluşturun.
management.azure.com | Azure geçişi ile iletişim kurmak için gereç için Azure AD uygulamaları oluşturun.
*.services.visualstudio.com | İç izleme için kullanılan gereç günlüklerini karşıya yükleyin.
*.vault.azure.net | Azure Key Vault gizli dizileri yönetin.<br/> Note: çoğaltma sunucularının bu sunucuya erişimi olduğundan emin olun.
aka.ms/* | Diğer adıyla bağlantılarına erişime izin ver; alet Hizmetleri için en son güncelleştirmeleri indirmek ve yüklemek için kullanılır.
download.microsoft.com/download | Microsoft İndirme Merkezi 'nden indirmelere izin ver.
*.servicebus.windows.net | Gereç ve Azure geçişi hizmeti arasındaki iletişim.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Azure geçişi hizmeti URL 'Lerine bağlanın.
*.hypervrecoverymanager.windowsazure.com | **VMware aracısız geçişi için kullanılır**<br/><br/> Azure geçişi hizmeti URL 'Lerine bağlanın.
*.blob.core.windows.net |  **VMware aracısız geçişi için kullanılır**<br/><br/>Geçiş için verileri depolamaya yükleyin.

### <a name="government-cloud-urls"></a>Kamu Bulutu URL 'Leri

**URL** | **Ayrıntılar**  
--- | --- |
*. portal.azure.us  | Azure portalına gidin.
graph.windows.net | Azure aboneliğinizde oturum açın.
login.microsoftonline.us  | Gereç için Azure geçişi ile iletişim kurmak üzere Azure Active Directory (AD) uygulamalar oluşturun.
management.usgovcloudapi.net | Azure geçişi hizmeti ile iletişim kurmak için gereç için Azure AD uygulamaları oluşturun.
*.services.visualstudio.com | İç izleme için kullanılan gereç günlüklerini karşıya yükleyin.
*. vault.usgovcloudapi.net | Azure Key Vault gizli dizileri yönetin.
aka.ms/* | Diğer adıyla bağlantılarına erişime izin ver; alet Hizmetleri için en son güncelleştirmeleri indirmek ve yüklemek için kullanılır.
download.microsoft.com/download | Microsoft İndirme Merkezi 'nden indirmelere izin ver.
*. servicebus.usgovcloudapi.net  | Gereç ve Azure geçişi hizmeti arasındaki iletişim.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Azure geçişi hizmeti URL 'Lerine bağlanın.
*. hypervrecoverymanager.windowsazure.us | **VMware aracısız geçişi için kullanılır**<br/><br/> Azure geçişi hizmeti URL 'Lerine bağlanın.
*. blob.core.usgovcloudapi.net  |  **VMware aracısız geçişi için kullanılır**<br/><br/>Geçiş için verileri depolamaya yükleyin.
*. applicationinsights.us | İç izleme için kullanılan gereç günlüklerini karşıya yükleyin.  

### <a name="public-cloud-urls-for-private-link-connectivity"></a>Özel bağlantı bağlantısı için genel bulut URL 'Leri

Gereç, özel bağlantı erişiminin üzerinde ve üzerinde aşağıdaki URL 'Lere (doğrudan veya proxy aracılığıyla) erişim gerektirir. 

**URL** | **Ayrıntılar**  
--- | --- | 
*.portal.azure.com  | Azure portalına gidin.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *. office.com | Azure aboneliğinizde oturum açın.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Gereç için Azure geçişi ile iletişim kurmak üzere Azure Active Directory (AD) uygulamalar oluşturun.
management.azure.com | Azure geçişi ile iletişim kurmak için gereç için Azure AD uygulamaları oluşturun.
*. services.visualstudio.com (isteğe bağlı) | İç izleme için kullanılan gereç günlüklerini karşıya yükleyin.
aka.ms/* (isteğe bağlı) | Diğer adıyla bağlantılarına erişime izin ver; alet Hizmetleri için en son güncelleştirmeleri indirmek ve yüklemek için kullanılır.
download.microsoft.com/download | Microsoft İndirme Merkezi 'nden indirmelere izin ver.
*.servicebus.windows.net | **VMware aracısız geçişi için kullanılır**<br/><br/> Gereç ve Azure geçişi hizmeti arasındaki iletişim.
*.vault.azure.net | **VMware aracısız geçişi için kullanılır**<br/><br/>  Çoğaltma sunucularının bu sunucuya erişimi olduğundan emin olun.
*.hypervrecoverymanager.windowsazure.com | **VMware aracısız geçişi için kullanılır**<br/><br/> Azure geçişi hizmeti URL 'Lerine bağlanın.
*.blob.core.windows.net |  **VMware aracısız geçişi için kullanılır**<br/><br/>Geçiş için verileri depolamaya yükleyin.

### <a name="government-cloud-urls-for-private-link-connectivity"></a>Özel bağlantı bağlantısı için kamu bulutu URL 'Leri   

Gereç, özel bağlantı erişiminin üzerinde ve üzerinde aşağıdaki URL 'Lere (doğrudan veya proxy aracılığıyla) erişim gerektirir. 

**URL** | **Ayrıntılar**  
--- | --- |
*. portal.azure.us  | Azure portalına gidin.
graph.windows.net | Azure aboneliğinizde oturum açın.
login.microsoftonline.us  | Gereç için Azure geçişi ile iletişim kurmak üzere Azure Active Directory (AD) uygulamalar oluşturun.
management.usgovcloudapi.net | Azure geçişi hizmeti ile iletişim kurmak için gereç için Azure AD uygulamaları oluşturun.
*. services.visualstudio.com (isteğe bağlı) | İç izleme için kullanılan gereç günlüklerini karşıya yükleyin.
aka.ms/* (isteğe bağlı) | Diğer adıyla bağlantılarına erişime izin ver; alet Hizmetleri için en son güncelleştirmeleri indirmek ve yüklemek için kullanılır.
download.microsoft.com/download | Microsoft İndirme Merkezi 'nden indirmelere izin ver.
*. servicebus.usgovcloudapi.net  | **VMware aracısız geçişi için kullanılır**<br/><br/> Gereç ve Azure geçişi hizmeti arasındaki iletişim. 
*. vault.usgovcloudapi.net | **VMware aracısız geçişi için kullanılır**<br/><br/> Azure Key Vault gizli dizileri yönetin.
*. hypervrecoverymanager.windowsazure.us | **VMware aracısız geçişi için kullanılır**<br/><br/> Azure geçişi hizmeti URL 'Lerine bağlanın.
*. blob.core.usgovcloudapi.net  |  **VMware aracısız geçişi için kullanılır**<br/><br/>Geçiş için verileri depolamaya yükleyin.
*. applicationinsights.us (isteğe bağlı) | İç izleme için kullanılan gereç günlüklerini karşıya yükleyin.  

## <a name="collected-data---vmware"></a>Toplanan veriler-VMware

Gereç yapılandırma meta verilerini, performans meta verilerini ve sunucu bağımlılıkları verilerini (aracısız [bağımlılık Analizi](concepts-dependency-visualization.md) kullanılıyorsa) toplar.

### <a name="metadata"></a>Meta veri

Azure geçişi gereci tarafından bulunan meta veriler, sunucuların Azure 'a geçiş için hazır olup olmadığını, doğru boyut sunucularını, planlar maliyetlerini ve uygulama bağımlılıklarını çözümlemeyi sağlamanıza yardımcı olur. Microsoft bu verileri hiçbir lisans uyumluluğu denetimine kullanmaz.

Bu, gerecin topladığı ve Azure 'a gönderdiği sunucu meta verilerinin tam listesidir.

**VERILERI** | **SAYAÇ**
--- | ---
**Sunucu ayrıntıları** |
Sunucu KIMLIĞI | vm.Config. Instanceuuıd
Sunucu adı | vm.Config. Ada
vCenter Server KIMLIĞI | VMwareClient. Instance. UUID
Sunucu açıklaması | vm.Summary.Config. Ek açıklama
Lisans ürün adı | VM. Client. ServiceContent. about. LicenseProductName
İşletim sistemi türü | VM. SummaryConfig. GuestFullName
Önyükleme türü | vm.Config. Yazılımında
Çekirdek sayısı | vm.Config. Hardware. NumCPU
Bellek (MB) | vm.Config. Hardware. MemoryMB
Disk sayısı | vm.Config. Hardware. Device. ToList (). FindAll (x => VirtualDisk). Count
Disk boyutu listesi | vm.Config. Hardware. Device. ToList (). FindAll (x => VirtualDisk)
Ağ bağdaştırıcıları listesi | vm.Config. Hardware. Device. ToList (). FindAll (x => Virtualalethernet). Count
CPU kullanımı | CPU. Usage. Average
Bellek kullanımı |mem. kullanım. Ortalama
**Disk başına Ayrıntılar** |
Disk anahtarı değeri | dis. Anahtar
Dikunit numarası | dis. UnitNumber
Disk denetleyicisi anahtar değeri | dis. ControllerKey. Value
Gigabayt sağlandı | virtualDisk. DeviceInfo. Summary
Disk adı | Disk kullanılarak oluşturulan değer. UnitNumber, disk. Anahtar, disk. ControllerKey. VAlue
Saniye başına okuma işlemi | virtualDisk. Numberreadaveryaşlandırılmış. Average
Saniye başına yazma işlemi | virtualDisk. Numberwriteortalama. Ortalama
Aktarım hızını oku (MB/saniye) | virtualDisk. Read. Average
Yazma üretilen işi (MB/saniye) | virtualDisk. Write. Average
**NIC başına Ayrıntılar** |
Ağ bağdaştırıcısı adı | 'i. Anahtar
MAC adresi | ((Virtualalether, NIC) NIC). MacAddress
IPv4 adresleri | vm.Guest.Net
IPv6 adresleri | vm.Guest.Net
Aktarım hızını oku (MB/saniye) | net. alınan. Ortalama
Yazma üretilen işi (MB/saniye) | net. iletilmiş. Average
**Envanter yolu ayrıntıları** |
Name | kapsayıcı. GetType (). Ada
Alt nesnenin türü | kapsayıcı. ChildType
Başvuru ayrıntıları | kapsayıcı. MoRef
Üst Ayrıntılar | Container. Parent
Sunucu başına klasör ayrıntıları | ((Klasör) kapsayıcısı). ChildEntity. Type
Sunucu başına veri merkezi ayrıntıları | (Datacenter) kapsayıcısı). VmFolder
Konak klasörü başına veri merkezi ayrıntıları | (Datacenter) kapsayıcısı). HostFolder
Konak başına küme ayrıntıları | ((ClusterComputeResource) kapsayıcısı). Konağının
Sunucu başına ana bilgisayar ayrıntıları | (HostSystem) kapsayıcısı). 'Nın

### <a name="performance-data"></a>Performans verileri

İşte, bir gerecin VMware üzerinde çalışan bir sunucu için topladığı ve Azure 'a gönderdiği performans verileri.

**Veriler** | **Sayaç** | **Değerlendirme etkisi**
--- | --- | ---
CPU kullanımı | CPU. Usage. Average | Önerilen sunucu boyutu/maliyet
Bellek kullanımı | mem. kullanım. Ortalama | Önerilen sunucu boyutu/maliyet
Disk okuma üretilen işi (MB/saniye) | virtualDisk. Read. Average | Disk boyutu, depolama maliyeti, sunucu boyutu için hesaplama
Disk yazma miktarı (MB/saniye) | virtualDisk. Write. Average | Disk boyutu, depolama maliyeti, sunucu boyutu için hesaplama
Saniye başına disk okuma işlemi | virtualDisk. Numberreadaveryaşlandırılmış. Average | Disk boyutu, depolama maliyeti, sunucu boyutu için hesaplama
Saniye başına disk yazma işlemleri | virtualDisk. Numberwriteortalama. Ortalama  | Disk boyutu, depolama maliyeti, sunucu boyutu için hesaplama
NIC okuma üretilen işi (MB/saniye) | net. alınan. Ortalama | Sunucu boyutu için hesaplama
NIC yazma üretilen işi (MB/saniye) | net. iletilmiş. Average  |Sunucu boyutu için hesaplama

### <a name="installed-software-inventory"></a>Yüklü yazılım envanteri

Gereç, sunuculardaki yüklü yazılım envanteri hakkındaki verileri toplar.

#### <a name="windows-server-software-inventory-data"></a>Windows Server yazılım envanteri verileri

Bu, gerecin VMware ortamınızda bulunan her bir Windows sunucusundan topladığı yazılım envanteri verileri.

**Veriler** | **Kayıt Defteri Konumu** | **Key**
--- | --- | ---
Uygulama Adı  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Sürüm  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
Sağlayıcı  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Windows Server özellikleri verileri

İşte, bu gereç, VMware ortamınızda bulunan her bir Windows sunucusundan toplanan özellikler verileri.

**Veriler**  | **PowerShell cmdlet 'i** | **Özellik**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Özellik türü | Get-WindowsFeature  | FeatureType
Üst  | Get-WindowsFeature  | Üst

#### <a name="sql-server-metadata"></a>SQL Server meta veri

Bu, gerecin VMware ortamınızda bulunan her bir Windows sunucusundan topladığı SQL Server verileri aşağıda bulabilirsiniz.

**Veriler**  | **Kayıt Defteri Konumu**  | **Key**
--- | --- | ---
Name  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server\ınstance Names\SQL  | ınstalınstalstance
Sürüm  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Sürüm
Hizmet Paketi  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | SP2
Sürüm  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Sürüm

#### <a name="windows-server-operating-system-data"></a>Windows Server işletim sistemi verileri

Bu, gerecin VMware ortamınızda bulunan her bir Windows sunucusundan topladığı işletim sistemi verileri.

**Veriler**  | **WMI sınıfı**  | **WMI sınıfı özelliği**
--- | --- | ---
Name  | Win32_operatingsystem  | Başlık
Sürüm  | Win32_operatingsystem  | Sürüm
Mimari  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Linux Server yazılım envanteri verileri

Bu, gerecin VMware ortamınızda bulunan her bir Linux sunucusundan topladığı yazılım envanteri verileri. Sunucunun işletim sistemine bağlı olarak, bir veya daha fazla komut çalıştırılır.

**Veriler**  | **Komutlar**
--- | ---
Name | RPM, dpkg-Query, Snap
Sürüm | RPM, dpkg-Query, Snap
Sağlayıcı | RPM, dpkg-Query, Snap

#### <a name="linux-server-operating-system-data"></a>Linux sunucusu işletim sistemi verileri

Bu, gerecin VMware ortamınızda bulunan her bir Linux sunucusundan topladığı işletim sistemi verileri.

**Veriler**  | **Komutlar**
--- | ---
Name <br/> sürüm | Aşağıdaki dosyalardan bir veya daha fazlası toplanmıştır:<br/> <br/>/etc/OS-Release  <br> /usr/lib/OS-Release  <br> /etc/Enterprise-Release  <br> /etc/redhat-release  <br> /etc/Oracle-Release  <br> /etc/SuSE-release  <br> /etc/LSB-Release  <br> /etc/debian_version
Mimari | uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server örnekleri ve veritabanı verileri

Gereç SQL Server örneklerine ve veritabanlarına veri toplar.

> [!Note]
> VMware ortamınızda çalışan SQL Server örnekleri ve veritabanlarının keşfi ve değerlendirmesi artık önizlemededir. Bu özelliği denemek için [**bu bağlantıyı**](https://aka.ms/AzureMigrate/SQL) kullanarak **Doğu Avustralya** bölgesinde bir proje oluşturun. Zaten Doğu Avustralya bölgesinde bir projeniz varsa ve bu özelliği denemek istiyorsanız, lütfen portalda bu [**önkoşulları**](how-to-discover-sql-existing-project.md) tamamladığınızdan emin olun.

#### <a name="sql-database-metadata"></a>SQL veritabanı meta verileri

**Veritabanı meta verileri** | **Görünümler/SQL Server özellikleri**
--- | ---
Veritabanının benzersiz tanımlayıcısı | sys.databases
Sunucu tanımlı veritabanı KIMLIĞI | sys.databases
Veritabanının adı | sys.databases
Veritabanının uyumluluk düzeyi | sys.databases
Veritabanının harmanlama adı | sys.databases
Veritabanının durumu | sys.databases
Veritabanının boyutu (MB) | sys.master_files
Veri dosyalarını içeren konumun sürücü harfi | SERVERPROPERTY ve Software\Microsoft\MSSQLServer\MSSQLServer
Veritabanı dosyaları listesi | sys. databases, sys.master_files
Hizmet Aracısı etkin değil veya çalışmıyor | sys.databases
Veritabanı değişiklik verilerini yakalama için etkinleştirildi | sys.databases

#### <a name="sql-server-metadata"></a>SQL Server meta veri

**Sunucu meta verileri** | **Görünümler/SQL Server özellikleri**
--- | ---
Sunucu adı |SERVERPROPERTY
FQDN | Yüklü uygulamaların keşfinden türetilmiş bağlantı dizesi
Yüklemesi KIMLIĞI | sys.dm_server_registry
Sunucu sürümü | SERVERPROPERTY
Sunucu sürümü | SERVERPROPERTY
Sunucu ana bilgisayar platformu (Windows/Linux) | SERVERPROPERTY
Sunucunun ürün düzeyi (RTM SP CTP) | SERVERPROPERTY
Varsayılan yedekleme yolu | SERVERPROPERTY
Veri dosyalarının varsayılan yolu | SERVERPROPERTY ve Software\Microsoft\MSSQLServer\MSSQLServer
Günlük dosyalarının varsayılan yolu | SERVERPROPERTY ve Software\Microsoft\MSSQLServer\MSSQLServer
Hayır. sunucu üzerindeki çekirdekler | sys.dm_os_schedulers, sys.dm_os_sys_info
Sunucu harmanlama adı | SERVERPROPERTY
Hayır. GÖRÜNÜR ÇEVRIMIÇI durumu olan sunucudaki çekirdekler | sys.dm_os_schedulers
Benzersiz sunucu KIMLIĞI | sys.dm_server_registry
HA etkin veya değil | SERVERPROPERTY
Arabellek havuzu uzantısı etkin değil veya | sys.dm_os_buffer_pool_extension_configuration
Yük devretme kümesi yapılandırıldı veya | SERVERPROPERTY
Yalnızca Windows kimlik doğrulama modu kullanan sunucu | SERVERPROPERTY
Sunucu PolyBase 'i yüklüyor | SERVERPROPERTY
Hayır. sistemdeki mantıksal CPU 'Lar | sys.dm_server_registry, sys.dm_os_sys_info
Bir fiziksel işlemci paketi tarafından sunulan mantıksal veya fiziksel çekirdek sayısı oranı | sys.dm_os_schedulers, sys.dm_os_sys_info
Sistemdeki fiziksel CPU sayısı | sys.dm_os_schedulers, sys.dm_os_sys_info
Son başlayan tarih ve saat sunucusu | sys.dm_server_registry
En fazla sunucu belleği kullanımı (MB) | sys.dm_os_process_memory
Toplam No. tüm veritabanları genelindeki kullanıcılar | sys. databases, sys. Logins
Tüm kullanıcı veritabanlarının toplam boyutu | sys.databases
Geçici veritabanı boyutu | sys.master_files, sys.configurışları sys.dm_os_sys_info
Hayır. oturum açma işlemleri | sys. Logins
Bağlı sunucuların listesi | sys. Servers
Aracı işinin listesi | [msdb]. [dbo]. [sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Performans meta verileri

**Performans** | **Görünümler/SQL Server özellikleri** | **Değerlendirme etkisi**
--- | --- | ---
SQL Server CPU kullanımı| sys.dm_os_ring_buffers| Önerilen SKU boyutu (CPU boyutu)
SQL mantıksal CPU sayısı| sys.dm_os_sys_info| Önerilen SKU boyutu (CPU boyutu)
Kullanılan SQL fiziksel belleği| sys.dm_os_process_memory| Kullanılmıyor
SQL bellek kullanım yüzdesi| sys.dm_os_process_memory | Kullanılmıyor
Veritabanı CPU kullanımı| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Önerilen SKU boyutu (CPU boyutu)
Kullanımdaki veritabanı belleği (arabellek havuzu)| sys.dm_os_buffer_descriptors| Önerilen SKU boyutu (bellek boyutu)
Dosya okuma/yazma GÇ| sys.dm_io_virtual_file_stats, sys.master_files| Önerilen SKU boyutu (GÇ boyutu)
Dosya okuma/yazma sayısı| sys.dm_io_virtual_file_stats, sys.master_files| Önerilen SKU boyutu (verimlilik boyutu)
Dosya GÇ kabini okuma/yazma (MS)| sys.dm_io_virtual_file_stats, sys.master_files| Önerilen SKU boyutu (GÇ gecikme boyutu)
Dosya boyutu| sys.master_files| Önerilen SKU boyutu (depolama boyutu)


### <a name="application-dependency-data"></a>Uygulama bağımlılığı verileri

Aracısız bağımlılık analizi, bağlantı ve işlem verilerini toplar.

#### <a name="windows-server-dependencies-data"></a>Windows Server bağımlılıklar verileri

Bu, gerecin aracısız bağımlılık analizi için etkinleştirilen her bir Windows Server 'dan topladığı bağlantı verileri aşağıda verilmiştir.

**Veriler** | **Komutlar**
--- | ---
Yerel bağlantı noktası | Netstat
Yerel IP adresi | Netstat
Uzak bağlantı noktası | Netstat
Uzak IP adresi | Netstat
TCP bağlantı durumu | Netstat
İşlem Kimliği | Netstat
Etkin bağlantı sayısı | Netstat

Bu, gerecin aracısız bağımlılık analizi için etkinleştirilen her bir Windows Server 'dan topladığı bağlantı verileri aşağıda verilmiştir.

**Veriler** | **WMI sınıfı** | **WMI sınıfı özelliği**
--- | --- | ---
İşlem adı | Win32_Process | ExecutablePath
İşlem bağımsız değişkenleri | Win32_Process | CommandLine
Uygulama adı | Win32_Process | ExecutablePath özelliğinin VersionInfo. ProductName parametresi

#### <a name="linux-server-dependencies-data"></a>Linux sunucu bağımlılıkları verileri

Bu, gerecin aracısız bağımlılık analizi için etkinleştirilen her bir Linux sunucusundan topladığı bağlantı verileri aşağıda verilmiştir.

**Veriler** | **Komutlar**
--- | ---
Yerel bağlantı noktası | Netstat
Yerel IP adresi | Netstat
Uzak bağlantı noktası | Netstat
Uzak IP adresi | Netstat
TCP bağlantı durumu | Netstat
Etkin bağlantı sayısı | Netstat
İşlem Kimliği  | Netstat
İşlem adı | PS
İşlem bağımsız değişkenleri | PS
Uygulama adı | dpkg veya rpm

## <a name="collected-data---hyper-v"></a>Toplanan veriler-Hyper-V

Gereç, Hyper-V ortamında çalışan sunuculardan yapılandırma ve performans meta verilerini toplar.

### <a name="metadata"></a>Meta veri
Azure geçişi gereci tarafından bulunan meta veriler, sunucuların Azure 'a geçiş için hazır olup olmadığını, doğru boyut sunucularını ve planların maliyetlerini bulmanıza yardımcı olur. Microsoft bu verileri hiçbir lisans uyumluluğu denetimine kullanmaz.

Bu, gerecin topladığı ve Azure 'a gönderdiği sunucu meta verilerinin tam listesidir.

**Veriler** | **WMI sınıfı** | **WMI sınıfı özelliği**
--- | --- | ---
**Sunucu ayrıntıları** | 
BIOS seri numarası | Msvm_BIOSElement | Bıino SerialNumber
Sunucu türü (Gen 1 veya 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Sunucu görünen adı | Msvm_VirtualSystemSettingData | ElementName
Sunucu sürümü | Msvm_ProcessorSettingData | VirtualQuantity
Bellek (bayt) | Msvm_MemorySettingData | VirtualQuantity
Sunucu tarafından tüketilen maksimum bellek | Msvm_MemorySettingData | Sınır
Dinamik bellek etkin | Msvm_MemorySettingData | DynamicMemoryEnabled
İşletim sistemi adı/sürümü/FQDN | Msvm_KvpExchangeComponent | Guestıntrinsicexchangeıtems adı verileri
Sunucu güç durumu | Msvm_ComputerSystem | EnabledState
**Disk başına Ayrıntılar** |
Disk tanımlayıcısı | Msvm_VirtualHardDiskSettingData | Virtualdiskıd
Sanal sabit disk türü | Msvm_VirtualHardDiskSettingData | Tür
Sanal sabit disk boyutu | Msvm_VirtualHardDiskSettingData | Maxınternalsize
Sanal sabit disk üst öğesi | Msvm_VirtualHardDiskSettingData | ParentPath
**NIC başına Ayrıntılar** |
IP adresleri (yapay NIC 'ler) | Msvm_GuestNetworkAdapterConfiguration | IpAdresleri
DHCP etkin (yapay NIC 'ler) | Msvm_GuestNetworkAdapterConfiguration | DHCPEtkin
NIC KIMLIĞI (yapay NIC 'ler) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC adresi (yapay NIC 'ler) | Msvm_SyntheticEthernetPortSettingData | Adres
NIC KIMLIĞI (eski NIC 'ler) | MsvmEmulatedEthernetPortSetting verileri | InstanceID
NIC MAC KIMLIĞI (eski NIC 'ler) | MsvmEmulatedEthernetPortSetting verileri | Adres

### <a name="performance-data"></a>Performans verileri

Bu, gerecin topladığı ve Azure 'a gönderdiği sunucu performans verileri aşağıda verilmiştir.

**Performans sayacı sınıfı** | **Sayaç** | **Değerlendirme etkisi**
--- | --- | ---
Hyper-V hiper yönetici sanal Işlemcisi | % Konuk çalışma zamanı | Önerilen sunucu boyutu/maliyet
Hyper-V Dinamik Bellek sunucusu | Geçerli basınç (%)<br/> Konuk görünür fiziksel bellek (MB) | Önerilen sunucu boyutu/maliyet
Hyper-V sanal depolama cihazı | Okunan bayt/saniye | Disk boyutu, depolama maliyeti, sunucu boyutu için hesaplama
Hyper-V sanal depolama cihazı | Yazılan bayt/saniye | Disk boyutu, depolama maliyeti, sunucu boyutu için hesaplama
Hyper-V sanal ağ bağdaştırıcısı | Alınan bayt/saniye | Sunucu boyutu için hesaplama
Hyper-V sanal ağ bağdaştırıcısı | Gönderilen bayt/saniye | Sunucu boyutu için hesaplama

- CPU kullanımı, bir sunucuya bağlı tüm sanal işlemciler için tüm kullanımlarının toplamıdır.
- Bellek kullanımı (geçerli basınç * Konuk görünür fiziksel bellek)/100.
- Disk ve ağ kullanım değerleri, listelenen Hyper-V performans sayaçlarından toplanır.

## <a name="collected-data---physical"></a>Toplanan veriler-fiziksel

Gereç, şirket içinde çalışan fiziksel veya sanal sunuculardan yapılandırma ve performans meta verilerini toplar.

### <a name="metadata"></a>Meta veri

Azure geçişi gereci tarafından bulunan meta veriler, sunucuların Azure 'a geçiş için hazır olup olmadığını, doğru boyut sunucularını ve planların maliyetlerini bulmanıza yardımcı olur. Microsoft bu verileri hiçbir lisans uyumluluğu denetimine kullanmaz.

### <a name="windows-server-metadata"></a>Windows Server meta verileri

Bu, gerecin topladığı ve Azure 'a gönderdiği Windows Server meta verilerinin tam listesidir.

**Veriler** | **WMI sınıfı** | **WMI sınıfı özelliği**
--- | --- | ---
FQDN | Win32_ComputerSystem | Etki alanı, ad, PartOfDomain
İşlemci çekirdek sayısı | Win32_PRocessor | Numberofçekirdekler
Ayrılan bellek | Win32_ComputerSystem | TotalPhysicalMemory
BIOS seri numarası | Win32_ComputerSystemProduct | Identifyingnumber
BıOS GUıD 'SI | Win32_ComputerSystemProduct | EDIN
Önyükleme türü | Win32_DiskPartition | Tür = GPT olan bölümü denetle **:** EFı/BIOS için sistem
İşletim sistemi adı | Win32_OperatingSystem | Başlık
İşletim sistemi sürümü |Win32_OperatingSystem | Sürüm
İşletim sistemi mimarisi | Win32_OperatingSystem | OSArchitecture
Disk sayısı | Win32_DiskDrive | Model, boyut, DeviceID, MediaType, ad
Disk boyutu | Win32_DiskDrive | Boyut
NIC listesi | Win32_NetworkAdapterConfiguration | Açıklama, Dizin
NIC IP adresi | Win32_NetworkAdapterConfiguration | IPAddress
NIC MAC adresi | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-server-metadata"></a>Linux sunucusu meta verileri

Bu, gerecin topladığı ve Azure 'a gönderdiği Linux sunucu meta verilerinin tam listesidir.

**Veriler** | **Komutlar**
--- | ---
FQDN | Cat/proc/sys/kernel/hostname, ana bilgisayar adı-f
İşlemci çekirdek sayısı |  /proc/cpuinfo \| awk '/^ işlemci/{print $3} ' \| WC-l
Ayrılan bellek | Cat/proc/meminfo \| grep MemTotal \| awk ' {printf "%. 0f", $2/1024} '
BIOS seri numarası | lshw \| grep "seri:" \| baş-N1 \| awk ' {Print $2} ' <br/> /usr/sbin/dmidecode-t 1 \| grep ' Serial ' \| awk ' {$1 = ""; $2 = ""; Print} '
BıOS GUıD 'SI | Cat/sys/Class/DMI/ID/product_uuid
Önyükleme türü | [-d/sys/firmware/EFI]  && Echo EFı \| \| echo BIOS
İşletim sistemi adı/sürümü | Bu dosyalara işletim sistemi sürümü ve adı için erişirsiniz:<br/><br/> /etc/OS-Release<br/> /usr/lib/OS-Release <br/> /etc/Enterprise-Release <br/> /etc/redhat-release<br/> /etc/Oracle-Release<br/>  /etc/SuSE-release<br/>  /etc/LSB-Release  <br/> /etc/debian_version
İşletim sistemi mimarisi | UNAME-a
Disk sayısı | Fdisk-l \| egrep ' disk. * bayt ' \| awk ' {Print $2} ' \| Kes-F1-d ': '
Önyükleme diski | df/Boot \| SED-n 2p \| awk ' {Print $1} '
Disk boyutu | Fdisk-l \| egrep ' disk. * bayt ' \| egrep $disk: \| awk ' {Print $5} '
NIC listesi | IP-o-4 addr \| awk ' {print $2} ' göster
NIC IP adresi | IP addr $nic \| grep Inet \| awk ' {Print $2} ' \| Kes-F1-d "/" 
NIC MAC adresi | IP addr $Nic \| grep istatistiklerinden  \| awk ' {Print $2} ' öğesini göster

### <a name="windows-performance-data"></a>Windows performans verileri

Bu, gerecin topladığı ve Azure 'a gönderdiği Windows Server performans verileri aşağıda verilmiştir.

**Veriler** | **WMI sınıfı** | **WMI sınıfı özelliği**
--- | --- | ---
CPU kullanımı | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Bellek kullanımı | Win32_PerfFormattedData_PerfOS_Memory | Availablembayt
NIC sayısı | Win32_PerfFormattedData_Tcpip_NetworkInterface | Ağ aygıtı sayısını alın.
NIC başına alınan veri | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
NIC başına aktarılan veriler | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Disk sayısı | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Disk sayısı
Disk ayrıntıları | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Linux performans verileri

Bu, gerecin topladığı ve Azure 'a gönderdiği Linux sunucu performansı verileri.

| **Veriler** | **Komutlar** |
| --- | --- |
| CPU kullanımı | Cat/proc/stat/ \| grep ' CPU '/proc/stat |
| Bellek kullanımı | ücretsiz \| grep mem \| awk ' {Print $3/$ 2 * 100,0} ' |
| NIC sayısı | lshw sınıfı ağ \| grep ETH [0-60] \| WC-l |
| NIC başına alınan veri | Cat/sys/Class/net/ETH $ NIC/istatistikler/rx_bytes |
| NIC başına aktarılan veriler | Cat/sys/Class/net/ETH $ NIC/istatistikler/tx_bytes |
| Disk sayısı | Fdisk-l \| egrep ' disk. \* bayt ' \| awk ' {print $2} ' \| Kes-F1-d ': ' |
| Disk ayrıntıları | Cat/proc/diskstats |

## <a name="appliance-upgrades"></a>Gereç yükseltmeleri

Gereç üzerinde çalışan Azure geçişi Hizmetleri güncelleştirildiğinden gereç yükseltilir. Otomatik güncelleştirme, Gereç üzerinde varsayılan olarak etkinleştirildiğinden, bu otomatik olarak gerçekleşir. Bu varsayılan ayarı, Gereç hizmetlerini el ile güncelleştirmek için değiştirebilirsiniz.

### <a name="turn-off-auto-update"></a>Otomatik güncelleştirmeyi devre dışı bırakma

1. Gereci çalıştıran sunucuda kayıt defteri düzenleyicisini açın.
2. **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance** gidin.
3. Otomatik güncelleştirmeyi devre dışı bırakmak için, DWORD değeri 0 olan bir kayıt defteri anahtarı **Otomatik güncelleştirme** anahtarı oluşturun.

    ![Kayıt defteri anahtarını ayarla](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Otomatik güncelleştirmeyi Aç

Aşağıdaki yöntemlerden birini kullanarak otomatik güncelleştirme 'yi açabilirsiniz:

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance otomatik güncelleştirme kayıt defteri anahtarını silerek.
- Otomatik güncelleştirmeyi açmak için **önkoşulları ayarlama** bölmesindeki en son güncelleştirme denetimlerinden **gereç hizmetlerini görüntüle** ' ye tıklayın.

Kayıt defteri anahtarını silmek için:

1. Gereci çalıştıran sunucuda kayıt defteri düzenleyicisini açın.
2. **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance** gidin.
3. Daha önce otomatik güncelleştirmeyi devre dışı bırakmak için oluşturulmuş kayıt **defteri anahtarını silin**.

Gereç Configuration Manager açmak için, bulma tamamlandıktan sonra:

1. Gereç Yapılandırma Yöneticisi ' nde, **önkoşulları ayarla** paneli ' ne gidin
2. En son güncelleştirmeler denetiminde **gereç hizmetlerini görüntüle** ' ye tıklayın ve otomatik güncelleştirmeyi açmak için bağlantıya tıklayın.

    ![Otomatik güncelleştirmeleri aç](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Gereç Hizmetleri sürümünü denetleyin

Aşağıdaki yöntemlerden birini kullanarak gereç Hizmetleri sürümünü denetleyebilirsiniz:

- Gereç Yapılandırma Yöneticisi 'nde **önkoşulları ayarla** paneline gidin.
- Gereç üzerinde, **Denetim Masası**  >  **Programlar ve Özellikler**' de.

Gereç yapılandırma yöneticisini iade etmek için:

1. Gereç Yapılandırma Yöneticisi ' nde, **önkoşulları ayarla** paneli ' ne gidin
2. En son güncelleştirmeler denetiminde **gereç hizmetlerini görüntüle**' ye tıklayın.

    ![Sürümü denetle](./media/migrate-appliance/versions.png)

Denetim masasını denetlemek için:

1. Gereç üzerinde **Başlat**  >  **Denetim Masası**  >  **Programlar ve Özellikler** ' e tıklayın.
2. Listedeki gereç Hizmetleri sürümlerini denetleyin.

    ![Denetim Masası 'nda sürümü denetle](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Daha eski bir sürümü el ile güncelleştirme

Hizmetlerin herhangi biri için daha eski bir sürüm çalıştırıyorsanız, hizmeti kaldırmanız ve en son sürüme el ile güncelleştirmeniz gerekir.

1. En son gereç hizmeti sürümlerini denetlemek için dosyadaki LatestComponents.js[indirin](https://aka.ms/latestapplianceservices) .
2. İndirdikten sonra dosyayı Not defteri 'nde LatestComponents.jsaçın.
3. Dosyadaki en son hizmet sürümünü ve bunun için karşıdan yükleme bağlantısını bulun. Örnek:

    "Ad": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4. Güncel olmayan bir hizmetin, dosyadaki indirme bağlantısını kullanarak en son sürümünü indirin.
5. İndirdikten sonra, indirilen MSI bütünlüğünü doğrulamak için yönetici komut penceresinde aşağıdaki komutu çalıştırın.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Örneğin: C: \> certutil-HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Komut çıktısının dosyadaki hizmet için karma değer girdisiyle eşleşip eşleşmediğini denetleyin (örneğin, yukarıdaki MD5 karma değeri).
6. Şimdi, hizmeti yüklemek için MSI 'yi çalıştırın. Bu sessiz bir yüklemedir ve yükleme penceresi bittikten sonra kapanır.
7. Yükleme tamamlandıktan sonra, **Denetim Masası**  >  **Programlar ve Özellikler**' de hizmetin sürümünü denetleyin. Hizmet sürümü artık json dosyasında gösterilen en son sürüme yükseltilmelidir.

## <a name="next-steps"></a>Sonraki adımlar

- VMware için gereci ayarlamayı [öğrenin](how-to-set-up-appliance-vmware.md) .
- Hyper-V için gereci ayarlamayı [öğrenin](how-to-set-up-appliance-hyper-v.md) .
- Fiziksel sunucular için gereci ayarlamayı [öğrenin](how-to-set-up-appliance-physical.md) .
