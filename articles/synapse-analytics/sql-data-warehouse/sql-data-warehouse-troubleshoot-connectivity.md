---
title: Bağlantı sorunlarını giderme
description: Adanmış SQL havuzunda (eski adıyla SQL DW) bağlantı sorunlarını giderme.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-csharp
ms.openlocfilehash: 8b23a3634b34277b732d4ba18bef7e71c783ebd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98681195"
---
# <a name="troubleshooting-connectivity-issues-in-dedicated-sql-pool-formerly-sql-dw"></a>Adanmış SQL havuzunda (eski adıyla SQL DW) bağlantı sorunlarını giderme

Bu makalede, adanmış SQL havuzu veritabanınıza (eski adıyla SQL DW) bağlanılmasıyla ilgili genel sorun giderme teknikleri listelenmektedir.

## <a name="check-service-availability"></a>Hizmet kullanılabilirliğini denetle

Hizmetin kullanılabilir olup olmadığını denetleyin. Azure portal, bağlanmaya çalıştığınız adanmış SQL havuzuna (eski adıyla SQL DW) gidin. Sol IÇINDEKILER panelinde, **sorunları Tanıla ve çöz**' e tıklayın.

![Kaynak durumunu seçin](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Adanmış SQL havuzunuzun durumu (eski adıyla SQL DW) burada gösterilir. Hizmet **kullanılabilir** olarak görüntülenmiyorsa, daha fazla adım denetleyin.

![Hizmet kullanılabilir](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Kaynak sağlık, adanmış SQL havuzunuzun (eski adıyla SQL DW) örneğinin duraklatıldığını veya ölçeklendirilmesini gösteriyorsa, örneğinizi sürdürmeye yönelik yönergeleri izleyin.

![Ekran görüntüsünde, duraklatılan veya ölçeklendirerek adanmış SQL havuzunun bir örneği gösterilir.](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)
Kaynak Durumu hakkında daha fazla bilgi burada bulunabilir.

## <a name="check-for-paused-or-scaling-operation"></a>Duraklatılmış veya ölçeklendirilen işlemleri denetleme

Adanmış SQL havuzunuzun (eski adıyla SQL DW) örneğinin duraklatıldığını veya ölçeklendirilmesini görmek için portalı denetleyin.

![Ekran görüntüsünde bir veri ambarının duraklatıldığını denetleme işlemi gösterilir.](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Hizmetinizin duraklatıldığını veya ölçeklendirilmesini görürseniz, bakım zamanlamanız sırasında olup olmadığını kontrol edin. Özel SQL havuzunuza (eski adıyla SQL DW) *genel bakış* için Portal 'da, seçili bakım zamanlamasını görürsünüz.

![Genel Bakış bakım zamanlaması](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Aksi takdirde, bu bakımın zamanlanmış bir olay olmadığını doğrulamak için BT yöneticinize başvurun. Adanmış SQL Havuzu (eski adıyla SQL DW) örneğini yeniden başlatmak için [aşağıdaki adımları](pause-and-resume-compute-portal.md)izleyin.

## <a name="check-your-firewall-settings"></a>Güvenlik duvarı ayarlarını denetleme

Adanmış SQL Havuzu (eski adıyla SQL DW) veritabanı 1433 bağlantı noktası üzerinden iletişim kurar.Bir şirket ağından bağlanmaya çalışıyorsanız ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, BT departmanınız 1433 numaralı bağlantı noktasını açmadığı takdirde [mantıksal sunucunuza](../../azure-sql/database/logical-servers.md) bağlanamazsınız. Güvenlik Duvarı yapılandırmalarına ilişkin ek bilgilere [buradan](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules)ulaşabilirsiniz.

## <a name="check-your-vnetservice-endpoint-settings"></a>Sanal Ağ/Hizmet Uç Noktası ayarlarınızı denetleme

40914 ve 40615 hatalarını alıyorsanız, [buraya hata açıklaması ve çözüm](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)bölümüne bakın.

## <a name="check-for-the-latest-drivers"></a>En son sürücüleri denetleme

### <a name="software"></a>Yazılım

Adanmış SQL havuzunuza (eski adıyla SQL DW) bağlanmak için en son araçları kullandığınızdan emin olun:

- SSMS
- Azure Data Studio
- SQL Server Veri Araçları (Visual Studio)

### <a name="drivers"></a>Sürücüler

En son sürücü sürümlerini kullandığınızdan emin olun.Eski sürücüler yeni özellikleri desteklemedikleri için sürücülerin eski bir sürümünü kullanmak beklenmeyen davranışlara neden olabilir.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="check-your-connection-string"></a>Bağlantı dizenizi denetleme

Bağlantı dizelerinizin düzgün ayarlandığından emin olun.  Aşağıda bazı örnekler verilmiştir.  Bağlantı dizeleriyle ilgili ek bilgileri [burada](sql-data-warehouse-connection-strings.md) bulabilirsiniz.

ADO.NET bağlantı dizesi

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC bağlantı dizesi

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP bağlantı dizesi

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC bağlantı dizesi

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Aralıklı bağlantı sorunları

Sunucu üzerinde çok sayıda sıraya alınmış isteğin olduğu ağır yüklenme sorunu yaşayıp yaşamadığınızı denetleyin. Ek kaynaklar için adanmış SQL havuzunuzu (eski adıyla SQL DW) ölçeklendirmeniz gerekebilir.

## <a name="common-error-messages"></a>Genel hata iletileri

Hatalar 40914 ve 40615, [buradaki hata açıklamasına ve çözümüne](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)bakın.

## <a name="still-having-connectivity-issues"></a>Hala bağlantı sorunları var mı?

Mühendislik ekibinin sizi destekleyebilmesi için bir [destek bileti](sql-data-warehouse-get-started-create-support-ticket.md) oluşturun.
