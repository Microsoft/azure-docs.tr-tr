---
title: Connect ve Query-tek sunuculu PostgreSQL
description: Hızlı başlangıç bağlantıları, PostgreSQL için Azure veritabanınıza tek sunucuya nasıl bağlanacağınızı ve sorguları nasıl çalıştıracağınızı gösterir.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 1506ce311fe443247050a36e1b9fa4600360ac6e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604150"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>PostgreSQL için Azure Veritabanı bağlantı ve sorgulama süreçlerine genel bakış - Tek Sunucu

Aşağıdaki belge, PostgreSQL için Azure veritabanı tek sunucu ile bağlanma ve sorgu oluşturmayı gösteren örneklere bağlantılar içerir. Bu kılavuzda, aşağıdaki desteklenen dillerde sunucusuna bağlanmak için kullanabileceğiniz TLS önerileri ve uzantısı da bulunur.

## <a name="quickstarts"></a>Hızlı Başlangıçlar

| Hızlı Başlangıç | Description |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|Sunucuya bağlanmak için pgAdmin kullanabilirsiniz ve veritabanı nesnelerinin oluşturulmasını, bakımını ve kullanımını basitleştirir.|
|[Azure Cloud Shell 'de psql](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|Bu makalede, sunucunuzdaki verileri sorgulamak, eklemek, güncelleştirmek ve silmek için [Azure Cloud Shell](../cloud-shell/overview.md) [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) 'in nasıl çalıştırılacağı gösterilmektedir. Geliştirme ortamınızda yüklüyse **psql** 'i çalıştırabilirsiniz|
|[VS Code ile PostgreSQL](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|VS Code için Azure veritabanları uzantısı (Önizleme), hem yerel olarak hem de zengin IntelliSense ile Scrapbook 'ları kullanarak PostgreSQL sunucunuza hem yerel olarak hem de bulutta geçiş yapmanıza olanak sağlar. |
|[PHP](connect-php.md)|Bu hızlı başlangıçta PHP kullanarak bir veritabanına bağlanma ve verileri sorgulamak için veritabanı nesneleriyle çalışma kullanma gibi bir program oluşturma işlemlerinin nasıl yapılacağı gösterilmiştir.|
|[Java](connect-java.md)|Bu hızlı başlangıçta Java kullanarak bir veritabanına bağlanma ve sonra verileri sorgulamak için veritabanı nesneleriyle çalışma kullanma işlemlerinin nasıl yapılacağı gösterilmiştir.|
|[Node.js](connect-nodejs.md)|Bu hızlı başlangıçta, bir veritabanına bağlanmak ve veri sorgulamak için veritabanı nesneleriyle çalışma kullanmak üzere bir program oluşturmak üzere Node.js nasıl kullanılacağı gösterilmektedir.|
|[.NET (C#)](connect-csharp.md)|Bu hızlı başlangıçta, bir veritabanına bağlanmak ve veri sorgulamak için veritabanı nesneleriyle çalışma kullanmak üzere C# programı oluşturmak üzere nasıl use.NET (C#) yapılacağı gösterilmektedir.|
|[Git](connect-go.md)|Bu hızlı başlangıçta Go kullanarak bir veritabanına nasıl bağlanabileceğinizi gösterir. Verileri sorgulamak ve değiştirmek için Transact-SQL bildirimleri de gösterilir.|
|[Python](connect-python.md)|Bu hızlı başlangıçta Python kullanarak bir veritabanına bağlanma ve verileri sorgulamak için veritabanı nesneleriyle çalışma kullanma işlemlerinin nasıl yapılacağı gösterilmiştir. |
|[Ruby](connect-ruby.md)|Bu hızlı başlangıçta, Ruby kullanarak bir veritabanına bağlanma ve verileri sorgulamak için veritabanı nesneleriyle çalışma kullanma adlı bir program oluşturma işlemlerinin nasıl yapılacağı gösterilmiştir.|

## <a name="tls-considerations-for-database-connectivity"></a>Veritabanı bağlantısı için TLS konuları

Aktarım Katmanı Güvenliği (TLS), Microsoft 'un PostgreSQL için Azure veritabanı 'nda veritabanlarına bağlanmak için sağladığı veya desteklediği tüm sürücüler tarafından kullanılır. Özel yapılandırma gerekmez, ancak yeni oluşturulan sunucular için TLS 1,2 ' i zorunlu tutun. TLS 1,0 ve 1,1 kullanıyorsanız, sunucularınızın TLS sürümünü güncelleştirmeniz önerilir. Bkz. [TLS Yapılandırma](howto-tls-configurations.md)

## <a name="postgresql-extensions"></a>PostgreSQL uzantıları

PostgreSQL, uzantıları kullanarak veritabanınızın işlevselliğini genişletmenizi sağlar. Uzantılar birden çok ilgili SQL nesnesini tek bir komutla veritabanınıza yüklenip kaldırılabilecek bir paket haline getirir. Veritabanınıza yüklenen uzantılar, yerleşik özellikler gibi çalışır.

- [Postgres 11 uzantıları](./concepts-extensions.md#postgres-11-extensions)
- [Postgres 10 uzantıları](./concepts-extensions.md#postgres-10-extensions)
- [Postgres 9,6 uzantıları](./concepts-extensions.md#postgres-96-extensions)

Daha fazla ayrıntı için bkz. [tek sunucuda PostgreSQL uzantılarını kullanma](concepts-extensions.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Döküm ve geri yükleme kullanarak verileri geçirme](howto-migrate-using-dump-and-restore.md)
- [İçeri ve dışarı aktarma kullanarak verileri geçirme](howto-migrate-using-export-and-import.md)
