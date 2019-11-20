---
title: Ağ Geçidi trafiği geçiş bildirimi
description: Makale, kullanıcılara Azure SQL veritabanı ağ geçitleri IP adreslerini geçirme hakkında bildirim sağlar
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: fe35dc4c22f3852934cde0d6f33084b56266d514
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73807698"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL veritabanı trafiğini daha yeni ağ geçitlerine geçirme

Azure altyapısı gelişdiğinde, Microsoft, olası en iyi müşteri deneyimini sağlamamızı sağlamak için donanımı düzenli olarak yenileyecek. Önümüzdeki aylarda, daha yeni donanım oluşturma, trafiği geçirme ve son olarak bazı bölgelerde daha eski donanımlar üzerinde oluşturulmuş ağ geçitlerinin yetkisini alma planlıyoruz.  

Müşteriler, e-posta ile ve her bölgede kullanılabilir olan ağ geçitlerinde yapılan herhangi bir değişikliğe göre Azure portal iyi bir şekilde bildirilir. En güncel bilgiler [Azure SQL veritabanı ağ GEÇIDI IP adresleri](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) tablosunda tutulacaktır.

## <a name="impact-of-this-change"></a>Bu değişikliğin etkisi

Trafik geçişinin yeni ağ geçitlerine ilk **turu, aşağıdaki bölgelerde 14 ekim 2019** için zamanlanır:
- Güney Brezilya
- Batı ABD
- Batı Avrupa
- Doğu ABD
- Orta ABD
- Güneydoğu Asya
- Orta Güney ABD
- Kuzey Avrupa
- Orta Kuzey ABD
- Japonya Batı
- Japonya Doğu
- Doğu ABD 2
- Doğu Asya

Trafik geçişi, DNS 'nin SQL veritabanınız için çözümlediği genel IP adresini değiştirecek.
Şu durumlarda etkilenirsiniz:
- Şirket içi güvenlik duvarınızdaki belirli bir ağ geçidinin IP adresini sabit olarak kodlanmış
- Hizmet uç noktası olarak Microsoft. SQL kullanan tüm alt ağlar, ancak ağ geçidi IP adresleriyle iletişim kuramaz

Şunları yaptıysanız etkilenmeyecektir:
- Bağlantı ilkesi olarak yeniden yönlendirme
- Azure 'un içinden ve hizmet etiketlerini kullanarak SQL veritabanı 'na bağlantı
- SQL Server için JDBC sürücüsünün desteklenen sürümleri kullanılarak yapılan bağlantılar, hiçbir etkisi görmez. Desteklenen JDBC sürümleri için bkz. [SQL Server Için MICROSOFT JDBC sürücüsü indirme](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Etkileniyorsanız yapmanız gerekenler

TCP bağlantı noktası 1433 ' deki bölgedeki tüm [Azure SQL veritabanı ağ GEÇIDI IP adresleri](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) ve 11000-11999 bağlantı noktası ARALıĞı için IP adreslerine giden trafiğe izin vermeniz önerilir. Bu öneri, Şirket içinden bağlanan istemciler ve ayrıca hizmet uç noktaları aracılığıyla bağlanan istemciler için geçerlidir. Bağlantı noktası aralıkları hakkında daha fazla bilgi için bkz. [bağlantı ilkesi](sql-database-connectivity-architecture.md#connection-policy).

4,0 sürümünün altında Microsoft JDBC sürücüsü kullanılarak gerçekleştirilen uygulamalardan yapılan bağlantılar sertifika doğrulaması başarısız olabilir. Microsoft JDBC 'nın daha düşük sürümleri, sertifikanın konu alanında ortak adı (CN) kullanır. Risk azaltma, Hostnameincercertificate özelliğinin *. database.windows.net olarak ayarlandığından emin olunması. Hostnameincercertificate özelliğinin nasıl ayarlanacağı hakkında daha fazla bilgi için bkz. [SSL şifrelemesiyle bağlanma](/sql/connect/jdbc/connecting-with-ssl-encryption).

Yukarıdaki hafifletme işe yaramazsa, aşağıdaki URL 'YI kullanarak SQL veritabanı için bir destek isteği dosyası kullanın: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL bağlantı mimarisi](sql-database-connectivity-architecture.md) hakkında daha fazla bilgi edinin
