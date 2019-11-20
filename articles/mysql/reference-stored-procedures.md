---
title: MySQL için Azure veritabanı yönetimi saklı yordamları
description: MySQL için Azure veritabanı 'nda bulunan saklı yordamların, verileri yapılandırma, saat dilimini ayarlama ve sorguları sonlandırma konusunda ne kadar kullanışlı olduğunu öğrenin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/19/2019
ms.openlocfilehash: 34598278ac7f432c5976de86eaf5dcf477c7e81a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970333"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>MySQL için Azure veritabanı yönetimi saklı yordamları

MySQL sunucunuzu yönetmeye yardımcı olmak için MySQL sunucuları için Azure veritabanı 'nda saklı yordamlar bulunur. Bu, sunucunuzun bağlantılarını, sorgularını yönetmeyi ve Gelen Verileri Çoğaltma ayarlamayı içerir.  

## <a name="data-in-replication-stored-procedures"></a>Gelen Verileri Çoğaltma saklı yordamlar

Gelen Verileri Çoğaltma, şirket içinde, sanal makinelerde veya diğer bulut sağlayıcıları tarafından barındırılan veritabanı hizmetlerinde çalışan bir MySQL sunucusundan, MySQL için Azure Veritabanı hizmetine verileri eşitlemenize olanak sağlar.

Ana ve çoğaltma arasında Gelen Verileri Çoğaltma ayarlamak veya kaldırmak için aşağıdaki saklı yordamlar kullanılır.

|**Saklı yordam adı**|**Giriş Parametreleri**|**Çıkış parametreleri**|**Kullanım notunun**|
|-----|-----|-----|-----|
|*MySQL. az _replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Yok|SSL moduyla veri aktarmak için CA sertifikasının bağlamını master_ssl_ca parametresine geçirin. </br><br>SSL olmadan veri aktarmak için master_ssl_ca parametresine boş bir dize geçirin.|
|*MySQL. az _replication _start*|Yok|Yok|Çoğaltmayı başlatır.|
|*MySQL. az _replication _stop*|Yok|Yok|Çoğaltmayı durduruyor.|
|*MySQL. az _replication _remove_master*|Yok|Yok|Ana ve çoğaltma arasındaki çoğaltma ilişkisini kaldırır.|
|*MySQL. az _replication_skip_counter*|Yok|Yok|Bir çoğaltma hatasını atlar.|

MySQL için Azure veritabanı 'nda ana ve çoğaltma arasında Gelen Verileri Çoğaltma ayarlamak için, [gelen verileri çoğaltma yapılandırma](howto-data-in-replication.md)konusuna bakın.

## <a name="other-stored-procedures"></a>Diğer saklı yordamlar

Aşağıdaki saklı yordamlar, MySQL için Azure veritabanı 'nda sunucunuzu yönetmek için kullanılabilir.

|**Saklı yordam adı**|**Giriş Parametreleri**|**Çıkış parametreleri**|**Kullanım notunun**|
|-----|-----|-----|-----|
|*MySQL. az _kıll*|processlist_id|Yok|[@No__t-1](https://dev.mysql.com/doc/refman/8.0/en/kill.html) komutuna eşdeğerdir. , Bağlantının yürütüldüğü tüm deyimleri sonlandırdıktan sonra, belirtilen processlist_id ilişkili bağlantıyı sonlandırır.|
|*MySQL. az _kill_query*|processlist_id|Yok|[@No__t-1](https://dev.mysql.com/doc/refman/8.0/en/kill.html) komutuna eşdeğerdir. Bağlantı şu anda yürütülmekte olan ifadeyi sonlandırır. Bağlantıyı canlı bırakır.|
|*MySQL. az _load_timezone*|Yok|Yok|@No__t-0 parametresinin adlandırılmış değerler (örn.) olarak ayarlanbilmesini sağlamak için saat dilimi tablolarını yükler. "ABD/Pasifik").|

## <a name="next-steps"></a>Sonraki adımlar
- [Gelen verileri çoğaltma](howto-data-in-replication.md) ayarlamayı öğrenin
- [Saat dilimi tablolarını](howto-server-parameters.md#working-with-the-time-zone-parameter) nasıl kullanacağınızı öğrenin
