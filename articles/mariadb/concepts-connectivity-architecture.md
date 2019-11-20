---
title: MariaDB için Azure veritabanı 'nda bağlantı mimarisi
description: MariaDB sunucusu için Azure veritabanı 'nın bağlantı mimarisini açıklar.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 5c24a89ca12c36a54a84c61c6343ce960da012c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498062"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'nda bağlantı mimarisi
Bu makalede, MariaDB bağlantı mimarisi için Azure veritabanı 'nın yanı sıra trafiğin Azure 'daki ve dışındaki istemcilerden MariaDB örneğine nasıl yönlendirildiği açıklanmaktadır.

## <a name="connectivity-architecture"></a>Bağlantı mimarisi

MariaDB için Azure veritabanınıza bağlantı, gelen bağlantıları kümelerimize ait fiziksel konuma yönlendirmekten sorumlu bir ağ geçidiyle oluşturulmuştur. Aşağıdaki diyagramda trafik akışı gösterilmektedir.

![Bağlantı mimarisine genel bakış](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

İstemci veritabanına bağlandığında, ağ geçidine bağlanan bir bağlantı dizesi alırlar. Bu ağ geçidinin 3306 numaralı bağlantı noktasını dinleyen bir genel IP adresi vardır. Veritabanı kümesi içinde trafik, MariaDB için uygun Azure veritabanına iletilir. Bu nedenle, şirket ağları gibi sunucunuza bağlanmak için, giden trafiğin ağ geçitlerimize ulaşmasını sağlamak üzere istemci tarafı güvenlik duvarını açmanız gerekir. Aşağıda, bölge başına ağ geçitlerimiz tarafından kullanılan IP adreslerinin tamamen bir listesini bulabilirsiniz.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>MariaDB ağ geçidi IP adresleri için Azure veritabanı

Aşağıdaki tabloda, tüm veri bölgeleri için MariaDB ağ geçidi için Azure veritabanı 'nın birincil ve ikincil IP 'Leri listelenmektedir. Birincil IP adresi, ağ geçidinin geçerli IP adresidir ve ikinci IP adresi birincil hata durumunda bir yük devretme IP adresidir. Belirtildiği gibi, müşteriler her iki IP adresine de giden trafiği izin vermelidir. İkinci IP adresi, bağlantıları kabul etmek için MariaDB için Azure veritabanı tarafından etkinleştirilinceye kadar hiçbir hizmet üzerinde dinleme yapmaz.

| **Bölge adı** | **Birincil IP adresi** | **İkincil IP adresi** |
|:----------------|:-------------|:------------------------|
| Avustralya Doğu | 13.75.149.87 | 40.79.161.1 |
| Avustralya Güneydoğu | 191.239.192.109 | 13.73.109.251 |
| Güney Brezilya | 104.41.11.5 | |
| Kanada Orta | 40.85.224.249 | |
| Doğu Kanada | 40.86.226.166 | |
| Orta ABD | 23.99.160.139 | 13.67.215.62 |
| Çin Doğu 1 | 139.219.130.35 | |
| Çin Doğu 2 | 40.73.82.1 | |
| Çin Kuzey 1 | 139.219.15.17 | |
| Çin Kuzey 2 | 40.73.50.0 | |
| Doğu Asya | 191.234.2.139 | 52.175.33.150 |
| Doğu ABD 1 | 191.238.6.43 | 40.121.158.30 |
| Doğu ABD 2 | 191.239.224.107 | 40.79.84.180 * |
| Fransa Orta | 40.79.137.0 | 40.79.129.1 |
| Almanya Orta | 51.4.144.100 | |
| Hindistan Orta | 104.211.96.159 | |
| Hindistan Güney | 104.211.224.146 | |
| Hindistan Batı | 104.211.160.80 | |
| Doğu Japonya | 191.237.240.43 | 13.78.61.196 |
| Japonya Batı | 191.238.68.11 | 104.214.148.156 |
| Kore Orta | 52.231.32.42 | |
| Kore Güney | 52.231.200.86 |  |
| Orta Kuzey ABD | 23.98.55.75 | 23.96.178.199 |
| Kuzey Avrupa | 191.235.193.75 | 40.113.93.91 |
| Orta Güney ABD | 23.98.162.75 | 13.66.62.124 |
| Güneydoğu Asya | 23.100.117.95 | 104.43.15.0 |
| Güney Afrika Kuzey | 102.133.152.0 | |
| Güney Afrika Batı | 102.133.24.0 | |
| BAE Kuzey | 65.52.248.0 | |
| Birleşik Krallık Güney | 51.140.184.11 | |
| Birleşik Krallık Batı | 51.141.8.11| |
| Batı Avrupa | 191.237.232.75 | 40.68.37.158 |
| Batı ABD 1 | 23.99.34.75 | 104.42.238.205 |
| Batı ABD 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *Doğu ABD 2* Ayrıca `52.167.104.0`BIR üçüncül IP adresine sahiptir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure portal kullanarak MariaDB için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](./howto-manage-firewall-portal.md)
* [Azure CLı kullanarak MariaDB için Azure veritabanı güvenlik duvarı kuralları oluşturma ve yönetme](./howto-manage-firewall-cli.md)
