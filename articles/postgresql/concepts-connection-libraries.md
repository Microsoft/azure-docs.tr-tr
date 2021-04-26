---
title: Bağlantı kitaplıkları-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, Connect ve PostgreSQL için Azure veritabanı-tek sunucu sorgulama için uygulama kodlayıp kullanabileceğiniz çeşitli kitaplıklar ve sürücüler açıklanır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: b8a1526605195b5eb24d8044f42b70ca5336bf7c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878317"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı bağlantı kitaplıkları-tek sunucu
Bu makalede, geliştiricilerin PostgreSQL için Azure veritabanı 'na bağlanmak ve veritabanını sorgulamak üzere uygulama geliştirmek için kullanabilecekleri kitaplıklar ve sürücüler listelenmiştir.

## <a name="client-interfaces"></a>İstemci arabirimleri
PostgreSQL'e bağlanmak için kullanılan çoğu dildeki istemci kitaplığı dış kaynaklar tarafından geliştirilir ve bağımsız bir şekilde dağıtılır. Listelenen kitaplıklar, PostgreSQL için Azure veritabanı 'na bağlanmak üzere Windows, Linux ve Mac platformlarında desteklenir. Birkaç hızlı başlangıç örneği, sonraki adımlar bölümünde listelenmiştir.

| **Dil** | **İstemci arabirimi** | **Ek bilgi** | **İndir** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2,0 uyumlu | [İndir](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Veritabanı uzantısı | [Yükleme](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Sayfalık NPM paketi](https://www.npmjs.com/package/pg) | Saf JavaScript engelleyici olmayan istemci | [Yükleme](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Tür 4 JDBC sürücüsü | [İndir](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Sayfalık GED](https://deveiate.org/code/pg/) | Ruby arabirimi | [İndir](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Paket PQ](https://godoc.org/github.com/lib/pq) | Saf go Postgres sürücüsü | [Yükleme](https://github.com/lib/pq/blob/master/README.md) |
| C \# /.net | [Npgsql](https://www.npgsql.org/) | ADO.NET Veri Sağlayıcısı | [İndir](https://dotnet.microsoft.com/download) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC Sürücüsü | [İndir](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Birincil C dil arabirimi | Dahil |
| C++ | [libpqxx](http://pqxx.org/) | Yeni stil C++ arabirimi | [İndir](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Sonraki adımlar
Tercih ettiğiniz dili kullanarak PostgreSQL için Azure veritabanı 'na bağlanma ve sorgulama ile ilgili bu hızlı başlangıçlardan birini okuyun:

[Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Java](./connect-java.md)  |  [Ruby](./connect-ruby.md)  |  [Php](./connect-php.md)  |  [.net (C#)](./connect-csharp.md)  |  [Git](./connect-go.md)
