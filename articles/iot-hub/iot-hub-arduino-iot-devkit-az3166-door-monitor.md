---
title: Azure Işlevleri kullanılarak kapı açıldığında e-posta gönder
description: Bir kapısının açılıp açılmadığını algılamak için manyetik algılayıcıyı izleyin ve bir e-posta bildirimi göndermek için Azure Işlevlerini kullanın.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 23607b52f866bb7b0be760e49be7d62d1732d43c
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954746"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Kapı Izleyicisi--Azure Işlevleri ve SendGrid kullanarak bir kapı açıldığında e-posta gönderin           

Mxyongaıot DevKit yerleşik bir manyetik algılayıcı içeriyor. Bu projede, yakın bir güçlü manyetik alanın varlığını veya yokluğunu tespit edersiniz. Bu durumda, küçük ve kalıcı bir mıknatıs 'den geliyor.

## <a name="what-you-learn"></a>Öğrenecekleriniz

Bu projede şunları öğrenirsiniz:
- Bir mıknatıt IoT DevKit 'in manyetik algılayıcısını kullanarak yakın bir mıknatıs 'in hareketini tespit edin.
- E-posta adresinize bildirim göndermek için SendGrid hizmetini kullanma.

> [!NOTE]
> Bu projenin pratik kullanımı için aşağıdaki görevleri gerçekleştirin:
> - Kapısının kenarına bir mıknatıs bağlayın.
> - DevKit 'i kapıya Jamb kapasitesinde takın. Kapıdan açma veya kapatma, algılayıcısı tetikler ve bu da olay için bir e-posta bildirimi almaya yol açar.

## <a name="what-you-need"></a>Ne gerekiyor

[Başlarken Kılavuzunu](iot-hub-arduino-iot-devkit-az3166-get-started.md) şu şekilde sona erdirin:

* DevKit 'in Wi-Fi ' e bağlanmasını sağlama
* Geliştirme ortamını hazırlama

Etkin bir Azure aboneliği. Bir tane yoksa, aşağıdaki yöntemlerden birini kullanarak kaydedebilirsiniz:

