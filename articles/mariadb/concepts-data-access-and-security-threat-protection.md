---
title: Gelişmiş tehdit koruması-MariaDB için Azure veritabanı
description: Gelişmiş tehdit koruması, veritabanına ilişkin olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılar.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 018ba33271f8a3a81c3b7109607334b076d8609c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98661628"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>MariaDB Gelişmiş tehdit koruması için Azure veritabanı

MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, veritabanları için olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri algılar.

> [!IMPORTANT]
> Gelişmiş tehdit koruması genel önizlemede.

Gelişmiş tehdit koruması, gelişmiş güvenlik özelliklerine yönelik Birleşik bir paket olan gelişmiş veri güvenliği sunumunun bir parçasıdır. Gelişmiş tehdit korumasına [Azure Portal](https://portal.azure.com)aracılığıyla erişilebilir ve yönetebilirsiniz. Özelliği, Genel Amaçlı ve bellek için Iyileştirilmiş sunucular için kullanılabilir.

> [!NOTE]
> Gelişmiş tehdit koruması özelliği şu Azure Kamu ve bağımsız bulut **bölgelerinde kullanılamaz:** US gov Teksas, US gov Arizona, US gov Iowa, US, gov Virginia, US DOD Doğu, US DOD orta, Almanya Orta, Almanya Kuzey, Çin Doğu, Çin Doğu 2. Genel ürün kullanılabilirliği için lütfen [bölgeye göre sunulan ürünleri](https://azure.microsoft.com/global-infrastructure/services/) ziyaret edin.


## <a name="what-is-advanced-threat-protection"></a>Gelişmiş tehdit koruması nedir?

MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, müşterilerin anormal etkinliklerde güvenlik uyarıları sunarak meydana gelebilecek olası tehditleri algılamasına ve yanıt vermesine olanak tanıyan yeni bir güvenlik katmanı sağlar. Kullanıcılar şüpheli veritabanı etkinliklerine ve potansiyel güvenlik açıklarına ek olarak anormal veritabanı erişimi ve sorgu düzenlerine bir uyarı alırlar. MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, şüpheli etkinliklerin ayrıntılarını içeren ve tehdidi araştırmak ve hafifletmek için eylem öneren [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)ile ilgili uyarıları tümleştirir. MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, bir güvenlik uzmanı veya gelişmiş güvenlik izleme sistemlerini yönetmek zorunda kalmadan, olası tehditleri veritabanına kolayca adreslamanızı sağlar. 

![Gelişmiş tehdit koruması kavramı](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Gelişmiş tehdit koruması uyarıları 
MariaDB için Azure veritabanı için Gelişmiş tehdit koruması, veritabanları için olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri algılar ve aşağıdaki uyarıları tetikleyebilirler:
- **Olağan olmayan konumdan erişim**: Bu uyarı, bir kişinin MariaDB sunucusu için Azure veritabanı 'na erişim modelinde bir değişiklik olduğunda tetiklenir. Bu, bir kullanıcının, bir olağan dışı coğrafi konumdan MariaDB sunucusu Için Azure veritabanı 'nda oturum açtığı yerdir. Bazı durumlarda uyarı güvenli işlemleri (yeni bir uygulama veya geliştirici bakımı gibi) de algılar. Diğer durumlarda, uyarı kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Olağan dışı Azure veri merkezi 'Nden erişim**: Bu uyarı, bir kullanıcının sunucuda en son dönemde görülen olağan dışı bir Azure veri merkezinden sunucuda oturum açtığı MariaDB sunucusu Için Azure veritabanı 'na erişim modelinde bir değişiklik olduğunda tetiklenir. Bazı durumlarda, uyarı meşru bir eylem (Azure 'da yeni uygulamanız Power BI) algılar. Diğer durumlarda, uyarı Azure kaynağı/hizmetinden kaynaklanan kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Bilmediğiniz sorumludan erişim**: Bu uyarı, Kullanıcı olağan dışı bir sorumlu (MariaDB kullanıcısı Için Azure veritabanı) kullanarak sunucuda oturum açmış olan MariaDB sunucusu Için Azure veritabanı 'na erişim modelinde bir değişiklik olduğunda tetiklenir. Bazı durumlarda uyarı güvenli işlemleri (yeni uygulama ve geliştirici bakımı gibi) de algılar. Diğer durumlarda, uyarı kötü amaçlı işlemleri (önceki çalışan ve şirket dışı saldırgan gibi) algılar.
- **Zararlı olabilecek bir uygulamadan erişim**: Bu uyarı, zararlı olabilecek bir uygulama veritabanına erişmeye çalıştığında tetiklenir. Bazı durumlarda, uyarı güvenlik testlerini algılar. Diğer durumlarda, yarı yaygın saldırı araçlarının kullandığı saldırıları algılar.
- **Deneme yanılma Azure veritabanı 'Nı MariaDB kimlik bilgileri için zorlama**: Bu uyarı, farklı kimlik bilgileri ile olağan dışı yüksek sayıda başarısız oturum açma işlemi olduğunda tetiklenir. Bazı durumlarda, uyarı güvenlik testlerini algılar. Diğer durumlarda, uyarı deneme yanılma saldırılarını algılar.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Güvenlik Merkezi](../security-center/security-center-introduction.md) hakkında daha fazla bilgi edinin
* Fiyatlandırma hakkında daha fazla bilgi için bkz. [MariaDB Için Azure veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/mariadb/) 
* Azure portal kullanarak [MariaDB Gelişmiş tehdit koruması Için Azure veritabanı](howto-database-threat-protection-portal.md) 'nı yapılandırma