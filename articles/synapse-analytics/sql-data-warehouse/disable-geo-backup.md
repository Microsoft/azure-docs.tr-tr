---
title: Coğrafi yedeklemeleri devre dışı bırak
description: Azure SYNAPSE Analytics 'te adanmış bir SQL Havuzu (eski adıyla SQL DW) için coğrafi yedeklemeleri devre dışı bırakma Kılavuzu
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98739150"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış bir SQL Havuzu (eski adıyla SQL DW) için coğrafi yedeklemeleri devre dışı bırakın

Bu makalede, adanmış SQL havuzunuzun (eskiden SQL DW) Azure portal coğrafi yedeklemeleri devre dışı bırakmayı öğreneceksiniz.

## <a name="disable-geo-backups-through-azure-portal"></a>Coğrafi yedeklemeleri Azure portal aracılığıyla devre dışı bırakma

Adanmış SQL havuzunuz (eski adıyla SQL DW) için coğrafi yedeklemeleri devre dışı bırakmak için aşağıdaki adımları izleyin:

> [!NOTE]
> Coğrafi yedeklemeleri devre dışı bırakırsanız, artık adanmış SQL havuzunuzu (eski adıyla SQL DW) başka bir Azure bölgesine kurtarmanız mümkün olmayacaktır. 
>

1. [Azure Portal](https://portal.azure.com/) hesabınızda oturum açın.
1. Coğrafi yedeklemeleri devre dışı bırakmak istediğiniz adanmış SQL Havuzu (eski adıyla SQL DW) kaynağını seçin. 
1. Sol taraftaki Gezinti panelindeki **Ayarlar** ' ın altında **coğrafi yedekleme ilkesi**' ni seçin.

   ![Coğrafi yedeklemeyi devre dışı bırak](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. Coğrafi yedeklemeleri devre dışı bırakmak için **devre dışı**' yı seçin. 

   ![Devre dışı coğrafi yedekleme](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. Ayarlarınızın kaydedildiğinden emin olmak için *Kaydet* ' i seçin. 

   ![Coğrafi yedekleme ayarlarını kaydet](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Var olan ayrılmış bir SQL havuzunu geri yükleme (eski adıyla SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Silinen ayrılmış bir SQL havuzunu geri yükleme (eski adıyla SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
