---
title: Uzaktan Izleme çözümünü yerel olarak dağıtma-Visual Studio Code-Azure | Microsoft Docs
description: Bu nasıl yapılır kılavuzunda, test ve geliştirme için Visual Studio Code kullanarak uzaktan izleme çözümü hızlandırıcısının yerel makinenize nasıl dağıtılacağı gösterilmektedir.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890950"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Uzaktan Izleme çözüm hızlandırıcıyı yerel olarak dağıtma-Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Bu makalede, test ve geliştirme için uzaktan Izleme çözümü hızlandırıcısının yerel makinenize nasıl dağıtılacağı gösterilmektedir. Visual Studio Code 'de mikro hizmetleri çalıştırmayı öğreneceksiniz. Yerel bir mikro hizmet dağıtımı şu bulut hizmetlerini kullanır: IoT Hub, Cosmos DB, Azure Akış Analizi ve Azure Time Series Insights.

## <a name="prerequisites"></a>Ön koşullar

Uzaktan Izleme çözümü Hızlandırıcısı tarafından kullanılan Azure hizmetlerini dağıtmak için etkin bir Azure aboneliğine ihtiyacınız vardır.

Hesabınız yoksa yalnızca birkaç dakika içinde ücretsiz bir deneme sürümü hesabı oluşturabilirsiniz. Ayrıntılar için bkz. [Azure Ücretsiz Deneme](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Makine Kurulumu

Yerel dağıtımı tamamlamaya yönelik olarak, yerel geliştirme makinenizde aşağıdaki araçların yüklü olması gerekir:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [NGINX](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code C# uzantısı](https://code.visualstudio.com/docs/languages/csharp)
* [Node. js V8](https://nodejs.org/) -bu yazılım, betiklerin Azure kaynakları oluşturmak IÇIN KULLANDıĞı bilgisayar CLI için bir önkoşuldur. Node. js ile v10 arasındaki kullanma

> [!NOTE]
> Visual Studio Code, Windows, Mac ve Ubuntu için kullanılabilir.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Mikro hizmetleri çalıştırma

Bu bölümde, uzaktan Izleme mikro hizmetlerini çalıştırırsınız. Web Kullanıcı arabirimini yerel olarak, Docker 'daki cihaz benzetimi hizmetini ve Visual Studio Code mikro hizmetleri çalıştırın.

### <a name="build-the-code"></a>Kodu oluşturma

Komut isteminde Azure-iot-PCs-Remote-Monitoring-dotnet\services adresine gidin ve kodu derlemek için aşağıdaki komutları çalıştırın.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Tüm diğer mikro hizmetleri yerel makinede dağıt

Aşağıdaki adımlarda, Visual Studio Code 'de uzaktan Izleme mikro hizmetlerinin nasıl çalıştırılacağı gösterilmektedir:

1. Visual Studio Code'u başlatın.
1. VS Code, **Azure-IoT-PCs-Remote-Monitoring-DotNet** klasörünü açın.
1. **Azure-IoT-PCs-Remote-Monitoring-DotNet** klasöründe **. vscode** adlı yeni bir klasör oluşturun.
1. '. **JSON** ve **Tasks. JSON** dosyalarını services\scripts\local\launch\ıdesettings\vscode konumundan yeni oluşturduğunuz **. vscode** klasörüne kopyalayın.
1. VS Code **Hata Ayıkla panelini** açın ve **tüm mikro hizmetler yapılandırmasını Çalıştır** ' a çalıştırın. Bu yapılandırma, Docker 'da cihaz benzetimi mikro hizmetini çalıştırır ve hata ayıklayıcıda diğer mikro hizmetleri çalıştırır.

Çalıştırma hatası, hata ayıklama konsolundaki **tüm mikro soervıces** çalıştırma sırasında aşağıdaki gibi görünür:

[![dağıtımı-yerel-mikro hizmetler](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Web Kullanıcı arabirimini çalıştırma

Bu adımda, Web Kullanıcı arabirimini başlatın. Yerel kopyanızı **Azure-iot-PCs-Remote-Monitoring-dotnet\webui** klasörüne gidin ve aşağıdaki komutları çalıştırın:

```cmd
npm install
npm start
```

Başlangıç tamamlandığında tarayıcınızda **http:\//localhost: 3000/Dashboard**sayfası görüntülenir. Bu sayfadaki hatalar beklenmektedir. Uygulamayı hata olmadan görüntülemek için aşağıdaki adımı izleyin.

### <a name="configure-and-run-nginx"></a>NGıNX 'i yapılandırma ve çalıştırma

Yerel makinenizde çalışan Web uygulamasını ve mikro hizmetleri bağlamak için bir ters proxy sunucusu ayarlayın:

* **NGINX. conf** dosyasını **webui\scripts\localhost** klasöründen **nginx\conf** install dizinine kopyalayın.
* **NGINX**'i çalıştırın.

**NGINX**çalıştırma hakkında daha fazla bilgi için bkz. [Windows için NGINX](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Panoya Bağlan

Uzaktan Izleme çözümü panosuna erişmek için tarayıcınızda http:\//localhost: 9000 adresine gidin.

## <a name="clean-up"></a>Temizleme

Gereksiz ücretlerden kaçınmak için, testlerinizi bitirdiğinizde Azure aboneliğinizden bulut hizmetlerini kaldırın. Hizmetleri kaldırmak için [Azure Portal](https://ms.portal.azure.com) gidin ve **Start. cmd** betiğinin oluşturulduğu kaynak grubunu silin.

Ayrıca, kaynak kodu GitHub 'dan Klonladığınız zaman oluşturulan uzaktan Izleme deposunun yerel kopyasını da silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Uzaktan Izleme çözümünü dağıttığınıza göre, bir sonraki adım [çözüm panosunun yeteneklerini araştırmakta](quickstart-remote-monitoring-deploy.md).
