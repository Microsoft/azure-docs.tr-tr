---
title: include dosyası
description: include dosyası
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 03674a51566ab89791725d1a51c7af12ed6949e5
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952912"
---
Arşiv depolama birimindeki verileri okumak için katmanı önce sık erişimli veya seyrek erişimli blob katmanı olarak değiştirmeniz gerekir. Bu işleme yeniden doldurma denir ve tamamlanması saat sürebilir. En iyi yeniden doldurma performansı için büyük blob boyutları öneririz. Birkaç küçük blobu aynı anda yeniden doldurmak süreyi uzatabilir. Şu anda iki yeniden doldurma önceliği vardır, yüksek (Önizleme) ve standart, bir [BLOB katmanı kümesi](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) veya [blobu kopyalama](https://docs.microsoft.com/rest/api/storageservices/copy-blob) işlemi üzerinde isteğe bağlı *x-MS-rehite-Priority* özelliği aracılığıyla ayarlanabilir.

* **Standart öncelik**: Yeniden doldurma isteği alındığı sırada işlenir ve 15 saate kadar sürebilir.
* **Yüksek öncelikli (Önizleme)** : Yeniden doldurma isteğine standart isteklerden öncelikli olarak öncelik verilecek ve 1 saat içinde bitirilebilecek. En yüksek öncelik, blob boyutuna ve geçerli talebe bağlı olarak 1 saatten uzun sürebilir. Yüksek öncelikli isteklerin standart öncelik istekleri üzerinden öncelikli olması garanti edilir.

> [!NOTE]
> Standart öncelik Arşiv için varsayılan yeniden hidratıon seçeneğidir. Yüksek öncelikli, standart öncelikten daha fazla yeniden doldurma maliyeti sağlayacak ve genellikle acil durum veri geri yükleme durumlarında kullanılmak üzere ayrılmış olan daha hızlı bir seçenektir.

Yeniden doldurma sırasında katmanın değişip değişmediğini onaylamak için *Arşiv Durumu* blob özelliğini kontrol edebilirsiniz. Durum, hedef katmana göre "rehydrate-pending-to-hot" veya "rehydrate-pending-to-cool" olabilir. Tamamlandıktan sonra arşiv durumu özelliği kaldırılır ve *Erişim Katmanı* blob özelliği sık veya seyrek erişimli bu yeni katmanı gösterir.
