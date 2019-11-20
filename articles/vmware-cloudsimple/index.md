---
title: CloudSimple tarafından sunulan Azure VMware Çözümü
description: CloudSimple tarafından sunulan Azure VMware Çözümü için belge portalı.
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 936a9eb4d9b33b45800fab213fc72a306a04146e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972734"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>CloudSimple tarafından sunulan Azure VMware Çözümü

CloudSimple tarafından sunulan Azure VMware Çözümü ile ilgili yardım için tek adres olan portala hoş geldiniz.
Belgeler sitesinde aşağıdaki konularda bilgi edinebilirsiniz:

## <a name="overview"></a>Genel Bakış

CloudSimple tarafından sunulan Azure VMware Çözümü hakkında daha fazla bilgi edinin

* [CloudSimple tarafından sunulan Azure VMware Çözümü nedir?](cloudsimple-vmware-solutions-overview.md) sayfasında özellikleri, avantajları ve kullanım senaryolarını öğrenin
* [Yönetimle ilgili önemli kavramları](key-concepts.md) gözden geçirin

## <a name="quickstart"></a>Hızlı Başlangıç

Çözümü kullanmaya başlamayı öğrenin

* [Hizmetin nasıl başlatılacağını ve nasıl kapasite satın alınacağını](quickstart-create-cloudsimple-service.md) anlayın
* [Özel Bulut Ortamı Yapılandırma](quickstart-create-private-cloud.md) sayfasında yeni VMware ortamı oluşturmayı öğrenin
* [Azure'da Consume VMware VM'lerini tüketme](quickstart-create-vmware-virtual-machine.md) konusunu gözden geçirerek VMware ile Azure arasında yönetimi birleştirmeyi öğrenin

## <a name="concepts"></a>Kavramlar

Aşağıdaki kavramlar hakkında bilgi edinin

