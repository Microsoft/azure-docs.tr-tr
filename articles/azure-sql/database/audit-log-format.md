---
title: SQL veritabanı denetim günlüğü biçimi
description: Azure SQL veritabanı denetim günlüklerinin nasıl yapılandırıldığını anlayın.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: reference
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 06/03/2020
ms.openlocfilehash: f5c176db4f679c79bb42c6ceb46b3588e9440874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572221"
---
# <a name="sql-database-audit-log-format"></a>SQL veritabanı denetim günlüğü biçimi

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL veritabanı denetimi](auditing-overview.md) , veritabanı olaylarını izler ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne yazar veya bunları aşağı akış işleme ve analiz Için Olay Hub 'ına veya Log Analytics gönderir.

## <a name="naming-conventions"></a>Adlandırma kuralları

### <a name="blob-audit"></a>Blob denetimi

Azure Blob depolamada depolanan denetim günlükleri `sqldbauditlogs` , Azure depolama hesabında adlı bir kapsayıcıda depolanır. Kapsayıcı içindeki dizin hiyerarşisi formundadır `<ServerName>/<DatabaseName>/<AuditName>/<Date>/` . BLOB dosya adı biçimi biçimindedir `<CreationTime>_<FileNumberInSession>.xel` , burada `CreationTime` UTC `hh_mm_ss_ms` biçimindedir ve `FileNumberInSession` oturum günlüklerinin birden çok blob dosyasına yayıldığı durumlarda çalışan bir dizindir.

Örneğin, aşağıdaki veritabanı için `Database1` `Server1` olası geçerli bir yol verilmiştir:

`Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel`

[Salt okuma çoğaltmaları](read-scale-out.md) denetim günlükleri aynı kapsayıcıda depolanır. Kapsayıcı içindeki dizin hiyerarşisi formundadır `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/` . BLOB dosya adı aynı biçimi paylaşır. Salt okuma çoğaltmalarının denetim günlükleri aynı kapsayıcıda depolanır.


### <a name="event-hub"></a>Olay Hub'ı

Denetim olayları denetim yapılandırması sırasında tanımlanan ad alanına ve olay hub'ına yazılır; [Apache Avro](https://avro.apache.org/) olaylarının gövdesinde yakalanır ve UTF-8 kodlamasıyla JSON biçimlendirmesi kullanılarak depolanır. Denetim günlüklerini okumak için [Avro Araçları](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools)'nı veya bu biçimi işleyebilen benzer araçları kullanabilirsiniz.

### <a name="log-analytics"></a>Log Analytics

Denetim olayları denetim yapılandırması sırasında tanımlanan Log Analytics çalışma alanında `AzureDiagnostics` tablosuna `SQLSecurityAuditEvents` kategorisiyle yazılır. Log Analytics arama dili ve komutları hakkında diğer yararlı bilgiler için bkz. [Log Analytics arama başvurusu](../../azure-monitor/logs/log-query-overview.md).

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Denetim günlüğü alanları

