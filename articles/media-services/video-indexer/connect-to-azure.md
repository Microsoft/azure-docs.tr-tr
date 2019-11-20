---
title: Azure portal Video Indexer hesap oluşturma
titleSuffix: Azure Media Services
description: Bu makalede Azure portal Video Indexer hesabının nasıl oluşturulacağı gösterilmektedir.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: dc844392d64178cb9000db15af39f923521efc19
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838392"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Azure 'a bağlı bir Video Indexer hesabı oluşturun

Video Indexer hesabınızı oluştururken ücretsiz bir deneme hesabı (belirli sayıda ücretsiz dizin oluşturma dakikası elde edersiniz) veya ücretli bir seçenek (kota sınırlaması olmaz) arasından seçim yapabilirsiniz. Ücretsiz deneme kullanıldığında Video Indexer, web sitesi kullanıcılarına 600 dakikaya kadar ve API kullanıcılarına ise 2400 dakikaya kadar ücretsiz dizin oluşturma olanağı sunar. Ücretli seçenekle, Azure aboneliğinize ve bir Azure Media Services hesabına bağlı bir Video Indexer hesabı oluşturursunuz. Dizin oluşturma faaliyeti yapılan dakika sayısının yanı sıra Medya Hesabı ile ilgili ücretler için ödeme yaparsınız. 

