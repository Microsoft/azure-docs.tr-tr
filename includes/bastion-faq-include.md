---
title: include dosyası
description: include dosyası
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ca74b6cf63e3c81d830eca76eea6815548faa1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732256"
---
### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Azure ile bağlantı kurmak için sanal makinemde genel bir IP almam gerekir mi?

Hayır. Azure savunma kullanarak bir VM 'ye bağlandığınızda, bağlandığınız Azure sanal makinesinde genel bir IP 'ye gerek kalmaz. Savunma hizmeti, sanal ağınız dahilinde sanal makinenizin özel IP 'si üzerinden sanal makinenize yönelik RDP/SSH oturumu/bağlantısını açar.

### <a name="is-ipv6-supported"></a>IPv6 destekleniyor mu?

Şu anda IPv6 desteklenmez. Azure savunma yalnızca IPv4 'Ü destekler.

### <a name="can-i-use-azure-bastion-with-azure-private-dns-zones"></a>Azure Özel DNS bölgeleriyle Azure savunma kullanabilir miyim?

Azure Özel DNS bölgeleriyle Azure savunma kullanımı Şu anda desteklenmiyor. Azure savunma kaynağınızı dağıtmadan önce lütfen konak sanal ağının özel bir DNS bölgesine bağlı olmadığından emin olun.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>RDP veya SSH istemcisine ihtiyacım var mı?

Hayır. Azure portal Azure sanal makinenize RDP/SSH 'ye erişmek için bir RDP veya SSH istemcisine ihtiyacınız yoktur. Sanal makinenize doğrudan tarayıcıda RDP/SSH erişimi almanızı sağlamak için [Azure Portal](https://portal.azure.com) kullanın.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Azure sanal makinesinde çalışan bir aracıya ihtiyacım var mı?

Hayır. Tarayıcınıza veya Azure sanal makinenize bir aracı ya da herhangi bir yazılımı yüklemeniz gerekmez. Savunma hizmeti aracısız olur ve RDP/SSH için ek yazılım gerektirmez.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Her bir Azure alımı kaç tane eş zamanlı RDP ve SSH oturumu destekler?

Hem RDP hem de SSH, kullanım tabanlı bir protokoldür. Oturumların yüksek kullanımı, savunma konağının daha düşük toplam oturum sayısını desteklemeye neden olur. Aşağıdaki numaralar normal günlük iş akışlarını kabul eder.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>RDP oturumunda hangi özellikler destekleniyor?

Şu anda yalnızca metin kopyalama/yapıştırma işlemi desteklenir. Dosya kopyalama gibi özellikler desteklenmez. [Azure savunma geri bildirim sayfasındaki](https://feedback.azure.com/forums/217313-networking?category_id=367303)yeni özelliklerle ilgili görüşlerinizi paylaşabilirsiniz.

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Savunma, sanal makine Uzantısı ile birleştirilmiş VM 'lerle çalışır mı?

Bu özellik, Azure AD kullanıcıları kullanan, ASıFATı VM uzantısına katılmış makinelerde çalışmaz. Daha fazla bilgi için bkz. [Windows Azure VM 'leri ve Azure AD](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Hangi tarayıcılar destekleniyor?

Tarayıcı HTML 5 ' i desteklemelidir. Windows üzerinde Microsoft Edge tarayıcısını veya Google Chrome 'u kullanın. Apple Mac için Google Chrome tarayıcısı ' nı kullanın. Microsoft Edge Kmıum, sırasıyla hem Windows hem de Mac 'te desteklenir.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Azure, müşteri verilerini nerede depolar?

Azure savunma, müşteri verilerini dağıtıldığı bölgenin dışına taşımaz veya depolamaz.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Bir sanal makineye erişmek için gereken roller nelerdir?

Bir bağlantı oluşturmak için aşağıdaki roller gereklidir:

* Sanal makinede okuyucu rolü
* Sanal makinenin özel IP’si ile NIC’de okuyucu rolü
* Azure Bastion kaynağında okuyucu rolü

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Fiyatlandırma nedir?

Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://aka.ms/BastionHostPricing).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Azure savunma, Azure 'da barındırılan VM 'lerde yönetim amacıyla bir RDS CAL gerektiriyor mu?

Hayır, Azure 'da Windows Server VM 'lerine erişim yalnızca yönetim amacıyla kullanıldığında bir [RDS CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) gerektirmez.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Uzak oturum sırasında hangi klavye düzenleri desteklenir?

Azure savunma Şu anda VM 'nin içindeki en-US-QWERTY klavye yerleşimini desteklemektedir.  Klavye düzeni için diğer yerel ayarlara yönelik destek, devam etmekte olan çalışmadır.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Kullanıcı tanımlı yönlendirme (UDR), bir Azure savunma alt ağında destekleniyor mu?

Hayır. UDR, bir Azure savunma alt ağında desteklenmez.

Aynı sanal ağda hem Azure savunma hem de Azure Güvenlik Duvarı/ağ sanal gereci (NVA) içeren senaryolarda, Azure savunma ve VM 'leriniz arasındaki iletişim özel olduğundan Azure savunma alt ağından Azure Güvenlik Duvarı 'na trafik zorlamaya gerek kalmaz. Daha fazla bilgi için bkz. [Azure Güvenlik duvarı arkasındaki sanal makinelere savunma Ile erişme](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Savunma oturumu başlamadan önce "oturumunuzun süresi doldu" hata iletisini neden alıyorum?

Bir oturum yalnızca Azure portal başlatılmalıdır. Azure portal oturum açın ve oturumunuzu yeniden başlatın. URL 'ye doğrudan başka bir tarayıcı oturumundan veya sekmesinden giderseniz, bu hata beklenmektedir. Oturumunuzun daha güvende olduğundan ve oturum yalnızca Azure portal aracılığıyla erişilebilmesini sağlamaya yardımcı olur.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Dağıtım başarısızlıklarını Nasıl yaparım? mi?

Tüm hata iletilerini gözden geçirin ve [Azure Portal gerektiğinde bir destek talebi yükseltin](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) . Dağıtım sorunları [, Azure abonelik sınırları, kotaları ve kısıtlamalarından](../articles/azure-resource-manager/management/azure-subscription-service-limits.md)kaynaklanabilir. Özellikle, müşteriler, Azure savunma dağıtımının başarısız olmasına neden olan abonelik başına izin verilen genel IP adresi sayısında bir sınır ile karşılaşabilir.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>Olağanüstü durum kurtarma planmda Azure savunma Nasıl yaparım? eklensin mi?

Azure savunma, VNET 'ler veya eşlenmiş VNET 'ler içinde dağıtılır ve bir Azure bölgesiyle ilişkilendirilir. Azure savunma 'yi olağanüstü durum kurtarma (DR) site VNet 'e dağıtmaya sorumlusunuz. Bir Azure bölgesi arızası durumunda, VM 'niz için DR bölgesine bir yük devretme işlemi gerçekleştirin. Daha sonra, burada dağıtılan VM 'lere bağlanmak için DR bölgesinde dağıtılan Azure savunma konağını kullanın.
