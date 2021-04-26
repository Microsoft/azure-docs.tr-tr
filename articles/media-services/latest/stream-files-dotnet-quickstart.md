---
title: Azure Media Services .NET ile video dosyaları akışı
description: Yeni bir Azure Media Services hesabı oluşturmak, bir dosya kodlamak ve Azure Media Player göndermek için .NET kullanmak üzere Bu öğreticinin adımlarını izleyin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, akış
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: e7c35e079a6f4bf6c40992debace60e10631a8ba
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962060"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>Öğretici: URL 'yi temel alarak uzak bir dosyayı kodlayın ve videoyu akışa sunun-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu öğreticide, Azure Media Services kullanarak çok çeşitli tarayıcılarda ve cihazlarda akış videolarının kodlanması ve başlatılması ne kadar kolay olduğunu gösterir. Azure Blob depolamada bulunan dosyaların yolları, SAS URL’leri veya HTTPS URL’leri kullanılarak girdi içeriği belirtilebilir.
Bu konu başlığındaki örnek, bir HTTPS URL’si aracılığıyla erişilebilir hale getirdiğiniz içerikleri kodlar. AMS v3’ün şu anda HTTPS URL'leri üzerinden yığın halinde aktarım kodlamasını desteklemediğini unutmayın.

Öğreticinin sonuna kadar video akışını sağlayabileceksiniz.  

![Videoyu yürütme](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

- Visual Studio yüklü değilse, [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)’yi edinebilirsiniz.
- [Media Services hesabı oluşturun](./account-create-how-to.md).<br/>Kaynak grubu adı ve Media Services hesap adı için kullandığınız değerleri anımsadığınızdan emin olun.
- [Azure CLI Ile Access Azure Media Services API 'sindeki](./access-api-howto.md) adımları izleyin ve kimlik bilgilerini kaydedin. API 'ye erişmek için bunları kullanmanız gerekir.

## <a name="download-and-configure-the-sample"></a>Örneği indirin ve yapılandırın

Aşağıdaki komutu kullanarak, akış .NET örneğini içeren bir GitHub havuzunu makinenize kopyalayın:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

Örnek [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles) klasöründe bulunur.

İndirilen projenizde [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) açın. Değerleri, [API 'lere eriştiğiniz](./access-api-howto.md)kimlik bilgileriyle değiştirin.

Örnek aşağıdaki eylemleri gerçekleştirir:

1. Bir **dönüşüm** oluşturur (ilk olarak, belirtilen dönüştürmenin var olup olmadığını denetler). 
2. Kodlama **işinin** çıkışı olarak kullanılan bir çıktı **varlığı** oluşturur.
3. Bir HTTPS URL 'sini temel alan, **işin** girişini oluşturur.
4. Daha önce oluşturulmuş giriş ve çıktıyı kullanarak kodlama **işini** gönderir.
5. İşin durumunu denetler.
6. Bir **akış Bulucu** oluşturur.
7. Akış URL'leri oluşturur.

Örnekteki her bir işlevin ne yaptığına dair açıklamalar için kodu inceleyin ve [bu kaynak dosyadaki](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) açıklamalara bakın.

## <a name="run-the-sample-app"></a>Örnek uygulamayı çalıştırma

Uygulamayı çalıştırdığınızda, farklı protokolleri kullanarak videoyu kayıttan yürütmek için kullanılabilen URL’ler görüntülenir. 

1. *EncodeAndStreamFiles* uygulamasını çalıştırmak için Ctrl+F5 tuşlarına basın.
2. Apple’ın **HLS** protokolünü (*manifest(format=m3u8-aapl)* ile biter) seçin ve konsoldan akış URL’sini kopyalayın.

![Visual Studio 'da, Azure Media Player kullanımı için üç akış URL 'Si gösteren EncodeAndStreamFiles uygulamasının çıktısının ekran görüntüsü.](./media/stream-files-tutorial-with-api/output.png)

Örneğin [kaynak kodunda](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs), URL’nin nasıl oluşturulduğunu görebilirsiniz. Bunu derlemek için, akış uç noktasının ana bilgisayar adını ve akış bulucu yolunu birleştirmeniz gerekir.  

## <a name="test-with-azure-media-player"></a>Azure Media Player ile test etme

Bu makalede, akışı test etmek için Azure Media Player kullanılmaktadır. 

> [!NOTE]
> Oynatıcı bir https sitesinde barındırılıyorsa, "https" URL’sini güncelleştirdiğinizden emin olun.

1. Bir Web tarayıcısı açın ve adresine gidin [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. **URL:** kutusuna, uygulamayı çalıştırdığınızda aldığınız akış URL değerlerinden birini yapıştırın. 
 
     URL 'yi HLS, Dash veya kesintisiz biçimde yapıştırabilir ve Azure Media Player cihazınızda otomatik olarak kayıttan yürütmek için uygun bir akış protokolüne geçiş yapar.
3. **Oynatıcıyı Güncelleştir** düğmesine basın.

Azure Media Player, test için kullanılabilir, ancak üretim ortamında kullanılmamalıdır. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunuzda, bu öğretici için oluşturduğunuz Media Services ve depolama hesapları da dahil olmak üzere herhangi bir kaynağa ihtiyacınız yoksa, kaynak grubunu silin.

Aşağıdaki CLı komutunu yürütün:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Kodu inceleme

Örnekteki her bir işlevin ne yaptığına dair açıklamalar için kodu inceleyin ve [bu kaynak dosyadaki](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) açıklamalara bakın.

[Dosyaları karşıya yükleme, kodlama ve akışa alma](stream-files-tutorial-with-api.md) öğreticisi size ayrıntılı açıklamaları içeren daha gelişmiş bir akışa alma örneği sunar. 

### <a name="job-error-codes"></a>İş hata kodları

Bkz. [hata kodları](/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Çoklu iş parçacığı kullanımı

Azure Media Services v3 SDK’ları, iş parçacığı güvenli değildir. Çok iş parçacıklı uygulama ile çalışırken, iş parçacığı başına yeni bir AzureMediaServicesClient nesnesi oluşturmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: dosyaları karşıya yükleme, kodlama ve akışa alma](stream-files-tutorial-with-api.md)
