---
title: Azure Portal'dan işlev uygulaması oluşturma | Microsoft Docs
description: Portaldan Azure 'da yeni bir işlev uygulaması oluşturun.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 93bce0404c9b3bf630416557726dca0c856528c3
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170796"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Azure portalından işlev uygulaması oluşturma

Bu konuda, Azure portal bir işlev uygulaması oluşturmak için Azure Işlevlerinin nasıl kullanılacağı gösterilmektedir. İşlev uygulaması, tek tek işlevlerin yürütülmesini barındıran kapsayıcıdır. 

## <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

İşlev uygulaması oluştururken yalnızca harf, sayı ve kısa çizgi içerebilecek bir **Uygulama adı** sağlayın. Alt çizgi ( **_** ) izin verilen bir karakter değildir.

Depolama hesabı adları 3 ile 24 karakter arasında olmalı ve yalnızca sayıyla küçük harf içermelidir. Depolama hesabınızın adının Azure içinde benzersiz olması gerekir. 

İşlev uygulaması oluşturulduktan sonra bir veya daha fazla dilde tek tek işlevler oluşturabilirsiniz. [Portalı kullanarak](functions-create-first-azure-function.md#create-function), [sürekli dağıtım](functions-continuous-deployment.md) aracılığıyla veya [FTP ile karşıya yükleyerek](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp) çeşitli işlevler oluşturun.

## <a name="service-plans"></a>Hizmet planları

Azure Işlevleri üç farklı hizmet planına sahiptir: Tüketim planı, Premium plan ve adanmış (App Service) plan. İşlev uygulamanız oluşturulduğunda hizmet planınızı seçmeniz gerekir ve daha sonra değiştirilemez. Daha fazla bilgi edinmek için bkz. [Azure İşlevleri barındırma planı seçme](functions-scale.md).

JavaScript işlevlerini adanmış (App Service) bir planda çalıştırmayı planlıyorsanız, daha az çekirdeğe sahip bir plan seçmelisiniz. Daha fazla bilgi edinmek için bkz. [İşlevler için JavaScript başvurusu](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Depolama hesabı gereksinimleri

Bir işlev uygulaması oluştururken blob, kuyruk ve tablo depolamayı destekleyen genel amaçlı bir Azure depolama hesabı oluşturmanız veya bağlamanız gerekir. Dahili olarak İşlevler tetikleyicileri yönetme ve işlev yürütmelerini günlüğe kaydetme gibi işlemler için Depolama’yı kullanır. Yalnızca blob depolama hesapları, Azure Premium Depolama ve ZRS çoğaltmalı genel amaçlı depolama hesapları gibi bazı depolama hesapları kuyrukları ve tabloları desteklemez. Bir işlev uygulaması oluşturulurken bu hesaplar Depolama Hesabı dikey penceresinden filtrelenir.

>[!NOTE]
>Tüketim barındırma planı kullanılırken işlev kodunuz ve bağlama yapılandırma dosyalarınız ana depolama hesabındaki Azure Dosya depolama alanında saklanır. Ana depolama hesabını sildiğinizde bu içerik silinir ve kurtarılamaz.

Depolama hesabı türleri hakkında daha fazla bilgi edinmek için bkz. [Azure Depolama Hizmetlerine Giriş](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Sonraki adımlar

Azure portal, Işlevleri oluşturmayı ve denemeyi daha kolay hale getirir, ancak [Yerel geliştirmeyi](functions-develop-local.md)öneririz. Portalda bir işlev uygulaması oluşturduktan sonra yine de bir işlev eklemeniz gerekir. 

> [!div class="nextstepaction"]
> [HTTP ile tetiklenen bir işlev ekleme](functions-create-first-azure-function.md#create-function)
