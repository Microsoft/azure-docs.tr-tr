---
title: Windows 'da Azure IoT Edge cihaz oluşturmak için hızlı başlangıç | Microsoft Docs
description: Bu hızlı başlangıçta, IoT Edge bir cihaz oluşturmayı ve ardından Azure portal önceden oluşturulmuş kodu uzaktan dağıtmayı öğrenin.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 9f0562d4471ac1129bf9bc7ecfee058cddac7c61
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533139"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Hızlı başlangıç: ilk IoT Edge modülünüzü bir Windows cihazına dağıtma (Önizleme)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Windows IoT Edge cihazında bir Linux 'a Kapsayıcılı kod dağıtarak bu hızlı başlangıçta Azure IoT Edge deneyin. IoT Edge, iş yüklerinizi kenarda daha fazla gönderebilmeniz için cihazlarınızda kodu uzaktan yönetmenizi sağlar. Bu hızlı başlangıçta, Windows 'da Linux için Azure IoT Edge kullanmanın ne kadar kolay olduğunu görmek için kendi cihazınızı kullanmanızı öneririz.

Bu hızlı başlangıçta şunları yapmayı öğreneceksiniz:

* IoT Hub 'ı oluşturun.
* Bir IoT Edge cihazını IoT hub'ınıza kaydetme.
* Windows çalışma zamanı üzerindeki Linux için IoT Edge cihazınıza yükleyip başlatın.
* Bir IoT Edge cihaza uzaktan bir modül dağıtın ve telemetri gönderin.

![Cihazınız ve bulutunuz için bu hızlı başlangıç mimarisini gösteren diyagram.](./media/quickstart/install-edge-full.png)

Bu hızlı başlangıçta, Windows cihazında Linux için Azure IoT Edge ayarlama işlemi adım adım gösterilmektedir. Daha sonra, Azure portal cihazınıza bir modül dağıtırsınız. Kullanacağınız modül, sıcaklık, nem ve basınç verileri üreten bir sanal algılayıcı. Diğer Azure IoT Edge öğreticileri, iş öngörüleri için sanal verileri çözümleyen modüller dağıtarak, burada yaptığınız çalışmayı oluşturur.

Etkin bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

