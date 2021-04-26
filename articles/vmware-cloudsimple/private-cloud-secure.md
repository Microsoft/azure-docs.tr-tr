---
title: CloudSimple güvenli özel buluta göre Azure VMware çözümleri
description: Azure VMware çözümlerini CloudSimple özel bulutu tarafından nasıl güvence altına alınacağını açıklar
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e70745cd6e2f6a2a13581052f65e014bd0d0481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899176"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Özel Bulut ortamınızın güvenliğini sağlama

CloudSimple hizmeti, CloudSimple portalı ve Azure 'dan özel bulut için rol tabanlı erişim denetimi (RBAC) tanımlayın.  Özel bulutun vCenter 'a erişmesi için kullanıcılar, gruplar ve roller VMware SSO kullanılarak belirtilir.  

## <a name="azure-rbac-for-cloudsimple-service"></a>CloudSimple hizmeti için Azure RBAC

CloudSimple hizmeti oluşturma, Azure aboneliğinde **sahip** veya **katkıda bulunan** rolü gerektirir.  Varsayılan olarak, tüm sahipler ve katkıda bulunanlar bir CloudSimple hizmeti oluşturabilir ve özel bulutlar oluşturmak ve yönetmek için CloudSimple portalına erişebilir.  Her bölge için yalnızca bir CloudSimple hizmeti oluşturulabilir.  Belirli yöneticilere erişimi kısıtlamak için aşağıdaki yordamı izleyin.

1. Azure portal yeni bir **kaynak grubunda** Cloudsimple hizmeti oluşturun
2. Kaynak grubu için Azure RBAC belirtin.
3. Aynı kaynak grubunu CloudSimple hizmeti olarak satın alın ve kullanın

Yalnızca kaynak grubunda **sahip** veya **katkıda bulunan** ayrıcalıklarına sahip kullanıcılar cloudsimple hizmetini görebilir ve cloudsimple Portal 'ı başlatır.

Daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>Özel bulut vCenter için RBAC

`CloudOwner@cloudsimple.local`Özel bir bulut oluşturulduğunda vCenter SSO etki alanında varsayılan bir Kullanıcı oluşturulur.  CloudOwner kullanıcısının vCenter yönetimi ayrıcalıkları vardır. Farklı kullanıcılara erişim vermek için vCenter SSO 'ya ek kimlik kaynakları eklenir.  Önceden tanımlanmış roller ve gruplar, ek kullanıcı eklemek için kullanılabilecek vCenter üzerinde ayarlanır.

### <a name="add-new-users-to-vcenter"></a>VCenter 'a Yeni Kullanıcı ekleme

1. Özel buluttaki **Cloudowner \@ cloudsimple. Local** kullanıcısı için [ayrıcalıkları ilerletin](escalate-private-cloud-privileges.md) .
2. **Cloudowner \@ cloudsimple. Local** kullanarak vCenter 'da oturum açın
3. [VCenter tek Sign-On kullanıcıları ekleyin](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. [VCenter çoklu oturum açma gruplarına](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)Kullanıcı ekleyin.

Önceden tanımlanmış roller ve gruplar hakkında daha fazla bilgi için bkz. [Cloudsimple özel bulut izin modeli VMware vCenter](learn-private-cloud-permissions.md) article.

### <a name="add-new-identity-sources"></a>Yeni kimlik kaynakları ekle

Özel bulutunuzun vCenter SSO etki alanı için ek kimlik sağlayıcıları ekleyebilirsiniz.  Kimlik sağlayıcıları kimlik doğrulama ve vCenter SSO grupları, kullanıcılar için yetkilendirme sağlar.

* Özel bulut vCenter üzerinde [kimlik sağlayıcısı olarak Active Directory kullanın](set-vcenter-identity.md) .
* Azure AD 'yi özel bulut vCenter üzerinde [kimlik sağlayıcısı olarak kullanma](azure-ad.md)

1. Özel buluttaki **Cloudowner \@ cloudsimple. Local** kullanıcısı için [ayrıcalıkları ilerletin](escalate-private-cloud-privileges.md) .
2. **Cloudowner \@ cloudsimple. Local** kullanarak vCenter 'da oturum açın
3. Kimlik sağlayıcısından [vCenter çoklu oturum açma gruplarına](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)Kullanıcı ekleyin.

## <a name="secure-network-on-your-private-cloud-environment"></a>Özel bulut ortamınızda güvenli ağ

Özel bulut ortamının ağ güvenliği, ağ erişiminin güvenliğini sağlayarak ve kaynaklar arasındaki ağ trafiğini denetleyerek denetlenir.

### <a name="access-to-private-cloud-resources"></a>Özel bulut kaynaklarına erişim

Özel bulut vCenter ve kaynakları erişimi, güvenli bir ağ bağlantısı üzerinden yapılır:

* **[ExpressRoute bağlantısı](on-premises-connection.md)**. ExpressRoute, şirket içi ortamınızdan güvenli, yüksek bant genişliğine sahip, düşük gecikme süreli bir bağlantı sağlar.  Bağlantının kullanılması, şirket içi hizmetlerinizin, ağlarınızın ve kullanıcılarınızın özel bulut vCenter 'larınıza erişmesine izin verir.
* **[Siteden sıteye VPN ağ geçidi](vpn-gateway.md)**. Siteden siteye VPN, güvenli bir tünel üzerinden şirket içinden özel bulut kaynaklarınıza erişim sağlar.  Hangi şirket içi ağların özel bulutunuzda ağ trafiği gönderebileceğini ve alabileceğini belirtirsiniz.
* **[Noktadan sıteye VPN ağ geçidi](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Özel bulut vCenter 'unuzda hızlı uzaktan erişim için Noktadan siteye VPN bağlantısı kullanın.

### <a name="control-network-traffic-in-private-cloud"></a>Özel buluttaki ağ trafiğini denetleme

Güvenlik Duvarı tabloları ve kuralları özel buluttaki ağ trafiğini denetler.  Güvenlik Duvarı tablosu, tabloda tanımlanan kuralların birleşimine bağlı olarak bir kaynak ağ veya IP adresi ile hedef ağ veya IP adresi arasındaki ağ trafiğini denetlemenize olanak tanır.

1. Bir [güvenlik duvarı tablosu](firewall.md#add-a-new-firewall-table)oluşturun.
2. Güvenlik Duvarı tablosuna [kurallar ekleyin](firewall.md#create-a-firewall-rule) .
3. [BIR VLAN/subnet 'e güvenlik duvarı tablosu ekleyin](firewall.md#attach-vlans-subnet).
