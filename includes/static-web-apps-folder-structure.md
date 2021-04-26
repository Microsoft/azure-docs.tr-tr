---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543575"
---
| Özellik | Açıklama | Örnek | Gerekli |
|---|---|---|---|
| `app_location` | Uygulama kodunuzun konumu. | `/`Uygulama kaynak kodunuzun deponun kökünde olup olmadığını veya `/app` uygulama kodunuzun adlı bir dizinde olup olmadığını girin `app` . | Yes |
| `api_location` | Azure Işlevleri kodunuzun konumu. | `/api`Uygulama kodunuzun adlı bir klasörde olup olmadığını girin `api` . Klasörde hiçbir Azure Işlevleri uygulaması algılanmazsa, derleme başarısız olmaz, iş akışı bir API istemediğinizi varsayar. | No |
| `output_location` | Derleme çıkış dizininin öğesine göre konumu `app_location` . | Uygulama kaynak kodunuz konumunda bulunuyorsa `/app` ve derleme betiği dosyaları klasörüne çıktıdaysa `/app/build` `build` değer olarak ayarlanır `output_location` . | Hayır |