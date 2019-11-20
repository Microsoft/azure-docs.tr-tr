---
title: Uzaktan Izleme çözümü Kullanıcı arabirimine sayfa ekleme-Azure | Microsoft Docs
description: Bu makalede, uzaktan Izleme çözümü Hızlandırıcısı Web Kullanıcı arabirimine nasıl yeni bir sayfa ekleyebileceğiniz gösterilmektedir.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: ec0b9fbdfdb96317e1e7f6fe00384ba4f8c42bcc
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607959"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Uzaktan Izleme çözümü Hızlandırıcısı Web Kullanıcı arabirimine özel bir sayfa ekleme

Bu makalede, uzaktan Izleme çözümü Hızlandırıcısı Web Kullanıcı arabirimine nasıl yeni bir sayfa ekleyebileceğiniz gösterilmektedir. Makalede şunları açıklanmaktadır:

- Yerel bir geliştirme ortamı hazırlama.
- Web Kullanıcı arabirimine yeni sayfa ekleme.

Diğer nasıl yapılır kılavuzlarında, eklediğiniz sayfaya daha fazla özellik eklemek için bu senaryo genişletilir.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzundaki adımları tamamlayabilmeniz için, yerel geliştirme makinenizde aşağıdaki yazılımların yüklü olması gerekir:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Kullanıcı arabirimi için yerel bir geliştirme ortamı hazırlama

