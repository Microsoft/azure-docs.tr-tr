---
title: Portalda Azure Medya Clipper 'ı kullanma | Microsoft Docs
description: Azure portalından Azure Media Clipper kullanarak klipler oluşturma
services: media-services
keywords: klip; alt klip; kodlama; medya
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ec43fa469547dcd6481c0c6781c438f42ab4e2bd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684994"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Portalda Azure Medya Clipper ile klipler oluşturma  

Media Services hesaplarınızdaki varlıklardan klipler oluşturmak için portalda Azure Medya Clipper 'ı kullanabilirsiniz. Başlamak için portalda Medya Hizmetleri hesabınıza gidin. Sonra, **alt klip** sekmesini seçin.

**Alt klip** sekmesinde, küçük resim oluşturmaya başlayabileceksiniz. Portalda, Clipper tek bit hızlı MP4 'leri, çoklu bit hızına sahip MP4 'leri ve geçerli bir akış bulucu ile yayınlanan canlı arşivleri yükler. Yayımlanmamış varlıklar yüklenmez.

Clipper Şu anda genel önizleme aşamasındadır. Azure portal Clipper 'a erişmek için bu [genel önizleme sayfasına](https://portal.azure.com/?feature.subclipper=true)gidin.

Aşağıdaki görüntüde, Media Services hesabınızdaki Clipper giriş sayfası gösterilmektedir: Azure portal Azure Media Clipper ![](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Küçük resim üretme
Bir klip oluşturmak için, bir varlığı sürükle ve bir varlık için klip arabirimine bırakın. İşaretleme zamanları biliniyorsa, bunları arabirime el ile girebilirsiniz. Aksi takdirde, istenen işaretle ve işaretle zaman aşımı süresini bulmak için varlığı kayıttan veya oynatma kafasını sürükleyin. Bir işaret veya işaretleme zaman aşımı süresi sağlanmazsa, klip baştan başlar veya giriş varlığının sonuna kadar devam eder.

Çerçeve doğruluğu/GOP doğruluğu ile gezinmek için, çerçeve-ileri/GOP-ileri veya Frame-geri/GOP-geri düğmelerini kullanın. Birden çok varlığa göre kırpma için, birden çok varlığı, varlık seçim panelinden klip arabirimine sürükleyip bırakın. Arabirimdeki varlıkları istediğiniz sırada seçebilir ve yeniden sıralayabilirsiniz. Varlık seçimi paneli, her varlık için varlık süresi, tür ve çözüm meta verileri sağlar. Birden çok varlığı birlikte birleştirirken, her giriş dosyasının kaynak çözünürlüğünü göz önünde bulundurun. Kaynak çözünürlükleri farklıysa, daha düşük çözüm girişi, en yüksek çözünürlüklü varlığın çözümlenme uyacak şekilde ölçeklendirilir. Kırpma işinin çıkışını önizlemek için Önizleme düğmesini seçin ve klip seçili işaret süreleriyle oynatılır.

## <a name="producing-dynamic-manifest-filters"></a>Dinamik bildirim filtreleri üretme
[Dinamik bildirim filtreleri](https://azure.microsoft.com/blog/dynamic-manifest/) , bildirim özniteliklerini ve varlık zaman çizelgesini temel alan bir kurallar kümesini anlatmaktadır. Bu kurallar, akış uç noktanızın çıkış çalma listesini (bildirim) nasıl kullanacağını belirlemektir. Filtre, kayıttan yürütme için akan segmentleri değiştirmek üzere kullanılabilir. Clipper tarafından üretilen filtreler yerel filtrelerdir ve kaynak varlığa özeldir. Oluşturulan kliplerin aksine, filtreler yeni varlıklar değildir ve bir kodlama işinin üretilmesi gerekmez. Bunlar, [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) veya [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest)aracılığıyla hızlı bir şekilde oluşturulabilir, ancak yalnızca GOP-doğru olur. Genellikle, akış için kodlanan varlıkların iki saniyelik bir GOP boyutu vardır.

Dinamik bir bildirim filtresi oluşturmak için **varlıklar** sekmesine gidin ve istediğiniz varlığı seçin. Üst menü için **alt klip** düğmesini seçin. Gelişmiş ayarlar menüsünden kırpma modu olarak dinamik bildirim filtresi ' ni seçin. Daha sonra, filtre oluşturmak için işlenmiş bir klip oluşturmak üzere aynı işlemi izleyebilirsiniz. Filtreler yalnızca tek bir varlıktan üretilebilirler.

Aşağıdaki görüntüde, Azure portal dinamik bildirim filtresi modunda Azure Medya Clipper ![Azure portal: dinamik bildirim filtresi modundaki Clipper gösterilmektedir](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Kırpma işleri gönderme
Klibi oluşturmayı bitirdiğinizde, karşılık gelen kırpma işini veya dinamik bildirim çağrısını başlatmak için işi Gönder düğmesini seçin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Medya Clipper 'ı kullanmaya başlamak için, pencere öğesinin nasıl dağıtılacağı hakkındaki ayrıntılar için [Başlarken](media-services-azure-media-clipper-getting-started.md) makalesini okuyun.
