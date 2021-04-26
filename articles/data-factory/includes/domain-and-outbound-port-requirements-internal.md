---
title: include dosyası
description: include dosyası
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 24a541080b580eab967987fa7c92cea64b99d65c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389526"
---
| Etki alanı adları                                          | Giden bağlantı noktaları | Description                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Genel bulut: `*.servicebus.windows.net` <br> Azure Kamu: `*.servicebus.usgovcloudapi.net` <br> Arasında `*.servicebus.chinacloudapi.cn`   | 443            | Etkileşimli yazma için şirket içinde barındırılan tümleştirme çalışma zamanı için gereklidir. |
| Genel bulut: `{datafactory}.{region}.datafactory.azure.net`<br> veya `*.frontend.clouddatahub.net` <br> Azure Kamu: `{datafactory}.{region}.datafactory.azure.us` <br> Arasında `{datafactory}.{region}.datafactory.azure.cn` | 443            | Şirket içinde barındırılan tümleştirme çalışma zamanı tarafından Data Factory hizmetine bağlanmak için gereklidir. <br>Genel bulutta oluşturulan yeni Data Factory için, lütfen {DataFactory} biçiminde olan şirket içinde barındırılan Integration Runtime anahtarınızdan FQDN 'yi bulun. {Region}. DataFactory. Azure. net. Eski Veri Fabrikası için, FQDN 'yi şirket içinde barındırılan tümleştirme anahtarınıza görmüyorsanız lütfen bunun yerine *. frontend.clouddatahub.net kullanın. |
| `download.microsoft.com`    | 443            | Güncelleştirmeleri indirmek için şirket içinde barındırılan tümleştirme çalışma zamanı için gereklidir. Otomatik güncelleştirmeyi devre dışı bırakırsanız, bu etki alanını yapılandırmayı atlayabilirsiniz. |
| Anahtar Kasası URL'si | 443           | Kimlik bilgisini Key Vault depoluyordu Azure Key Vault gerekir. |
