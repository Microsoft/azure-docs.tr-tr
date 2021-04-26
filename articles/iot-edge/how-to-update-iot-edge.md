---
title: Cihazlarda IoT Edge sürümü güncelleştirme-Azure IoT Edge | Microsoft Docs
description: Güvenlik cini ve IoT Edge çalışma zamanının en son sürümlerini çalıştırmak için IoT Edge cihazları güncelleştirme
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e5034c228a354c98b5792492d484da9eb10b8cf2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310861"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>IoT Edge güvenlik daemon'ını ve çalışma zamanını güncelleştirme

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge hizmeti yeni sürümleri yayındıkça, en son özellikler ve güvenlik geliştirmeleri için IoT Edge cihazlarınızı güncellemek isteyeceksiniz. Bu makalede yeni bir sürüm kullanılabilir olduğunda IoT Edge cihazlarınızı güncelleştirme hakkında bilgi sağlanır.

Daha yeni bir sürüme geçmek istiyorsanız, bir IoT Edge cihazının iki bileşeni de güncelleştirilmeleri gerekir. Birincisi, cihazda çalışan ve cihaz başladığında çalışma zamanı modüllerini Başlatan güvenlik cini ' dir. Şu anda güvenlik arka plan programı yalnızca cihazın kendisinden güncelleştirilebilen olabilir. İkinci bileşen, IoT Edge hub ve IoT Edge aracı modülleriyle oluşturulan çalışma zamanı. Dağıtımınızı nasıl yapılandırdığınıza bağlı olarak, çalışma zamanı cihazdan veya uzaktan güncelleştirilebilen olabilir.

