---
title: Windows 'da Linux için Azure IoT Edge yüklemesi | Microsoft Docs
description: Windows 10, Windows Server ve Windows IoT Core 'da Linux kapsayıcıları için yükleme yönergelerini Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 649c4271b2786eca506460551cfad956eeadf3c5
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964403"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Linux kapsayıcılarını çalıştırmak için Windows üzerinde IoT Edge kullanma

Windows Machine kullanan Linux cihazları için test IoT Edge modülleri. 

Bir üretim senaryosunda Windows cihazları yalnızca Windows kapsayıcıları çalıştırmalıdır. Ancak, yaygın bir geliştirme senaryosu, Linux cihazlarına yönelik IoT Edge modüller oluşturmak için bir Windows bilgisayarı kullanmaktır. Windows için IoT Edge çalışma zamanı, **test ve geliştirme** amaçlarıyla Linux kapsayıcıları çalıştırmanızı sağlar. 

Bu makalede, Windows x64 (AMD/Intel) sisteminizdeki Linux kapsayıcıları kullanılarak Azure IoT Edge çalışma zamanını yüklemek için gereken adımlar listelenmektedir. IoT Edge çalışma zamanı yükleyicisi hakkında daha fazla bilgi edinmek için, tüm yükleme parametreleriyle ilgili ayrıntılar dahil olmak üzere, bkz. [Windows 'da Azure IoT Edge çalışma zamanını yükleme](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Önkoşullar

Windows cihazınızın IoT Edge destekleyip desteklemediğini gözden geçirmek ve yüklemeden önce bir kapsayıcı altyapısı için hazırlamak üzere bu bölümü kullanın. 

### <a name="supported-windows-versions"></a>Desteklenen Windows sürümleri

Linux kapsayıcılarıyla Azure IoT Edge, [Docker Desktop gereksinimlerini](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) karşılayan herhangi bir Windows sürümünde çalıştırılabilir

IoT Edge en son sürümüne nelerin dahil olduğu hakkında daha fazla bilgi için bkz. [Azure IoT Edge yayınlar](https://github.com/Azure/azure-iotedge/releases).

Bir sanal makineye IoT Edge yüklemek istiyorsanız, iç içe sanallaştırmayı etkinleştirin ve en az 2 GB bellek ayırın. İç içe sanallaştırmayı etkinleştirme, kullandığınız hiper yöneticiye bağlı olarak farklılık gösteren bir değer. Hyper-V için 2. nesil sanal makinelerde varsayılan olarak etkinleştirilmiş iç içe sanallaştırma vardır. VMWare için, sanal makinenizde özelliği etkinleştirmek üzere bir geçiş düğmesi vardır. 

### <a name="prepare-the-container-engine"></a>Kapsayıcı altyapısını hazırlama 

Azure IoT Edge, [OCI uyumlu](https://www.opencontainers.org/) bir kapsayıcı altyapısına bağlıdır. Bir Windows makinesinde çalışan Windows ve Linux kapsayıcıları arasındaki en büyük yapılandırma farkı, IoT Edge yüklemesinde Windows kapsayıcı çalışma zamanı dahil değildir ancak IoT Edge yüklemeden önce Linux kapsayıcıları için kendi çalışma zamanını sağlamanız gerekir. 

Linux cihazları için kapsayıcılar geliştirmek ve test etmek üzere bir Windows makinesi ayarlamak için, kapsayıcı altyapınız olarak [Docker Desktop](https://www.docker.com/docker-windows) ' ı kullanabilirsiniz. IoT Edge yüklemeden önce Docker 'ı yüklemeniz ve [Linux kapsayıcıları kullanacak](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) şekilde yapılandırmanız gerekir.  

IoT Edge cihazınız bir Windows bilgisayar ise, Hyper-V için [sistem gereksinimlerini](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) karşıladığından emin olun.

## <a name="install-iot-edge-on-a-new-device"></a>Yeni bir cihaza IoT Edge yüklemesi

>[!NOTE]
>Azure IoT Edge yazılım paketleri, paketlerde bulunan lisans koşullarına tabidir (LISANS dizininde). Lütfen paketi kullanmadan önce lisans koşullarını okuyun. Paketi yüklemeniz ve kullanmanız, bu şartlarınızın kabul edildiğini oluşturur. Lisans koşullarını kabul etmiyorsanız, paketini kullanmayın.

Bir PowerShell betiği, Azure IoT Edge güvenlik cini indirir ve yükler. Güvenlik cini daha sonra, diğer modüllerin uzak dağıtımlarını sağlayan IoT Edge Aracısı olan iki çalışma zamanı modülünün ilki başlatılır. 

IoT Edge çalışma zamanını bir cihaza ilk kez yüklediğinizde, cihazı IoT Hub 'ından bir kimlikle sağlamanız gerekir. Tek bir IoT Edge cihaz, IoT Hub 'ınız tarafından sağlanan bir cihaz bağlantı dizesi kullanılarak el ile sağlanabilir. Ya da cihaz sağlama hizmeti 'ni kullanarak cihazları otomatik olarak temin edebilir, bu da birçok cihaza sahip olduğunuzda yararlı olur. 

[Windows 'a Azure IoT Edge çalışma zamanını yükleme](how-to-install-iot-edge-windows.md)makalesindeki farklı yükleme seçenekleri ve parametreleri hakkında daha fazla bilgi edinebilirsiniz. Docker Desktop 'ı yükledikten ve Linux kapsayıcıları için yapılandırıldıktan sonra, ana yükleme farkı Linux 'u **-containeros** parametresiyle bildiriyor. Örnek: 

1. Henüz yapmadıysanız, yeni bir IoT Edge cihazı kaydedin ve cihaz bağlantı dizesini alın. Bu bölümün ilerleyen kısımlarında kullanılacak bağlantı dizesini kopyalayın. Aşağıdaki araçları kullanarak bu adımı tamamlayabilirsiniz:

   * [Azure portalda](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. PowerShell 'i yönetici olarak çalıştırın.

   >[!NOTE]
   >PowerShell (x86) değil IoT Edge yüklemek için PowerShell 'in AMD64 oturumunu kullanın. Hangi oturum türünü kullandığınızdan emin değilseniz, aşağıdaki komutu çalıştırın:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **Deploy-ıotedge** komutu, Windows makinenizin desteklenen bir sürümde olup olmadığını denetler, kapsayıcılar özelliğini açar ve ardından Moby çalışma zamanını (Linux kapsayıcıları için kullanılmayan) ve IoT Edge çalışma zamanını indirir. Komutu Windows kapsayıcılarına varsayılan olarak, bu nedenle Linux 'u istenen kapsayıcı işletim sistemi olarak bildirin. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Bu noktada, IoT çekirdek cihazları otomatik olarak yeniden başlatılabilir. Diğer Windows 10 veya Windows Server cihazları yeniden başlatmanızı isteyebilir. Bu durumda cihazınızı şimdi yeniden başlatın. Cihazınız çalışmaya başladıktan sonra PowerShell 'i yönetici olarak yeniden çalıştırın.

5. **Initialize-ıotedge** komutu, makinenizde IoT Edge çalışma zamanını yapılandırır. Komut, bir cihaz bağlantı dizesiyle el ile sağlamayı varsayılan olarak belirler. Linux 'u istenen kapsayıcı işletim sistemi olarak yeniden bildirin. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. İstendiğinde, 1. adımda aldığınız cihaz bağlantı dizesini belirtin. Cihaz bağlantı dizesi, fiziksel cihazı IoT Hub bir cihaz KIMLIĞIYLE ilişkilendirir. 

   Cihaz bağlantı dizesi aşağıdaki biçimi alır ve tırnak işaretleri içermemelidir: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Yüklemenin başarılı olduğunu doğrulama

IoT Edge hizmetinin durumunu kontrol edin: 

```powershell
Get-Service iotedge
```

Son 5 dakikadan hizmet günlüklerini inceleyin: 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

En yaygın yapılandırma ve ağ hataları için otomatik bir denetim çalıştırın: 

```powershell
iotedge check
```

Çalışan modülleri listeleyin. Yeni bir yüklemeden sonra, çalıştırmayı görmeniz gereken tek modül **Edgeagent**' dir. [IoT Edge modüllerini](how-to-deploy-modules-portal.md) ilk kez dağıttıktan sonra, diğer sistem modülü, **edgehub**, cihazda da başlatılır. 

```powershell
iotedge list
```

## <a name="next-steps"></a>Sonraki adımlar

Çalışma zamanının yüklü olduğu bir IoT Edge cihazınıza sahip olduğunuza göre, [IoT Edge modülleri dağıtabilirsiniz](how-to-deploy-modules-portal.md).

Yükleme IoT Edge düzgün şekilde yüklerken [sorun yaşıyorsanız sorun giderme](troubleshoot.md) sayfasına göz atın.

Mevcut bir yüklemeyi en yeni IoT Edge sürümüne güncelleştirmek için bkz. [IoT Edge güvenlik cini ve çalışma zamanını güncelleştirme](how-to-update-iot-edge.md).
