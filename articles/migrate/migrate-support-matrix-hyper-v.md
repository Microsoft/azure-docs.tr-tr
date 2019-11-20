---
title: Hyper-V değerlendirmesi ve geçişi için Azure geçişi destek matrisi
description: Azure geçişi hizmetini kullanarak Hyper-V değerlendirmesi ve geçişi için ayarları ve sınırlamaları özetler.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: raynew
ms.openlocfilehash: dd100e2390cdd2731df498379e376bde4cf2b87d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498704"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Hyper-V değerlendirmesi ve geçiş için destek matrisi

Makineleri değerlendirmek ve Microsoft Azure buluta geçirmek için [Azure geçişi hizmetini](migrate-overview.md) kullanabilirsiniz. Bu makalede, şirket içi Hyper-V VM 'lerini değerlendirmek ve geçirmek için destek ayarları ve sınırlamaları özetlenmektedir.



## <a name="hyper-v-scenarios"></a>Hyper-V senaryoları

Tablo, Hyper-V VM 'Leri için desteklenen senaryoları özetler.

**Dağıtım** | **Ayrıntılar***
--- | ---
**Şirket içi Hyper-V VM 'lerini değerlendirme** | İlk değerlendirmenizi [ayarlayın](tutorial-prepare-hyper-v.md) .<br/><br/> Büyük ölçekli bir değerlendirme [çalıştırın](scale-hyper-v-assessment.md) .
**Hyper-V VM’lerini Azure’a geçirme** | Azure 'a geçiş [yapmayı deneyin](tutorial-migrate-hyper-v.md) .

## <a name="azure-migrate-projects"></a>Azure geçişi projeleri

