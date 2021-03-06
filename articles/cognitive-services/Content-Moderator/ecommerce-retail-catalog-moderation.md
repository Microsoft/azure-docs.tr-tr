---
title: 'Öğretici: Orta e-ticaret ürün görüntüleri-Content Moderator'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, bir uygulamayı belirtilen etiketlerle (Azure Görüntü İşleme ve Özel Görüntü İşleme kullanarak) ürün görüntülerini çözümlemek ve sınıflandırmak için nasıl ayarlanacağı gösterilmektedir. Sakıncalı resimleri daha fazla gözden geçirilecek şekilde etiketleyin (Azure Content Moderator kullanarak).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: e67a9b35291615b87edf918b4146ab8d77a0ae7f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065152"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Öğretici: Azure Content Moderator ile orta e-ticaret ürün görüntüleri

Bu öğreticide, bir e-ticaret senaryosuna yönelik ürün görüntülerini sınıflandırmak ve bunlara göre Content Moderator dahil olmak üzere Azure bilişsel hizmetler 'i nasıl kullanacağınızı öğreneceksiniz. Resimlere Etiketler (Etiketler) uygulamak için Görüntü İşleme ve Özel Görüntü İşleme kullanacaksınız ve ardından, Content Moderator makine öğrenimi tabanlı teknolojileri, bir akıllı denetim sistemi sağlamak için insan incelemesi ekipleriyle birleştiren bir ekip incelemesi oluşturacaksınız.

Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> * Content Moderator kaydolun ve bir gözden geçirme ekibi oluşturun.
> * Olası yetişkinlere yönelik ve müstehcen içerik taraması için Content Moderator'ın resim API'sini kullanma.
> * Görüntü İşleme hizmetini kullanarak ünlülerde içeriği (veya diğer bilgisayar görme algılanamaz etiketleri) tarayın.
> * Bayrakların, oyunların ve kalemlerin (veya diğer özel etiketlerin) varlığını taramak için Özel Görüntü İşleme hizmetini kullanın.
> * İnsan incelemesi ve nihai karar oluşturma için birleştirilmiş tarama sonuçlarını sunun.

