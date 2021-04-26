---
title: Azure ortamınızdaki bir Oracle olağanüstü durum kurtarma senaryosuna genel bakış | Microsoft Docs
description: Azure ortamınızda Oracle Database 12c veritabanı için olağanüstü durum kurtarma senaryosu
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: 68b5b9dfd205628c9d7c430df4c0230267752e01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669945"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Azure ortamında Oracle Database 12c veritabanı için olağanüstü durum kurtarma

## <a name="assumptions"></a>Varsayımlar

- Oracle Data Guard tasarımını ve Azure ortamlarını kavramış olursunuz.


## <a name="goals"></a>Hedefler
- Olağanüstü durum kurtarma (DR) gereksinimlerinizi karşılayan topoloji ve yapılandırmayı tasarlayın.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Senaryo 1: Azure 'da birincil ve DR siteleri

Müşterinin birincil sitede ayarlanmış bir Oracle veritabanı vardır. Bir DR sitesi farklı bir bölgedeyse. Müşteri bu siteler arasında hızlı kurtarma için Oracle Data Guard 'ı kullanır. Birincil sitenin raporlama ve diğer kullanımlar için ikincil bir veritabanı da vardır. 

### <a name="topology"></a>Topoloji

Azure kurulumunun özeti aşağıda verilmiştir:

- İki site (birincil site ve bir DR sitesi)
- İki sanal ağ
- Data Guard ile iki Oracle veritabanı (birincil ve bekleme)
- Altın kapısı veya Data Guard ile iki Oracle veritabanı (yalnızca birincil site)
- İki uygulama hizmeti, bir birincil ve bir DR sitesi
- Birincil sitede veritabanı ve uygulama hizmeti için kullanılan bir *kullanılabilirlik kümesi*
- Her sitede özel ağa erişimi kısıtlayan ve yalnızca bir yönetici tarafından oturum açmaya izin veren bir atlama kutusu
- Ayrı alt ağlardaki bir atlama kutusu, uygulama hizmeti, veritabanı ve VPN ağ geçidi
- NSG uygulama ve veritabanı alt ağlarında zorlandı

![Azure 'da birincil ve DR sitelerini gösteren diyagram.](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Senaryo 2: Azure 'da birincil site şirket içi ve DR sitesi

Müşteri, şirket içi bir Oracle veritabanı kurulumuna (birincil site) sahiptir. Bir DR sitesi Azure üzerinde. Oracle Data Guard, bu siteler arasında hızlı kurtarma için kullanılır. Birincil sitenin raporlama ve diğer kullanımlar için ikincil bir veritabanı da vardır. 

Bu kurulum için iki yaklaşım vardır.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Yaklaşım 1: şirket içi ve Azure arasında doğrudan bağlantılar, güvenlik duvarında açık TCP bağlantı noktaları gerektirir 

TCP bağlantı noktalarını dış dünyada kullanıma sunduklarında, doğrudan bağlantıları önermiyoruz.

#### <a name="topology"></a>Topoloji

Azure kurulumunun özeti aşağıda verilmiştir:

- Bir DR sitesi 
- Bir sanal ağ
- Data Guard ile bir Oracle veritabanı (etkin)
- DR sitesinde bir uygulama hizmeti
- Özel ağa erişimi kısıtlayan ve yalnızca bir yönetici tarafından oturum açmaya izin veren bir atlama kutusu
- Ayrı alt ağlardaki bir atlama kutusu, uygulama hizmeti, veritabanı ve VPN ağ geçidi
- NSG uygulama ve veritabanı alt ağlarında zorlandı
- Gelen TCP bağlantı noktası 1521 ' e (veya Kullanıcı tanımlı bir bağlantı noktasına) izin veren bir NSG ilkesi/kuralı
- Sanal ağa erişmek için yalnızca şirket içi IP adresini/adreslerini (DB veya uygulama) kısıtlamak için bir NSG ilkesi/kuralı

![Şirket içi ve Azure arasında doğrudan bağlantıları gösteren ve güvenlik duvarında açık TCP bağlantı noktaları gerektiren diyagram.](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Yaklaşım 2: siteden siteye VPN
Siteden siteye VPN daha iyi bir yaklaşımdır. VPN ayarlama hakkında daha fazla bilgi için bkz. [CLI kullanarak siteden sıteye VPN bağlantısı ile sanal ağ oluşturma](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md).

#### <a name="topology"></a>Topoloji

Azure kurulumunun özeti aşağıda verilmiştir:

- Bir DR sitesi 
- Bir sanal ağ 
- Data Guard ile bir Oracle veritabanı (etkin)
- DR sitesinde bir uygulama hizmeti
- Özel ağa erişimi kısıtlayan ve yalnızca bir yönetici tarafından oturum açmaya izin veren bir atlama kutusu
- Bir atlama kutusu, uygulama hizmeti, veritabanı ve VPN Gateway farklı alt ağlarda bulunur
- NSG uygulama ve veritabanı alt ağlarında zorlandı
- Şirket içi ve Azure arasında siteden siteye VPN bağlantısı

![DR topolojisi sayfasının ekran görüntüsü](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Ek okuma

- [Azure 'da Oracle veritabanı tasarlama ve uygulama](oracle-design.md)
- [Oracle Data Guard’ı yapılandırma](configure-oracle-dataguard.md)
- [Oracle altın kapısını yapılandırma](configure-oracle-golden-gate.md)
- [Oracle yedekleme ve kurtarma](./oracle-overview.md)


## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: yüksek oranda kullanılabilir VM 'Ler oluşturma](../../linux/create-cli-complete.md)
- [VM dağıtımı Azure CLı örneklerini keşfet](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)