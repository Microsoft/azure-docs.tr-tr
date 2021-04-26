---
title: Birden çok kiracıyı Video Indexer-Azure ile yönetme
description: Bu makalede, Video Indexer ile birden çok kiracıyı yönetmek için farklı tümleştirme seçenekleri önerilir.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 7596aa4cef6c4bc601c15018e8ede4265f395eb7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312867"
---
# <a name="manage-multiple-tenants"></a>Birden çok kiracıyı yönetme

Bu makalede, Video Indexer ile birden çok kiracının yönetilmesine yönelik farklı seçenekler açıklanmaktadır. Senaryonuza en uygun yöntemi seçin:

* Kiracı başına Video Indexer hesabı
* Tüm kiracılar için tek Video Indexer hesabı
* Kiracı başına Azure aboneliği

## <a name="video-indexer-account-per-tenant"></a>Kiracı başına Video Indexer hesabı

Bu mimariyi kullanırken her kiracı için bir Video Indexer hesabı oluşturulur. Kiracılar kalıcı ve işlem katmanında tam yalıtımına sahiptir.  

![Kiracı başına Video Indexer hesabı](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Müşteriler, depolama hesaplarını paylaşmazlar (müşteri tarafından el ile yapılandırılmadığı müddetçe).
* Müşteriler, işlem (ayrılmış birimleri) paylaşmazlar ve bir diğerinin iş sürelerini işlemeyi etkilemez.
* Video Indexer hesabını silerek bir kiracıyı sistemden kolayca kaldırabilirsiniz.
* Kiracılar arasında özel modeller paylaşma yeteneği yoktur.

    Özel modelleri paylaşmak için bir iş gereksinimi olmadığından emin olun.
* Kiracı başına birden çok Video Indexer (ve ilişkili Media Services) hesabı nedeniyle yönetimi daha zordur.

> [!TIP]
> [Video Indexer Geliştirici Portalında](https://api-portal.videoindexer.ai/) sisteminiz için yönetici kullanıcı oluşturun ve kiracılar ilgili [hesap erişim belirtecini](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token)sağlamak için yetkilendirme API 'sini kullanın.

## <a name="single-video-indexer-account-for-all-users"></a>Tüm kullanıcılar için tek Video Indexer hesabı

Bu mimari kullanılırken, müşteri kiracılar yalıtımıyla sorumludur. Tüm kiracıların tek bir Azure Media Service hesabıyla tek bir Video Indexer hesabı kullanması gerekir. İçeriği karşıya yüklerken, ararken veya silerken, müşterinin bu kiracıya yönelik doğru sonuçları filtrelemeniz gerekir.

![Tüm kullanıcılar için tek Video Indexer hesabı](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Bu seçenekle, modelleri kiracıya göre filtreleyerek, özelleştirme modelleri (kişi, dil ve markalar) kiracılar arasında paylaşılabilir veya yalıtılabilir.

[Videoları karşıya yüklerken](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video), her kiracı için farklı bir bölüm özniteliği belirtebilirsiniz. Bu, [Arama API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos)'sinde yalıtımına izin verir. Arama API 'sindeki bölüm özniteliğini belirterek yalnızca belirtilen bölümün sonuçlarını elde edersiniz. 

### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Kiracılar arasında içerik ve özelleştirme modelleri paylaşma özelliği.
* Bir kiracı, diğer kiracıların performansını etkiler.
* Müşterinin Video Indexer en üstünde karmaşık bir yönetim katmanı oluşturması gerekir.

> [!TIP]
> Kiracılar işlerinin önceliklerini belirlemek için [Priority](upload-index-videos.md) özniteliğini kullanabilirsiniz.

## <a name="azure-subscription-per-tenant"></a>Kiracı başına Azure aboneliği 

Bu mimariyi kullanırken, her kiracının kendi Azure aboneliği olur. Her Kullanıcı için kiracı aboneliğinde yeni bir Video Indexer hesabı oluşturacaksınız.

![Kiracı başına Azure aboneliği](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Bu, fatura ayırmayı sağlayan tek seçenektir.
* Bu tümleştirmenin, kiracı başına Video Indexer hesabından daha fazla yönetim yükü vardır. Faturalandırma bir gereksinim değilse, bu makalede açıklanan diğer seçeneklerden birini kullanmanız önerilir.

## <a name="next-steps"></a>Sonraki adımlar

[Genel Bakış](video-indexer-overview.md)
