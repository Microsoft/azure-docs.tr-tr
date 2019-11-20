---
title: Azure VMware çözümünü CloudSimple özel bulutuna göre yönetme
description: CloudSimple özel bulut kaynaklarınızı ve etkinliğinizi yönetmek için kullanılabilen özellikleri açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 620c0226d3aca907352658ebbe1b94c7673d91cd
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812277"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Özel bulut kaynaklarını ve etkinliğini yönetme

Özel bulutlar CloudSimple portalından yönetilir.  Durumu, kullanılabilir kaynakları, özel buluttaki etkinliği ve CloudSimple portalından diğer ayarları kontrol edin.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple portalına erişim

[Cloudsimple portalına](access-cloudsimple-portal.md)erişin.

## <a name="view-the-list-of-private-clouds"></a>Özel bulutlar listesini görüntüleyin

**Kaynaklar** sayfasındaki **özel bulutlar** sekmesi aboneliğinizdeki tüm özel bulutları listeler. Bilgiler, vSphere kümelerinin adını, konumunu, özel bulutun geçerli durumunu ve kaynak bilgilerini içerir.

![Özel bulut sayfası](media/manage-private-cloud.png)

Ek bilgi ve eylemler için özel bir bulut seçin.

## <a name="private-cloud-summary"></a>Özel bulut Özeti

Seçilen özel bulutun kapsamlı bir özetini görüntüleyin.  Özet sayfası, özel buluta dağıtılan DNS sunucularını içerir.  Şirket içi DNS sunucularından DNS iletmeyi, özel bulut DNS sunucularınıza ayarlayabilirsiniz.  DNS iletimi hakkında daha fazla bilgi için bkz. [Şirket Içinden özel bulut vCenter için ad çözümlemesi IÇIN DNS yapılandırma](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Özel bulut Özeti](media/private-cloud-summary.png)

### <a name="available-actions"></a>Kullanılabilir eylemler

* [VSphere Istemcisini başlatın](https://docs.azure.cloudsimple.com/vsphere-access/). Bu özel bulut için vCenter 'a erişin.
* [Düğümleri satın alın](create-nodes.md). Bu özel buluta düğüm ekleyin.
* [Öğesini genişletin](expand-private-cloud.md). Bu özel buluta düğüm ekleyin.
* **Yenileyin**. Bu sayfadaki bilgileri güncelleştirin.
* **Silin**. Özel bulutu dilediğiniz zaman silebilirsiniz. **Silmeden önce tüm sistemleri ve verileri yedeklediğinizden emin olun.** Özel bir bulutu silmek, tüm VM 'Leri, vCenter yapılandırmasını ve verileri siler. Seçili özel bulutun Özet bölümünde **Sil** ' e tıklayın. Silme sonrasında, tüm özel bulut verileri güvenli ve yüksek oranda uyumlu bir işlem sürecinde silinir.
* [VSphere ayrıcalıklarını değiştirin](escalate-private-cloud-privileges.md).  Bu özel bulutta ayrıcalıklarınızı ilerletin.

## <a name="private-cloud-vlanssubnets"></a>Özel bulut VLAN 'LARı/alt ağları

Seçilen özel bulut için tanımlı VLAN/alt ağların listesini görüntüleyin.  Liste, özel bulut oluşturulduğunda oluşturulan yönetim VLAN 'Ları/alt ağlarını içerir.

![Özel bulut-VLAN 'Lar/alt ağlar](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Kullanılabilir eylemler

* [VLAN/alt ağlar ekleyin](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Bu özel buluta bir VLAN/alt küme ekleyin.

Aşağıdaki eylemler için bir VLAN/subnet seçin
* [Güvenlik duvarı tablosu ekleyin](https://docs.azure.cloudsimple.com/firewall/). Bu özel buluta bir güvenlik duvarı tablosu ekleyin.
* **Düzenle**
* **Sil** (yalnızca Kullanıcı tanımlı VLAN/alt ağlar)

## <a name="private-cloud-activity"></a>Özel bulut etkinliği

Seçilen özel bulut için aşağıdaki bilgileri görüntüleyin.  Etkinlik bilgileri, seçilen özel bulutun tüm etkinliklerinin filtrelenmiş bir listesidir.  Bu sayfada en fazla 25 son etkinlik gösterilmektedir.

* Son uyarılar
* Son olaylar
* Son kullanılan görevler
* Son denetim

![Özel bulut-etkinlik](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Bulut rafları

Bulut raflar, özel bulutunuzun yapı taşlarıdır. Her raf bir kapasite birimi sağlar. CloudSimple, özel bir bulut oluştururken veya genişletirken seçimlerinize göre bulut raflarını otomatik olarak yapılandırır.  Her birinin atandığı özel bulut dahil olmak üzere bulut raflarının tam listesini görüntüleyin.

![Özel bulut-bulut raflar](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere yönetim ağı

Özel bulutta yapılandırılmış olan VMware yönetim kaynakları ve sanal makinelerin listesi. Bilgiler yazılım sürümünü, tam etki alanı adını (FQDN) ve kaynakların IP adresini içerir.

![Özel bulut-vSphere yönetim ağı](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'da VMware VM 'lerini kullanma](quickstart-create-vmware-virtual-machine.md)
* [Özel bulutlar](cloudsimple-private-cloud.md) hakkında daha fazla bilgi edinin