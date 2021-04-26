---
title: Hızlı başlangıç Linux üzerinde Azure IoT Edge cihaz oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta, Linux üzerinde IoT Edge bir cihaz oluşturmayı ve sonra önceden oluşturulmuş kodu Azure portal uzaktan dağıtmayı öğrenin.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cdc5dd2df0dc6ac682d37aea3328545fcb7e5ad2
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728617"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Hızlı başlangıç: ilk IoT Edge modülünüzü bir sanal Linux cihazına dağıtma

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Kapsayıcılı kodu bir sanal Linux IoT Edge cihazına dağıtarak bu hızlı başlangıçta Azure IoT Edge test edin. IoT Edge, iş yüklerinizi kenarda daha fazla gönderebilmeniz için cihazlarınızda kodu uzaktan yönetmenizi sağlar. Bu hızlı başlangıç için, IoT Edge cihazınız için bir Azure sanal makinesi kullanmanızı öneririz. Bu, hızlı bir şekilde bir test makinesi oluşturmanıza ve sonra işiniz bittiğinde silmenizi sağlar.

Bu hızlı başlangıçta şunları yapmayı öğrenirsiniz:

* Bir IoT Hub oluşturma.
* Bir IoT Edge cihazını IoT hub'ınıza kaydetme.
* IoT Edge çalışma zamanını bir sanal cihaza yükleyip başlatın.
* Bir IoT Edge cihazına uzaktan modül dağıtma.

![Diyagram-cihaz ve bulut için hızlı başlangıç mimarisi](./media/quickstart-linux/install-edge-full.png)

Bu hızlı başlangıçta, IoT Edge bir cihaz olarak yapılandırılmış bir Linux sanal makinesi oluşturma işlemi adım adım açıklanmaktadır. Daha sonra, Azure portal bir modülü cihazınıza dağıtırsınız. Bu hızlı başlangıçta kullanılan modül, sıcaklık, nem ve basınç verileri oluşturan bir sanal sensördür. Diğer Azure IoT Edge öğreticileri, iş öngörüleri için sanal verileri çözümleyen ek modüller dağıtarak, burada yaptığınız işi oluşturur.

Etkin bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Ortamınızı Azure CLı için hazırlayın.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Bulut kaynakları:

* Bu hızlı başlangıçta kullandığınız tüm kaynakları yönetmek için kullanacağınız bir kaynak grubu. Bu hızlı başlangıçta ve aşağıdaki öğreticilerde bulunan **ıotedgeresobir** örnek kaynak grubu adı kullanıyoruz.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Azure CLı ile IoT Hub 'ı oluşturarak hızlı başlangıcı başlatın.

