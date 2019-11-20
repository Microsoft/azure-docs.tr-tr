---
title: Azure Media Analytics izlenecek yüzü redaksiyonu | Microsoft Docs
description: Bu konuda, Azure Media Services Explorer (AMI) ve Azure Media Redactor görselleştiricisi (açık kaynak aracı) kullanarak tam bir redaksiyon iş akışının nasıl çalıştırılacağı hakkında adım adım yönergeler gösterilmektedir.
services: media-services
documentationcenter: ''
author: Lichard
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: a8db8de6ef062dcf757f3d264379677d6550ea3a
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997686"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Azure Media Analytics izlenecek yüzü redaksiyonu

## <a name="overview"></a>Genel Bakış

**Azure Media Redactor** , bulutta ölçeklenebilir yüz redaksiyon sağlayan bir [Azure Media Analytics](media-services-analytics-overview.md) medya işlemcisidir (MP). Yüz Redaksiyon, seçili kişilerin yüzlerini bulanıklaştırmak için videonuzu değiştirmenize olanak sağlar. Yüz redaksiyon hizmetini genel güvenlik ve haber medya senaryolarında kullanmak isteyebilirsiniz. Birden çok yüz içeren birkaç dakikalık bir çekimi, el ile redaksiyona kadar zaman alabilir, ancak bu hizmetle yüz redaksiyon süreci yalnızca birkaç basit adım gerektirir. Daha fazla bilgi için [Bu](https://azure.microsoft.com/blog/azure-media-redactor/) bloga bakın.

**Azure Media Redactor**hakkındaki ayrıntılar için bkz. [yüz redaksiyon genel bakış](media-services-face-redaction.md) konusu.

Bu konuda, Azure Media Services Explorer (AMI) ve Azure Media Redactor görselleştiricisi (açık kaynak aracı) kullanarak tam bir redaksiyon iş akışının nasıl çalıştırılacağı hakkında adım adım yönergeler gösterilmektedir.

Daha fazla bilgi için [Bu](https://azure.microsoft.com/blog/redaction-preview-available-globally) bloga bakın.

## <a name="azure-media-services-explorer-workflow"></a>Gezgin iş akışını Azure Media Services

Redactor ile çalışmaya başlamanın en kolay yolu GitHub 'da açık kaynak AMı aracını kullanmaktır. JSON ek açıklamasına veya yüz jpg görüntülerine erişmeniz gerekmiyorsa, **birleştirilmiş** mod aracılığıyla basitleştirilmiş bir iş akışı çalıştırabilirsiniz.

### <a name="download-and-setup"></a>İndir ve Kur

1. AMS v2 için AMSE aracını [buradan](https://aka.ms/amseforv2)indirin.
1. Hizmet anahtarınızı kullanarak Media Services hesabınızda oturum açın.

    Hesap adını ve anahtar bilgilerini almak için [Azure portalına](https://portal.azure.com/) gidin ve AMS hesabınızı seçin. Ardından, Ayarlar > anahtarlar ' ı seçin. Anahtarları yönet pencerelerinde hesap adı gösterilir ve birincil anahtar ile ikincil anahtar görüntülenir. Hesap adı ve birincil anahtar değerlerini kopyalayın.

![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>İlk geçiş – analiz modu

1. Medya dosyanızı varlık – > karşıya yükleme veya sürükleme ve bırakma yoluyla karşıya yükleyin. 
1. Media Analytics – > Azure Media Redactor – > çözümleme modunu kullanarak medya dosyanızı sağ tıklayıp işleyin. 


![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Çıktı, yüz konumu verileri içeren bir ek açıklama JSON dosyası ve algılanan her bir yüzeyi de içerecektir. 

![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>İkinci geçiş – redakct modu

1. Özgün video varlığınızı ilk geçişte çıktıya yükleyin ve birincil varlık olarak ayarlayın. 

    ![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. Seçim Redaksiyona eklemek istediğiniz kimliklerin bir yeni satır sınırlı listesini içeren bir ' Dance_idlist. txt ' dosyası yükleyin. 

    ![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. Seçim Ek açıklamalar. json dosyasında, sınırlama kutusu sınırlarını artırma gibi herhangi bir düzenleme yapın. 
4. İlk geçişte çıkış varlığına sağ tıklayın, Redactor ' ı seçin ve **Redakct** moduyla çalıştırın. 

    ![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Son Redaksiyonu yapılmış çıkış varlığını indirin veya paylaşabilirsiniz. 

    ![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor görselleştiricisi açık kaynak aracı

Açık kaynak [görselleştiricisi aracı](https://github.com/Microsoft/azure-media-redactor-visualizer) , geliştiricilerin yalnızca, çıktıyı ayrıştırarak ve kullanarak ek açıklama biçimiyle başlamasını sağlamaya yardımcı olmak için tasarlanmıştır.

Depoyu kopyaladıktan sonra, projeyi çalıştırmak için, [resmi sitesinden](https://ffmpeg.org/download.html)FFmpeg 'yi indirmeniz gerekir.

JSON ek açıklama verilerini ayrıştırmaya çalışan bir geliştiricisiyseniz, örnek kod örnekleri için modeller. MetaData içinde bakın.

### <a name="set-up-the-tool"></a>Aracı kurma

1.  Tüm çözümü indirin ve derleyin. 

    ![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  FFMPEG 'yi [buradan](https://ffmpeg.org/download.html)indirin. Bu proje başlangıçta statik bağlama ile sürüm be1d324 (2016-10-04) ile geliştirilmiştir. 
3.  FFmpeg. exe ve ffaraştırma. exe ' yi Azudüzeltici Aredactor. exe ile aynı çıkış klasörüne kopyalayın. 

    ![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Azudüzeltici Aredadctor. exe ' yi çalıştırın. 

### <a name="use-the-tool"></a>Aracı kullanma

1. Azure Media Services hesabınızda, analiz modundaki Redactor MP ile videonuzu işleyin. 
2. Orijinal video dosyasını ve redaksiyon-analiz işinin çıkışını indirin. 
3. Görselleştirici uygulamasını çalıştırın ve yukarıdaki dosyaları seçin. 

    ![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Dosyanızı önizleyin. Sağdaki kenar çubuğu aracılığıyla bulanıklaştırmak istediğiniz yüzleri seçin. 
    
    ![Yüz flulaştırma](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Alt metin alanı, yüz kimlikleri ile güncelleştirilecek. "Idlist. txt" adlı bir dosya oluşturun ve bu kimlikleri yeni bir satır sınırlı listesi olarak oluşturun. 

    >[!NOTE]
    > Idlist. txt dosyası ANSI 'ye kaydedilmelidir. Not defteri 'ni kullanarak ANSI 'ye kaydedebilirsiniz.
    
6.  Bu dosyayı 1. adımdaki çıkış varlığına yükleyin. Özgün videoyu da bu kıymete yükleyin ve birincil varlık olarak ayarlayın. 
7.  Son Redaksiyonu yapılmış videoyu almak için bu varlık üzerinde redaksiyon işini "Redact" moduyla çalıştırın. 

## <a name="next-steps"></a>Sonraki adımlar 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>İlgili bağlantılar
[Azure Media Services Analytics genel bakışı](media-services-analytics-overview.md)

[Azure Media Analytics gösterileri](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Azure Media Analytics için yüz redaksiyon duyurusu](https://azure.microsoft.com/blog/azure-media-redactor/)