* [30 günlük ücretsiz deneme Microsoft Azure hesabını](https://azure.microsoft.com/free/)etkinleştirin.
* MSDN veya Visual Studio abonesi olduğunuzda [Azure kredinizi](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) talep edin.

## <a name="deploy-the-sendgrid-service-in-azure"></a>SendGrid hizmetini Azure 'da dağıtma

[SendGrid](https://sendgrid.com/) , bulut tabanlı bir e-posta teslim platformudur. Bu hizmet, e-posta bildirimleri göndermek için kullanılacaktır.

> [!NOTE]
> Zaten bir SendGrid hizmeti dağıttıysanız, [Azure 'da IoT Hub dağıtmaya](#deploy-iot-hub-in-azure)doğrudan devam edebilirsiniz.

### <a name="sendgrid-deployment"></a>SendGrid dağıtımı

Azure hizmetleri sağlamak için **Azure 'A dağıt** düğmesini kullanın. Bu düğme, açık kaynaklı projelerinizi Microsoft Azure hızlı ve kolay bir şekilde dağıtımına olanak sağlar.

Aşağıdaki **Azure 'A dağıt** düğmesine tıklayın. 

[![Azure’a dağıtma](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Zaten Azure hesabınızda oturum açmadıysanız, şimdi oturum açın. 

Şimdi SendGrid Kaydolma formunu görürsünüz.

![SendGrid dağıtımı](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Kaydolma formunu doldurun:

   * **Kaynak grubu**: SendGrid hizmetini barındırmak için bir kaynak grubu oluşturun veya mevcut bir tane kullanın. Bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/manage-resource-groups-portal.md).

   * **Ad**: SendGrid hizmetinizin adı. Sahip olduğunuz diğer hizmetlerden farklı olarak benzersiz bir ad seçin.

   * **Parola**: hizmet, bu projedeki herhangi bir şey için kullanılmayacak bir parola gerektiriyor.

   * **E-posta**: SendGrid hizmeti bu e-posta adresine doğrulama gönderecek.

Daha sonra bu uygulamanın daha kolay bulunmasını sağlamak için **panoya sabitle** seçeneğini işaretleyin ve ardından oturum açma formunu göndermek Için **satın al** ' a tıklayın.
 
### <a name="sendgrid-api-key-creation"></a>SendGrid API anahtarı oluşturma

Dağıtım tamamlandıktan sonra, tıklatın ve ardından **Yönet** düğmesine tıklayın. E-posta adresinizi doğrulamanız gereken SendGrid hesabınız sayfası görüntülenir.

![SendGrid yönetimi](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

SendGrid sayfasında, **ayarlar** > **API anahtarları** > **API anahtarı oluştur**' a tıklayın.

![SendGrid önce API oluştur](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

**API anahtarı oluştur** sayfasında, **API anahtarı adı** ' nı girin ve **& Görünüm Oluştur**' a tıklayın.

![SendGrid API Second oluştur](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

API anahtarınız yalnızca bir kez görüntülenir. Bir sonraki adımda kullanıldığından, güvenli bir şekilde kopyalanıp depoladığınızdan emin olun.

## <a name="deploy-iot-hub-in-azure"></a>Azure 'da IoT Hub dağıtma

Aşağıdaki adımlar, diğer Azure IoT ile ilgili hizmetleri sağlayacak ve bu proje için Azure Işlevleri dağıtacaktır.

Aşağıdaki **Azure 'A dağıt** düğmesine tıklayın. 

[![Azure’a dağıtma](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Kaydolma formu görüntülenir.

![Iothub dağıtımı](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Kaydolma formundaki alanları girin.

   * **Kaynak grubu**: SendGrid hizmetini barındırmak için bir kaynak grubu oluşturun veya mevcut bir tane kullanın. Bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/manage-resource-groups-portal.md).

   * **IoT Hub adı**: IoT Hub 'ınızın adı. Sahip olduğunuz diğer hizmetlerden farklı olarak benzersiz bir ad seçin.

   * **IoT Hub SKU 'su**: F1 (abonelik başına bir tane ile sınırlıdır) ücretsizdir. [Fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/iot-hub/)daha fazla fiyatlandırma bilgisi görebilirsiniz.

   * **E-posta**: Bu alan, SendGrid hizmetini ayarlarken kullandığınız e-posta adresi ile aynı olmalıdır.

Daha sonra bu uygulamanın daha kolay bulunmasını sağlamak için **panoya sabitle** seçeneğini işaretleyin ve ardından bir sonraki adıma devam etmek Için hazırsanız **satın al** ' a tıklayın.
 
## <a name="build-and-upload-the-code"></a>Kodu derleyin ve karşıya yükleyin

Sonra, örnek kodu VS Code yükleyin ve gerekli Azure hizmetlerini sağlayın.

### <a name="start-vs-code"></a>VS Code Başlat

- DevKit 'in **bilgisayarınıza bağlı olmadığından** emin olun.
- VS Code başlatın.
- DevKit 'i bilgisayarınıza bağlayın.

> [!NOTE]
> VS Code başlattığınızda, Arduino IDE veya ilgili Pano paketini bulamayacağı belirten bir hata iletisi alabilirsiniz. Bu hatayı alırsanız, VS Code kapatın, Arduino IDE 'yi yeniden başlatın ve VS Code Arduino IDE yolunu doğru bulmalıdır.

### <a name="open-arduino-examples-folder"></a>Arduino örnekleri klasörünü aç

Sol taraftaki **Arduino örnekleri** bölümünü GENIŞLETIN, **mxyongaAZ3166 > AzureIoT için örneklere**gidin ve **DoorMonitor**' yi seçin. Bu eylem, içinde bir proje klasörü olan yeni bir VS Code penceresi açar.

![Mini çözüm-örnekler](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Örnek uygulamayı komut paletinden da açabilirsiniz. Komut paletini açmak için `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) kullanın, **Arduino**yazın ve **Arduino: örnekleri**bulun ve seçin.

### <a name="provision-azure-services"></a>Azure hizmetlerini sağlama

Çözüm penceresinde, bulut sağlama görevini çalıştırın:
- `Ctrl+P` yazın (macOS: `Cmd+P`).
- Sunulan metin kutusuna `task cloud-provision` girin.

VS Code terminalinde, etkileşimli bir komut satırı gerekli Azure hizmetlerini sağlama sırasında size rehberlik eder. Daha önce [Azure 'da IoT Hub dağıtma](#deploy-iot-hub-in-azure)bölümünde sağladığınız, istenen listeden tüm aynı öğeleri seçin.

![Bulut sağlama](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Azure 'da oturum açmaya çalışırken sayfa yükleme durumunda askıda kalırsa, bu sorunu çözmek için [IoT devkit SSS konusunun "oturum açılırken sayfa eğişiklikler" bölümüne](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) bakın. 

### <a name="build-and-upload-the-device-code"></a>Cihaz kodunu derleyin ve karşıya yükleyin

Ardından, cihaz için kodu karşıya yükleyin.

#### <a name="windows"></a>Windows

1. `task device-upload`çalıştırmak için `Ctrl+P` kullanın.

2. Terminal sizden yapılandırma moduna girmenizi ister. Bunu yapmak için, düğme A ' yı basılı tutun ve sıfırlama düğmesini gönderin ve serbest bırakın. Ekran, DevKit kimlik numarasını ve sözcük *yapılandırmasını*görüntüler.

#### <a name="macos"></a>macOS

1. DevKit 'i yapılandırma moduna alın: A düğmesini basılı tutun, ardından Sıfırla düğmesini gönderin ve serbest bırakın. Ekran ' yapılandırma ' görüntüler.

2. `task device-upload`çalıştırmak için `Cmd+P` ' ye tıklayın.

#### <a name="verify-upload-and-run-the-sample-app"></a>Örnek uygulamayı doğrulayın, karşıya yükleyin ve çalıştırın

[Azure hizmetleri sağlama](#provision-azure-services) adımından alınan bağlantı dizesi artık ayarlanmıştır. 

VS Code daha sonra, Arduino taslağını bir DevKit 'e doğrulamaya ve yüklemeye başlar.

![cihaz-karşıya yükle](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit yeniden başlatılır ve kodu çalıştırmaya başlar.

> [!NOTE]
> Bazen bir "Error: AZ3166: Unknown Package" hata iletisi alabilirsiniz. Pano paket dizini doğru yenilenmediğinde bu hata oluşur. Bu hatayı çözmek için [IoT DevKit SSS konusunun geliştirme bölümüne](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)bakın.

## <a name="test-the-project"></a>Projeyi test etme

Program ilk olarak DevKit kararlı bir manyetik alan olduğunda başlatılır.

Başlangıçtan sonra `Door closed` ekranda görüntülenir. Mıknatıslı alanında bir değişiklik olduğunda durum `Door opened`olarak değişir. Kapı durumu her değiştiğinde bir e-posta bildirimi alırsınız. (Bu e-posta iletilerinin alınması beş dakika kadar sürebilir.)

![Manağı sensöre yakın: kapılı kapalı](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Manağı sensöre yakın: kapılı kapalı")

![Mıknatıs sensörden uzağa taşındı: kapıdan açıldı](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Mıknatıs sensörden uzağa taşındı: kapıdan açıldı")

## <a name="problems-and-feedback"></a>Sorunlar ve geri bildirim

Sorunlarla karşılaşırsanız [IoT DevKit SSS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) sayfasına bakın veya aşağıdaki kanalları kullanarak bağlanın:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Sonraki adımlar

Bir DevKit cihazını Azure IoT uzaktan Izleme çözüm hızlandırıcısına bağlamayı ve SendGrid hizmetini bir e-posta göndermek için nasıl kullanacağınızı öğrendiniz. Önerilen sonraki adımlar aşağıda verilmiştir:

* [Azure IoT uzaktan Izleme çözüm hızlandırıcısının genel bakış](https://docs.microsoft.com/azure/iot-suite/)
* [Bir Mxyonga IoT DevKit cihazını Azure IoT Central uygulamanıza bağlama](/azure/iot-central/core/howto-connect-devkit)