![Diyagram-bulutta IoT Hub 'ı oluşturma](./media/quickstart-linux/create-iot-hub.png)

IoT Hub’ın ücretsiz düzeyi bu hızlı başlangıç için kullanılabilir. Geçmişte IoT Hub kullandıysanız ve zaten oluşturulmuş bir hub 'ınız varsa, bu IoT Hub 'ını kullanabilirsiniz.

Aşağıdaki kod, **ıotedgereso,** kaynak grubunda ücretsiz bir **F1** hub oluşturur. `{hub_name}`IoT Hub 'ınız için benzersiz bir adla değiştirin. IoT Hub oluşturmak birkaç dakika sürebilir.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Aboneliğinizde zaten bir ücretsiz hub olduğu için hata alırsanız, SKU değerini **S1** olarak değiştirin. Her aboneliğin yalnızca bir ücretsiz IoT hub’ı olabilir. IoT Hub adının kullanılamadığı bir hata alırsanız, başka birinin zaten bu adı taşıyan bir hub 'ı olduğu anlamına gelir. Yeni bir ad deneyin.

## <a name="register-an-iot-edge-device"></a>IoT Edge cihazı kaydetme

Yeni oluşturulan IoT hub'ına bir IoT Edge cihazı kaydedin.

![Diyagram-IoT Hub kimliği olan bir cihazı kaydetme](./media/quickstart-linux/register-device.png)

IoT Edge cihazınız için, IoT Hub 'ınız ile iletişim kurabilmesi için bir cihaz kimliği oluşturun. Cihaz kimliği bulutta kalır ve fiziksel cihazla cihaz kimliği arasında bağlantı kurmak için benzersiz bir bağlantı dizesi kullanmanız gerekir.

IoT Edge cihazlar sıradan IoT cihazlarından farklı şekilde yönetilemediğinden, bu kimliği bayrağıyla IoT Edge bir cihaz için olacak şekilde bildirin `--edge-enabled` .

1. Azure Cloud Shell, hub 'ınızdaki **Myedgedevice** adlı bir cihaz oluşturmak için aşağıdaki komutu girin.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   İothubowner ilke anahtarları hakkında bir hata alırsanız, Cloud Shell Azure-IoT uzantısının en son sürümünü çalıştırdığından emin olun.

2. Fiziksel cihazınızı IoT Hub içindeki kimliğiyle bağlayan cihazınız için bağlantı dizesini görüntüleyin. IoT Hub 'ınızın adını, cihazınızın adını ve iki arasındaki bağlantıların kimliğini doğrulayan paylaşılan bir anahtarı içerir. IoT Edge cihazınızı ayarlarken sonraki bölümde bu bağlantı dizesine yeniden başvuracağız.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![CLı çıktısından bağlantı dizesini görüntüle](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>IoT Edge cihazınızı yapılandırma

Üzerinde Azure IoT Edge çalışma zamanına sahip bir sanal makine oluşturun.

![Diyagram-çalışma zamanını cihazda Başlat](./media/quickstart-linux/start-runtime.png)

IoT Edge çalışma zamanı tüm IoT Edge cihazlarına dağıtılır. Üç bileşeni vardır. *IoT Edge güvenlik arka plan programı* , IoT Edge aracısını başlatarak IoT Edge bir cihaz her önyüklendiğinde ve cihazı önyükleme başlatılır. *IoT Edge Aracısı* , IoT Edge hub 'ı da dahil olmak üzere IoT Edge cihazdaki modüllerin dağıtılmasını ve izlenmesini kolaylaştırır. *IoT Edge hub* , IoT Edge cihazdaki modüller arasındaki iletişimleri ve cihaz ile IoT Hub yönetir.

Çalışma zamanı yapılandırması sırasında cihaz bağlantı dizesi sağlamanız gerekir. Bu, Azure CLı 'dan aldığınız dizedir. Bu dize, fiziksel cihazınızı Azure'daki IoT Edge cihaz kimliğiyle ilişkilendirir.

### <a name="deploy-the-iot-edge-device"></a>IoT Edge cihazı dağıtma

Bu bölüm, yeni bir sanal makine oluşturmak ve buna IoT Edge çalışma zamanını yüklemek için bir Azure Resource Manager şablonu kullanır. Bunun yerine kendi Linux cihazınızı kullanmak istiyorsanız, [Azure IoT Edge çalışma zamanını yükleme](how-to-install-iot-edge.md)' deki yükleme adımlarını izleyerek bu hızlı başlangıca geri dönebilirsiniz.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Önceden oluşturulmuş [ıotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) şablonuna dayalı IoT Edge cihazınızı oluşturmak IÇIN aşağıdaki CLI komutunu kullanın.

* Bash veya Cloud Shell kullanıcılar için, aşağıdaki komutu bir metin düzenleyicisine kopyalayın, yer tutucu metnini bilgilerinizi ile değiştirin ve sonra bash veya Cloud Shell pencerenize kopyalayın:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* PowerShell kullanıcıları için aşağıdaki komutu PowerShell pencerenize kopyalayın, sonra yer tutucu metnini kendi bilgileriniz ile değiştirin:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Önceden oluşturulmuş [ıotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy/tree/1.2.0) şablonuna dayalı IoT Edge cihazınızı oluşturmak IÇIN aşağıdaki CLI komutunu kullanın.

* Bash veya Cloud Shell kullanıcılar için, aşağıdaki komutu bir metin düzenleyicisine kopyalayın, yer tutucu metnini bilgilerinizi ile değiştirin ve sonra bash veya Cloud Shell pencerenize kopyalayın:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* PowerShell kullanıcıları için aşağıdaki komutu PowerShell pencerenize kopyalayın, sonra yer tutucu metnini kendi bilgileriniz ile değiştirin:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```
:::moniker-end
<!-- end 1.2 -->

Bu şablon aşağıdaki parametreleri alır:

| Parametre | Açıklama |
| --------- | ----------- |
| **kaynak grubu** | Kaynakların oluşturulacağı kaynak grubu. Bu makale genelinde kullandığımız varsayılan **ıotedgeresogenellerinizi** kullanın veya aboneliğinizde mevcut bir kaynak grubunun adını sağlayın. |
| **Şablon-URI** | Kullandığımız Kaynak Yöneticisi şablonuna yönelik bir işaretçi. |
| **dnsLabelPrefix** | Sanal makinenin ana bilgisayar adını oluşturmak için kullanılacak dize. Yer tutucu metnini, sanal makineniz için bir adla değiştirin. |
| **adminUsername** | Sanal makinenin yönetici hesabı için bir Kullanıcı adı. Örnek **azureUser** kullanın veya yeni bir Kullanıcı adı sağlayın. |
| **deviceConnectionString** | Sanal makinede IoT Edge çalışma zamanını yapılandırmak için kullanılan IoT Hub cihaz kimliğinden bağlantı dizesi. Bu parametre içindeki CLı komutu sizin için bağlantı dizesini dönüştürür. Yer tutucu metnini IoT Hub 'ınızın adıyla değiştirin. |
| **authenticationType** | Yönetici hesabı için kimlik doğrulama yöntemi. Bu hızlı başlangıç, **parola** kimlik doğrulaması kullanır, ancak bu parametreyi **sshpublickey** olarak da ayarlayabilirsiniz. |
| **adminPasswordOrKey** | Yönetici hesabı için SSH anahtarının parolası veya değeri. Yer tutucu metnini güvenli bir parola ile değiştirin. Parolanız en az 12 karakter uzunluğunda olmalı ve şunlardan üç tane olmalıdır: küçük harfli karakterler, büyük harfler, rakamlar ve özel karakterler. |

Dağıtım tamamlandıktan sonra, CLı 'de sanal makineye bağlanmak için SSH bilgilerini içeren JSON biçimli çıkış almalısınız. **Çıktılar** bölümünün **ortak SSH** girişinin değerini kopyalayın:

   ![Çıktısından ortak SSH değerini Al](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>IoT Edge çalışma zamanı durumunu görüntüleme

Bu hızlı başlangıçtaki komutların geri kalanı IoT Edge cihazınızın kendisinde gerçekleşirken cihazda neler olduğunu görmeniz için. Bir sanal makine kullanıyorsanız, sizin ayarladığınız yönetici kullanıcı adını ve dağıtım komutu tarafından çıktı olan DNS adını kullanarak o makineye şimdi bağlanın. Ayrıca, DNS adını Azure portal sanal makinenizin Genel Bakış sayfasında bulabilirsiniz. Sanal makinenize bağlanmak için aşağıdaki komutu kullanın. `{admin username}`Ve `{DNS name}` değerlerini kendi değerlerinizle değiştirin.

   ```console
   ssh {admin username}@{DNS name}
   ```

Sanal makinenize bağlandıktan sonra, çalışma zamanının IoT Edge cihazınızda başarıyla yüklenip yapılandırıldığını doğrulayın.

<!--1.1 -->
:::moniker range="iotedge-2018-06"

1. IoT Edge güvenlik arka plan programının sistem hizmeti olarak çalışıp çalışmadığını denetleyin.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Sistem hizmeti olarak çalışan IoT Edge Daemon 'a bakın](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >`iotedge` komutlarını çalıştırmak için yükseltilmiş ayrıcalıklara ihtiyacınız olacaktır. Makinenizdeki oturumu kapattıktan sonra IoT Edge çalışma zamanını yükleyip oturum açtığınızda izinleriniz otomatik olarak güncelleştirilir. Bundan sonra `sudo` komutların önünde kullanın.

2. Hizmetle ilgili sorunları gidermeniz gerekirse hizmet günlüklerini alın.

   ```bash
   journalctl -u iotedge
   ```

3. IoT Edge cihazınızda çalışan tüm modülleri görüntüleyin. Hizmet ilk kez başlatıldığı için yalnızca **edgeAgent** modülünün çalıştığını göreceksiniz. EdgeAgent modülü varsayılan olarak çalışır ve cihazınıza dağıttığınız ek modüllerin yüklenmesini ve başlamasını sağlar.

   ```bash
   sudo iotedge list
   ```

   ![Cihazınızda bir modülü görüntüleme](./media/quickstart-linux/iotedge-list-1.png)
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge çalışıp çalışmadığını denetleyin. Aşağıdaki komut, IoT Edge çalışıyorsa **Tamam** durumu döndürmelidir veya herhangi bir hizmet hatası verebilir.

   ```bash
   sudo iotedge system status
   ```

   >[!TIP]
   >`iotedge` komutlarını çalıştırmak için yükseltilmiş ayrıcalıklara ihtiyacınız olacaktır. Makinenizdeki oturumu kapattıktan sonra IoT Edge çalışma zamanını yükleyip oturum açtığınızda izinleriniz otomatik olarak güncelleştirilir. Bundan sonra `sudo` komutların önünde kullanın.

2. Hizmetle ilgili sorunları gidermeniz gerekirse hizmet günlüklerini alın.

   ```bash
   sudo iotedge system logs
   ```

3. IoT Edge cihazınızda çalışan tüm modülleri görüntüleyin. Hizmet ilk kez başlatıldığı için yalnızca **edgeAgent** modülünün çalıştığını göreceksiniz. EdgeAgent modülü varsayılan olarak çalışır ve cihazınıza dağıttığınız ek modüllerin yüklenmesini ve başlamasını sağlar.

   ```bash
   sudo iotedge list
   ```

:::moniker-end
<!-- end 1.2 -->

Artık IoT Edge cihazınız yapılandırıldı. Bulutta dağıtılan modülleri çalıştırmak için hazır.

## <a name="deploy-a-module"></a>Modül dağıtma

Azure IoT Edge cihazınızı, IoT Hub'ına telemetri verileri gönderecek bir modül dağıtmak için buluttan yönetin.

![Diyagram-modülü buluttan cihaza dağıt](./media/quickstart-linux/deploy-module.png)

<!-- [!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2 -->

Azure IoT Edge temel yetilerinden biri, buluttan IoT Edge cihazlarınıza kod dağıtmakta. *IoT Edge modüller* , kapsayıcı olarak uygulanan yürütülebilir paketlerdir. Bu bölümde, [Azure Marketi 'nin IoT Edge modüller bölümünden](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) doğrudan Azure IoT Hub ' den önceden oluşturulmuş bir modül dağıtırsınız.

Bu bölümde dağıttığınız modül bir algılayıcısı taklit eder ve üretilen verileri gönderir. Bu modül, geliştirme ve test amacıyla sanal verileri kullanabilmeniz için IoT Edge kullanmaya başlarken yararlı bir kod parçasıdır. Bu modülün tam olarak ne yaptığını görmek isterseniz, [sanal sıcaklık algılayıcısı kaynak kodunu](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)görüntüleyebilirsiniz.

Azure Marketi 'nden ilk modülünüzü dağıtmak üzere **modülleri ayarlama** sihirbazını başlatmak için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.

1. Soldaki menüden, **otomatik cihaz yönetimi** altında **IoT Edge**' yi seçin.

1. Cihaz listesinden hedef cihazın cihaz KIMLIĞINI seçin.

1. Üstteki çubukta **modülleri ayarla**' yı seçin.

   ![Set modules seçen ekran görüntüsü.](./media/quickstart/select-set-modules.png)

### <a name="modules"></a>Modül

Sihirbazın ilk adımı, cihazınızda hangi modüllerin çalıştırılmasını istediğinizi seçin.

**IoT Edge modüller** altında, **Ekle** açılan menüsünü açın ve ardından **Market modülü**' nü seçin.

   ![Ekle açılan menüsünü gösteren ekran görüntüsü.](./media/quickstart/add-marketplace-module.png)

**IoT Edge Module marketi**' nde, modülü arayın ve seçin `Simulated Temperature Sensor` . Modül, istenen **çalışma** durumu Ile IoT Edge modüller bölümüne eklenir.

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

EdgeHub ve edgeAgent modüllerinin ayarlarını açmak için **çalışma zamanı ayarları** ' nı seçin. Bu ayarlar bölümü, ortam değişkenleri ekleyerek veya oluşturma seçeneklerini değiştirerek çalışma zamanı modüllerini yönetebileceğiniz yerdir.

Hem edgeHub hem de edgeAgent modüllerinin **görüntü** alanını 1,2 sürüm etiketini kullanacak şekilde güncelleştirin. Örnek:

* `mcr.microsoft.com/azureiotedge-hub:1.2`
* `mcr.microsoft.com/azureiotedge-agent:1.2`

Çalışma zamanı modüllerine yaptığınız değişiklikleri uygulamak için **Kaydet** ' i seçin.

:::moniker-end
<!--end 1.2-->

**İleri ' yi seçin:** Sihirbazın bir sonraki adımına devam etmek için yollar.

   ![Modül eklendikten sonra sonraki adıma devam eden gösteren ekran görüntüsü.](./media/quickstart/view-temperature-sensor-next-routes.png)

### <a name="routes"></a>Yollar

**Rotalar** sekmesinde, varsayılan yolu, **route**' u kaldırın ve ardından İleri ' yi seçin. sihirbazın sonraki adımına devam etmek Için yeni ' yi **inceleyin ve oluştur** ' a tıklayın.

   >[!Note]
   >Yollar ad ve değer çiftleri kullanılarak oluşturulur. Bu sayfada iki yol görmeniz gerekir. Varsayılan yol, **route**, tüm iletileri IoT Hub gönderir (denir `$upstream` ). Azure Marketi 'nden modülü eklediğinizde, **SimulatedTemperatureSensorToIoTHub** adlı ikinci bir yol otomatik olarak oluşturulmuştur. Bu yol, sanal sıcaklık modülünden tüm iletileri IoT Hub gönderir. Bu durumda gereksiz olduğu için varsayılan yolu silebilirsiniz.

   ![Sonraki adıma geçmek üzere varsayılan yolun kaldırılmasını gösteren ekran görüntüsü.](./media/quickstart/delete-route-next-review-create.png)

### <a name="review-and-create"></a>Gözden geçir ve oluştur

JSON dosyasını gözden geçirin ve ardından **Oluştur**' u seçin. JSON dosyası IoT Edge cihazınıza dağıttığınız tüm modülleri tanımlar. **SimulatedTemperatureSensor** modülünü ve iki çalışma zamanı modülünü, **edgeagent** ve **edgehub**'ı görürsünüz.

   >[!Note]
   >IoT Edge cihazına yeni bir dağıtım gönderdiğinizde cihazınıza hiçbir şey gönderilmez. Bunun yerine cihaz, IoT Hub'ı düzenli olarak sorgulayarak yeni yönergeler olup olmadığını denetler. Güncelleştirilmiş bir dağıtım bildirimi bulması halinde cihaz yeni dağıtımla ilgili bilgileri kullanarak buluttaki modül görüntülerini çeker ve modülleri yerel ortamda çalıştırmaya başlar. Bu işlem birkaç dakika sürebilir.

Modül dağıtım ayrıntılarını oluşturduktan sonra, sihirbaz sizi cihaz ayrıntıları sayfasına döndürür. **Modüller** sekmesinde dağıtım durumunu görüntüleyin.

Üç modül görmeniz gerekir: **$edgeAgent**, **$edgeHub** ve **SimulatedTemperatureSensor**. Bir veya daha fazla modülden **dağıtımda belirtilen** **Evet** varsa ancak **cihaz tarafından raporlanmadığında**, IoT Edge cihazınız hala başlatılıyor demektir. Birkaç dakika bekleyin ve sonra sayfayı yenileyin.

   ![Dağıtılan modüller listesinde sanal sıcaklık algılayıcısı gösteren ekran görüntüsü.](./media/quickstart/view-deployed-modules.png)

## <a name="view-generated-data"></a>Oluşturulan verileri görüntüleme

Bu hızlı başlangıçta, yeni bir IoT Edge cihazı oluşturdunuz ve üzerine IoT Edge çalışma zamanını yüklediniz. Ardından, cihazın kendisinde değişiklik yapmak zorunda kalmadan cihazda çalıştırmak üzere bir IoT Edge modülünü dağıtmak için Azure portal kullandınız.

Bu durumda, gönderdiğiniz modül daha sonra test etmek için kullanabileceğiniz örnek ortam verileri oluşturur. Sanal algılayıcı hem makinenin hem de makinenin bulunduğu ortamın izlenmesini izler. Örneğin bu sensör bir sunucu odasında, fabrika sahasında veya rüzgar türbininde olabilir. İletide ortam sıcaklığı ve nemi, makine sıcaklığı ve basıncı ile bir zaman damgası bulunur. IoT Edge öğreticileri, bu modülle oluşturulan verileri analiz için test verileri olarak kullanır.

IoT Edge cihazınızda komut istemini yeniden açın veya Azure CLI'den SSH bağlantısı kurun. Buluttan dağıtılan modülün IoT Edge cihazınızda çalıştığından emin olun:

   ```bash
   sudo iotedge list
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
   ![Cihazınızda üç modül görüntüleme](./media/quickstart-linux/iotedge-list-2-version-201806.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
   ![Cihazınızda üç modül görüntüleme](./media/quickstart-linux/iotedge-list-2-version-202011.png)
:::moniker-end

Sıcaklık Algılayıcı modülünden gönderilen iletileri görüntüleyin:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge komutlar modül adlarına başvururken büyük/küçük harfe duyarlıdır.

   ![Verileri modülünüzden görüntüleme](./media/quickstart-linux/iotedge-logs.png)

Ayrıca, [Visual Studio Code Için Azure IoT Hub uzantısını](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)kullanarak IoT Hub 'ınıza gelen iletileri izleyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

IoT Edge öğreticilerine devam etmek istiyorsanız bu hızlı başlangıçta kaydettiğiniz ve ayarladığınız cihazı kullanabilirsiniz. Aksi takdirde, ücretlendirmemek için oluşturduğunuz Azure kaynaklarını silebilirsiniz.

Sanal makinenizi ve IoT hub’ınızı yeni bir kaynak grubunda oluşturduysanız, bu grubu ve ilişkili tüm kaynaklarını silebilirsiniz. Tutmak istediğiniz bir şey olduğundan emin olmak için kaynak grubunun içeriğini iki kez kontrol edin. Tüm grubu silmek istemiyorsanız, bunun yerine tek tek kaynakları silebilirsiniz.

> [!IMPORTANT]
> Silinen kaynak grupları geri alınamaz.

**IoTEdgeResources** grubunu kaldırın. Bir kaynak grubunun silinmesi birkaç dakika sürebilir.

```azurecli-interactive
az group delete --name IoTEdgeResources --yes
```

Kaynak gruplarının listesini görüntüleyerek, kaynak grubunun kaldırıldığını doğrulayabilirsiniz.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir IoT Edge cihaz oluşturdunuz ve cihaza kod dağıtmak için Azure IoT Edge bulut arabirimini kullandınız. Artık ortamı hakkında ham veri üreten bir test cihazınız var.

Bir sonraki adım, iş mantığınızı çalıştıran IoT Edge modüller oluşturmaya başlayabilmeniz için yerel geliştirme ortamınızı ayarlamanıza olanak sağlar.

> [!div class="nextstepaction"]
> [Linux cihazları için IoT Edge modülleri geliştirmeye başlama](tutorial-develop-for-linux.md)
