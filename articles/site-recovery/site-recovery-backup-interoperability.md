---
title: Azure Backup ile Azure Site Recovery kullanma desteği
description: Azure Site Recovery ve Azure Backup birlikte nasıl kullanılabileceğine ilişkin bir genel bakış sağlar.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376210"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Azure Backup ile Site Recovery kullanma desteği

Bu makalede, [Azure Backup hizmetiyle](https://docs.microsoft.com/azure/backup/backup-overview)birlikte [Site Recovery hizmetini](site-recovery-overview.md) kullanma desteği özetlenmektedir.

**Eylem** | **Site Recovery desteği** | **Ayrıntılar**
--- | --- | ---
**Hizmetleri birlikte dağıtma** | Desteklenen | Hizmetler birlikte çalışabilir ve birlikte yapılandırılabilir.
**Dosya yedekleme/geri yükleme** | Desteklenen | Yedekleme ve çoğaltma bir VM için etkinleştirildiğinde ve yedeklemeler çekilirken, kaynak tarafı VM 'lerde veya VM grupları üzerinde dosya geri yükleme konusu yoktur. Çoğaltma, çoğaltma durumunda hiçbir değişiklik yapmadan her zamanki gibi devam eder.
**Disk geri yükleme** | Geçerli destek yok | Yedeklenen bir diski geri yüklerseniz, VM için çoğaltmayı yeniden devre dışı bırakıp yeniden etkinleştirmeniz gerekir.
**VM geri yükleme** | Geçerli destek yok | Bir VM 'yi veya VM grubunu geri yüklerseniz, VM için çoğaltmayı devre dışı bırakıp yeniden etkinleştirmeniz gerekir.  


