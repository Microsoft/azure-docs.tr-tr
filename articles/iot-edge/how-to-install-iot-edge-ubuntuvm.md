---
title: Ubuntu sanal makinelerinde Azure IoT Edge çalıştırma | Microsoft Docs
description: Ubuntu 18,04 LTS sanal makineleri için Azure IoT Edge kurulum yönergeleri
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1cd89f3f772effce4997fb69b37858ce2077c1dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201088"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Ubuntu sanal makinelerinde Azure IoT Edge çalıştırma

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge çalışma zamanı, bir cihazı IoT Edge cihazına dönüştürür. Çalışma zamanı, cihazlarda Raspberry Pi kadar küçük veya endüstriyel sunucu olarak büyük olarak dağıtılabilir. Bir cihaz IoT Edge çalışma zamanıyla yapılandırıldığında, buluttan cihaza iş mantığını dağıtmaya başlayabilirsiniz.

IoT Edge çalışma zamanının nasıl çalıştığı ve hangi bileşenlerin dahil olduğu hakkında daha fazla bilgi edinmek için bkz. [Azure IoT Edge çalışma zamanını ve mimarisini anlayın](iot-edge-runtime.md).

Bu makalede, önceden sağlanmış bir cihaz bağlantı dizesi kullanılarak yüklenen ve yapılandırılan Azure IoT Edge çalışma zamanına sahip bir Ubuntu 18,04 LTS sanal makinesini dağıtma adımları listelenir. Dağıtım, [ıotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) proje deposunda tutulan bir [Cloud-init](../virtual-machines/linux/using-cloud-init.md
) tabanlı [Azure Resource Manager şablonu](../azure-resource-manager/templates/overview.md) kullanılarak gerçekleştirilir.

