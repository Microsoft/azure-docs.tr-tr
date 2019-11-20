---
title: 'Hızlı başlangıç: Python için Content Moderator istemci kitaplığı | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Python için istemci kitaplığı Content Moderator Azure bilişsel hizmetler 'i kullanmaya nasıl başlaleyeceğinizi öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: 3978b4ec302bf4dd2390f2390c967ca6e12e67d5
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744397"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Hızlı başlangıç: Python için Content Moderator istemci kitaplığı

Python için Content Moderator istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Content Moderator, zararlı olabilecek, riskli veya başka türlü istenmeyen malzemeler için metin, resim ve video içeriğini denetleyen bir bilişsel hizmettir. Hizmet, böyle bir öğeyle karşılaştığında içeriğe uygun etiketler (bayraklar) ekler. Uygulamanız da bu bayraklı içeriği dikkate alarak düzenlemelere uygunluk ya da kullanıcılar için istenen ortamın oluşturulması amacıyla işlem gerçekleştirebilir.

Python için Content Moderator istemci kitaplığını şu şekilde kullanın:

* [Orta metin](#moderate-text)
* [Özel terimler listesi kullanma](#use-a-custom-terms-list)
* [Orta görüntüler](#moderate-images)
* [Özel görüntü listesi kullanma](#use-a-custom-image-list)
* [İnceleme oluştur](#create-a-review)

[Başvuru belgeleri](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [paketi (pipy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [örnekleri](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-content-moderator-azure-resource"></a>Content Moderator Azure kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. Yerel makinenizde [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) kullanarak Content moderator için bir kaynak oluşturun. Aşağıdakileri de yapabilirsiniz:

* Ücretsiz olarak yedi gün boyunca geçerli bir [deneme anahtarı](https://azure.microsoft.com/try/cognitive-services/#decision) alın. Kaydolduktan sonra [Azure Web sitesinde](https://azure.microsoft.com/try/cognitive-services/my-apis/)mevcut olacaktır.  
* [Azure Portal](https://portal.azure.com/) kaynağı görüntüleyin

Deneme aboneliğinizden veya kaynağından bir anahtar aldıktan sonra, sırasıyla `CONTENT_MODERATOR_SUBSCRIPTION_KEY` ve `CONTENT_MODERATOR_ENDPOINT`adlı anahtar ve uç nokta URL 'SI için [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .
 
### <a name="create-a-python-script"></a>Python betiği oluşturma

Yeni bir Python betiği oluşturun ve tercih ettiğiniz düzenleyicide veya IDE 'de açın. Ardından, aşağıdaki `import` deyimlerini dosyanın en üstüne ekleyin.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Daha sonra, kaynağın uç nokta konumu ve anahtarı ortam değişkenleri olarak bir değişken oluşturun. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Uygulamayı başlattıktan sonra ortam değişkenlerini oluşturduysanız, değişkenlere erişmek için onu çalıştıran düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Content Moderator istemci kitaplığını aşağıdaki komutla yükleyebilirsiniz:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar, Content Moderator Python SDK 'sının bazı önemli özelliklerini işler.

|Ad|Açıklama|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Bu sınıf tüm Content Moderator işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve diğer sınıfların örneklerini oluşturmak için kullanırsınız.|
|[Imagemoderationoperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Bu sınıf yetişkinlere yönelik içerik, kişisel bilgiler veya insan yüzeyleri için görüntüleri analiz etmek üzere işlevsellik sağlar.|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Bu sınıf, dil, küfür, hatalar ve kişisel bilgiler için metin çözümleme işlevlerini sağlar.|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Bu sınıf, iş oluşturma, özel iş akışları ve insan incelemeleri için yöntemler de dahil olmak üzere, gözden geçirme API 'lerinin işlevlerini sağlar.|

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Python için Content Moderator istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Orta metin](#moderate-text)
* [Özel terimler listesi kullanma](#use-a-custom-terms-list)
* [Orta görüntüler](#moderate-images)
* [Özel görüntü listesi kullanma](#use-a-custom-image-list)
* [İnceleme oluştur](#create-a-review)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

> [!NOTE]
> Bu hızlı başlangıç, Content Moderator anahtarınız ve uç noktanız için [ortam değişkenleri oluşturduğunuzu](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) varsayar.

Uç noktanız ve anahtarınızla bir istemci örneği oluşturun. Anahtarınızla bir [Biliveservicescredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) nesnesi oluşturun ve bir [Contentmoderatorclient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) nesnesi oluşturmak için bunu uç noktanızla birlikte kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Orta metin

Aşağıdaki kod, bir metin gövdesini çözümlemek ve sonuçları konsola yazdırmak için bir Content Moderator istemcisi kullanır. İlk olarak, projenizin kökünde bir **text_files/** klasör oluşturun ve bir *content_moderator_text_moderation. txt* dosyası ekleyin. Bu dosyaya kendi metninizi ekleyin veya aşağıdaki örnek metni kullanın:

```
Is this a grabage email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
Crap is the profanity here. Is this information PII? phone 3144444444
```

Yeni klasöre bir başvuru ekleyin.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Ardından, Python betiğe aşağıdaki kodu ekleyin.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Özel terimler listesi kullanma

Aşağıdaki kod, metin düzenlemesi için özel terimlerin bir listesini yönetmeyi gösterir. [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) sınıfını kullanarak bir hüküm listesi oluşturabilir, tek tek koşulları yönetebilir ve diğer metin gövdelerini buna karşı görüntüleyebilirsiniz.

### <a name="get-sample-text"></a>Örnek metin al

Bu örneği kullanmak için, projenizin kökünde bir **text_files/** klasör oluşturmanız ve bir *content_moderator_term_list. txt* dosyası eklemeniz gerekir. Bu dosya, terimler listesine göre denetlenecek organik metin içermelidir. Aşağıdaki örnek metni kullanabilirsiniz:

```
This text contains the terms "term1" and "term2".
```

Henüz bir tane tanımınızda klasöre bir başvuru ekleyin.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Liste Oluştur

Özel bir hüküm listesi oluşturmak ve KIMLIK değerini kaydetmek için aşağıdaki kodu Python betiğe ekleyin.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Liste ayrıntılarını tanımlayın

Adını ve açıklamasını düzenlemek için bir listenin KIMLIĞINI kullanabilirsiniz.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Listeye bir terim ekleyin

Aşağıdaki kod `"term1"` ve `"term2"` terimleri listeye ekler.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Listedeki tüm terimleri al

Listedeki tüm koşulları döndürmek için liste KIMLIĞINI kullanabilirsiniz.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Liste dizinini Yenile

Listeden terim eklediğinizde veya kaldırdığınızda, güncelleştirilmiş listeyi kullanabilmeniz için dizini yenilemeniz gerekir.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Listeye karşı ekran metni

Özel terimler listesinin ana işlevselliği, bir metin gövdesinin listeyle karşılaştırılacağı ve eşleşen bir terim olup olmadığını bulmaktır. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Listeden bir terimi kaldırma

Aşağıdaki kod `"term1"` terimi listeden kaldırır.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Bir listeden tüm terimleri kaldır

Tüm koşullarının listesini temizlemek için aşağıdaki kodu kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Listeyi sil

Özel terimler listesini silmek için aşağıdaki kodu kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Orta görüntüler

Aşağıdaki kod, Yetişkin ve kcy içeriğinin görüntülerini çözümlemek için bir [ımagemoderationoperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) nesnesiyle birlikte bir Content moderator istemcisi kullanır.

### <a name="get-images"></a>Görüntüleri al

Analiz etmek için bazı görüntülere bir başvuru tanımlayın.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Ardından, görüntüleriniz arasında yinelemek için aşağıdaki kodu ekleyin. Bu bölümdeki kodun geri kalanı bu döngünün içine gidecektir.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Yetişkin/kcy içeriğini denetle

Aşağıdaki kod, verilen URL 'deki görüntüyü yetişkinlere veya kcy içeriğine göre denetler ve sonuçları konsola yazdırır. Bu koşulların ne anlama geldiğini öğrenmek için [görüntü denetleme kavramları](./image-moderation-api.md) Kılavuzu ' na bakın.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Görünür metni denetle

Aşağıdaki kod görüntüyü görünür metin içeriği için denetler ve sonuçları konsola yazdırır.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Yüzeyleri denetle

Aşağıdaki kod, resmi insan yüzeyleri için denetler ve sonuçları konsola yazdırır.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Özel görüntü listesi kullanma

Aşağıdaki kod, görüntü düzenlemesi için özel bir görüntü listesini yönetmeyi gösterir. Bu özellik, platformunuz Sıklıkla, ekran dışına almak istediğiniz görüntü kümesinin örneklerini alırsa kullanışlıdır. Bu belirli görüntülerin bir listesini tutarak performansı artırabilirsiniz. [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) sınıfı, bir görüntü listesi oluşturmanıza, listedeki tek resimleri yönetmenize ve diğer görüntüleri buna karşı karşılaştırmanıza olanak sağlar.

Bu senaryoda kullanacağınız görüntü URL 'Lerini depolamak için aşağıdaki metin değişkenlerini oluşturun.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Bu, doğru listenin kendisi değil, kodun `add images` bölümüne eklenecek olan görüntülerin resmi olmayan bir listesi.


### <a name="create-an-image-list"></a>Görüntü listesi oluşturma

Bir görüntü listesi oluşturmak ve KIMLIĞINI bir başvuruya kaydetmek için aşağıdaki kodu ekleyin.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Listeye resim ekleme

Aşağıdaki kod, tüm görüntülerinizi listeye ekler.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

**Add_images** yardımcı işlevini betiğiniz başka bir yerde tanımlayın.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Listedeki görüntüleri al

Aşağıdaki kod, listenizdeki tüm görüntülerin adlarını yazdırır.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Liste ayrıntılarını güncelleştir

Listenin adını ve açıklamasını güncelleştirmek için liste KIMLIĞINI kullanabilirsiniz.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Liste ayrıntılarını al

Listenizin geçerli ayrıntılarını yazdırmak için aşağıdaki kodu kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Liste dizinini Yenile

Görüntü ekledikten veya kaldırdıktan sonra, diğer görüntüleri ekran için kullanabilmeniz için liste dizinini yenilemeniz gerekir.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Görüntüleri listeyle eşleştirin

Resim listelerinin ana işlevi, yeni görüntüleri karşılaştırmak ve herhangi bir eşleşme olup olmadığını görmaktır.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Listeden bir görüntüyü kaldırma

Aşağıdaki kod, listeden bir öğeyi kaldırır. Bu durumda, liste kategorisiyle eşleşmeyen bir görüntüdür.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Bir listeden tüm görüntüleri kaldır

Bir görüntü listesini temizlemek için aşağıdaki kodu kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Görüntü listesini silme

Belirli bir görüntü listesini silmek için aşağıdaki kodu kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>İnceleme oluştur

İnsanların gözden geçirebilmesi için Content Moderator Python SDK 'sını kullanarak içerik akışını [Gözden geçirme aracında](https://contentmoderator.cognitive.microsoft.com) bulabilirsiniz. Inceleme aracı hakkında daha fazla bilgi edinmek için bkz. [İnceleme aracı kavramsal Kılavuzu](./review-tool-user-guide/human-in-the-loop.md).

Aşağıdaki kod, gözden geçirme oluşturmak, KIMLIĞINI almak ve gözden geçirme aracının Web portalı aracılığıyla insan girişini aldıktan sonra ayrıntılarını denetlemek için [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) sınıfını kullanır.

### <a name="get-review-credentials"></a>Inceleme kimlik bilgilerini al

İlk olarak, gözden geçirme aracında oturum açın ve takımınızın adını alın. Ardından kodu koddaki uygun değişkene atayın. İsteğe bağlı olarak, gözden geçirme etkinliği üzerinde güncelleştirmeleri almak için bir geri çağırma uç noktası ayarlayabilirsiniz.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Görüntü incelemesi oluşturma

Verilen görüntü URL 'SI için bir inceleme oluşturmak ve göndermek üzere aşağıdaki kodu ekleyin. Kod, gözden geçirme KIMLIĞINE bir başvuru kaydeder. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>İnceleme ayrıntılarını al

Belirli bir incelemenin ayrıntılarını denetlemek için aşağıdaki kodu kullanın. İncelemeyi oluşturduktan sonra, gözden geçirme aracına kendiniz gidebilir ve içerikle etkileşime geçebilirsiniz. Bunun nasıl yapılacağı hakkında bilgi için bkz. [İnceleme nasıl yapılır Kılavuzu](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). İşiniz bittiğinde, bu kodu tekrar çalıştırabilirsiniz ve gözden geçirme işleminin sonuçları alınır.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Bu senaryoda bir geri çağırma uç noktası kullandıysanız, bu biçimde bir olay almalıdır:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı hızlı başlangıç dosyanızdaki `python` komutuyla çalıştırın.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, denetleme görevlerini yapmak için Content Moderator Python kitaplığını nasıl kullanacağınızı öğrendiniz. Daha sonra, bir kavramsal kılavuz okuyarak görüntülerin veya diğer ortamların denetimi hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
>[Görüntü denetleme kavramları](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Azure Content Moderator nedir?](./overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py)' da bulunabilir.