En son Azure IoT Edge sürümünü bulmak için, bkz. [Azure IoT Edge yayınlar](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Güvenlik cini güncelleştirme

IoT Edge güvenlik arka plan programı, IoT Edge cihazında Paket Yöneticisi kullanılarak güncelleştirilmesi gereken yerel bir bileşendir.

Komutunu kullanarak cihazınızda çalışan güvenlik arka plan programının sürümünü denetleyin `iotedge version` .

>[!IMPORTANT]
>Bir cihazı 1,0 veya 1,1 sürümü 1,2 sürümüne güncelleştiriyorsanız, yükleme ve yapılandırma işlemlerinde ek adımlar gerektiren farklılıklar vardır. Daha fazla bilgi için bu makalenin devamındaki adımlara bakın: [özel durum: 1,0 veya 1,1 ' den 1,2](#special-case-update-from-10-or-11-to-12)' a güncelleştirin.

# <a name="linux"></a>[Linux](#tab/linux)

Linux x64 cihazlarda, güvenlik cini en son sürüme güncelleştirmek için apt-get veya uygun paket yöneticisini kullanın.

Microsoft 'tan en son depo yapılandırmasını alın:

* **Ubuntu Server 18,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspberry PI OS Esnetme**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Oluşturulan listeyi kopyalayın.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Microsoft GPG ortak anahtarını yükler.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Apt 'yi güncelleştirin.

   ```bash
   sudo apt-get update
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Hangi IoT Edge sürümlerinin kullanılabilir olduğunu görmek için denetleyin.

   ```bash
   apt list -a iotedge
   ```

Güvenlik arka plan programının en son sürümüne güncelleştirmek istiyorsanız, **libiothsm-STD** ' ı en son sürüme de güncelleştiren aşağıdaki komutu kullanın:

   ```bash
   sudo apt-get install iotedge
   ```

Güvenlik arka plan programının belirli bir sürümüne güncelleştirmek istiyorsanız, apt listesi çıktısından sürümü belirtin. **İotedge** güncelleştirildiğinde, otomatik olarak **libiothsm-STD** paketini en son sürümüne güncelleştirmeye çalışır ve bu da bağımlılık çakışmasına neden olabilir. En son sürüme gitmeyecekleriniz için her iki paketi de aynı sürüme hedeflediğinizden emin olun. Örneğin, aşağıdaki komut 1,1 sürümünün belirli bir sürümünü yüklüyor:

   ```bash
   sudo apt-get install iotedge=1.1.1 libiothsm-std=1.1.1
   ```

Yüklemek istediğiniz sürüm apt-get ile kullanılamıyorsa, [IoT Edge yayımları](https://github.com/Azure/azure-iotedge/releases) deposundaki herhangi bir sürümü hedeflemek için kıvrımlı kullanabilirsiniz. Yüklemek istediğiniz sürüm için cihazınız için uygun **libiothsm-STD** ve **ıotedge** dosyalarını bulun. Her dosya için dosya bağlantısına sağ tıklayın ve bağlantı adresini kopyalayın. Bu bileşenlerin belirli sürümlerini yüklemek için bağlantı adresini kullanın:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
```
<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Hangi IoT Edge sürümlerinin kullanılabilir olduğunu görmek için denetleyin.

   ```bash
   apt list -a aziot-edge
   ```

En son IoT Edge sürümüne güncelleştirmek istiyorsanız, kimlik hizmetini de en son sürüme güncelleştiren aşağıdaki komutu kullanın:

   ```bash
   sudo apt-get install aziot-edge
   ```
<!-- end 1.2 -->
:::moniker-end

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Windows üzerinde Linux için IoT Edge, IoT Edge bir Windows cihazında barındırılan bir Linux sanal makinesinde çalışır. Bu sanal makine, IoT Edge ile önceden yüklenir ve bileşenleri güncel tutmak için Microsoft Update ile yönetilir. Otomatik Güncelleştirmeler etkinse, yeni güncelleştirmeler kullanılabilir olduğunda indirilir ve yüklenir.

Windows için IoT Edge ile, IoT Edge doğrudan Windows cihazında çalışır. PowerShell betiklerini kullanan güncelleştirme yönergeleri için bkz. [Windows için Azure IoT Edge 'ı yükleyip yönetme](how-to-install-iot-edge-windows-on-windows.md).
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Şu anda Windows cihazlarda çalışan 1,2 IoT Edge sürümü için destek yoktur.

:::moniker-end

---

## <a name="update-the-runtime-containers"></a>Çalışma zamanı kapsayıcılarını güncelleştirme

IoT Edge aracısını ve IoT Edge hub kapsayıcılarını güncelleştirme yöntemi, dağıtımda (1,1 gibi) veya belirli Etiketler (örneğin, 1.1.1) kullanıp kullanmayacağınızı bağlı olarak değişir.

IoT Edge aracısının sürümünü ve şu anda cihazınızdaki IoT Edge hub modüllerini ve komutlarını kullanarak denetleyin `iotedge logs edgeAgent` `iotedge logs edgeHub` .

  ![Günlüklerde kapsayıcı sürümünü bul](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge etiketlerini anlama

IoT Edge Aracısı ve IoT Edge hub görüntüleri ilişkili oldukları IoT Edge sürümü ile etiketlenir. Çalışma zamanı görüntülerinde Etiketler kullanmanın iki farklı yolu vardır:

* **Kayan Etiketler** -bu basamakla eşleşen en son görüntüyü almak için sürüm numarasının yalnızca ilk iki değerini kullanın. Örneğin, en son 1.1. x sürümünü işaret eden yeni bir sürüm olduğunda 1,1 güncelleştirilir. IoT Edge cihazınızdaki kapsayıcı çalışma zamanı görüntüyü yeniden çeker, çalışma zamanı modülleri en son sürüme güncelleştirilir. Azure portal varsayılan olan dağıtımlar, kayan etiketlere. *Bu yaklaşım geliştirme amaçlarıyla önerilir.*

* **Belirli Etiketler** -görüntü sürümünü açıkça ayarlamak için sürüm numarasının tüm üç değerini kullanın. Örneğin, 1.1.0 ilk sürümünden sonra değişmez. Güncelleştirme yapmaya hazırsanız dağıtım bildiriminde yeni bir sürüm numarası bildirebilirsiniz. *Bu yaklaşım, üretim amaçları için önerilir.*

### <a name="update-a-rolling-tag-image"></a>Bir sıralı etiket görüntüsünü güncelleştirme

Dağıtımınızda sıralı Etiketler kullanıyorsanız (örneğin, mcr.microsoft.com/azureiotedge-hub:**1,1**), cihazdaki kapsayıcı çalışma zamanını görüntünün en son sürümünü çekmek için zorlamanız gerekir.

IoT Edge cihazınızdan görüntünün yerel sürümünü silin. Windows makinelerinde, güvenlik arka plan programının kaldırılması çalışma zamanı görüntülerini da kaldırır, bu nedenle bu adımı tekrar yapmanız gerekmez.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.1
docker rmi mcr.microsoft.com/azureiotedge-agent:1.1
```

`-f`Görüntüleri kaldırmak için zorla bayrağını kullanmanız gerekebilir.

IoT Edge hizmeti, çalışma zamanı görüntülerinin en son sürümlerini çeker ve yeniden cihazınızda otomatik olarak başlatır.

### <a name="update-a-specific-tag-image"></a>Belirli bir etiket görüntüsünü güncelleştirme

Dağıtımınızda belirli Etiketler kullanırsanız (örneğin, mcr.microsoft.com/azureiotedge-hub:**1.1.1**), tüm yapmanız gereken, dağıtım bildiriminizde etiketi güncelleştirip değişiklikleri cihazınıza uygular.

1. Azure portal IoT Hub IoT Edge cihazınızı seçin ve **modülleri ayarla**' yı seçin.

1. **IoT Edge modüller** bölümünde **çalışma zamanı ayarları**' nı seçin.

   ![Çalışma zamanı ayarlarını yapılandırma](./media/how-to-update-iot-edge/configure-runtime.png)

1. **Çalışma zamanı ayarları**' nda **Edge hub** için **görüntü** değerini istenen sürümle güncelleştirin. Henüz **Kaydet** ' i seçmeyin.

   ![Edge hub görüntüsünü güncelleştirme sürümü](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. **Edge hub** ayarlarını daraltın veya aşağı kaydırın ve **Edge Aracısı** için **görüntü** değerini aynı istenen sürümle güncelleştirin.

   ![Edge hub Agent sürümünü Güncelleştir](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. **Kaydet**’i seçin.

1. **Gözden geçir + oluştur**' u seçin, dağıtımı gözden geçirin ve **Oluştur**' u seçin.

## <a name="special-case-update-from-10-or-11-to-12"></a>Özel durum: 1,0 veya 1,1 ' den 1,2 ' ye güncelleştirme

Sürüm 1,2 ' den başlayarak, IoT Edge hizmeti yeni bir paket adı kullanır ve yükleme ve yapılandırma işlemlerinde bazı farklılıklar vardır. 1,0 veya 1,1 sürümünü çalıştıran bir IoT Edge cihazınız varsa, bu yönergeleri kullanarak 1,2 güncelleştirme hakkında bilgi edinin.

>[!NOTE]
>Şu anda Windows cihazlarda çalışan 1,2 IoT Edge sürümü için destek yoktur.

1,2 ve önceki sürümler arasındaki temel farklılıklar şunlardır:

* Paket adı, **ıotedge** 'den **azıot-Edge** olarak değiştirildi.
* **Libiothsm-STD** paketi artık kullanılmıyor. IoT Edge sürümünün bir parçası olarak sunulan standart paketi kullandıysanız, yapılandırmanız yeni sürüme aktarılabilir. Libiothsm-STD ' nin farklı bir uygulamasını kullandıysanız, cihaz kimlik sertifikası, cihaz CA ve güven paketi gibi kullanıcı tarafından sağlanmış tüm sertifikaların yeniden yapılandırılması gerekir.
* Yeni bir kimlik hizmeti olan **azıot-Identity-Service** , 1,2 sürümünün bir parçası olarak sunulmuştur. Bu hizmet, IoT Edge için kimlik sağlamayı ve yönetimini ve [IoT Hub Için cihaz güncelleştirmesi](../iot-hub-device-update/understand-device-update.md)gibi IoT Hub iletişim kurması gereken diğer cihaz bileşenlerini işler.
* Varsayılan yapılandırma dosyası, yeni bir ad ve konuma sahiptir. `/etc/iotedge/config.yaml`Daha önce, cihaz yapılandırma bilgilerinizin artık varsayılan olarak içinde olması bekleniyor `/etc/aziot/config.toml` . Bu `iotedge config import` komut, yapılandırma bilgilerini eski konumdan ve söz dizimi yeni bir sözdizimine geçirmeye yardımcı olması için kullanılabilir.
  * Import komutu bir cihazın güvenilir Platform Modülü 'ne (TPM) erişim kurallarını algılayamaz veya değiştiremezler. Cihazınız TPM kanıtlama kullanıyorsa, aziottpm hizmetine erişim sağlamak için/etc/udev/rules. d/tpmaccess.exe dosyasını el ile güncelleştirmeniz gerekir. Daha fazla bilgi için bkz. [TPM 'ye IoT Edge erişim verme](how-to-auto-provision-simulated-device-linux.md?view=iotedge-2020-11&preserve-view=true#give-iot-edge-access-to-the-tpm).
* Sürüm 1,2 ' deki iş yükü API 'SI şifreli gizli dizileri yeni bir biçimde kaydeder. Eski bir sürümden sürüm 1,2 ' e yükseltirseniz, var olan ana şifreleme anahtarı içeri aktarılır. İş yükü API 'SI, içeri aktarılan şifreleme anahtarını kullanarak önceki biçimde kaydedilmiş gizli dizileri okuyabilir. Ancak, iş yükü API 'SI şifreli gizli dizileri eski biçimde yazamaz. Bir parola bir modülle yeniden şifrelendikten sonra yeni biçimde kaydedilir. Sürüm 1,2 ' de şifrelenen gizlilikler, sürüm 1,1 ' de aynı modülle okunamaz. Şifrelenmiş verileri konağa bağlı bir klasöre veya birime kalıcı yaparsanız, gerekli olduğunda düşürme özelliğini sürdürmek üzere yükseltmeden *önce* her zaman verilerin bir yedek kopyasını oluşturun.

Herhangi bir güncelleştirme işlemini otomatikleştirmeye başlamadan önce, test makinelerinde çalıştığını doğrulayın.

Hazırsanız, cihazlarınızda IoT Edge güncelleştirmek için aşağıdaki adımları izleyin:

1. Microsoft 'tan en son depo yapılandırmasını alın:

   * **Ubuntu Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry PI OS Esnetme**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Oluşturulan listeyi kopyalayın.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Microsoft GPG ortak anahtarını yükler.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

4. Apt 'yi güncelleştirin.

   ```bash
   sudo apt-get update
   ```

5. Önceki IoT Edge sürümünü kaldırın ve yapılandırma dosyalarınızı yerinde bırakın.

   ```bash
   sudo apt-get remove iotedge
   ```

6. IoT Edge en son sürümünü IoT kimlik hizmeti ile birlikte yükler.

   ```bash
   sudo apt-get install aziot-edge
   ```

7. Eski config. YAML Dosyanızı yeni biçimine aktarın ve yapılandırma bilgilerini uygulayın.

   ```bash
   sudo iotedge config import
   ```

Artık cihazlarınızda çalışan IoT Edge hizmeti güncelleştirildiğinden, [çalışma zamanı kapsayıcılarını de güncelleştirmek](#update-the-runtime-containers)için bu makaledeki adımları izleyin.

## <a name="special-case-update-to-a-release-candidate-version"></a>Özel durum: sürüm adayı sürümüne güncelleştirme

Azure IoT Edge, IoT Edge hizmetinin yeni sürümlerini düzenli olarak yayınlar. Her kararlı sürümden önce bir veya daha fazla sürüm adayı (RC) sürümü vardır. RC sürümleri, yayın için tüm planlı özellikleri içerir, ancak yine de test ve doğrulama işlemleri yapılır. Yeni bir özelliği erken test etmek istiyorsanız bir RC sürümü yükleyebilir ve GitHub aracılığıyla geri bildirim sağlayabilirsiniz.

Sürüm adayı sürümleri, yayınların aynı numaralandırma kuralını izler, ancak **-RC** ve sonuna artımlı bir sayı eklenir. Sürüm adaylarını kararlı sürümlerle aynı [Azure IoT Edge yayınları](https://github.com/Azure/azure-iotedge/releases) listesinde görebilirsiniz. Örneğin, **1.2.0**'ten önce yayınlanan sürüm adaylarınızdan biri olan **1.2.0-RC4** bulun. Ayrıca, RC sürümlerinin **yayın öncesi** etiketleriyle işaretlendiğini de görebilirsiniz.

IoT Edge Aracısı ve hub modülleri aynı kurala göre etiketlenmiş RC sürümlerine sahiptir. Örneğin, **MCR.Microsoft.com/azureiotedge-Hub:1.2.0-RC4**.

Önizleme olarak, sürüm adayı sürümleri, normal yükleyicilerin hedefi olan en son sürüm olarak dahil değildir. Bunun yerine, sınamak istediğiniz RC sürümü için varlıkları el ile hedefleyebilirsiniz. Çoğu bölüm için, bir RC sürümüne yükleme veya güncelleştirme, IoT Edge diğer belirli sürümlerini hedefleyecek şekilde aynıdır.

Bir IoT Edge cihazını güvenlik arka plan veya çalışma zamanı modüllerinin belirli bir sürümüne güncelleştirme hakkında bilgi edinmek için bu makaledeki bölümleri kullanın.

IoT Edge yüklüyorsanız, var olan bir yüklemeyi yükseltmek yerine [çevrimdışı veya belirli bir sürüm yüklemesinde](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional)bulunan adımları kullanın.

## <a name="next-steps"></a>Sonraki adımlar

En son [Azure IoT Edge sürümlerini](https://github.com/Azure/azure-iotedge/releases)görüntüleyin.

[Nesnelerin interneti blogdaki](https://azure.microsoft.com/blog/topics/internet-of-things/) son güncelleştirmeler ve Duyurular ile güncel kalın
