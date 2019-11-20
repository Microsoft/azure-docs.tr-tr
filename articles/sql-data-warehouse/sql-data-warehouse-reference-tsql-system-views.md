---
title: Sistem görünümleri
description: Azure SQL veri ambarı 'nda desteklenen sistem görünümleri belgelerinin bağlantıları.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 06/13/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a8073084007d3174b92995c16785c3f8bc82b642
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692544"
---
# <a name="system-views-supported-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda desteklenen sistem görünümleri
Azure SQL veri ambarı 'nda desteklenen T-SQL deyimlerine yönelik belgelere bağlantılar.

## <a name="sql-data-warehouse-catalog-views"></a>SQL veri ambarı Katalog görünümleri
* [sys. PDW _column_distribution_properties](https://msdn.microsoft.com/library/mt204022.aspx)
* [sys. PDW _dağıtımları](https://msdn.microsoft.com/library/mt203892.aspx)
* [sys. PDW _ındex_mappings](https://msdn.microsoft.com/library/mt203912.aspx)
* [sys. PDW _loader_backup_run_details](https://msdn.microsoft.com/library/mt203877.aspx)
* [sys. PDW _loader_backup_çalıştırmaları](https://msdn.microsoft.com/library/mt203884.aspx)
* [sys. PDW _materialized_view_column_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-column-distribution-properties-transact-sql?view=azure-sqldw-latest) (Önizleme)
* [sys. PDW _materialized_view_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-distribution-properties-transact-sql?view=azure-sqldw-latest) (Önizleme)
* [sys. PDW _materialized_view_mappings](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-mappings-transact-sql?view=azure-sqldw-latest) (Önizleme)
* [sys. PDW _nodes_column_store_sözlükler](https://msdn.microsoft.com/library/mt203902.aspx)
* [sys. PDW _nodes_column_store_row_groups](https://msdn.microsoft.com/library/mt203880.aspx)
* [sys. PDW _nodes_column_store_segments](https://msdn.microsoft.com/library/mt203916.aspx)
* [sys. PDW _nodes_columns](https://msdn.microsoft.com/library/mt203881.aspx)
* [sys. PDW _nodes_ındexes](https://msdn.microsoft.com/library/mt203885.aspx)
* [sys. PDW _nodes_partitions](https://msdn.microsoft.com/library/mt203908.aspx)
* [sys. PDW _nodes_pdw_physical_databases](https://msdn.microsoft.com/library/mt203897.aspx)
* [sys. PDW _nodes_tables](https://msdn.microsoft.com/library/mt203886.aspx)
* [sys. PDW _replicated_table_cache_state](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql)
* [sys. PDW _table_distribution_properties](https://msdn.microsoft.com/library/mt203896.aspx)
* [sys. PDW _table_mappings](https://msdn.microsoft.com/library/mt203876.aspx)
* [sys. resource_governor_workload_groups](/sql/relational-databases/system-catalog-views/sys-resource-governor-workload-groups-transact-sql)
* [sys. workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql) (Önizleme)
* [sys. workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql) (Önizleme)

## <a name="sql-data-warehouse-dynamic-management-views-dmvs"></a>SQL veri ambarı dinamik yönetim görünümleri (DMVs)
* [sys. DM _pdw_dms_çekirdekler](https://msdn.microsoft.com/library/mt203911.aspx)
* [sys. DM _pdw_dms_external_work](https://msdn.microsoft.com/library/mt204024.aspx)
* [sys.dm_pdw_dms_workers](https://msdn.microsoft.com/library/mt203878.aspx)
* [sys. DM _pdw_errors](https://msdn.microsoft.com/library/mt203904.aspx)
* [sys. DM _pdw_exec_connections](https://msdn.microsoft.com/library/mt203882.aspx)
* [sys.dm_pdw_exec_requests](https://msdn.microsoft.com/library/mt203887.aspx)
* [sys. DM _pdw_exec_sessions](https://msdn.microsoft.com/library/mt203883.aspx)
* [sys. DM _pdw_hadoop_operations](https://msdn.microsoft.com/library/mt204032.aspx)
* [sys. DM _pdw_lock_wait](https://msdn.microsoft.com/library/mt203901.aspx)
* [sys. DM _pdw_nodes](https://msdn.microsoft.com/library/mt203907.aspx)
* [sys. DM _pdw_nodes_database_encryption_keys](https://msdn.microsoft.com/library/mt203922.aspx)
* [sys. DM _pdw_os_threads](https://msdn.microsoft.com/library/mt203917.aspx)
* [sys.dm_pdw_request_steps](https://msdn.microsoft.com/library/mt203913.aspx)
* [sys. DM _pdw_resource_wait](https://msdn.microsoft.com/library/mt203906.aspx)
* [sys.dm_pdw_sql_requests](https://msdn.microsoft.com/library/mt203889.aspx)
* [sys. DM _pdw_sys_ınfo](https://msdn.microsoft.com/library/mt203900.aspx)
* [sys. DM _pdw_wait_stats](https://msdn.microsoft.com/library/mt203909.aspx)
* [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)

## <a name="sql-server-dmvs-applicable-to-sql-data-warehouse"></a>SQL veri ambarı 'na uygulanan SQL Server DMVs
Aşağıdaki DMVs, SQL veri ambarı için geçerlidir, ancak **ana** veritabanına bağlanarak yürütülmesi gerekir.

* [sys. database_service_objectives](https://msdn.microsoft.com/library/mt712619.aspx)
* [sys. DM _operation_status](https://msdn.microsoft.com/library/dn270022.aspx)
* [sys. fn_helpcollations ()](https://msdn.microsoft.com/library/ms187963.aspx)

## <a name="sql-server-catalog-views"></a>SQL Server katalog görünümleri
* [sys. All _columns](https://msdn.microsoft.com/library/ms177522.aspx)
* [sys. All _objects](https://msdn.microsoft.com/library/ms178618.aspx)
* [sys. All _parameters](https://msdn.microsoft.com/library/ms190340.aspx)
* [sys. All _sql_modules](https://msdn.microsoft.com/library/ms184389.aspx)
* [sys. All _views](https://msdn.microsoft.com/library/ms189510.aspx)
* [sys. Assemblies](https://msdn.microsoft.com/library/ms189790.aspx)
* [sys. assembly_modules](https://msdn.microsoft.com/library/ms180052.aspx)
* [sys. assembly_types](https://msdn.microsoft.com/library/ms178020.aspx)
* [sys. Certificates](https://msdn.microsoft.com/library/ms189774.aspx)
* [sys. check_constraints](https://msdn.microsoft.com/library/ms187388.aspx)
* [sys. Columns](https://msdn.microsoft.com/library/ms176106.aspx)
* [sys. computed_columns](https://msdn.microsoft.com/library/ms188744.aspx)
* [sys. Credentials](https://msdn.microsoft.com/library/ms189745.aspx)
* [sys. data_spaces](https://msdn.microsoft.com/library/ms190289.aspx)
* [sys. database_credentials](https://msdn.microsoft.com/library/mt270282.aspx)
* [sys. database_files](https://msdn.microsoft.com/library/ms174397.aspx)
* [sys. database_permissions](https://msdn.microsoft.com/library/ms188367.aspx)
* [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx)
* [sys. database_query_store_options](/sql/relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql?view=azure-sqldw-latest)
* [sys. database_role_members](https://msdn.microsoft.com/library/ms189780.aspx)
* [sys. databases](https://msdn.microsoft.com/library/ms178534.aspx)
* [sys. default_constraints](https://msdn.microsoft.com/library/ms173758.aspx)
* [sys. external_data_sources](https://msdn.microsoft.com/library/dn935019.aspx)
* [sys. external_file_formats](https://msdn.microsoft.com/library/dn935025.aspx)
* [sys. external_tables](https://msdn.microsoft.com/library/dn935029.aspx)
* [sys. dosya grupları](https://msdn.microsoft.com/library/ms187782.aspx)
* [sys. foreign_key_columns](https://msdn.microsoft.com/library/ms186306.aspx)
* [sys. foreign_keys](https://msdn.microsoft.com/library/ms189807.aspx)
* [sys. identity_columns](https://msdn.microsoft.com/library/ms187334.aspx)
* [sys. index_columns](https://msdn.microsoft.com/library/ms175105.aspx)
* [sys. Indexes](https://msdn.microsoft.com/library/ms173760.aspx)
* [sys. Key _kısıtlamaları](https://msdn.microsoft.com/library/ms174321.aspx)
* [sys. numbered_procedures](https://msdn.microsoft.com/library/ms179865.aspx)
* [sys. Objects](https://msdn.microsoft.com/library/ms190324.aspx)
* [sys. Parameters](https://msdn.microsoft.com/library/ms176074.aspx)
* [sys. partition_functions](https://msdn.microsoft.com/library/ms187381.aspx)
* [sys. partition_parameters](https://msdn.microsoft.com/library/ms175054.aspx)
* [sys. partition_range_values](https://msdn.microsoft.com/library/ms187780.aspx)
* [sys. partition_schemes](https://msdn.microsoft.com/library/ms189752.aspx)
* [sys. partitions](https://msdn.microsoft.com/library/ms175012.aspx)
* [sys. procedures](https://msdn.microsoft.com/library/ms188737.aspx)
* [sys. query_context_settings](/sql/relational-databases/system-catalog-views/sys-query-context-settings-transact-sql?view=azure-sqldw-latest)
* [sys. query_store_plan](/sql/relational-databases/system-catalog-views/sys-query-store-plan-transact-sql?view=azure-sqldw-latest)
* [sys. query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=azure-sqldw-latest)
* [sys. query_store_query_text](/sql/relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql?view=azure-sqldw-latest)
* [sys. query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql?view=azure-sqldw-latest)
* [sys. query_store_runtime_stats_interval](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-interval-transact-sql?view=azure-sqldw-latest)
* [sys. schemas](https://msdn.microsoft.com/library/ms176011.aspx)
* [sys. securable_classes](https://msdn.microsoft.com/library/ms408301.aspx)
* [sys. SQL _expression_dependencies](https://msdn.microsoft.com/library/bb677315.aspx)
* [sys. SQL _modülleri](https://msdn.microsoft.com/library/ms175081.aspx)
* [sys. stats](https://msdn.microsoft.com/library/ms177623.aspx)
* [sys. stats_columns](https://msdn.microsoft.com/library/ms187340.aspx)
* [sys. symmetric_keys](https://msdn.microsoft.com/library/ms189446.aspx)
* [sys. eşanlamlılar](https://msdn.microsoft.com/library/ms189458.aspx)
* [sys. syscharsets](https://msdn.microsoft.com/library/ms190300.aspx)
* [sys. syscolumns](https://msdn.microsoft.com/library/ms186816.aspx)
* [sys. sysdatabases](https://msdn.microsoft.com/library/ms179900.aspx)
* [sys. syslanguages](https://msdn.microsoft.com/library/ms190303.aspx)
* [sys. sysobjects](https://msdn.microsoft.com/library/ms177596.aspx)
* [sys. sysreferences](https://msdn.microsoft.com/library/ms186900.aspx)
* [sys. system_columns](https://msdn.microsoft.com/library/ms178596.aspx)
* [sys. system_objects](https://msdn.microsoft.com/library/ms173551.aspx)
* [sys. system_parameters](https://msdn.microsoft.com/library/ms174367.aspx)
* [sys. system_sql_modules](https://msdn.microsoft.com/library/ms188034.aspx)
* [sys. system_views](https://msdn.microsoft.com/library/ms187764.aspx)
* [sys. systypes](https://msdn.microsoft.com/library/ms175109.aspx)
* [sys. sysusers](https://msdn.microsoft.com/library/ms179871.aspx)
* [sys. Tables](https://msdn.microsoft.com/library/ms187406.aspx)
* [sys. Types](https://msdn.microsoft.com/library/ms188021.aspx)
* [sys. views](https://msdn.microsoft.com/library/ms190334.aspx)

## <a name="sql-server-dmvs-available-in-sql-data-warehouse"></a>SQL veri ambarı 'nda SQL Server DMVs Ile kullanılabilir
SQL veri ambarı SQL Server dinamik yönetim görünümlerinin çoğunu (DMVs) kullanıma sunar. SQL veri ambarı 'nda sorgulandığında bu görünümler, dağıtımlar üzerinde çalışan SQL veritabanlarının durumunu raporluyor.

SQL veri ambarı ve analiz platformu sisteminin paralel veri ambarı (PDW) aynı sistem görünümlerini kullanır. Her DMV, Işlem düğümünün tanımlayıcısı olan pdw_node_id adlı bir sütun içerir. 

> [!NOTE]
> Bu görünümleri kullanmak için, aşağıdaki tabloda gösterildiği gibi ada ' pdw_nodes_ ' ekleyin:
> 
> 

| SQL Data Warehouse 'da DMV adı | SQL Server Transact-SQL makalesi|
|:--- |:--- |
| sys. DM _pdw_nodes_db_column_store_row_group_physical_stats | [sys. DM _db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql)| 
| sys. DM _pdw_nodes_db_column_store_row_group_operational_stats | [sys. DM _db_column_store_row_group_operational_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-operational-stats-transact-sql)| 
| sys. DM _pdw_nodes_db_file_space_usage |[sys. DM _db_file_space_usage](https://msdn.microsoft.com/library/ms174412.aspx) |
| sys. DM _pdw_nodes_db_ındex_usage_stats |[sys. DM _db_ındex_usage_stats](https://msdn.microsoft.com/library/ms188755.aspx) |
| sys. DM _pdw_nodes_db_partition_stats |[sys. DM _db_partition_stats](https://msdn.microsoft.com/library/ms187737.aspx) |
| sys. DM _pdw_nodes_db_session_space_usage |[sys. DM _db_session_space_usage](https://msdn.microsoft.com/library/ms187938.aspx) |
| sys. DM _pdw_nodes_db_task_space_usage |[sys. DM _db_task_space_usage](https://msdn.microsoft.com/library/ms190288.aspx) |
| sys. DM _pdw_nodes_exec_background_job_queue |[sys. DM _exec_background_job_queue](https://msdn.microsoft.com/library/ms173512.aspx) |
| sys. DM _pdw_nodes_exec_background_job_queue_stats |[sys. DM _exec_background_job_queue_stats](https://msdn.microsoft.com/library/ms176059.aspx) |
| sys. DM _pdw_nodes_exec_cached_planlar |[sys. DM _exec_cached_planlar](https://msdn.microsoft.com/library/ms187404.aspx) |
| sys. DM _pdw_nodes_exec_connections |[sys. DM _exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) |
| sys. DM _pdw_nodes_exec_procedure_stats |[sys. DM _exec_procedure_stats](https://msdn.microsoft.com/library/cc280701.aspx) |
| sys. DM _pdw_nodes_exec_query_memory_verdiğini |[sys. DM _exec_query_memory_yetkilendirme](https://msdn.microsoft.com/library/ms365393.aspx) |
| sys. DM _pdw_nodes_exec_query_optimizer_ınfo |[sys. DM _exec_query_optimizer_ınfo](https://msdn.microsoft.com/library/ms175002.aspx) |
| sys. DM _pdw_nodes_exec_query_resource_semafor |[sys. DM _exec_query_resource_semaforlar](https://msdn.microsoft.com/library/ms366321.aspx) |
| sys. DM _pdw_nodes_exec_query_stats |[sys. DM _exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) |
| sys. DM _pdw_nodes_exec_requests |[sys. DM _exec_requests](https://msdn.microsoft.com/library/ms177648.aspx) |
| sys. DM _pdw_nodes_exec_sessions |[sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) |
| sys. DM _pdw_nodes_io_pending_io_requests |[sys. DM _io_pending_io_requests](https://msdn.microsoft.com/library/ms188762.aspx) |
| sys. DM _pdw_nodes_io_virtual_file_stats |[sys. DM _io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) |
| sys. DM _pdw_nodes_os_buffer_descriptor |[sys. DM _os_buffer_descriptor](https://msdn.microsoft.com/library/ms173442.aspx) |
| sys. DM _pdw_nodes_os_child_ınstances |[sys. DM _os_child_ınstances](https://msdn.microsoft.com/library/ms165698.aspx) |
| sys. DM _pdw_nodes_os_cluster_nodes |[sys. DM _os_cluster_nodes](https://msdn.microsoft.com/library/ms187341.aspx) |
| sys. DM _pdw_nodes_os_dispatcher_pools |[sys. DM _os_dispatcher_pools](https://msdn.microsoft.com/library/bb630336.aspx) |
| sys. DM _pdw_nodes_os_dispatchers |Transact-SQL belgeleri kullanılamaz. |
| sys. DM _pdw_nodes_os_konakları |[sys. DM _os_konakları](https://msdn.microsoft.com/library/ms187800.aspx) |
| sys. DM _pdw_nodes_os_latch_stats |[sys. DM _os_latch_stats](https://msdn.microsoft.com/library/ms175066.aspx) |
| sys. DM _pdw_nodes_os_memory_aracılar |[sys. DM _os_memory_aracılar](https://msdn.microsoft.com/library/bb522548.aspx) |
| sys. DM _pdw_nodes_os_memory_cache_clock_eller |[sys. DM _os_memory_cache_clock_eller](https://msdn.microsoft.com/library/ms173786.aspx) |
| sys. DM _pdw_nodes_os_memory_cache_counters |[sys. DM _os_memory_cache_counters](https://msdn.microsoft.com/library/ms188760.aspx) |
| sys. DM _pdw_nodes_os_memory_cache_entries |[sys. DM _os_memory_cache_entries](https://msdn.microsoft.com/library/ms189488.aspx) |
| sys. DM _pdw_nodes_os_memory_cache_hash_tables |[sys. DM _os_memory_cache_hash_tables](https://msdn.microsoft.com/library/ms182388.aspx) |
| sys. DM _pdw_nodes_os_memory_memks |[sys. DM _os_memory_memks](https://msdn.microsoft.com/library/ms175019.aspx) |
| sys. DM _pdw_nodes_os_memory_node_access_stats |Transact-SQL belgeleri kullanılamaz. |
| sys. DM _pdw_nodes_os_memory_nodes |[sys. DM _os_memory_nodes](https://msdn.microsoft.com/library/bb510622.aspx) |
| sys. DM _pdw_nodes_os_memory_objects |[sys. DM _os_memory_objects](https://msdn.microsoft.com/library/ms179875.aspx) |
| sys. DM _pdw_nodes_os_memory_pools |[sys. DM _os_memory_pools](https://msdn.microsoft.com/library/ms175022.aspx) |
| sys. DM _pdw_nodes_os_nodes |[sys. DM _os_nodes](https://msdn.microsoft.com/library/bb510628.aspx) |
| sys. DM _pdw_nodes_os_performance_counters |[sys. DM _os_performance_counters](https://msdn.microsoft.com/library/ms187743.aspx) |
| sys. DM _pdw_nodes_os_process_memory |[sys. DM _os_process_memory](https://msdn.microsoft.com/library/bb510747.aspx) |
| sys. DM _pdw_nodes_os_schedulers |[sys. DM _os_schedulers](https://msdn.microsoft.com/library/ms177526.aspx) |
| sys. DM _pdw_nodes_os_spinlock_stats |Transact-SQL belgeleri kullanılamaz. |
| sys. DM _pdw_nodes_os_sys_ınfo |[sys. DM _os_sys_ınfo](https://msdn.microsoft.com/library/ms175048.aspx) |
| sys. DM _pdw_nodes_os_sys_memory |[sys. DM _os_memory_nodes](https://msdn.microsoft.com/library/bb510622.aspx) |
| sys. DM _pdw_nodes_os_tasks |[sys. DM _os_tasks](https://msdn.microsoft.com/library/ms174963.aspx) |
| sys. DM _pdw_nodes_os_threads |[sys. DM _os_threads](https://msdn.microsoft.com/library/ms187818.aspx) |
| sys. DM _pdw_nodes_os_virtual_address_dump |[sys. DM _os_virtual_address_dump](https://msdn.microsoft.com/library/ms186294.aspx) |
| sys. DM _pdw_nodes_os_wait_stats |[sys. DM _os_wait_stats](https://msdn.microsoft.com/library/ms179984.aspx) |
| sys. DM _pdw_nodes_os_waiting_tasks |[sys. DM _os_waiting_tasks](https://msdn.microsoft.com/library/ms188743.aspx) |
| sys. DM _pdw_nodes_os_çalışanlar |[sys. DM _os_çalışanlar](https://msdn.microsoft.com/library/ms178626.aspx) |
| sys. DM _pdw_nodes_tran_active_snapshot_database_transactions |[sys. DM _tran_active_snapshot_database_transactions](https://msdn.microsoft.com/library/ms180023.aspx) |
| sys. DM _pdw_nodes_tran_active_transactions |[sys. DM _tran_active_transactions](https://msdn.microsoft.com/library/ms174302.aspx) |
| sys. DM _pdw_nodes_tran_commit_table |[sys. DM _tran_commit_table](https://msdn.microsoft.com/library/cc645959.aspx) |
| sys. DM _pdw_nodes_tran_current_snapshot |[sys. DM _tran_current_snapshot](https://msdn.microsoft.com/library/ms184390.aspx) |
| sys. DM _pdw_nodes_tran_current_transaction |[sys. DM _tran_current_transaction](https://msdn.microsoft.com/library/ms186327.aspx) |
| sys. DM _pdw_nodes_tran_database_transactions |[sys. DM _tran_database_transactions](https://msdn.microsoft.com/library/ms186957.aspx) |
| sys. DM _pdw_nodes_tran_kilitler |[sys. DM _tran_kilitleri](https://msdn.microsoft.com/library/ms190345.aspx) |
| sys. DM _pdw_nodes_tran_session_transactions |[sys. DM _tran_session_transactions](https://msdn.microsoft.com/library/ms188739.aspx) |
| sys. DM _pdw_nodes_tran_top_version_generıcılar |[sys. DM _tran_top_version_generıcılar](https://msdn.microsoft.com/library/ms188778.aspx) |

## <a name="sql-server-2016-polybase-dmvs-available-in-sql-data-warehouse"></a>SQL Server 2016 PolyBase DMVs, SQL veri ambarı 'nda kullanılabilir
Aşağıdaki DMVs, SQL veri ambarı için geçerlidir, ancak **ana** veritabanına bağlanarak yürütülmesi gerekir.

* [sys. DM _exec_compute_node_errors](https://msdn.microsoft.com/library/mt146380.aspx)
* [sys. DM _exec_compute_node_status](https://msdn.microsoft.com/library/mt146382.aspx)
* [sys. DM _exec_compute_nodes](https://msdn.microsoft.com/library/mt130700.aspx)
* [sys. DM _exec_distributed_request_steps](https://msdn.microsoft.com/library/mt130701.aspx)
* [sys. DM _exec_distributed_requests](https://msdn.microsoft.com/library/mt146385.aspx)
* [sys. DM _exec_distributed_sql_requests](https://msdn.microsoft.com/library/mt146390.aspx)
* [sys. DM _exec_dms_services](https://msdn.microsoft.com/library/mt146374.aspx)
* [sys. DM _exec_dms_çalışanlar](https://msdn.microsoft.com/library/mt146392.aspx)
* [sys. DM _exec_external_operations](https://msdn.microsoft.com/library/mt146391.aspx)
* [sys. DM _exec_external_work](https://msdn.microsoft.com/library/mt146375.aspx)

## <a name="sql-server-information_schema-views"></a>SQL Server INFORMATION_SCHEMA görünümleri
* [CHECK_CONSTRAINTS](https://msdn.microsoft.com/library/ms189772.aspx)
* [SÜTUNLARDAN](https://msdn.microsoft.com/library/ms188348.aspx)
* [PARAMETRELERE](https://msdn.microsoft.com/library/ms173796.aspx)
* [ÇALıŞTıRMASıNı](https://msdn.microsoft.com/library/ms188757.aspx)
* [ŞEMALARıN SERILEŞTIRILMESI](https://msdn.microsoft.com/library/ms182642.aspx)
* [TAKVIMLERI](https://msdn.microsoft.com/library/ms186224.aspx)
* [VIEW_COLUMN_USAGE](https://msdn.microsoft.com/library/ms190492.aspx)
* [VIEW_TABLE_USAGE](https://msdn.microsoft.com/library/ms173869.aspx)
* [Görünümler](https://msdn.microsoft.com/library/ms181381.aspx)

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla başvuru bilgisi için bkz. Azure SQL veri [ambarı 'Nda t-SQL deyimleri](sql-data-warehouse-reference-tsql-statements.md)ve [Azure SQL veri ambarı 'NDA t-SQL dil öğeleri](sql-data-warehouse-reference-tsql-language-elements.md).
