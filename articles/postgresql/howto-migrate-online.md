---
title: PostgreSQL için Azure veritabanı 'na en az kapalı kalma süresi-tek sunucu
description: Bu makalede, Azure veritabanı geçiş hizmeti kullanılarak bir PostgreSQL veritabanının Azure veritabanı 'na PostgreSQL için Azure veritabanı 'na en az kapalı kalma süresi nasıl gerçekleştireceğiniz açıklanır.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 27da5f1b731b2cdb0604f91f7f9e78b19ee2908b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489804"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'na en az kapalı kalma süresi-tek sunucu
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

[Azure veritabanı geçiş hizmeti](https://aka.ms/get-dms) (DMS) için yeni sunulan **sürekli eşitleme özelliğini** kullanarak PostgreSQL için Azure veritabanı 'na geçiş işlemini en az kapalı kalma süresine sahip olabilirsiniz. Bu işlevsellik, uygulama tarafından tahakkuk eden kesinti süresini sınırlandırır.

## <a name="overview"></a>Genel Bakış
Azure DMS, şirket içinde PostgreSQL için Azure veritabanı 'na ilk kez yük gerçekleştirir ve ardından uygulama çalışmaya devam ederken yeni işlemleri sürekli olarak Azure 'a eşitler. Veriler hedef Azure tarafında tamamlandıktan sonra, uygulamayı kısa bir süre (en düşük kapalı kalma süresi) durdurur, son veri toplu iş için bekleyin (uygulamanın yeni bir trafiği almak için etkin hale gelene kadar uygulamayı durdurmanız gerekir) ve ardından Bağlantı dizenizi Azure 'a işaret etmek üzere güncelleştirin. İşiniz bittiğinde, uygulamanız Azure 'da canlı olacaktır!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Azure veritabanı geçiş hizmeti ile sürekli eşitleme":::

## <a name="next-steps"></a>Sonraki adımlar
- PostgreSQL uygulamalarının PostgreSQL için Azure veritabanı 'na nasıl geçirileceğini gösteren bir demo içeren [Microsoft Azure ile video uygulaması Modernleştirmesini](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102)görüntüleyin.
- [DMS kullanarak PostgreSQL for PostgreSQL Için Azure veritabanı 'Na geçiş](../dms/tutorial-postgresql-azure-postgresql-online.md)öğreticisine bakın.