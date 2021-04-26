---
title: Ubuntu (18,04 x64) simülatörü başvuru Aracısı 'nı kullanarak Azure IoT Hub öğreticisi için cihaz güncelleştirmesi | Microsoft Docs
description: Ubuntu (18,04 x64) simülatörü başvuru Aracısı 'nı kullanarak Azure IoT Hub cihaz güncelleştirmesi ile çalışmaya başlayın.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 90e72bd12d9115e5ff95213428ae4ac37979dcf3
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120104"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Ubuntu (18,04 x64) simülatörü başvuru Aracısı 'nı kullanarak Azure IoT Hub öğreticisi için cihaz güncelleştirmesi

IoT Hub cihaz güncelleştirmesi iki güncelleştirme biçimini destekler: görüntü tabanlı ve paket tabanlı.

Görüntü güncelleştirmeleri, cihazın bitiş durumunda daha yüksek bir güven düzeyi sağlar. Paket ve bağımlılıklarıyla aynı zorluklara sahip olmadığından, bir üretim öncesi ortamı ve üretim ortamı arasında görüntü güncelleştirme sonuçlarını çoğaltmak genellikle daha kolay olur. Atomik doğası nedeniyle, bir A/B yük devretme modelini de kolayca benimseyebilirler.

Bu öğreticide, IoT Hub için cihaz güncelleştirmesini kullanarak uçtan uca bir görüntü tabanlı güncelleştirme tamamlamaya yönelik adımlar adım adım gösterilmektedir. 

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Görüntü indir ve yükle
> * IoT cihazınıza etiket ekleme
> * Bir güncelleştirmeyi içeri aktar
> * Cihaz grubu oluşturma
> * Görüntü güncelleştirme dağıtımı
> * Güncelleştirme dağıtımını izleme

## <a name="prerequisites"></a>Önkoşullar
* Daha önce yapmadıysanız, IoT Hub yapılandırma dahil bir [cihaz güncelleştirme hesabı ve örneği](create-device-update-account.md)oluşturun.

### <a name="download-and-install"></a>İndirme ve yükleme

* PowerShell için az (Azure CLı) cmdlet 'leri:
  * PowerShell 'i açın > Azure CLı 'yi yüklemek için ("güvenilmeyen" kaynaktan yüklenecek istemler için "Y")

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>Windows cihazınızda WSL 'yi etkinleştirme (Linux için Windows alt sistemi)

1. Makinenizde yönetici olarak PowerShell 'i açın ve aşağıdaki komutu çalıştırın (her adımdan sonra yeniden başlatmanız istenebilir; sorulduğunda yeniden başlatın):

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(*Bu adımdan sonra yeniden başlatmanız istenebilir*)