Uzaktan Izleme çözümü Hızlandırıcısı Kullanıcı arabirimi kodu, [tepki](https://reactjs.org/) verme JavaScript çerçevesi kullanılarak uygulanır. Kaynak kodunu [Uzaktan Izleme WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub deposunda bulabilirsiniz.

Kullanıcı arabiriminde değişiklikler yapmak ve bu değişiklikleri test etmek için yerel geliştirme makinenizde çalıştırabilirsiniz. İsteğe bağlı olarak, yerel kopya gerçek veya sanal cihazlarınızla etkileşime geçmesini sağlamak için çözüm hızlandırıcının dağıtılan bir örneğine bağlanabilir.

Yerel geliştirme ortamınızı hazırlamak için git ' i kullanarak [Uzaktan Izleme WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) deposunu yerel makinenize kopyalayın:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Sayfa ekleme

Web Kullanıcı arabirimine bir sayfa eklemek için, sayfayı tanımlayan kaynak dosyaları eklemeniz ve mevcut bazı dosyaları değiştirerek Web Kullanıcı arabirimini yeni sayfanın farkında olması gerekir.

### <a name="add-the-new-files-that-define-the-page"></a>Sayfayı tanımlayan yeni dosyaları ekleyin

Başlangıç yapmanız için, **src/izlenecek yol/bileşenler/sayfalar/basicpage** klasörü, basit bir sayfayı tanımlayan dört dosya içerir:

**basicPage. Container. js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage. js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage. SCSS**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage. test. js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Yeni bir **src/Components/Pages/example** klasörü oluşturun ve bu dört dosyayı buna kopyalayın.

### <a name="add-the-new-page-to-the-web-ui"></a>Yeni sayfayı Web Kullanıcı arabirimine ekleyin

Yeni sayfayı Web Kullanıcı arabirimine eklemek için, varolan dosyalarda aşağıdaki değişiklikleri yapın:

1. Yeni sayfa kapsayıcısını **src/Components/Pages/index. js** dosyasına ekleyin:

    ```js
    export * from './example/basicPage.container';
    ```

1. Seçim  Yeni sayfa için bir SVG simgesi ekleyin. Daha fazla bilgi için bkz. [WebUI/src/Utilities/Readme. MD](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Varolan bir SVG dosyası kullanabilirsiniz.

1. Sayfa adını, **genel/yerel ayarlar/en/Çeviriler. JSON**çeviri dosyasına ekleyin. Web Kullanıcı arabirimi Uluslararası hale getirme için [i18next](https://www.i18next.com/) kullanır.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Üst düzey uygulama sayfasını tanımlayan **src/Components/App. js** dosyasını açın. Yeni sayfayı içeri aktarmalar listesine ekleyin:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Aynı dosyada, yeni sayfayı `pagesConfig` diziye ekleyin. Yolun adresini ayarlayın, daha önce eklenen SVG simgesine ve çevirilerine başvurun ve öğesini sayfanın kapsayıcısına `component` ayarlayın: `to`

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. `crumbsConfig` Diziye yeni bir içerik haritaları ekleyin:

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    Bu örnek sayfada yalnızca bir içerik haritası vardır ancak bazı sayfalar daha fazla olabilir.

Yaptığınız tüm değişiklikleri kaydedin. Web Kullanıcı arabirimini yeni sayfanız eklenmiş olarak çalıştırmaya hazırsınız.

### <a name="test-the-new-page"></a>Yeni sayfayı test etme

Komut isteminde deponun yerel kopyasının köküne gidin ve gerekli kitaplıkları yüklemek ve Web Kullanıcı arabirimini yerel olarak çalıştırmak için aşağıdaki komutları çalıştırın:

```cmd/sh
npm install
npm start
```

Önceki komut, Kullanıcı arabirimini ' de [http://localhost:3000/dashboard](http://localhost:3000/dashboard)yerel olarak çalıştırır.

Web Kullanıcı arabiriminin yerel örneğinizi Çözüm hızlandırıcının dağıtılan bir örneğine bağlamadan, panoda hatalar görürsünüz. Bu hatalar, yeni sayfanızı test etme yeteneğinizi etkilemez.

Artık, site yerel olarak çalışırken kodu düzenleyebilir ve Web Kullanıcı arabirimi güncelleştirmesini dinamik olarak görebilirsiniz.

## <a name="optional-connect-to-deployed-instance"></a>Seçim Dağıtılan örneğe Bağlan

İsteğe bağlı olarak, Web Kullanıcı arabiriminin yerel çalışan kopyanızı buluttaki uzaktan Izleme çözümü hızlandırıcısına bağlayabilirsiniz:

1. **Bilgisayar** CLI 'sını kullanarak çözüm hızlandırıcının **temel** bir örneğini dağıtın. Dağıtımınızın adını ve sanal makine için verdiğiniz kimlik bilgilerini bir yere unutmayın. Daha fazla bilgi için bkz. [CLI kullanarak dağıtma](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Çözümünüzde mikro hizmetleri barındıran sanal makineye SSH erişimini etkinleştirmek için Azure portal veya [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) kullanın. Örneğin:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Yalnızca test ve geliştirme sırasında SSH erişimini etkinleştirmeniz gerekir. SSH 'yi etkinleştirirseniz, [mümkün olan en kısa sürede yeniden devre dışı bırakmanız gerekir](../security/fundamentals/network-best-practices.md).

1. Sanal makinenizin adını ve genel IP adresini bulmak için Azure portal veya [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) kullanın. Örneğin:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Önceki adımdan IP adresini ve çözümü dağıtmak üzere **bilgisayarları** çalıştırdığınızda verdiğiniz kimlik bilgilerini kullanarak sanal makinenize bağlanmak için SSH kullanın.

1. Yerel UX 'in bağlanmasına izin vermek için, sanal makinedeki bash kabuğu 'nda aşağıdaki komutları çalıştırın:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Komutun tamamlandığını ve Web sitesi başladıktan sonra, sanal makine bağlantısını kesebilirsiniz.

1. [Uzaktan Izleme WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) deposunun yerel kopyasında, DAĞıTıLAN çözümünüzün URL 'sini eklemek için **. env** dosyasını düzenleyin:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, uzaktan Izleme çözüm hızlandırıcısında Web Kullanıcı arabirimini özelleştirmenize yardımcı olacak kaynaklar hakkında bilgi edindiniz.

Bir sayfa tanımladınız, bir sonraki adım, [Uzaktan izleme çözümü Hızlandırıcı Web Kullanıcı arabirimine](iot-accelerators-remote-monitoring-customize-service.md) , Kullanıcı arabiriminde görüntülenecek verileri alan özel bir hizmet eklemektir.

Uzaktan Izleme çözümü Hızlandırıcısı hakkında daha fazla kavramsal bilgi için bkz. [Uzaktan izleme mimarisi](iot-accelerators-remote-monitoring-sample-walkthrough.md).