İlk önyüklemede, Ubuntu 18,04 LTS sanal makinesi, [Azure IoT Edge çalışma zamanının en son sürümünü Cloud-init aracılığıyla yükler](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Ayrıca, çalışma zamanı başlamadan önce, bir SSH veya Uzak Masaüstü oturumu başlatmaya gerek kalmadan IoT Edge cihazı kolayca yapılandırıp bağlayabilmenizi sağlayan bir bağlantı dizesi de ayarlar.

>[!NOTE]
>Bu makalede kullanılan şablon IoT Edge sürüm 1,1 ' i yüklüyor.

## <a name="deploy-using-deploy-to-azure-button"></a>Azure 'a dağıt düğmesini kullanarak dağıtma

[Azure 'A dağıt düğmesi](../azure-resource-manager/templates/deploy-to-azure-button.md) , GitHub 'da tutulan [Azure Resource Manager şablonlarının](../azure-resource-manager/templates/overview.md) kolaylaştırılmış şekilde dağıtılmasını sağlar.  Bu bölümde, [ıotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) proje deposunda bulunan Azure 'A dağıt düğmesinin kullanımı gösterilmektedir.  


1. İotedge-VM-Deploy Azure Resource Manager şablonunu kullanarak Azure IoT Edge etkinleştirilmiş bir Linux sanal makinesi dağıtacağız.  Başlangıç olarak aşağıdaki düğmeye tıklayın:

    [![iotedge-vm-deploy için Azure Düğmesini dağıtma](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Yeni başlatılan penceresinde, kullanılabilir form alanlarını girin:

    > [!div class="mx-imgBorder"]
    > [![iotedge-vm-deploy şablonunu gösteren ekran görüntüsü](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Abonelik**: sanal makineyi dağıtmak Için etkin Azure aboneliği.

    **Kaynak grubu**: sanal makineyi ve ilişkili kaynaklarını içeren, mevcut veya yeni oluşturulmuş bir kaynak grubu.

    **DNS etiketi ön eki**: sanal makinenin ana bilgisayar adına önek olarak eklemek için seçtiğiniz bir değer.

    **Yönetici Kullanıcı adı**: dağıtımda kök ayrıcalıklar sağlanacak olan Kullanıcı adı.

    **Cihaz bağlantı dizesi**: hedeflediğiniz [IoT Hub](../iot-hub/about-iot-hub.md)içinde oluşturulmuş bir cihaz için bir [Cihaz bağlantı dizesi](./how-to-register-device.md) .

    **VM boyutu**: dağıtılacak sanal makine [boyutu](../cloud-services/cloud-services-sizes-specs.md)

    **Ubuntu IŞLETIM sistemi sürümü**: temel sanal makineye yüklenecek Ubuntu işletim sistemi sürümü.

    **Konum**: sanal makinenin içine dağıtılacağı [coğrafi bölge](https://azure.microsoft.com/global-infrastructure/locations/) , bu değer varsayılan olarak seçilen kaynak grubunun konumudur.

    **Kimlik doğrulama türü**: tercihlerinize göre **sshpublickey** veya **Password** öğesini seçin.

    **Yönetici parolası veya anahtarı**: kimlik doğrulama türü seçimine bağlı olarak SSH ortak anahtarının değeri veya parolanın değeri.

    Tüm alanlar doldurulduktan sonra, koşulları kabul etmek için sayfanın altındaki onay kutusunu seçin ve dağıtımı başlatmak için **satın al** ' ı seçin.

1. Dağıtımın başarıyla tamamlandığını doğrulayın.  Seçilen kaynak grubuna bir sanal makine kaynağı dağıtılmış olmalıdır.  Makine adını, bu biçimde olmalıdır `vm-0000000000000` . Ayrıca ilişkili **DNS Adı**’nı da not alın; bu ad `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com biçiminde olmalıdır.

    **DNS Adı**, Azure portalında yeni dağıtılan sanal makinenin **Genel Bakış** bölümünden alınabilir.

    > [!div class="mx-imgBorder"]
    > [![İotedge VM 'sinin DNS adını gösteren ekran görüntüsü](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Kurulumdan sonra bu VM 'ye SSH eklemek istiyorsanız, ilişkili **DNS adını** komutuyla kullanın:  `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Azure CLı 'dan dağıtma

1. Azure CLı IoT uzantısını ile yüklediğinizden emin olun:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Ardından, masaüstünüzde Azure CLı kullanıyorsanız oturum açarak başlayın:

   ```azurecli-interactive
   az login
   ```

1. Birden çok aboneliğiniz varsa, kullanmak istediğiniz aboneliği seçin:
   1. Aboneliklerinizi listeleyin:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Kullanmak istediğiniz aboneliğin abonelik alanı ' nı kopyalayın.

   1. Çalışma aboneliğinizi kopyaladığınız KIMLIKLE ayarlayın:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Yeni bir kaynak grubu oluşturun (veya sonraki adımlarda mevcut bir tane belirtin):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Yeni bir sanal makine oluşturun:

    Bir **AuthenticationType** kullanmak için `password` aşağıdaki örneğe bakın:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Bir SSH anahtarıyla kimlik doğrulaması yapmak için, bir **AuthenticationType** belirterek bunu yapabilirsiniz `sshPublicKey` ve sonra **adminpasswordorkey** parametresinde SSH anahtarının değerini sağlayın.  Aşağıda bir örnek gösterilmiştir.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. Dağıtımın başarıyla tamamlandığını doğrulayın.  Seçilen kaynak grubuna bir sanal makine kaynağı dağıtılmış olmalıdır.  Makine adını, bu biçimde olmalıdır `vm-0000000000000` . Ayrıca ilişkili **DNS Adı**’nı da not alın; bu ad `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com biçiminde olmalıdır.

    **DNS adı** , önceki ADıMDA bulunan JSON biçimli çıktısından, **Genel SSH** girişinin bir parçası olarak **çıktılar** bölümünde elde edilebilir.  Bu girdinin değeri, yeni dağıtılan makineye SSH eklemek için kullanılabilir.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **DNS adı** , Azure Portal içindeki yeni dağıtılan sanal makinenin **genel bakış** bölümünden de elde edilebilir.

    > [!div class="mx-imgBorder"]
    > [![İotedge VM 'sinin DNS adını gösteren ekran görüntüsü](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Kurulumdan sonra bu VM 'ye SSH eklemek istiyorsanız, ilişkili **DNS adını** komutuyla kullanın:  `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Sonraki adımlar

Çalışma zamanının yüklü olduğu bir IoT Edge cihazınıza sahip olduğunuza göre, [IoT Edge modülleri dağıtabilirsiniz](how-to-deploy-modules-portal.md).

IoT Edge çalışma zamanının düzgün şekilde yüklenmesiyle ilgili sorun yaşıyorsanız, [sorun giderme](troubleshoot.md) sayfasına göz atın.

Mevcut bir yüklemeyi en yeni IoT Edge sürümüne güncelleştirmek için bkz. [IoT Edge güvenlik cini ve çalışma zamanını güncelleştirme](how-to-update-iot-edge.md).

VM 'ye SSH veya diğer gelen bağlantılar aracılığıyla erişmek için bağlantı noktaları açmak isterseniz, [bir LINUX VM 'ye bağlantı noktalarını ve uç noktaları açma](../virtual-machines/linux/nsg-quickstart.md) Ile Ilgili Azure sanal makineler belgelerine bakın