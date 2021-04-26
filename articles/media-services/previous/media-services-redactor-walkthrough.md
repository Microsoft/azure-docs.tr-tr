---
title: Azure Media Analytics izlenecek yüzü redaksiyonu | Microsoft Docs
description: Bu konuda, Azure Media Services Explorer (AMI) ve Azure Media Redactor görselleştiricisi (açık kaynak aracı) kullanarak tam bir redaksiyon iş akışının nasıl çalıştırılacağı hakkında adım adım yönergeler gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: 1a7e20681dfa7da7ce30f46a7c4b0b6df6f78916
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009573"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Azure Media Analytics izlenecek yüzü redaksiyonu

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Genel Bakış

**Azure Media Redactor** , bulutta ölçeklenebilir yüz redaksiyon sağlayan bir [Azure Media Analytics](./legacy-components.md) medya işlemcisidir (MP). Yüz Redaksiyon, seçili kişilerin yüzlerini bulanıklaştırmak için videonuzu değiştirmenize olanak sağlar. Yüz redaksiyon hizmetini genel güvenlik ve haber medya senaryolarında kullanmak isteyebilirsiniz. Birden çok yüz içeren birkaç dakikalık bir çekimi, el ile redaksiyona kadar zaman alabilir, ancak bu hizmetle yüz redaksiyon süreci yalnızca birkaç basit adım gerektirir. Daha fazla bilgi için [Bu](https://azure.microsoft.com/blog/azure-media-redactor/) bloga bakın.

**Azure Media Redactor** hakkındaki ayrıntılar için bkz. [yüz redaksiyon genel bakış](media-services-face-redaction.md) konusu.

Bu konuda, Azure Media Services Explorer (AMI) ve Azure Media Redactor görselleştiricisi (açık kaynak aracı) kullanarak tam bir redaksiyon iş akışının nasıl çalıştırılacağı hakkında adım adım yönergeler gösterilmektedir.

Daha fazla bilgi için [Bu](https://azure.microsoft.com/blog/redaction-preview-available-globally) bloga bakın.

## <a name="azure-media-services-explorer-workflow"></a>Gezgin iş akışını Azure Media Services

Redactor ile çalışmaya başlamanın en kolay yolu GitHub 'da açık kaynak AMı aracını kullanmaktır. JSON ek açıklamasına veya yüz jpg görüntülerine erişmeniz gerekmiyorsa, **birleştirilmiş** mod aracılığıyla basitleştirilmiş bir iş akışı çalıştırabilirsiniz.

### <a name="download-and-setup"></a>İndir ve Kur

1. AMS v2 için AMSE aracını [buradan](https://aka.ms/amseforv2)indirin.
1. Hizmet anahtarınızı kullanarak Media Services hesabınızda oturum açın.

    Hesap adını ve anahtar bilgilerini almak için [Azure portalına](https://portal.azure.com/) gidin ve AMS hesabınızı seçin. Ardından, ayarlar > anahtarlar ' ı seçin. Anahtarları yönet pencerelerinde hesap adı gösterilir ve birincil anahtar ile ikincil anahtar görüntülenir. Hesap adı ve birincil anahtar değerlerini kopyalayın.

![Ekran görüntüsü, hesap adınızı ve anahtarınızı girebileceğiniz Microsoft Azure Media Services gösterir.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>İlk geçiş – analiz modu

1. Medya dosyanızı varlık – > karşıya yükleme veya sürükleme ve bırakma yoluyla karşıya yükleyin. 
1. Media Analytics – > Azure Media Redactor – > çözümleme modunu kullanarak medya dosyanızı sağ tıklayıp işleyin. 


![Ekran görüntüsünde, Azure Media Redactor olan Işlem varlıkları içeren bir menü gösterilir.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Ekran görüntüsü Ilk Pass Azure Media Redactor gösterir: çözümleme modu seçili.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Çıktı, yüz konumu verileri içeren bir ek açıklama JSON dosyası ve algılanan her bir yüzeyi de içerecektir. 

![Ekran görüntüsü Analize ait çıktıyı gösterir.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>İkinci geçiş – redakct modu

1. Özgün video varlığınızı ilk geçişte çıktıya yükleyin ve birincil varlık olarak ayarlayın. 

    ![Ekran görüntüsünde karşıya yükle ve birincil olarak ayarla düğmeleri gösterilir.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. Seçim Redaksiyona eklemek istediğiniz kimliklerin bir yeni satır sınırlı listesini içeren bir ' Dance_idlist.txt ' dosyasını karşıya yükleyin. 

    ![Ekran görüntüsü metin dosyasını karşıya yükleme seçeneğini gösterir.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. Seçim Dosyadaki annotations.js, sınırlayıcı kutu sınırlarını artırma gibi herhangi bir düzenleme yapın. 
4. İlk geçişte çıkış varlığına sağ tıklayın, Redactor ' ı seçin ve **Redakct** moduyla çalıştırın. 

    ![Ekran görüntüsünde Ikinci pass: Redakct modu seçiliyken Azure Media Redactor gösterilmektedir.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Son Redaksiyonu yapılmış çıkış varlığını indirin veya paylaşabilirsiniz. 

    ![Ekran görüntüsü Indir düğmesini gösterir.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor görselleştiricisi açık kaynak aracı

Açık kaynak [görselleştiricisi aracı](https://github.com/Microsoft/azure-media-redactor-visualizer) , geliştiricilerin yalnızca, çıktıyı ayrıştırarak ve kullanarak ek açıklama biçimiyle başlamasını sağlamaya yardımcı olmak için tasarlanmıştır.

Depoyu kopyaladıktan sonra, projeyi çalıştırmak için, [resmi sitesinden](https://ffmpeg.org/download.html)FFmpeg 'yi indirmeniz gerekir.

JSON ek açıklama verilerini ayrıştırmaya çalışan bir geliştiricisiyseniz, örnek kod örnekleri için modeller. MetaData içinde bakın.

### <a name="set-up-the-tool"></a>Aracı kurma

1.  Tüm çözümü indirin ve derleyin. 

    ![Ekran görüntüsü, menüden yapı çözümünü seçili olarak gösterir.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  FFMPEG 'yi [buradan](https://ffmpeg.org/download.html)indirin. Bu proje başlangıçta statik bağlama ile sürüm be1d324 (2016-10-04) ile geliştirilmiştir. 
3.  ffmpeg.exe ve ffprobe.exe AzureMediaRedactor.exe ile aynı çıkış klasörüne kopyalayın. 

    ![Ekran görüntüsü, ffmpeg ve ffaraştırması dahil olmak üzere klasörün içeriğini gösterir.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. AzureMediaRedactor.exe çalıştırın. 

### <a name="use-the-tool"></a>Aracı kullanma

1. Azure Media Services hesabınızda, analiz modundaki Redactor MP ile videonuzu işleyin. 
2. Orijinal video dosyasını ve redaksiyon-analiz işinin çıkışını indirin. 
3. Görselleştirici uygulamasını çalıştırın ve yukarıdaki dosyaları seçin. 

    ![Ekran görüntüsünde dosyaları karşıya yükleme Azure Media Redactor gösterilmektedir.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Dosyanızı önizleyin. Sağdaki kenar çubuğu aracılığıyla bulanıklaştırmak istediğiniz yüzleri seçin. 
    
    ![Ekran görüntüsünde, bulanıklaştırmak üzere yüzler önizlemesi oluşturabileceğiniz ve seçebileceğiniz Azure Media Redactor gösterilmektedir.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Alt metin alanı, yüz kimlikleri ile güncelleştirilecek. Bu kimliklerle "idlist.txt" adlı bir dosya oluşturun ve yeni bir liste oluşturun. 

    >[!NOTE]
    > idlist.txt ANSI 'ye kaydedilmelidir. Not defteri 'ni kullanarak ANSI 'ye kaydedebilirsiniz.
    
6.  Bu dosyayı 1. adımdaki çıkış varlığına yükleyin. Özgün videoyu da bu kıymete yükleyin ve birincil varlık olarak ayarlayın. 
7.  Son Redaksiyonu yapılmış videoyu almak için bu varlık üzerinde redaksiyon işini "Redact" moduyla çalıştırın. 

## <a name="next-steps"></a>Sonraki adımlar 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>İlgili bağlantılar
[Azure Media Services Analytics genel bakışı](./legacy-components.md)

[Azure Media Analytics gösterileri](http://amslabs.azurewebsites.net/demos/Analytics.html)

[Azure Media Analytics için yüz redaksiyon duyurusu](https://azure.microsoft.com/blog/azure-media-redactor/)