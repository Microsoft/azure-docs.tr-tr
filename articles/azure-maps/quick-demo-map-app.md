---
title: 'Hızlı başlangıç: Azure Maps ile etkileşimli harita arama'
description: Azure hızlı başlangıç-Azure haritaları kullanarak bir demo etkileşimli harita araması oluşturma
author: walsehgal
ms.author: v-musehg
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1b9d8c98391e7e2bac3492dcf696f098f5c6cf57
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903172"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Hızlı başlangıç: Azure haritalar kullanarak etkileşimli arama haritası oluşturma

Bu makalede, Azure Haritalar’ın kullanıcılara etkileşimli arama deneyimi sunan bir harita oluşturmaya yönelik özellikler gösterilmektedir. Bu temel adımlarda size kılavuzluk eder:
* Kendi Azure haritalar hesabınızı oluşturun.
* Tanıtım Web uygulamasında kullanılacak hesap anahtarınızı alın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-an-account-and-get-your-key"></a>Hesap oluşturma ve anahtarınızı alma

1. [Azure Portal](https://portal.azure.com) **kaynak oluştur**' u seçin.
2. Market 'te **Ara** kutusuna **haritalar**girin.
3. **Sonuçlar** içinden **Haritalar**’ı seçin. Haritanın altında görüntülenen **Oluştur** düğmesini seçin.
4. **Azure haritalar hesabı oluştur** sayfasında, aşağıdaki değerleri girin:
   - Yeni hesabınıza verilen **Ad**.
   - Bu hesap için kullanmak istediğiniz **Abonelik**.
   - Bu hesap için **Kaynak grubu**. Yeni bir kaynak grubu **oluşturmayı** veya **var olan** kaynak grubunu kullanmayı tercih edebilirsiniz.
   - Tercih ettiğiniz **fiyatlandırma katmanını** seçin.
   - **Lisans** ve **Gizlilik bildirimini**okuyun. Koşulları kabul etmek için onay kutusunu işaretleyin.
   - Son olarak **Oluştur** düğmesini seçin.

     ![Portalda Azure Maps hesabı oluşturma](./media/quick-demo-map-app/create-account.png)

5. Hesabınız başarıyla oluşturulduktan sonra, hesabı açın ve hesap menüsünün Ayarlar bölümünü bulun. Azure haritalar hesabınız için birincil ve ikincil anahtarları görüntülemek üzere **kimlik doğrulaması** ' nı seçin. **Birincil Anahtar** değerini sonraki bölümde kullanmak üzere yerel panonuza kopyalayın.

## <a name="download-the-application"></a>Uygulamayı indirme

1. [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) dosyasının içeriklerini indirin veya kopyalayın.
2. Bu dosyanın içeriğini **AzureMapDemo. html**olarak yerel olarak kaydedin. Bir metin düzenleyicisinde açın.
3. `<Your Azure Maps Key>`dize için arama yapın. Önceki bölümdeki **birincil anahtar** değeriyle değiştirin.

## <a name="open-the-application"></a>Uygulamayı açma

1. **AzureMapDemo.html** dosyasını istediğiniz bir tarayıcıda açın.
2. Los Angeles şehrinde gösterilen Haritayı gözlemleyin. Yakınlaştırma düzeyine bağlı olarak haritanın daha fazla veya daha az bilgiyle nasıl işlendiğini görmek için yakınlaştırma ve uzaklaştırma yapın. 
3. Haritanın varsayılan merkezini değiştirin. **AzureMapDemo.html** dosyasında, **center** adlı değişkeni arayın. Bu değişkenin boylam ve enlem çiftini, yeni **[-74.0060, 40.7128]** değerleriyle değiştirin. Dosyayı kaydedin ve tarayıcınızı yenileyin.
4. Etkileşimli arama deneyimini deneyin. Demo Web uygulamasının sol üst köşesindeki arama kutusunda **Restoran**' ı arayın.
5. Farenizi arama kutusunun altında görüntülenen adresler ve konumlar listesinin üzerine taşıyın. Haritadaki karşılık gelen PIN 'in bu konumla ilgili bilgileri nasıl göründüğüne dikkat edin. Özel işletmelerin gizliliğini korumak için kurgusal ad ve adresler gösterilir.

    ![Etkileşimli arama Web uygulaması](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiler, Azure haritalar 'ı hesabınızla nasıl kullanacağınızı ve yapılandıracağınızı ayrıntılandırır. Öğreticilere devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, kaynakları temizlemek için şu adımları uygulayın:

1. **AzureMapDemo. html** Web uygulamasını çalıştıran tarayıcıyı kapatın.
2. Azure portal sol menüden **tüm kaynaklar**' ı seçin. Ardından Azure haritalar hesabınızı seçin. **Tüm kaynaklar** dikey penceresinin en üstünde **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure Maps hesabınızı oluşturdunuz ve bir demo uygulaması oluşturdunuz. Azure haritalar hakkında bilgi edinmek için aşağıdaki öğreticilere göz atın:

> [!div class="nextstepaction"]
> [Azure haritalar 'ı kullanarak yakın ilgi çekici noktaları arayın](tutorial-search-location.md)

Daha fazla kod örneği ve etkileşimli kodlama deneyimi için aşağıdaki kılavuzlara bakın:

> [!div class="nextstepaction"]
> [Azure haritalar arama hizmetini kullanarak bir adres bulma](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Azure haritalar 'ı kullanın Harita Denetimi](how-to-use-map-control.md)
