---
title: SYNAPSE SQL 'de desteklenen sistem görünümleri
description: SYNAPSE SQL 'de desteklenen sistem görünümleri belgelerinin bağlantıları.
author: filippopovic
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: reference
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: da033523e95d89ba6fd6b0eec780b45f7587bc55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666277"
---
# <a name="system-views-supported-in-synapse-sql"></a>SYNAPSE SQL 'de desteklenen sistem görünümleri

SYNAPSE SQL 'de desteklenen T-SQL deyimlerinin belgelerinin bağlantıları.

> [!NOTE]
> SYNAPSE sunucusuz SQL havuzu yalnızca SQL Server katalog görünümlerini destekler.  

## <a name="dedicated-sql-pool-and-serverless-sql-pool-catalog-views"></a>Adanmış SQL havuzu ve sunucusuz SQL havuzu Katalog görünümleri

* [sys.pdw_column_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-column-distribution-properties-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_distributions](/sql/relational-databases/system-catalog-views/sys-pdw-distributions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_index_mappings](/sql/relational-databases/system-catalog-views/sys-pdw-index-mappings-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_loader_backup_run_details](/sql/relational-databases/system-catalog-views/sys-pdw-loader-backup-run-details-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_loader_backup_runs](/sql/relational-databases/system-catalog-views/sys-pdw-loader-backup-runs-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_materialized_view_column_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-column-distribution-properties-transact-sql?view=azure-sqldw-latest&preserve-view=true) (Önizleme)
* [sys.pdw_materialized_view_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-distribution-properties-transact-sql?view=azure-sqldw-latest&preserve-view=true) (Önizleme)
* [sys.pdw_materialized_view_mappings](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-mappings-transact-sql?view=azure-sqldw-latest&preserve-view=true) (Önizleme)
* [sys.pdw_nodes_column_store_dictionaries](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-column-store-dictionaries-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_nodes_column_store_row_groups](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-column-store-row-groups-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_nodes_column_store_segments](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-column-store-segments-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_nodes_columns](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_nodes_indexes](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-indexes-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_nodes_partitions](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-partitions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_nodes_pdw_physical_databases](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-pdw-physical-databases-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_nodes_tables](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_permanent_table_mappings](/sql/relational-databases/system-catalog-views/sys-pdw-permanent-table-mappings-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_table_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-table-distribution-properties-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.pdw_table_mappings](/sql/relational-databases/system-catalog-views/sys-pdw-table-mappings-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.resource_governor_workload_groups](/sql/relational-databases/system-catalog-views/sys-resource-governor-workload-groups-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest&preserve-view=true) (Önizleme)
* [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest&preserve-view=true) (Önizleme)

## <a name="dedicated-sql-pool-dynamic-management-views-dmvs"></a>Adanmış SQL havuzu dinamik yönetim görünümleri (DMVs)

