---
title: 'Öğretici: IoT Edge üzerinde Azure IoT Edge cihaz-makine öğrenimi yapılandırma'
description: Bu öğreticide, Linux çalıştıran bir Azure sanal makinesini, saydam bir ağ geçidi olarak davranan Azure IoT Edge bir cihaz olarak yapılandıracaksınız.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 65fd6e5b4d494f8e8486d72079b9fa97a175894b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376894"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Öğretici: Azure IoT Edge cihaz yapılandırma

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Bu makalede, Linux çalıştıran bir Azure sanal makinesini, saydam bir ağ geçidi olarak davranan Azure IoT Edge bir cihaz olacak şekilde yapılandıracağız. Saydam bir ağ geçidi yapılandırması, cihazların ağ geçidinin var olduğunu bilmeksizin ağ geçidi üzerinden Azure IoT Hub bağlanmasına olanak sağlar. Aynı zamanda, IoT Hub ' deki cihazlarla etkileşim kuran bir kullanıcı ara ağ geçidi cihazını farkında değildir. Sonuç olarak, saydam ağ geçidine IoT Edge modüller ekleyerek sistemimize Edge Analizi ekleyeceğiz.

>[!NOTE]
>Bu öğreticideki kavramlar tüm IoT Edge sürümleri için geçerlidir, ancak senaryoyu denemek için oluşturduğunuz örnek cihaz sürüm 1,1 ' i IoT Edge çalışır.

Bu makaledeki adımlar genellikle bir bulut geliştiricisi tarafından gerçekleştirilir.

Öğreticinin bu bölümünde şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
>
> * Ağ Geçidi cihazınızın aşağı akış cihazlarınıza güvenli bir şekilde bağlanmasına izin vermek için sertifika oluşturun.
> * IoT Edge bir cihaz oluşturun.
> * IoT Edge cihazınızın benzetimini yapmak için bir Azure sanal makinesi oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu makale, IoT Edge Azure Machine Learning kullanımı hakkında öğretici için bir serinin bir parçasıdır. Serideki her makale, önceki makaledeki iş üzerinde oluşturulur. Bu makaleye doğrudan ulaşdıysanız, serideki [ilk makaleye](tutorial-machine-learning-edge-01-intro.md) bakın.

## <a name="create-certificates"></a>Sertifika oluşturma

Bir cihazın ağ geçidi olarak çalışması için, aşağı akış cihazlarına güvenli bir şekilde bağlanması gerekir. IoT Edge, cihazlar arasında güvenli bağlantılar ayarlamak için ortak anahtar altyapısı (PKI) kullanabilirsiniz. Bu durumda, bir aşağı akış IoT cihazının saydam bir ağ geçidi görevi gören bir IoT Edge cihazına bağlanmasına izin veriyoruz. Makul güvenliği korumak için aşağı akış cihazı IoT Edge cihazının kimliğini onaylamasını sağlamalıdır. IoT Edge cihazların sertifikaları kullanma hakkında daha fazla bilgi için bkz. [Azure IoT Edge sertifikası kullanım ayrıntıları](iot-edge-certs.md).

Bu bölümde, oluşturup çalıştırdığımız bir Docker görüntüsü kullanarak otomatik olarak imzalanan sertifikalar oluşturacağız. Windows geliştirme makinesinde sertifika oluşturmak için gereken adım sayısını azalttığından, bu adımı tamamlamaya yönelik bir Docker görüntüsü kullanmayı seçtik. Docker görüntüsü ile otomatik olarak neler olduğunu anlamak için bkz. [IoT Edge cihaz özelliklerini test etmek için tanıtım sertifikaları oluşturma](how-to-create-test-certificates.md).

1. Geliştirme sanal makinenizde oturum açın.
1. Yolu ve adı **C:\edgecercertificate** olan yeni bir klasör oluşturun.

1. Zaten çalışmıyorsa, Windows Başlat menüsünden **Docker for Windows** başlatın.

1. Visual Studio Code’u açın.

1. **Dosya**  >  **Aç klasörünü** ve ardından **C: \\ kaynak \\ ıotedgeandmlsample \\ createcertificates** öğesini seçin.

1. **Gezgin** bölmesinde **dockerfile** öğesine sağ tıklayın ve **görüntü oluştur**' u seçin.

