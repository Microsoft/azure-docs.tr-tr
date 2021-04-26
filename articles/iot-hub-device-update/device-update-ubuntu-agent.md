---
title: Ubuntu Server 18,04 x64 paket aracısını kullanarak Azure IoT Hub öğreticisi için cihaz güncelleştirmesi | Microsoft Docs
description: Ubuntu Server 18,04 x64 paket aracısını kullanarak Azure IoT Hub cihaz güncelleştirmesi ile çalışmaya başlayın.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 6464ad632251053ac481fbd1f6a3e1197aa470df
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121311"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Ubuntu Server 18,04 x64 üzerinde paket aracısını kullanarak Azure IoT Hub öğreticisi için cihaz güncelleştirmesi

IoT Hub cihaz güncelleştirmesi iki güncelleştirme biçimini destekler: görüntü tabanlı ve paket tabanlı.

Paket tabanlı güncelleştirmeler, cihazdaki yalnızca belirli bir bileşeni veya uygulamayı değiştirecek olan güncelleştirmeler için geçerlidir. Paket tabanlı güncelleştirmeler, bant genişliğinin daha düşük tüketimine yol açabilir ve güncelleştirmeyi indirme ve yükleme süresini azaltmaya yardımcı olur. Paket güncelleştirmeleri genellikle bir güncelleştirme uygulanırken cihazların daha az kapalı kalma süresine izin verir ve görüntü oluşturma yükünden kaçınır.

Bu uçtan uca öğreticide, cihaz güncelleştirme paketi Aracısı 'nı kullanarak Ubuntu Server 18,04 x64 üzerinde Azure IoT Edge güncelleştirme işlemi adım adım açıklanmaktadır. Öğreticide IoT Edge güncelleştirme, benzer adımları kullanarak, kullandığı kapsayıcı altyapısı gibi diğer paketleri güncelleştirebilirsiniz.

Bu öğreticideki araçlar ve kavramlar, farklı bir işletim sistemi platformu yapılandırması kullanmayı planlıyorsanız bile geçerlidir. Bir uçtan uca güncelleştirme işlemine giriş yapın, ardından ayrıntıları incelemek için tercih ettiğiniz güncelleştirme ve işletim sistemi platformu formunuzu seçin.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Cihaz güncelleştirme aracısını ve bağımlılıklarını indirin ve yükleyin
> * Cihazınıza bir etiket ekleyin
> * Bir güncelleştirmeyi içeri aktar
> * Cihaz grubu oluşturma
> * Paket güncelleştirmesi dağıtma
> * Güncelleştirme dağıtımını izleme

## <a name="prerequisites"></a>Önkoşullar

* Daha önce yapmadıysanız, IoT Hub yapılandırma dahil bir [cihaz güncelleştirme hesabı ve örneği](create-device-update-account.md)oluşturun.
* [IoT Edge cihazının bağlantı dizesi](../iot-edge/how-to-register-device.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-connection-strings).

## <a name="prepare-a-device"></a>Cihaz hazırlama
### <a name="using-the-automated-deploy-to-azure-button"></a>Otomatik olarak Azure 'a dağıt düğmesini kullanma

Bu öğreticide kolaylık sağlaması için, bir Ubuntu 18,04 LTS sanal makinesini hızlıca ayarlamanıza yardımcı olması amacıyla bir [Cloud-init](../virtual-machines/linux/using-cloud-init.md)tabanlı [Azure Resource Manager şablonu](../azure-resource-manager/templates/overview.md) kullanılmaktadır. Hem Azure IoT Edge çalışma zamanını hem de cihaz güncelleştirme paketi aracısını yükleyip, daha sonra sağladığınız bir IoT Edge cihazının (Önkoşul) cihaz bağlantı dizesini kullanarak cihazı sağlama bilgileriyle otomatik olarak yapılandırır. Azure Resource Manager şablonu, kurulumu tamamlamaya yönelik bir SSH oturumu başlatma gereksinimini de önler.

