---
title: Postman ile istekte bulunma
titleSuffix: Azure Digital Twins
description: Azure dijital TWINS API 'Lerini test etmek için Postman yapılandırma ve kullanma hakkında bilgi edinin.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: a528e224511fda363afb80a7749a018e07b5fa26
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588013"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Azure dijital TWINS API 'Lerine istek göndermek için Postman kullanma

[Postman](https://www.getpostman.com/) , bir masaüstü ve EKLENTI tabanlı GUI 'de önemli http isteği işlevleri sağlayan bir rest test aracıdır. Bunu kullanarak HTTP istekleri oluşturabilir ve bunları [Azure Digital TWINS REST API 'lerine](how-to-use-apis-sdks.md)gönderebilirsiniz.

Bu makalede, [Postman Rest Istemcisinin](https://www.getpostman.com/) Azure dijital TWINS API 'leri ile etkileşime geçmek için aşağıdaki adımlarla nasıl yapılandırılacağı açıklanmaktadır:

1. Postman 'da API istekleri yapmak için kullanacağınız [**bir taşıyıcı belirteci almak**](#get-bearer-token) IÇIN Azure CLI 'yi kullanın.
1. Bir [**Postman koleksiyonu**](#about-postman-collections) kurun ve POSTMAN Rest istemcisini, kimlik doğrulaması için taşıyıcı belirtecinizi kullanacak şekilde yapılandırın. Koleksiyonu ayarlarken, şu seçeneklerden birini seçebilirsiniz:
    1. Azure dijital TWINS API isteklerinin önceden oluşturulmuş bir koleksiyonunu [**Içeri aktarın**](#import-collection-of-azure-digital-twins-apis) .
    1. Sıfırdan kendi koleksiyonunuzu [**oluşturun**](#create-your-own-collection) .
1. Yapılandırılmış koleksiyonunuza [**Istek ekleyin**](#add-an-individual-request) ve bunları Azure dijital TWINS API 'lerine gönderin.

Azure dijital TWINS 'in, birlikte çalışabilmeniz için kullanabileceğiniz iki API kümesi vardır: **veri düzlemi** ve **Denetim düzlemi**. Bu API kümeleri arasındaki fark hakkında daha fazla bilgi için bkz. [*nasıl yapılır: Azure Digital TWINS API 'leri ve SDK 'Larını kullanma*](how-to-use-apis-sdks.md). Bu makale, her iki API kümesi için bilgiler içerir.

## <a name="prerequisites"></a>Önkoşullar

Azure dijital TWINS API 'Lerine erişmek için Postman kullanmaya devam etmek için bir Azure dijital TWINS örneği ayarlamanız ve Postman 'yı indirmeniz gerekir. Bu bölümün geri kalanında bu adımlarda adım adım gösterilmektedir.

### <a name="set-up-azure-digital-twins-instance"></a>Azure dijital TWINS örneğini ayarlama

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Postman indirin

Ardından, Postman istemcisinin Masaüstü sürümünü indirin. [*Www.getpostman.com/apps*](https://www.getpostman.com/apps) adresine gidin ve uygulamayı indirmek için yönergeleri izleyin.

## <a name="get-bearer-token"></a>Taşıyıcı belirtecini al

Postman 'ı ve Azure dijital TWINS örneğinizi ayarlamış olduğunuza göre, Postman isteklerinin Azure dijital TWINS API 'Lerinde yetkilendirmek için kullanabileceği bir taşıyıcı belirteç almanız gerekir.

Bu belirteci almanın birkaç olası yolu vardır. Bu makalede, Azure hesabınızda oturum açmak ve bu şekilde bir belirteç almak için [Azure CLI](/cli/azure/install-azure-cli) kullanılmaktadır.

[Yerel olarak yüklü](/cli/azure/install-azure-cli)BIR Azure CLI 'niz varsa, aşağıdaki komutları çalıştırmak için makinenizde bir komut istemi başlatabilirsiniz.
Aksi takdirde, tarayıcınızda bir [Azure Cloud Shell](https://shell.azure.com) penceresi açabilir ve komutları burada çalıştırabilirsiniz.

1. İlk olarak, şu komutu çalıştırarak Azure 'da uygun kimlik bilgileriyle oturum açtığınızdan emin olun:

    ```azurecli-interactive
    az login
    ```

2. Daha sonra, Azure dijital TWINS hizmetine erişimi olan bir taşıyıcı belirteci almak için [az Account Get-Access-Token](/cli/azure/account#az_account_get_access_token) komutunu kullanın. Azure dijital TWINS kaynaklarına erişebilen bir erişim belirteci almak için bu komutta Azure Digital TWINS hizmet uç noktası kaynak KIMLIĞINI geçireceğiz. 

    Belirteç için gereken bağlam, kullandığınız API kümesine bağlıdır, bu nedenle [veri düzlemi](how-to-use-apis-sdks.md#overview-data-plane-apis) ve [Denetim düzlemi](how-to-use-apis-sdks.md#overview-control-plane-apis) API 'leri arasında seçim yapmak için aşağıdaki sekmeleri kullanın.

    # <a name="data-plane"></a>[Veri düzlemi](#tab/data-plane)
    
    **Veri düzlemi** API 'leri ile kullanmak üzere bir belirteç almak için, belirteç bağlamı için aşağıdaki statik değeri kullanın: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Bu, Azure Digital TWINS hizmet uç noktası için kaynak KIMLIĞIDIR.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Kontrol düzlemi](#tab/control-plane)
    
    **Denetim düzlemi** API 'leriyle kullanılacak bir belirteç almak için, belirteç bağlamı için aşağıdaki değeri kullanın: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---

    >[!NOTE]
    > Örneğinden farklı bir Azure Active Directory kiracısına ait bir hizmet sorumlusu veya Kullanıcı hesabı kullanarak Azure dijital TWINS örneğinizi erişmeniz gerekiyorsa, Azure dijital TWINS örneğinin "giriş" kiracısından bir **belirteç** istemeniz gerekir. Bu işlem hakkında daha fazla bilgi için bkz. [*nasıl yapılır: yazma uygulaması kimlik doğrulaması kodu*](how-to-authenticate-client.md#authenticate-across-tenants).

3. Sonuç olarak değerini kopyalayın `accessToken` ve sonraki bölümde kullanmak üzere kaydedin. Bu, isteklerinizi yetkilendirmek için Postman 'a sağlayabileceğiniz **belirteç değeridir** .

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Az Account Get-Access-Token komutunun sonucunu gösteren konsolun ekran görüntüsü. AccessToken alanı ve onun örnek değeri vurgulanır.":::

>[!TIP]
>Bu belirteç en az beş dakika ve en fazla 60 dakika için geçerlidir. Geçerli belirteç için ayrılan süre dışında çalıştırırsanız, yeni bir tane almak için bu bölümdeki adımları tekrarlayabilirsiniz.

Ardından, Azure dijital TWINS 'e API istekleri yapmak için bu belirteci kullanmak üzere Postman 'yi ayarlayacaksınız.

## <a name="about-postman-collections"></a>Postman koleksiyonları hakkında

Postman 'daki istekler **koleksiyonlara** (istek grupları) kaydedilir. İsteklerinizi gruplamak için bir koleksiyon oluşturduğunuzda, aynı anda birçok isteğe ortak ayarları uygulayabilirsiniz. Bu, Azure dijital TWINS API 'Lerinde birden fazla istek oluşturmayı planlıyorsanız, yalnızca tüm koleksiyon için bu ayrıntıları bir kez yapılandırmak istiyorsanız yetkilendirmeyi büyük ölçüde basitleştirebilir.

Azure dijital TWINS ile çalışırken, [tüm Azure dijital TWINS isteklerinin önceden oluşturulmuş bir koleksiyonunu](#import-collection-of-azure-digital-twins-apis)içeri aktararak çalışmaya başlayabilir. API 'Leri araştırıyorsanız ve istek örnekleri ile hızlıca bir proje kurmak istiyorsanız bunu yapmak isteyebilirsiniz.

Alternatif olarak, [kendi boş koleksiyonunuzu oluşturarak](#create-your-own-collection) ve yalnızca Ihtiyaç duyduğunuz API 'leri çağıran tek tek istekler ile doldurarak sıfırdan başlamak da tercih edebilirsiniz. 

Aşağıdaki bölümlerde bu işlemlerin her ikisi de açıklanır. Makalenin geri kalanı yerel Postman uygulamanızda gerçekleşmektedir, bu nedenle devam edin ve şimdi bilgisayarınızda Postman uygulamasını açın.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Azure dijital TWINS API 'Leri koleksiyonunu içeri aktarma

Postman 'da Azure dijital TWINS kullanmaya başlamak için hızlı bir yol, Azure dijital TWINS API 'Leri için önceden oluşturulmuş bir istek koleksiyonunu içeri aktarmanız.

### <a name="download-the-collection-file"></a>Koleksiyon dosyasını indir

API kümesini içeri aktarmanın ilk adımı bir koleksiyonu indirdir. Önceden oluşturulmuş koleksiyon seçeneklerini görmek için, seçtiğiniz veri düzlemi veya denetim düzlemi için aşağıdaki sekmeyi seçin.

# <a name="data-plane"></a>[Veri düzlemi](#tab/data-plane)

Şu anda seçim yapabileceğiniz iki Azure dijital TWINS veri düzlemi koleksiyonu mevcuttur:
* [**Azure dijital TWINS Postman koleksiyonu**](https://github.com/microsoft/azure-digital-twins-postman-samples): Bu koleksiyon, Postman 'Da Azure dijital TWINS için basit bir başlangıç deneyimi sağlar. İstekler örnek verileri içerir, böylece bunları gereken en az düzenleme ile çalıştırabilirsiniz. Örnek bilgiler içeren bir digestible anahtar API isteği kümesi istiyorsanız bu koleksiyonu seçin.
    - Koleksiyonu bulmak için depo bağlantısına gidin ve *postman_collection.js* adlı dosyayı açın.
* **[Azure dijital TWINS veri düzlemi Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)**: Bu depo, bir koleksiyon olarak Postman 'a indirilebilen ve içeri aktarılabilen Azure Digital TWıNS API kümesi Için tüm Swagger dosyasını içerir. Bu, her API isteğinin kapsamlı bir kümesini sağlar, ancak örnek veriler yerine boş veri gövdeleriyle birlikte. Her API çağrısına erişebilmek ve tüm verileri kendiniz doldurmanız istiyorsanız bu koleksiyonu seçin.
    - Koleksiyonu bulmak için depo bağlantısına gidin ve en son spec sürümüne ait klasörü seçin. Buradan *digitaltwins.js* adlı dosyayı açın.

# <a name="control-plane"></a>[Kontrol düzlemi](#tab/control-plane)

Şu anda denetim düzlemi için kullanılabilen koleksiyon, [**Azure Digital TWINS denetim düzlemi Swagger**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)' dur. Bu depo, Azure Digital TWINS API kümesi için, bir koleksiyon olarak indirilebilen ve Postman 'a alınabilecek tüm Swagger dosyasını içerir. Bu, her API isteğinin kapsamlı bir kümesini sağlar.

Koleksiyonu bulmak için depo bağlantısına gidin ve en son spec sürümüne ait klasörü seçin. Buradan *digitaltwins.js* adlı dosyayı açın.

---

Seçtiğiniz koleksiyonu Postman 'a alabilmeniz için makinenize nasıl indirileceği aşağıda verilmiştir.
1. Tarayıcınızda GitHub 'da koleksiyon dosyasını açmak için yukarıdaki bağlantıları kullanın.
1. Dosyanın ham metnini açmak için **Ham** düğmesini seçin.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="GitHub 'daki dosya digitaltwins.jsveri düzlemi ekran görüntüsü. Ham düğmenin etrafında bir vurgu vardır." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Penceredeki metni kopyalayın ve makinenizde yeni bir dosyaya yapıştırın.
1. Dosyayı bir *. JSON* uzantısıyla kaydedin (dosyayı daha sonra bulmak üzere anımsayabileceğiniz sürece dosya adı istediğiniz her şey olabilir).

### <a name="import-the-collection"></a>Koleksiyonu içeri aktar

Sonra, koleksiyonu Postman içine aktarın.

1. Ana Postman penceresinde **Içeri aktar** düğmesini seçin.
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Yeni açılan Postman penceresinin ekran görüntüsü. ' Içeri Aktar ' düğmesi vurgulanır." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. Aşağıdaki Içeri aktarma penceresinde, **dosyaları karşıya yükle** ' yi seçin ve daha önce oluşturduğunuz makinenizde koleksiyon dosyasına gidin. Aç’ı seçin.
1. Onaylamak için **Içeri aktar** düğmesini seçin.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Bir koleksiyon olarak içeri aktarılacak dosyayı ve Içeri aktarma düğmesini gösteren Postman 'ın ' Içeri aktarma ' penceresinin ekran görüntüsü.":::

Yeni içeri aktarılan koleksiyon artık koleksiyonlar sekmesinde ana Postman görünümünüze görünebilirler.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Ana Postman penceresinin ekran görüntüsü. Yeni içeri aktarılan koleksiyon ' koleksiyonlar ' sekmesinde vurgulanır." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Daha sonra, yetkilendirme için koleksiyona bir taşıyıcı belirteci eklemek ve Azure dijital TWINS örneğinizi bağlamak için bir sonraki bölüme devam edin.

### <a name="configure-authorization"></a>Yetkilendirmeyi yapılandırma

Daha sonra, bazı erişim ayrıntılarını yapılandırmak için oluşturduğunuz koleksiyonu düzenleyin. Oluşturduğunuz koleksiyonu vurgulayın ve **daha fazla eylem görüntüle** simgesini seçerek menüyü çekin. **Düzenle**'yi seçin.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Postman 'ın ekran görüntüsü. İçeri aktarılan koleksiyon için ' daha fazla eylem görüntüle ' simgesi vurgulanmıştır ve seçeneklerde ' Edit ' vurgulanır." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Yetkilendirme için koleksiyona bir taşıyıcı belirteci eklemek için bu adımları izleyin. Burada, koleksiyonunuzdaki tüm API istekleri için kullanmak üzere [taşıyıcı belirtecini al](#get-bearer-token) bölümünde topladığınız **belirteç değerini** kullanacaksınız.

1. Koleksiyonunuzun düzenleme iletişim kutusunda, **Yetkilendirme** sekmesinde olduğunuzdan emin olun. 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Postman 'da içeri aktarılan koleksiyonun düzenleme iletişim kutusunun ' yetkilendirme ' sekmesini gösteren ekran görüntüsü." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Türü **OAuth 2,0** olarak ayarlayın, erişim belirtecinizi erişim belirteci kutusuna yapıştırın ve **Kaydet**' i seçin.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="' Yetkilendirme ' sekmesinde içeri aktarılan koleksiyon için Postman düzenleme iletişim kutusunun ekran görüntüsü. Tür ' OAuth 2,0 ' ve erişim belirteci kutusu vurgulanır." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Ek yapılandırma

# <a name="data-plane"></a>[Veri düzlemi](#tab/data-plane)

Bir [veri düzlemi](how-to-use-apis-sdks.md#overview-data-plane-apis) koleksiyonu oluşturuyorsanız, koleksiyonlarla sağlanan bazı **değişkenleri** ayarlayarak koleksiyonun Azure dijital TWINS kaynaklarınıza kolayca bağlanmasına yardımcı olun. Bir koleksiyondaki çok sayıda istek aynı değeri gerektirdiğinde (Azure dijital TWINS örneğinizin ana bilgisayar adı gibi), değeri koleksiyondaki her istek için geçerli bir değişkende saklayabilirsiniz. Azure dijital TWINS için indirilebilir koleksiyonların her ikisi de, koleksiyon düzeyinde ayarlayabileceğiniz önceden oluşturulmuş değişkenlerle birlikte gelir.

1. Koleksiyonunuz için düzenleme iletişim kutusunda, **değişkenler** sekmesine geçin.

1. İlgili değişkenin GEÇERLI değer alanını ayarlamak için, [*Önkoşullar*](#prerequisites) bölümünden örneğinizin **ana bilgisayar adını** kullanın. **Kaydet**’i seçin.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="' Değişkenler ' sekmesini gösteren Postman 'da içeri aktarılan koleksiyonun düzenleme iletişim kutusunun ekran görüntüsü. ' GEÇERLI değer ' alanı vurgulanır." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Koleksiyonunuzun ek değişkenleri varsa, bu değerleri de doldurup kaydedin.

Yukarıdaki adımlarla işiniz bittiğinde, koleksiyonu yapılandırmayı tamamladınız demektir. İsterseniz koleksiyon için Düzenle sekmesini kapatabilirsiniz.

# <a name="control-plane"></a>[Kontrol düzlemi](#tab/control-plane)

Bir [Denetim düzlemi](how-to-use-apis-sdks.md#overview-control-plane-apis) koleksiyonu oluşturuyorsanız, koleksiyonu yapılandırmak için gereken her şeyi tamamladınız. İsterseniz koleksiyon için Düzenle sekmesini kapatabilir ve sonraki bölüme geçebilirsiniz.

--- 

### <a name="explore-requests"></a>İstekleri keşfet

Ardından, Azure Digital TWINS API koleksiyonu içindeki istekleri keşfedebilirsiniz. Önceden oluşturulmuş istekleri görüntülemek için koleksiyonu genişletebilirsiniz (işlem kategorisine göre sıralanır). 

Farklı istekler, örneğiniz ve verileri hakkında farklı bilgiler gerektirir. Belirli bir isteği yapmak için gereken tüm bilgileri görmek için, [Azure Digital TWINS REST API başvuru belgelerindeki](/rest/api/azure-digitaltwins/)istek ayrıntılarına bakın.

Aşağıdaki adımları kullanarak Postman koleksiyonundaki bir isteğin ayrıntılarını düzenleyebilirsiniz:

1. Düzenlenebilir ayrıntılarını çekmek için listeden seçin. 

1. **Yol değişkenleri** altındaki **params** sekmesinde listelenen değişkenlerin değerlerini girin.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Postman 'ın ekran görüntüsü. Koleksiyon, bir isteği göstermek üzere genişletilir. İstek ayrıntılarında ' yol değişkenleri ' bölümü vurgulanır." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. İlgili sekmelerde gerekli **üstbilgileri** veya **gövde** ayrıntılarını sağlayın.

Tüm gerekli ayrıntılar sağlandıktan sonra, isteği **Gönder** düğmesiyle çalıştırabilirsiniz.

Ayrıca, aşağıdaki [*tek bir Istek Ekle*](#add-an-individual-request) bölümünde açıklanan süreci kullanarak kendi isteklerinizi koleksiyona ekleyebilirsiniz.

## <a name="create-your-own-collection"></a>Kendi koleksiyonunuzu oluşturun

Tüm Azure dijital TWINS API 'Lerinin mevcut koleksiyonunu içeri aktarmak yerine, kendi koleksiyonunuzu sıfırdan da oluşturabilirsiniz. Daha sonra [Azure Digital TWINS REST API başvuru belgelerini](/rest/api/azure-digitaltwins/)kullanarak bireysel isteklerle doldurabilirsiniz.

### <a name="create-a-postman-collection"></a>Postman koleksiyonu oluşturma

1. Bir koleksiyon oluşturmak için, ana Postman penceresindeki **Yeni** düğmesini seçin.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Ana Postman penceresinin ekran görüntüsü. ' Yeni ' düğmesi vurgulanır." lightbox="media/how-to-use-postman/postman-new.png":::

    Bir **koleksiyon** türü seçin.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Postman 'da ' Yeni Oluştur ' iletişim kutusunun ekran görüntüsü. ' Koleksiyon ' seçeneği vurgulanır.":::

1. Bu, yeni koleksiyonun ayrıntılarının doldurulmasıyla ilgili bir sekme açar. Koleksiyonun varsayılan adının yanındaki Düzenle simgesini seçin (**Yeni koleksiyon**) ve kendi adınızla değiştirin. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Yeni koleksiyonun Postman 'daki düzenleme iletişim kutusunun ekran görüntüsü. ' Yeni koleksiyon ' adının yanındaki düzenleme simgesi vurgulanır." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Daha sonra, yetkilendirme için koleksiyona bir taşıyıcı belirteci eklemek üzere bir sonraki bölüme devam edin.

### <a name="configure-authorization"></a>Yetkilendirmeyi yapılandırma

Yetkilendirme için koleksiyona bir taşıyıcı belirteci eklemek için bu adımları izleyin. Burada, koleksiyonunuzdaki tüm API istekleri için kullanmak üzere [taşıyıcı belirtecini al](#get-bearer-token) bölümünde topladığınız **belirteç değerini** kullanacaksınız.

1. Yine de yeni koleksiyonunuz için düzenleme iletişim kutusunda, **Yetkilendirme** sekmesine geçin.

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Postman 'daki yeni koleksiyonun düzenleme iletişim kutusunun ' yetkilendirme ' sekmesini gösteren ekran görüntüsü." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Türü **OAuth 2,0** olarak ayarlayın, erişim belirtecinizi erişim belirteci kutusuna yapıştırın ve **Kaydet**' i seçin.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Yeni koleksiyon için Postman düzenleme iletişim kutusunun ' yetkilendirme ' sekmesinde ekran görüntüsü. Tür ' OAuth 2,0 ' ve erişim belirteci kutusu vurgulanır." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Yukarıdaki adımlarla işiniz bittiğinde, koleksiyonu yapılandırmayı tamamladınız demektir. İsterseniz yeni koleksiyon için düzenleme sekmesini kapatabilirsiniz.

Yeni koleksiyon, Koleksiyonlar sekmesindeki Ana Postman görünümünüze görünebilir.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Ana Postman penceresinin ekran görüntüsü. Yeni oluşturulan özel koleksiyon ' koleksiyonlar ' sekmesinde vurgulanır." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Tek bir istek ekleyin

Koleksiyonunuz ayarlandığına göre, Azure Digital Ikizi API 'Lerine kendi isteklerinizi ekleyebilirsiniz.

1. Bir istek oluşturmak için **Yeni** düğmesini yeniden kullanın.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Ana Postman penceresinin ekran görüntüsü. ' Yeni ' düğmesi vurgulanır." lightbox="media/how-to-use-postman/postman-new.png":::

    **İstek** türü seçin.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Postman 'da ' Yeni Oluştur ' iletişim kutusunun ekran görüntüsü. ' Istek ' seçeneği vurgulanır.":::

1. Bu eylem, isteğiniz için bir ad girebileceğiniz Istek Kaydet penceresini açar, isteğe bağlı bir açıklama verebilir ve bir parçası olan koleksiyonu seçebilir. Ayrıntıları girin ve isteği daha önce oluşturduğunuz koleksiyona kaydedin.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="' İsteği Kaydet ' penceresinin Postman 'da açıklanan alanları gösteren ekran görüntüsü. ' Azure dijital TWINS koleksiyonuna Kaydet ' düğmesi vurgulanır.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Artık isteğinizi koleksiyon altında görüntüleyebilir ve düzenlenebilir ayrıntılarını almak için bu seçeneği belirleyebilirsiniz.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Postman 'ın ekran görüntüsü. Azure dijital TWINS koleksiyonu, isteğin ayrıntılarını göstermek üzere genişletilir." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>İstek ayrıntılarını ayarla

Azure dijital TWINS API 'Lerinden birine bir Postman isteği yapmak için API 'nin URL 'SI ve gerektirdiği ayrıntıları hakkında bilgi almanız gerekir. Bu bilgileri [Azure Digital TWINS REST API başvuru belgelerinde](/rest/api/azure-digitaltwins/)bulabilirsiniz.

Örnek sorgu ile devam etmek için bu makalede sorgu API 'SI (ve [başvuru belgeleri](/rest/api/digital-twins/dataplane/query/querytwins)) kullanılarak bir örnekteki tüm dijital TWINS 'leri sorgulama yapılır.

1. Başvuru belgelerinden istek URL 'sini ve türünü alın. Sorgu API 'SI için bu şu anda *göndermekte `https://digitaltwins-hostname/query?api-version=2020-10-31`*.
1. Postman 'da, istek için türü ayarlayın ve URL 'de yer tutucuları gerektiği şekilde doldurarak istek URL 'sini girin. Bunun nedeni, [*Önkoşul*](#prerequisites) bölümünde örneğinizin **ana bilgisayar adını** kullanacaksınız.
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Postman 'daki yeni isteğin ayrıntılarının ekran görüntüsü. Başvuru belgelerindeki sorgu URL 'SI, istek URL 'si kutusuna doldurulmuştur." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. **Params** sekmesindeki istek için gösterilen parametrelerin başvuru belgelerinde açıklananlar ile eşleşip eşleşmediğinden emin olun. Postman 'daki bu istek için, `api-version` önceki adımda istek URL 'si girildiğinde parametre otomatik olarak doldurulmuştur. Sorgu API 'SI için bu tek gerekli parametredir, bu nedenle bu adım yapılır.
1. **Yetkilendirme** sekmesinde, türü **üst öğeden kimlik doğrulamasını devralacak** şekilde ayarlayın. Bu, bu isteğin, daha önce tüm koleksiyon için ayarladığınız yetkilendirmeyi kullanacağını gösterir.
1. **Üst bilgiler** sekmesindeki istek için gösterilen üst bilgilerin, başvuru belgelerinde açıklananlarla eşleştiğinden emin olun. Bu istek için, birkaç üst bilgi otomatik olarak doldurulmuştur. Sorgu API 'SI için üst bilgi seçeneklerinden hiçbiri gerekli değildir, bu nedenle bu adım yapılır.
1. **Gövde** sekmesindeki istek için gösterilen gövdenin, başvuru belgelerinde açıklanan gereksinimle eşleşip eşleşmediğini denetleyin. Sorgu API 'SI için, sorgu metnini sağlamak üzere bir JSON gövdesi gerekir. İşte, örnekteki tüm dijital TWINS sorgularını sorgulayan bu istek için örnek bir gövde:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="İleti sekmesinde, Postman 'daki yeni isteğin ayrıntılarının ekran görüntüsü. ' SELECT * FROM DIGITALTWıNS ' sorgusuyla bir ham JSON gövdesi içerir." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Azure dijital TWINS sorguları oluşturma hakkında daha fazla bilgi için bkz. [*nasıl yapılır: ikizi grafiğini sorgulama*](how-to-query-graph.md).

1. İstek türü için gerekebilecek diğer alanlar için başvuru belgelerini denetleyin. Sorgu API 'SI için, tüm gereksinimler Postman isteğinde karşılanmıştır, bu nedenle bu adım yapılır.
1. Tamamlanan isteğinizi göndermek için **Gönder** düğmesini kullanın.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Yeni isteğin ayrıntılarını gösteren Postman ekran görüntüsü. Gönder düğmesi vurgulanır." lightbox="media/how-to-use-postman/postman-request-send.png":::

İsteği gönderdikten sonra, yanıt ayrıntıları isteğin altındaki Postman penceresinde görüntülenir. Yanıtın durum kodunu ve tüm gövde metinlerini görüntüleyebilirsiniz.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Postman 'daki gönderilen isteğin ekran görüntüsü. İstek ayrıntılarının altına yanıt gösterilir. Durum 200 OK, gövde sorgu sonuçlarını gösterir." lightbox="media/how-to-use-postman/postman-request-response.png":::

Ayrıca, sonucu doğrulamak veya ortaya çıkan hatalar hakkında daha fazla bilgi edinmek için başvuru belgelerinde verilen beklenen yanıt verileriyle yanıtı karşılaştırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Dijital TWINS API 'Leri hakkında daha fazla bilgi edinmek için, bkz. [*nasıl yapılır: Azure dijital TWINS API 'leri ve SDK 'Larını kullanma*](how-to-use-apis-sdks.md)ya da [REST API 'lerinin başvuru belgelerini](/rest/api/azure-digitaltwins/)görüntüleme.