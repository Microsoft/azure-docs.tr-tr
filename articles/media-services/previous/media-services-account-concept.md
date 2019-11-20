---
title: Azure Media Services v2 hesaplarını yönetme | Microsoft Docs
description: Yönetmek, şifreleme, kodlama, çözümleme ve azure'da medya içeriği akışı başlatmak için bir Media Services hesabı oluşturmanız gerekir. Bu makalede, Azure Media Services v2 hesapları yönetmek açıklanmaktadır.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b4c19b1f502d079d7dfcc1edef4674d21f78ac3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622049"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Azure Media Services v2 hesaplarını yönetme

Yönetmek, şifreleme, kodlama, çözümleme ve azure'da medya içeriği akışı başlatmak için bir Media Services hesabı oluşturmanız gerekir. Media Services hesabı oluştururken, bir Azure Depolama hesabı kaynağının adını sağlamanız gerekir. Belirtilen depolama hesabı, Media Services hesabınıza eklenir. Media Services hesabı ve onunla ilişkili tüm depolama hesaplarının aynı Azure aboneliğinde olması gerekir.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Media Services hesabı abonelikler arasında taşıma 

Media Services hesabı için yeni bir aboneliği taşımak gerekiyorsa, önce yeni abonelik için Media Services hesabını içeren kaynak grubunun tamamını taşıyın gerekir. Tüm ekli kaynaklar taşımalısınız: Azure depolama hesapları, Azure CDN profili, vb. Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/resource-group-move-resources.md). Tüm kaynaklarla gibi Azure kaynak grubu taşıma tamamlanması biraz zaman alabilir.

Media Services v2, çok kiracılı model desteklemez. Bir abonelikte yeni bir kiracı için Media Services hesabı taşımanız gerekirse, yeni kiracıya yeni bir Azure Active Directory (Azure AD) uygulaması oluşturun. Ardından, yeni Kiracı aboneliği için hesabınızı taşıyın. Kiracı taşıma tamamlandıktan sonra v2 API'lerini kullanarak Media Services hesabına erişmesi için bir Azure AD uygulaması yeni kiracının kullanarak başlayabilirsiniz. 

> [!IMPORTANT]
> Sıfırlamak gereken [Azure AD kimlik doğrulaması](media-services-portal-get-started-with-aad.md) Media Services v2 API'ye erişmek için bilgi.  
### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Tüm verileri yedekler, farklı bir aboneliğe geçiş yapmadan önce hesabınızı oluşturun.
* Tüm akış uç noktaları durdurun ve canlı akış kaynakları gerekir. Kullanıcılarınızın içeriğinizi kaynak grubu taşıma süresince erişmek mümkün olmayacaktır. 

> [!IMPORTANT]
> Akış uç noktası taşıma başarıyla tamamlanana kadar başlatılmaz.

### <a name="troubleshoot"></a>Sorun giderme 

Media Services hesabı veya ilişkili bir Azure depolama hesabı "kaynak grubu taşıma aşağıdaki kesilirse" depolama hesabı anahtarlarını döndürme işlemini deneyin. Depolama hesabı anahtarlarını döndürme Media Services hesabı "bağlantısız" durumunu çözmezse dan yeni bir destek talebi dosya "Destek + sorun giderme" menüsünde Media Services hesabı.  
 
## <a name="next-steps"></a>Sonraki adımlar

[Hesap oluşturma](media-services-portal-create-account.md)
