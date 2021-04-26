---
title: Ağ Geçidi trafiği geçiş bildirimi
description: Makale, Azure SQL veritabanı ağ geçidi IP adreslerini geçirme hakkında kullanıcılara bildirim sağlar
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 07611a3620a2fd8efe0da075b03b55a5be3a5be9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505386"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL veritabanı trafiğini daha yeni ağ geçitlerine geçirme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure altyapısı gelişdiğinde, Microsoft, olası en iyi müşteri deneyimini sağlamamızı sağlamak için donanımı düzenli olarak yenileyecek. Önümüzdeki aylarda, daha yeni donanım oluşturma, trafiği geçirme ve son olarak bazı bölgelerde daha eski donanımlar üzerinde oluşturulmuş ağ geçitlerinin yetkisini alma planlıyoruz.  

Müşteriler, her bölgede kullanılabilir olan ağ geçitlerine yapılan herhangi bir değişikliğe göre hizmet durumu bildirimleri aracılığıyla bilgilendirilir. Müşteriler [, etkinlik günlüğü uyarılarını ayarlamak için Azure Portal kullanabilir](../../service-health/alerts-activity-log-service-notifications-portal.md).

En güncel bilgiler [Azure SQL veritabanı ağ GEÇIDI IP adresleri](connectivity-architecture.md#gateway-ip-addresses) tablosunda tutulacaktır.

## <a name="status-updates"></a>Durum güncelleştirmeleri

# <a name="in-progress"></a>[Sürüyor](#tab/in-progress-ip)
## <a name="may-2021"></a>Mayıs 2021
Yeni SQL ağ geçitleri aşağıdaki bölgelere ekleniyor:
- UK Güney: 51.140.144.36, 51.105.72.32  
- Orta Batı ABD: 13.71.193.32, 13.71.193.33 

Bu SQL ağ geçidi, 17 Mayıs 2021 ' de müşteri trafiğini kabul etmeye başlayacaktır.

## <a name="april-2021"></a>2021 Nisan
Yeni SQL ağ geçitleri aşağıdaki bölgelere ekleniyor:
- Doğu ABD 2:40.70.144.193

Bu SQL ağ geçidi, 30 Nisan 2021 tarihinde müşteri trafiğini kabul etmeye başlayacaktır.

Yeni SQL ağ geçitleri aşağıdaki bölgelere ekleniyor:
- Norveç Doğu: 51.120.96.33
- Güney Doğu Asya: 13.67.16.193
- Güney Afrika Kuzey: 102.133.152.32
- Kore Güney: 52.231.151.96
- Kuzey Orta: ABD 52.162.105.9
- Avustralya Güney Doğu: 13.77.49.32 

Bu SQL ağ geçitleri, 5 Nisan 2021 tarihinde müşteri trafiğini kabul etmeye başlar.

## <a name="march-2021"></a>Mart 2021
Birden çok bölgedeki aşağıdaki SQL ağ geçitleri devre dışı bırakılıyor sürecinde:
- Brezilya Güney: 104.41.11.5
- Doğu Asya: 191.234.2.139
- Doğu ABD: 191.238.6.43
- Japonya Doğu: 191.237.240.43
- Japonya Batı: 191.238.68.11
- Kuzey Avrupa: 191.235.193.75
- Orta Güney ABD: 23.98.162.75
- Güneydoğu Asya: 23.100.117.95
- Batı Avrupa: 191.237.232.75
- Batı ABD: 23.99.34.75

Bu ağ geçitleri (eski donanımlar üzerinde çalışan) hiçbir müşteri trafiğini yönlendirmediğinden hiçbir müşteri etkisi önerilmez. Bu ağ geçitlerinin IP adresleri 15 Mart 2021 ' de devre dışı bırakılır.

# <a name="completed"></a>[Tamamlandı](#tab/completed-ip)
Aşağıdaki ağ geçidi geçişleri tamamlanmıştır: 

## <a name="february-2021"></a>Şubat 2021
Yeni SQL ağ geçitleri aşağıdaki bölgelere ekleniyor:

- Orta ABD: 13.89.169.20

Bu SQL ağ geçitleri, 28 Şubat 2021 ' de müşteri trafiğini kabul etmeye başlar.

## <a name="january-2021"></a>Ocak 2021
Yeni SQL ağ geçitleri aşağıdaki bölgelere ekleniyor:

- Avustralya Orta: 20.36.104.6, 20.36.104.7 
- Avustralya Orta 2:20.36.112.6 
- Brezilya Güney: 191.234.144.16, 191.234.152.3 
- Kanada Doğu: 40.69.105.9, 40.69.105.10
- Hindistan Orta: 104.211.86.30, 104.211.86.31 
- Doğu Asya: 13.75.32.14 
- Fransa Orta: 40.79.137.8, 40.79.145.12 
- Fransa Güney: 40.79.177.10, 40.79.177.12
- Kore Orta: 52.231.17.22, 52.231.17.23
- Hindistan Batı: 104.211.144.4

Bu SQL ağ geçitleri, 31 Ocak 2021 tarihinde müşteri trafiğini kabul etmeye başlar.



### <a name="october-2020"></a>Ekim 2020

Yeni SQL ağ geçitleri aşağıdaki bölgelere ekleniyor:

- Almanya Orta Batı: 51.116.240.0, 51.116.248.0

Bu SQL ağ geçitleri, 12 Ekim 2020 ' de müşteri trafiğini kabul etmeye başlar. 

### <a name="september-2020"></a>Eylül 2020
Yeni SQL ağ geçitleri aşağıdaki bölgelere ekleniyor. Bu SQL ağ geçitleri, **15 eylül 2020**' de müşteri trafiğini kabul etmeye başlayacaktır:

- Avustralya Güneydoğu: 13.77.48.10
- Kanada Doğu: 40.86.226.166, 52.242.30.154
- UK Güney: 51.140.184.11, 51.105.64.0

Mevcut SQL ağ geçitleri aşağıdaki bölgelerde trafiği kabul etmeye başlayacaktır. Bu SQL ağ geçitleri, **15 eylül 2020** ' de müşteri trafiğini kabul etmeye başlayacaktır:

- Avustralya Güneydoğu: 191.239.192.109 ve 13.73.109.251
- Orta ABD: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 ve 104.208.21.1
- Doğu Asya: 191.234.2.139, 52.175.33.150 ve 13.75.32.4
- Doğu ABD: 40.121.158.30, 40.79.153.12, 191.238.6.43 ve 40.78.225.32
- Doğu ABD 2:40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 ve 104.208.150.3
- Fransa Orta: 40.79.137.0 ve 40.79.129.1
- Japonya Batı: 104.214.148.156, 40.74.100.192, 191.238.68.11 ve 40.74.97.10
- Orta Kuzey ABD: 23.96.178.199, 23.98.55.75 ve 52.162.104.33
- Güneydoğu Asya: 104.43.15.0, 23.100.117.95 ve 40.78.232.3
- Batı ABD: 104.42.238.205, 23.99.34.75 ve 13.86.216.196

Yeni SQL ağ geçitleri aşağıdaki bölgelere ekleniyor. Bu SQL ağ geçitleri, **10 eylül 2020**' de müşteri trafiğini kabul etmeye başlayacaktır:

- Orta Batı ABD: 13.78.248.43 
- Güney Afrika Kuzey: 102.133.120.2  

Yeni SQL ağ geçitleri aşağıdaki bölgelere ekleniyor. Bu SQL ağ geçitleri, **1 eylül 2020**' de müşteri trafiğini kabul etmeye başlayacaktır:

- Kuzey Avrupa: 13.74.104.113 
- Batı ABD2:40.78.248.10 
- Batı Avrupa: 52.236.184.163 
- Orta Güney ABD: 20.45.121.1, 20.49.88.1 

Mevcut SQL ağ geçitleri aşağıdaki bölgelerde trafiği kabul etmeye başlayacaktır. Bu SQL ağ geçitleri, **1 eylül 2020**' de müşteri trafiğini kabul etmeye başlayacaktır:
- Japonya Doğu: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>Ağustos 2020

Yeni SQL ağ geçitleri aşağıdaki bölgelere ekleniyor:

- Avustralya Doğu: 13.70.112.9
- Kanada Orta: 52.246.152.0, 20.38.144.1 
- Batı ABD 2:40.78.240.8

Bu SQL ağ geçitleri, 10 Ağustos 2020 ' de müşteri trafiğini kabul etmeye başlar. 

### <a name="october-2019"></a>Ekim 2019
- Güney Brezilya
- Batı ABD
- West Europe
- Doğu ABD
- Central US
- Güneydoğu Asya
- Orta Güney ABD
- Kuzey Avrupa
- Orta Kuzey ABD
- Batı Japonya
- Doğu Japonya
- Doğu ABD 2
- Doğu Asya

---

## <a name="impact-of-this-change"></a>Bu değişikliğin etkisi

Trafik geçişi, Azure SQL veritabanı 'nda veritabanınız için DNS tarafından çözümlenen genel IP adresini değiştirebilir.
Şu durumlarda etkilenmiş olabilirsiniz:

- Şirket içi güvenlik duvarınızdaki belirli bir ağ geçidinin IP adresini sabit olarak kodlanmış
- Hizmet uç noktası olarak Microsoft. SQL kullanan ancak ağ geçidi IP adresleriyle iletişim kuramayan alt ağlarınız var
- [Genel amaçlı katman için bölge yedekli yapılandırmasını](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) kullanma
- [Premium & iş açısından kritik katmanlar için bölgesel olarak yedekli yapılandırmayı](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) kullanın

Şunları yaptıysanız etkilenmeyecektir:
 
- Bağlantı ilkesi olarak yeniden yönlendirme
- Azure 'un içinden ve hizmet etiketlerini kullanarak SQL veritabanı 'na bağlantı
- SQL Server için JDBC sürücüsünün desteklenen sürümleri kullanılarak yapılan bağlantılar, hiçbir etkisi görmez. Desteklenen JDBC sürümleri için bkz. [SQL Server Için MICROSOFT JDBC sürücüsü indirme](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Etkileniyorsanız yapmanız gerekenler

TCP bağlantı noktası 1433 ve bağlantı noktası aralığı 11000-11999 ' deki bölgedeki tüm [ağ GEÇIDI IP adresleri](connectivity-architecture.md#gateway-ip-addresses) için IP adreslerine giden trafiğe izin vermeniz önerilir. Bu öneri, Şirket içinden bağlanan istemciler ve ayrıca hizmet uç noktaları aracılığıyla bağlanan istemciler için geçerlidir. Bağlantı noktası aralıkları hakkında daha fazla bilgi için bkz. [bağlantı ilkesi](connectivity-architecture.md#connection-policy).

4,0 sürümünün altında Microsoft JDBC sürücüsü kullanılarak gerçekleştirilen uygulamalardan yapılan bağlantılar sertifika doğrulaması başarısız olabilir. Microsoft JDBC 'nın daha düşük sürümleri, sertifikanın konu alanında ortak adı (CN) kullanır. Risk azaltma, Hostnameincercertificate özelliğinin *. database.windows.net olarak ayarlandığından emin olunması. Hostnameincercertificate özelliğinin nasıl ayarlanacağı hakkında daha fazla bilgi için bkz. [şifreleme Ile bağlanma](/sql/connect/jdbc/connecting-with-ssl-encryption).

Yukarıdaki hafifletme işe yaramazsa, aşağıdaki URL 'YI kullanarak SQL veritabanı veya SQL yönetilen örneği için bir destek isteği dosyası kullanın: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL bağlantı mimarisi](connectivity-architecture.md) hakkında daha fazla bilgi edinin