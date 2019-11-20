---
title: 'Azure ExpressRoute: yönlendirici yapılandırma örnekleri'
description: Bu sayfa, Cisco ve Juniper yönlendirici için yönlendirici yapılandırma örnekleri sağlar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.openlocfilehash: 2c37dadeb669fb88f858b5487379828a8dddec6c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076660"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Ayarlama ve yönetme yönlendirme için yönlendirici yapılandırma örnekleri
Bu sayfa arabirimi ve yönlendirme yapılandırma örnekleri IOS-XE Cisco ve Juniper MX serisi yönlendiriciler için ExpressRoute ile çalışırken sağlar. Bu kılavuzu yalnızca örnekleri olacak şekilde tasarlanmıştır ve olarak kullanılmamalıdır. Ağınızın uygun yapılandırmalarla gündeme için satıcınıza çalışabilirsiniz. 

> [!IMPORTANT]
> Bu sayfada örnekleri, tamamen kılavuzunu olacak şekilde tasarlanmıştır. Satıcınızın satış veya teknik ekip ve ağ takımınızın ihtiyaçlarınıza uygun yapılandırmalarla gündeme ile çalışması gerekir. Microsoft bu sayfada listelenen yapılandırmaları ile ilgili sorunlar desteklemez. Destek sorunları için cihaz satıcınıza başvurmanız gerekir.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Yönlendirici arabirimlerinin MTU ve TCP MSS ayarları
* Bir Ethernet arabirimi yönlendiriciye için tipik varsayılan MTU 1500, ExpressRoute arabirimin MTU var. Yönlendiriciniz varsayılan olarak farklı bir MTU olmadığı sürece, yönlendirici arabirimdeki bir değer belirtmek için gerek yoktur.
* Azure VPN Gateway, bir ExpressRoute bağlantı hattı için TCP MSS belirtilmesi gerekmez.

Yönlendirici yapılandırma örnekleri tüm eşlemeleri için geçerlidir. Gözden geçirme [ExpressRoute eşlemeleri](expressroute-circuit-peerings.md) ve [ExpressRoute yönlendirme gereksinimleri](expressroute-routing.md) yönlendirme hakkında daha fazla ayrıntı için.


## <a name="cisco-ios-xe-based-routers"></a>Yönlendiriciler Cisco IOS-XE tabanlı
Bu bölümdeki örnekler, XE IOS işletim sistemi ailesi çalıştıran herhangi bir yönlendirici için geçerlidir.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. arabirimleri ve alt arabirimleri yapılandırma
Microsoft'a bağlanmak için her yönlendirici, eşleme başına bir alt arabirimi gerektirir. Bir VLAN kimliği veya VLAN kimlikleri ve bir IP adresi Yığılmış bir çift bir sub arabirimi tanımlanabilir.

**Dot1Q arabirim tanımı**

Bu örnek, tek bir VLAN kimliği ile bir alt arabirimi alt arabirimi tanımını sağlar. VLAN kimliği, eşleme başına benzersizdir. Son sekizli IPv4 adresinizin, her zaman tek bir sayı olur.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ arabirim tanımı**

Bu örnek, bir iki VLAN kimliği ile bir alt arabirimi alt arabirimi tanımını sağlar. Dış VLAN kimliği (s-kullandıysanız etiketi), eşlemeler arasında aynı kalır. İç VLAN kimliği (c-tag), eşleme başına benzersizdir. Son sekizli IPv4 adresinizin, her zaman tek bir sayı olur.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. eBGP oturumlarını ayarlama
Her eşleme için Microsoft ile bir BGP oturumu ayarlamanız gerekir. Aşağıdaki örnek, Microsoft bir BGP oturumu ayarlamak sağlar. (Microsoft) BGP komşu IP adresini, alt arabiriminiz için kullanılan IPv4 adresi a.b.c.d, a.b.c.d+1 olacaktır. BGP komşu'nin IPv4 adresi son sekizli bir çift sayı her zaman olur.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. BGP oturumu üzerinden tanıtımak üzere ön ekleri ayarlama
Select öneklerini Microsoft'a yönlendiriciniz yapılandırabilirsiniz. Aşağıdaki örneği kullanarak bunu yapabilirsiniz.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. yol haritaları
Yol haritaları kullanabilirsiniz ve ağınıza yayılan filtre ön ekleri için önek listeler. Aşağıdaki örnekte, bir görevi gerçekleştirmek için kullanabilirsiniz. Uygun önekle listeleri Kurulum olduğundan emin olun.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Juniper MX serisi yönlendiriciler
Bu bölümdeki örnekler tüm Juniper MX serisi yönlendiriciler için geçerlidir.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. arabirimleri ve alt arabirimleri yapılandırma

**Dot1Q arabirim tanımı**

Bu örnek, tek bir VLAN kimliği ile bir alt arabirimi alt arabirimi tanımını sağlar. VLAN kimliği, eşleme başına benzersizdir. Son sekizli IPv4 adresinizin, her zaman tek bir sayı olur.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**QinQ arabirim tanımı**

Bu örnek, bir iki VLAN kimliği ile bir alt arabirimi alt arabirimi tanımını sağlar. Dış VLAN kimliği (s-kullandıysanız etiketi), eşlemeler arasında aynı kalır. İç VLAN kimliği (c-tag), eşleme başına benzersizdir. Son sekizli IPv4 adresinizin, her zaman tek bir sayı olur.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. eBGP oturumlarını ayarlama
Her eşleme için Microsoft ile bir BGP oturumu ayarlamanız gerekir. Aşağıdaki örnek, Microsoft bir BGP oturumu ayarlamak sağlar. (Microsoft) BGP komşu IP adresini, alt arabiriminiz için kullanılan IPv4 adresi a.b.c.d, a.b.c.d+1 olacaktır. BGP komşu'nin IPv4 adresi son sekizli bir çift sayı her zaman olur.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. BGP oturumu üzerinden tanıtımak üzere ön ekleri ayarlama
Select öneklerini Microsoft'a yönlendiriciniz yapılandırabilirsiniz. Aşağıdaki örneği kullanarak bunu yapabilirsiniz.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. yol haritaları
Yol haritaları kullanabilirsiniz ve ağınıza yayılan filtre ön ekleri için önek listeler. Aşağıdaki örnekte, bir görevi gerçekleştirmek için kullanabilirsiniz. Uygun önekle listeleri Kurulum olduğundan emin olun.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Sonraki Adımlar
Daha fazla ayrıntı için bkz. [ExpressRoute SSS](expressroute-faqs.md).

