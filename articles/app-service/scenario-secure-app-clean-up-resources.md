---
title: Öğretici-Kaynakları Temizleme | Mavisi
description: Bu öğreticide, Web uygulamasını oluştururken ayrılan Azure kaynaklarını temizlemeyi öğreneceksiniz.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: cfb42b82943f03c3633ff69662ab841bf587ede4
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221866"
---
# <a name="tutorial-clean-up-resources"></a>Öğretici: Kaynakları Temizleme

Bu çok parçalı öğreticideki tüm adımları tamamladıysanız, bir kaynak grubunda bir App Service, App Service barındırma planı ve bir depolama hesabı oluşturdunuz. Ayrıca, Azure Active Directory bir uygulama kaydı da oluşturdunuz. Artık gerekli değilse, ücretleri tahakkuk ettirmek için bu kaynakları ve uygulama kaydını silin.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Öğreticiyi takip ederken oluşturulan Azure kaynaklarını silin.

## <a name="delete-the-resource-group"></a>Kaynak grubunu silme

[Azure Portal](https://portal.azure.com), Portal menüsünden **kaynak grupları** ' nı seçin ve App Service ve App Service planınızı içeren kaynak grubunu seçin.

Kaynak grubunu ve tüm kaynakları silmek için **kaynak grubunu sil** ' i seçin.

:::image type="content" alt-text="Kaynak grubunun silinmesini gösteren ekran görüntüsü." source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

Bu komutun çalışması birkaç dakika sürebilir.

## <a name="delete-the-app-registration"></a>Uygulama kaydını silme

Portal menüsünden **Azure Active Directory**  >  **uygulama kayıtları**' ni seçin. Ardından oluşturduğunuz uygulamayı seçin.
:::image type="content" alt-text="Uygulama kaydı seçimini gösteren ekran görüntüsü." source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

Uygulama kaydına genel bakış ' da **Sil**' i seçin.
:::image type="content" alt-text="Uygulama kaydını silmeyi gösteren ekran görüntüsü." source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
>
> * Öğreticiyi takip ederken oluşturulan Azure kaynaklarını silin.

Bir [.NET Core uygulamasını](tutorial-dotnetcore-sqldb-app.md), [Python uygulamasını](tutorial-python-postgresql-app.md), [Java uygulamasını](tutorial-java-spring-cosmosdb.md)veya [Node.js uygulamasını](tutorial-nodejs-mongodb-app.md) bir veritabanına bağlamayı öğrenin.