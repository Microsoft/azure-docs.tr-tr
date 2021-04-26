---
title: Azure Media Services V2 hesaplarını yönetme | Microsoft Docs
description: Azure 'da medya içeriğini yönetmeye, şifrelemeye, kodlamaya, çözümlemeye ve akışla başlamak için bir Media Services hesabı oluşturmanız gerekir. Bu makalede Azure Media Services V2 hesaplarının nasıl yönetileceği açıklanmaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a097b186a2287ec13866c8a5ee9420641b44d131
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015957"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Azure Media Services V2 hesaplarını yönetme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure 'da medya içeriğini yönetmeye, şifrelemeye, kodlamaya, çözümlemeye ve akışla başlamak için bir Media Services hesabı oluşturmanız gerekir. Media Services hesabı oluştururken, bir Azure Depolama hesabı kaynağının adını sağlamanız gerekir. Belirtilen depolama hesabı, Media Services hesabınıza eklenir. Media Services hesabı ve onunla ilişkili tüm depolama hesaplarının aynı Azure aboneliğinde olması gerekir.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Media Services bir hesabı abonelikler arasında taşıma

Bir Media Services hesabını yeni bir aboneliğe taşımanız gerekiyorsa, önce Media Services hesabını içeren kaynak grubunun tamamını yeni aboneliğe taşımanız gerekir. Tüm bağlı kaynakları taşımanız gerekir: Azure depolama hesapları, Azure CDN profilleri vb. Daha fazla bilgi için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Azure 'daki tüm kaynaklarda olduğu gibi, kaynak grubu hareketlerinin tamamlanması biraz zaman alabilir.

Media Services V2, çok kiracılı modeli desteklemez. Bir Media Services hesabını yeni bir Kiracıdaki bir aboneliğe taşımanız gerekiyorsa yeni kiracıda yeni bir Azure Active Directory (Azure AD) uygulaması oluşturun. Ardından, hesabınızı yeni Kiracıdaki aboneliğe taşıyın. Kiracı taşıma işlemi tamamlandıktan sonra, v2 API 'Lerini kullanarak Media Services hesaba erişmek için yeni kiracıdan bir Azure AD uygulaması kullanmaya başlayabilirsiniz.

> [!IMPORTANT]
> Media Services V2 API 'sine erişmek için [Azure AD kimlik doğrulaması](media-services-portal-get-started-with-aad.md) bilgilerini sıfırlamanız gerekir.
  
### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Farklı bir aboneliğe geçmeden önce hesabınızdaki tüm verilerin yedeklerini oluşturun.
* Tüm akış uç noktalarını ve canlı akış kaynaklarını durdurmanız gerekir. Kullanıcılarınız, kaynak grubu taşıma süresi boyunca içeriğinize erişemez.

> [!IMPORTANT]
> Taşıma başarılı bir şekilde tamamlanana kadar akış uç noktasını başlatmayın.

### <a name="troubleshoot"></a>Sorun giderme

Kaynak grubu taşıma sonrasında bir Media Services hesabı ya da ilişkili bir Azure depolama hesabı "bağlantısı kesildi" ise, depolama hesabı anahtarlarını döndürmeyi deneyin. Depolama hesabı anahtarlarını döndürmek, Media Services hesabının "bağlantısı kesildi" durumunu çözümlemezse, Media Services hesabındaki "destek + sorun giderme" menüsünden Yeni bir destek isteği ister.  

## <a name="next-steps"></a>Sonraki adımlar

[Hesap oluşturma](media-services-portal-create-account.md)