* [sys.dm_pdw_dms_cores](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-cores-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_dms_external_work](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-external-work-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_errors](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-errors-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-connections-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_hadoop_operations](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-hadoop-operations-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_lock_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-lock-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_os_threads](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-os-threads-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_resource_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-resource-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_sys_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sys-info-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-wait-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="sql-server-dmvs-applicable-to-dedicated-sql-pool"></a>Özel SQL havuzu için geçerli SQL Server DMVs

Aşağıdaki DMVs, adanmış SQL havuzu için geçerlidir, ancak **ana** veritabanına bağlanarak yürütülmesi gerekir.

* [sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?view=azure-sqldw-latest&preserve-view=true)
* [sys.fn_helpcollations ()](/sql/relational-databases/system-functions/sys-fn-helpcollations-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="sql-server-catalog-views"></a>SQL Server katalog görünümleri

* [sys.all_columns](/sql/relational-databases/system-catalog-views/sys-all-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.all_objects](/sql/relational-databases/system-catalog-views/sys-all-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.all_parameters](/sql/relational-databases/system-catalog-views/sys-all-parameters-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.all_sql_modules](/sql/relational-databases/system-catalog-views/sys-all-sql-modules-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.all_views](/sql/relational-databases/system-catalog-views/sys-all-views-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. Assemblies](/sql/relational-databases/system-catalog-views/sys-assemblies-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.assembly_modules](/sql/relational-databases/system-catalog-views/sys-assembly-modules-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.assembly_types](/sql/relational-databases/system-catalog-views/sys-assembly-types-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. Certificates](/sql/relational-databases/system-catalog-views/sys-certificates-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.check_constraints](/sql/relational-databases/system-catalog-views/sys-check-constraints-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.computed_columns](/sql/relational-databases/system-catalog-views/sys-computed-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. Credentials](/sql/relational-databases/system-catalog-views/sys-credentials-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.data_spaces](/sql/relational-databases/system-catalog-views/sys-data-spaces-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.database_credentials](/sql/relational-databases/system-catalog-views/sys-database-credentials-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.database_permissions](/sql/relational-databases/system-catalog-views/sys-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.database_query_store_options](/sql/relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.database_role_members](/sql/relational-databases/system-catalog-views/sys-database-role-members-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.default_constraints](/sql/relational-databases/system-catalog-views/sys-default-constraints-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.external_data_sources](/sql/relational-databases/system-catalog-views/sys-external-data-sources-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.external_file_formats](/sql/relational-databases/system-catalog-views/sys-external-file-formats-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.external_tables](/sql/relational-databases/system-catalog-views/sys-external-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. dosya grupları](/sql/relational-databases/system-catalog-views/sys-filegroups-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.foreign_key_columns](/sql/relational-databases/system-catalog-views/sys-foreign-key-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.foreign_keys](/sql/relational-databases/system-catalog-views/sys-foreign-keys-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. Indexes](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.key_constraints](/sql/relational-databases/system-catalog-views/sys-key-constraints-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.numbered_procedures](/sql/relational-databases/system-catalog-views/sys-numbered-procedures-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. Parameters](/sql/relational-databases/system-catalog-views/sys-parameters-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.partition_functions](/sql/relational-databases/system-catalog-views/sys-partition-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.partition_parameters](/sql/relational-databases/system-catalog-views/sys-partition-parameters-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.partition_range_values](/sql/relational-databases/system-catalog-views/sys-partition-range-values-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.partition_schemes](/sql/relational-databases/system-catalog-views/sys-partition-schemes-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. partitions](/sql/relational-databases/system-catalog-views/sys-partitions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. procedures](/sql/relational-databases/system-catalog-views/sys-procedures-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.query_context_settings](/sql/relational-databases/system-catalog-views/sys-query-context-settings-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.query_store_plan](/sql/relational-databases/system-catalog-views/sys-query-store-plan-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.query_store_query_text](/sql/relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.query_store_runtime_stats_interval](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-interval-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. schemas](/sql/relational-databases/system-catalog-views/schemas-catalog-views-sys-schemas?view=azure-sqldw-latest&preserve-view=true)
* [sys.securable_classes](/sql/relational-databases/system-catalog-views/sys-securable-classes-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.sql_expression_dependencies](/sql/relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.sql_modules](/sql/relational-databases/system-catalog-views/sys-sql-modules-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.symmetric_keys](/sql/relational-databases/system-catalog-views/sys-symmetric-keys-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. eşanlamlılar](/sql/relational-databases/system-catalog-views/sys-synonyms-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.sysdeğiştirmeye](/sql/relational-databases/system-compatibility-views/sys-syscharsets-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.syssütunları](/sql/relational-databases/system-compatibility-views/sys-syscolumns-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.sysveritabanları](/sql/relational-databases/system-compatibility-views/sys-sysdatabases-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.sysdilleri](/sql/relational-databases/system-compatibility-views/sys-syslanguages-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.sysnesneleri](/sql/relational-databases/system-compatibility-views/sys-sysobjects-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.sysbaşvuruları](/sql/relational-databases/system-compatibility-views/sys-sysreferences-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.system_columns](/sql/relational-databases/system-catalog-views/sys-system-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.system_objects](/sql/relational-databases/system-catalog-views/sys-system-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.system_parameters](/sql/relational-databases/system-catalog-views/sys-system-parameters-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.system_sql_modules](/sql/relational-databases/system-catalog-views/sys-system-sql-modules-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.system_views](/sql/relational-databases/system-catalog-views/sys-system-views-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.systürleri](/sql/relational-databases/system-compatibility-views/sys-systypes-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.syskullanıcılar](/sql/relational-databases/system-compatibility-views/sys-sysusers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. Types](/sql/relational-databases/system-catalog-views/sys-types-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys. views](/sql/relational-databases/system-catalog-views/sys-views-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="sql-server-dmvs-available-in-dedicated-sql-pool"></a>SQL Server DMVs adanmış SQL havuzunda kullanılabilir

SQL havuzu SQL Server dinamik yönetim görünümlerinin çoğunu (DMVs) kullanıma sunar. Adanmış SQL havuzunda sorgulandığında bu görünümler, dağıtımlarda çalışan SQL veritabanlarının durumunu raporluyor.

SQL havuzu ve Analytics Platform sisteminin paralel veri ambarı (PDW) aynı sistem görünümlerini kullanır. Her DMV, Işlem düğümünün tanımlayıcısı olan pdw_node_id adlı bir sütuna sahiptir.

> [!NOTE]
> Bu görünümleri kullanmak için, aşağıdaki tabloda gösterildiği gibi ada ' pdw_nodes_ ' ekleyin:

| Adanmış SQL havuzunda DMV adı | SQL Server Transact-SQL makalesi|
|:--- |:--- |
| sys.dm_pdw_nodes_db_column_store_row_group_physical_stats | [sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true)|
| sys.dm_pdw_nodes_db_column_store_row_group_operational_stats | [sys.dm_db_column_store_row_group_operational_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-operational-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true)|
| sys.dm_pdw_nodes_db_file_space_usage |[sys.dm_db_file_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-file-space-usage-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_db_index_usage_stats |[sys.dm_db_index_usage_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-index-usage-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_db_partition_stats |[sys.dm_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_db_session_space_usage |[sys.dm_db_session_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-session-space-usage-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_db_task_space_usage |[sys.dm_db_task_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-task-space-usage-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_exec_background_job_queue |[sys.dm_exec_background_job_queue](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-background-job-queue-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_exec_background_job_queue_stats |[sys.dm_exec_background_job_queue_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-background-job-queue-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_exec_cached_plans |[sys.dm_exec_cached_plans](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_exec_connections |[sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_exec_procedure_stats |[sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_exec_query_memory_grants |[sys.dm_exec_query_memory_grants](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-memory-grants-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_exec_query_optimizer_info |[sys.dm_exec_query_optimizer_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-optimizer-info-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_exec_query_resource_semaphores |[sys.dm_exec_query_resource_semaphores](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-resource-semaphores-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_exec_query_stats |[sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_exec_requests |[sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_exec_sessions |[sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_io_pending_io_requests |[sys.dm_io_pending_io_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-pending-io-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_io_virtual_file_stats |[sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_buffer_descriptors |[sys.dm_os_buffer_descriptors](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-buffer-descriptors-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_child_instances |[sys.dm_os_child_instances](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-child-instances-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_cluster_nodes |[sys.dm_os_cluster_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-cluster-nodes-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_dispatcher_pools |[sys.dm_os_dispatcher_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-dispatcher-pools-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_dispatchers |Transact-SQL belgeleri kullanılamaz. |
| sys.dm_pdw_nodes_os_hosts |[sys.dm_os_hosts](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-hosts-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_latch_stats |[sys.dm_os_latch_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-latch-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_memory_brokers |[sys.dm_os_memory_brokers](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-brokers-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_memory_cache_clock_hands |[sys.dm_os_memory_cache_clock_hands](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-cache-clock-hands-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_memory_cache_counters |[sys.dm_os_memory_cache_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-cache-counters-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_memory_cache_entries |[sys.dm_os_memory_cache_entries](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-cache-entries-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_memory_cache_hash_tables |[sys.dm_os_memory_cache_hash_tables](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-cache-hash-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_memory_clerks |[sys.dm_os_memory_clerks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_memory_node_access_stats |Transact-SQL belgeleri kullanılamaz. |
| sys.dm_pdw_nodes_os_memory_nodes |[sys.dm_os_memory_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-nodes-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_memory_objects |[sys.dm_os_memory_objects](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_memory_pools |[sys.dm_os_memory_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-pools-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_nodes |[sys.dm_os_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-nodes-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_performance_counters |[sys.dm_os_performance_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_process_memory |[sys.dm_os_process_memory](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-process-memory-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_schedulers |[sys.dm_os_schedulers](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-schedulers-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_spinlock_stats |Transact-SQL belgeleri kullanılamaz. |
| sys.dm_pdw_nodes_os_sys_info |[sys.dm_os_sys_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_sys_memory |[sys.dm_os_memory_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-nodes-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_tasks |[sys.dm_os_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_threads |[sys.dm_os_threads](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-threads-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_virtual_address_dump |[sys.dm_os_virtual_address_dump](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-virtual-address-dump-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_wait_stats |[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_waiting_tasks |[sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_os_workers |[sys.dm_os_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_tran_active_snapshot_database_transactions |[sys.dm_tran_active_snapshot_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-snapshot-database-transactions-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_tran_active_transactions |[sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_tran_commit_table |[sys.dm_tran_commit_table](/sql/relational-databases/system-dynamic-management-views/change-tracking-sys-dm-tran-commit-table?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_tran_current_snapshot |[sys.dm_tran_current_snapshot](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-current-snapshot-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_tran_current_transaction |[sys.dm_tran_current_transaction](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-current-transaction-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_tran_database_transactions |[sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_tran_locks |[sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_tran_session_transactions |[sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| sys.dm_pdw_nodes_tran_top_version_generators |[sys.dm_tran_top_version_generators](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-top-version-generators-transact-sql?view=azure-sqldw-latest&preserve-view=true) |

## <a name="sql-server-2016-polybase-dmvs-available-in-dedicated-sql-pool"></a>SQL Server 2016 PolyBase DMVs, adanmış SQL havuzunda kullanılabilir

Aşağıdaki DMVs, adanmış SQL havuzu için geçerlidir, ancak **ana** veritabanına bağlanarak yürütülmesi gerekir.

* [sys.dm_exec_compute_node_errors](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-compute-node-errors-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_exec_compute_node_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-compute-node-status-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_exec_compute_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-compute-nodes-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_exec_distributed_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-distributed-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_exec_distributed_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-distributed-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_exec_distributed_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-distributed-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_exec_dms_services](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-dms-services-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_exec_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_exec_external_operations](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-external-operations-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_exec_external_work](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-external-work-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="sql-server-information_schema-views"></a>SQL Server INFORMATION_SCHEMA görünümleri

* [CHECK_CONSTRAINTS](/sql/relational-databases/system-information-schema-views/check-constraints-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [SÜTUNLARDAN](/sql/relational-databases/system-information-schema-views/columns-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [PARAMETRELERE](/sql/relational-databases/system-information-schema-views/parameters-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [ÇALıŞTıRMASıNı](/sql/relational-databases/system-information-schema-views/routines-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [ŞEMALARıN SERILEŞTIRILMESI](/sql/relational-databases/system-information-schema-views/schemata-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [TAKVIMLERI](/sql/relational-databases/system-information-schema-views/tables-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [VIEW_COLUMN_USAGE](/sql/relational-databases/system-information-schema-views/view-column-usage-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [VIEW_TABLE_USAGE](/sql/relational-databases/system-information-schema-views/view-table-usage-transact-sql?view=azure-sqldw-latest&preserve-view=true)
* [Görünümler](/sql/relational-databases/system-information-schema-views/views-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla başvuru bilgisi için bkz. [t-SQL deyimlerine SYNAPSE SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-language-elements.md)ve [t-SQL DIL öğeleri SYNAPSE SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md).

