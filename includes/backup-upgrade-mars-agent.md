---
title: Azure Backup aracısını yükseltme
description: Bu bilgiler, Azure Backup aracısını yükseltmeniz ve yükseltmenin nereye indirileceğini açıklar.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: ea1295b08aa77a3e1a03d944ddbcbf37b6d17702
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71251547"
---
## <a name="upgrade-the-mars-agent"></a>MARS aracısını yükseltme

2\.0.9083.0 altına Microsoft Azure kurtarma hizmeti (MARS) aracısının sürümlerinin Azure Access Control hizmeti 'ne (ACS) bağımlılığı vardır. MARS aracısına Azure Backup Aracısı da denir. 2018 ' de Azure, [azure Access Control hizmeti 'ni (ACS) kullanımdan kaldırılmıştır](../articles/active-directory/develop/active-directory-acs-migration.md). 19 Mart 2018 ' den itibaren, 2.0.9083.0 altındaki MARS aracısının tüm sürümleri yedekleme hatalarıyla karşılaşacaktır. Yedekleme başarısızlıklarını önlemek veya çözmek için [Mars aracınızı en son sürüme yükseltin](https://go.microsoft.com/fwlink/?linkid=229525). MARS Aracısı yükseltmesi gerektiren sunucuları belirlemek için, [Mars aracılarını yükseltmek Için Yedekleme blogundan](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)adımları izleyin. MARS Aracısı, dosya ve klasörleri ve sistem durumu verilerini Azure 'a yedeklemek için kullanılır. System Center DPM ve Azure Backup Sunucusu verileri Azure 'a yedeklemek için MARS aracısını kullanın.
