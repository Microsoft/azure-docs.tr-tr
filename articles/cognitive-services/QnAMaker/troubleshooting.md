---
title: Sorun giderme-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Soru-Cevap Oluşturma hizmetiyle ilgili en sık sorulan soruların seçkin listesi, hizmeti daha hızlı ve daha iyi sonuçlarla benimsemenizi sağlayacak.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e98fd089ce8ec1285232840a40bb42ac5b81446
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795436"
---
# <a name="troubleshooting-for-qna-maker"></a>Soru-Cevap Oluşturma için sorun giderme

Soru-Cevap Oluşturma hizmetiyle ilgili en sık sorulan soruların seçkin listesi, hizmeti daha hızlı ve daha iyi sonuçlarla benimsemenizi sağlayacak.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>QnAMaker hizmet uç noktasını alma

Qnamaker hizmeti uç noktası, QnAMaker desteği veya UserVoice ile iletişime geçerek hata ayıklama amacıyla faydalıdır. Uç nokta şu biçimdeki bir URL 'dir: https://your-resource-name.azurewebsites.net.
    
1. [Azure Portal](https://portal.azure.com) QnAMaker hizmetinize (kaynak grubu) gidin

    ![Azure portal 'de QnAMaker Azure Kaynak grubu](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Soru-Cevap Oluşturma kaynakla ilişkili App Service seçin. Genellikle, adlar aynıdır.

     ![QnAMaker App Service seçin](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Uç nokta URL 'SI genel bakış bölümünde kullanılabilir

    ![QnAMaker uç noktası](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Soru-Cevap Oluşturma portalında yardım bot 'ı kullanma

Soru-Cevap Oluşturma size yardımcı olmak için Soru-Cevap Oluşturma portalında bir **Yardım** bot sağlar. Yardım bot, her Web sayfasında kullanılabilir. Bot, yanıtları sağlamak için soru-cevap oluşturma kullanır ve kendi yanıt bot 'ınızla hızlıca çalışmaya devam edebilmeniz için bot [ C# Framework kod projesini](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) bot 'a sağlar. 

![! [Soru-Cevap Oluşturma size yardımcı olmak için Soru-Cevap Oluşturma portalında bir * * yardım * * bot sağlar.] (./Media/qnamaker-FAQ/Use-QNA-Maker-Help-bot-to-learn-QNA-Maker-Service.exe)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Bilgi Bankası 'nı yönetme

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Soru-Cevap Oluşturma bir parçasını yanlışlıkla sildim, ne yapmam gerekir? 

Arama veya Web uygulaması gibi Soru-Cevap Oluşturma kaynakla birlikte oluşturulan Azure hizmetlerinden hiçbirini silmeyin. Bunlar Soru-Cevap Oluşturma çalışması için gereklidir, Soru-Cevap Oluşturma doğru çalışmayı durdurur.

Soru ve yanıt çiftleri, dosyalar, URL 'Ler, özel sorular ve yanıtlar, bilgi tabanları veya Azure kaynakları dahil olmak üzere tüm silmeler kalıcıdır. Bilgi Bankalarınızın herhangi bir bölümünü silmeden önce, bilgi bankaınızı **Ayarlar** sayfasından dışarı aktardığınızdan emin olun. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>URL 'lerim neden soru-cevap çiftlerini ayıklamıyor?

Soru-Cevap Oluşturma, geçerli SSS URL 'Lerinden bazı soru-cevap (QnA) içeriğini otomatik olarak ayıklayamıyor olabilir. Bu gibi durumlarda, QnA içeriğini bir. txt dosyasına yapıştırabilir ve aracın bunu alıp alabildiğinden bkz. Alternatif olarak, [soru-cevap oluşturma portalı](https://qnamaker.ai)aracılığıyla bilgi bankasında içerik ekleyebilirsiniz.

### <a name="how-large-a-knowledge-base-can-i-create"></a>Ne kadar büyük bir bilgi bankası oluşturabilirim?

Bilgi bankasındaki boyut, Soru-Cevap Oluşturma Hizmeti oluştururken seçtiğiniz Azure Search SKU 'suna bağlıdır. Daha ayrıntılı bilgi için [burayı](./Tutorials/choosing-capacity-qnamaker-deployment.md) okuyun.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Yeni bir Bilgi Bankası oluşturmaya çalıştığımda neden açılan kutuda hiçbir şey göremiyorum?

Henüz Azure 'da hiç Soru-Cevap Oluşturma Hizmeti oluşturmadınız. Bunu nasıl yapacağınızı öğrenmek için [buradan](./How-To/set-up-qnamaker-service-azure.md) okuyun.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Bilgi Bankası 'nı başkalarıyla paylaşmak Nasıl yaparım? mı?

Paylaşım, Soru-Cevap Oluşturma bir hizmet düzeyinde çalışır, diğer bir deyişle, hizmette tüm bilgi tabanları paylaşılır. Bilgi Bankası 'nda nasıl [işbirliği yapılacağını okuyun](./How-To/collaborate-knowledge-base.md) .

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Bilgi Bankası 'nı değiştirmek için aynı AAD kiracısında olmayan bir katkıda bulunan bir Bilgi Bankası paylaşabilir misiniz? 

Paylaşım, Azure rol tabanlı erişim denetimi 'ni (RBAC) temel alır. Azure 'da _herhangi_ bir kaynağı başka bir kullanıcıyla paylaşabilmek için soru-cevap oluşturma de paylaşabilirsiniz.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>5 QnAMaker bilgi tabanı içeren bir App Service planınız varsa. Her birinin yalnızca 1 QnAMaker Bilgi Bankası 'na erişebilmeleri için 5 farklı kullanıcıya okuma/yazma hakları atayabilir misiniz?

Tek bilgi temellerini değil, tüm QnAMaker hizmetini paylaşabilirsiniz.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hiçbir iyi eşleşme bulunamadığında varsayılan iletiyi nasıl değiştirebilirim?

Varsayılan ileti, App Service 'teki ayarların bir parçasıdır.
- Azure portal App Service kaynağına gidin

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- **Ayarlar** seçeneğine tıklayın

![qnamaker appservice ayarları](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- **Defaultanswer** ayarının değerini değiştirme
- App Service 'i yeniden başlatın

![qnamaker appservice yeniden başlatma](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Neden SharePoint bağlantım ayıklanmıyor?

Daha fazla bilgi için bkz. [veri kaynağı konumları](./Concepts/data-sources-supported.md#data-source-locations) .

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Bilgi tabanmda yaptığım güncelleştirmeler Yayımla ' ya yansıtılmıyor. Neden?

Bir tablo güncelleştirmesinde, testte veya ayarda, yayımlanmadan önce kaydedilmesi gereken her düzenleme işlemi. Her düzenleme işleminden sonra **Kaydet ve eğitme** düğmesine tıkladığınızdan emin olun.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Bilgi Bankası zengin veri veya multimedyayı destekliyor mu?

Bilgi bankası Markdown’ı destekler. Bununla birlikte, URL 'lerden otomatik ayıklamanın HTML 'den markın dönüştürme özelliğine sınırlı olması gerekir. Tam kapsamlı Marku kullanmak istiyorsanız, içeriğinizi doğrudan tabloda değiştirebilir veya zengin içerikle bir Bilgi Bankası yükleyebilirsiniz.

Görüntü ve video gibi çoklu ortam şu anda desteklenmiyor.

### <a name="does-qna-maker-support-non-english-languages"></a>Soru-Cevap Oluşturma İngilizce dışındaki dilleri destekler mi?

[Desteklenen diller](./Overview/languages-supported.md)hakkında daha fazla ayrıntı için bkz.

Birkaç dilde içeriğiniz varsa mutlaka her biri için ayrı bir hizmet oluşturun.

## <a name="manage-service"></a>Hizmeti yönetme

### <a name="when-should-i-restart-my-app-service"></a>App Service 'i ne zaman yeniden başlatmalıyım? 

**Kullanıcı ayarları** [sayfasındaki](https://www.qnamaker.ai/UserSettings) **Endpoint Keys** tablosundaki Bilgi Bankası 'nın sürüm değerinin yanında uyarı simgesi olduğunda App Service 'i yenileyin.

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Var olan arama hizmetimi sildim. Bunu nasıl giderebilirim?

Azure Bilişsel Arama dizinini silerseniz, işlem nihai ve Dizin kurtarılamıyor. 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Arama hizmetimde `testkb` dizinimi sildim. Bunu nasıl giderebilirim? 

Eski verileriniz kurtarılamıyor. Yeni bir Soru-Cevap Oluşturma kaynağı oluşturun ve bilgi bankanızı tekrar oluşturun.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Uç nokta anahtarlarımı ne zaman yenilemem gerekir?

Tehlikede olduğunu düşünüyorsanız, uç nokta anahtarlarınızı yenileyin.

### <a name="can-i-use-the-same-azure-cognitive-search-resource-for-knowledge-bases-using-multiple-languages"></a>Birden çok dil kullanarak Bilgi Bankası için aynı Azure Bilişsel Arama kaynağını kullanabilir miyim?

Birden çok dil ve birden çok bilgi tabanı kullanmak için, kullanıcının her dil için bir Soru-Cevap Oluşturma kaynağı oluşturması gerekir. Bu, dil başına ayrı bir Azure Arama hizmeti oluşturur. Tek bir Azure Search hizmetinde farklı dil bilgisi esaslarını karışmaya, sonuçların uygunluğu düşürüldü.

### <a name="how-can-i-change-the-name-of-the-azure-cognitive-search-resource-used-by-qna-maker"></a>Soru-Cevap Oluşturma tarafından kullanılan Azure Bilişsel Arama kaynağının adını nasıl değiştirebilirim?

Azure Bilişsel Arama kaynağının adı, sonunda bazı rastgele harfler eklenmiş Soru-Cevap Oluşturma kaynak adıdır. Bu, Soru-Cevap Oluşturma için birden çok arama kaynağı arasında ayrım yapmayı zorlaştırır. Ayrı bir arama hizmeti oluşturun (istediğiniz şekilde adlandırın) ve QnA hizmetinize bağlayın. Adımlar, [bir Azure aramasını yükseltmek](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)için yapmanız gereken adımlara benzerdir.

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Soru-Cevap Oluşturma `Runtime core is not initialized,` döndürüldüğünde bunu nasıl giderebilirim?

App Service 'niz için disk alanı dolu olabilir. Disk alanınızı çözme adımları:

1. [Azure Portal](https://portal.azure.com), soru-cevap oluşturma App Service ' i seçin ve ardından hizmeti durdurun.
1. Uygulama hizmetinde hala **geliştirme araçları**' nı ve **Gelişmiş Araçlar**' ı seçin, sonra da **Git**' i seçin. Bu, yeni bir tarayıcı penceresi açar.
1. Komut satırı aracını açmak için **hata ayıklama konsolu**' nu ve ardından **cmd** ' yi seçin. 
1. _Site/Wwwroot/veri/QnAMaker/_ dizine gidin.
1. Adı `rd`başlayan tüm klasörleri kaldırın. 

    Aşağıdakileri **silmeyin** :

    * KbIdToRankerMappings. txt dosyası
    * EndpointSettings. JSON dosyası
    * EndpointKeys klasörü 

1. App Service 'i başlatın.
1. Şimdi çalıştığını doğrulamak için bilgi bankaıza erişin. 


## <a name="integrate-with-other-services-including-bots"></a>Botlar dahil diğer hizmetlerle tümleştirin

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Soru-Cevap Oluşturucu’yu kullanmak için Bot Framework’ü kullanmam mı gerekir?

Hayır, Soru-Cevap Oluşturma ile [bot çerçevesini](https://github.com/Microsoft/botbuilder-dotnet) kullanmanız gerekmez. Ancak, Soru-Cevap Oluşturma [Azure bot hizmetinde](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)birkaç şablondan biri olarak sunulur. Bot hizmeti, Microsoft bot Framework aracılığıyla hızlı akıllı bot geliştirmesi sunar ve sunucu-daha az bir ortamda çalışır.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Soru-Cevap Oluşturma yeni bir bot nasıl oluşturabilirim?

Azure bot hizmeti ile bot 'nizi oluşturmak için [Bu](./Tutorials/create-qna-bot.md) belgelerdeki yönergeleri izleyin.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Nasıl yaparım? mevcut bir Azure bot hizmeti ile farklı bir Bilgi Bankası mı kullanıyorsunuz?

Bilgi tabanınız hakkında aşağıdaki bilgilere sahip olmanız gerekir:

* Bilgi Bankası KIMLIĞI.
* Bilgi Bankası 'nın yayımlanan bitiş noktası özel alt etki alanı adı, `host`olarak bilinen, yayımlama sonrasında **Ayarlar** sayfasında bulunur.
* Bilgi Bankası 'nın yayımlanmış uç nokta anahtarı-yayımladıktan sonra **Ayarlar** sayfasında bulunur. 

Bu bilgilerle, Azure portal bot 'ın App Service 'e gidin. **Ayarlar-> yapılandırma-> uygulama ayarları**altında bu değerleri değiştirin.  

Bilgi Bankası 'nın uç nokta anahtarı, ABS hizmetindeki `QnAAuthkey` etiketlidir. 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>İki veya daha fazla istemci uygulaması Bilgi Bankası 'nı paylaşabilir mi? 

Evet, Bilgi Bankası 'nda herhangi bir sayıda istemciden sorgulanabilecek. Bilgi bankasından gelen yanıt yavaş veya zaman aşımına uğrar görünüyorsa, Bilgi Bankası ile ilişkili App Service 'in hizmet katmanını yükseltmeyi göz önünde bulundurun.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Soru-Cevap Oluşturucu hizmetini web siteme nasıl ekleyebilirim?

Soru-Cevap Oluşturma hizmetini Web sitenize web sohbeti denetimi olarak eklemek için aşağıdaki adımları izleyin:

1. [Buradaki](./Tutorials/create-qna-bot.md)YÖNERGELERI izleyerek SSS bot uygulamanızı oluşturun.
2. [Buradaki](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) adımları izleyerek Web sohbetini etkinleştirin

## <a name="data-storage"></a>Veri depolama

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Hangi veriler depolanır ve nerede depolanır? 

Soru-Cevap Oluşturma hizmetinizi oluşturduğunuzda bir Azure bölgesi seçtiniz. Bilgi tabanlarınız ve günlük dosyalarınız bu bölgede depolanır. 
