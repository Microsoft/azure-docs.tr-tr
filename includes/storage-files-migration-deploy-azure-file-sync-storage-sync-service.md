---
title: include dosyası
description: include dosyası
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 1142082caef06dcc36dfd9b0aaddc44d13cf6cc8
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075604"
---
Bu adımda, Azure abonelik kimlik bilgilerinizin olması gerekir.

Azure Dosya Eşitleme için yapılandırılacak çekirdek kaynağa *depolama eşitleme hizmeti* denir. Artık aynı dosya kümesini eşitlenen tüm sunucular için veya gelecekte yalnızca bir tane dağıtmanızı öneririz. Yalnızca verileri hiçbir zaman değiş tokuşu gereken farklı sunucu kümelerine sahipseniz, birden çok depolama eşitleme hizmeti oluşturun. Örneğin, aynı Azure dosya paylaşımının hiçbir zaman eşitlenmemesi gereken sunucularınız olabilir. Aksi halde, tek bir depolama eşitleme hizmeti en iyi uygulamadır.

Konumunuzda yakın olan depolama eşitleme hizmetiniz için bir Azure bölgesi seçin. Diğer tüm bulut kaynakları aynı bölgede dağıtılmalıdır. Yönetimi basitleştirmek için aboneliğinizde eşitleme ve depolama kaynaklarını barındıran yeni bir kaynak grubu oluşturun.

Daha fazla bilgi için, Azure Dosya Eşitleme dağıtma hakkında makalesindeki [depolama eşitleme hizmeti 'ni dağıtma hakkında bölümüne](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) bakın. Makalenin yalnızca bu bölümünü izleyin. Sonraki adımlarda makalenin diğer bölümlerine bağlantılar olacaktır.