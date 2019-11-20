---
title: Azure portal ile çoklu bit hızına sahip akışlar oluşturmak için Azure Media Services kullanarak canlı akış gerçekleştirme | Microsoft Docs
description: Bu öğreticide, tek bit hızında bir canlı akışı alıp Azure portalını kullanarak çoklu bit hızında akışa kodlayan bir Kanal oluşturulması adım adım anlatılmaktadır.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 504f74c2-3103-42a0-897b-9ff52f279e23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 3217a2d5ba3d45a069eacdb67a8d69e9abc674b8
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2019
ms.locfileid: "69015203"
---
# <a name="perform-live-streaming-using-media-services-to-create-multi-bitrate-streams-with-azure-portal"></a>Azure portal ile çoklu bit hızına sahip akışlar oluşturmak için Media Services kullanarak canlı akış gerçekleştirme  
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [REST API](https://docs.microsoft.com/rest/api/media/operations/channel)
> 

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

Bu öğreticide, tek bit hızında bir canlı akışı alıp çoklu bit hızında akışa kodlayan bir **Kanal** oluşturulması adım adım anlatılmaktadır.

Gerçek zamanlı kodlama için etkinleştirilmiş Kanallar ile ilgili daha fazla kavramsal bilgi için bkz. [Çoklu bit hızı akışları oluşturmak için Azure Media Services kullanarak canlı akış](media-services-manage-live-encoder-enabled-channels.md).

## <a name="common-live-streaming-scenario"></a>Ortak Canlı Akış Senaryosu
Yaygın canlı akış uygulamaları oluşturmak için gerekli olan genel adımlar aşağıdadır.

> [!NOTE]
> Canlı bir etkinlik için önerilen en uzun süre şu anda 8 saattir. Daha uzun bir süre için bir Kanal çalıştırmanız gerekiyorsa lütfen amshelp@microsoft.com adresine başvurun.

1. Bilgisayara bir video kamera bağlayın. <br/>Kurulum fikirleri için [basit ve taşınabilir olay video dişli kurulumuna]( https://link.medium.com/KNTtiN6IeT)göz atın.

    Bir kameraya erişiminiz yoksa, [Telestream kablolu dönüştürme](https://www.telestream.net/wirecast/overview.htm) gibi araçlar, bir video dosyasından canlı bir akış oluşturmak için kullanılabilir.
1. Aşağıdaki protokollerden birinde tek bit hızlı bir akış çıktısı veren şirket içi bir Live Encoder başlatın ve yapılandırın: RTMP veya Kesintisiz Akış. Daha fazla bilgi için bkz. [Azure Media Services RTMP Desteği ve Gerçek Zamanlı Kodlayıcılar](https://go.microsoft.com/fwlink/?LinkId=532824). <br/>Ayrıca, bu bloga göz atın: [OBS Ile canlı akış üretimi](https://link.medium.com/ttuwHpaJeT).

    Bu adım, Kanalınızı oluşturduktan sonra da gerçekleştirilebilir.
1. Bir Kanal oluşturup başlatın. 
1. Kanal alma URL’sini alın. 

    Alma URL’si gerçek zamanlı kodlayıcı tarafından akışı Kanala göndermek için kullanılır.
1. Kanal önizleme URL’sini alın. 

    Kanalınızın canlı akışı düzgün şekilde aldığını doğrulamak için bu URL’yi kullanın.
1. Bir olay/program oluşturun (ayrıca bir varlık oluşturur). 
1. Olayı yayımlayın (ilişkili varlığa yönelik bir OnDemand bulucu oluşturulur).    
1. Akışa ve arşivlemeye hazır olduğunuzda olayı başlatın.
1. İsteğe bağlı olarak, gerçek zamanlı kodlayıcıya bir reklam başlatması bildirilebilir. Reklam, çıktı akışına eklenir.
1. Olay akışını ve arşivlemeyi durdurmak istediğinizde, olayı durdurun.
1. Olayı silin (ve isteğe bağlı olarak varlığı silin).   

## <a name="prerequisites"></a>Önkoşullar

Öğreticiyi tamamlamak için aşağıdakiler gereklidir.

* Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. Bir hesabınız yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. 
  Ayrıntılar için bkz [Azure ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/).
* Bir Media Services hesabı. Media Services hesabı oluşturma konusunda bilgi edinmek için bkz. [Hesap Oluşturma](media-services-portal-create-account.md).
* Bir web kamerası ve tek bit hızlı bir canlı akış gönderebilen bir kodlayıcı.

## <a name="create-a-channel"></a>Kanal oluşturma

1. [Azure portalında](https://portal.azure.com/), Media Services'i seçin ve ardından Media Services hesabınızın adına tıklayın.
2. **Canlı Akış**’ı seçin.
3. **Özel oluştur**’u seçin. Bu seçenek canlı kodlama için etkinleştirilmiş bir kanal oluşturmanızı sağlar.

    ![Kanal oluşturma](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
4. **Ayarlar**’a tıklayın.

   1. **Live Encoding** kanal türünü seçin. Bu tür, gerçek zamanlı kodlama için etkinleştirilmiş bir Kanal oluşturmak istediğinizi belirtir. Bu durumda, gelen tek bit hızlı akış Kanala gönderilir ve belirlenmiş gerçek zamanlı kodlayıcı ayarları kullanılarak çoklu bit hızına sahip bir akışa kodlanır. Daha fazla bilgi için bkz. [Çoklu bit hızına sahip akışlar oluşturmak üzere Azure Media Services’i kullanarak canlı akış gerçekleştirme](media-services-manage-live-encoder-enabled-channels.md). Tamam'a tıklayın.
   2. Bir kanalın adını belirtin.
   3. Ekranın alt kısmındaki Tamam’a tıklayın.
5. **Al** sekmesini seçin.

   1. Bu sayfada bir akış protokolü seçebilirsiniz. **Live Encoding** kanal türü için geçerli protokol seçenekleri şunlardır:

      * Tek bit hızlı Parçalanmış MP4 (Kesintisiz Akış)
      * Tek bit hızlı RTMP

        Her protokole ait detaylı bilgiler için bkz. [Çoklu bit hızına sahip akışlar oluşturmak üzere Azure Media Services’i kullanarak canlı akış gerçekleştirme](media-services-manage-live-encoder-enabled-channels.md).

        Kanal veya ilişkili olayları/programları çalışıyorken protokol seçeneğini değiştiremezsiniz. Farklı protokollere ihtiyacınız varsa her bir akış protokolü için farklı bir kanal oluşturmalısınız.  
   2. Alma işlemine IP kısıtlaması uygulayabilirsiniz. 

       Bu kanala video almasına izin verilen IP adreslerini tanımlayabilirsiniz. İzin verilen IP adresleri, tek bir IP adresi (ör. '10.0.0.1'), bir IP adresi ve CIDR alt ağ maskesi kullanarak bir IP aralığı (ör. ‘10.0.0.1/22’) veya bir IP adresi ve noktalı ondalık alt ağ maskesi kullanarak bir IP aralığı (ör. '10.0.0.1(255.255.252.0)').

       Herhangi bir IP adresi belirtilmezse ve bir kural tanımı yoksa hiçbir IP adresine izin verilmez. Tüm IP adreslerine izin vermek için, bir kural oluşturun ve 0.0.0.0/0 olarak ayarlayın.
6. **Önizleme** sekmesinde önizlemeye IP kısıtlaması uygulayın.
7. **Kodlama** sekmesinde kodlama ön ayarını belirtin. 

    Şu anda seçebileceğiniz tek sistem önayarı **Varsayılan 720p** seçeneğidir. Özel bir ön ayar belirtmek için bir Microsoft destek bileti açın. Ardından, sizin için oluşturulan ön ayarın adını girin. 

> [!NOTE]
> Şu anda, bir Kanalın başlaması 30 dakika kadar sürebilir. Kanal sıfırlanması 5 dakika kadar sürebilir.
> 
> 

Kanalı oluşturduktan sonr, Kanal’a tıklayıp **Ayarlar**’ı seçerek kanal yapılandırmalarınızı görüntüleyebilirsiniz. 

Daha fazla bilgi için bkz. [Çoklu bit hızına sahip akışlar oluşturmak üzere Azure Media Services’i kullanarak canlı akış gerçekleştirme](media-services-manage-live-encoder-enabled-channels.md).

## <a name="get-ingest-urls"></a>Alma URL’leri alma
Kanal oluşturulduktan sonra, gerçek zamanlı kodlayıcıya sağlayacağınız alma URL’lerini alabilirsiniz. Kodlayıcı bu URL'leri canlı akış girişi için kullanır.

![Alma URL 'leri](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)

## <a name="create-and-manage-events"></a>Olay oluşturma ve yönetme

### <a name="overview"></a>Genel Bakış
Bir kanal, canlı akıştaki kesimleri yayımlamanızı ve depolamanızı denetlemenizi sağlayan olaylar/programlarla ilişkilidir. Olayları/programları kanallar yönetir. Kanal ve Program arasındaki ilişki, kanalın sürekli bir içerik akışının bulunduğu ve programın bu kanalda zamanlanmış bir olayı kapsadığı geleneksel medyadaki ilişkiye benzer.

Olay için kaydedilen içeriği kaç saat tutmak istediğinizi **Arşiv Penceresi** uzunluğunu ayarlayarak belirleyebilirsiniz. Bu değer en az 5 dakika, en çok 25 saat olarak ayarlanabilir. Arşiv penceresi uzunluğu, istemcilerin geçerli canlı konumdan zamanda geri gidebilecekleri en uzun süre miktarını da belirler. Olaylar belirtilen süre miktarında yürütülür, ancak pencere uzunluğunu aşan içerik sürekli olarak iptal edilir. Bu özelliğin bu değeri, istemci bildiriminin ne kadar uzayabileceğini de belirler.

Her olay bir Varlıkla ilişkilidir. Olayı yayımlamak için ilişkili varlığa yönelik bir OnDemand bulucu oluşturmanız gerekir. Bu bulucuya sahip olmak, istemcilerinize sağlayabileceğiniz bir akış URL’si oluşturmanıza olanak tanır.

Bir kanal eşzamanlı çalışan üç olaya kadar destekler, böylece aynı gelen akışta birden fazla arşiv oluşturabilirsiniz. Bu özellik, gerektiğinde bir olayın farklı kısımlarını yayımlamanıza ve arşivlemenize olanak tanır. Örneğin, işiniz gereği bir olayın 6 saatini arşivlerken yalnızca son 10 dakikasını yayınlamanız gerekebilir. Bunu yapmak için, eşzamanlı olarak çalışan iki olay oluşturmanız gerekir. 6 saatlik olayı arşivlemek için bir olay ayarlanır ancak program yayımlanmaz. Diğer olay 10 dakika arşivlenecek şekilde ayarlanır ve bu program yayımlanır.

Yeni olaylar için mevcut programları yeniden kullanmamalısınız. Bunun yerine, her olay için yeni bir program oluşturup başlatın.

Akışı ve arşivlemeyi başlatmaya hazır olduğunuzda bir olay/program başlatın. Olay akışını ve arşivlemeyi durdurmak istediğinizde, olayı durdurun. 

Arşivlenen içeriği silmek için, olayı durdurun ve ardından ilişkili varlığı silin. Olay tarafından kullanılan bir varlık silinemez; önce olayın silinmesi gerekir. 

Olayı durdurduktan ve sildikten sonra dahi, varlığı silmeniz sürece, kullanıcılar arşivlenen içeriğinizin isteğe bağlı içerik olarak akışını gerçekleştirebilir.

Arşivlenen içeriği tutmak istiyor ancak bu içeriğin akış için kullanılmasını istemiyorsanız, akış bulucuyu silin.

### <a name="createstartstop-events"></a>Olay oluşturma/başlatma/durdurma
Akışın Kanala akması sağlandıktan sonra bir Varlık, Program ve Akış Bulucu oluşturarak akış olayını başlatabilirsiniz. Bu olay, akışı arşivler ve akışın Akış Uç Noktası aracılığıyla izleyiciler tarafından kullanılabilmesini sağlar. 

>[!NOTE]
>AMS hesabınız oluşturulduğunda hesabınıza **Durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir. 

Olayı başlatmanın iki yolu vardır: 

1. **Kanal** sayfasında **Canlı Olay**’a basarak yeni bir olay ekleyin.

    Şunları belirleyin: olay adı, varlık adı, arşiv penceresi ve şifreleme seçeneği.

    ![program oluştur](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)

    **Bu canlı olayı şimdi yayımla** seçeneğini işaretli bıraktıysanız URL'leri YAYIMLAMA olayı oluşturulacaktır.

    Olayı yayınlamaya hazır olduğunuzda **Başlat** tuşuna basabilirsiniz.

    Olayı başlattıktan sonra, içeriği oynatmayı başlatmak için **İzle**’ye basabilirsiniz.
2. Alternatif olarak, bir kısayol kullanıp **Kanal** sayfasındaki **Yayını Başlat** düğmesine basabilirsiniz. Bunun yapılması varsayılan bir Varlık, Program ve Akış Bulucu oluşturur.

    Olaya **default** adı verilir ve arşiv penceresi 8 saat olarak ayarlanır.

Yayımlanmış olayı **Canlı olay** sayfasından izleyebilirsiniz. 

**Çevrimdışı** seçeneğine tıklarsanız tüm canlı olaylar durdurulur. 

## <a name="watch-the-event"></a>Olayı izleme
Olay izlemek için, Azure portalda **İzle**’ye tıklayın veya akış URL'sini kopyalayın ve tercih ettiğiniz bir oynatıcı kullanın. 

![Oluşturuldu](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Canlı olay durduğunda otomatik olarak isteğe bağlı içeriğe dönüşür.

## <a name="clean-up"></a>Temizleme
Olayların akışla aktarılmasını tamamlayıp önceden sağlanan kaynakları temizlemek istediğinizde aşağıdaki yordamı izleyin.

* Kodlayıcıdan akışı göndermeyi durdurun.
* Kanalı durdurun. Kanal durdurulduktan sonra herhangi bir ücret uygulanmaz. Tekrar başlatmanız gerektiğinde, aynı alma URL’sine sahip olacağından kodlayıcıyı yeniden yapılandırmanız gerekmez.
* Canlı olayınızın arşivini isteğe bağlı bir akış olarak sunmaya devam etmek istemiyorsanız Akış Uç Noktanızı durdurabilirsiniz. Kanalın durumu, durdurulmuş ise herhangi bir ücret uygulanmaz.

## <a name="view-archived-content"></a>Arşivlenen içeriği görüntüleme
Olayı durdurduktan ve sildikten sonra dahi, varlığı silmeniz sürece, kullanıcılar arşivlenen içeriğinizin isteğe bağlı içerik olarak akışını gerçekleştirebilir. Bir olay tarafından kullanılıyorsa varlık silinemez; önce olayın silinmesi gerekir. 

Varlıklarınızı yönetmek için, **Ayar**’ı seçin ve **Varlıklar**’a tıklayın.

![Varlıklar](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

## <a name="considerations"></a>Dikkat edilmesi gerekenler
* Canlı bir etkinlik için önerilen en uzun süre şu anda 8 saattir. Daha uzun bir süre için bir Kanal çalıştırmanız gerekiyorsa lütfen amshelp@microsoft.com adresine başvurun.
* İçerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumunda olduğundan emin olun.

## <a name="next-step"></a>Sonraki adım
Media Services öğrenme yollarını gözden geçirin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

