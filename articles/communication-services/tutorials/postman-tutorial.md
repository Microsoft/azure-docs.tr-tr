---
title: Eğitim-Postman ile ACS ' SMS API 'sine giriş ve istek yapma
titleSuffix: An Azure Communication Services tutorial
description: Bir SMS mesajı göndermek için Postman ile ACS için nasıl imza gönderileceğini ve isteklerin nasıl yapılacağını öğrenin.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5805734a9253962d672a4236a5650e9de8b37f0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044303"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Öğretici: oturum açma ve istekleri Postman ile yapma
Bu öğreticide, HTTP kullanarak Azure Communication Services (ACS) hizmetlerine yönelik bir istek oluşturmak için Postman 'ı ayarlamaya ve kullanmaya başlayacağız. Bu öğreticinin sonunda, ACS ve Postman kullanarak başarıyla SMS iletisi gönderdiniz ve ACS içindeki diğer API 'Leri araştırmak için Postman 'ı kullanabilirsiniz.

Bu öğreticide şunları yapacağız:
> [!div class="checklist"]
> * Postman indiriliyor
> * HTTP Isteklerini imzalamak için Postman ayarlama
> * ACS ' SMS API 'sine karşı bir ileti göndermek için bir istek oluşturma.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. Ayrıntılar için bkz. [ücretsiz hesap oluşturma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Ücretsiz hesap, herhangi bir hizmet birleşimini denemek için size Azure kredileri $200 sağlar.
- Etkin bir Iletişim Hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturmayı öğrenin](../quickstarts/create-communication-resource.md).
- SMS mesajları gönderebileceği bir ACS telefon numarası öğrenmek için [telefon numarası aldım](../quickstarts/telephony-sms/get-phone-number.md) .

## <a name="downloading-and-installing-postman"></a>Postman indiriliyor ve yükleniyor

