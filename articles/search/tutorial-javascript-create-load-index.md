---
title: 'JavaScript öğreticisi: Web uygulamalarına arama ekleme'
titleSuffix: Azure Cognitive Search
description: NPM SDK 'sını kullanarak dizin oluşturma ve CSV verilerini JavaScript ile arama dizinine aktarma @azure/search-documents .
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 0fd28262f4a4b852386fa354037e69c5097109c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726980"
---
# <a name="2---create-and-load-search-index-with-javascript"></a>2-JavaScript ile arama dizini oluşturma ve yükleme

Arama etkin Web sitenizi oluşturmaya devam edin:
* VS Code uzantısıyla bir arama kaynağı oluşturma
* Örnek betiği ve Azure SDK 'sını kullanarak yeni bir dizin oluşturma ve JavaScript ile veri aktarma [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) .

## <a name="create-an-azure-search-resource"></a>Azure Search kaynağı oluşturma 

Visual Studio Code için [Azure bilişsel arama](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) Uzantısı ile yeni bir arama kaynağı oluşturun.

1. Visual Studio Code, [etkinlik çubuğunu](https://code.visualstudio.com/docs/getstarted/userinterface)açın ve Azure simgesini seçin. 

1. Yan çubukta, alanı altında **Azure aboneliğinize sağ tıklayın** `Azure: Cognitive Search` ve **Yeni arama hizmeti oluştur**' u seçin.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="Yan çubukta, * * Azure: Bilişsel Arama * * alanı altında Azure aboneliğinize sağ tıklayın ve * * yeni arama hizmeti oluştur * * ' u seçin.":::

1. Aşağıdaki bilgileri sağlamak için istemleri izleyin:

    |İstem|Enter|
    |--|--|
    |Yeni Arama Hizmeti için genel olarak benzersiz bir ad girin.|**Bu adı unutmayın**. Bu kaynak adı, kaynak uç noktanızın bir parçası haline gelir.|
    |Yeni kaynaklar için bir kaynak grubu seçin|Bu öğretici için oluşturduğunuz kaynak grubunu kullanın.|
    |Arama hizmetiniz için SKU 'YU seçin.|Bu öğretici için **ücretsiz** ' i seçin. Hizmet oluşturulduktan sonra bir SKU fiyatlandırma katmanını değiştiremezsiniz.|
    |Yeni kaynaklar için bir konum seçin.|Size yakın bir bölge seçin.|

1. İstemleri tamamladıktan sonra yeni arama kaynağınız oluşturulur. 

## <a name="get-your-search-resource-admin-key"></a>Arama kaynağı yönetici anahtarınızı alın

Visual Studio Code uzantılı arama kaynağı yönetici anahtarınızı alın. 

1. Visual Studio Code, yan çubukta, arama kaynağınız üzerinde sağ tıklayın ve **yönetici anahtarını Kopyala**' yı seçin.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="Yan çubukta, arama kaynağına sağ tıklayın ve * * yönetici anahtarını Kopyala * * ' yı seçin.":::

1. Bu yönetici anahtarını sakla, [daha sonraki bir bölümde](#prepare-the-bulk-import-script-for-search)kullanmanız gerekecektir. 

## <a name="prepare-the-bulk-import-script-for-search"></a>Toplu içeri aktarma betiğini arama için hazırlama

Betik Bilişsel Arama için Azure SDK 'sını kullanır:

* [NPM paketi @azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* [Başvuru Belgeleri](/javascript/api/overview/azure/search-documents-readme)

1. Visual Studio Code, `bulk_insert_books.js` dosyayı alt dizininde açın,  `search-web/bulk-insert` Azure Search SDK ile kimlik doğrulamak için aşağıdaki değişkenleri kendi değerlerinizle değiştirin:

    * ARAMA-KAYNAK ADıNıZ
    * ARAMA YÖNETICI ANAHTARıNıZ

    :::code language="javascript" source="~/azure-search-javascript-samples/search-website/bulk-insert/bulk_insert_books.js" highlight="16,17" :::

1. Visual Studio 'da proje dizininin alt dizini için tümleşik bir Terminal açın `search-web/bulk-insert` ve bağımlılıklarını yüklemek için aşağıdaki komutu çalıştırın. 

    ```bash
    npm install 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>Arama için toplu içeri aktarma betiğini çalıştırma

1. `search-web/bulk-insert`Komut dosyasını çalıştırmak için aşağıdaki Bash komutunu çalıştırmak üzere, Visual Studio 'da proje dizininin alt dizini için tümleşik Terminal 'yi kullanmaya devam edin `bulk_insert_books.js` :

    ```javascript
    npm start
    ```

1. Kod çalışırken, konsol ilerlemeyi görüntüler. 
1. Karşıya yükleme tamamlandığında, konsola basılan son ifade "bitti" olur.

## <a name="review-the-new-search-index"></a>Yeni arama dizinini gözden geçirin

Karşıya yükleme tamamlandıktan sonra arama dizini kullanıma hazırdır. Yeni dizininizi gözden geçirin.

1. Visual Studio Code Azure Bilişsel Arama uzantısını açın ve arama kaynağınızı seçin.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="Visual Studio Code ' de Azure Bilişsel Arama uzantısını açın ve arama kaynağınızı açın.":::

1. Belgeye özgü tüm verileri görmek için dizinler ' i ve ardından belgeler ' i genişletin ve ardından `good-books` bir belge seçin.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="Dizinler ' i ve ardından ' iyi kitaplar ' ' ı genişletin, ardından bir belge seçin.":::

## <a name="copy-your-search-resource-name"></a>Arama kaynağı adınızı kopyalayın

**Arama kaynağı adınızı** aklınızda edin. Azure Işlev uygulamasını arama kaynağına bağlamak için buna ihtiyacınız olacak. 

> [!CAUTION]
> Azure Işlevinde arama yönetici anahtarınızı kullanmayı düşünebilirsiniz, ancak en az ayrıcalık ilkesini takip edebilirsiniz. Azure Işlevi, en az ayrıcalığa uyum sağlamak için sorgu anahtarını kullanır. 

## <a name="next-steps"></a>Sonraki adımlar

[Statik Web uygulamanızı dağıtma](tutorial-javascript-deploy-static-web-app.md)