>[!NOTE]
>Windows üzerinde Linux için IoT Edge [genel önizlemede](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Ortamınızı Azure CLı için hazırlayın.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Bu hızlı başlangıçta kullanacağınız tüm kaynakları yönetmek için bir bulut kaynak grubu oluşturun.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge cihazınızın aşağıdaki gereksinimleri karşıladığından emin olun:

* Sürümler
  * Windows 10 sürüm 1809 veya üzeri; derleme 17763 veya üzeri
    * Professional, Enterprise, IoT Enterprise
  * Windows Server 2019 derleme 17763 veya üzeri

* Donanım gereksinimleri
  * Minimum boş bellek: 1 GB
  * Minimum boş disk alanı: 10 GB

>[!NOTE]
>Bu hızlı başlangıçta Windows 'da Linux için IoT Edge dağıtımı oluşturmak üzere Windows Yönetim Merkezi kullanılır. PowerShell de kullanabilirsiniz. Dağıtımınızı oluşturmak için PowerShell 'i kullanmak istiyorsanız, [bir Windows cihazında Linux için Azure IoT Edge yükleme ve sağlama](how-to-install-iot-edge-on-windows.md)hakkındaki nasıl yapılır kılavuzundaki adımları izleyin.

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Azure CLı ile bir IoT Hub 'ı oluşturarak başlayın.

![Bir ı T T hub 'ı oluşturma adımını gösteren diyagram.](./media/quickstart/create-iot-hub.png)

Azure IoT Hub 'in ücretsiz düzeyi bu hızlı başlangıç için geçerlidir. Geçmişte IoT Hub kullandıysanız ve zaten oluşturulmuş bir hub 'ınız varsa, bu IoT Hub 'ını kullanabilirsiniz.

Aşağıdaki kod, kaynak grubunda ücretsiz bir **F1** hub oluşturur `IoTEdgeResources` . `{hub_name}`IoT Hub 'ınız için benzersiz bir adla değiştirin. IoT Hub 'ı oluşturmak birkaç dakika sürebilir.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Aboneliğinizde zaten bir ücretsiz hub 'ınız olduğundan bir hata alırsanız, SKU 'YU olarak değiştirin `S1` . IoT Hub adının kullanılamadığı bir hata alırsanız, başka birinin zaten bu adı taşıyan bir hub 'ı vardır. Yeni bir ad deneyin.

## <a name="register-an-iot-edge-device"></a>IoT Edge cihazı kaydetme

Yeni oluşturulan IoT hub'ına bir IoT Edge cihazı kaydedin.

![Bir cihazı IoT Hub kimliğiyle kaydetme adımını gösteren diyagram.](./media/quickstart/register-device.png)

IoT hub'ınızla iletişim kurabilmesi amacıyla simülasyon cihazınız için bir cihaz kimliği oluşturun. Cihaz kimliği bulutta kalır ve fiziksel cihazla cihaz kimliği arasında bağlantı kurmak için benzersiz bir bağlantı dizesi kullanmanız gerekir.

IoT Edge aygıtlar, tipik IoT cihazlarından farklı şekilde yönetilebilir ve yönetilebilir. `--edge-enabled`Bu kimliğin IoT Edge bir cihaz için olduğunu bildirmek için bayrağını kullanın.

1. Azure Cloud Shell, hub 'ınızda **Myedgedevice** adlı bir cihaz oluşturmak için aşağıdaki komutu girin.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     İlke anahtarları hakkında bir hata alırsanız `iothubowner` , Cloud Shell Azure IoT uzantısının en son sürümünü çalıştırdığından emin olun.

1. Fiziksel cihazınızı IoT Hub içindeki kimliğiyle bağlayan cihazınız için bağlantı dizesini görüntüleyin. IoT Hub 'ınızın adı, cihazınızın adı ve iki arasındaki bağlantıların kimliğini doğrulayan paylaşılan bir anahtar içerir.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. JSON çıktısındaki `connectionString` anahtarının değerini kopyalayıp kaydedin. Bu değer, cihaz bağlantı dizesidir. Bunu, sonraki bölümde IoT Edge çalışma zamanını yapılandırmak için kullanacaksınız.

     ![Cloud Shell connectionString çıkışını gösteren ekran görüntüsü.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge çalışma zamanını yükleme ve başlatma

Cihazınıza Windows üzerinde Linux için IoT Edge yükleyip cihaz bağlantı dizesiyle yapılandırın.

![IoT Edge çalışma zamanını başlatma adımını gösteren diyagram.](./media/quickstart/start-runtime.png)

1. [Windows yönetici merkezini indirin](https://aka.ms/wacdownload).

1. Cihazınızda Windows yönetici merkezini ayarlamak için yükleme sihirbazındaki yönergeleri izleyin.

1. Windows yönetici merkezini açın.

1. Sağ üst köşedeki **Ayarlar dişli** simgesini seçin ve ardından **Uzantılar**' ı seçin.

1. **Akışlar** sekmesinde **Ekle**' yi seçin.

1. `https://aka.ms/wac-insiders-feed`Metin kutusuna girin ve ardından **Ekle**' yi seçin.

1. Akış eklendikten sonra, **kullanılabilir uzantılar** sekmesine gidin ve uzantılar listesinin güncelleştirilmesini bekleyin.

1. **Kullanılabilir uzantılar** listesinden **Azure IoT Edge**' yi seçin.

1. Uzantıyı yükler.

1. Uzantı yüklendiğinde, ana pano sayfasına gitmek için sol üst köşedeki **Windows Yönetim Merkezi** ' ni seçin.

     **Localhost** bağlantısı, Windows Yönetim Merkezi 'NI çalıştırdığınız bilgisayarı temsil eder.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Windows yönetici başlangıç sayfasının ekran görüntüsü.":::

1. **Add (Ekle)** seçeneğini belirleyin.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Windows Yönetim Merkezi 'nde Ekle düğmesinin seçildiğinin gösterildiği ekran görüntüsü.":::

1. Yükleme sihirbazını başlatmak için Azure IoT Edge kutucuğunda **Yeni oluştur** ' u seçin.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Azure IoT Edge til içinde yeni bir dağıtım oluşturmayı gösteren ekran görüntüsü.":::

1. Microsoft yazılımı lisans koşulları 'nı kabul etmek için Yükleme Sihirbazı ' na ilerleyin ve sonra **İleri**' yi seçin.

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Yükleme Sihirbazı 'nda devam etmek için Ileri seçeneğini gösteren ekran görüntüsü.":::

1. **Isteğe bağlı Tanılama verileri**' ni seçin ve ardından **İleri: dağıt**' ı seçin. Bu seçim, Microsoft 'un hizmet kalitesini izlemelerine ve korumasına yardımcı olan genişletilmiş Tanılama verileri sağlar.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Tanılama verisi seçeneklerini gösteren ekran görüntüsü.":::

1. **Hedef cihaz Seç** ekranında, istenen hedef cihazınızı seçerek en düşük gereksinimleri karşıladığını doğrulayın. Bu hızlı başlangıç için yerel cihaza IoT Edge yüklüyorsunuz, bu nedenle **localhost** bağlantısını seçiyoruz. Hedef cihaz gereksinimleri karşılıyorsa, devam etmek için **İleri** ' yi seçin.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Hedef cihaz listesini gösteren ekran görüntüsü.":::

1. Varsayılan ayarları kabul etmek için **İleri ' yi** seçin. Dağıtım ekranında, paketi indirme, paketi yükleme, konağı yapılandırma ve Linux sanal makinesini (VM) son ayarlama işlemi gösterilir. Başarılı bir dağıtım şöyle görünür:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Başarılı bir dağıtımın ekran görüntüsü.":::

1. **İleri ' yi seçin:** Azure IoT Edge cihazınızı IoT Hub örneğiniz IÇIN cihaz kimliğiyle sağlamak üzere son adıma devam etmek için Bağlan ' a tıklayın.

1. [Daha önce bu hızlı](#register-an-iot-edge-device) başlangıçta kopyaladığınız bağlantı dizesini **Cihaz bağlantı dizesi** alanına yapıştırın. Ardından **Seçili metotla sağlama** öğesini seçin.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Cihaz bağlantı dizesi alanındaki bağlantı dizesini gösteren ekran görüntüsü.":::

1. Sağlama tamamlandıktan sonra **, Tamam ' ı seçin ve** Windows Yönetim Merkezi Başlangıç ekranına geri dönün. Cihazınızı IoT Edge cihaz olarak listelenmiş olarak görmeniz gerekir.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Windows Yönetim Merkezi 'ndeki tüm bağlantıları gösteren ekran görüntüsü.":::

1. Panosunu görüntülemek için Azure IoT Edge cihazınızı seçin. Azure IoT Hub 'deki cihaz ikizi iş yüklerinin dağıtıldığını görmeniz gerekir. **IoT Edge modül listesi** , **edgeagent** çalıştıran bir modül göstermelidir ve **IoT Edge durumunun** **etkin (çalışıyor)** olması gerekir.

Artık IoT Edge cihazınız yapılandırıldı. Bulutta dağıtılan modülleri çalıştırmak için hazır.

## <a name="deploy-a-module"></a>Modül dağıtma

IoT Hub 'ye telemetri verileri gönderen bir modül dağıtmak için Azure IoT Edge cihazınızı buluttan yönetin.

![Modül dağıtma adımını gösteren diyagram.](./media/quickstart/deploy-module.png)

<!--
[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2
-->

Azure IoT Edge temel yetilerinden biri, buluttan IoT Edge cihazlarınıza kod dağıtmakta. *IoT Edge modüller* , kapsayıcı olarak uygulanan yürütülebilir paketlerdir. Bu bölümde, [Azure Marketi 'nin IoT Edge modüller bölümünden](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) doğrudan Azure IoT Hub ' den önceden oluşturulmuş bir modül dağıtırsınız.

Bu bölümde dağıttığınız modül bir algılayıcısı taklit eder ve üretilen verileri gönderir. Bu modül, geliştirme ve test amacıyla sanal verileri kullanabilmeniz için IoT Edge kullanmaya başlarken yararlı bir kod parçasıdır. Bu modülün tam olarak ne yaptığını görmek isterseniz, [sanal sıcaklık algılayıcısı kaynak kodunu](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)görüntüleyebilirsiniz.

Azure Marketi 'nden ilk modülünüzü dağıtmak için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.

1. Soldaki menüden, **otomatik cihaz yönetimi** altında **IoT Edge**' yi seçin.

1. Cihaz listesinden hedef cihazın cihaz KIMLIĞINI seçin.

1. Üstteki çubukta **modülleri ayarla**' yı seçin.

   ![Set modules seçen ekran görüntüsü.](./media/quickstart/select-set-modules.png)

1. **IoT Edge modüller** altında, **Ekle** açılan menüsünü açın ve ardından **Market modülü**' nü seçin.

   ![Ekle açılan menüsünü gösteren ekran görüntüsü.](./media/quickstart/add-marketplace-module.png)

1. **IoT Edge Module marketi**' nde, modülü arayın ve seçin `Simulated Temperature Sensor` .

   Modül, istenen **çalışma** durumu Ile IoT Edge modüller bölümüne eklenir.

1. **İleri ' yi seçin:** Sihirbazın bir sonraki adımına devam etmek için yollar.

   ![Modül eklendikten sonra sonraki adıma devam eden gösteren ekran görüntüsü.](./media/quickstart/view-temperature-sensor-next-routes.png)

1. **Rotalar** sekmesinde, varsayılan yolu, **route**' u kaldırın ve ardından İleri ' yi seçin. sihirbazın sonraki adımına devam etmek Için yeni ' yi **inceleyin ve oluştur** ' a tıklayın.

   >[!Note]
   >Yollar ad ve değer çiftleri kullanılarak oluşturulur. Bu sayfada iki yol görmeniz gerekir. Varsayılan yol, **route**, tüm iletileri IoT Hub gönderir (denir `$upstream` ). Azure Marketi 'nden modülü eklediğinizde, **SimulatedTemperatureSensorToIoTHub** adlı ikinci bir yol otomatik olarak oluşturulmuştur. Bu yol, sanal sıcaklık modülünden tüm iletileri IoT Hub gönderir. Bu durumda gereksiz olduğu için varsayılan yolu silebilirsiniz.

   ![Sonraki adıma geçmek üzere varsayılan yolun kaldırılmasını gösteren ekran görüntüsü.](./media/quickstart/delete-route-next-review-create.png)

1. JSON dosyasını gözden geçirin ve ardından **Oluştur**' u seçin. JSON dosyası IoT Edge cihazınıza dağıttığınız tüm modülleri tanımlar. **SimulatedTemperatureSensor** modülünü ve iki çalışma zamanı modülünü, **edgeagent** ve **edgehub**'ı görürsünüz.

   >[!Note]
   >IoT Edge cihazına yeni bir dağıtım gönderdiğinizde cihazınıza hiçbir şey gönderilmez. Bunun yerine cihaz, IoT Hub'ı düzenli olarak sorgulayarak yeni yönergeler olup olmadığını denetler. Güncelleştirilmiş bir dağıtım bildirimi bulması halinde cihaz yeni dağıtımla ilgili bilgileri kullanarak buluttaki modül görüntülerini çeker ve modülleri yerel ortamda çalıştırmaya başlar. Bu işlem birkaç dakika sürebilir.

1. Modül dağıtım ayrıntılarını oluşturduktan sonra, sihirbaz sizi cihaz ayrıntıları sayfasına döndürür. **Modüller** sekmesinde dağıtım durumunu görüntüleyin.

   Üç modül görmeniz gerekir: **$edgeAgent**, **$edgeHub** ve **SimulatedTemperatureSensor**. Bir veya daha fazla modülden **dağıtımda belirtilen** **Evet** varsa ancak **cihaz tarafından raporlanmadığında**, IoT Edge cihazınız hala başlatılıyor demektir. Birkaç dakika bekleyin ve sonra sayfayı yenileyin.

   ![Dağıtılan modüller listesinde sanal sıcaklık algılayıcısı gösteren ekran görüntüsü.](./media/quickstart/view-deployed-modules.png)

## <a name="view-the-generated-data"></a>Oluşturulan verileri görüntüleme

Bu hızlı başlangıçta, yeni bir IoT Edge cihazı oluşturdunuz ve üzerine IoT Edge çalışma zamanını yüklediniz. Ardından, cihazın kendisinde değişiklik yapmak zorunda kalmadan cihazda çalıştırmak üzere bir IoT Edge modülünü dağıtmak için Azure portal kullandınız.

Gönderdiğiniz modül, daha sonra test etmek için kullanabileceğiniz örnek ortam verileri oluşturur. Sanal algılayıcı hem makinenin hem de makinenin bulunduğu ortamın izlenmesini izler. Örneğin bu sensör bir sunucu odasında, fabrika sahasında veya rüzgar türbininde olabilir. Gönderdiği iletiler çevresel sıcaklık ve nem, makine sıcaklığı ve basınç ve bir zaman damgası içerir. IoT Edge öğreticileri, bu modülle oluşturulan verileri analiz için test verileri olarak kullanır.

Windows Yönetim Merkezi 'ndeki komut kabuğu 'ndan, buluttan dağıttığınız modülün IoT Edge cihazınızda çalıştığından emin olun.

1. Yeni oluşturulan IoT Edge cihazınıza bağlanın.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Windows Yönetim Merkezi 'nde Bağlan seçimini gösteren ekran görüntüsü.":::

     **Genel bakış** sayfasında **IoT Edge modülü listesini** ve **IoT Edge durumunu** görürsünüz. Dağıtılan modülleri ve cihaz durumunu görebilirsiniz.  

1. **Araçlar** altında **komut kabuğu**' nu seçin. Komut kabuğu, Windows BILGISAYARıNıZDA Azure IoT Edge cihazınızın Linux sanal makinesine bağlanmak için Secure Shell (SSH) otomatik olarak kullanan bir PowerShell terminaldir.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Komut kabuğunu açmayı gösteren ekran görüntüsü.":::

1. Cihazınızdaki üç modülü doğrulamak için aşağıdaki Bash komutunu çalıştırın:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Komut kabuğu g uç listesi çıkışını gösteren ekran görüntüsü.":::

1. Sıcaklık algılayıcısı modülünden buluta gönderilen iletileri görüntüleyin.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >IoT Edge komutlar, modül adlarına başvurduklarında büyük/küçük harfe duyarlıdır.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Modülden buluta gönderilen iletilerin listesini gösteren ekran görüntüsü.":::

IoT Hub 'ınıza gelen iletileri izlemek için [Visual Studio Code Azure IoT Hub uzantısını](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) da kullanabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

IoT Edge öğreticilere devam etmek istiyorsanız, bu adımı atlayın. Bu hızlı başlangıçta, kaydettiğiniz ve ayarladığınız cihazı kullanabilirsiniz. Aksi takdirde, ücretlendirmemek için oluşturduğunuz Azure kaynaklarını silebilirsiniz.

Sanal makinenizi ve IoT hub’ınızı yeni bir kaynak grubunda oluşturduysanız, bu grubu ve ilişkili tüm kaynaklarını silebilirsiniz. Tüm grubu silmek istemiyorsanız, bunun yerine tek tek kaynakları silebilirsiniz.

> [!IMPORTANT]
> Korumak istediğiniz bir şey olduğundan emin olmak için kaynak grubunun içeriğini denetleyin. Silinen kaynak grupları geri alınamaz.

**Iotedgeresobir** grubunu kaldırmak için aşağıdaki komutu kullanın. Silme işlemi birkaç dakika sürebilir.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Kaynak gruplarının listesini görüntülemek için bu komutu kullanarak kaynak grubunun kaldırıldığını doğrulayabilirsiniz.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Windows 'da Linux için Azure IoT Edge kaldırma

Windows 'da Linux için Azure IoT Edge kaldırmak için Windows Yönetim Merkezi 'ndeki Pano uzantısı ' nı kullanın.

1. Windows Yönetim Merkezi 'nde IoT Edge cihaza bağlanın. Azure Pano aracı uzantısı yüklenir.

1. **Kaldır**'ı seçin. Azure IoT Edge kaldırıldıktan sonra, Windows Yönetim Merkezi Azure IoT Edge cihaz bağlantı girişini **Başlangıç** sayfasından kaldırır.

>[!Note]
>Windows sisteminizden Azure IoT Edge kaldırmanın bir diğer yolu   >    >    >    >  da IoT Edge cihazınızda **kaldırma** Azure IoT Edge Başlat ayarları uygulamalarını seçkullanmaktır. Bu yöntem, IoT Edge cihazınızdan Azure IoT Edge kaldırır, ancak Windows Yönetim Merkezi 'nde bağlantıyı geride bırakır. Kaldırma işlemini gerçekleştirmek için, Windows Yönetim merkezini **Ayarlar** menüsünden de kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir IoT Edge cihaz oluşturdunuz ve cihaza kod dağıtmak için Azure IoT Edge bulut arabirimini kullandınız. Artık ortamı hakkında ham veriler üreten bir test cihazınız var.

Sonra, iş mantığınızı çalıştıran IoT Edge modüller oluşturmaya başlayabilmeniz için yerel geliştirme ortamınızı ayarlayın.

> [!div class="nextstepaction"]
> [IoT Edge modülleri geliştirmeye başlama](tutorial-develop-for-linux.md)
