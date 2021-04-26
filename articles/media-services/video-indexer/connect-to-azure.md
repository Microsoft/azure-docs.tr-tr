---
title: Azure 'a bağlı bir Video Indexer hesabı oluşturun
titleSuffix: Azure Media Services
description: Azure 'a bağlı Video Indexer hesabı oluşturmayı öğrenin.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 35d01fd60d0549a528a45013add48bd203b86d6e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654688"
---
# <a name="create-a-video-indexer-account"></a>Video Indexer hesabı oluşturma

Video Indexer hesabınızı oluştururken ücretsiz bir deneme hesabı (belirli sayıda ücretsiz dizin oluşturma dakikası elde edersiniz) veya ücretli bir seçenek (kota sınırlaması olmaz) arasından seçim yapabilirsiniz. Ücretsiz deneme kullanıldığında Video Indexer, web sitesi kullanıcılarına 600 dakikaya kadar ve API kullanıcılarına ise 2400 dakikaya kadar ücretsiz dizin oluşturma olanağı sunar. Ücretli seçenekle, Azure aboneliğinize bağlı bir Video Indexer hesabı oluşturursunuz. Dizin oluşturma için ödeme yaparsınız. daha fazla bilgi için bkz. [Media Services fiyatlandırması](https://azure.microsoft.com/pricing/details/media-services/).

Bu makalede, bir Azure aboneliğine ve bir Azure Media Services hesabına bağlı Video Indexer hesabının nasıl oluşturulacağı gösterilmektedir. Bu konuda otomatik (varsayılan) Flow kullanılarak Azure 'a bağlanmak için gereken adımlar sağlanmaktadır. Ayrıca, Azure 'a el ile nasıl bağlanagösterdiğini gösterir (Gelişmiş).

*Deneme* sürümünden *ücretli* video Indexer hesabına geçiş yapıyorsanız tüm videolar ve model özelleştirmesini, [içeriğinizi deneme hesabından içeri aktarma](#import-your-content-from-the-trial-account) bölümünde anlatıldığı şekilde yeni hesaba kopyalamayı seçebilirsiniz.

Makale Ayrıca [video Indexer hesabının Azure Kamu 'ya](#video-indexer-in-azure-government)bağlanmasını da içerir.

## <a name="prerequisites-for-connecting-to-azure"></a>Azure 'a bağlanma önkoşulları

* Azure aboneliği.

    Henüz bir Azure aboneliğiniz yoksa [Azure Ücretsiz deneme sürümü](https://azure.microsoft.com/free/)için kaydolun.
* Azure Active Directory (Azure AD) etki alanı.

    Azure AD etki alanınız yoksa, Azure aboneliğinizle bu etki alanını oluşturun. Daha fazla bilgi için bkz. [Azure AD 'de özel etki alanı adlarını yönetme](../../active-directory/enterprise-users/domains-manage.md)
* Azure AD etki alanındaki bir **Uygulama Yöneticisi** rolüyle bir kullanıcı. Video Indexer hesabınızı Azure 'a bağlarken bu üyeyi kullanacaksınız.

    Bu kullanıcının iş veya okul hesabı olan bir Azure AD kullanıcısı olması gerekir. Outlook.com, live.com veya hotmail.com gibi bir kişisel hesap kullanmayın.

    ![Tüm Azure AD kullanıcıları](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Otomatik akış için ek önkoşullar

* Azure AD etki alanındaki bir Kullanıcı ve üye.

    Video Indexer hesabınızı Azure 'a bağlarken bu üyeyi kullanacaksınız.

    Bu Kullanıcı, bir **sahip** rolü ya da **katkıda** bulunan ve **Kullanıcı erişimi yönetici** rolleri ile Azure aboneliğinizde bir üye olmalıdır. Bir Kullanıcı iki rol ile iki kez eklenebilir. Katkıda bulunan ve Kullanıcı erişimi yöneticisiyle bir kez. Daha fazla bilgi için bkz. [kullanıcının Azure kaynaklarına erişimi görüntüleme](../../role-based-access-control/check-access.md).

    ![erişim denetimi](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>El ile akışa yönelik ek önkoşullar

* Azure portal kullanarak EventGrid kaynak sağlayıcısını kaydedin.

    [Azure Portal](https://portal.azure.com/) **abonelikler**-> [abonelik]->**resourceproviders**' a gidin.

    **Microsoft. Media** ve **Microsoft. eventgrid** için arama yapın. "Kayıtlı" durumunda değilse, **Kaydet**' e tıklayın. Kaydolmak birkaç dakika sürer.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="create-a-new-account-on-azure"></a>Azure 'da yeni bir hesap oluşturun 

> [!NOTE]
> Azure aboneliğiniz sertifika tabanlı Multi-Factor Authentication kullanıyorsa, gerekli sertifikaların yüklü olduğu bir cihazda aşağıdaki adımları gerçekleştirmeniz çok önemlidir.

1. [Video Indexer](https://www.videoindexer.ai/) web sitesine gidip oturum açın.
1. **Sınırsız hesap oluştur** düğmesini seçin:

    ![Yeni Video Indexer hesabı oluştur](./media/create-account/create-unlimited-account.png)
1. Abonelikler listesi göründüğünde, kullanmak istediğiniz aboneliği seçin.

    ![Video Indexer Azure 'a bağlama](./media/create-account/new-account-on-azure-subscription.png)
1. Desteklenen konumlardan bir Azure bölgesi seçin: Batı ABD 2, Kuzey Avrupa veya Doğu Asya.
1. **Azure Media Services hesabı** altında şu seçeneklerden birini seçin:

    * Yeni bir Media Services hesabı oluşturmak için **Yeni kaynak grubu oluştur**' u seçin. Kaynak grubunuz için bir ad girin.

        Azure yeni bir Azure depolama hesabı da dahil olmak üzere aboneliğinizde yeni hesabınızı oluşturacak.  
    * Mevcut bir Media Services hesabını kullanmak için **mevcut kaynağı kullan**' ı seçin. Hesaplar listesinden hesabınızı seçin.

        Media Services hesabınız Video Indexer hesabınızla aynı bölgeye sahip olmalıdır.

        > [!NOTE]
        > Dizin oluşturma süresini ve düşük aktarım hızını en aza indirmek için, Media Services hesabınızdaki [ayrılmış birimlerin](../previous/media-services-scale-media-processing-overview.md ) türünü ve sayısını **10 S3 ayrılmış birimlerine** ayarlamanız önemle önerilir. Bkz. [ayrılmış birimleri değiştirmek için portalı kullanma](../previous/media-services-portal-scale-media-processing.md). Ayrılmış birimler hesabınıza ücretlendirilir, [fiyatlandırma ayrıntılarını](https://azure.microsoft.com/pricing/details/media-services/#analytics)görüntüleyin.
    * Bağlantınızı el ile yapılandırmak için **el ile yapılandırma bağlantısına geç** bağlantısını seçin.

        Ayrıntılı bilgi için, aşağıda bulunan [Azure 'a El Ile bağlanma](#connect-to-azure-manually-advanced-option) (Gelişmiş seçenek) bölümüne bakın.
1. İşiniz bittiğinde **Oluştur**’u seçin. Bu işlem birkaç dakika sürebilir.

    Azure 'a bağlandıktan sonra yeni Video Indexer hesabınız hesap listesinde görüntülenir:

    ![Yeni hesap](./media/create-account/new-account.png)
1. Video Indexer Web uygulamasında videolarınızı oynatabilmeniz için Media Services hesabının akış uç noktasının çalıştığından emin olun (durdurulmuş durumdaysa Başlat ' a basın).

> [!TIP]
> Hesabınız için kolay bir görüntü sağlamak üzere **Ayarlar**' a gidin.

## <a name="connect-to-azure-manually-advanced-option"></a>Azure 'a el ile bağlanma (Gelişmiş seçenek)

Azure bağlantısı başarısız olduysa, el ile bağlanarak sorunu gidermeyi deneyebilirsiniz.

> [!NOTE]
> Aynı bölgede aşağıdaki üç hesabın olması önerilir: Media Services hesabıyla bağlanmakta olduğunuz Video Indexer hesap ve aynı Media Services hesabına bağlı Azure depolama hesabı.

### <a name="create-and-configure-a-media-services-account"></a>Media Services hesabı oluşturma ve yapılandırma

1. [Hesap oluşturma](../previous/media-services-portal-create-account.md)bölümünde açıklandığı gibi bir Azure Media Services hesabı oluşturmak için [Azure](https://portal.azure.com/) portalını kullanın.

     Media Services hesabının klasik API 'lerle oluşturulduğundan emin olun. 
 
    ![Media Services klasik API](./media/create-account/enable-classic-api.png)


    Media Services hesabınız için bir depolama hesabı oluştururken, çoğaltma alanları için hesap türü ve coğrafi olarak **yedekli** (GRS) için **StorageV2** öğesini seçin.

    ![Yeni AMS hesabı](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > Media Services kaynak ve hesap adlarını yazdığınızdan emin olun. Sonraki bölümde yer aldığı adımlarda bunlara ihtiyacınız olacak.
1. Oluşturulan Media Services hesapta, [ayrılmış birimlerin](../previous/media-services-scale-media-processing-overview.md ) türünü ve sayısını **10 S3 ayrılmış birim** olarak ayarlayın. Bkz. [ayrılmış birimleri değiştirmek için portalı kullanma](../previous/media-services-portal-scale-media-processing.md).

    Ayrılmış birimler hesabınıza ücretlendirilir, [fiyatlandırma ayrıntılarını](https://azure.microsoft.com/pricing/details/media-services/#analytics)görüntüleyin. s
1. Video Indexer Web uygulamasında videolarınızı oynatabilmeniz için önce yeni Media Services hesabının varsayılan **akış uç noktasını** başlatmanız gerekir.

    Yeni Media Services hesabında, **akış uç noktaları**' nı seçin. Ardından akış uç noktasını seçip Başlat ' a basın.

    ![Akış uç noktaları](./media/create-account/create-ams-account-se.png)
4. Media Services API 'SI ile kimlik doğrulaması yapmak Video Indexer için, bir AD uygulamasının oluşturulması gerekir. Aşağıdaki adımlar, [Azure Portal kullanarak Azure AD kimlik doğrulaması ile çalışmaya başlama](../previous/media-services-portal-get-started-with-aad.md)bölümünde AÇıKLANAN Azure AD kimlik doğrulaması sürecinde size kılavuzluk ediyor:

    1. Yeni Media Services hesabında **API erişimi**' ni seçin.
    2. [Hizmet sorumlusu kimlik doğrulama yöntemini](../previous/media-services-portal-get-started-with-aad.md)seçin.
    3. İstemci KIMLIĞINI ve gizli anahtarı al

        **Ayarlar** -> **anahtarlar**' ı seçtikten sonra, **Açıklama** ekleyin, **Kaydet**' e basın ve anahtar değeri doldurulur.

        Anahtarın süresi dolarsa, hesap sahibinin anahtarı yenilemek için Video Indexer desteğe başvurması gerekecektir.

        > [!NOTE]
        > Anahtar değerini ve uygulama KIMLIĞINI yazdığınızdan emin olun. Sonraki bölümde yer aldığı adımlarda buna ihtiyacınız olacak.

### <a name="connect-manually"></a>El ile bağlan

[Video Indexer](https://www.videoindexer.ai/) sayfanızın **bir Azure aboneliğinde yeni hesap oluştur** iletişim kutusunda **el ile yapılandırma bağlantısına geç** bağlantısını seçin.

İletişim kutusunda, aşağıdaki bilgileri sağlayın:

|Ayar|Açıklama|
|---|---|
|Video Indexer hesap bölgesi|Video Indexer hesap bölgesinin adı. Daha iyi performans ve düşük maliyetler için, Azure Media Services kaynağının ve Azure depolama hesabının bulunduğu bölgenin adını belirtmeniz kesinlikle önerilir. |
|Azure AD kiracısı|Azure AD kiracının adı, örneğin "contoso.onmicrosoft.com". Kiracı bilgileri Azure portal elde edilebilir. İmlecinizi sağ üst köşedeki oturum açan kullanıcının adının üzerine getirin. **Etki alanının** sağındaki adı bulun.|
|Abonelik Kimliği|Bu bağlantının oluşturulması gereken Azure aboneliği. Abonelik KIMLIĞI Azure portal elde edilebilir. Sol panelde **tüm hizmetler** ' i seçin ve "abonelikler" i arayın. **Abonelikler** ' i seçin ve aboneliklerinizin LISTESINDEN istediğiniz kimliği seçin.|
|Azure Media Services kaynak grubu adı|Media Services hesabı oluşturduğunuz kaynak grubunun adı.|
|Medya hizmeti kaynak adı|Önceki bölümde oluşturduğunuz Azure Media Services hesabının adı.|
|Uygulama Kimliği|Önceki bölümde oluşturduğunuz Azure AD uygulama KIMLIĞI (belirtilen Media Services hesabı için izinlerle).|
|Uygulama anahtarı|Önceki bölümde oluşturduğunuz Azure AD uygulama anahtarı. |

### <a name="import-your-content-from-the-trial-account"></a>İçeriğinizi *deneme* hesabından içeri aktarın

Yeni bir hesap oluştururken, içeriğinizi *deneme* hesabından yeni hesaba aktarma seçeneğiniz vardır. **Azure aboneliğindeki yeni hesap oluştur** iletişim kutusunda *içeri aktar* seçeneğini belirlerseniz, tüm medya ve içerik modeli özelleştirmeleri, *deneme* hesabından yeni hesaba kopyalanacaktır.

İçeriği içeri aktarma özelliği, yukarıda açıklanan otomatik ve el ile yaklaşımlar için geçerlidir.

> [!NOTE]
> İçerik her bir hesaptan yalnızca bir kez içeri aktarılabilir.
>
> *Deneme* hesabı, Azure Kamu Bulutu üzerinde bir kullanılabilirlik değildir.

## <a name="azure-media-services-considerations"></a>Azure Media Services konuları

Aşağıdaki Azure Media Services ilgili konular geçerlidir:

* Mevcut bir Media Services hesabına bağlanmayı planlıyorsanız, Media Services hesabının klasik API 'lerle oluşturulduğundan emin olun. 
 
    ![Media Services klasik API](./media/create-account/enable-classic-api.png)
* Mevcut bir Media Services hesabına bağlanırsanız, Video Indexer var olan medya **ayrılmış birimleri** yapılandırmasını değiştirmez.

   Planlı yüklerinize göre medya ayrılmış birimlerinin türünü ve sayısını ayarlamanız gerekebilir. Yük yüksekse ve yeterli miktarda birim veya hız yoksa, video işleme zaman aşımı hatalarıyla sonuçlanabilir.
* Yeni bir Media Services hesabına bağlanırsanız, Video Indexer varsayılan **akış uç noktasını** otomatik olarak başlatır:

    ![Media Services akış uç noktası](./media/create-account/ams-streaming-endpoint.png)

    Akış uç noktalarında önemli bir başlangıç süresi vardır. Bu nedenle, videolarınızın Video Indexer Web uygulamasında akışı ve ilerlebilmesine kadar hesabınızı Azure 'a bağladığınız zamandan birkaç dakika sürebilir.
* Mevcut bir Media Services hesabına bağlanırsanız, Video Indexer varsayılan akış uç noktası yapılandırmasını değiştirmez. Çalışan bir **akış uç noktası** yoksa, bu Media Services hesabından veya video Indexer videoları izleyebilirsiniz.
* Otomatik olarak bağlanıyorsanız Video Indexer medya **ayrılmış birimlerini** 10 S3 birimine ayarlar:

    ![Ayrılmış birimler Media Services](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-indexer-account"></a>Video Indexer hesabı oluşturmayı otomatikleştirin

Hesap oluşturma işleminin otomatik hale getirmek için iki adımdan oluşan bir işlemdir:
 
1. Bir Azure Media Services hesabı ve Azure AD uygulaması oluşturmak için Azure Resource Manager kullanın.

    [Media Services hesap oluşturma şablonuna](https://github.com/Azure-Samples/media-services-v3-arm-templates)bir örnek bakın.
1. [Media Services ve Azure AD uygulamasıyla oluşturma-hesabını](https://videoindexer.ai.azure.us/account/login?source=apim)çağırın.

## <a name="video-indexer-in-azure-government"></a>Azure Kamu 'da Video Indexer

### <a name="prerequisites-for-connecting-to-azure-government"></a>Azure Kamu 'ya bağlanma önkoşulları

-   [Azure Kamu](../../azure-government/index.yml)'da Azure aboneliği.
- Azure Kamu 'da Azure AD hesabı.
- Yukarıdaki [Azure 'a bağlanma önkoşulları](#prerequisites-for-connecting-to-azure)bölümünde açıklandığı gibi, izinlerin ve kaynakların tüm ön gereksinimleri. [Otomatik akış Için ek önkoşulları](#additional-prerequisites-for-automatic-flow) ve [El Ile akışa yönelik ek önkoşulları](#additional-prerequisites-for-manual-flow)denetlediğinizden emin olun.

### <a name="create-new-account-via-the-azure-government-portal"></a>Azure Kamu portalı aracılığıyla yeni hesap oluşturma

> [!NOTE]
> Azure Kamu Bulutu Video Indexer *deneme* deneyimini içermez.

Video Indexer portalı aracılığıyla ücretli bir hesap oluşturmak için:

1. Şuraya gidin: https://videoindexer.ai.azure.us 
1. Azure Kamu Azure AD hesabınızla oturum açın.
1.  Azure Kamu 'da sahip olduğunuz veya katkıda bulunan bir Video Indexer hesabınız yoksa, hesabınızı oluşturmaya başlayacağınız boş bir deneyim alacaksınız. 

    Akışın geri kalanı yukarıda açıklanan şekilde, yalnızca seçilecek bölgeler, video Indexer 'ın kullanılabildiği kamu bölgeleri olacaktır 

    Azure Kamu 'da mevcut bir veya daha fazla Video Indexer hesabının zaten katılımcısı veya bir yöneticisi varsa, bu hesaba yönlendirilirsiniz ve buradan açıklandığı gibi ek bir hesap oluşturmak için aşağıdaki adımları uygulayabilirsiniz.
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>Azure Kamu 'da API aracılığıyla yeni hesap oluşturma

Azure Kamu 'da ücretli bir hesap oluşturmak için, [Create-ücretli hesap]()' daki yönergeleri izleyin. Bu API uç noktası yalnızca kamu bulut bölgelerini içerir.

### <a name="limitations-of-video-indexer-on-azure-government"></a>Azure Kamu 'daki Video Indexer sınırlamaları

*   Kamu Bulutu 'nda el ile içerik denetlemesi yok. 

    Ortak bulutta, içerik bir içerik denetimi temelinde rahatsız edici olarak kabul edildiğinde müşteri, bir insan 'nin bu içeriğe bakmasını isteyebilir ve bu kararı potansiyel olarak döndürebilir.  
*   Deneme hesabı yok. 
* Bing açıklaması-gov bulutu 'nda, ünlüler ve adlandırılan adlandırılmış varlıkların bir açıklamasını sunacağız. Bu yalnızca bir UI özelliğidir. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu Öğreticiyi tamamladıktan sonra, kullanmayı planlamadığını planladığınız kaynakları silin.

### <a name="delete-a-video-indexer-account"></a>Video Indexer hesabı silme

Bir Video Indexer hesabını silmek istiyorsanız, hesabı Video Indexer Web sitesinden silebilirsiniz. Hesabı silmek için sahip olmanız gerekir.

Hesap > **ayarları**  ->  **Bu hesabı Sil**' i seçin. 

Hesap, 90 gün içinde kalıcı olarak silinir.

## <a name="next-steps"></a>Sonraki adımlar

Deneme hesabınızla ve/veya Azure 'a bağlı Video Indexer hesaplarınız ile programlama yoluyla etkileşimli olarak etkileşimde bulunabilirsiniz: [API 'Leri kullanma](video-indexer-use-apis.md).

Azure 'a bağlanırken kullandığınız Azure AD kullanıcısını kullanmanız gerekir.
