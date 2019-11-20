---
title: 'Öğretici: Azure IoT Edge IoT Edge cihaz Machine Learning yapılandırma'
description: 'Öğretici: Linux çalıştıran bir Azure sanal makinesini, saydam bir ağ geçidi olarak davranan Azure IoT Edge bir cihaz olarak yapılandırın.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 714f0c335e2871fa1afe2f99d08870f0e39c488e
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113975"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Öğretici: IoT Edge cihaz yapılandırma

> [!NOTE]
> Bu makale, IoT Edge Azure Machine Learning kullanımı hakkında öğretici için bir serinin bir parçasıdır. Bu makaleye doğrudan ulaşdıysanız, en iyi sonuçlar için serideki [ilk makaleyle](tutorial-machine-learning-edge-01-intro.md) başlamanızı öneririz.

Bu makalede, Linux çalıştıran bir Azure sanal makinesini, saydam bir ağ geçidi olarak davranan Azure IoT Edge bir cihaz olacak şekilde yapılandıracağız. Saydam ağ geçidi yapılandırması, cihazların ağ geçidinin mevcut olduğunu bilmeksizin ağ geçidi üzerinden Azure IoT Hub bağlanmasına olanak sağlar. Aynı zamanda, IoT Hub ' deki cihazlarla etkileşim kuran bir kullanıcı ara ağ geçidi cihazını farkında değildir. Son olarak, ağ geçidine IoT Edge modüller ekleyerek Edge Analytics 'i sistemimize eklemek için saydam ağ geçidini kullanırız.

Bu makaledeki adımlar genellikle bir bulut geliştiricisi tarafından gerçekleştirilir.

## <a name="generate-certificates"></a>İstemci sertifikaları oluşturma

Bir cihazın bir ağ geçidi olarak çalışması için, aşağı akış cihazlarına güvenli bir şekilde bağlanabilme ihtiyacı vardır. Azure IOT Edge cihazları arasında güvenli bağlantılar kurmak için bir ortak anahtar altyapısı (PKI) kullanmanıza olanak tanır. Bu durumda, biz saydam bir ağ geçidi olarak görev yapan bir IOT Edge cihazına bağlamak için bir aşağı akış cihazı vermiş olursunuz. Makul güvenliği korumak için aşağı akış cihazı IoT Edge cihazının kimliğini onaylamasını sağlamalıdır. IoT Edge cihazların sertifikaları kullanma hakkında daha fazla bilgi için bkz. [Azure IoT Edge sertifikası kullanım ayrıntıları](iot-edge-certs.md).

