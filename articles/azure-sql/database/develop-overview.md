---
title: Uygulama geliştirmeye genel bakış
description: SQL Veritabanına bağlanan uygulamalar için kullanılabilen bağlantı kitaplıkları ve en iyi uygulamalar hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92782986"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>Uygulama geliştirmeye genel bakış-SQL veritabanı & SQL yönetilen örneği

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Bu makalede, bir geliştiricinin Azure 'da veritabanınıza bağlanmak için kod yazarken dikkat edilmesi gereken temel noktalar anlatılmaktadır. Bu makale, Azure SQL veritabanı ve Azure SQL yönetilen örneği için geçerlidir.

## <a name="language-and-platform"></a>Dil ve platform

Azure SQL veritabanı 'na bağlanmak ve veritabanını sorgulamak için çeşitli [programlama dillerini ve platformlarını](connect-query-content-reference-guide.md) kullanabilirsiniz. Veritabanına bağlanmak için kullanabileceğiniz [örnek uygulamalar](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) bulabilirsiniz.

[Cheetah](https://github.com/wunderlist/cheetah), [SQL-CLI](https://www.npmjs.com/package/sql-cli), [vs Code](https://code.visualstudio.com/)gibi açık kaynaklı araçlardan yararlanabilirsiniz. Ayrıca, Azure SQL Veritabanı [Visual Studio](https://www.visualstudio.com/downloads/) ve [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) gibi Microsoft araçlarıyla birlikte çalışır. Ayrıca, Azure portal, PowerShell ve REST API 'Lerini kullanarak ek üretkenlik elde etmenize yardımcı olabilirsiniz.

## <a name="authentication"></a>Kimlik Doğrulaması

Azure SQL veritabanı 'na erişim, oturum açmalar ve güvenlik duvarları ile korunmaktadır. Azure SQL veritabanı hem SQL Server hem de [Azure Active Directory kimlik doğrulama](authentication-aad-overview.md) kullanıcılarını ve oturum açmaları destekler. Azure Active Directory oturum açma işlemleri yalnızca SQL yönetilen örneği 'nde kullanılabilir. 

[Veritabanı erişimini yönetme ve oturum açma](logins-create-manage.md)hakkında daha fazla bilgi edinin.

## <a name="connections"></a>Bağlantılar

İstemci bağlantısı mantığınızda varsayılan zaman aşımını 30 saniye olacak şekilde geçersiz kılın. 15 saniyelik varsayılan değer, internet kullanan bağlantılar için çok kısadır.

[Bağlantı havuzu](/dotnet/framework/data/adonet/sql-server-connection-pooling) kullanıyorsanız programınız etkin olarak kullanmadığında ve yeniden kullanma hazırlığı yapmadığında bağlantıyı kapatın.

Herhangi bir altyapı veya bağlantı hatası işlemi geri alabileceğinden uzun süredir çalışan işlemlerden kaçının. Mümkünse, işlemi birden çok küçük harekette ayırın ve [performansı artırmak için toplu](../performance-improve-use-batching.md)işlem kullanın.

## <a name="resiliency"></a>Dayanıklılık

Azure SQL veritabanı, temeldeki altyapıda veya bulut varlıkları arasındaki iletişimde gerçekleşen geçici hataları beklebileceğiniz bir bulut hizmetidir. Azure SQL veritabanı geçişli altyapı hatalarında dayanıklı olsa da, bu arızalar bağlantınızı etkileyebilir. SQL veritabanına bağlanırken geçici bir hata oluştuğunda, kodunuzun [çağrıyı yeniden denemesi](troubleshoot-common-connectivity-issues.md)gerekir. Aynı anda birden çok istemcisini yeniden denemeye yönelik hizmeti aşırı bir şekilde kapatmaması için, yeniden deneme mantığının geri alma mantığını kullanmasını öneririz. Yeniden deneme mantığı, [SQL veritabanı istemci programlarının hata iletilerine](troubleshoot-common-errors-issues.md)bağlıdır.

Azure SQL veritabanınızda planlı bakım olaylarına hazırlanma hakkında daha fazla bilgi için bkz. Azure [SQL veritabanı 'Nda Azure bakım olaylarını planlama](planned-maintenance.md).

## <a name="network-considerations"></a>Ağ konuları

- İstemci programınızı barındıran bilgisayarda güvenlik duvarının 1433 numaralı bağlantı noktasından giden TCP iletişimine izin verdiğinden emin olun.  Daha fazla bilgi: [Azure SQL veritabanı güvenlik duvarını yapılandırma](firewall-configure.md).
- İstemciniz bir Azure sanal makinesi (VM) üzerinde çalışırken istemci programınız SQL veritabanı 'na bağlanırsa, sanal makine üzerinde belirli bağlantı noktası aralıklarını açmanız gerekir. Daha fazla bilgi: [ADO.NET 4,5 ve SQL veritabanı için 1433 dışındaki bağlantı noktaları](adonet-v12-develop-direct-route-ports.md).
- Azure SQL veritabanı 'na yönelik istemci bağlantıları bazen proxy 'yi atlar ve veritabanıyla doğrudan etkileşime geçin. 1433 dışındaki bağlantı noktaları önemli hale gelmiştir. Daha fazla bilgi için, [Azure SQL veritabanı bağlantı mimarisi](connectivity-architecture.md) ve [ADO.NET 4,5 ve SQL veritabanı için 1433](adonet-v12-develop-direct-route-ports.md)' den fazla bağlantı noktası.
- SQL yönetilen örneği örneği için ağ yapılandırması için bkz. [SQL yönetilen örneği için ağ yapılandırması](../managed-instance/how-to-content-reference-guide.md#network-configuration).

## <a name="next-steps"></a>Sonraki adımlar

[SQL veritabanı](sql-database-paas-overview.md) ve [SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md)'nin tüm özelliklerini keşfet.

Başlamak için bkz. [Azure SQL veritabanı](quickstart-content-reference-guide.md) ve [Azure SQL yönetilen örnekleri](../managed-instance/quickstart-content-reference-guide.md)için kılavuzlar.