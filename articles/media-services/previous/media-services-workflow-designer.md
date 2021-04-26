---
title: İş Akışı Tasarımcısı ile gelişmiş kodlama Iş akışları oluşturun | Microsoft Docs
description: İş Akışı Tasarımcısı ile gelişmiş kodlama iş akışları oluşturma hakkında bilgi edinin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 744d302afd21e6521fb17bf7bef6e68d21fb8372
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639398"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>İş Akışı Tasarımcısı ile Gelişmiş Kodlama İş Akışları Oluşturma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Genel Bakış
**İş akışı Tasarımcısı** , **Media Encoder Premium Workflow** kodlamaya yönelik özel iş akışlarını tasarlamak ve derlemek için kullanılan bir Windows Masaüstü aracıdır.
İş akışı Tasarımcısı aracının gücünü kullanarak, **Medya Kodlayıcısı Premium**'da çalışacak karmaşık iş akışları tasarlayabilir ve oluşturabilirsiniz.  

İş akışları, giriş kaynak dosyasının özelliklerine göre müşteri karar mantığını ve dallanmayı içerebilir. En karmaşık kodlama görevlerinin bulutta tekrarlanmasını ve özelleştirmeyi kolay hale getirmek için geçersiz kılınabilir özelliklerle ve dinamik değerlerle iş akışları oluşturabilirsiniz.

Oluşturabileceğiniz örnek iş akışları şunlardır:

* Çözümleme için kaynak içeriğini denetleyen ve yalnızca istenen çıktıyı izleyen karar tabanlı iş akışları.  Bu, kaynak içeriğin yanlışlıkla ölçeklendirilmesi tarafından üretilecek olan harcanan parçaları ortadan kaldırarak yararlı olur.
* Birden çok giriş dosyası, açıklamalı alt yazıları, yer paylaşımlarını ve içeriği bir araya getirebilirsiniz. 

Bu araç, [yayımlanan iş akışlarımızdan](media-services-workflow-designer.md#existing_workflows)herhangi birini değiştirmek için de kullanılabilir. 

> [!NOTE]
> İş Akışı Tasarımcısı aracı kopyanızı almak için lütfen iletişim kurun mepd@microsoft.com .

Bir iş akışı dosyası oluşturulduktan sonra bir varlık olarak karşıya yüklenebilir ve ardından medya dosyalarını kodlamak için kullanılabilir. **.Net** kullanarak **Media Encoder Premium Workflow** ile kodlama hakkında daha fazla bilgi için bkz. [Media Encoder Premium Workflow ile gelişmiş kodlama](media-services-encode-with-premium-workflow.md).

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Mevcut iş akışlarını değiştirme
Varsayılan [yayımlanan iş akışları](media-services-workflow-designer.md#existing_workflows) tasarımcı aracı kullanılarak değiştirilebilir. Varsayılan iş akışı dosyalarını [buradan](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/media-services/previous/media-services-encode-with-premium-workflow.md)edinebilirsiniz. Klasör Ayrıca bu dosyaların açıklamasını içerir.

Aşağıdaki videolar tasarımcı 'nın nasıl kullanılacağını göstermektedir.

### <a name="day-1--getting-started"></a>Gün 1 – başlangıç
Gün 1 video şunları içerir:

* Tasarımcıya genel bakış
* Temel Iş akışları – "Merhaba Dünya"
* Azure Media Services akışı ile kullanmak için birden çok çıkış MP4 dosyası oluşturma

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Gün 2
Gün 2 video şunları içerir:

* Değişen kaynak dosya senaryoları – ses işleme
* Gelişmiş Logic ile iş akışları
* Grafik aşamaları

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Gün 3
Gün 3 video şunları içerir:

* Iş akışları/planlar içinde betik oluşturma
* Geçerli kodlayıcıyla ilgili kısıtlamalar
* Soru-Cevap

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Yardıma mı ihtiyacınız var?

[Yeni destek isteğine](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) giderek bir destek bileti açabilirsiniz

## <a name="next-step"></a>Sonraki adım
Media Services öğrenme yollarını gözden geçirin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca Bkz.
[Azure Premium Kodlayıcısı İş Akışı Tasarımcısı eğitim videoları](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