**Destek** | **Ayrıntılar**
--- | ---
Azure izinleri | Azure geçişi projesi oluşturmak için abonelikte katkıda bulunan veya sahip izinlerinizin olması gerekir.
Hyper-V Sanal Makineleri | Tek bir projede 35.000 adede kadar Hyper-V VM 'yi değerlendirin. Bir Azure aboneliğinde birden çok projeniz olabilir. Bir proje, değerlendirme sınırlarına kadar hem VMware VM 'lerini hem de Hyper-V sanal makinelerini içerebilir.
Coğrafya | Desteklenen coğrafi lıkları [gözden geçirin](migrate-support-matrix.md#supported-geographies) .


## <a name="assessment-hyper-v-host-requirements"></a>Değerlendirme-Hyper-V ana bilgisayar gereksinimleri

| **Destek**                | **Ayrıntılar**               
| :-------------------       | :------------------- |
| **Konak dağıtımı**       | Hyper-V konağı tek başına olabilir veya bir kümede dağıtılabilir. |
| **İzinler**           | Hyper-V konağında yönetici izinlerine sahip olmanız gerekir. <br/> Alternatif olarak, yönetici izinleri atamak istemiyorsanız, bir yerel veya etki alanı kullanıcı hesabı oluşturun ve kullanıcıyı bu gruplara (uzak yönetim kullanıcıları, Hyper-V yöneticileri ve performans Izleyicisi kullanıcıları) ekleyin. |
| **Konak işletim sistemi** | Windows Server 2019, Windows Server 2016 veya Windows Server 2012 R2.<br/> Windows Server 2012 çalıştıran Hyper-V konaklarında yer alan VM'leri değerlendiremezsiniz. |
| **PowerShell uzaktan Iletişim**   | Her konakta etkin olmalıdır. |
| **Hyper-V çoğaltma**       | Hyper-V çoğaltması kullanıyorsanız (veya aynı VM tanımlayıcılarına sahip birden fazla sanal makine varsa) Azure geçişi 'ni kullanarak hem özgün hem de çoğaltılan VM 'Leri keşfetiyorsanız, Azure geçişi tarafından oluşturulan değerlendirme doğru olmayabilir. |


## <a name="assessment-hyper-v-vm-requirements"></a>Değerlendirme-Hyper-V VM gereksinimleri

| **Destek**                  | **Ayrıntılar**               
| :----------------------------- | :------------------- |
| **İşletim sistemi** | Azure tarafından desteklenen tüm [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) ve [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) işletim sistemleri. |
| **Tümleştirme Hizmetleri**       | [Hyper-V tümleştirme hizmetlerinin](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) , işletim sistemi bilgilerini yakalamak için değerlendirmekte olduğunuz VM 'lerde çalışıyor olması gerekir. |



## <a name="assessment-appliance-requirements"></a>Değerlendirme-gereç gereksinimleri

Değerlendirme için Azure geçişi, Hyper-V VM 'lerini bulmaya ve VM meta verilerini ve performans verilerini Azure geçişi 'ne göndermenizi sağlamak için basit bir gereç çalıştırır. Gereç bir Hyper-V VM 'sinde çalışır ve Azure portal indirdiğinizde, sıkıştırılmış bir Hyper-V VHD 'SI kullanarak ayarlanır. Aşağıdaki tablo gereç gereksinimlerini özetler.

| **Destek**                | **Ayrıntılar**               
| :-------------------       | :------------------- |
| **Gereç dağıtımı**   |  Gereci bir Hyper-V VM 'si olarak dağıtırsınız.<br/> Azure geçişi tarafından belirtilen gereç sanal makinesi, Hyper-V VM sürüm 5,0 ' dir.<br/> Hyper-V konağı Windows Server 2012 R2 veya üstünü çalıştırmalıdır.<br/> Ana bilgisayar, 16 GB RAM, 8 vCPU, 80 GB depolama alanı etrafında ve gereç VM 'si için bir dış anahtar ayırmak üzere yeterli alana sahip olmalıdır.<br/> Gereç statik veya dinamik bir IP adresi ve internet erişimi gerektirir.
| **Azure geçişi projesi**  |  Bir gereç, tek bir projeyle ilişkilendirilebilir.<br/> Herhangi bir sayıda gereç, tek bir projeyle ilişkilendirilebilir.<br/> Bir projede en fazla 35.000 sanal makineyi değerlendirebilirsiniz.
| **Hyper-V konakları**          | Bir gereç, 300 adede kadar Hyper-V konaklarına bağlanabilir.
| **Keşfini**              | Tek bir gereç, en fazla 5000 VM bulabilir.
| **Değerlendirme grubu**       | Tek bir gruba en fazla 35.000 makine ekleyebilirsiniz.
| **Değerlendirme**             | Tek bir değerlendirmede 35.000 adede kadar VM 'yi değerlendirebilirsiniz.



## <a name="assessment-appliance-url-access"></a>Değerlendirme-gereç URL 'SI erişimi

VM 'Leri değerlendirmek için, Azure geçişi gereci internet bağlantısı gerektirir.

- Gereci dağıttığınızda Azure geçişi, aşağıdaki tabloda özetlenen URL 'lere bir bağlantı denetimi yapar.
- URL tabanlı bir ara sunucu kullanıyorsanız, tablodaki URL 'lere erişime izin verin ve proxy 'nin URL 'Leri ararken alınan CNAME kayıtlarını çözümlediği emin olun.
- Bir kesintiye uğratan ara sunucu varsa, sunucu sertifikasını proxy sunucusundan gereç 'ya aktarmanız gerekebilir.


**URL** | **Ayrıntılar**  
--- | ---
*.portal.azure.com | Azure portal gezinti
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Azure aboneliğinizde oturum açın
*.microsoftonline.com <br/> *. microsoftonline-p.com | Gereç için hizmet iletişimine yönelik Azure Active Directory uygulamalar oluşturma.
management.azure.com | Gereç için hizmet iletişimine yönelik Azure Active Directory uygulamalar oluşturma.
dc.services.visualstudio.com | Günlüğe kaydetme ve izleme
*.vault.azure.net | Gereç ve hizmet arasında iletişim kurarken Azure Key Vault gizli dizileri yönetin.
aka.ms/* | Diğer adıyla bağlantılarına erişime izin ver.
https://download.microsoft.com/download/* | Microsoft Download sitesinden indirmelere izin verir.



## <a name="assessment-port-requirements"></a>Değerlendirme-bağlantı noktası gereksinimleri

Aşağıdaki tabloda, değerlendirme için bağlantı noktası gereksinimleri özetlenmektedir.

**Aygıtların** | **Bağlanma**
--- | ---
**Elektrikli** | TCP bağlantı noktası 3389 üzerindeki gelen bağlantılar, gereci Uzak Masaüstü bağlantılarına izin vermek için.<br/> 44368 numaralı bağlantı noktası ile gereç yönetimi uygulamasına uzaktan erişim için gelen bağlantılar: ``` https://<appliance-ip-or-name>:44368 ```<br/> Azure geçişi 'ne bulma ve performans meta verileri göndermek için 443, 5671 ve 5672 bağlantı noktalarında giden bağlantılar.
**Hyper-V konağı/kümesi** | Genel Bilgi Modeli (CıM) oturumu kullanarak Hyper-V VM 'lerinin yapılandırma ve performans meta verilerini çekmek için WinRM bağlantı noktaları 5985 (HTTP) ve 5986 (HTTPS) üzerinde gelen bağlantılar.

## <a name="migration-limitations"></a>Geçiş-sınırlamalar
Çoğaltma için aynı anda en fazla 10 VM seçebilirsiniz. Daha fazla makine geçirmek istiyorsanız, 10 grup içinde çoğaltın.

## <a name="migration-hyper-v-host-requirements"></a>Geçiş-Hyper-V ana bilgisayar gereksinimleri

| **Destek**                | **Ayrıntılar**               
| :-------------------       | :------------------- |
| **Konak dağıtımı**       | Hyper-V konağı tek başına olabilir veya bir kümede dağıtılabilir. |
| **İzinler**           | Hyper-V konağında yönetici izinlerine sahip olmanız gerekir. |
| **Konak işletim sistemi** | Windows Server 2019, Windows Server 2016 veya Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Geçiş-Hyper-V VM gereksinimleri

| **Destek**                  | **Ayrıntılar**               
| :----------------------------- | :------------------- |
| **İşletim sistemi** | Azure tarafından desteklenen tüm [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) ve [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) işletim sistemleri. |
| **İzinler**           | Değerlendirmek istediğiniz her Hyper-V VM üzerinde yönetici izinlerine sahip olmanız gerekir. |
| **Tümleştirme Hizmetleri**       | [Hyper-V tümleştirme hizmetlerinin](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) , işletim sistemi bilgilerini yakalamak için değerlendirmekte olduğunuz VM 'lerde çalışıyor olması gerekir. |
| **Azure için gereken değişiklikler** | Bazı VM 'Ler, Azure 'da çalışabilecek şekilde değişiklik gerektirebilir. Azure geçişi, bu değişiklikleri aşağıdaki işletim sistemleri için otomatik olarak yapar:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Detem 7, 8<br/><br/> Diğer işletim sistemleri için geçişten önce el ile ayarlamalar yapmanız gerekir. İlgili makaleler, bunun nasıl yapılacağı hakkında yönergeler içerir. |
| **Linux önyüklemesi**                 | /Boot ayrılmış bir bölümse, işletim sistemi diskinde bulunmalı ve birden çok diske yayılmamalıdır.<br/> /Boot kök (/) bölümünün parçasıysa, '/' bölümünün işletim sistemi diskinde olması ve diğer disklere yayılmamamakta olması gerekir. |
| **UEFı önyüklemesi**                  | Azure 'daki geçirilmiş VM otomatik olarak bir BIOS önyükleme VM 'sine dönüştürülür. VM yalnızca Windows Server 2012 ve üstünü çalıştırmalıdır. İşletim sistemi diski en fazla beş bölüm veya daha az olmalıdır ve işletim sistemi diskinin boyutu 300 GB 'tan az olmalıdır.
  |
| **Disk boyutu**                  | işletim sistemi diski için 2 TB, veri diskleri için 4 TB.
| **Disk numarası** | VM başına en fazla 16 disk.
| **Şifrelenmiş diskler/birimler**    | Geçiş için desteklenmez. |
| **RDM/geçiş diskleri**      | Geçiş için desteklenmez. |
| **Paylaşılan disk** | Paylaşılan diskler kullanan VM 'Ler geçiş için desteklenmez.
| **ALACAĞıNı**                        | VM 'Lere birim olarak bağlanmış NFS birimleri çoğaltılmaz. |
| **ISCSı**                      | Iscsı hedefleri olan VM 'Ler geçiş için desteklenmez.
| **Hedef disk**                | Azure VM 'lerine yalnızca yönetilen disklerle geçiş yapabilirsiniz. |
| **IPv6** | Desteklenmiyor.
| **NIC ekibi oluşturma** | Desteklenmiyor.
| **Azure Site Recovery** | VM, Azure Site Recovery ile çoğaltma için etkinleştirilmişse Azure geçişi geçiş sunucusu geçişini çoğaltamaz.





## <a name="migration-hyper-v-host-url-access"></a>Geçiş-Hyper-V konağı URL 'SI erişimi

Aşağıdaki tabloda, Hyper-V konakları için URL erişim gereksinimleri özetlenmektedir.

**URL** | **Ayrıntılar**  
--- | ---
login.microsoftonline.com | Active Directory kullanarak erişim denetimi ve kimlik yönetimi.
*.backup.windowsazure.com | Çoğaltma veri aktarımı ve düzenlemesi.
*.hypervrecoverymanager.windowsazure.com | Azure geçişi hizmeti URL 'Lerine bağlanın.
*.blob.core.windows.net | Verileri depolama hesaplarına yükleyin.
dc.services.visualstudio.com | İç izleme için kullanılan uygulama günlüklerini karşıya yükleyin.
time.windows.com | Sistem ve genel saat arasındaki saat eşitlemesini doğrular.

## <a name="migration-port-access"></a>Geçiş-bağlantı noktası erişimi

Aşağıdaki tabloda, VM geçişi için Hyper-V konaklarında ve VM 'lerde bağlantı noktası gereksinimleri özetlenmektedir.

**Aygıtların** | **Bağlanma**
--- | ---
Hyper-V konakları/VM 'Ler | HTTPS bağlantı noktası 443 ' deki giden bağlantılar, VM çoğaltma verilerini Azure geçişi 'ne gönderir.




## <a name="next-steps"></a>Sonraki adımlar

Geçiş için [Hyper-V VM değerlendirmesi Için hazırlanma](tutorial-prepare-hyper-v.md) .
