---
title: Uygulama geliştirmeye genel bakış
description: SQL Veritabanına bağlanan uygulamalar için kullanılabilen bağlantı kitaplıkları ve en iyi uygulamalar hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.openlocfilehash: 26aa9948a44727ff4c8092eb5131b1c054bf5442
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082443"
---
# <a name="sql-database-application-development-overview"></a>SQL veritabanı uygulaması geliştirmeye genel bakış

Bu makalede, geliştiricilerin Azure SQL Veritabanı ile bağlantı kurmak üzere kod yazarken dikkat etmesi gereken noktalara yer verilmiştir. Bu makale, Azure SQL veritabanı 'nın (tek veritabanı, elastik havuzlar, yönetilen örnek) tüm dağıtım modellerine yöneliktir.

> [!TIP]
> Azure SQL veritabanınızı oluşturmanız gerekiyorsa, [tek veritabanları](sql-database-single-database-quickstart-guide.md) ve [yönetilen örnekler](sql-database-managed-instance-quickstart-guide.md) için Başlarken kılavuzlarına bakın.
>

## <a name="language-and-platform"></a>Dil ve platform

Azure SQL veritabanı 'na bağlanmak ve veritabanını sorgulamak için çeşitli [programlama dillerini ve platformlarını](sql-database-connect-query.md) kullanabilirsiniz. Azure SQL veritabanına bağlanmak için kullanabileceğiniz [örnek uygulamalar](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) bulabilirsiniz.

[Cheetah](https://github.com/wunderlist/cheetah), [SQL-CLI](https://www.npmjs.com/package/sql-cli), [vs Code](https://code.visualstudio.com/)gibi açık kaynaklı araçlardan yararlanabilirsiniz. Ayrıca, Azure SQL Veritabanı [Visual Studio](https://www.visualstudio.com/downloads/) ve [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) gibi Microsoft araçlarıyla birlikte çalışır. Ayrıca, Azure portal, PowerShell ve REST API 'Lerini kullanarak ek üretkenlik elde etmenize yardımcı olabilirsiniz.

## <a name="authentication"></a>Kimlik Doğrulaması

Azure SQL veritabanı 'na erişim, oturum açmalar ve güvenlik duvarları ile korunmaktadır. Azure SQL veritabanı hem SQL Server hem de [Azure Active Directory (AAD) kimlik doğrulama](sql-database-aad-authentication.md) kullanıcılarını ve oturum açmaları destekler. AAD oturum açmaları yalnızca yönetilen örnekte kullanılabilir. 

[Veritabanı erişimini yönetme ve oturum açma](sql-database-manage-logins.md)hakkında daha fazla bilgi edinin.

## <a name="connections"></a>Bağlantılar

İstemci bağlantısı mantığınızda varsayılan zaman aşımını 30 saniye olacak şekilde geçersiz kılın. 15 saniyelik varsayılan değer, internet kullanan bağlantılar için çok kısadır.

[Bağlantı havuzu](https://msdn.microsoft.com/library/8xx3tyca.aspx) kullanıyorsanız programınız etkin olarak kullanmadığında ve yeniden kullanma hazırlığı yapmadığında bağlantıyı kapatın.

Herhangi bir altyapı veya bağlantı hatası işlemi geri alabileceğinden uzun süredir çalışan işlemlerden kaçının. Mümkünse, işlemi birden çok küçük harekette ayırın ve [performansı artırmak için toplu](sql-database-use-batching-to-improve-performance.md)işlem kullanın.

## <a name="resiliency"></a>Dayanıklılık

Azure SQL veritabanı, temeldeki altyapıda veya bulut varlıkları arasındaki iletişimde gerçekleşen geçici hataları beklebileceğiniz bir bulut hizmetidir. Azure SQL veritabanı geçişli altyapı hatalarında dayanıklı olsa da, bu arızalar bağlantınızı etkileyebilir. SQL veritabanına bağlanırken geçici bir hata oluştuğunda, kodunuzun [çağrıyı yeniden denemesi](sql-database-connectivity-issues.md)gerekir. Yeniden deneme mantığının SQL Veritabanını aynı anda yeniden deneme yapan birden fazla istemciyle boğmaması için geri alma mantığını kullanmasını öneririz. Yeniden deneme mantığı, [SQL veritabanı istemci programlarının hata iletilerine](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)bağlıdır.

Azure SQL veritabanınızda planlı bakım olaylarına hazırlanma hakkında daha fazla bilgi için bkz. Azure [SQL veritabanı 'Nda Azure bakım olaylarını planlama](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Ağ konuları

- İstemci programınızı barındıran bilgisayarda güvenlik duvarının 1433 numaralı bağlantı noktasından giden TCP iletişimine izin verdiğinden emin olun.  Daha fazla bilgi: [Azure SQL veritabanı güvenlik duvarını yapılandırma](sql-database-configure-firewall-settings.md).
- İstemciniz bir Azure sanal makinesi (VM) üzerinde çalışırken istemci programınız SQL veritabanı 'na bağlanırsa, sanal makine üzerinde belirli bağlantı noktası aralıklarını açmanız gerekir. Daha fazla bilgi: [ADO.NET 4,5 ve SQL veritabanı için 1433 dışındaki bağlantı noktaları](sql-database-develop-direct-route-ports-adonet-v12.md).
- Azure SQL veritabanı 'na yönelik istemci bağlantıları bazen proxy 'yi atlar ve veritabanıyla doğrudan etkileşime geçin. 1433 dışındaki bağlantı noktaları önemli hale gelmiştir. Daha fazla bilgi için, [Azure SQL veritabanı bağlantı mimarisi](sql-database-connectivity-architecture.md) ve [ADO.NET 4,5 ve SQL veritabanı için 1433](sql-database-develop-direct-route-ports-adonet-v12.md)' den fazla bağlantı noktası.
- Yönetilen bir örnek için ağ yapılandırması için bkz. [yönetilen örnekler için ağ yapılandırması](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Sonraki adımlar

Tüm [SQL Veritabanı özelliklerini](sql-database-technical-overview.md) keşfedin.
