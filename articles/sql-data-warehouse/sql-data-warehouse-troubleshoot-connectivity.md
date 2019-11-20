---
title: Bağlantı sorunlarını giderme
description: Azure SQL veri ambarı 'nda bağlantı sorunlarını giderme.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d1139032176b3b44c58471b87cabd10ffeaa3d20
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692416"
---
# <a name="troubleshooting-connectivity-issues"></a>Bağlantı sorunlarını giderme

Bu makalede, SQL veri ambarınıza bağlanılmasıyla ilgili yaygın sorun giderme teknikleri listelenmektedir.
- [Hizmet kullanılabilirliğini denetle](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Duraklatılmış veya ölçeklendirilen işlemleri denetleme](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Güvenlik duvarı ayarlarını denetleme](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Sanal Ağ/Hizmet Uç Noktası ayarlarınızı denetleme](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [En son sürücüleri denetleme](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Bağlantı dizenizi denetleme](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Aralıklı bağlantı sorunları](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Genel hata iletileri](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Hizmet kullanılabilirliğini denetle

Hizmetin kullanılabilir olup olmadığını denetleyin. Azure portal, bağlanmaya çalıştığınız SQL veri ambarına gidin. Sol IÇINDEKILER panelinde, **sorunları Tanıla ve çöz**' e tıklayın.

![Kaynak durumunu seçin](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

SQL veri ambarınızın durumu burada gösterilir. Hizmet **kullanılabilir**olarak görüntülenmiyorsa, daha fazla adım denetleyin.

![Hizmet kullanılabilir](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Kaynak sistem durumu, veri ambarınızın duraklatıldığını veya ölçeklendirilmesini gösteriyorsa, veri Ambarınızı sürdürmeye yönelik yönergeleri izleyin.

![hizmeti duraklatıldı](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Kaynak Durumu hakkında ek bilgiler burada bulunabilir.

## <a name="check-for-paused-or-scaling-operation"></a>Duraklama veya ölçeklendirme işlemini denetle

SQL veri ambarınız duraklatıldığında mi yoksa ölçeklendirmi olduğunu görmek için portalı denetleyin.

![Hizmet duraklatıldı](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Hizmetinizin duraklatıldığını veya ölçeklendirilmesini görürseniz, bakım zamanlamanız sırasında olup olmadığını kontrol edin. SQL veri ambarınızın *genel bakış*portalındaki seçili bakım zamanlamasını görürsünüz.

![Genel Bakış bakım zamanlaması](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Aksi takdirde, bu bakımın zamanlanmış bir olay olmadığını doğrulamak için BT yöneticinize başvurun. SQL veri ambarını duraklatmak için [burada](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute)özetlenen adımları izleyin.

## <a name="check-your-firewall-settings"></a>Güvenlik duvarı ayarlarınızı denetleyin

SQL Veri Ambarı 1433 numaralı bağlantı noktası üzerinden iletişim kurar.   Bir kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, BT departmanınız 1433 numaralı bağlantı noktasını açmadığı sürece Azure SQL Veritabanı sunucunuza bağlanamazsınız. Güvenlik Duvarı yapılandırmalarına ilişkin ek bilgilere [buradan](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)ulaşabilirsiniz.

## <a name="check-your-vnetservice-endpoint-settings"></a>VNet/hizmet uç noktası ayarlarınızı denetleyin

40914 ve 40615 hatalarını alıyorsanız, [buraya hata açıklaması ve çözüm](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)bölümüne bakın.

## <a name="check-for-the-latest-drivers"></a>En son sürücüleri denetle

### <a name="software"></a>Yazılım

SQL veri ambarınıza bağlanmak için en son araçları kullandığınızdan emin olun:

* SSMS
* Azure Data Studio
* SQL Server Veri Araçları (Visual Studio)

### <a name="drivers"></a>Sürücüler

En son sürücü sürümlerini kullandığınızdan emin olun.  Eski sürücüler yeni özellikleri desteklemedikleri için sürücülerin eski bir sürümünü kullanmak beklenmeyen davranışlara neden olabilir.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Bağlantı dizenizi denetleyin

Bağlantı dizelerinizin düzgün ayarlandığından emin olun.  Aşağıda bazı örnekler verilmiştir.  Bağlantı dizeleriyle ilgili ek bilgileri [burada](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings) bulabilirsiniz.

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

Sunucu üzerinde çok sayıda sıraya alınmış isteğin olduğu ağır yüklenme sorunu yaşayıp yaşamadığınızı denetleyin. Ek kaynaklar için veri ambarınızın ölçeğini artırmanız gerekebilir.

## <a name="common-error-messages"></a>Genel hata iletileri

Hatalar 40914 ve 40615, [buradaki hata açıklamasına ve çözümüne](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)bakın.

## <a name="still-having-connectivity-issues"></a>Hala bağlantı sorunları var mı?
Mühendislik ekibinin sizi destekleyebilmesi için bir [destek bileti](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) oluşturun.
