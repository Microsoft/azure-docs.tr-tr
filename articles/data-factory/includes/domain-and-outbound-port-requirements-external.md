---
title: include dosyası
description: include dosyası
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: d24e8957dc63024a46495e8a66bad7dbb3790f38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389525"
---
| Etki alanı adları                  | Giden bağlantı noktaları | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Kendi kendine barındırılan tümleştirme çalışma zamanı tarafından, hazırlanan kopyalama özelliğini kullandığınızda Azure depolama hesabına bağlanmak için kullanılır. |
| `*.database.windows.net`      | 1433           | Yalnızca, Azure SQL veritabanı veya Azure SYNAPSE Analytics 'ten ya da isteğe bağlı olarak, aksi takdirde gerekir. 1433 numaralı bağlantı noktasını açmadan verileri SQL veritabanı veya Azure SYNAPSE Analytics 'e kopyalamak için hazırlanan kopyalama özelliğini kullanın. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Yalnızca Azure Data Lake Store 'den veya ' a kopyaladığınızda ve isteğe bağlı olarak, aksi takdirde gereklidir. |