Bu bölümde, oluşturup çalıştırdığımız bir Docker görüntüsü kullanarak otomatik olarak imzalanan sertifikalar oluşturacağız. Windows geliştirme makinesinde sertifikaların oluşturulması için gereken adım sayısını önemli ölçüde azaldığından, bu adımı tamamlamaya yönelik bir Docker görüntüsü kullanmayı seçtik. Bir kapsayıcı kullanmadan sertifikaların nasıl Üretienizle ilgili ayrıntılı bilgi için bkz. [Windows ile sertifika oluşturma](how-to-create-transparent-gateway.md#generate-certificates-with-windows) . [Linux ile sertifika oluşturma](how-to-create-transparent-gateway.md#generate-certificates-with-linux) , Docker görüntüsü ile otomatikleştirdiğimiz yönergeler kümesine sahiptir.

1. Geliştirme sanal makinenizde oturum açın.

2. Bir komut satırı istemi açın ve VM 'de bir dizin oluşturmak için aşağıdaki komutu çalıştırın.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Windows Başlat menüsünden **Docker for Windows** başlatın.

4. Visual Studio Code'u açın.

5. **Dosya** > **klasörü aç...** ' ı seçin ve **C:\\kaynak\\ıotedgeandmlsample\\createcertificates**öğesini seçin.

6. Dockerfile dosyasına sağ tıklayın ve **görüntü oluştur**' u seçin.

7. İletişim kutusunda, görüntü adı ve etiketi için varsayılan değeri kabul edin: **createcertificates: latest**.

8. Oluşturma işleminin tamamlanmasını bekleyin.

    > [!NOTE]
    > Eksik bir ortak anahtar hakkında bir uyarı görebilirsiniz. Bu uyarıyı yoksaymak güvenlidir. Benzer şekilde, bu görüntüde göz ardı etmeniz güvenli olan görüntinizdeki izinleri gözden geçirmenizi/sıfırlamayı öneren bir güvenlik uyarısı görürsünüz.

9. Visual Studio Code Terminal penceresinde createcertificates kapsayıcısını çalıştırın.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker, **c:\\** sürücüsüne erişim ister. **Paylaşma**seçeneğini belirleyin.

11. İstendiğinde kimlik bilgilerinizi sağlayın.

12. Kapsayıcı çalışmayı tamamladıktan sonra, **c:\\Edgecercertificate**' da şu dosyaları denetleyin:

    * c:\\Edgecercertificate\\CERT\\Azure-iot-test-only. root. ca. cert. pem
    * c:\\Edgecercertificate\\CERT\\New-Edge-Device-Full-Chain. cert. pem
    * c:\\Edgecercertificate\\CERT\\New-Edge-Device. cert. pem
    * c:\\Edgecercertificate\\CERT\\New-Edge-Device. cert. pfx
    * c:\\Edgecercertificate\\özel\\New-Edge-Device. Key. pem

## <a name="upload-certificates-to-azure-key-vault"></a>Sertifikaları Azure Key Vault yükleme

Sertifikalarımızı güvenli bir şekilde depolamak ve bunları birden fazla cihazdan erişilebilir hale getirmek için, sertifikaları Azure Key Vault içine yükleyeceğiz. Yukarıdaki listeden görebileceğiniz gibi iki tür sertifika dosyası vardır: PFX ve ped. PFX 'yi, Key Vault yüklenecek Key Vault sertifikaları olarak değerlendireceğiz. PEK dosyaları düz metinlerdir ve bunları Key Vault gizli dizileri olarak değerlendireceğiz. [Azure Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks)çalıştırarak oluşturduğumuz Azure Machine Learning çalışma alanıyla ilişkili Key Vault kullanacağız.

1. [Azure Portal](https://portal.azure.com), Azure Machine Learning çalışma alanınıza gidin.

2. Azure Machine Learning çalışma alanının Genel Bakış sayfasında, **Key Vault**adını bulun.

    ![Anahtar Kasası adını Kopyala](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Geliştirme makinenizde Key Vault için sertifikaları karşıya yükleyin. **\<subscriptionıd\>** ve **\<keyvaultname\>** yerine kaynak bilgilerinizi koyun.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. İstenirse Azure 'da oturum açın.

5. Komut dosyası, yeni Key Vault girdilerini listeleyen çıkışlarla birkaç dakika çalışır.

    ![Key Vault betiği çıkışı](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>IoT Edge cihazı oluşturma

Bir Azure IoT Edge cihazını IoT Hub 'ına bağlamak için önce hub 'da cihaz için bir kimlik oluşturacağız. Bağlantı dizesini buluttaki cihaz kimliğinden alıp IoT Edge cihazımızda çalışma zamanını yapılandırmak için kullanacaksınız. Cihaz yapılandırıldıktan ve hub 'a bağlandıktan sonra, modüller dağıtabilir ve ileti gönderebiliriz. Ayrıca, IoT Hub 'da karşılık gelen cihaz kimliği yapılandırmasını değiştirerek fiziksel IoT Edge cihazının yapılandırmasını değiştirebiliriz.

Bu öğreticide, Visual Studio Code kullanarak yeni cihaz kimliğini oluşturacağız. Ayrıca, [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)veya [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)kullanarak bu adımları tamamlayabilirsiniz.

1. Geliştirme makinenizde Visual Studio Code açın.

2. Visual Studio Code Gezgini görünümünden **Azure IoT Hub cihazları** çerçevesini açın.

3. Üç nokta simgesine tıklayın ve **IoT Edge cihaz oluştur**' u seçin.

4. Cihaza bir ad verin. Kolaylık olması için, **Aaturbofanedgedevice** ' ı kullanarak, daha önce oluşturduğunuz tüm istemci cihazlarından önce, test verilerini göndermek için cihaz bandı aracılığıyla sıralandık.

5. Yeni cihaz, cihaz listesinde görünür.

    ![VS Code Explorer 'da yeni aaTurbofanEdgeDevice 'ı görüntüle](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Azure sanal makinesini dağıtma

Bu öğretici için IoT Edge cihazımızı oluşturmak üzere Azure Marketi 'ndeki [Ubuntu görüntüsündeki Azure IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) kullanırız. Ubuntu görüntüsündeki Azure IoT Edge, en son Azure IoT Edge çalışma zamanını ve bu grubun bağımlılıklarını başlangıçta kurar. VM 'yi bir PowerShell betiği kullanarak dağıyoruz, `Create-EdgeVM.ps1`; Kaynak Yöneticisi şablonu, `IoTEdgeVMTemplate.json`; ve bir kabuk betiği `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Programlı dağıtımı etkinleştir

Bir komut dosyası dağıtımında marketten görüntü kullanmak için görüntü için programlı dağıtımı etkinleştirmemiz gerekir.

1. Azure Portal’da oturum açın.

1. **Tüm Hizmetler**’i seçin.

1. Arama çubuğunda **Market**' i girip seçin.

1. Arama çubuğuna, **Ubuntu üzerinde Azure IoT Edge**girin ve seçin.

1. **Programlı olarak dağıtmak istiyor musunuz? Başlangıç** Köprüsü.

1. **Etkinleştir** düğmesini seçin ve **kaydedin**.

    ![VM için programlı dağıtımı etkinleştir](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Bir başarı bildirimi görürsünüz.

### <a name="create-virtual-machine"></a>Sanal makine oluşturma

Sonra, IoT Edge cihazınız için sanal makineyi oluşturmak üzere betiği çalıştırın.

1. Bir PowerShell penceresi açın ve **Edgevm** dizinine gidin.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Sanal makineyi oluşturmak için betiği çalıştırın.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. İstendiğinde, her bir parametre için değerler sağlayın. Abonelik, kaynak grubu ve konum için, bu öğreticide tüm kaynaklarla aynı şekilde kullanmanızı öneririz.

    * **Azure ABONELIK kimliği**: Azure Portal bulundu
    * **Kaynak grubu adı**: Bu öğreticide kaynakları gruplandırmak için hatırlayabileceğiniz ad
    * **Konum**: sanal makinenin oluşturulacağı Azure konumu. Örneğin, westus2 veya northeurope. Daha fazla bilgi için bkz. tüm [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUserName**: sanal makinede oturum açmak için kullanacağınız yönetici hesabının adı
    * **AdminPassword**: sanal makinede AdminUserName için ayarlanacak parola

4. Betiğin VM 'yi ayarlayabilmesi için, kullanmakta olduğunuz Azure aboneliğiyle ilişkili kimlik bilgileriyle Azure 'da oturum açmanız gerekir.

5. Betik, sanal makinenizin oluşturulmasına ilişkin bilgileri onaylar. Devam etmek için **y** veya **ENTER** ' ı seçin.

6. Komut dosyası aşağıdaki adımları yürüttüğünde birkaç dakika çalışır:

    * Zaten mevcut değilse kaynak grubunu oluşturun
    * Sanal makineyi oluşturma
    * 22 (SSH), 5671 (AMQP), 5672 (AMPQ) ve 443 (SSL) bağlantı noktaları için sanal makine için NSG özel durumları ekleyin
    * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)'yı yükler)

7. Betik, sanal makineye bağlanmak için SSH bağlantı dizesini çıktı. Sonraki adım için bağlantı dizesini kopyalayın.

    ![VM için SSH bağlantı dizesini Kopyala](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>IOT Edge Cihazınızı bağlama

Sonraki birkaç bölüm oluşturduğumuz Azure sanal makinesini yapılandırır. İlk adım, sanal makineye bağlandır.

1. Komut istemi açın ve betik çıktısından kopyaladığınız SSH bağlantı dizesini yapıştırın. Önceki bölümde yer alan PowerShell betiğine verdiğiniz değerlere göre Kullanıcı adı, sonek ve bölge için kendi bilgilerinizi girin.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Konağın orijinalliğini doğrulamanız istendiğinde, **Evet** yazın ve **ENTER**' u seçin.

3. İstendiğinde parolanızı girin.

4. Ubuntu bir hoş geldiniz iletisi görüntüler ve ardından `<username>@<machinename>:~$`gibi bir istem görmeniz gerekir.

## <a name="download-key-vault-certificates"></a>Key Vault sertifikaları indirin

Bu makalenin önceki kısımlarında, IoT Hub ile iletişim kurmak için bir ağ geçidi olarak IoT Edge cihazını kullanan bir aşağı akış cihazı olan IoT Edge cihazımız ve yaprak cihazımız için Key Vault sertifikaları karşıya yükledik. Öğreticinin ilerleyen kısımlarında yer alacak yaprak cihazımız ile ilgileneceğiz. Bu bölümde, sertifikaları IoT Edge cihaza indirin.

1. Linux sanal makinesindeki SSH oturumunda Azure CLı ile Azure 'da oturum açın.

    ```bash
    az login
    ```

1. <https://microsoft.com/devicelogin> için bir tarayıcı açmanız ve benzersiz bir kod sağlamanız istenecektir. Bu adımları yerel makinenizde gerçekleştirebilirsiniz. Kimlik doğrulamayı bitirdiğinizde tarayıcı penceresini kapatın.

1. Kimlik doğrulaması başarılı olduğunda, Linux VM oturum açıp Azure aboneliklerinizi listelecektir.

1. Azure CLı komutları için kullanmak istediğiniz Azure aboneliğini ayarlayın.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Sertifikalar için VM 'de bir dizin oluşturun.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Anahtar kasasında depoladığınız sertifikaları indirin: New-Edge-Device-Full-Chain. cert. pek, New-Edge-Device. Key. pek ve Azure-iot-test-only. root. ca. cert. ped

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>IoT Edge cihaz yapılandırmasını güncelleştirme

IoT Edge çalışma zamanı, yapılandırmasını sürdürmek için/etc/iotedge/config.exe dosyasını kullanır. Bu dosyada üç bilgi parçasını güncelleştirmemiz gerekiyor:

* **Cihaz bağlantı dizesi**: Bu cihazın kimliğinden gelen bağlantı dizesi IoT Hub
* **Sertifikalar:** aşağı akış cihazlarıyla yapılan bağlantılarda kullanılacak sertifikalar
* **Ana bilgisayar adı:** VM IoT Edge cihazın tam etki alanı adı (FQDN).

IoT Edge sanal makinesini oluşturmak için kullandığımız *Ubuntu görüntüsündeki Azure IoT Edge* , bağlantı dizesiyle config. YAML 'yi güncelleştiren bir kabuk betiği ile birlikte gelir.

1. Visual Studio Code IoT Edge cihazına sağ tıklayıp **Cihaz bağlantı dizesini Kopyala**' yı seçin.

    ![Bağlantı dizesini Visual Studio Code Kopyala](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. SSH oturumunuzda, config. YAML dosyasını cihaz bağlantı dizeniz ile güncelleştirmek için komutunu çalıştırın.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Ardından, config. YAML dosyasını doğrudan düzenleyerek sertifikaları ve ana bilgisayar adını güncelleştireceğiz.

1. Config. YAML dosyasını açın.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Önde gelen `#` kaldırarak ve yolun aşağıdaki örnekteki gibi görünmesi için yolu ayarlayarak config. YAML 'nin sertifikalar bölümünü güncelleştirin:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    "Certificates:" nın önünde boşluk olmadığından ve sertifikaların her birinin önünde iki boşluk olmadığından emin olun.

    Nano 'da sağ tıklama, panonuzun içeriğini geçerli imleç konumuna yapıştırır. Dizeyi değiştirmek için, değiştirmek istediğiniz dizeye gitmek üzere klavye oklarını kullanın, dizeyi silin ve sonra arabellekten yapıştırmak için sağ tıklayın.

3. Azure portal sanal makinenize gidin. **Genel bakış** bölümünden DNS adını (makinenin FQDN 'si) kopyalayın.

4. FQDN 'yi config. yıml öğesinin hostname bölümüne yapıştırın. Adın tümüyle küçük olduğundan emin olun.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Dosyayı kaydedin ve kapatın (`Ctrl + X`, `Y`, `Enter`).

6. İotedge cini yeniden başlatın.

    ```bash
    sudo systemctl restart iotedge
    ```

7. IoT Edge Daemon 'ın durumunu denetleyin (komuttan sonra, çıkmak için ": q" yazın).

    ```bash
    systemctl status iotedge
    ```

8. Ayrıntılı hata bilgileri için durum Inceleme günlüklerini Inceleyin ("\[ERROR\]" önekini içeren renkli metin) hatasını görürseniz.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Sonraki adımlar

Azure VM 'yi Azure IoT Edge saydam ağ geçidi olarak yapılandırmayı tamamladık. Azure Key Vault ' ye karşıya yüklediğimiz test sertifikaları oluşturarak başladık. Daha sonra, Azure Marketi 'nden "Ubuntu Server 16,04 LTS + Azure IoT Edge Runtime" görüntüsüyle VM 'yi dağıtmak için bir betik ve Kaynak Yöneticisi şablonu kullandık. Betik, Azure CLı 'yı yüklemenin ek adımını aldı ([Azure CLI 'yi apt Ile yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). VM 'yi çalışır duruma getirerek, Azure 'da oturum açtıktan sonra, Key Vault 'den indirilen sertifikaları indirip, config. YAML dosyasını güncelleştirerek IoT Edge çalışma zamanının yapılandırmasında birkaç güncelleştirme yaptık. Ağ geçidi olarak IoT Edge kullanma hakkında daha fazla bilgi için, bkz. [bir IoT Edge cihazının ağ geçidi olarak nasıl kullanılabileceği](iot-edge-as-gateway.md). IoT Edge cihazının saydam bir ağ geçidi olarak nasıl yapılandırılacağı hakkında daha fazla bilgi için, bkz. bir [IoT Edge cihazı, saydam bir ağ geçidi olarak davranacak şekilde yapılandırma](how-to-create-transparent-gateway.md).

IoT Edge modülleri derlemek için sonraki makaleye geçin.

> [!div class="nextstepaction"]
> [Özel IoT Edge modülleri oluşturun ve dağıtın](tutorial-machine-learning-edge-06-custom-modules.md)
