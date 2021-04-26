---
title: VLAN/alt ağlar oluşturma-CloudSimple tarafından Azure VMware çözümü
description: CloudSimple tarafından Azure VMware çözümleri-özel bulutlarınız için VLAN 'Ları/alt ağları oluşturmayı ve yönetmeyi ve sonra güvenlik duvarı kuralları uygulamayı açıklar.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06bebcb7369f6604fc79c1d3d0a4a6afa8b0a1da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97896320"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Özel bulutlarınız için VLAN 'Lar/alt ağlar oluşturun ve yönetin

Özel bulutlarınızın VLAN 'Ları/alt ağlarını oluşturmak ve yönetmek için ağ sayfasında VLAN/alt ağlar sekmesini açın. Bir VLAN/subnet oluşturduktan sonra güvenlik duvarı kuralları uygulayabilirsiniz.

## <a name="create-a-vlansubnet"></a>VLAN/subnet oluşturma

1. [CloudSimple portalına erişin](access-cloudsimple-portal.md) ve yan menüdeki **ağ** ' ı seçin.
2. **VLAN/alt ağlar**' ı seçin.
3. **VLAN/subnet oluştur**' a tıklayın.

    ![VLAN/alt ağ sayfası](media/vlan-subnet-page.png)

4. Yeni VLAN/alt ağ için özel bulutu seçin.
5. Bir VLAN KIMLIĞI girin.
6. Alt ağ adını girin.
7. VLAN (alt ağ) üzerinde yönlendirmeyi etkinleştirmek için, alt ağ CıDR aralığını belirtin. CıDR aralığının, şirket içi alt ağlarınızın, Azure alt ağlarının veya ağ geçidi alt ağının herhangi biriyle çakışmadığından emin olun.
8. **Gönder**' e tıklayın.

    ![VLAN/subnet oluştur](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Özel bulut başına 30 VLAN kotası vardır. Bu sınırlar, [Destek ile iletişim](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)kurarak artırılabilir.

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>VSphere içinde dağıtılmış bir bağlantı noktası grubu ayarlamak için VLAN bilgilerini kullanma

VSphere içinde dağıtılmış bir bağlantı noktası grubu oluşturmak için, <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere ağ kılavuzunda</a>' dağıtılmış bağlantı noktası grubu ekleme ' VMware konusundaki yönergeleri izleyin. Dağıtılmış bağlantı noktası grubunu ayarlarken, CloudSimple yapılandırmasından VLAN bilgilerini sağlayın.

![Dağıtılmış bağlantı noktası grubu](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Bir güvenlik duvarı tablosu seçin

Güvenlik Duvarı tabloları ve ilişkili kurallar, **ağ > güvenlik duvarı tabloları** sayfasında tanımlanmıştır. Özel bir bulut için VLAN/subnet 'e uygulanacak güvenlik duvarı tablosunu seçmek için **VLAN/subnet sayfasında** **güvenlik duvarı tablo eki** ' ne tıklayın. Güvenlik Duvarı tablolarını ayarlama ve kuralları tanımlama hakkında yönergeler için bkz. [güvenlik duvarı tabloları](firewall.md) .

![Güvenlik Duvarı tablosu bağlantısı](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Bir alt ağ, bir güvenlik duvarı tablosuyla ilişkilendirilebilir. Bir güvenlik duvarı tablosu, birden çok alt ağ ile ilişkilendirilebilir.

## <a name="edit-a-vlansubnet"></a>VLAN/subnet düzenleme

Bir VLAN/subnet ayarlarını düzenlemek için **VLAN/alt ağlar** sayfasında bunu seçin ve **Düzenle** simgesine tıklayın. Değişiklik yapıp **alt karşılandığından**' ye tıklayın.

## <a name="delete-a-vlansubnet"></a>VLAN/subnet silme

Bir VLAN/subnet silmek için **VLAN/alt ağlar** sayfasında bunu seçin ve **Sil** simgesine tıklayın. Onaylamak için **Sil** ' e tıklayın.