1. Başlangıç olarak aşağıdaki düğmeye tıklayın:

   [![iotedge-vm-deploy için Azure Düğmesini dağıtma](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fdevice-update-tutorial%2FedgeDeploy.json)

1. Yeni başlatılan penceresinde, kullanılabilir form alanlarını girin:

    > [!div class="mx-imgBorder"]
    > [![iotedge-vm-deploy şablonunu gösteren ekran görüntüsü](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Abonelik**: sanal makineyi dağıtmak Için etkin Azure aboneliği.

    **Kaynak grubu**: sanal makineyi ve ilişkili kaynaklarını içeren, mevcut veya yeni oluşturulmuş bir kaynak grubu.

    **DNS etiketi ön eki**: sanal makinenin ana bilgisayar adına önek olarak eklemek için seçtiğiniz bir değer.

    **Yönetici Kullanıcı adı**: dağıtımda kök ayrıcalıklar sağlanacak olan Kullanıcı adı.

    **Cihaz bağlantı dizesi**: hedeflediğiniz [IoT Hub](../iot-hub/about-iot-hub.md)içinde oluşturulmuş bir cihaz için bir [Cihaz bağlantı dizesi](../iot-edge/how-to-register-device.md) .

    **VM boyutu**: dağıtılacak sanal makine [boyutu](../cloud-services/cloud-services-sizes-specs.md)

    **Ubuntu IŞLETIM sistemi sürümü**: temel sanal makineye yüklenecek Ubuntu işletim sistemi sürümü. Varsayılan değeri değiştirmeden bırakın, çünkü Ubuntu 18,04-LTS olarak ayarlanır.

    **Konum**: sanal makinenin içine dağıtılacağı [coğrafi bölge](https://azure.microsoft.com/global-infrastructure/locations/) , bu değer varsayılan olarak seçilen kaynak grubunun konumudur.

    **Kimlik doğrulama türü**: tercihlerinize göre **sshpublickey** veya **Password** öğesini seçin.

    **Yönetici parolası veya anahtarı**: kimlik doğrulama türü seçimine bağlı olarak SSH ortak anahtarının değeri veya parolanın değeri.

    Tüm alanlar doldurulduktan sonra, koşulları kabul etmek için sayfanın altındaki onay kutusunu seçin ve dağıtımı başlatmak için **satın al** ' ı seçin.

1. Dağıtımın başarıyla tamamlandığını doğrulayın. Yükleme sonrası ve yapılandırmanın IoT Edge ve cihaz paketi güncelleştirme aracısını yüklemeyi tamamlaması için dağıtım tamamlandıktan birkaç dakika bekleyin.

   Seçilen kaynak grubuna bir sanal makine kaynağı dağıtılmış olmalıdır.  Biçimde olması gereken makine adını unutmayın `vm-0000000000000` . Ayrıca ilişkili **DNS Adı**’nı da not alın; bu ad `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com biçiminde olmalıdır.

    **DNS Adı**, Azure portalında yeni dağıtılan sanal makinenin **Genel Bakış** bölümünden alınabilir.

    > [!div class="mx-imgBorder"]
    > [![İotedge VM 'sinin DNS adını gösteren ekran görüntüsü](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > Kurulumdan sonra bu VM 'ye SSH eklemek istiyorsanız, ilişkili **DNS adını** komutuyla kullanın: `ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>Seçim Bir cihazı el ile hazırlama
[Cloud-init betiğinin](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt)otomatikleştirilmiş adımlarına benzer şekilde, aşağıdaki adımları izleyerek cihazı yüklemek ve yapılandırmak için el ile yapılan adımlar verilmektedir. Bu adımlar, fiziksel bir cihazı hazırlamak için kullanılabilir.

1. [Azure IoT Edge çalışma zamanını yüklemek](../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true)için yönergeleri izleyin.
   > [!NOTE]
   > Cihaz güncelleştirme paketi Aracısı IoT Edge bağımlı değildir. Ancak, bir kimlik elde etmek ve IoT Hub bağlanmak için IoT Edge (1.2.0 ve üzeri) ile yüklenen IoT kimlik hizmeti cinini kullanır.
   >
   > Bu öğreticide kapsanmamakla birlikte, [IoT kimlik hizmeti cinini Linux tabanlı IoT cihazlarına tek başına yüklenebilir](https://azure.github.io/iot-identity-service/packaging.html). Yükleme sırası önemli. IoT kimlik hizmetinden _sonra_ cihaz güncelleştirme paketi aracısının yüklü olması gerekir. Aksi takdirde, paket Aracısı IoT Hub bağlantı kurmak için yetkili bir bileşen olarak kaydedilmeyecektir.

1. Ardından, cihaz Güncelleştirme Aracısı. deb paketlerini yükler.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

Azure IoT Hub yazılım paketleri için cihaz güncelleştirmesi, aşağıdaki lisans koşullarına tabidir:
  * [IoT Hub lisansı için cihaz güncelleştirmesi](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [Teslim iyileştirme istemci lisansı](https://github.com/microsoft/do-client/blob/main/LICENSE)

Paket kullanmadan önce lisans koşullarını okuyun. Bir paketi yüklemeniz ve kullanmanız, bu şartlarınızın kabul edildiğini oluşturur. Lisans koşullarını kabul etmiyorsanız, bu paketi kullanmayın.

## <a name="add-a-tag-to-your-device"></a>Cihazınıza bir etiket ekleyin

1. [Azure Portal](https://portal.azure.com) açın ve IoT Hub gidin.

2. Sol gezinti bölmesindeki ' IoT Edge ' içinden, IoT Edge cihazınızı bulun ve cihaz Ikizi veya modül Ikizi sayfasına gidin.

3. Cihaz Güncelleştirme Aracısı modülünün Ikizi modülünde, var olan tüm cihaz güncelleştirme etiketi değerlerini null olarak ayarlayarak silin. Cihaz kimliği ile cihaz güncelleştirme aracısı kullanıyorsanız, bu değişiklikleri cihaz Ikizi üzerinde yapın.

4. Aşağıda gösterildiği gibi yeni bir cihaz güncelleştirme etiketi değeri ekleyin.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>Güncelleştirmeyi içeri aktar

1. GitHub 'daki [cihaz güncelleştirme sürümleri](https://github.com/Azure/iot-hub-device-update/releases) ' ne gidin ve "varlıklar" açılan düğmesine tıklayın.

3. `Edge.package.update.samples.zip`Üzerine tıklayarak indirin.

5. Güncelleştirme örneğini ve buna karşılık gelen içeri aktarma bildirimlerini bulacak şekilde klasörün içeriğini ayıklayın. 

2. Azure portal ' de, IoT Hub sol taraftaki Gezinti çubuğundan otomatik cihaz yönetimi altında cihaz güncelleştirmeleri seçeneğini belirleyin.

3. Güncelleştirmeler sekmesini seçin.

4. "+ Yeni güncelleştirme al" seçeneğini belirleyin.

5. "Içeri aktarma bildirim dosyası seçin" altında klasör simgesini veya metin kutusunu seçin. Bir dosya Seçicisi iletişim kutusu görürsünüz. `sample-1.0.1-aziot-edge-importManifest.json`Daha önce indirdiğiniz klasörden içeri aktarma bildirimi ' ni seçin. Sonra, "bir veya daha fazla güncelleştirme dosyası seçin" altında klasör simgesini veya metin kutusunu seçin. Bir dosya Seçicisi iletişim kutusu görürsünüz. `sample-1.0.1-aziot-edge-apt-manifest.json`Daha önce indirdiğiniz klasörden apt bildirim güncelleştirme dosyasını seçin.
Bu güncelleştirme, `aziot-identity-service` ve `aziot-edge` paketlerini cihazınızda 1.2.0 ~ RC4-1 sürümüne güncelleştirecek.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Güncelleştirme dosyası seçimini gösteren ekran görüntüsü." lightbox="media/import-update/select-update-files.png":::

6. "Bir depolama kapsayıcısı seçin" altında klasör simgesini veya metin kutusunu seçin. Ardından uygun depolama hesabını seçin.

7. Zaten bir kapsayıcı oluşturduysanız, onu yeniden kullanabilirsiniz. (Aksi takdirde, güncelleştirmeler için yeni bir depolama kapsayıcısı oluşturmak üzere "+ kapsayıcı" seçeneğini belirleyin.).  Kullanmak istediğiniz kapsayıcıyı seçin ve "Seç" e tıklayın.

   :::image type="content" source="media/import-update/container.png" alt-text="Kapsayıcı seçimini gösteren ekran görüntüsü." lightbox="media/import-update/container.png":::

8. İçeri aktarma işlemini başlatmak için "Gönder" i seçin.

9. İçeri aktarma işlemi başlar ve ekran "Içeri aktarma geçmişi" bölümünde değişir. İçeri aktarma işlemi tamamlanana kadar ilerlemeyi görüntülemek için "Yenile" yi seçin. Güncelleştirme boyutuna bağlı olarak, içeri aktarma işlemi birkaç dakika içinde tamamlanabilir, ancak daha uzun sürebilir.

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Güncelleştirme içeri aktarma sırasını gösteren ekran görüntüsü." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Durum sütunu içeri aktarma işleminin başarılı olduğunu gösteriyorsa, "dağıtıma hazırlanıyor" üst bilgisini seçin. İçeri aktarılan güncelleştirmenizi şimdi listede görmeniz gerekir.

Güncelleştirmeleri içeri aktarma hakkında [daha fazla bilgi edinin](import-update.md) .

## <a name="create-update-group"></a>Güncelleştirme grubu oluştur

1. Daha önce cihaz güncelleştirme örneğinizi bağladığınız IoT Hub gidin.

1. Sol taraftaki Gezinti çubuğundan otomatik cihaz yönetimi altında cihaz güncelleştirmeleri seçeneğini belirleyin.

1. Sayfanın üst kısmındaki gruplar sekmesini seçin.

1. Yeni bir grup oluşturmak için Ekle düğmesini seçin.

1. Önceki adımda oluşturduğunuz IoT Hub etiketini listeden seçin. Güncelleştirme grubu oluştur ' u seçin.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Etiket seçimini gösteren ekran görüntüsü." lightbox="media/create-update-group/select-tag.PNG":::

Etiket ekleme ve güncelleştirme grupları oluşturma hakkında [daha fazla bilgi edinin](create-update-group.md)

## <a name="deploy-update"></a>Güncelleştirme dağıt

1. Grup oluşturulduktan sonra, _kullanılabilir güncelleştirmeler_ sütununda güncelleştirme bağlantısı ile cihaz grubunuz için kullanılabilen yeni bir güncelleştirme görmeniz gerekir. Bir kez yenilemeniz gerekebilir.

1. Kullanılabilir güncelleştirme bağlantısına tıklayın.

1. Hedef grup olarak doğru grubun seçili olduğunu onaylayın ve dağıtımınızı zamanlayın

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Güncelleştirme seçin" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > Varsayılan olarak, başlangıç tarihi/saati geçerli zamandan 24 saat olur. Dağıtımın daha önce başlamasını istiyorsanız farklı bir tarih/saat seçtiğinizden emin olun.

1. Güncelleştirme Dağıt ' ı seçin.

1. Uyumluluk grafiğini görüntüleyin. Güncelleştirmenin devam etmekte olduğunu görmeniz gerekir. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Güncelleştirme devam ediyor" lightbox="media/deploy-update/update-in-progress.png":::

1. Cihazınız başarıyla güncelleştirildikten sonra, uyumluluk grafiği ve dağıtım ayrıntıları güncelinizi aynı şekilde yansıtacak şekilde görmeniz gerekir. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Güncelleştirme başarılı oldu" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Güncelleştirme dağıtımını izleme

1. Sayfanın üst kısmındaki dağıtımlar sekmesini seçin.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Dağıtımlar sekmesi" lightbox="media/deploy-update/deployments-tab.png":::

1. Dağıtım ayrıntılarını görüntülemek için oluşturduğunuz dağıtımı seçin.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Dağıtım ayrıntıları" lightbox="media/deploy-update/deployment-details.png":::

1. En son durum ayrıntılarını görüntülemek için Yenile ' yi seçin. Durum başarılı olana kadar bu işleme devam edin.

Artık bir Ubuntu Server 18,04 x64 cihazında IoT Hub için cihaz güncelleştirmesi 'ni kullanarak başarılı bir uçtan uca paket güncelleştirmesini tamamladınız. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, cihaz güncelleştirme hesabınızı, örneğinizi, IoT Hub ve IoT Edge cihazını (VM 'yi Azure 'a dağıt düğmesine kullanarak oluşturduysanız) temizleyin. Bunu, her bir kaynağa giderek ve "Sil" seçeneğini belirleyerek yapabilirsiniz. Cihaz güncelleştirme hesabını temizlemeden önce bir cihaz güncelleştirme örneği temizlemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Raspberry PI 3 ' te görüntü güncelleştirme B + öğretici](device-update-raspberry-pi.md)
