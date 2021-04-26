---
title: Durdur/Başlat-Azure portal-PostgreSQL için Azure veritabanı esnek sunucu
description: Bu makalede, Azure portal aracılığıyla PostgreSQL için Azure veritabanı 'nda işlemleri durdurma/başlatma işlemi açıklanır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e28243f5919c355e6ad0d4998ff8388a56e3d0ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607377"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>PostgreSQL için Azure veritabanı 'nı Durdur/Başlat-esnek sunucu (Önizleme)

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu şu anda önizleme aşamasındadır.

Bu makale, esnek bir sunucuyu durdurmak ve başlatmak için adım adım yönergeler sağlar.

## <a name="pre-requisites"></a>Ön koşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

-   PostgreSQL için Azure veritabanı esnek sunucusu olmalıdır.

## <a name="stop-a-running-server"></a>Çalışan bir sunucuyu durdur

1.  [Azure Portal](https://portal.azure.com/), durdurmak istediğiniz esnek sunucuyu seçin.

2.  **Genel bakış** sayfasında, araç çubuğundaki **Durdur** düğmesine tıklayın.

> [!NOTE]
> Sunucu durdurulduktan sonra esnek sunucu için diğer yönetim işlemleri kullanılamaz.

Durdurulan sunucuların yedi gün sonra otomatik olarak yeniden başlatılacağını lütfen unutmayın. Bekleyen bakım güncelleştirmeleri, sunucu bir sonraki sefer başlatıldığında uygulanır.

## <a name="start-a-stopped-server"></a>Durdurulmuş bir sunucu başlatma

1.  [Azure Portal](https://portal.azure.com/), başlatmak istediğiniz esnek sunucuyu seçin.

2.  **Genel bakış** sayfasında, araç çubuğundaki **Başlat** düğmesine tıklayın.

> [!NOTE]
> Sunucu başlatıldıktan sonra, tüm yönetim işlemleri artık esnek sunucu için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [PostgreSQL Için Azure veritabanı esnek sunucusu 'nda işlem ve depolama seçenekleri](./concepts-compute-storage.md)hakkında daha fazla bilgi edinin.
