---
title: 'Hızlı başlangıç: statik HTML Web uygulaması oluşturma'
description: İlk HTML Merhaba Dünya Azure App Service dakikalar içinde dağıtın. App Service dağıtmak için birçok yol olan git kullanarak dağıtın.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-html-uiex
ms.openlocfilehash: 0a33889277c9f6cc60f5e81aff520e27828a1d06
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379630"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Azure'da statik bir HTML web uygulaması oluşturma

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Bu hızlı başlangıçta, Azure App Service için temel bir HTML + CSS sitesinin nasıl dağıtılacağı gösterilmektedir. Bu hızlı başlangıcı [Cloud Shell](../cloud-shell/overview.md)'de tamamlayacaksınız ama bu komutları [Azure CLI](/cli/azure/install-azure-cli) ile yerel olarak da çalıştırabilirsiniz.

![Örnek uygulamanın giriş sayfası](media/quickstart-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Örneği indirme

Cloud Shell'de bir quickstart dizini oluşturun ve o dizine geçin.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Ardından, örnek uygulama deposunu quickstart dizininize kopyalamak için aşağıdaki komutu çalıştırın.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

Örnek kodu içeren dizine geçin ve `az webapp up` komutunu çalıştırın. Aşağıdaki komutta <app_name> kısmını benzersiz uygulama adıyla değiştirin. Statik içerik `--html` bayrağıyla belirtilir.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

`az webapp up` komutu şu eylemleri gerçekleştirir:

- Varsayılan kaynak grubunu oluşturur.

- Bir varsayılan uygulama hizmeti planı oluşturur.

- Belirtilen adla bir uygulama oluşturur.

- Dosyaları geçerli çalışma dizininden web uygulamasına [sıkıştırıp dağıtır](./deploy-zip.md).

Bu komutun çalıştırılması birkaç dakika sürebilir. Çalıştırıldığında, aşağıdaki örneğe benzer bilgiler görüntüler:

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

`resourceGroup` değerini not edin. [Kaynakları temizleme](#clean-up-resources) bölümünde ihtiyacınız olacak.

## <a name="browse-to-the-app"></a>Uygulamaya göz atma

Bir tarayıcıda uygulama URL 'sine gidin: `http://<app_name>.azurewebsites.net` .

Sayfa bir Azure App Service web uygulaması çalıştırıyor.

![Örnek uygulama ana sayfası](media/quickstart-html/hello-world-in-browser-az.png)

**Tebrikler!** App Service'e ilk HTML uygulamanızı dağıttınız.

## <a name="update-and-redeploy-the-app"></a>Uygulamayı güncelleştirme ve yeniden dağıtma

Cloud Shell'de, nano metin düzenleyicisini açmak için `nano index.html` yazın. Aşağıda gösterildiği gibi, `<h1>` başlığında "Azure App Service - Sample Static HTML Site" değerini "Azure App Service" ile değiştirin.

![Nano index.html](media/quickstart-html/nano-index-html.png)

Değişikliklerinizi kaydedin ve nanodan çıkın. Kaydetmek için `^O` ve çıkmak için `^X` komutunu kullanın.

Şimdi aynı `az webapp up` komutuyla uygulamayı yeniden dağıtacaksınız.

```bash
az webapp up --location westeurope --name <app_name> --html
```

Dağıtım tamamlandıktan sonra **Uygulamaya göz atma** adımında açılan tarayıcı penceresine dönüp sayfayı yenileyin.

![Güncelleştirilen örnek uygulama giriş sayfası](media/quickstart-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>Yeni Azure uygulamanızı yönetin

Oluşturduğunuz Web uygulamasını yönetmek için [Azure Portal](https://portal.azure.com), **uygulama hizmetleri**' ni arayıp seçin. 

![Azure portal uygulama hizmetleri 'ni seçin](./media/quickstart-html/portal0.png)

**Uygulama hizmetleri** sayfasında, Azure uygulamanızın adını seçin.

![Azure uygulamasına portal gezintisi](./media/quickstart-html/portal1.png)

Web uygulamanızın Genel Bakış sayfasını görürsünüz. Buradan göz atma, durdurma, başlatma, yeniden başlatma ve silme gibi temel yönetim görevlerini gerçekleştirebilirsiniz.

![Azure portalında App Service dikey penceresi](./media/quickstart-html/portal2.png)

Soldaki menü, uygulamanızı yapılandırmak için farklı sayfalar sağlar.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Önceki adımlarda, bir kaynak grubunda Azure kaynakları oluşturdunuz. Bu kaynakların gelecekte gerekli olacağını düşünmüyorsanız, Cloud Shell’de aşağıdaki komutu çalıştırarak kaynak grubunu silin. Kaynak grubu adının [web uygulaması oluşturma](#create-a-web-app) adında otomatik olarak oluşturulduğunu unutmayın.

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Bu komutun çalıştırılması bir dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel etki alanı eşleme](app-service-web-tutorial-custom-domain.md)
