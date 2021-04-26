---
title: Raspberry Pi 3 B + başvuru yocto görüntüsünü kullanarak Azure IoT Hub öğreticisi için cihaz güncelleştirmesi | Microsoft Docs
description: Raspberry Pi 3 B + başvuru yocto görüntüsünü kullanarak Azure IoT Hub cihaz güncelleştirmesi ile çalışmaya başlayın.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 143a7c411bea6a451645c860b7b5d12d2aa8d9f5
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121345"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>Raspberry Pi 3 B + başvuru görüntüsünü kullanarak Azure IoT Hub öğreticisi için cihaz güncelleştirmesi

IoT Hub cihaz güncelleştirmesi iki güncelleştirme biçimini destekler: görüntü tabanlı ve paket tabanlı.

Görüntü güncelleştirmeleri, cihazın bitiş durumunda daha yüksek bir güven düzeyi sağlar. Paket ve bağımlılıklarıyla aynı zorluklara sahip olmadığından, bir üretim öncesi ortamı ve üretim ortamı arasında görüntü güncelleştirme sonuçlarını çoğaltmak genellikle daha kolay olur. Atomik doğası nedeniyle, bir A/B yük devretme modelini de kolayca benimseyebilirler.

Bu öğreticide, IoT Hub için cihaz güncelleştirmesini kullanarak uçtan uca bir görüntü tabanlı güncelleştirme tamamlamaya yönelik adımlar adım adım gösterilmektedir. 

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Resmi indir
> * IoT cihazınıza etiket ekleme
> * Bir güncelleştirmeyi içeri aktar
> * Cihaz grubu oluşturma
> * Görüntü güncelleştirme dağıtımı
> * Güncelleştirme dağıtımını izleme

## <a name="prerequisites"></a>Önkoşullar
* Daha önce yapmadıysanız, IoT Hub yapılandırma dahil bir [cihaz güncelleştirme hesabı ve örneği](create-device-update-account.md)oluşturun.

## <a name="download-image"></a>Resmi indir

