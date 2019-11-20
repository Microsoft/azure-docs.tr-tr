---
title: MySQL için Azure veritabanı sürücüleri ve yönetim araçları uyumluluğu
description: Bu makalede MySQL için Azure veritabanı ile uyumlu MySQL sürücüleri ve yönetim araçları açıklanmaktadır.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/06/2019
ms.openlocfilehash: 916c02c30f6d54aef44459775a7a437fe96a4ff9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720144"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL için Azure veritabanı ile uyumlu MySQL sürücüleri ve yönetim araçları
Bu makalede, MySQL için Azure veritabanı ile uyumlu olan sürücüler ve yönetim araçları açıklanmaktadır.

## <a name="mysql-drivers"></a>MySQL sürücüleri
MySQL için Azure veritabanı, dünyanın en popüler MySQL veritabanı Community sürümünü kullanır. Bu nedenle, çok çeşitli programlama dilleri ve sürücüleriyle uyumludur. Amaç, en son üç sürüm MySQL sürücüsünü destekliyoruz ve MySQL sürücülerinin işlevselliğini ve kullanılabilirliğini sürekli olarak geliştirmek üzere açık kaynaklı topluluktaki yazarlarla ilgili çalışmalar devam eder. Test edilmiş ve MySQL için Azure veritabanı 5,6 ve 5,7 ile uyumlu olacak şekilde bulunan sürücülerin listesi aşağıdaki tabloda verilmiştir:

| **Programlama dili** | **Aygıt** | **Köprü** | **Uyumlu sürümler** | **Uyumsuz sürümler** | **Notlar** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, PDO_MYSQL, mysqlnd | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5,3 | SSL ile PHP 7,0 bağlantısı için, bağlantı dizesinde MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT ekleyin. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO kümesi: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` seçeneği false.|
| .NET | .NET için Async MySQL Bağlayıcısı | https://github.com/mysql-net/MySqlConnector <br> [NuGet 'den yükleme paketi](https://www.nuget.org/packages/MySqlConnector/) | 0,27 ve sonrası | 0.26.5 ve öncesi | |
| .NET | MySQL Bağlayıcısı/ağı | https://github.com/mysql/mysql-connector-net | 8,0, 7,0, 6,10 |  | Bir kodlama hatası, bazı UTF8 olmayan Windows sistemlerinde bağlantıların başarısız olmasına neden olabilir. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> NPM 'den yükleme paketi:<br> NPM 'den `npm install mysql` Çalıştır | 2,15 | 2.14.1 ve öncesi | |
| Node.js | düğüm-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | MySQL sürücüsüne git | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 ve öncesi | Sürüm 1,3 için bağlantı dizesinde `allowNativePasswords=true` kullanın. Sürüm 1,4 bir çözüm içerir ve `allowNativePasswords=true` artık gerekli değildir. |
| Python | MySQL Bağlayıcısı/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2, MySQL 8,0 ile 8.0.16 + kullanın  | 1.2.2 ve öncesi | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3 + | 0.9.0-0.9.2 (web2py 'de gerileme) | |
| Java | MariaDB Bağlayıcısı/J | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 ve öncesi | | 
| Java | MySQL Bağlayıcısı/J | https://github.com/mysql/mysql-connector-j | 5.1.20 +, MySQL 8,0 ile 8.0.17 + kullanın | 5.1.9 ve altı | |
| C | MySQL Bağlayıcısı/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | MySQL Bağlayıcısı/ODBC (MyODBC) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | MySQL Bağlayıcısı/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3 ve altı | | 
| C++ | MySQL + +| https://tangentsoft.net/mysql++ | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | MySQL-Swift | https://github.com/novi/mysql-swift | 0.7.2 + | | |
| Swift | Vapor/MySQL | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>Yönetim Araçları
Uyumluluk avantajı veritabanı yönetim araçlarına da genişletilir. Veritabanı düzenlemesi Kullanıcı izinlerinin sınırlandırmaları içinde çalıştığı sürece mevcut araçlarınız MySQL için Azure veritabanı ile çalışmaya devam etmelidir. Test edilmiş ve MySQL için Azure veritabanı 5,6 ve 5,7 ile uyumlu olmak üzere bulunan üç ortak veritabanı yönetim aracı aşağıdaki tabloda listelenmiştir:

|                                     | **MySQL çalışma ekranı 6. x ve yukarı** | **Navicat 12** | **PHPMyAdmin 4. x ve yukarı** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Oluşturma, güncelleştirme, okuma, yazma, silme | X | X | X |
| SSL bağlantısı | X | X | X |
| SQL sorgu otomatik tamamlama | X | X |  |
| Verileri içeri ve dışarı aktarma | X | X | X | 
| Birden çok biçime aktar | X | X | X |
| Yedekleme ve Geri Yükleme |  | X |  |
| Sunucu parametrelerini görüntüle | X | X | X |
| Istemci bağlantılarını görüntüle | X | X | X |

## <a name="next-steps"></a>Sonraki adımlar

- [MySQL için Azure Veritabanı bağlantı sorunlarını giderme](howto-troubleshoot-common-connection-issues.md)