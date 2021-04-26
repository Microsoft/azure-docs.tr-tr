---
title: 'JavaScript öğreticisi: arama etkin Web sitesi dağıtma'
titleSuffix: Azure Cognitive Search
description: Arama etkin Web sitesini Azure statik Web uygulamasına dağıtın.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726959"
---
# <a name="3---deploy-the-search-enabled-website"></a>3-arama etkin Web sitesini dağıtma

Arama etkin Web sitesini Azure statik Web uygulaması olarak dağıtın. Bu dağıtım hem tepki verme uygulaması hem de Işlev uygulamasını içerir.  

Statik Web uygulaması, örnek deposunun çatalınızı kullanarak GitHub 'dan dağıtım için bilgileri ve dosyaları çeker.  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Visual Studio Code içinde statik bir Web uygulaması oluşturma

1. Etkinlik çubuğundan **Azure** ' u seçin, sonra yan çubuktan **statik Web Apps** ' yi seçin. 
1. Abonelik adına sağ tıklayın ve ardından **statik Web uygulaması oluştur (Gelişmiş)** öğesini seçin.    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="Abonelik adına sağ tıklayın ve ardından * * statik Web uygulaması oluştur (Gelişmiş) * * seçeneğini belirleyin.":::

1. Aşağıdaki bilgileri sağlamak için istemleri izleyin:

    |İstem|Enter|
    |--|--|
    |Statik bir Web uygulamasını nasıl oluşturmak istiyorsunuz?|Mevcut GitHub deposunu kullan|
    |Kuruluş seçin|Kuruluş olarak _kendi_ GitHub diğer adınızı seçin.|
    |Depo seçin|Listeden **Azure-Search-JavaScript-Samples** öğesini seçin. |
    |Deponun dalını seçin|Listeden **ana öğe** ' yi seçin. |
    |Yeni statik Web uygulamasının adını girin.|Kaynağınız için benzersiz bir ad oluşturun. Örneğin, adınızı, gibi depo adına ekleyebilirsiniz `joansmith-azure-search-javascript-samples` . |
    |Yeni kaynaklar için bir kaynak grubu seçin.|Bu öğretici için oluşturduğunuz kaynak grubunu kullanın.|
    |Varsayılan proje yapısını yapılandırmak için derleme ön ayarı ' nı seçin.|**Özel** ' i seçin|
    |Uygulama kodunuzun konumunu seçin|`search-website`|
    |Azure Işlev kodunuzun konumunu seçin|`search-website/api`|
    |Yapı çıkışlarınızın yolunu girin...|derleme|
    |Yeni kaynaklar için bir konum seçin.|Size yakın bir bölge seçin.|

1. Kaynak oluşturulur, **bildirimlerdeki GitHub 'Da eylemleri aç** ' ı seçin. Bu, kendi deponuza işaret eden bir tarayıcı penceresi açar. 

    Eylem listesi, Web uygulamanızın hem istemci hem de işlevlerin Azure statik Web uygulamanıza başarıyla itildiğini gösterir. 

    Devam etmeden önce derleme ve dağıtım tamamlanana kadar bekleyin. Bu işlem bir dakika veya iki dakika sürebilir.

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Visual Studio Code Bilişsel Arama sorgu anahtarı al

1. Visual Studio Code, [etkinlik çubuğunu](https://code.visualstudio.com/docs/getstarted/userinterface)açın ve Azure simgesini seçin. 

1. Yan çubukta **Azure: bilişsel arama** alanı altında Azure aboneliğinizi seçin, sonra arama kaynağına sağ tıklayıp **sorgu anahtarını Kopyala**' yı seçin. 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="Yan çubukta, * * Azure: Bilişsel Arama * * alanı altında Azure aboneliğinizi seçin, sonra arama kaynağınız üzerinde sağ tıklayın ve * * sorgu anahtarını Kopyala * * seçeneğini belirleyin.":::

1. Bu sorgu anahtarını sakla, sonraki bölümde kullanmanız gerekecektir. Sorgu anahtarı dizininizi sorgulayabilir. 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Visual Studio Code yapılandırma ayarları ekleme

Azure Işlevi uygulaması, arama gizli dizileri ayarlarından çıkana kadar arama verileri döndürmez. 

1. Etkinlik çubuğundan **Azure** ' u seçin, sonra yan çubuktan **statik Web Apps** ' yi seçin. 
1. **Uygulama ayarları** görüntülenene kadar yeni statik Web uygulamanızı genişletin.
1. **Uygulama ayarları**' na sağ tıklayın ve ardından **yeni ayar Ekle**' yi seçin.

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="* * Uygulama ayarları * * öğesine sağ tıklayın ve ardından * * yeni ayar Ekle * * seçeneğini belirleyin.":::

1. Aşağıdaki ayarları ekleyin:

    |Ayar|Arama kaynak değeri|
    |--|--|
    |SearchApiKey|Arama sorgu anahtarınız|
    |SearchServiceName|Arama kaynak adınız|
    |Searchındexname|`good-books`|
    |Searchmodel|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>Statik Web uygulamanızda arama kullanın

1. Visual Studio Code, [etkinlik çubuğunu](https://code.visualstudio.com/docs/getstarted/userinterface)açın ve Azure simgesini seçin.
1. Yan çubukta, alanı altında **Azure aboneliğinize sağ tıklayın** `Static web apps` ve bu öğretici için oluşturduğunuz statik Web uygulamasını bulun.
1. Statik Web uygulaması adına sağ tıklayın ve **siteye gözatamıyorum**' ı seçin.
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="Statik Web uygulaması adına sağ tıklayın ve * * siteye gözatıp * * seçeneğini belirleyin.":::    

1. Açılır iletişim kutusunda **Aç** ' ı seçin.
1. Web sitesi arama çubuğunda, `code` öner özelliğinin kitap başlıklarını önereceği şekilde _yavaş_ bir arama sorgusu girin. Bir öneri seçin veya kendi sorgunuzu girmeye devam edin. Arama sorgunuzu tamamladığınızda ENTER tuşuna basın. 
1. Daha fazla ayrıntı görmek için sonuçları gözden geçirin ve ardından kitaplardan birini seçin. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturulan kaynakları temizlemek için kaynak grubunu silin.

1. Visual Studio Code, [etkinlik çubuğunu](https://code.visualstudio.com/docs/getstarted/userinterface)açın ve Azure simgesini seçin. 

1. Yan çubukta, alanı altında **Azure aboneliğinize sağ tıklayın** `Resource Groups` ve bu öğretici için oluşturduğunuz kaynak grubunu bulun.
1. Kaynak grubu adına sağ tıklayın ve ardından **Sil**' i seçin.
    Bu, hem arama hem de statik Web uygulaması kaynaklarını siler.
1. Örneğin GitHub çatalını artık istemiyorsanız GitHub ' da silmeyi unutmayın. Çatalınızın **ayarlarına** gidin ve çatalı silin. 


## <a name="next-steps"></a>Sonraki adımlar

* [Arama etkin Web sitesi için arama tümleştirmesini anlama](tutorial-javascript-search-query-integration.md)
