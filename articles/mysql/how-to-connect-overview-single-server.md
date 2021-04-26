---
title: Connect ve Query-tek sunuculu MySQL
description: Hızlı başlangıç bağlantıları, Azure SQL veritabanı tek sunucunuza nasıl bağlanacağınızı ve sorguları nasıl çalıştıracağınızı gösterir.
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 0414aaad5a1cf6edb9c2152eed70f8753946cca1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92546441"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>MySQL için Azure veritabanı 'na bağlanma ve sorguya genel bakış-tek sunucu

Aşağıdaki belge, MySQL tek sunucusu için Azure veritabanı ile bağlanma ve sorgu oluşturmayı gösteren örneklere bağlantılar içerir. Bu kılavuz Ayrıca, aşağıdaki desteklenen dillerde sunucuya bağlanmak için kullanabileceğiniz TLS önerilerini ve kitaplıklarını da içerir.

## <a name="quickstarts"></a>Hızlı Başlangıçlar

| Hızlı Başlangıç | Description |
|---|---|
|[MySQL çalışma ekranı](connect-workbench.md)|Bu hızlı başlangıçta, MySQL çalışma Istemcisi 'nin bir veritabanına bağlanmak için nasıl kullanılacağı gösterilmektedir. Daha sonra MySQL deyimlerini kullanarak veritabanındaki verileri sorgulama, ekleme, güncelleştirme ve silme yapabilirsiniz.|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|Bu makalede, sunucunuza bağlanmak ve sonra verileri sorgulamak, eklemek, güncelleştirmek ve silmek için deyimlerini çalıştırmak üzere [Azure Cloud Shell](../cloud-shell/overview.md) **mysql.exe** nasıl çalıştırılacağı gösterilmektedir.|
|[Visual Studio ile MySQL](https://www.mysql.com/why-mysql/windows/visualstudio)|MySQL sunucunuza bağlanmak için Visual Studio için MySQL kullanabilirsiniz. Visual Studio için MySQL, yeni bağlantıların kurulumunu ve veritabanı nesneleriyle çalışmayı kolaylaştıran Sunucu Gezgini doğrudan tümleşir.|
|[PHP](connect-php.md)|Bu hızlı başlangıçta, PHP kullanarak bir veritabanına bağlanıp verileri sorgulamak için bir program oluşturma ve MySQL deyimlerini kullanma gösterilmektedir.|
|[Java](connect-java.md)|Bu hızlı başlangıçta Java kullanarak bir veritabanına bağlanma ve sonra verileri sorgulamak için MySQL deyimlerini kullanma işlemlerinin nasıl yapılacağı gösterilmiştir.|
|[Node.js](connect-nodejs.md)|Bu hızlı başlangıçta, bir veritabanına bağlanmak ve MySQL deyimlerini kullanarak verileri sorgulamak için bir program oluşturmak üzere Node.js nasıl kullanılacağı gösterilmektedir.|
|[.NET (C#)](connect-csharp.md)|Bu hızlı başlangıçta, bir veritabanına bağlanmak ve MySQL deyimlerini kullanarak verileri sorgulamak için C# programı oluşturma konusunda nasıl use.NET (C#) yapılacağı gösterilmektedir.|
|[Git](connect-go.md)|Bu hızlı başlangıçta Go kullanarak bir veritabanına nasıl bağlanabileceğinizi gösterir. Verileri sorgulamak ve değiştirmek için Transact-SQL bildirimleri de gösterilir.|
|[Python](connect-python.md)|Bu hızlı başlangıçta Python kullanarak veritabanına bağlanma ve MySQL deyimlerini kullanarak verileri sorgulama işlemlerinin nasıl yapılacağı gösterilmiştir. |
|[Ruby](connect-ruby.md)|Bu hızlı başlangıçta, Ruby kullanarak bir veritabanına bağlanıp veri sorgulamak için MySQL deyimlerini kullanarak bir program oluşturma gösterilmektedir.|
|[C++](connect-cpp.md)|Bu hızlı başlangıçta, bir veritabanına bağlanmak ve sorgu verilerini kullanmak üzere bir program oluşturmak için C++ + kullanımı gösterilmektedir.|

## <a name="tls-considerations-for-database-connectivity"></a>Veritabanı bağlantısı için TLS konuları

Aktarım Katmanı Güvenliği (TLS), Microsoft 'un MySQL için Azure veritabanı 'nda veritabanlarına bağlanmak için sağladığı veya desteklediği tüm sürücüler tarafından kullanılır. Özel yapılandırma gerekmez, ancak yeni oluşturulan sunucular için TLS 1,2 ' i zorunlu tutun. TLS 1,0 ve 1,1 kullanıyorsanız, sunucularınızın TLS sürümünü güncelleştirmeniz önerilir. Bkz. [TLS Yapılandırma](howto-tls-configurations.md)

## <a name="libraries"></a>Kitaplıklar

MySQL için Azure veritabanı, dünyanın en popüler MySQL veritabanı Community sürümünü kullanır. Bu nedenle, çok çeşitli programlama dilleri ve sürücüleriyle uyumludur. Amaç, en son üç sürüm MySQL sürücüsünü destekliyoruz ve MySQL sürücülerinin işlevselliğini ve kullanılabilirliğini sürekli olarak geliştirmek üzere açık kaynaklı topluluktaki yazarlarla ilgili çalışmalar devam eder.

MySQL tek sunucusu için Azure veritabanı ile hangi [sürücülerin](concepts-compatibility.md) uyumlu olduğunu görün.

## <a name="next-steps"></a>Sonraki adımlar

- [Döküm ve geri yükleme kullanarak verileri geçirme](concepts-migrate-dump-restore.md)
- [İçeri ve dışarı aktarma kullanarak verileri geçirme](concepts-migrate-import-export.md)