---
title: En az kapalı kalma süresi geçişi-MySQL için Azure veritabanı
description: Bu makalede, Azure veritabanı geçiş hizmeti kullanılarak MySQL için Azure veritabanı 'na bir MySQL veritabanının en düşük kapalı kalma süresinin nasıl gerçekleştirileceği açıklanır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 75b548b53de4c0f4ea39959c3d70e9ddb361d64c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540340"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'na en düşük kesinti süresi geçişi
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

[Azure veritabanı geçiş hizmeti](https://aka.ms/get-dms) (DMS) için yeni sunulan **sürekli eşitleme özelliğini** kullanarak, MySQL Için Azure veritabanı 'na MySQL geçişleri gerçekleştirebilirsiniz. Bu işlevsellik, uygulama tarafından tahakkuk eden kesinti süresini sınırlandırır.

Ayrıca, [veritabanı geçiş kılavuzu](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) ' na başvurarak ayrıntılı bilgiler ve veritabanlarını MySQL Için Azure veritabanı 'na geçirme hakkındaki durumları kullanabilirsiniz. Bu kılavuz, Azure 'a bir MySQL geçişinin başarıyla planlanmasını ve yürütülmesini sağlayacak yönergeler sağlar.

## <a name="overview"></a>Genel Bakış
Azure DMS, şirket içi şirketinizin ilk yükünü MySQL için Azure veritabanı 'na uygular ve uygulama çalışmaya devam ederken yeni işlemleri sürekli olarak Azure 'a eşitler. Veriler hedef Azure tarafında tamamlandıktan sonra, uygulamayı kısa bir süre (en düşük kapalı kalma süresi) durdurur, son veri toplu iş için bekleyin (uygulamanın yeni bir trafiği almak için etkin hale gelene kadar uygulamayı durdurmanız gerekir) ve ardından Bağlantı dizenizi Azure 'a işaret etmek üzere güncelleştirin. İşiniz bittiğinde, uygulamanız Azure 'da canlı olacaktır!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Azure veritabanı geçiş hizmeti ile sürekli eşitleme":::

## <a name="next-steps"></a>Sonraki adımlar
- MySQL için Azure veritabanı 'na veritabanı geçirme hakkında daha fazla bilgi için bkz. [veritabanı geçiş kılavuzu](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- MySQL uygulamalarının MySQL için Azure veritabanı 'na nasıl geçirileceğini gösteren bir demo içeren [MySQL/PostgreSQL uygulamalarını Azure yönetilen hizmetine kolayca geçirin](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201).
- [DMS kullanarak MySQL Için MySQL for MySQL 'e Azure veritabanı 'Na geçiş](../dms/tutorial-mysql-azure-mysql-online.md)öğreticisine bakın.