Örnek kodun tamamı GitHub 'daki [eCommerce Katalog denetleme](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) deposunda bulunur.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- Content Moderator abonelik anahtarı. Content Moderator hizmetine abone olmak ve anahtarınızı almak için bilişsel [Hizmetler oluşturma](../cognitive-services-apis-create-account.md) ' daki yönergeleri izleyin.
- Görüntü İşleme abonelik anahtarı (yukarıdaki yönergeleri de aynı şekilde).
- [Visual Studio 2015 veya 2017](https://www.visualstudio.com/downloads/)'nin herhangi bir sürümü.
- Özel Görüntü İşleme sınıflandırıcının kullanacağı her etiket için bir görüntü kümesi (Bu örnekte, Toys, kalemler ve ABD bayraklarında).

## <a name="create-a-review-team"></a>Bir gözden geçirme ekibi oluşturun

[Content moderator gözden geçirme aracına](https://contentmoderator.cognitive.microsoft.com/) kaydolma ve bir gözden geçirme ekibi oluşturma hakkında yönergeler için Web hızlı başlangıç [Content moderator deneme](quick-start.md) bakın. **Kimlik bilgileri** SAYFASıNDAKI **Takım Kimliği** değerini bir yere göz atın.

## <a name="create-custom-moderation-tags"></a>Özel denetleme etiketleri oluşturma

Ardından, gözden geçirme aracında özel etiketler oluşturun (Bu işlemle ilgili yardıma ihtiyacınız varsa [Etiketler](./review-tool-user-guide/configure.md#tags) makalesine bakın). Bu durumda, şu etiketleri ekleyeceğiz: **ünlüylik**, **ABD**, **bayrak**, **oyuncak** ve **kalem**. Etiketlerin tümünün Görüntü İşleme ( **ünlüm** gibi) üzerinde algılanamaz Kategoriler olması gerekmez; Özel Görüntü İşleme sınıflandırıcısını daha sonra tespit etmek üzere eğitemiş olduğunuz sürece kendi özel etiketlerinizi ekleyebilirsiniz.

![Özel etiketleri yapılandırma](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Visual Studio projesi oluşturma

1. Visual Studio 'da yeni proje iletişim kutusunu açın. **Yüklü**, sonra **Visual C#** ve **konsol uygulaması (.NET Framework)** öğesini seçin.
1. Uygulamayı **EcommerceModeration** olarak adlandırın ve ardından **Tamam**' ı seçin.
1. Bu projeyi mevcut bir çözüme ekliyorsanız, tek bir başlangıç projesi olarak bu projeyi seçin.

Bu öğretici, projenin merkezi olan kodunu vurgular, ancak her kod satırını kapsamaz. _Program. cs_ ' nin tüm içeriğini örnek projeden (örnek [eCommerce Katalog denetlemesi](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) yeni projenizin _program. cs_ dosyasına kopyalayın. Daha sonra, projenin nasıl çalıştığı ve nasıl kullanılacağı hakkında bilgi edinmek için aşağıdaki bölümlerde adım adım ilerleyin.

## <a name="define-api-keys-and-endpoints"></a>API anahtarlarını ve uç noktalarını tanımlama

Bu öğretici üç bilişsel hizmet kullanır; Bu nedenle, üç ilgili anahtar ve API uç noktası gerektirir. **Program** sınıfında aşağıdaki alanlara bakın:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

`___Key`Alanları abonelik anahtarlarınızın değerleriyle güncelleştirmeniz gerekir ve `___Uri` alanları doğru uç nokta URL 'leriyle değiştirmeniz gerekir (özel görüntü işleme anahtarını ve uç noktayı daha sonra alacaksınız). Bu değerleri, her bir Azure kaynağının **hızlı başlangıç** sekmelerinde bulabilirsiniz. `YOURTEAMID` `ReviewUri` Daha önce oluşturduğunuz gözden GEÇIRME ekibinin kimliğiyle alanın parçasını girin. Daha sonra alanının son bölümünü doldurmanız gerekir `CustomVisionUri` .

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Birincil yöntem çağrıları

**Ana** yöntemde, bir görüntü URL 'si listesi üzerinden döngü gösteren aşağıdaki koda bakın. Her görüntüyü üç farklı hizmet ile analiz eder, uygulanan etiketleri, **belgeincelemeetiketi** dizisine kaydeder ve ardından görüntüleri Content moderator gözden geçirme aracına göndererek insan moderatörleri için bir gözden geçirme oluşturur. Aşağıdaki bölümlerde bu yöntemler araştırılacak. İsterseniz, hangi etiketlerin uygulanacağını denetlemek için bir koşullu **ifadede, gözden** geçirmek üzere hangi görüntülerin gönderileceğini kontrol edebilirsiniz.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy yöntemi

**Program** sınıfında **EvaluateAdultRacy** yöntemine bakın. Bu yöntem, bir görüntü URL 'SI ve anahtar-değer çiftleri dizisini parametre olarak alır. Görüntünün yetişkinlere yönelik ve yoğun puanlarını almak için Content Moderator Image API 'sini (REST kullanarak) çağırır. Her birinin puanı 0,4 ' den büyükse (Aralık 0 ile 1 arasındadır), bu, **Gözden** 

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>EvaluateComputerVisionTags yöntemi

Sonraki yöntem, bir görüntü URL 'sini ve Görüntü İşleme abonelik bilgilerinizi alır ve ünlüler için görüntüyü analiz eder. Bir veya daha fazla ünlüler bulunursa, Ayrıntılar **Etiketler** dizisindeki karşılık gelen değeri **true** olarak ayarlar.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags yöntemi

Ardından,  &mdash; Bu Case Flags, toys ve kalemlerdeki gerçek ürünleri sınıflandırın EvaluateCustomVisionTags yöntemine bakın. Kendi özel görüntü sınıflandırıcınızı oluşturmak ve görüntülerde, oyunlarınızı ve kalemleri (veya özel etiketleriniz olarak seçtiğiniz herhangi bir şeyi) anlamak için [nasıl sınıflandırıcı oluşturma](../custom-vision-service/getting-started-build-a-classifier.md) Kılavuzu ' nda bulunan yönergeleri izleyin. Bu örnekteki kategorilerden bazılarını hızlıca eğitebilmeniz için [GitHub deposunun](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) **örnek görüntüler** klasöründeki görüntüleri kullanabilirsiniz.

![Kalemlerin, oyunların ve bayrakların eğitim görüntüleriyle Web sayfası Özel Görüntü İşleme](images/tutorial-ecommerce-custom-vision.PNG)

Sınıflandırıcınızı eğittikten sonra, tahmin anahtarını ve tahmin uç nokta URL 'sini alın (bkz. alma konusunda yardıma ihtiyacınız varsa [URL 'yi ve tahmin anahtarını alma](../custom-vision-service/use-prediction-api.md#get-the-url-and-prediction-key) ) ve bu değerleri `CustomVisionKey` `CustomVisionUri` sırasıyla ve alanlarınıza atayın. Yöntemi sınıflandırıcının sorgulamak için bu değerleri kullanır. Sınıflandırıcı görüntüde bir veya daha fazla özel etiket bulursa, bu yöntem, Ayrıntılar **Etiketler** dizisindeki karşılık gelen değerleri **true** olarak ayarlar.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Inceleme aracı için incelemeler oluşturma

Önceki bölümlerde, uygulamanın yetişkinlere ve kcy içerik (Content Moderator), Ünlüler (Görüntü İşleme) ve diğer diğer nesneler (Özel Görüntü İşleme) için gelen görüntüleri nasıl taradığından bahsedilirsiniz. Ardından, tüm uygulanmış etiketleri ( _meta veri_ olarak geçirildi) Content moderator İnceleme aracına yükleyen **createreview** yöntemine bakın.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Görüntüler [Content moderator gözden geçirme aracının](https://contentmoderator.cognitive.microsoft.com/)gözden geçirme sekmesinde görünür.

![Birkaç görüntü ve vurgulanan etiketleriyle Content Moderator gözden geçirme aracının ekran görüntüsü](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Test görüntülerinin listesini gönderme

**Ana** yöntemde görebileceğiniz gibi, bu program, görüntü URL 'lerinin listesini içeren bir _Urls.txt_ dosyası Ile bir "c:test" dizini arar. Bu dosya ve dizini oluşturun veya yolu metin dosyanıza işaret etmek üzere değiştirin. Ardından, bu dosyayı test etmek istediğiniz görüntülerin URL 'Leriyle doldurun.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Programı çalıştırma

Yukarıdaki adımların tümünü izlediyseniz, programın her bir görüntüyü işlemesi (ilgili Etiketler için üç hizmeti sorguladığını) ve ardından etiket bilgileri olan görüntüleri Content Moderator gözden geçirme aracına yüklemesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, ürün görüntülerini analiz etmek, ürün türüne göre etiketlemek ve bir gözden geçirme ekibine içerik denetlemesi hakkında bilinçli kararlar vermek için bir program ayarlarsınız. Ardından, görüntü denetleme ayrıntıları hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Denetlenen görüntüleri gözden geçirme](./review-tool-user-guide/review-moderated-images.md)