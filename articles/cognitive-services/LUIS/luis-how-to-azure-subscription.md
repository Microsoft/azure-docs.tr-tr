---
title: Yazma ve çalışma zamanı anahtarlarını kullanma-LUSıS
description: LUSıS 'yi ilk kez kullandığınızda bir yazma anahtarı oluşturmanız gerekmez. Uygulamayı yayınlamak ve çalışma zamanı uç noktanızı kullanmak istediğinizde, çalışma zamanı anahtarını oluşturup uygulamaya atamanız gerekir.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 168833ea0a451913f4ed019cba832a16207e0d9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99988158"
---
# <a name="create-luis-resources"></a>LUSıS kaynakları oluşturma

Yazma ve sorgu tahmini çalışma zamanı kaynakları, Language Understanding (LUSıS) uygulamanız ve tahmin uç noktanıza kimlik doğrulaması sağlar.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUSıS kaynakları

LUSıS üç tür Azure kaynağına ve Azure olmayan bir kaynağa izin verir:

|Kaynak|Amaç|Bilişsel hizmet `kind`|Bilişsel hizmet `type`|
|--|--|--|--|
|Kaynak yazma|Uygulamalarınızı oluşturmanıza, yönetmenize, eğmenize, test etmenize ve yayımlamanıza olanak sağlar. LUSıS uygulamalarını programlı bir şekilde veya LUSıS portalından [yazmayı düşünüyorsanız BIR lusıs yazma kaynağı oluşturun](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) . Azure yazma kaynaklarınızı uygulamanıza bağlayabilmeniz için [lusıs hesabınızı geçirmeniz](luis-migration-authoring.md#what-is-migration) gerekir. Kişileri [katkıda bulunan rolü](#contributions-from-other-authors)atayarak yazma kaynağına yönelik izinleri denetleyebilirsiniz. <br><br> LUSıS yazma kaynağı için bir katman kullanılabilir:<br> <ul> <li>Ücretsiz **F0 Authoring Resource**, size aylık 1.000.000 ücretsiz yazma işlemi ve 1.000 ücretsiz test tahmin uç noktası istekleri sağlar. |`LUIS.Authoring`|`Cognitive Services`|
|Tahmin kaynağı| LUSıS uygulamanızı yayımladıktan sonra, tahmin uç noktası isteklerini sorgulamak için tahmin kaynağını/anahtarını kullanın. İstemci uygulamanızın, yazma veya başlangıç kaynağı tarafından belirtilen 1.000 isteklerinin ötesinde tahmin isteklerini yapmadan önce bir LUSıS tahmin kaynağı oluşturun. <br><br> Tahmin kaynağı için iki katman mevcuttur:<br><ul> <li> **Ücretsiz F0 tahmin kaynağı**, aylık 10.000 ücretsiz tahmin uç noktası isteği sağlar.<br> <li> Ücretli katman olan **Standart S0 tahmin kaynağı**. [Fiyatlandırma hakkında daha fazla bilgi edinin.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Başlangıç/deneme kaynağı|Uygulamalarınızı oluşturmanıza, yönetmenize, eğmenize, test etmenize ve yayımlamanıza olanak sağlar. Bu kaynak varsayılan olarak, LUO 'da ilk kez oturum açtığınızda başlangıç kaynağı seçeneğini belirlerseniz oluşturulur. Başlangıç anahtarı sonunda kullanım dışı olacaktır. Tüm LUO kullanıcılarının [hesaplarını geçirilmesi](luis-migration-authoring.md#what-is-migration) ve lusıs uygulamalarını bir yazma kaynağına bağlantısı gerekir. Yazma kaynağının aksine, bu kaynak size Azure rol tabanlı erişim denetimi için izinler vermez. <br><br> Yazma kaynağı gibi, başlangıç kaynağı size 1.000.000 ücretsiz yazma işlemi ve 1.000 ücretsiz test tahmin uç noktası istekleri sağlar.|-|Azure kaynağı değil.|
|[Bilişsel hizmetler çoklu hizmet kaynak anahtarı](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|LUVE diğer desteklenen bilişsel hizmetler ile paylaşılan sorgu tahmini uç noktası istekleri.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUıSıS iki tür F0 (ücretsiz katman) kaynağı sağlar: bir tane, işlem yazma ve tahmin işlemleri için bir tane. Tahmin işlemleri için ücretsiz kota kullanıyorsanız, F0 tahmin kaynağını kullandığınızdan emin olun. Bu, size aylık 1.000 tahmin işlemleri sağlayan yazma kaynağına değil aylık 10.000 ücretsiz işlem sağlar.

Azure kaynak oluşturma işlemi tamamlandığında, kaynağı LUı portalındaki uygulamaya [atayın](#assign-a-resource-to-an-app) .

> [!important]
> LUSıS uygulamalarını yayımlamak ve sorgulamak istediğiniz [bölgelerde](luis-reference-regions.md#publishing-regions) yazmanız gerekir.

## <a name="resource-ownership"></a>Kaynak sahipliği

Bir LUO kaynağı gibi bir Azure kaynağı, kaynağı içeren aboneliğe aittir.

Bir kaynağın sahipliğini değiştirmek için şu eylemlerden birini yapabilirsiniz:
* Aboneliğinizin [sahipliğini](../../cost-management-billing/manage/billing-subscription-transfer.md) aktarın.
* LUO uygulamasını bir dosya olarak dışarı aktarın ve uygulamayı farklı bir abonelikte içeri aktarın. Dışarı aktarma, Lua portalındaki **uygulamalarım** sayfasında bulunur.

## <a name="resource-limits"></a>Kaynak sınırları

### <a name="authoring-key-creation-limits"></a>Anahtar oluşturma sınırlarını yazma

Her abonelik için bölge başına 10 yazma anahtarı oluşturabilirsiniz. Yayımlama bölgeleri, yazma bölgelerinden farklıdır. İstemci uygulamanızın bulunmasını istediğiniz yayımlama bölgesine karşılık gelen yazma bölgesinde bir uygulama oluşturduğunuzdan emin olun. Yazma bölgelerini yayımlama bölgeleriyle eşleme hakkında bilgi için bkz. [yazma ve yayımlama bölgeleri](luis-reference-regions.md). 

Anahtar limitleri hakkında daha fazla bilgi için bkz. [anahtar sınırları](luis-limits.md#key-limits).

### <a name="errors-for-key-usage-limits"></a>Anahtar kullanımı sınırlarına ilişkin hatalar

Kullanım sınırları, fiyatlandırma katmanını temel alır.

Saniye başına işlem (TPS) kotanızı aşarsanız HTTP 429 hatası alırsınız. İşlem/saat (TPM) kotasını aşarsanız bir HTTP 403 hatası alırsınız.


### <a name="reset-an-authoring-key"></a>Yazma anahtarını sıfırlama

[Geçirilen yazma kaynak](luis-migration-authoring.md) uygulamaları için: yazma anahtarınız tehlikeye atılırsa, yazma kaynağının **anahtarlar** sayfasında Azure Portal anahtarı sıfırlayın.

Geçirilmeyen uygulamalar için: anahtar, Lua portalındaki tüm uygulamalarınızda sıfırlanır. Uygulamalarınızı yazma API 'Leri aracılığıyla yazardıysanız, değerini `Ocp-Apim-Subscription-Key` Yeni anahtara değiştirmeniz gerekir.

### <a name="regenerate-an-azure-key"></a>Azure anahtarını yeniden oluşturma

Azure portal **anahtarlar** sayfasından bir Azure anahtarını yeniden oluşturabilirsiniz.


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Uygulama sahipliği, erişim ve güvenlik

Bir uygulama, sahip olunan abonelikle belirlenen Azure kaynakları tarafından tanımlanır.

LUSıS uygulamanızı taşıyabilirsiniz. Azure portal veya Azure CLı kullanarak bunu yapmanıza yardımcı olması için aşağıdaki kaynakları kullanın:

* [Bir uygulamayı LUSıS yazma kaynakları arasında taşıma](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Kaynağı yeni bir kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Bir kaynağı aynı abonelik içinde veya abonelikler arasında taşıma](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Diğer yazarlardan katkı

[Geçirilen yazma kaynak](luis-migration-authoring.md) uygulamaları Için: **ERIŞIM denetimi (IAM)** sayfasını kullanarak Azure Portal bir yazma kaynağı için _katılımcıları_ yönetebilirsiniz. Ortak çalışan e-posta adresini ve katkıda bulunan rolünü kullanarak [bir kullanıcıyı nasıl ekleyeceğinizi](luis-how-to-collaborate.md) öğrenin.

Henüz geçirilmeyen uygulamalar için: tüm _ortak_ ÇALıŞANLARı, Lua portalındaki **Manage-> ortak çalışanlar** sayfasında yönetebilirsiniz.

### <a name="query-prediction-access-for-private-and-public-apps"></a>Özel ve genel uygulamalar için sorgu tahmin erişimi

Özel uygulamalar için sorgu tahmini çalışma zamanı erişimi, sahipler ve katkıda bulunanlar için kullanılabilir. Ortak uygulamalarda, çalışma zamanı erişimi kendi Azure bilişsel [hizmeti](../cognitive-services-apis-create-account.md) veya [lusıs](#create-resources-in-the-azure-portal) çalışma zamanı KAYNAĞıNA ve ortak uygulamanın kimliğine sahip olan kullanıcılar tarafından kullanılabilir.

Şu anda bir ortak uygulamalar kataloğu yok.

### <a name="authoring-permissions-and-access"></a>Yazma izinleri ve erişim
[LUIS](luis-reference-regions.md#luis-website) portalından bir uygulamaya erişim veya [yazma API 'leri](https://go.microsoft.com/fwlink/?linkid=2092087) , Azure yazma kaynağı tarafından denetlenir.

Sahibi ve tüm katkıda bulunanlar, uygulamayı yazmak için erişime sahiptir.

|Yazma erişimi şunları içerir:|Notlar|
|--|--|
|Uç nokta anahtarları ekleme veya kaldırma||
|Dışarı aktarma sürümü||
|Uç nokta günlüklerini dışarı aktar||
|İçeri aktarma sürümü||
|Uygulamayı genel yap|Bir uygulama genel olduğunda, yazma veya bitiş noktası anahtarına sahip olan herkes uygulamayı sorgulayabilir.|
|Modeli değiştir|
|Yayımlama|
|[Etkin öğrenme](luis-how-to-review-endpoint-utterances.md) için uç nokta utslerini gözden geçirme|
|Eğitim|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Tahmin uç noktası çalışma zamanı erişimi

Tahmin uç noktasını sorgulama erişimi, **Yönet** bölümündeki **uygulama bilgileri** sayfasında bir ayarla denetlenir.

|[Özel uç nokta](#runtime-security-for-private-apps)|[Genel uç nokta](#runtime-security-for-public-apps)|
|:--|:--|
|Sahip ve katkıda bulunanlar için kullanılabilir|Sahip, katkıda bulunanlar ve uygulama KIMLIĞINI bilen başka herkes için kullanılabilir|

Bir sunucudan sunucuya bir ortama çağırarak, LUSıS çalışma zamanı anahtarınızı kimlerin göreceğini denetleyebilirsiniz. LUSıS 'yi bir bot 'tan kullanıyorsanız, bot ve LUO arasındaki bağlantı zaten daha güvenlidir. LUSıS uç noktasını doğrudan arıyorsanız, denetimli erişimle (Azure [ad](https://azure.microsoft.com/services/active-directory/)gibi) bir sunucu tarafı API (Azure [işlevi](https://azure.microsoft.com/services/functions/)gibi) oluşturmanız gerekir. Sunucu tarafı API 'SI çağrıldığında ve kimlik doğrulaması yapıldığında ve yetkilendirme doğrulandığında, çağrısı HALSıS 'e geçirin. Bu strateji, ortadaki adam saldırıları engellemez. Ancak, kullanıcılarınızın anahtar ve uç nokta URL 'nizi gizleme, erişimi izlemenize ve uç nokta yanıt günlüğü ( [Application Insights](https://azure.microsoft.com/services/application-insights/)gibi) eklemenize olanak tanır.

### <a name="runtime-security-for-private-apps"></a>Özel uygulamalar için çalışma zamanı güvenliği

Özel bir uygulamanın çalışma zamanı yalnızca aşağıdaki anahtarlar için kullanılabilir:

|Anahtar ve Kullanıcı|Açıklama|
|--|--|
|Sahibin yazma anahtarı| En fazla 1.000 uç nokta okuması|
|Ortak çalışan/katkıda bulunan yazma anahtarları| En fazla 1.000 uç nokta okuması|
|LUŞUNA atanan herhangi bir anahtar, bir yazar veya ortak çalışan/katkıda bulunan|Anahtar kullanımı katmanına göre|

### <a name="runtime-security-for-public-apps"></a>Ortak uygulamalar için çalışma zamanı güvenliği

Uygulamanız ortak olarak yapılandırıldığında, anahtar tüm uç nokta kotasını kullanmadığı sürece, geçerli _BIR_ luya yazma anahtarı veya Luo Endpoint Key onu sorgulayabilir.

Sahibi veya katkıda bulunan bir Kullanıcı, yalnızca uygulama KIMLIĞI verildiğinde Ortak uygulamanın çalışma zamanına erişebilir. LUIN, kullanıcıların ortak bir uygulama araymasına yönelik genel bir pazara veya başka bir yola sahip değildir.

Ortak bir uygulama tüm bölgelerde yayımlanır. Bu nedenle, bölge temelli bir LUSıS kaynak anahtarına sahip bir Kullanıcı, kaynak anahtarıyla ilişkili bölge ile ilişkili olan uygulamaya erişebilir.


### <a name="control-access-to-your-query-prediction-endpoint"></a>Sorgu tahmin uç noktanıza erişimi denetleme

Bir sunucudan sunucuya bir ortama çağırarak, LUSıS tahmin çalışma zamanı uç noktası anahtarınızı kimlerin görebileceğini denetleyebilirsiniz. LUSıS 'yi bir bot 'tan kullanıyorsanız, bot ve LUO arasındaki bağlantı zaten daha güvenlidir. LUSıS uç noktasını doğrudan arıyorsanız, denetimli erişimle (Azure [ad](https://azure.microsoft.com/services/active-directory/)gibi) bir sunucu tarafı API (Azure [işlevi](https://azure.microsoft.com/services/functions/)gibi) oluşturmanız gerekir. Sunucu tarafı API 'SI çağrıldığında ve kimlik doğrulaması ve yetkilendirme doğrulandıktan sonra, çağrıyı HALSıS 'e geçirin. Bu strateji, ortadaki adam saldırıları engellemez. Ancak, bu, uç noktanızı kullanıcılarınıza göre gizleme, erişimi izlemenize ve uç nokta yanıt günlüğü ( [Application Insights](https://azure.microsoft.com/services/application-insights/)gibi) eklemenize olanak tanır.

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>LUO portalında oturum açın ve yazmaya başlayın

1. [Luo portalında](https://www.luis.ai) oturum açın ve kullanım koşullarını kabul edin.
1. Azure LUSıS yazma anahtarınızı seçerek LUSıS uygulamanızı yazmaya başlayın:

   ![Hoş Geldiniz ekranını gösteren ekran görüntüsü.](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Kaynak seçim işlemiyle işiniz bittiğinde [Yeni bir uygulama oluşturun](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Azure CLı 'de kaynak oluşturma

Her kaynağı ayrı ayrı oluşturmak için [Azure CLI](/cli/azure/install-azure-cli) 'yi kullanın.

Kaynak `kind` :

* Özgün `LUIS.Authoring`
* Hızlı `LUIS`

1. Azure CLı 'da oturum açın:

    ```azurecli
    az login
    ```

    Bu komut, doğru hesabı seçip kimlik doğrulaması sağlayabilmeniz için bir tarayıcı açar.

1. Adında bir LUSıS yazma kaynağı oluşturun `LUIS.Authoring` `my-luis-authoring-resource` . Bölge için adlı _mevcut_ kaynak grubunda oluşturun `my-resource-group` `westus` .

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Adında bir LUSıS tahmin uç noktası kaynağı oluşturun `LUIS` `my-luis-prediction-resource` . Bölge için adlı _mevcut_ kaynak grubunda oluşturun `my-resource-group` `westus` . Boş katmanın sağladığı daha yüksek aktarım hızını istiyorsanız, `F0` olarak değiştirin `S0` . [Fiyatlandırma katmanları ve verimlilik hakkında daha fazla bilgi edinin.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Bu anahtarlar, Lua   >  portalındaki **Azure kaynaklarını** Yönet sayfasında atanana kadar Luo portalı tarafından kullanılmaz.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>LUSıS portalında kaynak atama

Tek bir uygulama için veya LUSıS 'deki tüm uygulamalar için bir yazma kaynağı atayabilirsiniz. Aşağıdaki yordam, tüm uygulamaları tek bir yazma kaynağına atar.

1. [Lui portalında](https://www.luis.ai)oturum açın.
1. Sağ üst köşede kullanıcı hesabınızı seçin ve ardından **Ayarlar**' ı seçin.
1. **Kullanıcı ayarları** sayfasında, **yazma kaynağı Ekle**' yi seçin ve ardından var olan bir yazma kaynağı ' nı seçin. **Kaydet**’i seçin.

## <a name="assign-a-resource-to-an-app"></a>Uygulamaya kaynak atama

>[!NOTE]
>Azure aboneliğiniz yoksa, yeni bir kaynak atayamaz veya oluşturamayacaksınız. Portaldan yeni bir kaynak oluşturmak için bir [Azure Ücretsiz hesabı](https://azure.microsoft.com/en-us/free/) oluşturmanız ve ardından lusıs 'e dönmeniz gerekir.

Bu yordamı, bir yazma veya tahmin kaynağı oluşturmak veya bir uygulamaya atamak için kullanabilirsiniz: 

1. [Lui portalında](https://www.luis.ai)oturum açın. **Uygulamalarım** listesinden bir uygulama seçin.
1.   >  **Azure kaynaklarını** yönetme sayfasına gidin:

    ![Azure kaynakları sayfasını gösteren ekran görüntüsü.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. **Tahmin kaynağı** veya **yazma kaynağı** sekmesinde, **tahmin kaynağı Ekle** veya **yazma kaynağı Ekle** düğmesini seçin.
1. Doğru kaynağı bulmak için formundaki alanları kullanın ve ardından **Kaydet**' i seçin.
1. Mevcut bir kaynağınız yoksa pencerenin alt kısmında **Yeni BIR Luo kaynağı oluştur** ' u seçerek bir tane oluşturabilirsiniz.


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>LUO portalını kullanmadan sorgu tahmini çalışma zamanı kaynağı atama

CI/CD işlem hatları gibi otomatikleştirilmiş süreçler için bir Luo çalışma zamanı kaynağının bir LUU uygulamasına atanmasını otomatikleştirmek isteyebilirsiniz. Bunu yapmak için şu adımları izleyin:

1. [Bu Web sitesinden](https://resources.azure.com/api/token?plaintext=true)bir Azure Resource Manager belirteci alın. Bu belirtecin kullanım süreleri doluyor, bu nedenle hemen kullanın. İstek bir Azure Resource Manager belirteci döndürüyor.

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
    ![Azure Resource Manager belirteci istemek için Web sitesini gösteren ekran görüntüsü.](./media/luis-manage-keys/get-arm-token.png)

1. Bu belirteci kullanarak LUSıS çalışma zamanı kaynaklarını abonelikler arasında isteyin. Kullanıcı hesabınızın erişimi olan [Get Lusıs Azure hesapları API 'sini](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)kullanın.

    Bu POST API 'SI aşağıdaki değerleri gerektirir:

    |Üst bilgi|Değer|
    |--|--|
    |`Authorization`|Öğesinin değeri `Authorization` `Bearer {token}` . Belirteç değerinin önünde `Bearer` ve bir boşluk olması gerekir.|
    |`Ocp-Apim-Subscription-Key`|Yazma anahtarınız.|

    API, LUSıS aboneliklerinizi temsil eden bir JSON nesneleri dizisi döndürür. Döndürülen değerler, olarak döndürülen abonelik KIMLIĞINI, kaynak grubunu ve kaynak adını içerir `AccountName` . LUSıS uygulamasına atamak istediğiniz LUO kaynağı olan dizideki öğeyi bulun.

1. [Bir uygulama API 'sine BIR Lusıs Azure hesabı ata](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) ' yı kullanarak, belirteci lusıs kaynağına atayın.

    Bu POST API 'SI aşağıdaki değerleri gerektirir:

    |Tür|Ayar|Değer|
    |--|--|--|
    |Üst bilgi|`Authorization`|Öğesinin değeri `Authorization` `Bearer {token}` . Belirteç değerinin önünde `Bearer` ve bir boşluk olması gerekir.|
    |Üst bilgi|`Ocp-Apim-Subscription-Key`|Yazma anahtarınız.|
    |Üst bilgi|`Content-type`|`application/json`|
    |QueryString|`appid`|LUIS app kimliği.
    |Gövde||{"Azuyeniden gönderme Scriptionıd": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "Lua-usbatı-S0-2"}|

    Bu API başarılı olduğunda, döndürür `201 - created status` .

## <a name="unassign-a-resource"></a>Bir kaynağın atamasını kaldırma

1. [Luo portalında](https://www.luis.ai)oturum açın ve ardından **uygulamalarım** listesinden bir uygulama seçin.
1.   >  **Azure kaynaklarını** yönetme sayfasına gidin.
1. **Tahmin kaynağı** veya **yazma kaynağı** sekmesinde kaynağın **atamasını Kaldır** düğmesini seçin.

Bir kaynağın atamasını kaldırdığınızda, Azure 'dan silinmez. Yalnızca LUSıS bağlantısı kaldırılır.


## <a name="delete-an-account"></a>Hesap silme

Hesabınızı sildiğinizde hangi verilerin silindiği hakkında bilgi için bkz. [veri depolama ve kaldırma](luis-concept-data-storage.md#accounts) .

## <a name="change-the-pricing-tier"></a>Fiyatlandırma katmanını değiştirme

1.  [Azure Portal](https://portal.azure.com), lusıs aboneliğinizi bulun ve seçin:

    ![Azure portal bir LUO aboneliğini gösteren ekran görüntüsü.](./media/luis-usage-tiers/find.png)
1.  Kullanılabilir fiyatlandırma katmanlarını görmek için **fiyatlandırma katmanını** seçin:

    ![Fiyatlandırma Katmanı menü öğesini gösteren ekran görüntüsü.](./media/luis-usage-tiers/subscription.png)
1.  Fiyatlandırma katmanını seçin ve ardından Değişiklerinizi kaydetmek için **Seç** ' e tıklayın:

    ![Fiyatlandırma katmanını seçme ve kaydetme işlemlerinin nasıl yapılacağını gösteren ekran görüntüsü.](./media/luis-usage-tiers/plans.png)

    Fiyatlandırma değişikliği tamamlandığında, bir açılır pencere, fiyatlandırma katmanı güncelleştirmesini doğrular:

    ![Fiyatlandırma güncelleştirmesini doğrulayan açılır pencerenin ekran görüntüsü.](./media/luis-usage-tiers/updated.png)
1. [Bu uç nokta anahtarını](#assign-a-resource-to-an-app) **Yayımla** sayfasında atamayı ve tüm uç nokta sorgularında kullanmayı unutmayın.

## <a name="view-azure-resource-metrics"></a>Azure Kaynak ölçümlerini görüntüleme

### <a name="view-a-summary-of-azure-resource-usage"></a>Azure Kaynak kullanımının özetini görüntüleme
LUSıS kullanım bilgilerini Azure portal görüntüleyebilirsiniz. **Genel bakış** sayfasında, son aramalar ve hatalar dahil olmak üzere bir özet görüntülenir. Bir LUSıS uç noktası isteği yaparsanız, değişikliğin görünmesi en fazla beş dakika bekleyin.

![Genel Bakış sayfasını gösteren ekran görüntüsü.](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Azure Kaynak kullanımı grafiklerini özelleştirme
**Ölçümler** sayfası, verilerin daha ayrıntılı bir görünümünü sağlar:

![Ölçümler sayfasını gösteren ekran görüntüsü.](./media/luis-usage-tiers/metrics-default.png)

Ölçüm grafiklerinizi belirli bir süre ve ölçüm türü için yapılandırabilirsiniz:

![Özelleştirilmiş bir grafik gösteren ekran görüntüsü.](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Toplam işlem eşiği uyarısı
Belirli bir işlem eşiğine ne zaman ulaşırsanız (örneğin, 10.000 işlem), bir uyarı oluşturabilirsiniz:

![Uyarı kuralları sayfasını gösteren ekran görüntüsü.](./media/luis-usage-tiers/alert-default.png)

Belirli bir döneme ait **Toplam çağrı** ölçümü için bir ölçüm uyarısı ekleyin. Uyarı alması gereken tüm kişilerin e-posta adreslerini ekleyin. Uyarı alması gereken tüm sistemler için Web kancaları ekleyin. Ayrıca, uyarı tetiklendiğinde bir Logic App de çalıştırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Sürümlerin uygulama yaşam döngüsünü denetlemek için [nasıl](luis-how-to-manage-versions.md) kullanılacağını öğrenin.
* Yeni [yazma kaynağına](luis-migration-authoring.md)geçiş yapın.
