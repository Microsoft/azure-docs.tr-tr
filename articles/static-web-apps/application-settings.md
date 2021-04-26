---
title: Azure statik Web Apps uygulama ayarlarını yapılandırma
description: Azure statik Web Apps uygulama ayarlarını yapılandırmayı öğrenin
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 152b96bee63b6d2280a807ca53842ed3a67ab67d
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279774"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>Azure statik Web Apps önizlemesi için uygulama ayarlarını yapılandırma

Uygulama ayarları, veritabanı bağlantı dizeleri gibi değişebilir değerler için yapılandırma ayarlarını tutar. Uygulama ayarlarını eklemek, uygulama kodunu değiştirmek zorunda kalmadan, yapılandırma girişini uygulamanıza değiştirmenize izin verir.

Uygulama ayarları şunlardır:

- Rest 'te şifrelendi
- [Hazırlama](review-publish-pull-requests.md) ve üretim ortamlarına kopyalanmış

Uygulama ayarları bazen ortam değişkenleri olarak da adlandırılır.

> [!IMPORTANT]
> Bu makalede açıklanan uygulama ayarları, yalnızca bir Azure statik Web uygulamasının arka uç API 'SI için geçerlidir.
>
> Ön uç Web uygulamanızla ortam değişkenlerini kullanma hakkında daha fazla bilgi için, [JavaScript Framework](#javascript-frameworks-and-libraries) veya [statik site Oluşturucu](#static-site-generators)belgelerine bakın.

## <a name="prerequisites"></a>Önkoşullar

- Azure statik Web Apps uygulaması
- [Azure CLI](/cli/azure/install-azure-cli)

## <a name="types-of-application-settings"></a>Uygulama ayarları türleri

Azure statik Web Apps uygulamasının genellikle iki yönü vardır. Birincisi, Web uygulaması veya HTML, CSS, JavaScript ve görüntülerle temsil edilen statik içeriktir. İkincisi, bir Azure Işlevleri uygulaması tarafından desteklenen arka uç API 'sidir.

Bu makalede, Azure Işlevlerinde arka uç API 'SI için uygulama ayarlarının nasıl yönetileceği gösterilmektedir.

Bu makalede açıklanan uygulama ayarları statik Web uygulamalarında kullanılamaz veya başvurulamaz. Ancak, çoğu ön uç çerçeve ve statik site oluşturucuları, geliştirme sırasında ortam değişkenlerinin kullanılmasına izin verir. Derleme zamanında, bu değişkenler oluşturulan HTML veya JavaScript içindeki değerleriyle değiştirilmiştir. HTML ve JavaScript 'teki veriler site ziyaretçisi tarafından kolayca keşfedildiğinden, hassas bilgileri ön uç uygulamasına yerleştirmekten kaçınmak istiyorsunuz. Hassas verileri tutan ayarlar uygulamanızın API bölümünde en iyi şekilde bulunur.

Ortam değişkenlerinin JavaScript çerçevesi veya kitaplığı ile kullanımı hakkında bilgi için, daha fazla ayrıntı için aşağıdaki makalelere bakın.

### <a name="javascript-frameworks-and-libraries"></a>JavaScript çerçeveleri ve kitaplıkları

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>Statik site oluşturucuları

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>API uygulama ayarları hakkında

Azure statik Web Apps API 'Leri, Azure Işlevleri tarafından desteklenir ve bu, _local.settings.jsüzerinde_ uygulama ayarlarını tanımlamanızı sağlar. Bu dosya yapılandırma özelliğindeki uygulama ayarlarını tanımlar `Values` .

Aşağıdaki örnek _local.settings.js_ , için nasıl bir değer ekleneceğini gösterir `DATABASE_CONNECTION_STRING` .

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

Özellikte tanımlanan ayarlar, `Values` nesneden kullanılabilir olan ortam değişkenleri olarak koddan başvurulabilirler `process.env` .

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

`local.settings.json`Veritabanı bağlantı dizeleri gibi hassas bilgiler genellikle dosyaya dahil edildiğinden, dosya GitHub deposu tarafından izlenmez. Yerel ayarlar makinenizde kalacağından, ayarlarınızı Azure 'a el ile yüklemeniz gerekir.

Genellikle ayarlarınızı karşıya yüklemek nadiren yapılır ve her derlemede gerekli değildir.

## <a name="uploading-application-settings"></a>Uygulama ayarları karşıya yükleniyor

Uygulama ayarlarını Azure portal aracılığıyla veya Azure CLı ile yapılandırabilirsiniz.

### <a name="using-the-azure-portal"></a>Azure portalını kullanma

Azure portal, uygulama ayarlarını oluşturmak, güncelleştirmek ve silmek için bir arabirim sağlar.

1. [Azure Portal](https://portal.azure.com)gidin.

1. Arama çubuğunda **statik Web Apps** bulun ve seçin.

1. Kenar çubuğundaki **yapılandırma** seçeneğine tıklayın.

1. Uygulama ayarlarını uygulamak istediğiniz ortamı seçin. Hazırlama ortamları, bir çekme isteği oluşturulduğunda otomatik olarak oluşturulur ve çekme isteği birleştirildikten sonra üretime yükseltilir. Ortam başına uygulama ayarları ayarlayabilirsiniz.

1. Yeni bir uygulama ayarı eklemek için **Ekle düğmesine** tıklayın.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Azure statik Web Apps yapılandırma görünümü":::

1. Bir **ad** ve **değer** girin.

1. **Tamam**'a tıklayın.

1. **Kaydet**’e tıklayın.

### <a name="using-the-azure-cli"></a>Azure CLI'yı kullanma

`az rest`Ayarlarınızı Azure 'a toplu olarak karşıya yüklemek için komutunu kullanabilirsiniz. Komut, uygulama ayarlarını adlı bir üst özellikte JSON nesneleri olarak kabul eder `properties` .

Uygun değerlerle bir JSON dosyası oluşturmanın en kolay yolu, dosyasında _local.settings.js_ değiştirilmiş bir sürümü oluşturmaktır.

1. Gizli verileri olan yeni dosyanızı herkese açık bir şekilde açığa çıkardığından emin olmak için, _. gitignore_ dosyanıza aşağıdaki girişi ekleyin.

   ```bash
   local.settings*.json
   ```

2. Sonra, _local.settings.js_ dosyanın bir kopyasını oluşturun ve _local.settings.properties.jsüzerine_ adlandırın.

3. Yeni dosya içinde, uygulama ayarları hariç diğer tüm verileri dosyadan kaldırın ve olarak yeniden adlandırın `Values` `properties` .

   Dosyanız Şu örneğe benzer şekilde görünmelidir:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Azure CLı komutu, karşıya yüklemeyi çalıştırmak için hesabınıza özgü sayıda değer gerektirir. Statik Web Apps kaynağınızın _genel bakış_ penceresinde aşağıdaki bilgilere erişebilirsiniz:

1. Statik site adı
2. Kaynak grubu adı
3. Abonelik Kimliği

:::image type="content" source="media/application-settings/overview.png" alt-text="Azure statik Web Apps genel bakış":::

4. Terminal veya komut satırından aşağıdaki komutu yürütün. , Ve ' nin yer tutucuları, `<YOUR_STATIC_SITE_NAME>` `<YOUR_RESOURCE_GROUP_NAME>` `<YOUR_SUBSCRIPTION_ID>` _genel bakış_ penceresindeki değerlerinizle değiştirdiğinizden emin olun.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> "local.settings.properties.json" dosyası, bu komutun çalıştırıldığı dizinde olmalıdır. Bu dosya, istediğiniz her şey için adlandırılmış olabilir. Ad önemli değil.

### <a name="view-application-settings-with-the-azure-cli"></a>Azure CLı ile uygulama ayarlarını görüntüleme

Uygulama ayarları, Azure CLı aracılığıyla görüntüleyekullanılabilmektedir.

- Terminal veya komut satırından aşağıdaki komutu yürütün. Yer tutucuları, `<YOUR_SUBSCRIPTION_ID>` değerlerinizle değiştirdiğinizden emin `<YOUR_RESOURCE_GROUP_NAME>` olun `<YOUR_STATIC_SITE_NAME>` .

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Ön uç çerçeveleri yapılandırma](front-end-frameworks.md)
