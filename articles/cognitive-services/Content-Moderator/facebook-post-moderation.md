---
title: 'Öğretici: Orta Facebook içeriği-Content Moderator'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, BT öğrenimi tabanlı Content Moderator kullanarak, Facebook gönderilerine ve yorumlara yardımcı olma hakkında bilgi edineceksiniz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 5aa4cc24484a4ba1da608da9676ade492db35b6c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72936000"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Öğretici: Azure Content Moderator ile orta Facebook gönderileri ve komutları

Bu öğreticide, Facebook sayfasında gönderimlerin ve yorumların orta düzeyde sağlanmasına yardımcı olması için Azure Content Moderator kullanmayı öğreneceksiniz. Facebook, ziyaretçi tarafından gönderilen içeriği Content Moderator hizmetine gönderir. Daha sonra Content Moderator iş akışlarınız içerik puanlarını ve eşiklerine bağlı olarak, gözden geçirme aracı içinde içeriği yayımlar veya incelemeler oluşturur. Bu senaryonun çalışan bir örneği için [Build 2017 demo videosunu](https://channel9.msdn.com/Events/Build/2017/T6033) inceleyin.

Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> * Content Moderator takımı oluşturma.
> * Content Moderator'dan ve Facebook'tan HTTP olaylarını dinleyen Azure İşlevleri oluşturma.
> * Facebook uygulamasını kullanarak Facebook sayfasını Content Moderator bağlayın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Bu diyagramda bu senaryonun her bileşeni gösterilmektedir:

![Facebook 'tan "FBListener" aracılığıyla bilgi alma ve "CMListener" aracılığıyla bilgi gönderme Content Moderator diyagramı](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> 2018 ' de Facebook, Facebook uygulamalarının daha sıkı bir şekilde bir bölümünü uyguladık. Uygulamanız Facebook gözden geçirme ekibi tarafından incelenip onaylanmamışsa, Bu öğreticinin adımlarını tamamlayameyeceksiniz.

## <a name="prerequisites"></a>Önkoşullar

- Content Moderator abonelik anahtarı. Content Moderator hizmetine abone olmak ve anahtarınızı almak için bilişsel [Hizmetler oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ' daki yönergeleri izleyin.
- [Facebook hesabı](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Bir gözden geçirme ekibi oluşturun

[Content moderator gözden geçirme aracına](https://contentmoderator.cognitive.microsoft.com/) kaydolma ve bir gözden geçirme ekibi oluşturma hakkında yönergeler için Web hızlı başlangıç [Content moderator deneme](quick-start.md) bakın. **Kimlik bilgileri** SAYFASıNDAKI **Takım Kimliği** değerini bir yere göz atın.

## <a name="configure-image-moderation-workflow"></a>Görüntü denetleme iş akışını yapılandırma

Özel bir görüntü iş akışı oluşturmak için [tanımlama, test et ve iş akışlarını kullanma](review-tool-user-guide/workflows.md) kılavuzuna bakın. Content Moderator, Facebook 'ta görüntüleri otomatik olarak denetlemek ve bazılarını Inceleme aracına göndermek için bu iş akışını kullanacaktır. İş akışı **adını**bir yere göz atın.

## <a name="configure-text-moderation-workflow"></a>Metin denetleme iş akışını yapılandırma

Daha sonra [tanımlama, test et ve iş akışlarını kullanma](review-tool-user-guide/workflows.md) kılavuzuna bakın. Bu kez, özel bir metin iş akışı oluşturun. Content Moderator, metin içeriğini otomatik olarak denetlemek için bu iş akışını kullanacaktır. İş akışı **adını**bir yere göz atın.

![Metin İş Akışını Yapılandırma](images/text-workflow-configure.PNG)

İş akışınızı **Yürüt** düğmesini kullanarak iş akışınızı test edin.

![Metin İş Akışını Test Etme](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure İşlevleri oluşturma

[Azure Portal](https://portal.azure.com/) oturum açın ve şu adımları izleyin:

1. [Azure İşlevleri](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) sayfasında gösterildiği gibi bir Azure İşlev Uygulaması oluşturun.
1. Yeni oluşturulan İşlev Uygulaması gidin.
1. Uygulama içinde **platform özellikleri** sekmesine gidin ve **yapılandırma**' yı seçin. Aşağıdaki anahtar/değer çiftlerini eklemek için sonraki sayfanın **uygulama ayarları** bölümünde **Yeni uygulama ayarı** ' nı seçin:
    
    | Uygulama ayarı adı | değer   | 
    | -------------------- |-------------|
    | cm:TeamId   | Content Moderator Takım Kimliğiniz  | 
    | cm:SubscriptionKey | Content Moderator abonelik anahtarınız. Bkz. [Kimlik Bilgileri](review-tool-user-guide/credentials.md) |
    | cm:Region | Content Moderator bölge adınız (boşluk içermez). Bunu, Azure kaynağınızın **genel bakış** sekmesinin **konum** alanında bulabilirsiniz.|
    | cm:ImageWorkflow | Görüntüler üzerinde çalıştırılacak iş akışının adı |
    | cm:TextWorkflow | Metinler üzerinde çalıştırılacak iş akışının adı |
    | cm:CallbackEndpoint | Bu kılavuzda daha sonra oluşturacağınız İşlev Uygulaması CMListener URL 'si |
    | fb:VerificationToken | Facebook akış olaylarına abone olmak için kullanılan, sizin oluşturduğunuz gizli bir belirteç |
    | fb:PageAccessToken | Facebook graph api'si erişim belirtecinin süresi dolmaz ve işlevin sizin adınıza gönderileri Gizlemesini/Silmesini sağlar. Daha sonraki bir adımda bu belirteci alacaksınız. |

    Sayfanın üst kısmındaki **Kaydet** düğmesine tıklayın.

1. **Platform özellikleri** sekmesine geri dönün. **yeni işlev** bölmesini açmak için sol bölmedeki **+** düğmesini kullanın. Oluşturmak üzere olduğunuz işlev Facebook 'tan olayları alacak.

    ![Işlev Ekle düğmesi vurgulanmış şekilde Azure Işlevleri bölmesi.](images/new-function.png)

    1. **Http tetikleyicisini**belirten kutucuğa tıklayın.
    1. **FBListener** adını girin. **Yetkilendirme Düzeyi** alanı **İşlev** olarak ayarlanmalıdır.
    1. **Oluştur**’a tıklayın.
    1. **Run. CSX** Içeriğini **fblistener/Run. CSX** içeriğiyle değiştirin

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. **Cmlistener**adlı yeni bir **http tetikleyici** işlevi oluşturun. Bu işlev Content Moderator'dan olayları alır. **Run. CSX** Içeriğini **cmlistener/Run. CSX** içindekilerle değiştirin

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Facebook sayfasını ve Uygulamasını yapılandırma

1. Facebook Uygulaması oluşturun.

    ![Facebook Geliştirici sayfası](images/facebook-developer-app.png)

    1. [Facebook geliştirici sitesine](https://developers.facebook.com/) gidin
    1. **My Apps** (Uygulamalarım) öğesine tıklayın.
    1. Yeni Uygulama ekleyin.
    1. Bunu bir ad olarak adlandırın
    1. **Web kancaları-> ayarla** ' yı seçin
    1. Açılır menüden **sayfa** ' yı seçin ve **Bu nesneye abone ol ' u** seçin
    1. Geri Arama URL'si olarak **FBListener Url'sini** sağlayın ve **İşlevi Uygulaması Ayarları** altında yapılandırdığınız **Belirteci Doğrulayın**
    1. Abone olduktan sonra, akışı aşağı kaydırıp **subscribe** (abone ol) öğesini seçin.
    1. FBListener Azure işlevinizi test iletisi göndermek için **akış** satırının **Test** düğmesine tıklayın ve sonra **sunucuma gönder** düğmesine basın. İsteğin FBListener üzerinde alındığını görmeniz gerekir.

1. Facebook Sayfası oluşturun.

    > [!IMPORTANT]
    > 2018 ' de Facebook, Facebook uygulamalarının daha sıkı bir şekilde bir bölümünü uyguladık. Uygulamanız Facebook gözden geçirme ekibi tarafından incelenip onaylanmamışsa Bölüm 2, 3 ve 4 ' ü yürütemeyeceksiniz.

    1. [Facebook](https://www.facebook.com/bookmarks/pages)'a gidin ve **yeni bir Facebook Sayfası** oluşturun.
    1. Şu adımları izleyerek Facebook Uygulamasının bu sayfaya erişmesine izin verin:
        1. [Graph API Explorer](https://developers.facebook.com/tools/explorer/)'a gidin.
        1. **Uygulama**'yı seçin.
        1. **Sayfa Erişim Belirteci**'ni seçin. Bir **Get** isteği gönderin.
        1. Yanıtta **Sayfa Kimliği**'ne tıklayın.
        1. Şimdi **/subscribed_apps** bölümünü URL'ye ekleyin ve bir **Get** (boş yanıt) isteği gönderin.
        1. **Post** isteği gönderin. **success: true** gibi bir yanıt alırsınız.

3. Süresi dolmayan bir Graph API erişim belirteci oluşturun.

    1. [Graph API Explorer](https://developers.facebook.com/tools/explorer/)'a gidin.
    2. **Uygulama** seçeneğini belirtin.
    3. **Kullanıcı Erişim Belirteci Al** seçeneğini belirtin.
    4. **İzin Seç** bölümünde **manage_pages** ve **publish_pages** seçeneklerini belirtin.
    5. **Erişim belirtecini** (Kısa Ömürlü Belirteç) sonraki adımda kullanacağız.

4. Önümüzdeki birkaç adımda Postman kullanıyoruz.

    1. **Postman**'ı açın (veya [buradan](https://www.getpostman.com/) alın).
    2. Şu iki dosyayı içeri aktarın:
        1. [Postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman Environment](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Şu ortam değişkenlerini güncelleştirin:
    
        | Anahtar | Değer   | 
        | -------------------- |-------------|
        | appId   | Buraya Facebook Uygulama Tanımlayıcınızı ekleyin  | 
        | appSecret | Buraya Facebook Uygulamanızın gizli dizisini ekleyin | 
        | short_lived_token | Önceki adımda oluşturduğunuz kısa ömürlü kullanıcı erişim belirtecini ekleyin |
    4. Şimdi koleksiyonda listelenen 3 API'yi çalıştırın: 
        1. **Uzun Ömürlü Erişim Belirteci Oluştur**'u seçin ve **Gönder**'e tıklayın.
        2. **Kullanıcı Kimliğini Al**'ı seçin ve **Gönder**'e tıklayın.
        3. **Kalıcı Sayfa Erişim Belirtecini Al**'ı seçin ve **Gönder**'e tıklayın.
    5. Yanıttan **access_token** değerini kopyalayın ve bunu **fb:PageAccessToken** Uygulama ayarına atayın.

Çözüm, Facebook sayfanıza gönderilen tüm resimleri ve metinleri Content Moderator'a gönderir. Daha önce yapılandırdığınız iş akışları çağrılır. İş akışlarında tanımlı kriterlerinizi geçirmez, gözden geçirme aracının içindeki incelemelere geçirilir. İçeriğin geri kalanı otomatik olarak yayımlanır.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, ürün türlerine göre etiketlenmesi ve bir gözden geçirme ekibinin içerik denetlemesi hakkında bilinçli kararlar almasına izin vermek amacıyla ürün görüntülerini analiz etmek için bir program ayarlarsınız. Ardından, görüntü denetleme ayrıntıları hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Görüntü denetimi](./image-moderation-api.md)
