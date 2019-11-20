---
title: Fiziksel sunucu değerlendirmesi ve geçiş için Azure geçişi destek matrisi
description: Azure geçişi hizmetini kullanarak fiziksel sunucu değerlendirmesi ve geçişi için ayarları ve sınırlamaları özetler.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 55ada93e6a77dd7e31640988bad95034f77c90dd
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091887"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>Fiziksel sunucu değerlendirmesi ve geçişi için destek matrisi

Makineleri değerlendirmek ve Microsoft Azure buluta geçirmek için [Azure geçişi hizmetini](migrate-overview.md) kullanabilirsiniz. Bu makalede, şirket içi fiziksel sunucuları değerlendirmek ve geçirmek için destek ayarları ve sınırlamaları özetlenmektedir.

> [!NOTE]
> Burada henüz Azure geçişi portalında görmeyeceğiniz Özellikler bahsedildiğinde, ' ı kapatın. Bu, sonraki hafta içinde görünürler.


## <a name="physical-server-scenarios"></a>Fiziksel sunucu senaryoları

Tablo, fiziksel sunucular için desteklenen senaryoları özetler.

**Dağıtım** | **Ayrıntılar***
--- | ---
**Şirket içi fiziksel sunucuları değerlendirme** | İlk değerlendirmenizi [ayarlayın](tutorial-prepare-physical.md) .<br/><br/> Bir değerlendirme [çalıştırın](tutorial-assess-physical.md) .
**Fiziksel sunucuları Azure’a geçirme** | Azure 'a geçiş [yapmayı deneyin](tutorial-migrate-physical-virtual-machines.md) .


## <a name="azure-migrate-projects"></a>Azure geçişi projeleri

**Destek** | **Ayrıntılar**
--- | ---
**Azure izinleri** | Azure geçişi projesi oluşturmak için abonelikte katkıda bulunan veya sahip izinlerinizin olması gerekir.
**Fiziksel sunucular** | Tek bir projede en fazla 35.000 fiziksel sunucuyu değerlendirin. Bir Azure aboneliğinde birden çok projeniz olabilir. Bir proje, değerlendirme sınırlarına kadar fiziksel sunucular, VMware VM 'Leri ve Hyper-V VM 'Leri içerebilir.
**Coğrafya** | Azure geçişi projelerini çeşitli coğrafi bölgelerde oluşturabilirsiniz. Belirli coğrafi bölgelerde projeler oluşturabilseniz de, diğer hedef konumlar için makineleri değerlendirebilir veya geçirebilirsiniz. Proje Coğrafya yalnızca keşfedilen meta verileri depolamak için kullanılır.

  **Coğrafya** | **Meta veri depolama konumu**
  --- | ---
  Azure Kamu | ABD Devleti Virginia
  Asya Pasifik | Doğu Asya veya Güneydoğu Asya
  Avustralya | Avustralya Doğu veya Avustralya Güneydoğu
  Brezilya | Güney Brezilya
  Kanada | Kanada Orta veya Kanada Doğu
  Avrupa | Kuzey Avrupa veya Batı Avrupa
  Fransa | Fransa Orta
  Hindistan | Orta Hindistan veya Güney Hindistan
  Japonya |  Japonya Doğu veya Japonya Batı
  Güney Kore | Kore Orta veya Kore Güney
  Birleşik Krallık | UK Güney veya UK Batı
  Amerika Birleşik Devletleri | Orta ABD veya Batı ABD 2


 > [!NOTE]
 > Azure Kamu desteği şu anda yalnızca Azure geçişi 'nin [eski sürümünde](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) sunulmaktadır.


## <a name="assessment-physical-server-requirements"></a>Değerlendirme-fiziksel sunucu gereksinimleri

| **Destek**                | **Ayrıntılar**               
| :-------------------       | :------------------- |
| **Fiziksel sunucu dağıtımı**       | Fiziksel sunucu tek başına olabilir veya bir kümede dağıtılabilir. |
| **İzinler**           | **Windows:** Bulmaya dahil etmek istediğiniz tüm Windows sunucularında yerel bir kullanıcı hesabı ayarlayın. Kullanıcı hesabının bu gruplara eklenmesi gerekir-uzak masaüstü kullanıcıları, performans Izleyicisi kullanıcıları ve performans günlüğü kullanıcıları. <br/> **Linux:** Bulunmasını istediğiniz Linux sunucularında bir kök hesabınız olması gerekir. |
| **İşletim sistemi** | Aşağıdakiler dışında tüm [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) ve [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) işletim sistemleri desteklenir:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="assessment-appliance-requirements"></a>Değerlendirme-gereç gereksinimleri

