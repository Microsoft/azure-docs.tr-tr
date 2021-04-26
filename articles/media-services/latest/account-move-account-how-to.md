---
title: Azure Media Services v3 hesaplarını yönetme
description: Azure 'da medya içeriğini yönetmeye, şifrelemeye, kodlamaya, çözümlemeye ve akışla başlamak için bir Media Services hesabı oluşturmanız gerekir. Bu makalede Azure Media Services v3 hesaplarının nasıl yönetileceği açıklanmaktadır.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: inhenkel
ms.openlocfilehash: 5f48a60ee139cf4a89c0873633feea93cdde0940
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105964241"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Azure Media Services v3 hesaplarını yönetme

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 'da medya içeriğini yönetmeye, şifrelemeye, kodlamaya, çözümlemeye ve akışla başlamak için bir Media Services hesabı oluşturmanız gerekir. Media Services hesabı oluştururken, bir Azure Depolama hesabı kaynağının adını sağlamanız gerekir. Belirtilen depolama hesabı, Media Services hesabınıza eklenir. Media Services hesabı ve onunla ilişkili tüm depolama hesaplarının aynı Azure aboneliğinde olması gerekir. Daha fazla bilgi için bkz. [depolama hesapları](storage-account-concept.md).

[!INCLUDE [account creation note](./includes/note-2020-05-01-account-creation.md)]

## <a name="moving-a-media-services-account-between-subscriptions"></a>Media Services bir hesabı abonelikler arasında taşıma 

Bir Media Services hesabını yeni bir aboneliğe taşımanız gerekiyorsa, önce Media Services hesabını içeren kaynak grubunun tamamını yeni aboneliğe taşımanız gerekir. Tüm bağlı kaynakları taşımanız gerekir: Azure depolama hesapları, Azure CDN profilleri vb. Daha fazla bilgi için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Azure 'daki tüm kaynaklarda olduğu gibi, kaynak grubu hareketlerinin tamamlanması biraz zaman alabilir.

> [!NOTE]
> Media Services v3, çok kiracılı modeli destekler.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Farklı bir aboneliğe geçmeden önce hesabınızdaki tüm verilerin yedeklerini oluşturun.
* Tüm akış uç noktalarını ve canlı akış kaynaklarını durdurmanız gerekir. Kullanıcılarınız, kaynak grubu taşıma süresi boyunca içeriğinize erişemez. 

> [!IMPORTANT]
> Taşıma başarılı bir şekilde tamamlanana kadar akış uç noktasını başlatmayın.

### <a name="troubleshoot"></a>Sorun giderme

Kaynak grubu taşıma sonrasında bir Media Services hesabı ya da ilişkili bir Azure depolama hesabı "bağlantısı kesildi" ise, depolama hesabı anahtarlarını döndürmeyi deneyin. Depolama hesabı anahtarlarını döndürmek, Media Services hesabının "bağlantısı kesildi" durumunu çözümlemezse, Media Services hesabındaki "destek + sorun giderme" menüsünden Yeni bir destek isteği ister.  

## <a name="next-steps"></a>Sonraki adımlar

[Hesap oluşturma](./account-create-how-to.md)
