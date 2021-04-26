---
title: Bağlanma ve sorgulama
description: Azure SQL veritabanı hızlı başlangıçlara bağlanıp Azure SQL veritabanı ve Azure SQL yönetilen örneği 'ne nasıl bağlanılacağını ve sorgu yapılacağını gösteren bağlantılar.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/17/2021
ms.openlocfilehash: 96b52c80fa06be8c157ad39fd65be4e491e0cbe3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874864"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>Azure SQL veritabanı ve Azure SQL yönetilen örnek Connect ve sorgu makaleleri
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Aşağıdaki belge, Azure SQL veritabanı ve Azure SQL yönetilen örneği ile bağlanma ve sorgulama işlemlerinin nasıl yapılacağını gösteren Azure örneklerinin bağlantılarını içerir. Aktarım düzeyi güvenliği ile ilgili bazı öneriler için bkz. [veritabanı bağlantısı Için TLS konuları](#tls-considerations-for-database-connectivity).

## <a name="quickstarts"></a>Hızlı Başlangıçlar

| Hızlı Başlangıç | Description |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|Bu hızlı başlangıçta SSMS kullanarak bir veritabanına bağlanma ve ardından Transact-SQL deyimlerini kullanarak veritabanındaki verileri sorgulama, ekleme, güncelleştirme ve silme işlemlerinin nasıl yapılacağı gösterilmiştir.|
|[Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)|Bu hızlı başlangıçta, bir veritabanına bağlanmak için Azure Data Studio kullanma ve ardından Transact-SQL (T-SQL) deyimlerini kullanarak Azure Data Studio öğreticilerde kullanılan Tutorialdb 'yi oluşturma gösterilmektedir.|
|[Azure portalı](connect-query-portal.md)|Bu hızlı başlangıçta sorgu Düzenleyicisi 'ni kullanarak bir veritabanına bağlanma (yalnızca Azure SQL veritabanı) ve ardından Transact-SQL deyimlerini kullanarak veritabanındaki verileri sorgulama, ekleme, güncelleştirme ve silme işlemlerinin nasıl yapılacağı gösterilmiştir.|
|[Visual Studio Code](connect-query-vscode.md)|Bu hızlı başlangıçta, bir veritabanına bağlanmak için Visual Studio Code kullanma ve ardından Transact-SQL deyimlerini kullanarak veritabanındaki verileri sorgulama, ekleme, güncelleştirme ve silme işlemlerinin nasıl yapılacağı gösterilmiştir.|
|[Visual Studio ile .NET](connect-query-dotnet-visual-studio.md)|Bu hızlı başlangıçta, .NET Framework kullanarak bir veritabanına bağlanma ve Transact-SQL deyimleriyle veri sorgulayan bir C# programı oluşturma işlemlerinin nasıl yapılacağı gösterilmiştir.|
|[.NET Core](connect-query-dotnet-core.md)|Bu hızlı başlangıçta, Windows/Linux/macOS 'ta .NET Core kullanarak bir veritabanına bağlanma ve Transact-SQL deyimleriyle veri sorgulama işlemlerinin nasıl yapılacağı gösterilmiştir.|
|[Git](connect-query-go.md)|Bu hızlı başlangıçta Go kullanarak bir veritabanına nasıl bağlanabileceğinizi gösterir. Verileri sorgulamak ve değiştirmek için Transact-SQL bildirimleri de gösterilir.|
|[Java](connect-query-java.md)|Bu hızlı başlangıçta Java kullanarak bir veritabanına bağlanma ve ardından Transact-SQL deyimleriyle veri sorgulama işlemlerinin nasıl yapılacağı gösterilmiştir.|
|[Node.js](connect-query-nodejs.md)|Bu hızlı başlangıçta, bir veritabanına bağlanmak ve Transact-SQL deyimleriyle veri sorgulamak için bir program oluşturmak üzere Node.js nasıl kullanılacağı gösterilmektedir.|
|[PHP](connect-query-php.md)|Bu hızlı başlangıçta PHP kullanarak bir veritabanına bağlanma ve Transact-SQL deyimleriyle veri sorgulayan bir program oluşturma işlemlerinin nasıl yapılacağı gösterilmiştir.|
|[Python](connect-query-python.md)|Bu hızlı başlangıçta Python kullanarak bir veritabanına bağlanma ve Transact-SQL deyimleriyle veri sorgulama işlemlerinin nasıl yapılacağı gösterilmiştir. |
|[Ruby](connect-query-ruby.md)|Bu hızlı başlangıçta, Ruby kullanarak veritabanına bağlanan ve Transact-SQL deyimleriyle veri sorgulayan bir program oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.|
|||

## <a name="get-server-connection-information"></a>Sunucu bağlantı bilgilerini al

Azure SQL veritabanı 'nda veritabanına bağlanmak için gereken bağlantı bilgilerini alın. Yaklaşan yordamlar için tam sunucu adı veya ana bilgisayar adı, veritabanı adı ve oturum açma bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **SQL veritabanları** veya **SQL yönetilen örnekler** sayfasına gidin.

3. **Genel bakış** sayfasında, Azure SQL veritabanı 'nda veritabanı için **sunucu adı** ' nın yanında tam sunucu adını veya Azure SQL yönetilen örneği için **konak** ' ın YANıNDAKI tam sunucu adını (veya IP adresini) veya Azure VM 'de SQL Server ' yi gözden geçirin. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin.

> [!NOTE]
> Azure VM 'de SQL Server yönelik bağlantı bilgileri için bkz. [SQL Server örneğine bağlanma](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>ADO.NET bağlantı bilgilerini al (isteğe bağlı-yalnızca SQL veritabanı)

1. Azure portal veritabanı dikey penceresine gidin ve **Ayarlar** altında **bağlantı dizeleri**' ni seçin.

2. Tam **ADO.NET** bağlantı dizesini gözden geçirin.

    ![ADO.NET bağlantı dizesi](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Kullanmak istiyorsanız **ADO.net** bağlantı dizesini kopyalayın.

## <a name="tls-considerations-for-database-connectivity"></a>Veritabanı bağlantısı için TLS konuları

Aktarım Katmanı Güvenliği (TLS), Microsoft 'un Azure SQL veritabanı veya Azure SQL yönetilen örneğindeki veritabanlarına bağlanmak için sağladığı veya desteklediği tüm sürücüler tarafından kullanılır. Özel yapılandırma gerekli değildir. Bir SQL Server örneğine, Azure SQL veritabanı 'na veya Azure SQL yönetilen örneği örneğine yönelik tüm bağlantılar için, tüm uygulamaların aşağıdaki konfigürasyonları veya bunların eşdeğerleri ayarlaması önerilir:

- **Encrypt = On**
- **TrustServerCertificate = Off**

Bazı sistemler bu yapılandırma anahtar sözcükleri için farklı ancak eşdeğer anahtar sözcükler kullanmaktadır. Bu yapılandırmalar istemci sürücüsünün sunucudan alınan TLS sertifikası kimliğini doğrulamasını sağlamaktadır.

Ayrıca Ödeme Kartı Endüstrisi - Veri Güvenliği Standardı’na (PCI-DSS) uymanız gerekiyorsa istemcide TLS 1.1 ve 1.0’ı devre dışı bırakmanızı öneririz.

Microsoft olmayan sürücüler varsayılan olarak TLS’yi kullanmayabilir. Bu, Azure SQL veritabanı veya Azure SQL yönetilen örneği ile bağlantı kurulurken bir etken olabilir. Ekli sürücüleri olan uygulamalar bu bağlantı ayarlarını denetlemenize izin vermeyebilir. Hassas verilerle etkileşimde bulunan sistemlerde kullanmadan önce bu tarz sürücülerin ve uygulamaların güvenliğini incelemenizi öneririz.

## <a name="libraries"></a>Kitaplıklar

Azure SQL veritabanı veya Azure SQL yönetilen örneği 'ne bağlanmak için çeşitli kitaplıkları ve çerçeveleri kullanabilirsiniz. C#, Java, Node.js, PHP ve Python gibi programlama dillerini hızlıca kullanmaya başlamak için [Başlarken öğreticilerimize](https://aka.ms/sqldev) göz atın. Ardından, macOS üzerinde Linux üzerinde SQL Server veya Windows ya da Docker kullanarak bir uygulama oluşturun.

Aşağıdaki tabloda, istemci uygulamalarının şirket içinde veya bulutta çalışan SQL Server bağlanmak ve kullanmak için çeşitli dillerden kullanabileceği bağlantı kitaplıkları veya *sürücüler* listelenmiştir. Bunları Linux, Windows veya Docker üzerinde kullanabilir ve Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics 'e bağlanmak için kullanabilirsiniz.

| Dil | Platform | Ek kaynaklar | İndir | başlarken |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [SQL Server için Microsoft ADO.NET](/sql/connect/ado-net/microsoft-ado-net-sql-server) | [İndir](https://dotnet.microsoft.com/download) | [Kullanmaya başlama](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [SQL Server için Microsoft JDBC sürücüsü](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [İndir](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server) |  [Kullanmaya başlama](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [SQL Server için PHP SQL sürücüsü](/sql/connect/php/microsoft-php-driver-for-sql-server) | [İndir](/sql/connect/php/download-drivers-php-sql-server) | [Kullanmaya başlama](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [ SQL Server içinNode.js sürücüsü](/sql/connect/node-js/node-js-driver-for-sql-server/) | [Yükleme](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [Kullanmaya başlama](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL sürücüsü](/sql/connect/python/python-driver-for-sql-server/) | Seçenekleri yüklensin: <br/> \*[pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \*[pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [Kullanmaya başlama](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [SQL Server için Ruby sürücüsü](/sql/connect/ruby/ruby-driver-for-sql-server/) | [Yükleme](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [Kullanmaya başlama](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [SQL Server için Microsoft ODBC sürücüsü](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [İndir](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

Aşağıdaki tabloda, istemci uygulamalarının SQL Server, Azure SQL veritabanı, Azure SQL yönetilen örneği veya Azure SYNAPSE Analytics ile kullanabileceği nesne ilişkisel eşleme (ORM) çerçeveleri ve Web çerçeveleri örnekleri listelenmektedir. Çerçeveleri Linux, Windows veya Docker üzerinde kullanabilirsiniz.

| Dil | Platform | ORM (s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Varlık Çerçevesi](/ef)<br>[Entity Framework Core](/ef/core/index) |
| Java | Windows, Linux, macOS |[Bu ORM hazırda beklet](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laralevel (eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on rayları](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Sonraki adımlar

- Bağlanabilirlik mimarisi bilgileri için bkz. [Azure SQL Veritabanı Bağlanabilirlik Mimarisi](connectivity-architecture.md)
- İstemci uygulamalarından bağlanmak için kullanılan [SQL Server sürücülerini](/sql/connect/sql-connection-libraries/) bulun.
- Azure SQL veritabanı veya Azure SQL yönetilen örneği 'ne bağlanın:
  - [.NET kullanarak bağlanma ve sorgulama (C#)](connect-query-dotnet-core.md)
  - [PHP kullanarak bağlanma ve sorgulama](connect-query-php.md)
  - [Node.jskullanarak bağlanma ve sorgulama ](connect-query-nodejs.md)
  - [Java kullanarak bağlanma ve sorgulama](connect-query-java.md)
  - [Python kullanarak bağlanma ve sorgulama](connect-query-python.md)
  - [Ruby kullanarak bağlanma ve sorgulama](connect-query-ruby.md)
  - [Sqlcmd ve BCP 'Yi Linux üzerinde SQL Server komut satırı araçlarını yükleyip](/sql/linux/sql-server-linux-setup-tools) Linux kullanıcıları için, [sqlcmd](/sql/ssms/scripting/sqlcmd-use-the-utility)kullanarak Azure SQL veritabanı veya Azure SQL yönetilen örneği 'ne bağlanmayı deneyin.
- Yeniden deneme mantığı kodu örnekleri:
  - [Dayanıklı bağlantısı 'i ADO.NET ile bağlama][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Dayanıklı bağlantısı 'i PHP ile bağlama][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php
