---
title: Durdur/Başlat-Azure portal-MySQL için Azure veritabanı sunucusu
description: Bu makalede, MySQL için Azure veritabanı 'nda işlemleri durdurma/başlatma işlemi açıklanır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d297d215d4b0edfdd67b603ba4707bf02057ad78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100516881"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nı Durdur/Başlat

> [!IMPORTANT]
>  Sunucuyu **durdurduğunuzda** bir esnette sonraki 7 gün için bu durumda kalır. Bu süre içinde el ile **başlatmadıysanız** , sunucu otomatik olarak 7 gün sonunda başlatılır. Sunucuyu kullanmıyorsanız tekrar **durdurmayı** seçebilirsiniz.

Bu makalede, tek sunucu durdurma ve başlatma işlemleri gerçekleştirmeye yönelik adım adım yordamlar sunulmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

-   MySQL için Azure veritabanı tek sunucu olmalıdır.

> [!NOTE]
> [Durdur/Başlat](concepts-servers.md#limitations-of-stopstart-operation) kullanma sınırına bakın

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Azure portal kullanarak MySQL için Azure veritabanı 'nı durdurma/başlatma

### <a name="stop-a-running-server"></a>Çalışan bir sunucuyu durdur

1.  [Azure Portal](https://portal.azure.com/), durdurmak istediğiniz MySQL sunucunuzu seçin.

2.  **Genel bakış** sayfasında, araç çubuğundaki **Durdur** düğmesine tıklayın.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="MySQL için Azure veritabanı sunucuyu durdur":::

    > [!NOTE]
    > Sunucu durdurulduktan sonra, tek sunucu için diğer yönetim işlemleri kullanılamaz.

### <a name="start-a-stopped-server"></a>Durdurulmuş bir sunucu başlatma

1.  [Azure Portal](https://portal.azure.com/), başlatmak istediğiniz tek sunucunuzu seçin.

2.  **Genel bakış** sayfasında, araç çubuğundaki **Başlat** düğmesine tıklayın.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="MySQL için Azure veritabanı başlangıç sunucusu":::

    > [!NOTE]
    > Sunucu başlatıldıktan sonra, tüm yönetim işlemleri artık tek bir sunucu için kullanılabilir.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>CLı kullanarak MySQL için Azure veritabanı 'nı durdurma/başlatma

### <a name="stop-a-running-server"></a>Çalışan bir sunucuyu durdur

1.  [Azure Portal](https://portal.azure.com/), durdurmak istediğiniz MySQL sunucunuzu seçin.

2.  **Genel bakış** sayfasında, araç çubuğundaki **Durdur** düğmesine tıklayın.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Sunucu durdurulduktan sonra, tek sunucu için diğer yönetim işlemleri kullanılamaz.

### <a name="start-a-stopped-server"></a>Durdurulmuş bir sunucu başlatma

1.  [Azure Portal](https://portal.azure.com/), başlatmak istediğiniz tek sunucunuzu seçin.

2.  **Genel bakış** sayfasında, araç çubuğundaki **Başlat** düğmesine tıklayın.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Sunucu başlatıldıktan sonra, tüm yönetim işlemleri artık tek bir sunucu için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar
[Ölçümler üzerinde uyarılar oluşturma](howto-alert-on-metric.md)hakkında bilgi edinin.
