---
title: include dosyası
description: include dosyası
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: d77269c1e965d5bca1e32b756ef26e2c694e5c81
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747866"
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
İşletim sistemi yerel ayarı | İngilizce (en-us)
Windows Server rolleri | Bu rolleri etkinleştirmeyin: <br> - Active Directory Domain Services <br>- İnternet Bilgi Hizmetleri <br> - Hyper-V 
Grup ilkeleri | Bu grup ilkelerini etkinleştirmeyin: <br> -Komut istemine erişimi engelleyin. <br> -Kayıt defteri düzenlemesi araçlarına erişimi engelleyin. <br> -Dosya ekleri için güven mantığı. <br> -Betik yürütmeyi açın. <br> [Daha fazla bilgi](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Önceden varolan varsayılan Web sitesi yok. <br> -Önceden tanımlı bir Web sitesi veya uygulama, 443 bağlantı noktasını dinliyor. <br>- [Anonim kimlik doğrulamasını](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx)etkinleştirin. <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ayarını etkinleştirin.
| 

## <a name="network-requirements"></a>Ağ gereksinimleri

**Bileşen** | **Gereksinim** 
--- | --- 
IP adresi türü | Statik 
Bağlantı Noktaları | 443 (Denetim kanalı düzenleme)<br>9443 (Veri aktarımı) 
NIC türü | VMXNET3 (yapılandırma sunucusu bir VMware sanal makinesi ise)
 |
**Internet erişimi** (sunucunun doğrudan veya proxy aracılığıyla aşağıdaki URL 'lere erişmesi gerekir):|
\*.backup.windowsazure.com | Çoğaltılan veri aktarımı ve düzenlemesi için kullanılır
\*.store.core.windows.net | Çoğaltılan veri aktarımı ve düzenlemesi için kullanılır
\*.blob.core.windows.net | Çoğaltılan verileri depolayan depolama hesabına erişmek için kullanılır
\*.hypervrecoverymanager.windowsazure.com | Çoğaltma yönetimi işlemleri ve düzenlemesi için kullanılır
https:\//management.azure.com | Çoğaltma yönetimi işlemleri ve düzenlemesi için kullanılır 
*.services.visualstudio.com | Telemetri amaçları için kullanılır (isteğe bağlı)
time.nist.gov | Sistem ve genel saat arasındaki saat eşitlemesini denetlemek için kullanılır
time.windows.com | Sistem ve genel saat arasındaki saat eşitlemesini denetlemek için kullanılır
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF kurulumunun bu URL 'lere erişmesi gerekiyor. Erişim denetimi ve kimlik yönetimi için Azure Active Directory tarafından kullanılır.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | MySQL indirme işleminin tamamlanmasını sağlar. </br> Birkaç bölgede, indirme CDN URL 'sine yeniden yönlendirilebilir. Gerekirse CDN URL 'sinin da beyaz listeye eklendiğinden emin olun.
|

## <a name="required-software"></a>Gerekli yazılım

**Bileşen** | **Gereksinim** 
--- | ---
PowerCLI VMware vSphere | Yapılandırma sunucusu bir VMware VM üzerinde çalışıyorsa [PowerCLI sürüm 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) 'nin yüklenmesi gerekir.
MySQL | MySQL yüklenmelidir. Uygulamasını el ile yükleyebilir veya Azure Site Recovery yükleyebilirsiniz. (Daha fazla bilgi için bkz. [ayarları yapılandırma](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings).)

## <a name="sizing-and-capacity-requirements"></a>Boyutlandırma ve kapasite gereksinimleri

Aşağıdaki tabloda yapılandırma sunucusu için kapasite gereksinimleri özetlenmektedir. Birden çok VMware VM 'yi çoğaltırken, [Kapasite Planlama konuları](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) ' nı gözden geçirin ve [Azure Site Recovery dağıtım planlayıcısı aracını](../articles/site-recovery/site-recovery-deployment-planner.md)çalıştırın.


**'SUNA** | **Bellek** | **Önbellek diski** | **Veri değişim oranı** | **Çoğaltılan makineler**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 yuva * 4 çekirdek \@ 2,5 GHz | 16 GB | 300 GB | 500 GB veya daha az | < 100 makine
12 vCPU<br/><br/> 2 SOCKS * 6 çekirdek \@ 2,5 GHz | 18 GB | 600 GB | 500 GB ila 1 TB | 100-150 makine
16 vCPU<br/><br/> 2 SOCKS * 8 çekirdek \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 makine