* [CloudSimple Hizmeti](cloudsimple-service.md) ("CloudSimple tarafından sunulan Azure VMware Çözümü - Hizmet" olarak da bilinir). Bu kaynağın her bölgede bir kez oluşturulması gerekir.
* Bir veya birden çok [CloudSimple Düğümü](cloudsimple-node.md) kaynağı oluşturarak ortamınız için kapasite satın alın. Bu kaynaklar "CloudSimple tarafından sunulan Azure VMware Çözümü - Düğüm" olarak da adlandırılır.
* [Özel Bulutlar](cloudsimple-private-cloud.md) kullanarak VMware ortamınızı başlatın ve yapılandırın.
* [CloudSimple Sanal Makineleri](cloudsimple-virtual-machines.md) kullanarak yönetimi birleştirin (CloudSimple tarafından sunulan "Azure VMware Çözümü - Sanal makine" olarak da adlandırılır).
* [VLAN'lar/alt ağlar](cloudsimple-vlans-subnets.md) kullanarak temel ağı tasarlayın.
* [Güvenlik Duvarı Tablosu](cloudsimple-firewall-tables.md) kaynağını kullanarak temel ağınızı segmentlere ayırın ve güvenliğini sağlayın.
* [VPN Ağ Geçitleri](cloudsimple-vpn-gateways.md) kullanarak WAN üzerinden VMware ortamlarınıza güvenli erişim elde edin.
* [Genel IP](cloudsimple-public-ip-address.md) kullanarak iş yükleri için genel erişimi etkinleştirin.
* [Azure Ağ Bağlantısı](cloudsimple-azure-network-connection.md) kullanarak Azure Sanal Ağlarına ve Şirket içi ağlarına bağlantı oluşturun.
* [Hesap Yönetimi](cloudsimple-account.md) kullanarak uyarı e-postası hedeflerini yapılandırın.
* [Etkinlik Yönetimi](cloudsimple-activity.md) ekranlarını kullanarak kullanıcı ve sistem etkinlik günlüklerini görüntüleyin.
* Çeşitli [VMware bileşenlerini](vmware-components.md) anlayın.

## <a name="tutorials"></a>Öğreticiler

Aşağıdakileri gibi genel görevleri nasıl gerçekleştireceğinizi öğrenin:

* VMware ortamlarını dağıtmak istediğiniz her bölgede bir kez [CloudSimple Hizmeti oluşturma](create-cloudsimple-service.md).
* [CloudSimple portalında](access-cloudsimple-portal.md) temel hizmet işlevselliğini yönetme.
* [CloudSimple düğümleri satın alarak](create-nodes.md) altyapınız için kapasiteyi etkinleştirme ve faturalamayı iyileştirme.
* Özel Bulutları kullanarak VMware ortamı yapılandırmalarını yönetme. Özel Bulutları [oluşturabilir](create-private-cloud.md), [yönetebilir](manage-private-cloud.md), [genişletebilir](expand-private-cloud.md) veya [daraltabilirsiniz](shrink-private-cloud.md).
* [Azure Aboneliklerini eşleyerek](azure-subscription-mapping.md) birleşik yönetimi etkinleştirme.
* [Etkinlik sayfalarını](monitor-activity.md) kullanarak kullanıcı ve sistem etkinliğini izleme.
* [Alt ağları oluşturarak ve yöneterek](create-vlan-subnet.md) ortamlarınızda ağ iletişimini yapılandırma.
* [Güvenlik duvarı tablolarını ve kurallarını](firewall.md) kullanarak ortamınızı segmentlere ayırma ve güvenliğini sağlam.
* [Genel IP'ler ayırarak](public-ips.md) iş yükleriniz için gelen İnternet erişimini etkinleştirme.
* [VPN ayarlayarak](vpn-gateway.md) iç ağlarınızdan veya istemci iş istasyonlarınızdan gelen bağlantıyı etkinleştirme.
* Hem [şirket içi ortamlarınızdan](on-premises-connection.md) hem de [Azure Sanal ağlarından](virtual-network-connection.md) gelen iletişimi etkinleştirme.
* [Hesap özetinde](account.md) uyarı hedeflerini yapılandırma ve satın alınan toplam kapasiteyi görüntüleme
* CloudSimple portalına erişmiş olan [kullanıcıları](users.md) görüntüleme.
* Azure portalından VMware sanal makinelerini yönetme:
    * Azure portalında [sanal makineler oluşturma](azure-create-vm.md).
    * Oluşturduğunuz [sanal makineleri yönetme](azure-manage-vm.md).

## <a name="how-to-guides"></a>Nasıl Yapılır Kılavuzları

Bu kılavuzlarda şöyle hedeflerin çözümleri açıklanır:

* [Ortamınızın güvenliğini sağlama](private-cloud-secure.md)
* [Ayrıcalık yükseltme](escalate-privileges.md) kullanarak üçüncü taraf araçlarını yükleme, vSphere'de ek kullanıcıları ve dış kimlik doğrulama kaynağını etkinleştirme.
* [Şirket içi DNS'lerini yapılandırarak](on-premises-dns-setup.md) çeşitli VMware hizmetlerine erişimi yapılandırma.
* [İş yükü DNS veya DHCP'sini yapılandırarak](dns-dhcp-setup.md) iş yükleriniz için ad ve adres atamasını etkinleştirme.
* Hizmet [güncelleştirmeleri ve yükseltmeleri](vmware-components.md#updates-and-upgrades) aracılığıyla hizmetin platformunuzda güvenliği ve işlevselliği nasıl sağladığını anlama.
* [Veeam gibi bir üçüncü taraf yedekleme yazılımıyla](backup-workloads-veeam.md) örnek yedekleme mimarisi oluşturarak yedeklemelerde TCO tasarrufu elde etme.
* [Üçüncü taraf bir KMS şifreleme yazılımıyla](vsan-encryption.md) bekleyen verilerde şifrelemeyi etkinleştirerek güvenli bir ortam oluşturma.
* [Azure Active Directory (Azure AD) kimlik kaynağını](azure-ad.md) yapılandırarak Azure AD yönetimini VMware'e genişletme.
