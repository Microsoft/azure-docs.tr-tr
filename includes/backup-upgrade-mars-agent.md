---
title: Azure Backup aracısını yükseltme
description: Bu bilgiler, Azure Backup aracısını yükseltmeniz ve yükseltmenin nereye indirileceğini açıklar.
services: backup
cloud: ''
suite: ''
author: v-amallick
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: v-amallick
ms.openlocfilehash: bf77103db93652e1df837f6b1032b5e53bd41e1f
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294093"
---
## <a name="upgrade-the-mars-agent"></a>MARS aracısını yükseltme

2.0.9083.0 altındaki Microsoft Azure Kurtarma Hizmetleri (MARS) aracısının sürümlerinin Azure Access Control hizmetine bağımlılığı vardır. MARS aracısına Azure Backup Aracısı da denir.

2018 ' de, Microsoft [Azure Access Control hizmetini kullanımdan kaldırılmıştır](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). 19 Mart 2018 ' den itibaren, 2.0.9083.0 altındaki MARS aracısının tüm sürümleri yedekleme hatalarıyla karşılaşacaktır. Yedekleme başarısızlıklarını önlemek veya çözmek için [Mars aracınızı en son sürüme yükseltin](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). MARS Aracısı yükseltmesi gerektiren sunucuları belirlemek için [Microsoft Azure kurtarma hizmetleri (mars) aracısını yükseltme](../articles/backup/upgrade-mars-agent.md)bölümündeki adımları izleyin.

MARS Aracısı, dosya ve klasörleri ve sistem durumu verilerini Azure 'a yedeklemek için kullanılır. System Center DPM ve Azure Backup Sunucusu verileri Azure 'a yedeklemek için MARS aracısını kullanın.
