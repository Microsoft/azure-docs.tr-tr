---
title: include dosyası
description: include dosyası
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: a369eb7000fb8622a69f4205ffcc232ae9c9d242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95545943"
---
Arşiv depolama birimindeki verileri okumak için katmanı önce sık erişimli veya seyrek erişimli blob katmanı olarak değiştirmeniz gerekir. Bu işleme yeniden doldurma denir ve tamamlanması saat sürebilir. En iyi yeniden doldurma performansı için büyük blob boyutları öneririz. Birkaç küçük blobu aynı anda yeniden doldurmak süreyi uzatabilir. Şu anda iki adet yeniden doldurma önceliği vardır, yüksek ve standart. Bu, bir [BLOB katmanı kümesi](/rest/api/storageservices/set-blob-tier) veya [BLOB kopyalama](/rest/api/storageservices/copy-blob) işleminde isteğe bağlı *x-MS-rehite-Priority* özelliği aracılığıyla ayarlanabilir.

* **Standart öncelik**: yeniden doldurma isteği alındığı sırada işlenir ve 15 saate kadar sürebilir.
* **Yüksek öncelik**: yeniden doldurma isteğine standart isteklerden öncelikli olarak öncelik verilecek ve boyutu on GB 'ın altındaki nesneler için 1 saat altında bitecektir. 

> [!NOTE]
> Standart öncelik Arşiv için varsayılan yeniden hidratıon seçeneğidir. Yüksek öncelikli, standart öncelikten daha fazla yeniden doldurma maliyeti sağlayacak ve genellikle acil durum veri geri yükleme durumlarında kullanılmak üzere ayrılmış olan daha hızlı bir seçenektir.
>
> En yüksek öncelik, blob boyutuna ve geçerli talebe bağlı olarak 1 saatten uzun sürebilir. Yüksek öncelikli isteklerin standart öncelik istekleri üzerinden öncelikli olması garanti edilir.

Yeniden doldurma isteği başlatıldıktan sonra iptal edilemez. Yeniden doldurma işlemi sırasında, *x-MS-Access-Tier* blob özelliği çevrimiçi bir katmana yeniden doldurma tamamlanana kadar arşiv olarak gösterilmeye devam edecektir. Yeniden doldurma durumunu ve ilerlemesini onaylamak için, [BLOB özelliklerini al](/rest/api/storageservices/get-blob-properties) ' ı çağırıp *x-MS-Archive-Status* ve *x-MS-rehibulunan öncelikli* blob özelliklerini kontrol edebilirsiniz. Arşiv durumu, yeniden doldurma hedef katmanına bağlı olarak "rehion-to-Hot" veya "rehion-with-soğuk" olarak okunabilir. Yeniden doldurma önceliği, "yüksek" veya "standart" hızını gösterecektir. Tamamlandıktan sonra, arşiv durumu ve yeniden doldurma önceliği özellikleri kaldırılır ve erişim katmanı blobu özelliği seçili sık veya seyrek katmanı yansıtacak şekilde güncelleştirilir.