---
title: Ubuntu sanal makinelerinde Azure IoT Edge çalıştırma | Microsoft Docs
description: Ubuntu 16,04 Azure Marketi sanal makinelerinde kurulum yönergelerini Azure IoT Edge
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: fec39a9e788debcd9c3ac707a0431e268d87ed35
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146187"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Ubuntu sanal makinelerinde Azure IoT Edge çalıştırma

Azure IoT Edge çalışma zamanı, bir cihazı IoT Edge cihazına dönüştürür. Çalışma zamanı, cihazlarda Raspberry Pi kadar küçük veya endüstriyel sunucu olarak büyük olarak dağıtılabilir. Bir cihaz IoT Edge çalışma zamanına göre yapılandırıldıktan sonra, buluta iş mantığı dağıtmaya başlayabilirsiniz.

IoT Edge çalışma zamanının nasıl çalıştığı ve hangi bileşenlerin dahil olduğu hakkında daha fazla bilgi edinmek için bkz. [Azure IoT Edge çalışma zamanını ve mimarisini anlayın](iot-edge-runtime.md).

Bu makalede, [Ubuntu Azure Marketi teklifinin](https://aka.ms/azure-iot-edge-ubuntuvm)önceden yapılandırılmış Azure IoT Edge kullanılarak ubuntu 16,04 sanal makinesinde Azure IoT Edge çalışma zamanını çalıştırma adımları listelenmektedir. 

İlk önyüklemede, Ubuntu VM üzerindeki Azure IoT Edge Azure IoT Edge çalışma zamanının en son sürümünü önceden yükler. Ayrıca, bağlantı dizesini ayarlamak için bir komut dosyası içerir ve sonra, Azure VM portalı veya Azure komut satırı aracılığıyla uzaktan tetiklenebilecek, bir SSH veya uzaktan başlatmadan IoT Edge cihazını kolayca yapılandırıp bağlayabilmenizi sağlayan çalışma zamanını yeniden başlatacak. Masaüstü oturumu. Bu betik, IoT Edge istemcisi tam yükleninceye kadar bağlantı dizesini ayarlamayı bekleyecek ve bu sayede otomasyonu oluşturmak zorunda kalmazsınız.

## <a name="deploy-from-the-azure-marketplace"></a>Azure Marketi 'nden dağıtma
1.  [Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) market teklifinde Azure IoT Edge gidin veya [Azure Marketi](https://azuremarketplace.microsoft.com/) 'Nde "ubuntu 'da" Azure IoT Edge arayarak
2.  **Şimdi al** ' ı seçin ve ardından İleri Iletişim kutusunda **devam edin** .
3.  Azure portal bir kez **Oluştur** ' u seçin ve VM 'yi dağıtmak için Sihirbazı izleyin. 
    *   Bir VM 'yi ilk kez deniyorsanız, bir parola kullanmak ve genel gelen bağlantı noktası menüsünde SSH 'yi etkinleştirmek en kolay yoldur. 
    *   Yoğun kaynak iş yükünüz varsa, daha fazla CPU ve/veya bellek ekleyerek sanal makine boyutunu yükseltmeniz gerekir.
4.  Sanal makine dağıtıldıktan sonra, IoT Hub bağlanacak şekilde yapılandırın:
    1.  Cihaz Bağlantı dizenizi, IoT Hub oluşturulan IoT Edge cihazınızdan kopyalayın (Bu işlemi tanımıyorsanız [Azure Portal yordamda bağlantı dizesini alma '](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal) yı takip edebilirsiniz)
    1.  Azure portal yeni oluşturduğunuz sanal makine kaynağını seçin ve **komutu Çalıştır** seçeneğini açın
    1.  **Runshellscrıpt** seçeneğini belirleyin
    1.  Aşağıdaki betiği, cihaz bağlantı dizeniz ile komut penceresi aracılığıyla yürütün: `/etc/iotedge/configedge.sh "{device_connection_string}"`
    1.  **Çalıştır** 'ı seçin
    1.  Birkaç dakika bekleyin ve ekranın bağlantı dizesinin başarıyla ayarlandığını belirten bir başarı iletisi sağlaması gerekir.


## <a name="deploy-from-the-azure-portal"></a>Azure portal dağıtma
Azure portal, "Azure IoT Edge" araması yapın ve sanal makine oluşturma iş akışını başlatmak için **Ubuntu Server 16,04 LTS + Azure IoT Edge çalışma zamanını** seçin. Buradan, yukarıdaki "Azure Marketi 'nden dağıtma" yönergelerindeki adım 3 ve 4 ' ü doldurun.

## <a name="deploy-from-azure-cli"></a>Azure CLı 'dan dağıtma

1. Masaüstünüzde Azure CLı kullanıyorsanız, oturum açarak başlayın:

   ```azurecli-interactive
   az login
   ```
    
1. Birden çok aboneliğiniz varsa, kullanmak istediğiniz aboneliği seçin:
   1. Aboneliklerinizi listeleyin:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Kullanmak istediğiniz aboneliğin abonelik alanı ' nı kopyalayın.

   1. Çalışma aboneliğinizi yeni kopyaladığınız KIMLIKLE ayarlayın:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Yeni bir kaynak grubu oluşturun (veya sonraki adımlarda mevcut bir tane belirtin):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Sanal makine için kullanım koşullarını kabul edin. Önce koşulları gözden geçirmek istiyorsanız, [Azure Marketi 'Nden dağıtma](#deploy-from-the-azure-marketplace)' daki adımları izleyin.

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Yeni bir sanal makine oluşturun:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Cihaz bağlantı dizesini ayarlayın (Bu işleme alışkın değilseniz, [bağlantı dizesini Azure CLI yordamıyla alma](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) işlemini izleyebilirsiniz):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Kurulumdan sonra bu VM 'ye SSH eklemek istiyorsanız, Publicıpaddress komutunu şu komutla kullanın: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>Sonraki adımlar

Çalışma zamanının yüklü olduğu bir IoT Edge cihazınıza sahip olduğunuza göre, [IoT Edge modülleri dağıtabilirsiniz](how-to-deploy-modules-portal.md).

IoT Edge çalışma zamanının düzgün şekilde yüklenmesiyle ilgili sorun yaşıyorsanız, [sorun giderme](troubleshoot.md) sayfasına göz atın.

Mevcut bir yüklemeyi en yeni IoT Edge sürümüne güncelleştirmek için bkz. [IoT Edge güvenlik cini ve çalışma zamanını güncelleştirme](how-to-update-iot-edge.md).

VM 'ye SSH veya diğer gelen bağlantılar aracılığıyla erişmek için bağlantı noktaları açmak isterseniz, [bir LINUX VM 'ye bağlantı noktalarını ve uç noktaları açma](../virtual-machines/linux/nsg-quickstart.md) hakkında Azure sanal makine belgelerine bakın
