---
title: 'Öğretici: Özel arama Web sayfası oluşturma-Bing Özel Arama'
titleSuffix: Azure Cognitive Services
description: Özel bir Bing arama örneğini yapılandırmayı ve bir Web sayfasıyla tümleştirmeyi öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: aee84a4d9203d80b81f773df30d68c3539e5ec7c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564701"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>Öğretici: Özel Arama web sayfası derleme

Bing Özel Arama API’si, önemsediğiniz konulara özel olarak uyarlanmış arama deneyimleri oluşturmanızı sağlar. Örneğin, arama deneyimi sunan bir dövüş sanatları web sitesine sahipseniz Bing’in aradığı etki alanlarını, alt siteleri ve web sayfalarını belirtebilirsiniz. Kullanıcılarınız, arama sonucu sayfalarındaki alakasız olabilecek içeriği ayıklamak zorunda kalmadan içeriğe göre oluşturulan arama sonuçlarını görebilir. 

Bu öğreticide özel arama örneği yapılandırma ve bunu yeni bir sayfasıyla tümleştirme adımları gösterilmektedir.

Ele alınan görevler şunlardır:

> [!div class="checklist"]
> - Özel arama örneği oluşturma
> - Etkin girişleri ekleme
> - Engellenen girişleri ekleme
> - Sabitlenmiş girişleri ekleme
> - Özel aramayı bir web sayfasıyla tümleştirme

## <a name="prerequisites"></a>Önkoşullar

- Öğreticiyi takip edebilmek için Bing Özel Arama API'si için bir abonelik anahtarına ihtiyacınız olacaktır.  Anahtar almak için bkz. [Bilişsel Hizmetleri Deneme](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Visual Studio 2017 veya sonraki bir sürümü yüklü değilse, **ücretsiz** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)' ı indirip kullanabilirsiniz.

## <a name="create-a-custom-search-instance"></a>Özel arama örneği oluşturma

Bing Özel Arama örneği oluşturmak için:

1. Bir internet tarayıcısı açın.  
  
