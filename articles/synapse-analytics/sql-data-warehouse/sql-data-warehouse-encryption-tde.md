---
title: Adanmış SQL havuzu için Saydam Veri Şifrelemesi (portal) (eski adıyla SQL DW)
description: Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) için Saydam Veri Şifrelemesi (TDE)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: da4be6f4bc8335e0976a0a4a87c4d232b2a2285f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676315"
---
# <a name="get-started-with-transparent-data-encryption-tde-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) için Saydam Veri Şifrelemesi (TDE) ile çalışmaya başlama

> [!div class="op_single_selector"]
>
> * [Güvenliğe genel bakış](sql-data-warehouse-overview-manage-security.md)
> * [Kimlik Doğrulaması](sql-data-warehouse-authentication.md)
> * [Şifreleme (portal)](sql-data-warehouse-encryption-tde.md)
> * [Şifreleme (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Gerekli İzinler

Saydam Veri Şifrelemesi (TDE) etkinleştirmek için, bir yönetici veya dbmanager rolünün bir üyesi olmanız gerekir.

## <a name="enabling-encryption"></a>Şifrelemeyi etkinleştirme

TDE ' yi etkinleştirmek için aşağıdaki adımları izleyin:

1. Veritabanını [Azure Portal](https://portal.azure.com) açın
2. Veritabanı dikey penceresinde **Ayarlar** düğmesine tıklayın.
3. **Saydam veri şifreleme** seçeneği ![ Portal ayarlarını seçin](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Üzerinde Portal  ayarlarını ayarlama ' yı seçin. ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Portal ayarlarını **Kaydet**' i seçin 
    ![ Kaydet](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Şifrelemeyi devre dışı bırakma

TDE ' yi devre dışı bırakmak için aşağıdaki adımları izleyin:

1. Veritabanını [Azure Portal](https://portal.azure.com) açın
2. Veritabanı dikey penceresinde **Ayarlar** düğmesine tıklayın.
3. **Saydam veri şifreleme** seçeneği ![ Portal ayarlarını seçin](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. **Devre dışı bırakma** ![ portalı ayarlarını seçin](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Portalın **kaydedileceği ilkeyi kaydet** seçeneğini belirleyin 
    ![ 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Şifreleme DMVs

Şifreleme Aşağıdaki DMVs ile onaylanır:

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
