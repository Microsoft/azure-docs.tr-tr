---
title: Şirket içinden CloudSimple tarafından Azure VMware çözümüne erişme
description: Azure VMware çözümünüze, şirket içi ağınızdan bir güvenlik duvarı üzerinden CloudSimple ile erişme
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0f575417819f0e2d46565ad15aaa23a04fd7cf1
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972634"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>CloudSimple özel bulut ortamınıza ve şirket içi uygulamalarınıza erişme

Azure ExpressRoute veya siteden siteye VPN kullanılarak, şirket içi ağdan CloudSimple 'a bir bağlantı ayarlanabilir.  CloudSimple özel bulut vCenter ve bağlantıyı kullanarak özel bulutta çalıştırdığınız tüm iş yüklerinize erişin.  Şirket içi ağınızda bir güvenlik duvarı kullanarak bağlantıda hangi bağlantı noktalarının açıldığını kontrol edebilirsiniz.  Bu makalede tipik uygulamaların bağlantı noktası gereksinimlerinin bazıları açıklanmaktadır.  Diğer uygulamalar için, bağlantı noktası gereksinimleri için uygulama belgelerine bakın.

## <a name="ports-required-for-accessing-vcenter"></a>VCenter 'a erişmek için gereken bağlantı noktaları

Özel bulut vCenter ve NSX-T yöneticinize erişmek için aşağıdaki tabloda tanımlanan bağlantı noktaları şirket içi güvenlik duvarında açılmalıdır.  

| Port       | Source                           | Hedef                      | Amaç                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Şirket içi DNS sunucuları          | Özel bulut DNS sunucuları        | *Az.cloudsimple.io* 'in DNS aramasını şirket Içi ağdan özel bulut DNS sunucularına iletmek için gereklidir.       |
| 53 (UDP)   | Özel bulut DNS sunucuları        | Şirket içi DNS sunucuları          | Özel bulut vCenter 'dan şirket içi DNS sunucularına DNS 'in şirket içi etki alanı adlarını iletmek için gereklidir. |
| 80 (TCP)   | Şirket içi ağı              | Özel bulut yönetimi ağı | *Http* 'den *https*'ye vCenter URL 'sini yeniden yönlendirmek için gereklidir.                                                           |
| 443 (TCP)  | Şirket içi ağı              | Özel bulut yönetimi ağı | Şirket içi ağdan vCenter ve NSX-T yöneticisine erişmek için gereklidir.                                             |
| 8000 (TCP) | Şirket içi ağı              | Özel bulut yönetimi ağı | Şirket içi sanal makinelerin özel buluta vMotion için gereklidir.                                            |
| 8000 (TCP) | Özel bulut yönetimi ağı | Şirket içi ağı              | Özel buluttan şirket içine sanal makinelerin vMotion 'ı için gereklidir.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Kimlik kaynağı olarak şirket içi Active Directory 'yi kullanmak için gereken bağlantı noktaları

Şirket içi Active Directory 'yi özel bulut vCenter 'da kimlik kaynağı olarak yapılandırmak için, tabloda tanımlanan bağlantı noktaları açılmalıdır.  Yapılandırma adımları için bkz. [CloudSimple özel bulutu 'Nda vCenter için kimlik sağlayıcısı olarak Azure ad kullanma](https://docs.azure.cloudsimple.com/azure-ad/) .

| Port         | Source                           | Hedef                                         | Amaç                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Özel bulut DNS sunucuları        | Şirket içi DNS sunucuları                             | Şirket içi Active Directory etki alanı adlarını özel bulut vCenter 'dan şirket içi DNS sunucularına iletmek için gereklidir.          |
| 389 (TCP/UDP) | Özel bulut yönetimi ağı | Şirket içi Active Directory etki alanı denetleyicileri     | Özel bulut vCenter Server 'dan Kullanıcı kimlik doğrulaması için Active Directory etki alanı denetleyicilerine LDAP iletişimi için gereklidir.                |
| 636 (TCP)     | Özel bulut yönetimi ağı | Şirket içi Active Directory etki alanı denetleyicileri     | Özel bulut vCenter Server 'dan Kullanıcı kimlik doğrulaması için Active Directory etki alanı denetleyicilerine Güvenli LDAP (LDAPS) iletişimi için gereklidir. |
| 3268 (TCP)    | Özel bulut yönetimi ağı | Şirket içi Active Directory genel katalog sunucuları | Birden çok etki alanı denetleyicisi dağıtımlarında LDAP iletişimi için gereklidir.                                                                        |
| 3269 (TCP)    | Özel bulut yönetimi ağı | Şirket içi Active Directory genel katalog sunucuları | Çoklu etki alanı denetleyicisi dağıtımlarında LDAPS iletişimi için gereklidir.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>İş yükü sanal makinelerine erişmek için gereken ortak bağlantı noktaları

Özel bulutta çalışan erişim iş yükü sanal makinelerinin, şirket içi güvenlik duvarınızda bağlantı noktalarının açılmasını gerektirir.  Aşağıdaki tabloda, gerekli ortak bağlantı noktalarından bazıları ve bunların amaçları gösterilmektedir.  Uygulamaya özgü herhangi bir bağlantı noktası gereksinimi için uygulama belgelerine bakın.

| Port         | Source                         | Hedef                          | Amaç                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Şirket içi ağı            | Özel bulut iş yükü ağı       | Özel bulutta çalışan Linux sanal makinelerine güvenli kabuk erişimi.              |
| 3389 (TCP)    | Şirket içi ağı            | Özel bulut iş yükü ağı       | Özel bulutta çalışan Windows sanal makinelerine uzak masaüstü.                 |
| 80 (TCP)      | Şirket içi ağı            | Özel bulut iş yükü ağı       | Özel bulutta çalışan sanal makinelere dağıtılan herhangi bir Web sunucusuna erişin.        |
| 443 (TCP)     | Şirket içi ağı            | Özel bulut iş yükü ağı       | Özel bulutta çalışan sanal makinelere dağıtılan güvenli Web sunucularına erişin. |
| 389 (TCP/UDP) | Özel bulut iş yükü ağı | Şirket içi Active Directory ağı | Windows iş yükü sanal makinelerini şirket içi Active Directory etki alanına ekleyin.       |
| 53 (UDP)      | Özel bulut iş yükü ağı | Şirket içi ağı                  | Şirket içi DNS sunucularına iş yükü sanal makineleri için DNS hizmeti erişimi.         |

## <a name="next-steps"></a>Sonraki adımlar

* [VLAN 'Ları ve alt ağları oluşturma ve yönetme](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Azure ExpressRoute kullanarak şirket içi ağa bağlanma](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Şirket içinden siteden siteye VPN kurma](https://docs.azure.cloudsimple.com/vpn-gateway/)
