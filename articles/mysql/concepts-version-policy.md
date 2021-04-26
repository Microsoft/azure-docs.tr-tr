---
title: Sürüm destek ilkesi-MySQL için Azure veritabanı-tek sunucu ve esnek sunucu (Önizleme)
description: MySQL için Azure veritabanı 'nda MySQL 'in büyük ve küçük sürümlerinin bulunduğu ilkeyi açıklar
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 49e59c43e9eaedf770b1a8e052dd73aa331d31ce
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389579"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>MySQL için Azure veritabanı sürüm desteği ilkesi

Bu sayfada MySQL için Azure veritabanı sürüm oluşturma ilkesi açıklanmakta ve MySQL için Azure veritabanı-tek sunucu ve MySQL için Azure veritabanı-esnek sunucu (Önizleme) Dağıtım modları için geçerlidir.

## <a name="supported--mysql-versions"></a>Desteklenen MySQL sürümleri

MySQL için Azure veritabanı, InnoDB depolama altyapısı kullanılarak [MySQL Community Edition](https://www.mysql.com/products/community/)'dan geliştirilmiştir. Hizmet, topluluk tarafından desteklenen tüm geçerli ana sürümü destekler, burada MySQL 5,6, 5,7 ve 8,0. MySQL, X 'in ana sürüm olan X. Y. Z adlandırma şemasını kullanır, Y ise ikincil sürümdür ve Z hata çözme sürümüdür. Düzen hakkında daha fazla bilgi için [MySQL belgelerine](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)bakın.

MySQL için Azure veritabanı şu anda MySQL 'in aşağıdaki büyük ve küçük sürümlerini desteklemektedir:

| Sürüm | [Tek sunucu](overview.md) <br/> Geçerli ikincil sürüm |[Esnek Sunucu (Önizleme)](/azure/mysql/flexible-server/overview) <br/> Geçerli ikincil sürüm  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL sürüm 5,6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(kullanımdan kaldırıldı) | Desteklenmez|
|MySQL sürüm 5,7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL sürüm 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> Tek sunuculu dağıtım seçeneğinde, sunucu örneklerine bağlantıları yeniden yönlendirmek için bir ağ geçidi kullanılır. Bağlantı kurulduktan sonra, MySQL istemcisi MySQL sunucu örneğinizde çalışan gerçek sürümü değil ağ geçidinde belirlenen MySQL sürümünü görüntüler. MySQL sunucu örneğinizin sürümünü öğrenmek için MySQL komut isteminde `SELECT VERSION();` komutunu kullanın. Uygulamanızın belirli ana sürüme bağlanmak için v 5.7 veya v 8.0 gereksinimi varsa, buradaki belgelerimizde açıklandığı gibi sunucu bağlantı dizinizdeki bağlantı noktasını değiştirerek bunu yapabilirsiniz [.](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version)

> [!IMPORTANT]
> MySQL v 5.6, Febuary 2021 itibariyle tek bir sunucuda kullanımdan kaldırıldı. 1 Eylül 2021 ' den başlayarak, MySQL için Azure veritabanı-tek sunucu dağıtım seçeneğinde yeni v 5.6 sunucuları oluşturabilemeyeceksiniz. Bununla birlikte, mevcut sunucularınız için zaman içinde kurtarmalar gerçekleştirebilir ve okuma çoğaltmaları oluşturabilirsiniz.

[Sürüm desteği ilkesi belgelerindeki](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql) kullanımdan kaldırılan sürümler için sürüm desteği ilkesini okuyun.

## <a name="major-version-support"></a>Ana sürüm desteği
MySQL için Azure veritabanı, sürüm, sürüm [oluşturma ilkesinde](https://www.mysql.com/support/eol-notice.html)sağlandığı gibi MySQL Community tarafından devre dışı bırakılana kadar, MySQL Için Azure veritabanı tarafından desteklenecek.

## <a name="minor-version-support"></a>İkincil sürüm desteği
MySQL için Azure veritabanı, düzenli bakımın parçası olarak Azure tercih edilen MySQL sürümüne küçük sürüm yükseltmeleri otomatik olarak gerçekleştirir. 

## <a name="major-version-retirement-policy"></a>Ana sürüm kullanımdan kaldırma ilkesi
Aşağıdaki tabloda MySQL ana sürümleri için kullanımdan kaldırma ayrıntıları verilmiştir. Tarihler [MySQL sürüm oluşturma ilkesini](https://www.mysql.com/support/eol-notice.html)izler.

| Sürüm | Yenilikler | Azure desteği Başlangıç tarihi | Emeklilik tarihi|
| ----- | ----- | ------ | ----- |
| [MySQL 5,6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Özellikler](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 Mart 2018 | Şubat 2021
| [MySQL 5,7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Özellikler](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 Mart 2018 | 2023 Ekim
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Özellikler](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11 Aralık 2019 | 2026 Nisan


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Kullanımdan kaldırılan MySQL altyapısı sürümleri MySQL için Azure veritabanı 'nda desteklenmiyor

Her MySQL veritabanı sürümü için kullanımdan kaldırma tarihinden sonra, kullanımdan kaldırılan sürümü çalıştırmaya devam ederseniz aşağıdaki kısıtlamalara göz önünde bulabilirsiniz:
- Topluluk başka hata düzeltmeleri veya güvenlik düzeltmelerinin serbest bırakılmayacak, MySQL için Azure veritabanı, herhangi bir hata veya güvenlik sorunu için kullanımdan kaldırılan veritabanı altyapısına düzeltme eki uygulanmaz veya kullanımdan kaldırılan veritabanı altyapısından kaynaklanan güvenlik önlemleri almaz. Ancak Azure, ana bilgisayar, işletim sistemi, kapsayıcılar ve hizmetle ilgili diğer tüm bileşenler için düzenli bakım ve düzeltme eki gerçekleştirmeye devam edecektir.
- Karşılaşabileceğiniz herhangi bir destek sorunu MySQL veritabanıyla ilgili olarak size destek sunamayacak olabilir. Bu gibi durumlarda, size herhangi bir destek sağlayabilmesi için veritabanınızı yükseltmeniz gerekir.
- Kullanımdan kaldırılan sürüm için yeni veritabanı sunucuları oluşturabileceksiniz. Bununla birlikte, mevcut sunucularınız için zaman içinde kurtarmalar gerçekleştirebilir ve okuma çoğaltmaları oluşturabilirsiniz.
- MySQL için Azure veritabanı tarafından geliştirilen yeni hizmet özellikleri yalnızca desteklenen veritabanı sunucusu sürümleri için kullanılabilir olabilir.
- Çalışma süresi SLA 'Ları yalnızca MySQL hizmeti ile ilgili sorunlar için Azure veritabanı 'na, veritabanı altyapısından kaynaklanan hataların neden olması için geçerlidir.  
- Kullanımdan kaldırılmış olan veritabanı sürümünde tanımlanan MySQL veritabanı altyapısı güvenlik açığından kaynaklanan, hizmette ciddi bir tehdit olması durumunda Azure, önce hizmeti güvenli hale getirmek için veritabanı sunucunuzun işlem düğümünü durdurmayı tercih edebilir. Sunucuyu çevrimiçi duruma getirmeden önce sunucuyu yükseltmeniz istenir. Yükseltme işlemi sırasında, verileriniz her zaman, istenirse eski sürüme geri yüklemek için kullanılabilecek otomatik yedeklemeler kullanılarak korunur. 



## <a name="next-steps"></a>Sonraki adımlar
- Bkz. MySQL için Azure veritabanı-tek sunuculu [Desteklenen sürümler](./concepts-supported-versions.md)
- Bkz. MySQL için Azure veritabanı-esnek sunucu (Önizleme) [Desteklenen sürümler](flexible-server/concepts-supported-versions.md)
- Yükseltmeleri gerçekleştirmek için MySQL [dökümünü ve geri yüklemeyi](./concepts-migrate-dump-restore.md) inceleyin.