2. Web üzerinde Microsoft Store gidin ve [Ubuntu 18,04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`)'yi kaldırın.

3. "Ubuntu 18,04 LTS" öğesini başlatın ve uygulamasını başlatın.

4. Yüklendiğinde, kök adı (Kullanıcı adı) ve parola ayarlamanız istenir. Etkileyici bir kök adı parolası kullandığınızdan emin olun.

5. PowerShell 'de, Ubuntu 'ı varsayılan Linux dağıtımı olacak şekilde ayarlamak için aşağıdaki komutu çalıştırın:

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. Tüm Linux dağıtımlarını Listele, Ubuntu 'ın varsayılan değer olduğundan emin olun.

```powershell
PS> wsl --list
```

7. Şunları görmeniz gerekir: **Ubuntu-18,04 (varsayılan)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Cihaz güncelleştirme Ubuntu (18,04 x64) simülatörü başvuru aracısını indir

Ubuntu güncelleştirme görüntüsü, yayın notlarındaki *varlıklar* bölümünden [buradan](https://github.com/Azure/iot-hub-device-update/releases)indirilebilir.

Aracının iki sürümü vardır. Görüntü tabanlı senaryo kullandıysanız, Aducıotagentsim-Microsoft-swupdate kullanın ve paket tabanlı senaryo kullandıysanız, Aducıotagentsım-Microsoft-apt kullanın.

## <a name="install-device-update-agent-simulator"></a>Cihaz Güncelleştirme Aracısı simülatörü 'ni yükler

1. Ubuntu WSL 'yi başlatın ve aşağıdaki komutu girin (sonunda fazladan boşluk ve nokta olduğunu unutmayın).

  ```shell
  explorer.exe .
  ```

2. Aducıotagentsim-Microsoft-swupdate (veya Aducıotagentsım-Microsoft-apt) ' i yerel klasörünüzden,/mnt 'nin altında, WSL 'deki giriş klasörünüze indirildiği bir yere kopyalayın.

3. İkili dosyaları yürütülebilir hale getirmek için aşağıdaki komutu çalıştırın.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  veya

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
Azure IoT Hub yazılım için cihaz güncelleştirmesi aşağıdaki lisans koşullarına tabidir:
   * [IoT Hub lisansı için cihaz güncelleştirmesi](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Teslim iyileştirme istemci lisansı](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Aracıyı kullanmadan önce lisans koşullarını okuyun. Yüklemeniz ve kullanılması, bu şartlarınızın kabul edildiğini oluşturur. Lisans koşullarını kabul etmiyorsanız, IoT Hub Aracısı için cihaz güncelleştirmesini kullanmayın.

## <a name="add-device-to-azure-iot-hub"></a>Azure IoT Hub cihaz ekleme

Cihaz Güncelleştirme Aracısı bir IoT cihazında çalışmaya başladıktan sonra cihazın Azure IoT Hub eklenmesi gerekir.  Azure IoT Hub içinden belirli bir cihaz için bağlantı dizesi oluşturulur.

1. Azure portal, cihaz güncelleştirme IoT Hub başlatın.
2. Yeni bir cihaz oluşturun.
3. Sayfanın sol tarafında ' araştırıcılar ' > ' IoT cihazları ' > gidin ve "yeni" seçeneğini belirleyin.
4. ' Cihaz KIMLIĞI ' altında cihaz için bir ad sağlayın--"anahtarları otomatik oluşturma" onay kutusunun seçildiğinden emin olun.
5. ' Kaydet ' seçeneğini belirleyin.
6. Şimdi ' cihazlar ' sayfasına döndürülürsünüz ve oluşturduğunuz cihaz listede olmalıdır. Bu cihazı seçin.
7. Cihaz görünümünde, ' birincil bağlantı dizesi ' ' nin yanındaki ' Kopyala ' simgesini seçin.
8. Aşağıdaki adımlarda, kopyalanmış karakterleri daha sonra kullanmak üzere bir yere yapıştırın. **Bu kopyalanmış dize, cihaz bağlantı dizeniz**.

## <a name="add-connection-string-to-simulator"></a>Simülatör 'e bağlantı dizesi Ekle

Yeni yazılım cihazlarınızda cihaz güncelleştirme aracısını başlatın.

1. Ubuntu başlatın.
2. Cihaz güncelleştirme aracısını çalıştırın ve önceki bölümden, kesme işareti ile Sarmalanan cihaz bağlantı dizesini belirtin:

`<device connection string>`Bağlantı dizeniz ile değiştirin
```shell
./AducIotAgentSim-microsoft-swupdate -c '<device connection string>'
```

veya

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. Yukarı kaydırın ve cihazın "boşta" durumunda olduğunu gösteren dizeyi bulun. "Boşta" durumu, cihazın hizmet komutlarına hazırlandığını belirtir:

```markdown
Agent running. [main]
```

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

5. "Içeri aktarma bildirim dosyası seçin" altında klasör simgesini veya metin kutusunu seçin. Bir dosya Seçicisi iletişim kutusu görürsünüz. Yukarıda oluşturduğunuz Içeri aktarma bildirimini seçin.  Sonra, "bir veya daha fazla güncelleştirme dosyası seçin" altında klasör simgesini veya metin kutusunu seçin. Bir dosya Seçicisi iletişim kutusu görürsünüz. Daha önce indirdiğiniz Ubuntu güncelleştirme görüntüsünü seçin. 

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Güncelleştirme dosyası seçimini gösteren ekran görüntüsü." lightbox="media/import-update/select-update-files.png":::

6. "Bir depolama kapsayıcısı seçin" altında klasör simgesini veya metin kutusunu seçin. Ardından uygun depolama hesabını seçin.

7. Zaten bir kapsayıcı oluşturduysanız, onu yeniden kullanabilirsiniz. (Aksi takdirde, güncelleştirmeler için yeni bir depolama kapsayıcısı oluşturmak üzere "+ kapsayıcı" seçeneğini belirleyin.).  Kullanmak istediğiniz kapsayıcıyı seçin ve "Seç" e tıklayın.
  
  :::image type="content" source="media/import-update/container.png" alt-text="Kapsayıcı seçimini gösteren ekran görüntüsü." lightbox="media/import-update/container.png":::

8. İçeri aktarma işlemini başlatmak için "Gönder" i seçin.

9. İçeri aktarma işlemi başlar ve ekran "Içeri aktarma geçmişi" bölümünde değişir. İçeri aktarma işlemi tamamlanana kadar ilerlemeyi görüntülemek için "Yenile" yi seçin. Güncelleştirme boyutuna bağlı olarak, bu işlem birkaç dakika içinde tamamlanabilir ancak daha uzun sürebilir.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Güncelleştirme içeri aktarma sırasını gösteren ekran görüntüsü." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Durum sütunu içeri aktarma işleminin başarılı olduğunu gösteriyorsa, "dağıtıma hazırlanıyor" üst bilgisini seçin. İçeri aktarılan güncelleştirmenizi şimdi listede görmeniz gerekir.

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

Artık Ubuntu (18,04 x64) simülatörü başvuru Aracısı 'nı kullanarak IoT Hub için cihaz güncelleştirmesini kullanarak başarılı bir uçtan uca görüntü güncelleştirmesini tamamladınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, cihaz güncelleştirme hesabınızı, örneğinizi, IoT Hub ve IoT cihazınızı temizleyin. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Sorun giderme](troubleshoot-device-update.md)

