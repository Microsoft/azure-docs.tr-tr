---
title: Sunucuyu yeniden başlatma-Azure CLı-MySQL için Azure veritabanı
description: Bu makalede, Azure CLı kullanarak MySQL için Azure veritabanı sunucusunu nasıl yeniden başlatabileceğinizi açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77f20bba76ee3a37e6a72481e0b3d13c9904b106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541836"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Azure CLı kullanarak MySQL için Azure veritabanı sunucusunu yeniden başlatma
Bu konuda, MySQL sunucusu için Azure veritabanı 'nı nasıl yeniden başlatabileceğinizi açıklanmaktadır. Sunucu işlemi gerçekleştirdiğinden kısa süreli bir kesinti oluşmasına neden olan bakım nedenleriyle sunucunuzu yeniden başlatmanız gerekebilir.

Hizmet meşgulse sunucu yeniden başlatması engellenir. Örneğin, hizmet, sanal çekirdekleri ölçeklendirirken daha önce istenen bir işlemi işliyor olabilir.

Yeniden başlatmayı tamamlaması için gereken süre MySQL kurtarma işlemine bağlıdır. Yeniden başlatma süresini azaltmak için, yeniden başlatmadan önce sunucuda gerçekleşen etkinlik miktarını en aza indirmenizi öneririz.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için:

- [MySQL Için Azure veritabanı sunucusuna](quickstart-create-server-up-azure-cli.md)ihtiyacınız vardır.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="restart-the-server"></a>Sunucuyu yeniden başlatın

Aşağıdaki komutla sunucuyu yeniden başlatın:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Sonraki adımlar

[MySQL Için Azure veritabanı 'nda parametreleri ayarlama](howto-configure-server-parameters-using-cli.md) hakkında bilgi edinin
