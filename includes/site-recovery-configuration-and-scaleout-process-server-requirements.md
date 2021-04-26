---
title: include dosyası
description: include dosyası
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 07/08/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 61aa5ffcbab493109371067b1eb9d199a29cb852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100552151"
---
**Yapılandırma ve işlem sunucusu gereksinimleri**


## <a name="hardware-requirements"></a>Donanım gereksinimleri

**Bileşen** | **Gereksinim** 
--- | ---
CPU çekirdekleri | 8 
RAM | 16 GB
Disk sayısı | 3, işletim sistemi diski, işlem sunucusu önbellek diski ve yeniden çalışma için bekletme sürücüsü dahil 
Boş disk alanı (işlem sunucusu önbelleği) | 600 GB
Boş disk alanı (bekletme diski) | 600 GB
 | 

## <a name="software-requirements"></a>Yazılım gereksinimleri

**Bileşen** | **Gereksinim** 
--- | ---
İşletim sistemi | Windows Server 2012 R2 <br> Windows Server 2016
İşletim sistemi yerel ayarı | İngilizce (en-*)
Windows Server rolleri | Bu rolleri etkinleştirmeyin: <br> - Active Directory Domain Services <br>- İnternet Bilgi Hizmetleri <br> - Hyper-V 
Grup ilkeleri | Bu grup ilkelerini etkinleştirmeyin: <br> -Komut istemine erişimi engelleyin. <br> -Kayıt defteri düzenlemesi araçlarına erişimi engelleyin. <br> -Dosya ekleri için güven mantığı. <br> -Betik yürütmeyi açın. <br> [Daha fazla bilgi edinin](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | -Önceden var olan varsayılan Web sitesi yok <br> -Var olan bir Web sitesi/uygulama dinleme 443 bağlantı noktasında dinleniyor <br>- [Anonim kimlik doğrulamasını](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) etkinleştir <br> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) ayarını etkinleştir 
FIPS (Federal bilgi Işleme standartları) | FIPS modunu etkinleştirmeyin
|

## <a name="network-requirements"></a>Ağ gereksinimleri

**Bileşen** | **Gereksinim** 
--- | --- 
IP adresi türü | Statik 
Bağlantı noktaları | 443 (Denetim kanalı düzenleme)<br>9443 (Veri aktarımı) 
NIC türü | VMXNET3 (yapılandırma sunucusu bir VMware sanal makinesi ise)
 |
**Internet erişimi**  (sunucunun doğrudan veya proxy aracılığıyla aşağıdaki URL 'lere erişmesi gerekir):|
\*.backup.windowsazure.com | Çoğaltılan veri aktarımı ve düzenlemesi için kullanılır
\*.blob.core.windows.net | Çoğaltılan verileri depolayan depolama hesabına erişmek için kullanılır. Önbellek depolama hesabınızın belirli bir URL 'sini sağlayabilirsiniz.
\*.hypervrecoverymanager.windowsazure.com | Çoğaltma yönetimi işlemleri ve düzenlemesi için kullanılır
https:\//login.microsoftonline.com | Çoğaltma yönetimi işlemleri ve düzenlemesi için kullanılır 
time.nist.gov | Sistem ve genel saat arasındaki saat eşitlemesini denetlemek için kullanılır
time.windows.com | Sistem ve genel saat arasındaki saat eşitlemesini denetlemek için kullanılır
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https: \/ /login.Live.com </li><li> https: \/ /Graph.Windows.net </li><li> https:\//login.windows.net </li><li> *. services.visualstudio.com (Isteğe bağlı) </li><li> https: \/ /www.Live.com </li><li> https: \/ /www.Microsoft.com </li></ul> | OVF kurulumunun bu ek URL 'lere erişmesi gerekiyor. Erişim denetimi ve kimlik yönetimi için Azure Active Directory tarafından kullanılır.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | MySQL indirme işleminin tamamlanmasını sağlar. </br> Birkaç bölgede, indirme CDN URL 'sine yeniden yönlendirilebilir. Gerekirse CDN URL 'sinin da onaylanmış olduğundan emin olun.
|

> [!NOTE]
> Site Recovery kasasına yönelik [özel bağlantı bağlantılarınız](../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md) varsa, yapılandırma sunucusu için ek internet erişimi gerekmez. Bunun için bir özel durum OVA şablonu kullanarak CS makinesini ayarlarken, özel bağlantı erişiminin ve üzerinde, ve üzerinde aşağıdaki URL 'lere erişmeniz gerekecektir https://management.azure.com https://www.live.com https://www.microsoft.com . Bu URL 'lere erişim izni vermek istemiyorsanız, lütfen birleştirilmiş yükleyiciyi kullanarak CS 'yi ayarlayın.

## <a name="required-software"></a>Gerekli yazılımlar

**Bileşen** | **Gereksinim** 
--- | ---
PowerCLI VMware vSphere | 9,14 ve üzeri sürümler için gerekli değildir
MYSQL | MySQL yüklenmelidir. Uygulamasını el ile yükleyebilir veya Site Recovery yükleyebilirsiniz. (Daha fazla bilgi için [ayarları yapılandırma](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) bölümüne bakın)
|

## <a name="sizing-and-capacity-requirements"></a>Boyutlandırma ve kapasite gereksinimleri

Aşağıdaki tabloda yapılandırma sunucusu için kapasite gereksinimleri özetlenmektedir. Birden çok VMware VM 'yi çoğaltırken, [Kapasite Planlama konuları](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) ' nı gözden geçirin ve [Azure Site Recovery dağıtım planlayıcısı aracını](../articles/site-recovery/site-recovery-deployment-planner.md)çalıştırın.


**CPU** | **Bellek** | **Önbellek diski** | **Veri değişim oranı** | **Çoğaltılan makineler**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 yuva * 4 çekirdek \@ 2,5 GHz | 16 GB | 300 GB | 500 GB veya daha az | < 100 makine
12 vCPU<br/><br/> 2 SOCKS * 6 çekirdek \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100-150 makine
16 vCPU<br/><br/> 2 SOCKS * 8 çekirdek \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 makineler
|