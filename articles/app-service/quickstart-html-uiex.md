---
title: 'Hızlı başlangıç: statik HTML Web uygulaması oluşturma'
description: İlk HTML Merhaba Dünya Azure App Service dakikalar içinde dağıtın. App Service dağıtmak için birçok yol olan git kullanarak dağıtın.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603d8e642cd2e88beec6ae34094a2c6c43d179ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768962"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Azure'da statik bir HTML web uygulaması oluşturma

Bu hızlı başlangıçta temel bir HTML + CSS sitesinin nasıl dağıtılacağı gösterilmektedir <abbr title="Web uygulamalarını, REST API 'Leri ve mobil arka uç uygulamalarını barındırmak için HTTP tabanlı bir hizmet.">Azure App Service</abbr>. Bu hızlı başlangıcı [Cloud Shell](../cloud-shell/overview.md)'de tamamlayacaksınız ama bu komutları [Azure CLI](/cli/azure/install-azure-cli) ile yerel olarak da çalıştırabilirsiniz.

## <a name="1-prepare-your-environment"></a>1. ortamınızı hazırlayın

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[Cloud Shell](../cloud-shell/overview.md)' de bir hızlı başlangıç dizini oluşturun ve ardından bu dizin ile değiştirin.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Ardından, örnek uygulama deposunu quickstart dizininize kopyalamak için aşağıdaki komutu çalıştırın.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. bir Web uygulaması oluşturun

Örnek kodu içeren dizine geçin ve `az webapp up` komutunu çalıştırın. **Değiştir** `<app-name>` Genel olarak benzersiz bir ada sahip.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Sorun giderme</summary>
<ul>
<li><code>az</code>Komut tanınmazsa, <a href="#1-prepare-your-environment">ortamınızı hazırlama</a>bölümünde AÇıKLANDıĞı gibi Azure CLI 'nın yüklü olduğundan emin olun.</li>
<li><code>&lt;app-name&gt;</code>Tüm Azure genelinde benzersiz olan bir adla değiştirin ( <em> geçerli karakterler <code>a-z</code> , <code>0-9</code> ve <code>-</code> </em> ). İyi bir model, şirketinizin adının ve uygulama tanımlayıcısının bir birleşimini kullanmaktır.</li>
<li><code>--sku F1</code>Bağımsız değişkeni, ücretsiz fiyatlandırma katmanında Web uygulaması oluşturur. Saatlik maliyet içeren daha hızlı bir Premium katmanı kullanmak için bu bağımsız değişkeni atlayın.</li>
<li><code>--html</code>Bağımsız değişkeni, tüm klasör içeriğini statik içerik olarak değerlendirmek ve derleme Otomasyonu 'nu devre dışı bırakmak için diyor.</li>
<li>İsteğe bağlı olarak <code>--location &lt;location-name&gt;</code> <code>&lt;location-name&gt;</code> , kullanılabilir bir Azure bölgesi olan bağımsız değişkeni ekleyebilirsiniz. Komutunu çalıştırarak, Azure hesabınız için izin verilen bölgelerin bir listesini alabilirsiniz <a href="/cli/azure/appservice#az_appservice_list_locations"> <code>az account list-locations</code> </a> .</li>
</ul>
</details>

Komutun tamamlanması birkaç dakika sürebilir. 

<details>
<summary>Neler <code>az webapp up</code> yapılıyor?</summary>
<p><code>az webapp up</code> komutu şu eylemleri gerçekleştirir:</p>
<ul>
<li>Varsayılan kaynak grubunu oluşturur.</li>
<li>Varsayılan bir App Service planı oluşturun.</li>
<li>Belirtilen ada sahip <a href="/cli/azure/webapp#az_webapp_create">bir App Service uygulaması oluşturun</a> .</li>
<li><a href="/azure/app-service/deploy-zip">ZIP</a> dosyalarını geçerli çalışma dizininden uygulamaya dağıtın.</li>
<li>Çalıştırılırken, kaynak oluşturma, günlüğe kaydetme ve ZIP dağıtımı hakkında iletiler sağlar.</li>
</ul>

Tamamlandığında, aşağıdaki örneğe benzer bilgiler görüntüler:

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

</details>

`resourceGroup`Kaynakları daha sonra [temizlemek](#6-clean-up-resources) için değere ihtiyacınız olacak.

<hr/>

## <a name="3-browse-to-the-app"></a>3. uygulamaya gidin

Bir tarayıcıda uygulama URL 'sine gidin: `http://<app_name>.azurewebsites.net` .

Sayfa bir Azure App Service web uygulaması çalıştırıyor.

![Örnek uygulama ana sayfası](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. uygulamayı güncelleştirme ve yeniden dağıtma

Cloud Shell,  `nano index.html` nano metin düzenleyicisini açmak için yazın. 

`<h1>`Başlık etiketinde "Azure App Service-örnek STATIK HTML sitesi" ni "Azure App Service" olarak değiştirin.

![Nano index.html](media/quickstart-html/nano-index-html.png)

Komutunu kullanarak yaptığınız değişiklikleri **kaydedin** `^O` .

Komutunu kullanarak nano **'Dan çıkın** `^X` .

Uygulamayı komutla yeniden dağıtın `az webapp up` .

```bash
az webapp up --html
```

**Uygulamaya gözatma** adımında açılan tarayıcı penceresine geri dönün.

Sayfayı **yenileyin** .

![Güncelleştirilen örnek uygulama giriş sayfası](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. yeni Azure uygulamanızı yönetin

[Azure Portal](https://portal.azure.com) **gidin** . 

**Uygulama hizmetleri**' ni **arayıp** **seçin** .

![Azure portal uygulama hizmetleri 'ni seçin](./media/quickstart-html/portal0.png)

Azure uygulamanızın adını **seçin** .

![Azure uygulamasına portal gezintisi](./media/quickstart-html/portal1.png)

Web uygulamanızın Genel Bakış sayfasını görürsünüz. Buradan göz atma, durdurma, başlatma, yeniden başlatma ve silme gibi temel yönetim görevlerini gerçekleştirebilirsiniz.

![Azure portalında App Service dikey penceresi](./media/quickstart-html/portal2.png)

Soldaki menü, uygulamanızı yapılandırmak için farklı sayfalar sağlar.

<hr/>

## <a name="6-clean-up-resources"></a>6. Kaynakları Temizleme

Önceki adımlarda, bir kaynak grubunda Azure kaynakları oluşturdunuz. Bu kaynakların gelecekte gerekli olacağını düşünmüyorsanız, Cloud Shell’de aşağıdaki komutu çalıştırarak kaynak grubunu silin. Kaynak grubu adının [web uygulaması oluşturma](#2-create-a-web-app) adında otomatik olarak oluşturulduğunu unutmayın.

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Bu komutun çalıştırılması bir dakika sürebilir.

<hr/>

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel etki alanı eşleme](app-service-web-tutorial-custom-domain-uiex.md)