1. İletişim kutusunda, görüntü adı ve etiketi için varsayılan değeri kabul edin: **createcertificates: latest**.

    ![Visual Studio Code sertifika oluşturmayı gösteren ekran görüntüsü.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Oluşturma işleminin tamamlanmasını bekleyin.

    > [!NOTE]
    > Eksik bir ortak anahtarla ilgili bir uyarı görebilirsiniz. Bu uyarıyı yoksaymak güvenlidir. Benzer şekilde, bu görüntüde göz ardı etmeniz güvenli olan görüntinizdeki izinleri denetlemeyi veya sıfırlamayı öneren bir güvenlik uyarısı görürsünüz.

1. Visual Studio Code Terminal penceresinde createcertificates kapsayıcısını çalıştırın.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker, **c: \\** sürücüsüne erişim isteyecek. **Paylaşma** seçeneğini belirleyin.

1. İstendiğinde kimlik bilgilerinizi sağlayın.

1. Kapsayıcı çalışmayı tamamladıktan sonra, **c: \\ edgecercertificate** içinde aşağıdaki dosyaları denetleyin:

    * c: \\ edgecercertificate \\ sertifikaları \\ Azure-iot-test-only. root. ca. cert. pem
    * c: \\ edgecercertificate \\ sertifikaları \\ New-Edge-Device-Full-Chain. cert. pem
    * c: \\ edgecercertificate \\ sertifikaları \\ New-Edge-Device. cert. pem
    * c: \\ edgecercertificate \\ sertifikaları \\ New-Edge-Device. cert. pfx
    * c: \\ edgecercertificate \\ özel \\ New-Edge-Device. Key. pem

## <a name="upload-certificates-to-azure-key-vault"></a>Sertifikaları Azure Key Vault yükleme

Sertifikalarımızı güvenli bir şekilde depolamak ve bunları birden fazla cihazdan erişilebilir hale getirmek için, sertifikaları Azure Key Vault içine yükleyeceğiz. Yukarıdaki listeden görebileceğiniz gibi iki tür sertifika dosyası vardır: PFX ve ped. PFX dosyasını, Key Vault yüklenecek Key Vault sertifikaları olarak değerlendireceğiz. PEK dosyaları düz metinlerdir ve bunları Key Vault gizli dizi olarak kabul edeceğiz. [Jupyıter not defterlerini](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks)çalıştırarak oluşturduğumuz Azure Machine Learning çalışma alanıyla ilişkili Key Vault örneğini kullanacağız.

1. [Azure Portal](https://portal.azure.com), Azure Machine Learning çalışma alanınıza gidin.

1. Machine Learning çalışma alanının Genel Bakış sayfasında, **Key Vault** adını bulun.

    ![Anahtar Kasası adının kopyalanmasını gösteren ekran görüntüsü.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. Geliştirme makinenizde Key Vault için sertifikaları karşıya yükleyin. **\<subscriptionId\>** Ve yerine **\<keyvaultname\>** kaynak bilgilerinizi koyun.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. İstenirse Azure 'da oturum açın.

1. Komut dosyası, yeni Key Vault girdilerini listeleyen çıktı ile birkaç dakika çalışır.

    ![Key Vault betiği çıktısını gösteren ekran görüntüsü.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>IoT Edge cihazı oluşturma

Bir Azure IoT Edge cihazını IoT Hub 'ına bağlamak için önce hub 'da cihaz için bir kimlik oluşturacağız. Bağlantı dizesini buluttaki cihaz kimliğinden alıp IoT Edge cihazımızda çalışma zamanını yapılandırmak için kullanacaksınız. Yapılandırılmış bir cihaz hub 'a bağlandıktan sonra, modüller dağıtabilir ve ileti gönderebiliriz. Ayrıca, IoT Hub içindeki karşılık gelen cihaz kimliğini değiştirerek fiziksel IoT Edge cihazının yapılandırmasını da değiştirebiliriz.

Bu öğreticide, Visual Studio Code kullanarak yeni cihaz kimliğini oluşturacağız. Ayrıca, Azure portal veya Azure CLı kullanarak bu adımları tamamlayabilirsiniz.

1. Geliştirme makinenizde Visual Studio Code açın.

1. Visual Studio Code **Gezgini** görünümünden **Azure IoT Hub** çerçevesini genişletin.

1. Üç noktayı seçin ve **IoT Edge cihaz oluştur**' u seçin.

1. Cihaza bir ad verin. Kolaylık olması için **Aaturbofanedgedevice** adında listelenen cihazların en üstüne bir sıralama olacak şekilde kullanıyoruz.

1. Yeni cihaz, cihaz listesinde görüntülenir.

    ![Visual Studio Code Explorer 'da cihazın görünümünü gösteren ekran görüntüsü.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Azure sanal makinesini dağıtma

Bu öğretici için IoT Edge cihazımızı oluşturmak üzere Azure Marketi 'ndeki [Ubuntu görüntüsündeki Azure IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) kullanırız. Ubuntu görüntüsündeki Azure IoT Edge, en son IoT Edge çalışma zamanını ve bu grubun bağımlılıklarını başlangıçta kurar. Kullanarak VM 'yi dağıyoruz:

- Bir PowerShell betiği, `Create-EdgeVM.ps1` .
- Azure Resource Manager şablonu `IoTEdgeVMTemplate.json` .
- Bir kabuk betiği, `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Programlı dağıtımı etkinleştir

Azure Marketi 'ndeki görüntüyü betikleştirilmiş bir dağıtımda kullanmak için, görüntü için programlı dağıtımı etkinleştirmemiz gerekir.

1. Azure portalında oturum açın.

1. **Tüm Hizmetler**’i seçin.

1. Arama çubuğunda **Market**' i girip seçin.

1. Market arama çubuğunda **Ubuntu üzerinde Azure IoT Edge** girin ve seçin.

1. Programlı olarak dağıtmak üzere **Başlarken** köprüsünü seçin.

1. **Etkinleştir** düğmesini seçin ve ardından **Kaydet**' i seçin.

    ![Bir sanal makine için programlı dağıtımı etkinleştirmeyi gösteren ekran görüntüsü.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Bir başarı bildirimi görürsünüz.

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Sonra, IoT Edge cihazınız için sanal makineyi oluşturmak üzere betiği çalıştırın.

1. Bir PowerShell penceresi açın ve **Edgevm** dizinine gidin.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Sanal makineyi oluşturmak için betiği çalıştırın.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. İstendiğinde, her bir parametre için değerler sağlayın. Abonelik, kaynak grubu ve konum için, bu öğreticide tüm kaynaklar için sahip olduğunuz değerleri kullanmanızı öneririz.

    * **Azure ABONELIK kimliği**: Azure Portal bulundu.
    * **Kaynak grubu adı**: Bu öğreticide kaynakları gruplandırmak için hatırlayabileceğiniz ad.
    * **Konum**: sanal makinenin oluşturulacağı Azure konumu. Örneğin, westus2 veya northeurope. Daha fazla bilgi için bkz. tüm [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUserName**: sanal makinede oturum açmak için kullanacağınız yönetici hesabının adı.
    * **AdminPassword**: sanal makinede Yönetici Kullanıcı adı için ayarlanacak parola.

1. VM 'yi ayarlamak için betik için, kullanmakta olduğunuz Azure aboneliğiyle ilişkili kimlik bilgileriyle Azure 'da oturum açın.

1. Betik, sanal makinenizin oluşturulmasına ilişkin bilgileri onaylar. Devam etmek için **y** veya **ENTER** ' ı seçin.

1. Komut dosyası aşağıdaki adımları yürüttüğünde birkaç dakika çalışır:

    * Zaten mevcut değilse kaynak grubunu oluşturur
    * Sanal makineyi oluşturur
    * 22 (SSH), 5671 (AMQP), 5672 (AMPQ) ve 443 (TLS) bağlantı noktaları için VM için NSG özel durumları ekler
    * [Azure CLI](/cli/azure/install-azure-cli-apt) 'yı yükleme

1. Betik, sanal makineye bağlanmak için SSH bağlantı dizesini çıktı. Sonraki adım için bağlantı dizesini kopyalayın.

    ![Bir sanal makine için SSH bağlantı dizesinin kopyalanmasını gösteren ekran görüntüsü.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>IoT Edge cihazınıza bağlanma

Sonraki birkaç bölüm oluşturduğumuz Azure sanal makinesini yapılandırır. İlk adım, sanal makineye bağlandır.

1. Bir komut istemi açın ve betik çıktısından kopyaladığınız SSH bağlantı dizesini yapıştırın. Önceki bölümde yer alan PowerShell betiğine verdiğiniz değerlere göre Kullanıcı adı, sonek ve bölge için kendi bilgilerinizi girin.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. Konağın orijinalliğini doğrulamanız istendiğinde, **Evet** girin ve **ENTER**' u seçin.

1. İstendiğinde parolanızı girin.

1. Ubuntu bir hoş geldiniz iletisi görüntüler ve ardından gibi bir istem görmeniz gerekir `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Key Vault sertifikaları indirin

Bu makalenin önceki kısımlarında, IoT Edge cihazımızda ve yaprak cihazımızda kullanılabilir hale getirmek için Key Vault sertifikaları karşıya yükledik. Yaprak cihaz, IoT Hub iletişim kurmak için bir ağ geçidi olarak IoT Edge cihazını kullanan bir aşağı akış aygıtıdır.

Öğreticinin ilerleyen kısımlarında yer aldığı yaprak cihazla ilgileneceğiz. Bu bölümde, sertifikaları IoT Edge cihaza indirin.

1. Linux sanal makinesindeki SSH oturumunda Azure CLı ile Azure 'da oturum açın.

    ```azurecli
    az login
    ```

1. Bir [Microsoft cihaz oturum açma](https://microsoft.com/devicelogin) sayfasında tarayıcı açmanız ve benzersiz bir kod sağlamanız istenecektir. Bu adımları yerel makinenizde gerçekleştirebilirsiniz. Kimlik doğrulamayı bitirdiğinizde tarayıcı penceresini kapatın.

1. Kimlik doğrulaması başarılı olduğunda, Linux VM oturum açıp Azure aboneliklerinizi listelecektir.

1. Azure CLı komutları için kullanmak istediğiniz Azure aboneliğini ayarlayın.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Sertifikalar için VM 'de bir dizin oluşturun.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Anahtar kasasında depoladığınız sertifikaları indirin: New-Edge-Device-Full-Chain. cert. pek, New-Edge-Device. Key. pek ve Azure-iot-test-only. root. ca. cert. ped.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>IoT Edge cihaz yapılandırmasını güncelleştirme

IoT Edge çalışma zamanı, yapılandırmasını sürdürmek için/etc/iotedge/config.exe dosyasını kullanır. Bu dosyada üç bilgi parçasını güncelleştirmemiz gerekiyor:

* **Cihaz bağlantı dizesi**: Bu cihazın kimliğinden gelen bağlantı dizesi IoT Hub
* **Sertifikalar**: aşağı akış cihazlarıyla yapılan bağlantılarda kullanılacak sertifikalar
* **Ana bilgisayar** adı: VM IoT Edge cihazın tam etki alanı adı (FQDN)

IoT Edge sanal makinesini oluşturmak için kullandığımız Ubuntu görüntüsündeki Azure IoT Edge, config. YAML dosyasını bağlantı dizesiyle güncelleştiren bir kabuk betiği ile birlikte gelir.

1. Visual Studio Code, IoT Edge cihazına sağ tıklayın ve ardından **Cihaz bağlantı dizesini Kopyala**' yı seçin.

    ![Visual Studio Code bağlantı dizesinin kopyalanmasını gösteren ekran görüntüsü.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. SSH oturumunuzda, config. YAML dosyasını cihaz bağlantı dizeniz ile güncelleştirmek için komutunu çalıştırın.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Daha sonra, config. YAML dosyasını doğrudan düzenleyerek sertifikaları ve ana bilgisayar adını güncelleştireceğiz.

1. Config. YAML dosyasını açın.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Baştaki **#** ' ı kaldırarak ve yolu aşağıdaki örnekteki gibi görünecek şekilde ayarlayarak config. YAML dosyasının sertifikalar bölümünü güncelleştirin:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    **Sertifikalarda:** Line 'ın önünde boşluk olmadığından ve iç içe sertifikaların her birinin iki boşlukla girintilendiğinden emin olun.

    Nano 'da sağ tıklama, panonuzun içeriğini geçerli imleç konumuna yapıştırır. Dizeyi değiştirmek için, değiştirmek istediğiniz dizeye gitmek üzere klavye oklarınızı kullanın, dizeyi silin ve sonra arabellekten yapıştırmak için sağ tıklayın.

1. Azure portal sanal makinenize gidin. **Genel bakış** bölümünden DNS adını (makinenin FQDN 'si) kopyalayın.

1. FQDN 'yi config. yıml dosyasının hostname bölümüne yapıştırın. Adın tümüyle küçük olduğundan emin olun.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. **CTRL + X**, **Y** ve **ENTER**' ı seçerek dosyayı kaydedin ve kapatın.

1. IoT Edge arka plan programını yeniden başlatın.

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge Daemon 'ın durumunu denetleyin. Komuttan sonra çıkmak için **: q** yazın.

    ```bash
    systemctl status iotedge
    ```

1. Durum durumunda hata ("hata" önekli renkli metin \[ \] ) görürseniz, ayrıntılı hata bilgileri için Daemon günlüklerini inceleyin.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici, her bir makalenin bir önceki bölümde gerçekleştirilen iş üzerinde oluşturulduğu bir küme parçasıdır. Son öğreticiyi tamamlamadan tüm kaynakları temizlemeyi bekleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure VM 'yi IoT Edge saydam bir ağ geçidi olarak yapılandırmayı tamamladık. Key Vault için karşıya yüklemediğimiz test sertifikalarını oluşturarak başladık. Ardından, Azure Marketi 'nden Ubuntu Server 16,04 LTS + Azure IoT Edge Runtime görüntüsüyle VM 'yi dağıtmak için bir betik ve Kaynak Yöneticisi şablonu kullandık. VM çalışır ve çalışır duruma göre SSH aracılığıyla bağlandık. Daha sonra Azure 'da oturum açıyoruz ve Key Vault sertifikalar indirilir. Config. YAML dosyasını güncelleştirerek IoT Edge çalışma zamanının yapılandırmasında birkaç güncelleştirme yaptık.

IoT Edge modülleri derlemek için sonraki makaleye geçin.

> [!div class="nextstepaction"]
> [Özel IoT Edge modülleri oluşturun ve dağıtın](tutorial-machine-learning-edge-06-custom-modules.md)
