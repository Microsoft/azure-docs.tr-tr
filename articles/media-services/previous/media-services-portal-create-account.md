---
title: Azure portalı ile Azure Media Services hesabı oluşturma | Microsoft Belgeleri
description: Bu öğretici, Azure portalıyla bir Azure Media Services hesabı oluşturma adımlarında size kılavuzluk eder.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 53f0063d1458c218324d867c1942ae0e044f10d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011927"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Azure portal kullanarak Media Services hesabı oluşturma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure portalı bir Azure Media Services (AMS) hesabını hızlıca oluşturmanın yolunu sağlar. Azure’da medya içeriği depolamanıza, şifrelemenize, kodlamanıza, yönetmenize ve akış yapmanıza imkan tanıyan Media Services’e erişmek için hesabınızı kullanabilirsiniz. Media Services bir hesap oluşturduğunuzda, ilişkili bir depolama hesabı da oluşturursunuz (veya var olan bir depolama hesabı kullanmalısınız). Bir Media Services hesabını silerseniz ilişkili depolama hesabınızdaki blob’lar silinmez.

Media Services hesabı ve onunla ilişkili tüm depolama hesaplarının aynı Azure aboneliğinde olması gerekir. Gecikme süreleri ve veri çıkış maliyetleri ile karşı karşıya kalmamak için mutlaka Media Services ile aynı konumda bulunan depolama hesaplarını kullanmanız önerilir.

Bu makalede Azure portal kullanılarak Media Services hesabının nasıl oluşturulacağı gösterilmektedir.

> [!NOTE]
> Farklı bölgelerdeki Azure Media Services özelliklerinin kullanılabilirliği hakkında daha fazla bilgi için bkz. [bölgeler arasında AMS özellikleri](availability-regions-v-2.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-ams-account"></a>AMS hesabı oluşturma

Bu bölümdeki adımlar bir AMS hesabının nasıl oluşturulacağını gösterir.

1. [Azure portalda](https://portal.azure.com/) oturum açın.
2. **+ Kaynak**  >  **medyası** oluştur  >  **Media Services**' a tıklayın.
3. **MEDYA HİZMETLERİ HESABI OLUŞTUR**’a gerekli değerleri girin.

   1. **Hesap Adı**’nda, yeni AMS hesabının adını girin. Media Services hesabı adı, boşluk olmadan, tümü sayı ve küçük harften oluşmalı ve 3-24 karakter uzunluğunda olmalıdır.
   2. Abonelik’te, erişiminiz bulunan farklı Azure abonelikleri arasından seçim yapın.
   3. **Kaynak Grubu**’nda yeni veya mevcut bir kaynağı seçin.  Kaynak grubu; yaşam döngüsünü, izinleri ve ilkeleri paylaşan kaynakların bir koleksiyonudur. [Daha fazla bilgi edinin](../../azure-resource-manager/management/overview.md#resource-groups).
   4. **Konum**’da, Media Services hesabınız için medya ve meta veri kayıtlarını depolamak üzere kullanılacak coğrafi bölgeyi seçin. Bu bölge medyanızı işlemek ve akışını sağlamak için kullanılır. Yalnızca Media Services kullanılabilen bölgeler açılır listede görüntülenir. 
   5. **Depolama Hesabı** alanında, Media Services hesabınızdan gelen medya içeriğine blob depolama sağlamak üzere bir depolama hesabı seçin. Media Services hesabınızla aynı coğrafi bölgede bulunan mevcut bir depolama hesabını seçebilir ya da bir depolama hesabı oluşturabilirsiniz. Aynı bölgede yeni bir depolama hesabı oluşturulur. Depolama hesabı adları için kurallar Media Services hesapları ile aynıdır.

       Depolama hakkında daha fazla bilgi [burada](../../storage/common/storage-introduction.md).
   6. Hesap dağıtımını ilerleme durumunu görmek için **Panoya sabitle**’yi seçin.
4. Formun alt kısmındaki **Oluştur**’a tıklayın.

    Hesap başarıyla oluşturulduktan sonra genel bakış sayfası yüklenir. Akış uç noktası tablosunda hesapta **Durdurulmuş** durumda bir varsayılan akış uç noktası yer alır. 

    >[!NOTE]
    >AMS hesabınız oluşturulduğunda hesabınıza **durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir. 
   
## <a name="to-manage-your-ams-account"></a>AMS hesabınızı yönetmek için

AMS hesabınızı yönetmek için (örneğin, AMS API’ye programlama aracılığıyla bağlanarak karşıya video yükleme, varlıkları kodlama, içerik korumayı yapılandırma, iş ilerleme durumunu izleme) portalın sol tarafında bulunan **Ayarlar**’ı seçin. **Ayarlar**’da, kullanılabilir dikey pencerelerden birine (örneğin **API Erişimi**, **Varlıklar**, **İşler**, **İçerik koruma**) gidin.

## <a name="next-steps"></a>Sonraki adımlar

Bundan böyle dosyaları AMS hesabınıza yükleyebilirsiniz. Daha fazla bilgi için bkz. [Dosya yükleme](media-services-portal-upload-files.md).

AMS API’ye programlama aracılığıyla erişmeyi planlıyorsanız bkz. [Azure AD kimlik doğrulamasıyla Azure Media Services API’ye erişim](media-services-use-aad-auth-to-access-ams-api.md).

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
