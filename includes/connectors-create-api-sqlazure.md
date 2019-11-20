---
title: include dosyası
description: include dosyası
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 11/08/2019
ms.openlocfilehash: 0be29f6f541aa58e57eb665ebaf29e35f42865e4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826308"
---
* Azure SQL veritabanı kullanıyorsanız [Azure SQL veritabanı 'Na bağlanma](#connect-azure-sql-db)bölümündeki adımları izleyin.

* SQL Server kullanıyorsanız, [SQL Server Connect](#connect-sql-server)' in altındaki adımları izleyin.

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Azure SQL veritabanı 'na bağlanma

SQL tetikleyicisi veya eylemi sizden bağlantı bilgilerini isterse, her iki tetikleyici ve eylem için çalışan aşağıdaki adımları izleyin.

1. **Bağlantı adı**için bağlantınız için bir ad oluşturun.

1. **SQL Server adı**altında Azure SQL Server ' ı seçin. **SQL veritabanı ad** listesi göründüğünde, veritabanınızı seçin. Azure SQL sunucunuz için Kullanıcı adı ve parola belirtin.

   Bu bilgileri, SQL veritabanı özelliklerindeki veya bağlantı dizinizdeki Azure portal da bulabilirsiniz:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Azure SQL veritabanı ile bağlantı oluştur](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. İşiniz bittiğinde **Oluştur**’u seçin.

1. Bağlantınızı oluşturduktan sonra, [SQL tetikleyicisi ekleme](#add-sql-trigger) veya [bir SQL eylemi ekleme](#add-sql-action)ile devam edin.

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>SQL Server'a bağlanma

SQL tetikleyicisi veya eylemi sizden bağlantı bilgilerini isterse, her iki tetikleyici ve eylem için çalışan aşağıdaki adımları izleyin. Şirket [içi veri ağ geçidini](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) yerel bir bilgisayara yüklemenizi ve [Azure Data Gateway kaynağını oluşturmanızı](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)gerektiren senaryolar için, önce bu gereksinimleri tamamladığınızdan emin olun. Aksi halde, bağlantınızı oluştururken ağ geçidi kaynağınız ağ geçitleri listesinde görünmez.

Ayrıca, Windows kimlik doğrulamasını bir [tümleştirme hizmeti ortamında (ISE)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)SQL Server Bağlayıcısı ile kullanmak için, bağlayıcının Ise sürümünü ve şirket içi veri ağ geçidini kullanın. ISE etiketli sürüm Windows kimlik doğrulamasını desteklemez.

1. **Bağlantı adı**için bağlantınız için bir ad oluşturun.

1. Tetikleyici veya eylemde, SQL Server seçeneklerinin görünmesi için şirket **içi veri ağ geçidi üzerinden Bağlan** ' ı seçin.

1. **SQL Server adı** ve **SQL veritabanı adı**için, SQL sunucunuzun adresini ve veritabanınızın adını belirtin. Kullanıcı **adı** ve **parola**için, sunucunuzun Kullanıcı adını ve parolasını belirtin.

   Bu bilgileri, bağlantı dizeniz içinde de bulabilirsiniz:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![SQL Server bağlantı oluştur](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. SQL sunucunuz Windows veya temel kimlik doğrulaması kullanıyorsa, **kimlik doğrulama türünü**seçin.

1. **Ağ geçitleri**altında, önceden oluşturduğunuz şirket içi veri ağ geçidiniz Ile ilişkili Azure aboneliğini seçin ve şirket içi veri ağ geçidinizin adını seçin.

   Ağ geçidiniz listede görünmüyorsa, [ağ geçidinizin](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)doğru şekilde ayarlandığından emin olun.

   ![SQL Server bağlantı oluşturma tamamlandı](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. İşiniz bittiğinde **Oluştur**’u seçin.

1. Bağlantınızı oluşturduktan sonra, [SQL tetikleyicisi ekleme](#add-sql-trigger) veya [SQL eylemi ekleme](#add-sql-action)ile devam edin.
