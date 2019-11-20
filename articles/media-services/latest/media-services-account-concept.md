---
title: Azure Media Services v3 hesaplarını yönetme | Microsoft Docs
description: Yönetmek, şifreleme, kodlama, çözümleme ve azure'da medya içeriği akışı başlatmak için bir Media Services hesabı oluşturmanız gerekir. Bu makalede, Azure Media Services v3 hesaplarını yönetme açıklanmaktadır.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: fa9720c2c29af184016d2903e60520e701b4cf79
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670678"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Azure Media Services v3 hesaplarını yönetme

Yönetmek, şifreleme, kodlama, çözümleme ve azure'da medya içeriği akışı başlatmak için bir Media Services hesabı oluşturmanız gerekir. Media Services hesabı oluştururken, bir Azure Depolama hesabı kaynağının adını sağlamanız gerekir. Belirtilen depolama hesabı, Media Services hesabınıza eklenir. Media Services hesabı ve onunla ilişkili tüm depolama hesaplarının aynı Azure aboneliğinde olması gerekir. Daha fazla bilgi için [depolama hesapları](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Media Services hesabı abonelikler arasında taşıma 

Media Services hesabı için yeni bir aboneliği taşımak gerekiyorsa, önce yeni abonelik için Media Services hesabını içeren kaynak grubunun tamamını taşıyın gerekir. Tüm ekli kaynaklar taşımalısınız: Azure depolama hesapları, Azure CDN profili, vb. Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/resource-group-move-resources.md). Tüm kaynaklarla gibi Azure kaynak grubu taşıma tamamlanması biraz zaman alabilir.

> [!NOTE]
> Media Services v3 çok kiracılı modeli destekler.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Tüm verileri yedekler, farklı bir aboneliğe geçiş yapmadan önce hesabınızı oluşturun.
* Tüm akış uç noktaları durdurun ve canlı akış kaynakları gerekir. Kullanıcılarınızın içeriğinizi kaynak grubu taşıma süresince erişmek mümkün olmayacaktır. 

> [!IMPORTANT]
> Akış uç noktası taşıma başarıyla tamamlanana kadar başlatılmaz.

### <a name="troubleshoot"></a>Sorun giderme 

Media Services hesabı veya ilişkili bir Azure depolama hesabı "kaynak grubu taşıma aşağıdaki kesilirse" depolama hesabı anahtarlarını döndürme işlemini deneyin. Depolama hesabı anahtarlarını döndürme Media Services hesabı "bağlantısız" durumunu çözmezse dan yeni bir destek talebi dosya "Destek + sorun giderme" menüsünde Media Services hesabı.  

## <a name="next-steps"></a>Sonraki adımlar

[Hesap oluşturma](create-account-cli-quickstart.md)
