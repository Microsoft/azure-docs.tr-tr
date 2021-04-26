---
title: Azure veritabanı güvenlik denetim listesi | Microsoft Docs
description: Önemli bulut bilgi işlem güvenliği sorunlarını ele aldığınızdan emin olmak için Azure veritabanı güvenlik denetim listesini kullanın.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 80455b442bbfb9c8a7d40799b2ddd5fc25460578
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595589"
---
# <a name="azure-database-security-checklist"></a>Azure veritabanı güvenlik denetim listesi

Güvenliği artırmaya yardımcı olmak için Azure veritabanı, erişimi sınırlandırmak ve denetlemek için kullanabileceğiniz bir dizi yerleşik güvenlik denetimi içerir.

Bu modüller şunlardır:

-    IP adresine göre bağlantıyı sınırlayan [güvenlik duvarı kuralları](../../azure-sql/database/firewall-configure.md) oluşturmanıza olanak tanıyan bir güvenlik duvarı,
-    Sunucu düzeyinde güvenlik duvarı Azure portal erişilebilir
-    SSMS 'den erişilebilen veritabanı düzeyinde güvenlik duvarı kuralları
-    Güvenli bağlantı dizeleri kullanarak veritabanınıza güvenli bağlantı
-    Erişim yönetimini kullanma
-    Veri şifrelemesi
-    SQL Veritabanı denetimi
-    SQL veritabanı tehdit algılama

## <a name="introduction"></a>Giriş
Bulut bilgi işlem, birçok uygulama kullanıcısı, veritabanı yöneticileri ve programcılara alışkın olmayan yeni güvenlik paradigmalarına gerektirir. Sonuç olarak, bazı kuruluşlar, algılanan güvenlik riskleri nedeniyle veri yönetimi için bir bulut altyapısı uygulamaya yöneliktir. Bununla birlikte, Microsoft Azure ve Microsoft Azure SQL Veritabanı yerleşik güvenlik özellikleri hakkında daha iyi bir anlayışının yanı sıra bu kaygıdan çok alleviated olabilir.

## <a name="checklist"></a>Denetim Listesi
Bu denetim listesini gözden geçirmeden önce [Azure veritabanı güvenliği En Iyi Yöntemler](../../azure-sql/database/security-best-practice.md)  makalesini okumanızı öneririz. En iyi yöntemleri anladıktan sonra bu denetim listesinden en iyi şekilde yararlanabileceksiniz. Daha sonra bu denetim listesini kullanarak Azure veritabanı güvenliği 'nde önemli sorunları giderdiğinizden emin olabilirsiniz.


|Denetim listesi kategorisi| Description|
| ------------ | -------- |
|**Verileri koruma**||
| <br> Hareket/aktarım sırasında şifreleme| <ul><li>Veriler ağlara taşınırken veri şifreleme için [Aktarım Katmanı Güvenliği](/windows-server/security/tls/transport-layer-security-protocol).</li><li>Veritabanı, TLS üzerinden [tds (tablosal veri akışı)](/openspecs/windows_protocols/ms-tds/893fcc7e-8a39-4b3c-815a-773b7b982c50) Protokolü (Aktarım Katmanı Güvenliği) tabanlı istemcilerden güvenli iletişim gerektirir.</li></ul> |
|<br>Bekleme sırasında şifreleme| <ul><li>[Saydam veri şifrelemesi](../../azure-sql/database/transparent-data-encryption-tde-overview.md), etkin olmayan veriler fiziksel olarak herhangi bir dijital biçimde depolanır.</li></ul>|
|**Denetim erişimi**||  
|<br> Veritabanı Erişimi | <ul><li>[Kimlik](../../azure-sql/database/logins-create-manage.md) doğrulama (Azure Active Directory kimlik doğrulaması) ad kimlik doğrulaması, Azure Active Directory tarafından yönetilen kimlikleri kullanır.</li><li>[Yetkilendirme](../../azure-sql/database/logins-create-manage.md) kullanıcılara gereken en düşük ayrıcalıkları verin.</li></ul> |
|<br>Uygulama erişimi| <ul><li>[Satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security) (güvenlik ilkesini kullanarak, bir kullanıcının kimliğine, rolüne veya yürütme bağlamına göre satır düzeyinde erişimi kısıtlayarak aynı zamanda).</li><li>[Dinamik veri maskeleme](../../azure-sql/database/dynamic-data-masking-overview.md) (Izin & ilkesi kullanılarak, ayrıcalıklı olmayan kullanıcılara maskeleyerek hassas veri pozlamasını kısıtlar)</li></ul>|
|**Proaktif Izleme**||  
| <br>& algılama izleniyor| <ul><li>[Denetim](../../azure-sql/database/auditing-overview.md) , veritabanı olaylarını izler ve bunları [Azure Depolama hesabınızdaki](../../storage/common/storage-account-create.md)bir denetim günlüğüne/etkinlik günlüğüne yazar.</li><li>Azure [Izleyici etkinlik günlüklerini](../../azure-monitor/essentials/platform-logs-overview.md)kullanarak Azure veritabanı sistem durumunu izleyin.</li><li>[Tehdit algılama](../../azure-sql/database/threat-detection-configure.md) , veritabanına ilişkin olası güvenlik tehditlerini gösteren anormal veritabanı etkinliklerini algılar. </li></ul> |
|<br>Azure Güvenlik Merkezi| <ul><li>[Veri izleme](../../security-center/security-center-remediate-recommendations.md) SQL ve diğer Azure hizmetleri için merkezi bir güvenlik izleme çözümü olarak Azure Güvenlik Merkezi 'ni kullanın.</li></ul>|        

## <a name="conclusion"></a>Sonuç
Azure veritabanı, çok sayıda kuruluş ve mevzuat uyumluluk gereksinimini karşılayan bir dizi güvenlik özelliği olan sağlam bir veritabanı platformudur. Verilerinize fiziksel erişimi denetleyerek verileri kolayca koruyabilir ve dosya, sütun veya satır düzeyinde Saydam Veri Şifrelemesi, Cell-Level şifreleme veya Row-Level güvenliği ile veri güvenliği için çeşitli seçenekler kullanabilirsiniz. Always Encrypted Ayrıca, uygulama güncelleştirmeleri sürecini basitleştirecek şifreli verilere karşı işlemleri de mümkün bir şekilde sunar. Buna karşılık, SQL veritabanı etkinliğinin denetim günlüklerine erişim, size gereken bilgileri sağlar ve verilere nasıl ve ne zaman erişildiğini bilmenize olanak tanır.

## <a name="next-steps"></a>Sonraki adımlar
Yalnızca birkaç basit adımda kötü amaçlı kullanıcılara ya da yetkisiz erişime karşı veritabanınızın korumasını artırabilirsiniz. Bu öğreticide şunları öğrenirsiniz:

- Sunucunuz ve veritabanınız için [güvenlik duvarı kuralları](../../azure-sql/database/firewall-configure.md) ayarlayın.
- [Şifreleme](/sql/relational-databases/security/encryption/sql-server-encryption)ile verilerinizi koruyun.
- [SQL veritabanı denetimini](../../azure-sql/database/auditing-overview.md)etkinleştirin.