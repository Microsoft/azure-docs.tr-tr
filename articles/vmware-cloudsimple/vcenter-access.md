---
title: CloudSimple-Access vSphere Client tarafından Azure VMware çözümü
description: Özel bulutunuzun vCenter 'a nasıl erişebileceğinizi açıklar.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2e62042a84f7ac2615762e3d9b036e4340bd8c73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899125"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Özel bulut vCenter portala erişin

Özel bulut vCenter portalınızı Azure portal veya CloudSimple portalından başlatabilirsiniz.  vCenter portalı, VMware altyapısını özel bulutunuzda yönetmenizi sağlar.

## <a name="before-you-begin"></a>Başlamadan önce

VCenter portalına erişmek için ağ bağlantısı kurulması ve DNS ad çözümlemesinin etkinleştirilmesi gerekir.  Aşağıdaki seçeneklerden herhangi birini kullanarak özel bulutla ağ bağlantısı kurabilirsiniz.

* [ExpressRoute kullanarak şirket içinden CloudSimple 'a bağlanma](on-premises-connection.md)
* [CloudSimple özel bulutunuz için bir VPN bağlantısı yapılandırma](set-up-vpn.md)

Özel bulut VMware altyapı bileşenlerinizin DNS ad çözümlemesini ayarlamak için bkz. [Şirket içi iş Istasyonlarından özel bulut vCenter erişimi için ad çözümlemesi IÇIN DNS yapılandırma](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="access-vcenter-from-azure-portal"></a>Azure portal 'dan vCenter 'a erişme

Azure portal ' dan özel bulutunuzun vCenter Portal 'ı başlatabilirsiniz.

1. **Tüm Hizmetler**’i seçin.

2. **Cloudsimple Hizmetleri** için arama yapın.

3. Bağlanmak istediğiniz özel bulutunuzun CloudSimple hizmetini seçin.

4. **Genel bakış** sayfasında, **VMware özel bulutlarını görüntüle** ' ye tıklayın.

    ![CloudSimple hizmetine genel bakış](media/cloudsimple-service-overview.png)

5. Özel bulutlar listesinden özel bulutu seçin ve **vSphere Istemcisini Başlat**' a tıklayın.

    ![VSphere Istemcisini Başlat](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>CloudSimple portalından vCenter 'a erişme

CloudSimple Portal ' dan özel bulutunuzun vCenter Portal 'ı başlatabilirsiniz.

1. [Cloudsimple portala](access-cloudsimple-portal.md)erişin.

2. **Kaynaklarda** , erişmek Istediğiniz özel bulutu seçin ve **vSphere istemcisini Başlat**' a tıklayın.

    ![VSphere Istemcisini başlatma-kaynaklar](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Ayrıca özel bulutunuzun Özet ekranından vCenter Portalı ' nı da başlatabilirsiniz.

    ![VSphere Istemcisini başlatma-Özet](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Özel bulutlarınız için VLAN 'Lar/alt ağlar oluşturun ve yönetin](create-vlan-subnet.md)
* [CloudSimple özel bulut izin modeli VMware vCenter](learn-private-cloud-permissions.md)