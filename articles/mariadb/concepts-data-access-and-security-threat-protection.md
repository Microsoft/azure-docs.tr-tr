---
title: Gelişmiş tehdit koruması-MariaDB için Azure veritabanı | Microsoft Docs
description: Gelişmiş tehdit koruması, veritabanına ilişkin olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılar.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: a96d8c3b9f9e3f9b1aedf269bce141da3e4ccf42
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869696"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>MariaDB Gelişmiş tehdit koruması için Azure veritabanı

MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, veritabanları için olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri algılar.

> [!IMPORTANT]
> Gelişmiş tehdit koruması genel önizlemede.

Gelişmiş tehdit koruması, gelişmiş güvenlik özelliklerine yönelik Birleşik bir paket olan gelişmiş veri güvenliği sunumunun bir parçasıdır. Gelişmiş tehdit korumasına [Azure Portal](https://portal.azure.com)aracılığıyla erişilebilir ve yönetebilirsiniz. Özelliği, Genel Amaçlı ve bellek için Iyileştirilmiş sunucular için kullanılabilir.

> [!NOTE]
> Gelişmiş tehdit koruması özelliği, aşağıdaki Azure Kamu ve bağımsız bulut **bölgelerinde kullanılamaz:** US Gov Teksas, US Gov Arizona, US Gov Iowa, US, gov Virginia, US DoD Doğu, US DoD Orta, Almanya Orta, Almanya Kuzey, Çin Doğu, Çin Doğu 2. Genel ürün kullanılabilirliği için lütfen [bölgeye göre sunulan ürünleri](https://azure.microsoft.com/global-infrastructure/services/) ziyaret edin.


## <a name="what-is-advanced-threat-protection"></a>Gelişmiş tehdit koruması nedir?

MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, müşterilerin anormal etkinliklerde güvenlik uyarıları sunarak meydana gelebilecek olası tehditleri algılamasına ve yanıt vermesine olanak tanıyan yeni bir güvenlik katmanı sağlar. Kullanıcılar şüpheli veritabanı etkinliklerine ve potansiyel güvenlik açıklarına ek olarak anormal veritabanı erişimi ve sorgu düzenlerine bir uyarı alırlar. MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, şüpheli etkinliklerin ayrıntılarını içeren ve tehdidi araştırmak ve hafifletmek için eylem öneren [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)ile ilgili uyarıları tümleştirir. MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, bir güvenlik uzmanı veya gelişmiş güvenlik izleme sistemlerini yönetmek zorunda kalmadan, olası tehditleri veritabanına kolayca adreslamanızı sağlar. 

![Gelişmiş tehdit koruması kavramı](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Gelişmiş tehdit koruması uyarıları 
MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, veritabanları için olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri algılar ve aşağıdaki uyarıları tetikleyebilirler:
- **Olağan dışı konumdan erişim**: Bu uyarı, bir kullanıcı tarafından olağan dışı bir coğrafi konumdan MariaDB sunucusu için Azure veritabanı 'nda oturum açmış olan MariaDB sunucusu için Azure veritabanı 'na erişim modelinde bir değişiklik olduğunda tetiklenir. Bazı durumlarda uyarı güvenli işlemleri (yeni bir uygulama veya geliştirici bakımı gibi) de algılar. Diğer durumlarda, uyarı kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Olağandışı Azure veri merkezinden erişim**: Bu uyarı, en son dönemde bu sunucuda görülen olağan dışı bir Azure veri merkezinden sunucuda oturum açmış olan MariaDB sunucusu için Azure veritabanı 'na erişim modelinde bir değişiklik olduğunda tetiklenir. Bazı durumlarda, uyarı meşru bir eylem (Azure 'da yeni uygulamanız Power BI) algılar. Diğer durumlarda, uyarı Azure kaynağı/hizmetinden kaynaklanan kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Bilmediğiniz sorumludan erişim**: Bu uyarı, Kullanıcı olağan dışı bir sorumlu (MariaDB kullanıcısı için Azure veritabanı) kullanarak sunucuda oturum açmış olan MariaDB sunucusu için Azure veritabanı 'na erişim modelinde bir değişiklik olduğunda tetiklenir. Bazı durumlarda uyarı güvenli işlemleri (yeni uygulama ve geliştirici bakımı gibi) de algılar. Diğer durumlarda, uyarı kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Zararlı olabilecek bir uygulamadan erişim**: Bu uyarı, veritabanına erişmek için zararlı olabilecek bir uygulama kullanıldığında tetiklenir. Bazı durumlarda, uyarı güvenlik testlerini algılar. Diğer durumlarda, yarı yaygın saldırı araçlarının kullandığı saldırıları algılar.
- **Deneme yanılma Azure veritabanı 'Nı MariaDB kimlik bilgileri için zorlama**: Bu uyarı, farklı kimlik bilgileri ile olağan dışı yüksek sayıda başarısız oturum açma işlemi olduğunda tetiklenir. Bazı durumlarda, uyarı güvenlik testlerini algılar. Diğer durumlarda, uyarı deneme yanılma saldırılarını algılar.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/security-center/security-center-intro) hakkında daha fazla bilgi edinin
* Fiyatlandırma hakkında daha fazla bilgi için bkz. [MariaDB Için Azure veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/mariadb/) 
* Azure portal kullanarak [MariaDB Gelişmiş tehdit koruması Için Azure veritabanı](howto-database-threat-protection-portal.md) 'nı yapılandırma  