Postman, herhangi bir HTTP API 'sine karşı API istekleri yapabilen bir masaüstü uygulamasıdır. Genellikle API 'Leri test etmek ve keşfetmek için kullanılır. [Postman 'ın Web sitesinden en son masaüstü sürümünü](https://www.postman.com/downloads/)indiriyoruz. Postman için Windows, Mac ve Linux sürümleri bulunur, bu nedenle işletim sisteminiz için uygun sürümü indirin. İndirildikten sonra uygulamayı açın. Sizden oturum açmanızı veya bir Postman hesabı oluşturmanızı isteyen bir başlangıç ekranı görüntülenir. Hesap oluşturma isteğe bağlıdır ve "uygulamaya git ve uygulamayı geç" bağlantısına tıklanarak atlanabilir. Hesap oluşturma, API isteği ayarlarınızı Postman 'a kaydederek daha sonra diğer bilgisayarlardaki isteklerinizi seçmenizi sağlayabilir.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="Postman 'nın başlangıç ekranı, hesap oluşturma veya uygulamayı atlama ve uygulamaya gitme özelliğini gösterir.":::

Bir hesap oluşturduktan veya bir hesabı oluşturmayı atladıktan sonra, şimdi Postman 'nın ana penceresini görmeniz gerekir.

## <a name="creating-and-configuring-a-postman-collection"></a>Postman koleksiyonu oluşturma ve yapılandırma

Postman, istekleri birçok şekilde düzenleyebilir. Bu öğreticinin amaçları doğrultusunda. Bir Postman koleksiyonu oluşturacağız. Bunu yapmak için, uygulamanın sol tarafındaki koleksiyonlar düğmesini seçin:

:::image type="content" source="media/postman/collections-tab.png" alt-text=", Koleksiyonlar sekmesi vurgulanmış şekilde Postman 'nın ana ekranı.":::

Seçildiğinde, koleksiyon oluşturma işlemini başlatmak için "yeni koleksiyon oluştur" düğmesine tıklayın. Postman 'ın orta alanında yeni bir sekme açılır. Koleksiyonu dilediğiniz şekilde adlandırın. Koleksiyonda "ACS" adı verilir:

:::image type="content" source="media/postman/acs-collection.png" alt-text="Bir ACS koleksiyonuyla açılan Postman ve koleksiyonun adı vurgulanmış.":::

Koleksiyonunuz oluşturulup adlandırıldıktan sonra, onu yapılandırmaya hazırsanız.

### <a name="adding-collection-variables"></a>Koleksiyon değişkenleri ekleme

Kimlik doğrulamasını işlemek ve istekleri daha kolay hale getirmek için yeni oluşturulan ACS koleksiyonu içinde iki koleksiyon değişkeni belirteceğiz. Bu değişkenler, ACS koleksiyonunuzdaki tüm istekler için kullanılabilir. Değişken oluşturmaya başlamak için koleksiyonun Variable's sekmesini ziyaret edin.

:::image type="content" source="media/postman/variable-stab.png" alt-text="ACS koleksiyonunun değişkenler sekmesi ile Postman.":::

Koleksiyon sekmesinden iki değişken oluşturun:
- anahtar-bu değişken, Azure portal içindeki Azure Iletişim Hizmetleri ' anahtar sayfasından anahtarınızdan biri olmalıdır. Örneğin, `oW...A==`.
- uç nokta-bu değişken, anahtar sayfasından Azure Iletişim hizmetlerinizin uç noktanız olmalıdır. **Sondaki eğik çizgiyi Kaldırtığınızdan emin olun**. Örneğin, `https://contoso.communication.azure.com`.

Bu değerleri, değişkenler ekranının "Ilk değer" sütununa girin. Girdikten sonra sağ taraftaki tablonun hemen üzerinde yer alan "tümünü Sürdür" düğmesine basın. Doğru yapılandırıldığında Postman ekranınız şuna benzer görünmelidir:

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="ACS koleksiyonunun değişkenlerini doğru şekilde ayarlayarak Postman.":::

İle ilgili daha fazla bilgi edinmek için [Postman 'nın belgelerini](https://learning.postman.com/docs/sending-requests/variables)okuyun.

### <a name="creating-a-pre-request-script"></a>İstek öncesi betik oluşturma

Sonraki adım Postman içinde bir ön istek betiği oluşturmaktır. Ön istek betiği, Postman 'daki her istekten önce çalışan ve sizin adınıza istek parametrelerini değiştirebilen veya değiştirebilen bir betiktir. Bunu, ACS 'nin Hizmetleri tarafından yetkilendiribilecekleri HTTP isteklerimizi imzalamak için kullanacağız. Imzalama gereksinimleri hakkında daha fazla bilgi için, [kimlik doğrulamasında kılavuzumuzu okuyabilirsiniz](/rest/api/communication/authentication).

Bu betiği koleksiyonda bir istekte çalışacak şekilde koleksiyon içinde oluşturacağız. Bunu yapmak için, koleksiyon sekmesinde "istek öncesi betiği" alt sekmesine tıklayın.

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="ACS koleksiyonunun ön istek komut dosyası Sub-Tab seçili olan Postman.":::

Bu alt sekmede, aşağıdaki metin alanına girerek bir ön istek betiği oluşturabilirsiniz. Bunu, tam bir kod Düzenleyicisi içinde, tam olarak içine yapıştırmadan önce [Visual Studio Code](https://code.visualstudio.com/) yazmak daha kolay olabilir. Bu öğreticide betiğe ilişkin her bir bölüm ekleyeceğiz. Yalnızca Postman 'a kopyalamak ve kullanmaya başlamak istiyorsanız sonuna kadar atlayabilirsiniz. Betiği yazmaya başlayalım.

### <a name="writing-the-pre-request-script"></a>Ön istek betiği yazılıyor

Yaptığımız ilk şey eşgüdümlü bir evrensel saat (UTC) dizesi oluşturuyor ve bunu "Date" HTTP üstbilgisine ekliyor. Bu dizeyi daha sonra imzalarken daha sonra kullanmak için bir değişkende depoluyoruz:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

Sonra, SHA 256 kullanarak istek gövdesini karmasına ve `x-ms-content-sha256` üst bilgiye yerleştireceğiz. Postman, genel olarak karma ve imzalama için bazı [Standart kitaplıklar](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) içerir, bu nedenle bunları yüklememiz veya gerektirmemiz gerekmez:

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

Şimdi, HTTP ana bilgisayar üst bilgisinin değerini ayırt etmek için önceden belirtilen uç nokta değişkenimizi kullanacağız. Bu betik işlendikten sonra ana bilgisayar üst bilgisi ayarlanmamışsa bunu yapmanız gerekir:

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

Bu bilgiler oluşturulduktan sonra, HTTP Isteği için imzalandığımız dizeyi oluşturabilir, bu, önceden oluşturulmuş birkaç değerden oluşur:

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Son olarak, bu dizeyi ACS anahtarımız ' u kullanarak imzalayabilmemiz ve sonra bu dizeyi `Authorization` üst bilgide eklemesi gerekir:

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>Son istek ön isteği betiği

Son istek ön isteği betiği şuna benzemelidir:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Bu son betiği, istek öncesi betik sekmesi içindeki metin alanına girin veya yapıştırın:

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="ACS koleksiyonunun ön istek öncesi betiği girilen Postman.":::

Girdikten sonra CTRL + S tuşlarına basın veya Kaydet düğmesine basın. Bu işlem, betiği koleksiyona kaydeder. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Postman 'nın ön isteği kaydetme betiği düğmesi.":::

## <a name="creating-a-request-in-postman"></a>Postman 'da istek oluşturma

Her şey ayarlandığına göre Postman içinde ACS isteği oluşturmaya hazırız. Başlamak için ACS koleksiyonunun yanındaki artı (+) simgesine tıklayın:

:::image type="content" source="media/postman/create-request.png" alt-text="Postman 'nın artı düğmesi.":::

Bu, Postman 'daki istek için yeni bir sekme oluşturur. Bu oluşturulduktan sonra yapılandırmamız gerekir. SMS gönderme API 'sine karşı bir istek oluşturacağız, [Bu nedenle yardım için bu API 'ye yönelik belgelere](/rest/api/communication/sms/send)başvurduğunuzdan emin olun. Postman 'ın isteğini yapılandıralim.

İstek türü ' ne ayarlayarak `POST` ve `{{endpoint}}/sms?api-version=2021-03-07` istek URL 'si alanına girerek başlayın. Bu URL, `endpoint` ACS kaynağına otomatik olarak göndermek için önceden oluşturulmuş değişkenimizi kullanır.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="Tür GÖNDERI olarak ayarlanmış ve URL doğru şekilde ayarlanmış olan bir Postman isteği.":::

Şimdi isteğin gövde sekmesini seçin ve ardından radyo düğmesini "RAW" olarak değiştirin. Sağ tarafta, "metin" yazan bir açılan menü bulunur, bunu JSON olarak değiştirin:

:::image type="content" source="media/postman/postman-json.png" alt-text="İstek gövdesini ham ve JSON olarak ayarlama":::

Bu işlem, bir JSON biçiminde bilgi gönderme ve alma isteğini yapılandırır.

Aşağıdaki metin alanında bir istek gövdesi girmeniz gerekir, bu, aşağıdaki biçimde olmalıdır:

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

"Kimden" değeri için, ACS portalında daha önce bahsedildiği gibi [bir telefon numarası](../quickstarts/telephony-sms/get-phone-number.md) almanız gerekir. Bunu boşluk olmadan ve ülke kodunuzun önüne ekleyerek girin. Örneğin: `+15555551234`. "İletiniz", göndermek istediğiniz herhangi bir şey olabilir, ancak `Hello from ACS` iyi bir örnektir. "To" değeri SMS mesajları alabilen, erişiminiz olan bir telefon olmalıdır. Kendi mobil cihazınızı kullanmak iyi bir fikirdir.

Bu isteği girdikten sonra, daha önce oluşturduğumuz ACS koleksiyonuna kaydetmemiz gerekiyor. Bu, daha önce oluşturduğumuz değişkenleri ve ön istek betiğini seçer. Bunu yapmak için, istek alanının sağ üst kısmındaki "Kaydet" düğmesine tıklayın.

:::image type="content" source="media/postman/postman-save.png" alt-text="Postman isteği için Kaydet düğmesi.":::

Bunu yaptığınızda, isteği ne şekilde çağırmak istediğinizi ve kaydetmek istediğiniz konumu soran bir iletişim kutusu penceresi görüntülenir. Bunu dilediğiniz şekilde adlandırın, ancak iletişim kutusunun alt yarısında ACS koleksiyonunuzu seçtiğinizden emin olun:

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="ACS koleksiyonu seçili olan Postman istek Kaydet iletişim kutusu.":::

## <a name="sending-a-request"></a>İstek gönderiliyor

Her şey ayarlandığına göre, isteği gönderebilmeniz ve telefonunuzdaki bir SMS iletisi alabilmesi gerekir. Bunu yapmak için, oluşturduğunuz isteğin seçili olduğundan emin olun ve sağdaki "Gönder" düğmesine basın:

:::image type="content" source="media/postman/postman-send.png" alt-text="Gönder düğmesi vurgulanmış şekilde Postman isteği.":::

Her şey iyi sorun olursa, şimdi 202 durum kodu olması gereken ACS 'den yanıt görmeniz gerekir:

:::image type="content" source="media/postman/postman-202.png" alt-text="Bir Postman isteği, 202 durum koduyla başarıyla gönderildi.":::

"To" değerinde verdiğiniz sayının sahibi olan cep telefonu, SMS iletisi almış olmalıdır. Şimdi, ACS 'nin hizmetleriyle iletişim kurabilir ve SMS iletileri gönderebilen bir çalışan Postman ayarlamış olursunuz.


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [ACS API 'Lerini keşfet](/rest/api/communication/) 
>  [Kimlik doğrulaması](/rest/api/communication/authentication) 
>  hakkında daha fazla bilgi [Postman hakkında daha fazla bilgi edinin](https://learning.postman.com/)

Ayrıca şunları yapmak isteyebilirsiniz:

- [Uygulamanıza sohbet ekleme](../quickstarts/chat/get-started.md)
- [Kullanıcı erişim belirteçleri oluştur](../quickstarts/access-tokens.md)
- [İstemci ve sunucu mimarisi hakkında bilgi edinin](../concepts/client-and-server-architecture.md)
- [Kimlik doğrulaması hakkında bilgi edinin](../concepts/authentication.md)