Değerlendirme için Azure geçişi, fiziksel sunucuları bulmaya ve Azure geçişi 'ne sunucu meta verilerini ve performans verilerini göndermenizi sağlamak için basit bir gereç çalıştırır. Gereç bir fiziksel sunucu veya sanal makine üzerinde çalışabilir ve bu ayarı, Azure portal indirtiğiniz bir PowerShell betiği kullanarak ayarlayabilirsiniz. Aşağıdaki tablo gereç gereksinimlerini özetler.

| **Destek**                | **Ayrıntılar**               
| :-------------------       | :------------------- |
| **Gereç dağıtımı**   |  Gereci fiziksel bir sunucuda ya da bir sanal makinede dağıtırsınız.<br/>  Ana makine, Windows Server 2012 R2 veya üstünü çalıştırmalıdır.<br/> Ana bilgisayar, 16 GB RAM, 8 vCPU, 80 GB depolama alanı etrafında ve gereç VM 'si için bir dış anahtar ayırmak üzere yeterli alana sahip olmalıdır.<br/> Gereç statik veya dinamik bir IP adresi ve internet erişimi gerektirir.
| **Azure geçişi projesi**  |  Bir gereç, tek bir projeyle ilişkilendirilebilir.<br/> Herhangi bir sayıda gereç, tek bir projeyle ilişkilendirilebilir.<br/> Bir projede en fazla 35.000 makine değerlendirebilirsiniz.
| **Keşfini**              | Tek bir gereç, en fazla 250 sunucuyu bulabilir.
| **Değerlendirme grubu**       | Tek bir gruba en fazla 35.000 makine ekleyebilirsiniz.
| **Değerlendirme**             | Tek bir değerlendirmede en fazla 35.000 makine değerlendirebilirsiniz.


## <a name="assessment-appliance-url-access"></a>Değerlendirme-gereç URL 'SI erişimi

VM 'Leri değerlendirmek için, Azure geçişi gereci internet bağlantısı gerektirir.

- Gereci dağıttığınızda Azure geçişi, aşağıdaki tabloda özetlenen URL 'lere bir bağlantı denetimi yapar.
- URL tabanlı bir ara sunucu kullanıyorsanız, tablodaki URL 'lere erişime izin verin ve proxy 'nin URL 'Leri ararken alınan CNAME kayıtlarını çözümlediği emin olun.
- Bir kesintiye uğratan ara sunucu varsa, sunucu sertifikasını proxy sunucusundan gereç 'ya aktarmanız gerekebilir.


**URL** | **Ayrıntılar**  
--- | ---
*.portal.azure.com | Azure portal gezinti
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Azure aboneliğinizde oturum açın
*.microsoftonline.com <br/> *.microsoftonline-p.com | Gereç için hizmet iletişimine yönelik Azure Active Directory uygulamalar oluşturma.
management.azure.com | Gereç için hizmet iletişimine yönelik Azure Active Directory uygulamalar oluşturma.
dc.services.visualstudio.com | Günlüğe kaydetme ve izleme
*.vault.azure.net | Gereç ve hizmet arasında iletişim kurarken Azure Key Vault gizli dizileri yönetin.
aka.ms/* | Diğer adıyla bağlantılarına erişime izin ver.
https://download.microsoft.com/download/* | Microsoft Download sitesinden indirmelere izin verir.



## <a name="assessment-port-requirements"></a>Değerlendirme-bağlantı noktası gereksinimleri

Aşağıdaki tabloda, değerlendirme için bağlantı noktası gereksinimleri özetlenmektedir.

**cihaz** | **bağlantı**
--- | ---
**Elektrikli** | TCP bağlantı noktası 3389 üzerindeki gelen bağlantılar, gereci Uzak Masaüstü bağlantılarına izin vermek için.<br/> 44368 numaralı bağlantı noktası ile gereç yönetimi uygulamasına uzaktan erişim için gelen bağlantılar: ``` https://<appliance-ip-or-name>:44368 ```<br/> Azure geçişi 'ne bulma ve performans meta verileri göndermek için 443, 5671 ve 5672 bağlantı noktalarında giden bağlantılar.
**Fiziksel sunucular** | **Windows:** Windows sunucularından yapılandırma ve performans meta verilerini çekmek için 443, 5989 bağlantı noktalarında gelen bağlantılar. <br/> **Linux:**  22 (UDP) bağlantı noktasındaki, Linux sunucularından yapılandırma ve performans meta verileri çekmek için gelen bağlantılar. |


## <a name="next-steps"></a>Sonraki adımlar

Fiziksel sunucu değerlendirmesi ve geçişi için [fiziksel sunucu değerlendirmesi hazırlayın](tutorial-prepare-physical.md) .
