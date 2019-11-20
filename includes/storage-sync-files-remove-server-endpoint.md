---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391822"
---
Hayır: sunucu uç noktasını kaldırmak bir sunucuyu yeniden başlatma gibi değildir! Sunucu uç noktasının kaldırılması ve yeniden oluşturulması, eşitleme, bulut katmanlaması veya Azure Dosya Eşitleme diğer yönleri ile ilgili sorunları gidermek için neredeyse hiçbir zaman uygun bir çözümdür. Sunucu uç noktasının kaldırılması, bozucu bir işlemdir. Katmanlı dosyaların sunucu uç noktası ad alanı dışında bulunması durumunda veri kaybına neden olabilir. Daha fazla bilgi için bkz. [katmanlı dosyaların neden sunucu uç noktası dışında var](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) olduğunu öğrenin. Ya da sunucu uç noktası ad alanı içinde bulunan katmanlı dosyalar için erişilemeyen dosyalara neden olabilir. Sunucu uç noktası yeniden oluşturulduğunda bu sorunlar çözümlenmez. Bulut katmanlaması etkin olmasa bile, katmanlı dosyalar sunucu uç noktası ad alanınız içinde bulunabilir. Bu nedenle, bu klasör ile Azure Dosya Eşitleme kullanmayı durdurmak istemediğiniz ya da açıkça bir Microsoft mühendis tarafından kullanılması istenmedikçe sunucu uç noktasını kaldırmamanız önerilir. Sunucu uç noktalarını kaldırma hakkında daha fazla bilgi için bkz. [sunucu uç noktasını kaldırma](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
