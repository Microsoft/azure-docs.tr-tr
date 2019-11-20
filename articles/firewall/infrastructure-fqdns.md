---
title: Azure Güvenlik Duvarı için altyapı FQDN 'SI
description: Azure Güvenlik Duvarı'nda varsayılan olarak izin verilen altyapı FQDN'leri için yerleşik bir kural koleksiyonu bulunur.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168974"
---
# <a name="infrastructure-fqdns"></a>Altyapı FQDN'leri

Azure Güvenlik Duvarı'nda varsayılan olarak izin verilen altyapı FQDN'leri için yerleşik bir kural koleksiyonu bulunur. Bu FQDN'ler platforma özgüdür ve başka amaçlarla kullanılamaz. 

Aşağıdaki hizmetler yerleşik kural koleksiyonuna dahildir:

- Depolama platformu görüntü deposuna (PIR) erişim işlem
- Yönetilen diskler durum depolama erişimi
- Azure Tanılama ve günlüğe kaydetme (MDS)

## <a name="overriding"></a>Si 

Bu yerleşik altyapı kuralı koleksiyonunu, son işlenen bir reddetme tüm uygulama kuralı koleksiyonu oluşturarak geçersiz kılabilirsiniz. Bu kural her zaman altyapı kuralı koleksiyonundan önce işlenir. Altyapı kuralı koleksiyonunda bulunmayan öğeler varsayılan olarak reddedilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik duvarını dağıtmayı ve yapılandırmayı](tutorial-firewall-deploy-portal.md)öğrenin.