Belirli bir [cihaz güncelleştirmesi GitHub sürümünde](https://github.com/Azure/iot-hub-device-update/releases)"varlıkların" bir parçası olarak kullanılabilecek üç görüntü vardır. Ana görüntü (ADU-Base-Image) ve tek bir güncelleştirme görüntüsü (ADU-Update-image), cihazdaki SD kartını yakıp sönmenize gerek kalmadan farklı sürümlere piyasaya çıkarma deneyebilmeniz için sağlanır. Bunu yapmak için, güncelleştirme görüntülerini içeri aktarma işleminin bir parçası olarak IoT Hub hizmeti için cihaz güncelleştirmesine yüklemeniz gerekir.

## <a name="flash-sd-card-with-image"></a>Görüntüyle birlikte Flash SD kartı

En sevdiğiniz işletim sistemi yanıp sönme aracını kullanarak, Raspberry PI 3 B + cihazında kullanılacak SD kartına cihaz güncelleştirme temel görüntüsünü (ADU-Base-Image) yüklersiniz.

### <a name="using-bmaptool-to-flash-sd-card"></a>Bmaptool 'ı Flash SD kartına kullanma

1. Henüz yapmadıysanız, yardımcı programı ' nı yüklemelisiniz `bmaptool` .

   ```shell
   sudo apt-get install bmap-tools
   ```

2. İçindeki SD kartının yolunu bulun `/dev` . Yol veya gibi görünmelidir `/dev/sd*` `/dev/mmcblk*` . `dmesg`Doğru yolu bulmaya yardımcı olması için yardımcı programını kullanabilirsiniz.

3. Tüm bağlı bölümleri yanıp sönmeye başlamadan önce çıkarmanız gerekir.

   ```shell
   sudo umount /dev/<device>
   ```

4. Cihaza yazma izinleriniz olduğundan emin olun.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. İsteğe bağlı. Daha hızlı yanıp sönmek için, BMAP dosyasını görüntü dosyasıyla birlikte indirin ve aynı dizine yerleştirin.

6. SD kartını yakıp söndür.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
Azure IoT Hub yazılım için cihaz güncelleştirmesi aşağıdaki lisans koşullarına tabidir:
   * [IoT Hub lisansı için cihaz güncelleştirmesi](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Teslim iyileştirme istemci lisansı](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Aracıyı kullanmadan önce lisans koşullarını okuyun. Yüklemeniz ve kullanılması, bu şartlarınızın kabul edildiğini oluşturur. Lisans koşullarını kabul etmiyorsanız, IoT Hub Aracısı için cihaz güncelleştirmesini kullanmayın.

## <a name="create-device-in-iot-hub-and-get-connection-string"></a>IoT Hub cihaz oluşturma ve bağlantı dizesi al

Artık cihazın Azure IoT Hub eklenmesi gerekiyor.  Azure IoT Hub içinden cihaz için bir bağlantı dizesi oluşturulacaktır.

1. Azure portal Azure IoT Hub başlatın.
2. Yeni bir cihaz oluşturun.
3. Sayfanın sol tarafında ' araştırıcılar ' > ' IoT cihazları ' > gidin ve "yeni" seçeneğini belirleyin.
4. ' Cihaz KIMLIĞI ' altında cihaz için bir ad sağlayın--"anahtarları otomatik oluşturma" onay kutusunun seçildiğinden emin olun.
5. ' Kaydet ' seçeneğini belirleyin.
6. Artık ' cihazlar ' sayfasına döndürülecektir ve oluşturduğunuz cihaz listede olmalıdır. Bu cihazı seçin.
7. Cihaz görünümünde, ' birincil bağlantı dizesi ' ' nin yanındaki ' Kopyala ' simgesini seçin.
8. Aşağıdaki adımlarda, kopyalanmış karakterleri daha sonra kullanmak üzere bir yere yapıştırın.
   **Bu kopyalanmış dize, cihaz bağlantı dizeniz**.

## <a name="provision-connection-string-on-sd-card"></a>SD Card üzerinde bağlantı dizesi sağla

1. Raspberry pi3 'in ağa bağlı olduğundan emin olun.
2. PowerShell 'de, cihazda SSH için aşağıdaki komutu kullanın
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. ' Root ' olarak oturum açma girin ve parola boş olarak bırakılmalıdır.
5. Cihaza başarıyla SSH oluşturduktan sonra aşağıdaki komutları çalıştırın
 
`<device connection string>`Bağlantı dizeniz ile değiştirin
 ```markdown
    echo "connection_string=<device connection string>" > /adu/adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> /adu/adu-conf.txt
    echo "aduc_model=RefDevice" >> /adu/adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>Cihazı cihaz güncelleştirme IoT Hub bağlayın

1. Sayfanın sol tarafında, ' araştırıcılar ' altında ' IoT cihazları ' öğesini seçin.
2. Cihaz adınızın bulunduğu bağlantıyı seçin.
3. Sayfanın üst kısmında, ' Device Ikizi ' seçeneğini belirleyin.
4. Device ikizi özelliklerinin ' raporlanan ' bölümünde Linux çekirdek sürümü ' ne bakın.
Cihaz güncelleştirmesinden bir güncelleştirme almamış olan yeni bir cihaz için [DeviceManagement: Deviceınformation: 1. swVersion](device-update-plug-and-play.md) değeri cihazda çalışan üretici yazılımı sürümünü temsil eder.  Bir cihaza güncelleştirme uygulandıktan sonra cihaz güncelleştirme, cihazda çalışan bellenim sürümünü temsil etmek için [AzureDeviceUpdateCore: ClientMetadata: 4. ınstalınstalıd](device-update-plug-and-play.md) Özellik değeri kullanır.
5. Taban ve güncelleştirme görüntü dosyalarının dosya adında sürüm numarası vardır.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

Aşağıdaki güncelleştirme Içeri aktarma adımında bu sürüm numarasını kullanın.

## <a name="add-a-tag-to-your-device"></a>Cihazınıza bir etiket ekleyin

1. [Azure Portal](https://portal.azure.com) açın ve IoT Hub gidin.

2. Sol gezinti bölmesindeki ' IoT cihazları ' veya ' IoT Edge ' içinden IoT cihazınızı bulun ve cihaz Ikizi veya modül Ikizi ' a gidin.

3. Cihaz Güncelleştirme Aracısı modülünün Ikizi modülünde, var olan tüm cihaz güncelleştirme etiketi değerlerini null olarak ayarlayarak silin. Cihaz kimliği ile cihaz güncelleştirme aracısı kullanıyorsanız, bu değişiklikleri cihaz Ikizi üzerinde yapın.

4. Aşağıda gösterildiği gibi yeni bir cihaz güncelleştirme etiketi değeri ekleyin.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Güncelleştirmeyi içeri aktar

1. Bu [yönergeleri](import-update.md)Izleyerek Içeri aktarma bildirimi oluşturun.
2. Sol taraftaki Gezinti çubuğundan otomatik cihaz yönetimi altında cihaz güncelleştirmeleri seçeneğini belirleyin.
3. Güncelleştirmeler sekmesini seçin.
4. "+ Yeni güncelleştirme al" seçeneğini belirleyin.
5. "Içeri aktarma bildirim dosyası seçin" altında klasör simgesini veya metin kutusunu seçin. Bir dosya Seçicisi iletişim kutusu görürsünüz. Yukarıda oluşturduğunuz Içeri aktarma bildirimini seçin.  Sonra, "bir veya daha fazla güncelleştirme dosyası seçin" altında klasör simgesini veya metin kutusunu seçin. Bir dosya Seçicisi iletişim kutusu görürsünüz. IoT cihazlarınıza dağıtmak istediğiniz güncelleştirme dosyasını seçin.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Güncelleştirme dosyası seçimini gösteren ekran görüntüsü." lightbox="media/import-update/select-update-files.png":::

5. "Bir depolama kapsayıcısı seçin" altında klasör simgesini veya metin kutusunu seçin. Ardından uygun depolama hesabını seçin.

6. Zaten bir kapsayıcı oluşturduysanız, onu yeniden kullanabilirsiniz. (Aksi takdirde, güncelleştirmeler için yeni bir depolama kapsayıcısı oluşturmak üzere "+ kapsayıcı" seçeneğini belirleyin.).  Kullanmak istediğiniz kapsayıcıyı seçin ve "Seç" e tıklayın.
  
  :::image type="content" source="media/import-update/container.png" alt-text="Kapsayıcı seçimini gösteren ekran görüntüsü." lightbox="media/import-update/container.png":::

7. İçeri aktarma işlemini başlatmak için "Gönder" i seçin.

8. İçeri aktarma işlemi başlar ve ekran "Içeri aktarma geçmişi" bölümünde değişir. İçeri aktarma işlemi tamamlanana kadar ilerlemeyi görüntülemek için "Yenile" yi seçin. Güncelleştirme boyutuna bağlı olarak, bu işlem birkaç dakika içinde tamamlanabilir ancak daha uzun sürebilir.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Güncelleştirme içeri aktarma sırasını gösteren ekran görüntüsü." lightbox="media/import-update/update-publishing-sequence-2.png":::

9. Durum sütunu içeri aktarma işleminin başarılı olduğunu gösteriyorsa, "dağıtıma hazırlanıyor" üst bilgisini seçin. İçeri aktarılan güncelleştirmenizi şimdi listede görmeniz gerekir.

Güncelleştirmeleri içeri aktarma hakkında [daha fazla bilgi edinin](import-update.md) .

## <a name="create-update-group"></a>Güncelleştirme grubu oluştur

1. Daha önce cihaz güncelleştirme örneğinizi bağladığınız IoT Hub gidin.

2. Sol taraftaki Gezinti çubuğundan otomatik cihaz yönetimi altında cihaz güncelleştirmeleri seçeneğini belirleyin.

3. Sayfanın üst kısmındaki gruplar sekmesini seçin. 

4. Yeni bir grup oluşturmak için Ekle düğmesini seçin.

5. Önceki adımda oluşturduğunuz IoT Hub etiketini listeden seçin. Güncelleştirme grubu oluştur ' u seçin.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Etiket seçimini gösteren ekran görüntüsü." lightbox="media/create-update-group/select-tag.PNG":::

Etiket ekleme ve güncelleştirme grupları oluşturma hakkında [daha fazla bilgi edinin](create-update-group.md)


## <a name="deploy-update"></a>Güncelleştirme dağıt

1. Grup oluşturulduktan sonra, bekleyen güncelleştirmeler altındaki güncelleştirmeye yönelik bir bağlantı ile cihaz grubunuz için kullanılabilen yeni bir güncelleştirme görmeniz gerekir. Bir kez yenilemeniz gerekebilir. 

2. Kullanılabilir güncelleştirmeye tıklayın.

3. Hedef grup olarak doğru grubun seçili olduğunu onaylayın. Dağıtımınızı zamanlayın ve ardından güncelleştirme Dağıt ' ı seçin.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Güncelleştirme seçin" lightbox="media/deploy-update/select-update.png":::

4. Uyumluluk grafiğini görüntüleyin. Güncelleştirmenin devam etmekte olduğunu görmeniz gerekir. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Güncelleştirme devam ediyor" lightbox="media/deploy-update/update-in-progress.png":::

5. Cihazınız başarıyla güncelleştirildikten sonra, uyumluluk grafiği ve dağıtım ayrıntıları güncelinizi aynı şekilde yansıtacak şekilde görmeniz gerekir. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Güncelleştirme başarılı oldu" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Güncelleştirme dağıtımını izleme

1. Sayfanın üst kısmındaki dağıtımlar sekmesini seçin.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Dağıtımlar sekmesi" lightbox="media/deploy-update/deployments-tab.png":::

2. Dağıtım ayrıntılarını görüntülemek için oluşturduğunuz dağıtımı seçin.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Dağıtım ayrıntıları" lightbox="media/deploy-update/deployment-details.png":::

3. En son durum ayrıntılarını görüntülemek için Yenile ' yi seçin. Durum başarılı olana kadar bu işleme devam edin.

Artık bir Raspberry Pi 3 B + cihazında IoT Hub için cihaz güncelleştirmesini kullanarak başarılı bir uçtan uca görüntü güncelleştirmesini tamamladınız. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, cihaz güncelleştirme hesabınızı, örneğinizi, IoT Hub ve IoT cihazınızı temizleyin. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Simülatör başvuru Aracısı](device-update-simulator.md)