2. Özel arama [portalına](https://customsearch.ai) gidin.  
  
3. Bir Microsoft hesabı (MSA) kullanarak portalda oturum açın. Hesabınız yoksa **Microsoft hesabı oluşturun**'a tıklayın. Portalı ilk kez kullanıyorsanız verilerinize erişim izni istenir. Yordamı tamamlamak için **Evet**yüklemesini desteklemesi gerekir.  
  
4. Oturum açtıktan sonra **Yeni özel arama**'ya tıklayın. **Yeni özel arama örneği oluştur** penceresinde kolay anlaşılır ve arama tarafından döndürülen içeriğin türüne uygun bir ad girin. Adı dilediğiniz zaman değiştirebilirsiniz.  
  
   ![Yeni özel arama örneği oluştur kutusunun ekran görüntüsü](../media/newCustomSrch.png)  
  
5. Tamam'a tıklayın, bir URL belirtin ve URL'nin alt sayfalarının dahil edilip edilmeyeceğini seçin.  
  
   ![URL tanımlama sayfasının ekran görüntüsü](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Etkin girişleri ekleme

Sonuçlara dahil etmek istediğiniz web sitelerini veya URL'leri **Etkin** sekmesine girin.

1.  **Yapılandırma** sayfasında **Etkin** sekmesine tıklayın ve aramanıza dahil etmek istediğiniz bir veya daha fazla web sitesinin URL'sini girin.

    ![Tanım Düzenleyicisi etkin sekmesinin ekran görüntüsü](../media/customSrchEditor.png)

2.  Örneğinizin sonuç döndürdüğünü doğrulamak için sağ taraftaki önizleme bölmesine bir sorgu girin. Bing yalnızca dizine eklenmiş genel web sitelerinden sonuç döndürür.

## <a name="add-blocked-entries"></a>Engellenen girişleri ekleme

Sonuçlardan hariç tutmak istediğiniz web sitelerini veya URL'leri **Engellendi** sekmesine girin.

1. **Yapılandırma** sayfasında **Engellendi** sekmesine tıklayın ve aramanızdan hariç tutmak istediğiniz bir veya daha fazla web sitesinin URL'sini girin.

    ![Tanım Düzenleyicisi engellendi sekmesinin ekran görüntüsü](../media/blockedCustomSrch.png)


2. Örneğinizin engellenen web sitelerinden sonuç döndürmediğini doğrulamak için sağ taraftaki önizleme bölmesine bir sorgu girin. 

## <a name="add-pinned-entries"></a>Sabitlenmiş girişleri ekleme

Belirli bir web sayfasını arama sonuçlarının en üstüne sabitlemek için ilgili web sayfasını ve sorgu terimini **Sabitlendi** sekmesine ekleyin. **Sabitlendi** sekmesinde belirli bir arama sonucu için ilk sırada görünen web sayfasını belirten web sayfası ve sorgu terimi çiftlerinin listesi bulunur. Web sayfası yalnızca kullanıcının sorgu dizesinin sabitleme eşleme koşuluna göre sabitlenen sorgu dizesiyle eşleşmesi durumunda sabitlenir. Aramalarda yalnızca dizine alınmış web sayfaları görüntülenir. Daha fazla bilgi için bkz. [Özel görünümünüzü tanımlama](../define-your-custom-view.md#pin-slices-to-the-top-of-search-results).

1. **Yapılandırma** sayfasında **Sabitlendi** sekmesine tıklayın ve ilk sırada döndürülmesini istediğiniz web sayfasını ve sorgu terimini girin.  
  
2. Varsayılan olarak Bing'in web sayfasını ilk sırada döndürmesi için kullanıcının sorgu dizesinin sabitlediğiniz girişin sorgu dizesiyle tam olarak eşleşmesi gerekir. Eşleşme koşulunu değiştirmek için sabitlenen girişi düzenleyin (kalem simgesine tıklayın), **Sorgu eşleme koşulu** sütununa tıklayın ve uygulamanıza uygun eşleme koşulunu seçin.  
  
    ![Tanım Düzenleyicisi sabitlendi sekmesinin ekran görüntüsü](../media/pinnedCustomSrch.png)
  
3. Örneğinizin belirtilen sayfanın sonuçlarını ilk sırada döndürdüğünü doğrulamak için sağ taraftaki önizleme bölmesine sabitlediğiniz sorguyu girin.

## <a name="configure-hosted-ui"></a>Barındırılan kullanıcı arabirimini yapılandırma

Özel Arama hizmeti, özel arama örneğinizin JSON yanıtını işlemek için kullanabileceğiniz barındırılan kullanıcı arabirimine sahiptir. Kullanıcı arabirimi deneyiminizi tanımlamak için:

1. **Barındırılan kullanıcı arabirimi** sekmesine tıklayın.  
  
2. Bir düzen seçin.  
  
   ![Barındırılan kullanıcı arabirimi düzen seçme adımı ekran görüntüsü](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Bir renk teması seçin.  
  
   ![Barındırılan kullanıcı arabirimi renk teması seçme adımı ekran görüntüsü](./media/custom-search-hosted-ui-select-color-theme.png)  

   Web uygulamanıza uygun hale getirmek için renk temasında ayarlama yapmanız gerekiyorsa **Temayı özelleştir**'e tıklayın. Tüm renk yapılandırmaları her düzen temasıyla kullanılamaz. Bir rengi değiştirmek için rengin RGB HEX değerini (örneğin, #366eb8) ilgili metin kutusuna girin. Veya renk düğmesine tıklayarak istediğiniz tonu seçin. Renkleri seçerken mutlaka erişilebilirlik özelliklerini göz önünde bulundurun.
  
   ![Barındırılan kullanıcı arabirimi renk teması özelleştirme adımı ekran görüntüsü](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Ek yapılandırma seçeneklerini belirtin.  
  
   ![Barındırılan kullanıcı arabirimi ek yapılandırma adımı](./media/custom-search-hosted-ui-additional-configurations.png)  
  
   Gelişmiş yapılandırmalara erişmek için **Gelişmiş yapılandırmaları göster**'e tıklayın. Bunu yaptığınızda Web arama seçeneklerine *Bağlantı hedefi*, Görüntü ve Video seçeneklerine *Filtreleri etkinleştir*, Diğer seçeneklere ise *Arama kutusu yer tutucu metni* gibi yapılandırma ayarları eklenir.

   ![Barındırılan kullanıcı arabirimi gelişmiş yapılandırma adımı](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. Açılan listelerden abonelik anahtarlarınızı seçin. İsterseniz abonelik anahtarını el ile de girebilirsiniz. Anahtarları alma hakkında bilgi için bkz. [Bilişsel Hizmetler'i deneyin](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).  
  
   ![Barındırılan kullanıcı arabirimi ek yapılandırma adımı](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Barındırılan kullanıcı arabirimini kullanma

Barındırılan kullanıcı arabirimini kullanmanın iki yolu vardır.  

- Seçenek 1: Sağlanmış JavaScript kod parçacığını uygulamanızla tümleştirin.
- Seçenek 2: Belirtilen HTML uç noktasını kullanın.

Bu öğreticinin geri kalanında 1 **seçeneği gösterilmektedir: JavaScript kod**parçacığı.  

## <a name="set-up-your-visual-studio-solution"></a>Visual Studio çözümünüzü kurma

1. Bilgisayarınızda **Visual Studio**'yu açın.  
  
2. **Dosya** menüsünde **Yeni**'yi seçin ve ardından **Proje**'yi seçin.  
  
3. **Yeni Proje** penceresinde **Visual C#/Web/ASP.NET Core Web Uygulaması**'nı seçin, projenize bir ad verin ve **Tamam**'a tıklayın.  
  
   ![Yeni proje penceresinin ekran görüntüsü](./media/custom-search-new-project.png)  
  
4. **Yeni ASP.NET Core Web Uygulaması** penceresinde **Web Uygulaması**'nı seçip **Tamam**'a tıklayın.  
  
   ![Yeni proje penceresinin ekran görüntüsü](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>index.cshtml dosyasını düzenleme

1. **Çözüm Gezgini**'nde **Sayfalar**'ı genişletin ve **index.cshtml** dosyasına çift tıklayarak açın.  
  
   ![Sayfalar genişletilmiş ve index.cshtml dosyası seçilmiş şekilde Çözüm Gezgini'nin ekran görüntüsü](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. index.cshtml dosyasında 7. satırdan başlayarak alt kısmı tamamen silin.  
  
   ```razor
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }    
   ```  
  
3. Satır sonu öğesi ve kapsayıcı olarak görev yapacak bir div ekleyin.  
  
   ```html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch"></div>
   ```  
  
4. **Barındırılan kullanıcı arabirimi** sayfasında **Kullanıcı arabirimini kullanma** bölümüne inin. JavaScript kod parçacığına erişmek için *Uç noktalar*'a tıklayın. Kod parçacığını **Üretim**'e ve ardından **Barındırılan kullanıcı arabirimi** sekmesine tıklayarak da alabilirsiniz.
  
   <!-- Get new screenshot after prod gets new bits
   ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
   -->
  
5. Betik öğesini eklediğiniz kapsayıcıya yapıştırın.  
  
   ``` html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch">
      <script type="text/javascript" 
          id="bcs_js_snippet"
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
   </div>
   ```  
  
6. **Çözüm Gezgini**'nde **wwwroot** öğesine sağ tıklayıp **Tarayıcıda Görüntüle**'ye tıklayın.  
  
   ![wwwroot bağlam menüsünden Tarayıcıda Görüntüle'nin seçilmesini gösteren Çözüm Gezgini ekran görüntüsü](./media/custom-search-webapp-view-in-browser.png)  

Yeni özel arama web sayfanız aşağıdakine benzer olmalıdır:

![Özel arama web sayfası ekran görüntüsü](./media/custom-search-webapp-browse-index.png)

Arama yaptığınızda aşağıdaki gibi sonuçlar alırsınız:

![Özel arama sonuçlarının ekran görüntüsü](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bing Özel Arama uç noktasını çağırma (C#)](../call-endpoint-csharp.md)
