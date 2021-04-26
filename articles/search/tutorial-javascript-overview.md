---
title: 'JavaScript öğreticisi: arama tümleştirmesine genel bakış'
titleSuffix: Azure Cognitive Search
description: Bir Web sitesine arama eklemek ve Azure statik Web uygulamasına dağıtmak için teknik genel bakış ve kurulum.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 03192b8a84b78682b53bf3d47e7de7b65eb8bceb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726947"
---
# <a name="1---overview-of-adding-search-to-a-website"></a>1-Web sitesine arama eklemeye genel bakış

Bu öğretici, bir kitap kataloğunda arama yapmak için bir Web sitesi oluşturur ve Web sitesini bir Azure statik Web uygulamasına dağıtır. 

Uygulama kullanılabilir: 
* [Örnek](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [Demo web sitesi-aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>Örnek ne yapar? 

Bu örnek Web sitesi 10.000 kitapları kataloğuna erişim sağlar. Kullanıcı, arama çubuğuna metin girerek katalogda arama yapabilir. Kullanıcı metin girdiğinde, Web sitesi, metni tamamlamaya yönelik arama dizini önerme özelliğini kullanır. Sorgu tamamlandığında, kitap listesi ayrıntıların bir bölümüyle birlikte görüntülenir. Kullanıcı, kitabın arama dizininde depolanan tüm ayrıntıları görmek için bir kitabı seçebilir. 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="Bu örnek Web sitesi 10.000 kitapları kataloğuna erişim sağlar. Kullanıcı, arama çubuğuna metin girerek katalogda arama yapabilir. Kullanıcı metin girdiğinde, Web sitesi, metni tamamlamaya yönelik arama dizini önerme özelliğini kullanır. Arama tamamlandığında, kitap listesi ayrıntıların bir bölümüyle birlikte görüntülenir. Kullanıcı, kitabın arama dizininde depolanan tüm ayrıntıları görmek için bir kitabı seçebilir.":::

Arama deneyimi şunları içerir: 

* Arama – uygulama için arama işlevi sağlar.
* Öner: Kullanıcı arama çubuğuna yazarken öneriler sağlar.
* Belge arama – Ayrıntılar sayfasının tüm içeriğini almak için KIMLIĞE göre bir belge arar.

## <a name="how-is-the-sample-organized"></a>Örnek nasıl düzenlenir?

[Örnek](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website) şunları içerir:

|Uygulama|Amaç|GitHub<br>Depo<br>Konum|
|--|--|--|
|İstemci|Arama ile kitapları göstermek için uygulama (sunu katmanı) tepki verir. Azure Işlevi uygulamasını çağırır. |[/Search-website/src](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Sunucu|Azure Işlevi uygulaması (iş katmanı)-JavaScript SDK kullanarak Azure Bilişsel Arama API 'sini çağırır |[/Search-website/API](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Toplu ekleme|Dizini oluşturmak ve belgeye belge eklemek için JavaScript dosyası.|[/Search-website/bulk-INSERT](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>Geliştirme ortamınızı kurma

Yerel geliştirme ortamınız için aşağıdakileri yükler. 

- [Node.js 12 veya 14](https://nodejs.org/en/download)
    - Yerel bilgisayarınızda yüklü Node.js farklı bir sürümüne sahipseniz, [düğüm sürüm Yöneticisi 'ni](https://github.com/nvm-sh/nvm) (NVM) veya bir Docker kapsayıcısını kullanmayı düşünün.  
- [Git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/) ve aşağıdaki uzantılar
    - [Azure Kaynakları](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups)
    - [Azure Bilişsel Arama 0.2.0 +](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Azure statik Web uygulaması](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- İsteğe bağlı:
    - Bu öğretici Azure Function API 'sini yerel olarak çalıştırmaz, ancak yerel olarak çalıştırmayı düşünüyorsanız, aşağıdaki Bash komutuyla [Azure-Functions-Core-Tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash) ' u genel olarak yüklemeniz gerekir: 
    
    ```bash
    npm install -g azure-functions-core-tools
    ```

## <a name="fork-and-clone-the-search-sample-with-git"></a>Git ile arama örneğini çatalla ve kopyalama

Örnek deponun kullanılması, statik Web uygulamasını dağıtmak için kritik öneme sahiptir. Web Apps, kendi GitHub çatalınızın konumunu temel alarak derleme eylemlerini ve dağıtım içeriğini belirlenir. Statik Web uygulamasındaki kod yürütme, Azure statik Web Apps, kendi oluşturduğunuz örnekteki koddan okurken uzak bir uygulamadır.

1. GitHub 'da [örnek depoyu](https://github.com/Azure-Samples/azure-search-javascript-samples)çatalla. 

    GitHub hesabınızla Web tarayıcınızda çatal işlemini doldurun. Bu öğretici, Azure statik Web uygulamasına Dağıtım kapsamında çatalınızı kullanır. 

1. Bash terminalinde, örnek uygulamayı yerel bilgisayarınıza indirin. 

    `YOUR-GITHUB-ALIAS`GitHub diğer adınızla değiştirin. 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-javascript-samples
    ```

1. Visual Studio Code, kopyalanmış deponun yerel klasörünüzü açın. Kalan görevler, belirtilmediği takdirde Visual Studio Code tarafından gerçekleştirilir.

## <a name="create-a-resource-group-for-your-azure-resources"></a>Azure kaynaklarınız için bir kaynak grubu oluşturma

1. Visual Studio Code, [etkinlik çubuğunu](https://code.visualstudio.com/docs/getstarted/userinterface)açın ve Azure simgesini seçin. 
1. Yan çubukta, alanı altında **Azure aboneliğinize sağ tıklayın** `Resource Groups` ve **kaynak grubu oluştur**' u seçin.

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="Yan çubukta, * * ' kaynak grupları ' alanının altında bulunan Azure aboneliğinize sağ tıklayın ve * * kaynak grubu oluştur * * seçeneğini belirleyin.":::
1. Gibi bir kaynak grubu adı girin `cognitive-search-website-tutorial` . 
1. Size yakın bir konum seçin.
1. Bilişsel Arama ve statik Web uygulaması kaynaklarını oluşturduğunuzda, Öğreticinin ilerleyen kısımlarında bu kaynak grubunu kullanın. 

    Kaynak grubu oluşturmak, kaynakları yönetmek için bir mantıksal birim sağlar, bunları kullanmayı bitirdiğinizde bunları silme de dahil.

## <a name="next-steps"></a>Sonraki adımlar

* [Arama dizini oluşturma ve belgelerle yükleme](tutorial-javascript-create-load-index.md)
* [Statik Web uygulamanızı dağıtma](tutorial-javascript-deploy-static-web-app.md)