Bu makalede, bir Azure aboneliğine ve bir Azure Media Services hesabına bağlı Video Indexer hesabının nasıl oluşturulacağı gösterilmektedir. Bu konuda otomatik (varsayılan) Flow kullanılarak Azure 'a bağlanmak için gereken adımlar sağlanmaktadır. Ayrıca, Azure 'a el ile nasıl bağlanagösterdiğini gösterir (Gelişmiş).

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği.

    Henüz bir Azure aboneliğiniz yoksa [Azure Ücretsiz deneme sürümü](https://azure.microsoft.com/free/)için kaydolun.

* Azure Active Directory (AD) etki alanı.

    Azure AD etki alanınız yoksa, Azure aboneliğinizle bu etki alanını oluşturun. Daha fazla bilgi için bkz. [Azure Active Directory özel etki alanı adlarını yönetme](../../active-directory/users-groups-roles/domains-manage.md)

* Azure AD etki alanındaki bir **Uygulama Yöneticisi** rolüyle bir kullanıcı. Video Indexer hesabınızı Azure 'a bağlarken bu üyeyi kullanacaksınız.

    Bu Kullanıcı, outlook.com, live.com veya hotmail.com gibi kişisel bir hesap değil, iş veya okul hesabı olan bir Azure AD kullanıcısı olmalıdır.

    ![Tüm AAD kullanıcıları](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Otomatik akış için ek önkoşullar

Azure AD etki alanındaki bir Kullanıcı ve üye. Video Indexer hesabınızı Azure 'a bağlarken bu üyeyi kullanacaksınız.

Bu Kullanıcı, bir **sahip** rolü ya da **katkıda** bulunan ve **Kullanıcı erişimi yönetici** rolleri ile Azure aboneliğinizde bir üye olmalıdır. Bir Kullanıcı 2 rol ile iki kez eklenebilir. Katkıda bulunan ve Kullanıcı erişimi yöneticisiyle bir kez.

![Erişim denetimi](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>El ile akışa yönelik ek önkoşullar

Azure portal kullanarak EventGrid kaynak sağlayıcısını kaydedin.

[Azure Portal](https://portal.azure.com/) **abonelikler**-> [abonelik]->**resourceproviders**' a gidin. 

**Microsoft. Media** ve **Microsoft. eventgrid**için arama yapın. "Kayıtlı" durumunda değilse, **Kaydet**' e tıklayın. Kaydolmak birkaç dakika sürer.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Azure'a Bağlanma

> [!NOTE]
> Azure aboneliğiniz sertifika tabanlı Multi-Factor Authentication kullanıyorsa, gerekli sertifikaların yüklü olduğu bir cihazda aşağıdaki adımları gerçekleştirmeniz çok önemlidir.

1. [Video Indexer](https://www.videoindexer.ai/) web sitesine gidip oturum açın.

2. **Yeni hesap oluştur** düğmesine tıklayın:

    ![Azure 'a bağlanma](./media/create-account/connect-to-azure.png)

3. Abonelikler listesi göründüğünde, kullanmak istediğiniz aboneliği seçin.

    ![Video Indexer Azure 'a bağlama](./media/create-account/connect-vi-to-azure-subscription.png)

4. Desteklenen konumlardan bir Azure bölgesi seçin: Batı ABD 2, Kuzey Avrupa veya Doğu Asya.
5. **Azure Media Services hesabı**altında şu seçeneklerden birini seçin:

    * Yeni bir Media Services hesabı oluşturmak için **Yeni kaynak grubu oluştur**' u seçin. Kaynak grubunuz için bir ad girin.

        Azure yeni bir Azure depolama hesabı da dahil olmak üzere aboneliğinizde yeni hesabınızı oluşturacak. Yeni Media Services hesabınızın bir akış uç noktası ve 10 S3 ayrılmış birimi olan varsayılan bir başlangıç yapılandırması vardır.
    * Mevcut bir Media Services hesabını kullanmak için **mevcut kaynağı kullan**' ı seçin. Hesaplar listesinden hesabınızı seçin.

        Media Services hesabınız Video Indexer hesabınızla aynı bölgeye sahip olmalıdır. 

        > [!NOTE]
        > Dizin oluşturma süresini ve düşük aktarım hızını en aza indirmek için, Media Services hesabınızda [ayrılan birimlerin](../previous/media-services-scale-media-processing-overview.md ) türü ve sayısını **10 S3 ayrılmış birimlerine** ayarlamanız önemle önerilir. Bkz. [ayrılmış birimleri değiştirmek için portalı kullanma](../previous/media-services-portal-scale-media-processing.md).

    * Bağlantınızı el ile yapılandırmak için **el ile yapılandırmaya geç** bağlantısına tıklayın.

        Ayrıntılı bilgi için, aşağıda bulunan [Azure 'a El Ile bağlanma](#connect-to-azure-manually-advanced-option) (Gelişmiş seçenek) bölümüne bakın.
6. İşiniz bittiğinde **Bağlan**' ı seçin. Bu işlem birkaç dakika sürebilir. 

    Azure 'a bağlandıktan sonra yeni Video Indexer hesabınız hesap listesinde görüntülenir:

    ![Yeni hesap](./media/create-account/new-account.png)

7. Yeni hesabınıza gidin

## <a name="connect-to-azure-manually-advanced-option"></a>Azure 'a el ile bağlanma (Gelişmiş seçenek)

Azure bağlantısı başarısız olduysa, el ile bağlanarak sorunu gidermeyi deneyebilirsiniz.

> [!NOTE]
> Aynı bölgede aşağıdaki üç hesabın olması kesinlikle önerilir: Media Services hesabıyla bağlanmakta olduğunuz Video Indexer hesap ve aynı Media Services hesabına bağlı Azure depolama hesabı.

### <a name="create-and-configure-a-media-services-account"></a>Media Services hesabı oluşturma ve yapılandırma

1. [Hesap oluşturma](../previous/media-services-portal-create-account.md)bölümünde açıklandığı gibi bir Azure Media Services hesabı oluşturmak için [Azure](https://portal.azure.com/) portalını kullanın.

    Media Services hesabınız için bir depolama hesabı oluştururken, çoğaltma alanları için hesap türü ve coğrafi olarak **yedekli (GRS)** için **StorageV2** öğesini seçin.

    ![Yeni AMS hesabı](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Media Services kaynak ve hesap adlarını yazdığınızdan emin olun. Sonraki bölümde bulunan adımlarda bu adıma ihtiyacınız olacak.

2. Oluşturulan Media Services hesapta, [ayrılmış birimlerin](../previous/media-services-scale-media-processing-overview.md ) türünü ve sayısını **10 S3 ayrılmış birim** olarak ayarlayın. Bkz. [ayrılmış birimleri değiştirmek için portalı kullanma](../previous/media-services-portal-scale-media-processing.md).
3. Video Indexer Web uygulamasında videolarınızı oynatabilmeniz için önce yeni Media Services hesabının varsayılan **akış uç noktasını** başlatmanız gerekir.

    Yeni Media Services hesabında, **akış uç noktaları**' na tıklayın. Akış uç noktasını seçin ve Başlat ' a basın.

    ![Yeni AMS hesabı](./media/create-account/create-ams-account2.png)

4. Media Services API 'SI ile kimlik doğrulaması yapmak Video Indexer için, bir AD uygulamasının oluşturulması gerekir. Aşağıdaki adımlar, [Azure Portal kullanarak Azure AD kimlik doğrulaması ile çalışmaya başlama](../previous/media-services-portal-get-started-with-aad.md)bölümünde AÇıKLANAN Azure AD kimlik doğrulaması sürecinde size kılavuzluk ediyor:

    1. Yeni Media Services hesabında **API erişimi**' ni seçin.
    2. [Hizmet sorumlusu kimlik doğrulama yöntemini](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)seçin.
    3. İstemci kimliğini ve [istemci gizli anahtarını al bölümünde açıklandığı](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret) gıbı istemci kimliğini ve gizli anahtarı alın.

        **Ayarlar**->**anahtarlar**' ı seçtikten sonra **Açıklama**ekleyin, **Kaydet**' e basın, anahtar değeri doldurulur.

        Anahtarın süresi dolarsa, hesap sahibinin anahtarı yenilemek için Video Indexer destekle iletişim kurabilmesi gerekir.

        > [!NOTE]
        > Anahtar değerini ve uygulama KIMLIĞINI yazdığınızdan emin olun. Sonraki bölümde bulunan adımlarda bu adıma ihtiyacınız olacak.

### <a name="connect-manually"></a>El ile bağlan

[Video Indexer](https://www.videoindexer.ai/) sayfanızın **bir Azure aboneliğine video Indexer Bağlan** iletişim kutusunda **el ile yapılandırma bağlantısına geç** bağlantısını seçin.

İletişim kutusunda, aşağıdaki bilgileri sağlayın:

|Ayar|Açıklama|
|---|---|
|Video Indexer hesap bölgesi|Video Indexer hesap bölgesinin adı. Daha iyi performans ve düşük maliyetler için, Azure Media Services kaynağının ve Azure depolama hesabının bulunduğu bölgenin adını belirtmeniz kesinlikle önerilir. |
|Azure Active Directory (AAD) kiracısı|Azure AD kiracının adı, örneğin "contoso.onmicrosoft.com". Kiracı bilgileri Azure portal elde edilebilir. İmlecinizi sağ üst köşedeki oturum açan kullanıcının adının üzerine getirin. **Etki alanının**sağındaki adı bulun.|
|Abonelik Kimliği|Bu bağlantının oluşturulması gereken Azure aboneliği. Abonelik KIMLIĞI Azure portal elde edilebilir. Sol panelde **tüm hizmetler** ' e tıklayın ve "abonelikler" i arayın. **Abonelikler** ' i seçin ve aboneliklerinizin LISTESINDEN istediğiniz kimliği seçin.|
|Azure Media Services kaynak grubu adı|Media Services hesabı oluşturduğunuz kaynak grubunun adı.|
|Medya hizmeti kaynak adı|Önceki bölümde oluşturduğunuz Azure Media Services hesabının adı.|
|Uygulama Kimliği|Önceki bölümde oluşturduğunuz Azure AD uygulama KIMLIĞI (belirtilen Media Services hesabı için izinlerle).|
|Uygulama anahtarı|Önceki bölümde oluşturduğunuz Azure AD uygulama anahtarı. |

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Aşağıdaki Azure Media Services ilgili konular geçerlidir:

* Otomatik olarak bağlanıyorsanız, Azure aboneliğinizde yeni bir kaynak grubu, Media Services hesabı ve bir depolama hesabı görürsünüz.
* Otomatik olarak bağlanıyorsanız Video Indexer medya **ayrılmış birimlerini** 10 S3 birimine ayarlar:

    ![Ayrılmış birimler Media Services](./media/create-account/ams-reserved-units.png)

* Mevcut bir Media Services hesabına bağlanırsanız, Video Indexer var olan medya **ayrılmış birimleri** yapılandırmasını değiştirmez.

   Planlı yüklerinize göre medya ayrılmış birimlerinin türünü ve sayısını ayarlamanız gerekebilir. Yük yüksekse ve yeterli miktarda birim veya hız yoksa, video işleme zaman aşımı hatalarıyla sonuçlanabilir.

* Yeni bir Media Services hesabına bağlanırsanız, Video Indexer varsayılan **akış uç noktasını** otomatik olarak başlatır:

    ![Media Services akış uç noktası](./media/create-account/ams-streaming-endpoint.png)

    Akış uç noktalarında önemli bir başlangıç süresi vardır. Bu nedenle, hesabınızı Azure 'a bağladığınıza ve Video Indexer Web uygulamasında videolarınızın akışını yapana kadar birkaç dakika sürebilirsiniz.

* Mevcut bir Media Services hesabına bağlanırsanız, Video Indexer varsayılan akış uç noktası yapılandırmasını değiştirmez. Çalışan bir **akış uç noktası**yoksa, bu Media Services hesabından veya video Indexer videoları izleyemeyeceksiniz.

## <a name="next-steps"></a>Sonraki adımlar

Deneme hesabınızla ve/veya Azure 'a bağlı Video Indexer hesaplarınız ile programlama yoluyla etkileşimli olarak etkileşimde bulunabilirsiniz: [API 'Leri kullanma](video-indexer-use-apis.md).

Azure 'a bağlanırken kullandığınız Azure AD kullanıcısını kullanmanız gerekir.