| Ad (blob) | Ad (Event Hubs/Log Analytics) | Description | Blob türü | Event Hubs/Log Analytics türü |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | Eylemin KIMLIĞI | varchar (4) | string |
| action_name | action_name_s | Eylemin adı | Yok | string |
| additional_information | additional_information_s | XML olarak depolanan olayla ilgili ek bilgiler | nvarchar (4000) | string |
| affected_rows | affected_rows_d | Sorgudan etkilenen satır sayısı | bigint | int |
| application_name | application_name_s| İstemci uygulamasının adı | nvarchar (128) | string |
| audit_schema_version | audit_schema_version_d | Always 1 | int | int |
| class_type | class_type_s | Denetim üzerinde gerçekleştiği denetlenebilir varlık türü | varchar (2) | string |
| class_type_desc | class_type_description_s | Denetim üzerinde gerçekleştiği denetlenebilir varlığın açıklaması | Yok | string |
| client_ip | client_ip_s | İstemci uygulamasının kaynak IP 'si | nvarchar (128) | string |
| connection_id | Yok | Sunucudaki bağlantının KIMLIĞI | GUID | Yok |
| data_sensitivity_information | data_sensitivity_information_s | Veritabanındaki sınıflandırılan sütunlara göre denetlenen sorgu tarafından döndürülen bilgi türleri ve duyarlılık etiketleri. [Azure SQL veritabanı veri bulma ve sınıflandırma](data-discovery-and-classification-overview.md) hakkında daha fazla bilgi edinin | nvarchar (4000) | string |
| database_name | database_name_s | Eylemin gerçekleştiği veritabanı bağlamı | döndürmeli | string |
| database_principal_id | database_principal_id_d | İşlemin gerçekleştirildiği veritabanı kullanıcı bağlamının KIMLIĞI | int | int |
| database_principal_name | database_principal_name_s | Eylemin gerçekleştirildiği veritabanı kullanıcı bağlamının adı | döndürmeli | string |
| duration_milliseconds | duration_milliseconds_d | Milisaniye cinsinden sorgu yürütme süresi | bigint | int |
| event_time | event_time_t | Denetlenebilir eylemin harekete geçirildiğinde tarih ve saat | datetime2 | datetime |
| host_name | Yok | İstemci konak adı | string | Yok |
| is_column_permission | is_column_permission_s | Bu sütun düzeyi izninin olup olmadığını belirten bayrak. 1 = true, 0 = false | bit | string |
| Yok | is_server_level_audit_s | Bu denetimin sunucu düzeyinde olup olmadığını belirten bayrak | Yok | string |
| object_ kimliği | object_id_d | Üzerinde denetim gerçekleştiği varlığın KIMLIĞI. Buna: sunucu nesneleri, veritabanları, veritabanı nesneleri ve şema nesneleri dahildir. varlık sunucunun kendisi ise veya denetim bir nesne düzeyinde gerçekleştirildiyse 0 | int | int |
| object_name | object_name_s | Üzerinde denetim gerçekleştiği varlığın adı. Buna: sunucu nesneleri, veritabanları, veritabanı nesneleri ve şema nesneleri dahildir. varlık sunucunun kendisi ise veya denetim bir nesne düzeyinde gerçekleştirildiyse 0 | döndürmeli | string |
| permission_bitmask | permission_bitmask_s | Uygun olduğunda, verilen, reddedilen veya iptal edilen izinleri gösterir | varbinary (16) | string |
| response_rows | response_rows_d | Sonuç kümesinde döndürülen satır sayısı | bigint | int |
| schema_name | schema_name_s | Eylemin gerçekleştiği şema bağlamı. Şema dışında gerçekleşen denetimler için NULL | döndürmeli | string |
| Yok | securable_class_type_s | Denetlenen class_type eşleyen güvenli kılınabilir nesne | Yok | string |
| sequence_group_id | sequence_group_id_g | Benzersiz tanımlayıcı | ikili | GUID |
| sequence_number | sequence_number_d | Denetimler için yazma arabelleğine sığamayacak kadar büyük olan tek bir denetim kaydı içindeki kayıt sırasını izler | int | int |
| server_instance_name | server_instance_name_s | Denetim gerçekleştiği sunucu örneğinin adı | döndürmeli | string |
| server_principal_id | server_principal_id_d | Eylemin gerçekleştirildiği oturum açma bağlamının KIMLIĞI | int | int |
| server_principal_name | server_principal_name_s | Geçerli oturum açma | döndürmeli | string |
| server_principal_sid | server_principal_sid_s | Geçerli oturum açma SID 'SI | ikili | string |
| session_id | session_id_d | Olayın gerçekleştiği oturumun KIMLIĞI | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Oturum için sunucu sorumlusu | döndürmeli | string |
| Ekstre | statement_s | Yürütülen T-SQL deyimleri (varsa) | nvarchar (4000) | string |
| başarılı | succeeded_s | Olayı tetikleyen eylemin başarılı olup olmadığını gösterir. Login ve Batch dışındaki olaylar için, bu yalnızca izin denetiminin başarılı veya başarısız olduğunu bildirir, işlem değildir. 1 = başarılı, 0 = başarısız | bit | string |
| target_database_principal_id | target_database_principal_id_d | Veritabanı sorumlusu verme/reddetme/Iptal etme işlemi üzerinde gerçekleştirilir. geçerli değilse 0 | int | int |
| target_database_principal_name | target_database_principal_name_s | İşlemin kullanıcısını hedefleyin. Geçerli değilse NULL | string | string |
| target_server_principal_id | target_server_principal_id_d | VERME/reddetme/Iptal etme işleminin gerçekleştirildiği sunucu sorumlusu. Uygulanabilir değilse 0 döndürür | int | int |
| target_server_principal_name | target_server_principal_name_s | Eylemin hedef oturum açması. Geçerli değilse NULL | döndürmeli | string |
| target_server_principal_sid | target_server_principal_sid_s | Hedef oturum açmanın SID 'SI. Geçerli değilse NULL | ikili | string |
| transaction_id | transaction_id_d | Yalnızca SQL Server (2016 ile başlayarak)-Azure SQL veritabanı için 0 | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Sp_audit_write bir bağımsız değişken olarak Kullanıcı tanımlı olay KIMLIĞI geçirildi. Sistem olayları için NULL (varsayılan) ve Kullanıcı tanımlı olay için sıfır olmayan değer. Daha fazla bilgi için bkz. [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Sp_audit_write için bir bağımsız değişken olarak geçirilen kullanıcı tanımlı bilgiler. Sistem olayları için NULL (varsayılan) ve Kullanıcı tanımlı olay için sıfır olmayan değer. Daha fazla bilgi için bkz. [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar (4000) | string |

## <a name="next-steps"></a>Sonraki adımlar

[Azure SQL veritabanı denetimi](auditing-overview.md)hakkında daha fazla bilgi edinin.