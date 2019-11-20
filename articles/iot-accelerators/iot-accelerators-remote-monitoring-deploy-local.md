---
title: Uzaktan izleme çözümünü yerel olarak dağıt-VS IDE-Azure | Microsoft Docs
description: Bu nasıl yapılır kılavuzunda, test ve geliştirme için Visual Studio kullanarak uzaktan izleme çözümü hızlandırıcısının yerel makinenize nasıl dağıtılacağı gösterilmektedir.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890882"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Uzaktan Izleme çözüm Hızlandırıcısını yerel olarak dağıtma-Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Bu makalede, test ve geliştirme için uzaktan Izleme çözümü hızlandırıcısının yerel makinenize nasıl dağıtılacağı gösterilmektedir. Visual Studio 'da mikro hizmetleri çalıştırmayı öğrenirsiniz. Yerel bir mikro hizmet dağıtımı, bulutta IoT Hub, Cosmos DB, Azure Akış Analizi ve Azure Time Series Insights Hizmetleri kullanır.

Yerel makinenizde Docker 'da uzaktan Izleme çözüm hızlandırıcıyı çalıştırmak istiyorsanız, bkz. [Uzaktan izleme çözüm hızlandırıcıyı yerel olarak dağıtma-Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Ön koşullar

Uzaktan Izleme çözümü Hızlandırıcısı tarafından kullanılan Azure hizmetlerini dağıtmak için etkin bir Azure aboneliğine ihtiyacınız vardır.

Hesabınız yoksa yalnızca birkaç dakika içinde ücretsiz bir deneme sürümü hesabı oluşturabilirsiniz. Ayrıntılar için bkz. [Azure Ücretsiz Deneme](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Makine Kurulumu

Yerel dağıtımı tamamlamaya yönelik olarak, yerel geliştirme makinenizde aşağıdaki araçların yüklü olması gerekir:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [NGINX](https://nginx.org/en/download.html)
* [Node. js V8](https://nodejs.org/) -bu yazılım, betiklerin Azure kaynakları oluşturmak IÇIN KULLANDıĞı bilgisayar CLI için bir önkoşuldur. Node. js ile v10 arasındaki kullanmayın.

> [!NOTE]
> Visual Studio, Windows ve Mac için kullanılabilir.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Mikro hizmetleri çalıştırma

Bu bölümde, uzaktan Izleme mikro hizmetlerini çalıştırırsınız. Web Kullanıcı arabirimini yerel olarak, Docker 'daki cihaz benzetimi hizmetini ve Visual Studio 'daki mikro hizmetleri çalıştırın.

### <a name="run-the-device-simulation-service"></a>Cihaz simülasyon hizmetini çalıştırma

Önceki bölümde **Start. cmd** betiği tarafından ayarlanan ortam değişkenlerine erişiminizin olduğundan emin olmak için yeni bir komut istemi penceresi açın.

Cihaz benzetimi hizmeti için Docker kapsayıcısını başlatmak üzere aşağıdaki komutu çalıştırın. Hizmet, uzaktan izleme çözümü için cihazların benzetimini yapar.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Tüm diğer mikro hizmetleri yerel makinede dağıt

Aşağıdaki adımlarda, Visual Studio 'da uzaktan Izleme mikro hizmetlerinin nasıl çalıştırılacağı gösterilmektedir:

1. Visual Studio 'Yu başlatın.
1. Deponun yerel kopyasında bulunan **Hizmetler** klasöründe **Remote-Monitoring. sln** çözümünü açın.
1. **Çözüm Gezgini**, çözüme sağ tıklayın ve **Özellikler**' e tıklayın.
1. **Başlangıç projesi > ortak özellikler**' i seçin.
1. **Birden çok başlangıç projesi** seçin ve aşağıdaki projeler Için **başlatılacak** **eylemi** ayarlayın:
    * Web hizmeti (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * Web hizmeti (device-telemetry\WebService)
    * Web hizmeti (iothub-manager\WebService)
    * Web hizmeti (storage-adapter\WebService)
1. Seçimlerinizi kaydetmek için **Tamam** ' ı tıklatın.
1. Web hizmetlerini derlemek ve yerel makinede çalıştırmak için hata **ayıklamayı başlatmak > hata ayıkla** ' ya tıklayın.

Her Web hizmeti bir komut istemi ve Web tarayıcısı penceresi açar. Komut isteminde, çalışan hizmetten çıktı ' u görürsünüz ve tarayıcı penceresi durumu izlemenizi sağlar. Komut istemlerini veya Web sayfalarını kapatmayın, bu eylem Web hizmetini durduruyor.

### <a name="start-the-stream-analytics-job"></a>Stream Analytics işini Başlat

Stream Analytics işini başlatmak için aşağıdaki adımları izleyin:

1. [Azure portalına](https://portal.azure.com) gidin.
1. Çözümünüz için oluşturulan **kaynak grubuna** gidin. Kaynak grubunun adı, **Start. cmd** betiğini çalıştırdığınızda çözümünüz için seçtiğiniz addır.
1. Kaynak listesinde **Stream Analytics işe** tıklayın.
1. Stream Analytics iş **genel bakış** sayfasında **Başlat** düğmesine tıklayın. Şimdi işi başlatmak için **Başlat** ' a tıklayın.

### <a name="run-the-web-ui"></a>Web Kullanıcı arabirimini çalıştırma

Bu adımda, Web Kullanıcı arabirimini başlatın. **Start. cmd** betiği tarafından ayarlanan ortam değişkenlerine erişiminizin olduğundan emin olmak için yeni bir komut istemi penceresi açın. Deponun yerel kopyasında **webui** klasörüne gidin ve aşağıdaki komutları çalıştırın:

```cmd
npm install
npm start
```

Başlangıç tamamlandığında tarayıcınızda **http:\//localhost: 3000/Dashboard**sayfası görüntülenir. Bu sayfadaki hatalar beklenmektedir. Uygulamayı hata olmadan görüntülemek için aşağıdaki adımı izleyin.

### <a name="configure-and-run-nginx"></a>NGıNX 'i yapılandırma ve çalıştırma

Yerel makinenizde çalışan Web uygulamasını ve mikro hizmetleri bağlamak için bir ters proxy sunucusu ayarlayın:

* **NGINX. conf** dosyasını deponun yerel kopyasında bulunan **webui\scripts\localhost** klasöründen **nginx\conf** install dizinine kopyalayın.
* **NGINX**'i çalıştırın.

**NGINX**çalıştırma hakkında daha fazla bilgi için bkz. [Windows için NGINX](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Panoya Bağlan

Uzaktan Izleme çözümü panosuna erişmek için tarayıcınızda http:\//localhost: 9000 adresine gidin.

## <a name="clean-up"></a>Temizleme

Gereksiz ücretlerden kaçınmak için, testlerinizi bitirdiğinizde Azure aboneliğinizden bulut hizmetlerini kaldırın. Hizmetleri kaldırmak için [Azure Portal](https://ms.portal.azure.com) gidin ve **Start. cmd** betiğinin oluşturulduğu kaynak grubunu silin.

Ayrıca, kaynak kodu GitHub 'dan Klonladığınız zaman oluşturulan uzaktan Izleme deposunun yerel kopyasını da silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Uzaktan Izleme çözümünü dağıttığınıza göre, bir sonraki adım [çözüm panosunun yeteneklerini araştırmakta](quickstart-remote-monitoring-deploy.md).
