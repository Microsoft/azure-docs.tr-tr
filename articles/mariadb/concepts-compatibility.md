---
title: Sürücüler ve araçlar uyumluluğu-MariaDB için Azure veritabanı
description: Bu makalede, MariaDB için Azure veritabanı ile uyumlu olan MariaDB sürücüleri ve yönetim araçları açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 49fd2c8da3ba0a44f4901bc6c7a2241e9e20df4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98659878"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB sürücüleri ve yönetim araçları MariaDB için Azure veritabanı ile uyumlu

Bu makalede, MariaDB için Azure veritabanı ile uyumlu olan sürücüler ve yönetim araçları açıklanmaktadır.

## <a name="mariadb-drivers"></a>MariaDB sürücüleri

MariaDB için Azure veritabanı, MariaDB sunucusunun Community sürümünü kullanır. Bu nedenle, çok çeşitli programlama dilleri ve sürücüleriyle uyumludur. MariaDB 'nin API 'SI ve Protokolü MySQL tarafından kullanılanlarla uyumludur. Bu, MySQL ile çalışan bağlayıcıların de MariaDB ile çalışması gerektiği anlamına gelir.

Amaç, en son üç sürümü MariaDB sürücülerini destekliyoruz ve MariaDB sürücülerinin işlevlerini ve kullanılabilirliğini sürekli olarak geliştirmek üzere açık kaynaklı topluluktaki yazarlarla ilgili çalışmalar devam eder. Aşağıdaki tabloda, test edilmiş ve MariaDB 10,2 için Azure veritabanı ile uyumlu olarak bulunan sürücülerin bir listesi verilmiştir:

**Sürücü** | **Bağlantılar** | **Uyumlu sürümler** | **Uyumsuz sürümler** | **Notlar**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5.3 | SSL ile PHP 7,0 bağlantısı için, bağlantı dizesinde MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT ekleyin. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` seçenek false.
.NET | [GitHub 'da MySqlConnector](https://github.com/mysql-net/MySqlConnector) <br> [NuGet 'den yükleme paketi](https://www.nuget.org/packages/MySqlConnector/) | 0,27 ve sonrası | 0.26.5 ve öncesi |
MySQL Bağlayıcısı/ağı | [MySQL Bağlayıcısı/ağı](https://github.com/mysql/mysql-connector-net) | 8,0, 7,0, 6,10 |  | Bir kodlama hatası, bazı UTF8 olmayan Windows sistemlerinde bağlantıların başarısız olmasına neden olabilir.
Node.js |  [GitHub üzerinde MySQLjs](https://github.com/mysqljs/mysql/) <br> NPM 'den yükleme paketi:<br> `npm install mysql`NPM 'den Çalıştır | 2.15 | 2.14.1 ve öncesi
GO | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 ve öncesi | `allowNativePasswords=true`Sürüm 1,3 için bağlantı dizesinde kullanın. Sürüm 1,4, bir çözüm içerir ve `allowNativePasswords=true` artık gerekli değildir.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2 | 1.2.2 ve öncesi |
Java | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 ve öncesi |

## <a name="management-tools"></a>Yönetim Araçları

Uyumluluk avantajı veritabanı yönetim araçlarına da genişletilir. Veritabanı düzenlemesi Kullanıcı izinlerinin sınırlandırmaları içinde çalıştığı sürece mevcut araçlarınız, MariaDB için Azure veritabanı ile çalışmaya devam etmelidir. Daha önce sınanmış ve MariaDB 10,2 için Azure veritabanı ile uyumlu olmak üzere bulunan üç ortak veritabanı yönetim aracı aşağıdaki tabloda listelenmiştir:

| Eylem | **MySQL çalışma ekranı 6. x ve yukarı** | **Navicat 12** | **PHPMyAdmin 4. x ve yukarı**
---|---|---|---
Oluşturma, güncelleştirme, okuma, yazma, silme | X | X | X
SSL bağlantısı | X | X | X
SQL sorgu otomatik tamamlama | X | X |
Verileri içeri ve dışarı aktarma | X | X | X
Birden çok biçime aktar | X | X | X
Yedekleme ve Geri Yükleme |  | X |
Sunucu parametrelerini görüntüle | X | X | X
Istemci bağlantılarını görüntüle | X | X | X

## <a name="next-steps"></a>Sonraki adımlar

- [MariaDB için Azure Veritabanı bağlantı sorunlarını giderme](howto-troubleshoot-common-connection-issues.md)