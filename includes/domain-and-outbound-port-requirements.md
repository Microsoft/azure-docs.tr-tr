---
title: include dosyası
description: include dosyası
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285598"
---
| Etki alanı adları                  | Giden bağlantı noktaları | Açıklama                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Şirket içinde barındırılan tümleştirme çalışma zamanının Data Factory içindeki veri taşıma hizmetlerine bağlanması için gereklidir. |
| `*.frontend.clouddatahub.net` | 443            | Şirket içinde barındırılan tümleştirme çalışma zamanı tarafından Data Factory hizmetine bağlanmak için gereklidir. |
| `download.microsoft.com`    | 443            | Güncelleştirmeleri indirmek için şirket içinde barındırılan tümleştirme çalışma zamanı için gereklidir. Otomatik güncelleştirmeyi devre dışı bırakırsanız, bunu atlayabilirsiniz. |
| `*.core.windows.net`          | 443            | Kendi kendine barındırılan tümleştirme çalışma zamanı tarafından, [hazırlanan kopyalama](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) özelliğini kullandığınızda Azure depolama hesabına bağlanmak için kullanılır. |
| `*.database.windows.net`      | 1433           | Seçim Azure SQL veritabanı veya Azure SQL veri ambarı 'ndan veya sürümünden kopyalama yapmanız gerekir. Azure SQL veritabanı veya Azure SQL veri ambarı 'na veri kopyalamak için 1433 numaralı bağlantı noktasını açmadan hazırlanan kopyalama özelliğini kullanın. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Seçim Azure Data Lake Store ' den veya ' a kopyaladığınızda gereklidir. |
