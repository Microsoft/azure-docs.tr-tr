---
title: Node.JS ile video dosyalarını kodlama ve akışa alma
description: Node.JS ile video dosyalarını akışa alma. Bu öğreticinin adımlarını izleyerek yeni bir Azure Media Services hesabı oluşturun, bir dosya kodlayın ve Azure Media Player için akışını yapın.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Azure Media Services, Stream, Node.JS
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: 5bb061af37f6f6d7e6e27cf25f0faa63bca7353c
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109195"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Node.JS ile video dosyalarını kodlama ve akışa alma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu hızlı başlangıçta, Azure Media Services kullanarak çok çeşitli tarayıcı ve cihazda videoları kodlamanın akışa almaya başlamanın ne kadar kolay olduğu size gösterilmektedir. Bir giriş video dosyası, HTTPS URL 'Leri, SAS URL 'Leri veya Azure Blob depolamada bulunan dosyaların yolları kullanılarak belirtilebilir.

Bu hızlı başlangıç sonunda şunları bilirsiniz:

- Node.JS ile kodlama
- Node.JS ile akış yapma
- Node.JS ile HTTPS URL 'sinden dosya yükleme
- Node.JS ile HLS veya DASH istemci oynatıcı kullanma

Bu makaledeki örnek, bir HTTPS URL 'SI aracılığıyla erişilebilir hale getirmek istediğiniz içeriği kodluyor. AMS v3’ün şu anda HTTPS URL'leri üzerinden yığın halinde aktarım kodlamasını desteklemediğini unutmayın.

![Videoyu yürütme](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

- [Node.js](https://nodejs.org/en/download/) yüklensin
- [Media Services hesabı oluşturun](./create-account-howto.md).<br/>Kaynak grubu adı ve Media Services hesap adı için kullandığınız değerleri anımsadığınızdan emin olun.
- [Azure CLI Ile Access Azure Media Services API 'sindeki](./access-api-howto.md) adımları izleyin ve kimlik bilgilerini kaydedin. API 'ye erişmek için bunları kullanmanız gerekir.
- Node.js istemci SDK 'sını nasıl kullanacağınızı anlamak için [Node.jsIle yapılandırma ve bağlanma ](./configure-connect-nodejs-howto.md) hakkında bilgi edinin

## <a name="download-and-configure-the-sample"></a>Örneği indirin ve yapılandırın

Aşağıdaki komutu kullanarak, akış Node.js örneğini içeren bir GitHub deposunu makinenize kopyalayın:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Örnek, [Streamfilesörnek](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) klasöründe bulunur.

İndirilen projenizde [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) 'yi açın. Kök klasördeki *Sample. env* dosyasını, [API](./access-api-howto.md)'larından aldığınız değerler ve kimlik bilgileriyle güncelleştirin. *Sample. env* dosyasını *. env* (Evet, yalnızca uzantı) olarak yeniden adlandırın.

Örnek aşağıdaki eylemleri gerçekleştirir:

1. [Içerik duyarlı kodlama önceden ayarlanmış](./encode-content-aware-concept.md)olarak bir **dönüşüm** oluşturur. Önce belirtilen dönüştürmenin var olup olmadığını denetler.
1. Çıktıyı içeren kodlama **işi** tarafından kullanılan bir çıktı **varlığı** oluşturur
1. İsteğe bağlı olarak, Depolama Blobu SDK 'sını kullanarak yerel bir dosyayı karşıya yükler
1. Bir HTTPS URL 'sini veya karşıya yüklenen dosyayı temel alan **iş** girişini oluşturur
1. Daha önce oluşturulmuş giriş ve çıktıyı kullanarak kodlama **işini** gönderir
1. Işin durumunu denetler
1. Kodlama işinin çıkışını yerel bir klasöre indirir
1. Player 'da kullanmak için bir **akış Bulucu** oluşturur
1. HLS ve DASH için akış URL 'Leri oluşturur
1. İçeriği bir oynatıcı uygulamasında geri çalar-Azure Media Player

## <a name="run-the-sample"></a>Örneği çalıştırma

1. Uygulama kodlanmış dosyaları indirir. Çıkış dosyalarının gitmesini istediğiniz klasörü oluşturun ve [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/StreamFilesSample/index.ts#L59) dosyasındaki **outputfolder** değişkeninin değerini güncelleştirin. Varsayılan olarak "Temp" olarak ayarlanır.
1. Bir **komut istemi** açın, örnek dizinine gidin.
1. Dizini AMSv3Samples klasörü olarak değiştirin.

    ```bash
    cd AMSv3Samples
    ```

1. *packages.jsdosya üzerinde* kullanılan paketleri yükler.

    ```bash
    npm install 
    ```

1. Dizini *Streamfilesörnek* klasörüyle değiştirin.

    ```bash
    cd StreamFilesSample
    ```

1. *AMSv3Samples* klasöründen Visual Studio Code başlatın. (Bu, *. vscode* klasörünün ve dosyaların *tsconfig.js* bulunduğu klasörden başlatmak için gereklidir.)

    ```bash
    cd ..
    code .
    ```

*Streamfilessample* klasörünü açın ve Visual Studio Code düzenleyicisinde *index. TS* dosyasını açın.
*İndex. TS* dosyasında, hata ayıklayıcıyı başlatmak için F5 tuşuna basın.

## <a name="test-with-azure-media-player"></a>Azure Media Player ile test etme

Akışı test etmek için Azure Media Player kullanın. Ayrıca, Shaka oynatıcı, HLS.js, Dash.js veya diğerleri gibi her türlü HLS veya DASH uyumlu oynatıcı da kullanabilirsiniz.

Örnekte oluşturulan bağlantıya tıklayabilir ve daha önce yüklü olan DASH bildirimiyle AMP oynatıcı 'yı başlatabilirsiniz.

> [!NOTE]
> Oynatıcı bir https sitesinde barındırılıyorsa, "https" URL’sini güncelleştirdiğinizden emin olun.

1. Bir Web tarayıcısı açın ve adresine gidin [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. **URL:** kutusunda, uygulamayı çalıştırdığınızda ALDıĞıNıZ akış URL değerlerinden birini yapıştırın. URL 'yi HLS, Dash veya kesintisiz biçimde yapıştırabilir ve Azure Media Player cihazınızda otomatik olarak kayıttan yürütmek için uygun bir akış protokolüne geçiş yapar.
3. **Oynatıcıyı Güncelleştir** düğmesine basın.

Azure Media Player, test için kullanılabilir, ancak üretim ortamında kullanılmamalıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunuzda, bu öğretici için oluşturduğunuz Media Services ve depolama hesapları da dahil olmak üzere herhangi bir kaynağa ihtiyacınız yoksa, kaynak grubunu silin.

Aşağıdaki CLı komutunu yürütün:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Azure 'da Node.js için daha fazla geliştirici belgesi

- [JavaScript & için Azure Node.js geliştiriciler](/azure/developer/javascript/)
- [Git hub deposunda Media Services kaynak kodu @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Node.js geliştiricileri için Azure paket belgeleri](/javascript/api/overview/azure/)

## <a name="see-also"></a>Ayrıca bkz.

- [İş hata kodları](/rest/api/media/jobs/get#joberrorcode).
- [NPM yüklemesi @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [JavaScript & için Azure Node.js geliştiriciler](/azure/developer/javascript/)
- [Depodaki Media Services kaynak kodu @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Sonraki adımlar

> [Media Services kavramlar](concepts-overview.md)
