---
title: 'Azure ExpressRoute: yönlendirici yapılandırma örnekleri-NAT'
description: Bu sayfa, Cisco ve Juniper yönlendirici için yönlendirici yapılandırma örnekleri sağlar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.openlocfilehash: ef2fd40db422c459ca966e802344ef45f7ec01de
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072107"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Ayarlama ve yönetme NAT için yönlendirici yapılandırma örnekleri

Bu sayfa ExpressRoute ile çalışırken, Cisco ASA ve Juniper SRX serisi yönlendiriciler için NAT yapılandırma örnekleri sağlar. Bu kılavuzu yalnızca örnekleri olacak şekilde tasarlanmıştır ve olarak kullanılmamalıdır. Ağınızın uygun yapılandırmalarla gündeme için satıcınıza çalışabilirsiniz.

> [!IMPORTANT]
> Bu sayfada örnekleri, tamamen kılavuzunu olacak şekilde tasarlanmıştır. Satıcınızın satış veya teknik ekip ve ağ takımınızın ihtiyaçlarınıza uygun yapılandırmalarla gündeme ile çalışması gerekir. Microsoft bu sayfada listelenen yapılandırmaları ile ilgili sorunlar desteklemez. Destek sorunları için cihaz satıcınıza başvurmanız gerekir.
> 
> 

* Yönlendirici yapılandırma örnekleri, Azure genel ve Microsoft eşlemeleri için geçerlidir. NAT Azure özel eşleme için yapılandırmamalısınız. Gözden geçirme [ExpressRoute eşlemeleri](expressroute-circuit-peerings.md) ve [ExpressRoute NAT gereksinimleri](expressroute-nat.md) daha fazla ayrıntı için.

* İnternet ve ExpressRoute bağlantı için ayrı bir NAT IP havuzu kullanmanız gerekir. ExpressRoute ve internet aynı NAT IP havuzu kullanarak, asimetrik Yönlendirme ve bağlantı kaybına neden olur.


## <a name="cisco-asa-firewalls"></a>Cisco ASA güvenlik duvarları
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Microsoft Müşteri ağı giden trafik için PAT yapılandırma
    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>


    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>

    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>

    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>

    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2

    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Müşteri ağ Microsoft gelen trafik için PAT yapılandırma

**Arabirimleri ve yönü:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Yapılandırma:**

NAT havuzu:

    object network outbound-PAT
        host <NAT-IP>

Hedef sunucu:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Müşteri IP adresleri için nesne grubu

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

NAT komutlar:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Juniper SRX serisi yönlendiriciler
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. küme için yedekli Ethernet arabirimleri oluşturun
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. iki güvenlik bölgesi oluşturun
* İç ağ için güven bölgesi ve dış ağ uç yönlendiricileri karşılıklı Güvenilmeyenler bölgesi
* Bölgeleri için uygun arabirimleri atayın
* Arabirimler Services'ta izin ver

    güvenlik {bölgeleri {güvenlik bölgesi güven {konak gelen-trafiği {sistemi hizmetleri {ping;                   } {bgp; protokolleri                   {reth0.100;}} arabirimleri               }} {konak gelen-trafiği {sistemi hizmetleri {ping; güvenlik bölgesi Güvenilmeyenler                   } {bgp; protokolleri                   {reth1.100;}} arabirimleri               }           }       }   }


### <a name="3-create-security-policies-between-zones"></a>3. bölgeler arasında güvenlik ilkeleri oluşturma
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. NAT ilkelerini yapılandırma
* İki NAT havuzları oluşturun. Bir NAT trafiği Microsoft'a giden ve Microsoft'tan müşteriye kullanılır.
* İlgili trafik için NAT kuralları oluşturma
  
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. her yönde seçmeli önekleri tanıtmak için BGP 'yi yapılandırın
[Yönlendirme yapılandırma örnekleri](expressroute-config-samples-routing.md) sayfasındaki örneklere bakın.

### <a name="6-create-policies"></a>6. ilke oluşturma
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla ayrıntı için bkz. [ExpressRoute SSS](expressroute-faqs.md).

