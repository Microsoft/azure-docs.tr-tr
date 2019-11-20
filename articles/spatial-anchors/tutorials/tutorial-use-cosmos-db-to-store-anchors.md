---
title: Öğretici-Azure Cosmos DB arka ucu ile oturumlar ve cihazlar arasında Azure uzamsal çıpası paylaşma | Microsoft Docs
description: Bu öğreticide, bir arka uç hizmetiyle ve Azure Cosmos DB Unity 'de Android/iOS cihazlarında Azure uzamsal bağlayıcı tanımlayıcılarını paylaşmayı öğreneceksiniz.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7ddbff563f79992f21aef5182177f4fb60c61dab
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882159"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Öğretici: Azure uzamsal bağlayıcılarını Azure Cosmos DB arka ucu ile oturumlar ve cihazlar arasında paylaşma

Bu öğretici, [Azure uzamsal Tutturucuların oturumlar ve cihazlar arasında paylaşılmasının](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) bir devamıdır. Azure Cosmos DB, oturum ve cihazlarda Azure uzamsal bağlayıcıları paylaşırken arka uç depolama alanı olarak sunmak için daha fazla özellik ekleme sürecinde size kılavuzluk eder.

![Nesne kalıcılığını gösteren GIF](./media/persistence.gif)

Bu öğreticide Unity ve Azure Cosmos DB kullanacağınızı, ancak bu öğreticide yalnızca uzamsal bağlayıcı tanımlayıcılarının nasıl paylaşılacağını gösteren bir örnek sunacağınızı unutmayın. Aynı amaca ulaşmak için diğer dillere ve arka uç teknolojilerine kullanıcı ekleyebilirsiniz. Ayrıca, bu öğreticide kullanılan ASP.NET Core Web uygulaması .NET Core 2,2 SDK 'sını gerektirir. Windows için Web Apps üzerinde ince çalışır, ancak şu anda Linux için Web Apps üzerinde çalışmaz.

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma

Daha önce oluşturduğunuz kaynak grubuna bir Azure Cosmos veritabanı ekleyin. 

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

`Connection String`, ihtiyacınız olacak şekilde kopyalayın.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>SharingService dosyalarında küçük değişiklikler yapın

**Çözüm Gezgini**' de `SharingService\Startup.cs`açın.

Dosyanın üst kısmında `#define INMEMORY_DEMO` bulun ve bu çizginin dışına yorum yapın. Dosyayı kaydedin.

**Çözüm Gezgini**' de `SharingService\appsettings.json`açın.

`StorageConnectionString` özelliğini bulun ve değeri [veritabanı hesabı oluştur adımında](#create-a-database-account)kopyaladığınız `Connection String` değeriyle aynı olacak şekilde ayarlayın. Dosyayı kaydedin.

Paylaşım hizmetini yeniden yayımlayabilir ve örnek uygulamayı çalıştırabilirsiniz.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, cihazlarda bağlantı tanımlayıcılarını paylaşmak için Azure Cosmos DB kullandınız. Azure uzamsal çıpalarını yeni bir Unity HoloLens uygulamasında kullanma hakkında daha fazla bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yeni bir Unity HoloLens uygulaması başlatılıyor](./tutorial-new-unity-hololens-app.md)
