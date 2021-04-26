---
title: Azure IoT Hub cihaz sağlama hizmeti 'nde çok kiracılı cihazları sağlama
description: Cihaz sağlama hizmeti (DPS) örneğiniz ile çok kiracılı cihazları sağlama
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0b88923ff6447785a4ef5a7c80e1ff44d1a2b9cb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777392"
---
# <a name="how-to-provision-for-multitenancy"></a>Çok kiracı için sağlama 

Bu makalede, bir [ayırma ilkesi](concepts-service.md#allocation-policy)kullanılarak bir IoT Hub 'ı grubuna birden çok simetrik anahtar cihazı güvenli bir şekilde sağlama gösterilmektedir. Sağlama hizmeti tarafından tanımlanan ayırma ilkeleri, çeşitli ayırma senaryolarını destekler. İki yaygın senaryo şunlardır:

* **Coğrafi konum/tsıklık**: bir cihaz konumlar arasında taşındıkça, cihazın her bir konuma en yakın IoT Hub 'ına sağlanması halinde ağ gecikmesi geliştirilmiştir. Bu senaryoda, bölgeler arasında yayılan bir IoT Hub grubu kayıtları için seçilidir. Bu kayıtlar için **En düşük gecikme süresi** ayırma ilkesi seçilidir. Bu ilke, cihaz sağlama hizmeti 'nin cihaz gecikmesini değerlendirmesini ve IoT Hub 'ından IoT Hub 'ı grubunu belirlemesine neden olur. 

* **Çok kiracılı**: IoT çözümünde kullanılan cihazların belirli bir IoT Hub 'ına veya IoT Hub grubuna atanması gerekebilir. Çözüm, belirli bir kiracının belirli bir IoT Hub grubuyla iletişim kurması için tüm cihazların gerekli olabilir. Bazı durumlarda, bir kiracı IoT Hub 'larına sahip olabilir ve cihazların IoT Hub 'larına atanmasını gerektirebilir.

Bu iki senaryoyu birleştirmek yaygındır. Örneğin, çok kiracılı bir IoT çözümü, bölgeler arasında dağılmış olan bir IoT Hub 'ı grubunu kullanarak genellikle kiracı cihazları atar. Bu kiracı cihazları bu gruptaki IoT Hub 'ına atanabilir ve bu, coğrafi konuma göre en düşük gecikme süresine sahiptir.

Bu makalede, bölgeler arasında çok kiracılı bir senaryoda cihazların nasıl sağlanacağını göstermek için [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 'sından sanal bir cihaz örneği kullanılmaktadır. Bu makalede aşağıdaki adımları gerçekleştirirsiniz:

> [!div class="checklist"]
> * İki bölgesel IoT Hub 'ı (**Batı ABD** ve **Doğu ABD**) oluşturmak için Azure CLI 'yi kullanma
> * Çok kiracılı bir kayıt oluştur
> * Aynı bölgelerde (**Batı ABD** ve **Doğu ABD**) cihaz olarak davranacak iki bölgesel Linux VM oluşturmak için Azure CLI 'yi kullanın
> * Her iki Linux sanal makinesi üzerinde Azure IoT C SDK 'Sı için geliştirme ortamını ayarlama
> * En yakın bölgede aynı kiracı için sağlandığını görmek için cihazların benzetimini yapın.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Önkoşullar

- [IoT Hub cihazı sağlama hizmetini Azure Portal](./quick-setup-auto-provision.md) hızlı başlangıç ile tamamlama.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>İki bölgesel IoT Hub 'ı oluşturma

Bu bölümde, bir kiracının **Batı ABD** ve **Doğu ABD** bölgelerinde iki yeni bölgesel IoT hub 'ı oluşturmak için Azure Cloud Shell kullanacaksınız.


1. [Az Group Create](/cli/azure/group#az_group_create) komutuyla bir kaynak grubu oluşturmak için Azure Cloud Shell kullanın. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

    Aşağıdaki örnek, *eastus* bölgesinde *contoso-US-Resource-Group* adlı bir kaynak grubu oluşturur. Bu makalede oluşturulan tüm kaynaklar için bu grubu kullanmanız önerilir. Bu işlem tamamlandıktan sonra Temizleme işlemi daha kolay hale getirir.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. [Az IoT Hub Create](/cli/azure/iot/hub#az_iot_hub_create) komutuyla **Eastus** bölgesinde bir IoT hub 'ı oluşturmak için Azure Cloud Shell kullanın. IoT Hub 'ı *contoso-US-Resource-Group*'a eklenecektir.

    Aşağıdaki örnek *eastus* konumunda *contoso-Doğu-hub* adlı bir IoT Hub 'ı oluşturur. **Contoso-Doğu-hub** yerine kendi benzersiz hub 'ınızın adını kullanmanız gerekir.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Bu komutun tamamlanması birkaç dakika sürebilir.

3. [Az IoT Hub Create](/cli/azure/iot/hub#az_iot_hub_create) komutuyla **Westus** bölgesinde bir IoT hub 'ı oluşturmak için Azure Cloud Shell kullanın. Bu IoT Hub 'ı de *contoso-US-Resource-Group*' a eklenecektir.

    Aşağıdaki örnek *westus* konumunda *contoso-Batı-hub* adlı bir IoT Hub 'ı oluşturur. **Contoso-Batı-hub** yerine kendi benzersiz hub 'ınızın adını kullanmanız gerekir.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Bu komutun tamamlanması birkaç dakika sürebilir.



## <a name="create-the-multitenant-enrollment"></a>Çoklu kiracı kaydını oluşturma

Bu bölümde, kiracı cihazları için yeni bir kayıt grubu oluşturacaksınız.  

Kolaylık olması için, bu makale kayıt ile [simetrik anahtar kanıtlama](concepts-symmetric-key-attestation.md) kullanır. Daha güvenli bir çözüm için, bir güven zinciri ile [X. 509.440 sertifika kanıtlama](concepts-x509-attestation.md) kullanmayı göz önünde bulundurun.

1. [Azure Portal](https://portal.azure.com)oturum açın ve cihaz sağlama hizmeti örneğinizi açın.

2. Kayıtları **Yönet** sekmesini seçin ve ardından sayfanın en üstündeki **kayıt grubu Ekle** düğmesine tıklayın. 

3. **Kayıt grubu Ekle** sayfasında, aşağıdaki bilgileri girin ve **Kaydet** düğmesine tıklayın.

    **Grup adı**: **contoso-US-Devices** girin.

    **Kanıtlama türü**: **simetrik anahtar** seçin.

    **Anahtarları otomatik oluştur**: Bu onay kutusu zaten denetlenmelidir.

    **Cihazları hub 'lara nasıl atamak Istediğinizi seçin**: **En düşük gecikme süresini** seçin.

    ![Simetrik anahtar kanıtlama için çok kiracılı kayıt grubu ekleme](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. **Kayıt grubu Ekle**' de, her iki bölgesel hub 'ınızı bağlamak için **Yeni bir IoT Hub 'ına bağla** ' ya tıklayın.

    **Abonelik**: birden çok aboneliğiniz varsa, bölgesel IoT Hub 'larını oluşturduğunuz aboneliği seçin.

    **IoT Hub**: oluşturduğunuz bölgesel hub 'lardan birini seçin.

    **Erişim ilkesi**: **ıothubowner** öğesini seçin.

    ![Bölgesel IoT Hub 'larını sağlama hizmeti ile bağlama](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Her iki bölgesel IoT Hub bağlantısı kurulduktan sonra kayıt grubu için bunları seçmeniz ve kayıt için bölgesel IoT Hub grubunu oluşturmak üzere **Kaydet** ' e tıklamanız gerekir.

    ![Kayıt için bölgesel hub grubunu oluşturma](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Kayıt kaydedildikten sonra yeniden açın ve **birincil anahtarı** bir yere getirin. Anahtarların oluşturulması için önce kaydı kaydetmelisiniz. Bu anahtar, daha sonra sanal cihazlar için benzersiz cihaz anahtarları oluşturmak üzere kullanılacaktır.


## <a name="create-regional-linux-vms"></a>Bölgesel Linux VM 'Leri oluşturma

Bu bölümde, iki bölgesel Linux sanal makinesi (VM) oluşturacaksınız. Bu VM 'Ler, her iki bölgeden de kiracı cihazları için cihaz sağlamayı göstermek üzere her bir bölgeden bir cihaz benzetimi örneği çalıştırır.

Temizlemeyi kolaylaştırmak için, bu VM 'Ler oluşturulan IoT Hub 'larını içeren aynı kaynak grubuna eklenir, *contoso-US-Resource-Group*. Ancak, VM 'Ler ayrı bölgelerde (**Batı ABD** ve **Doğu ABD**) çalışır.

1. Azure Cloud Shell, komutta aşağıdaki parametre değişikliklerini yaptıktan sonra bir **Doğu ABD** Bölgesi sanal makinesi oluşturmak için aşağıdaki komutu yürütün:

    **--Name**: **Doğu ABD** bölgesel cihaz VM 'niz için benzersiz bir ad girin. 

    **--admin-username**: kendi yönetici kullanıcı adınızı kullanın.

    **--Admin-Password**: kendi yönetici parolanızı kullanın.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Bu komutun tamamlanması birkaç dakika sürecektir. Komut tamamlandıktan sonra, Doğu ABD bölgesi sanal makinenizin **Publicıpaddress** değerini unutmayın.

1. Azure Cloud Shell komutta aşağıdaki parametre değişikliklerini yaptıktan sonra bir **Batı ABD** Bölgesi sanal makinesi oluşturmak için komutunu yürütün:

    **--Name**: **Batı ABD** bölgesel cihaz VM 'niz için benzersiz bir ad girin. 

    **--admin-username**: kendi yönetici kullanıcı adınızı kullanın.

    **--Admin-Password**: kendi yönetici parolanızı kullanın.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Bu komutun tamamlanması birkaç dakika sürecektir. Komut tamamlandıktan sonra, Batı ABD bölgesi sanal makinenizin **Publicıpaddress** değerini unutmayın.

1. İki komut satırı kabuklarını açın. SSH kullanarak her kabukta bölgesel VM 'lerden birine bağlanın. 

    Yönetici Kullanıcı adınızı ve VM için not ettiğiniz genel IP adresini SSH 'e parametre olarak geçirin. İstendiğinde yönetici parolasını girin.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK geliştirme ortamını hazırlama

Bu bölümde, her VM 'de Azure IoT C SDK 'sını kopyalayacaksınız. SDK, her bölgeden kiracının cihaz sağlama benzetimi yapılacak bir örnek içerir.

1. Her VM için aşağıdaki komutları kullanarak **CMake**, **g + +**, **GCC** ve [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) ' i yüklemelisiniz:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. SDK 'nın [en son sürümü](https://github.com/Azure/azure-iot-sdk-c/releases/latest) için etiket adını bulun.

1. [Azure IoT C SDK 'sını](https://github.com/Azure/azure-iot-sdk-c) her iki VM 'de klonlayın.  Önceki adımda bulunan etiketini parametre değeri olarak kullanın `-b` :

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

1. Her iki VM için de depo içinde yeni bir **CMake** klasörü oluşturun ve bu klasöre geçin.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Her iki VM için de, geliştirme istemci platformunuza özgü bir SDK sürümü oluşturan aşağıdaki komutu çalıştırın. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Derleme başarılı olduktan sonra, son birkaç çıkış satırı aşağıdaki çıkışa benzer olacaktır:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Benzersiz cihaz anahtarları türet

Grup kayıtları ile simetrik anahtar kanıtlama kullanırken, kayıt grubu anahtarlarını doğrudan kullanamazsınız. Bunun yerine, her bir cihaz için benzersiz bir türetilmiş anahtar oluşturup, [Grup kayıtları simetrik anahtarlar ile](concepts-symmetric-key-attestation.md#group-enrollments)bahsedilir.

Cihaz anahtarı oluşturmak için, cihaz için benzersiz kayıt KIMLIĞI için [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) hesaplamak ve sonucu base64 biçimine dönüştürmek için Grup ana anahtarını kullanın.

Grup ana anahtarınızı cihaz kodunuza eklemeyin.

**OpenSSL** kullanarak her bir cihaz için türetilmiş bir cihaz anahtarı oluşturmak Için Bash Shell örneğini kullanın.

- **Anahtar** değerini kaydınız için önceden not ettiğiniz **birincil anahtarla** değiştirin.

- **REG_ID** değerini her cihaz için kendi BENZERSIZ kayıt Kimliğiniz ile değiştirin. Her iki kimliği de tanımlamak için küçük harf alfasayısal ve tire ('-') karakterlerini kullanın.

*Contoso-simdevice-Doğu* için örnek cihaz anahtarı oluşturma:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

*Contoso-simdevice-Batı* için örnek cihaz anahtarı oluşturma:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Kiracı cihazların her biri, kiracı IoT Hub 'larına sağlama sırasında kayıt grubuyla simetrik anahtar kanıtlama gerçekleştirmek için kendi türetilmiş Cihaz anahtarını ve benzersiz kayıt KIMLIKLERINI kullanır.




## <a name="simulate-the-devices-from-each-region"></a>Her bölgeden cihazların benzetimini yapın


Bu bölümde, Azure IoT C SDK 'sında her iki bölgesel VM için de bir sağlama örneği güncelleşirsiniz. 

Örnek kod, cihaz sağlama hizmeti örneğinize sağlama isteği gönderen bir cihaz önyükleme sırasının benzetimini yapar. Önyükleme sırası, cihazın, gecikme süresine göre en yakın IoT Hub 'ına tanınmasına ve atanmasına neden olur.

1. Azure Portal'da Cihaz Sağlama hizmetiniz için **Genel Bakış** sekmesini seçin ve **_Kimlik Kapsamı_** değerini not alın.

    ![Portal dikey penceresinden Cihaz Sağlama Hizmeti uç noktası bilgilerini ayıklama](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Her iki sanal makine üzerinde düzenlenmek üzere **~/Azure-iot-SDK-c/provisioning \_ Client/Samples/prov \_ dev \_ Client \_ Sample/prov \_ dev \_ Client \_ Sample. c** öğesini açın.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. `id_scope` sabitini bulun ve değeri daha önce kopyalamış olduğunuz **Kimlik Kapsamı** değerinizle değiştirin. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Aynı dosyada `main()` işlevinin tanımını bulun. `hsm_type`Değişkenin, `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` kayıt grubu kanıtlama yöntemiyle eşleşecek şekilde aşağıda gösterildiği gibi ayarlandığından emin olun. 

    Her iki VM 'de bulunan dosyalarınıza yaptığınız değişiklikleri kaydedin.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Her iki VM 'de de, `prov_dev_set_symmetric_key_info()` Açıklama eklenen **prov \_ dev \_ Client \_ Sample. c** dosyasında öğesine yapılan çağrıyı bulun.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    İşlev çağrılarının açıklamasını kaldırın ve yer tutucu değerlerini (açılı ayraçlar dahil) her bir cihaz için benzersiz kayıt kimlikleri ve türetilmiş cihaz anahtarları ile değiştirin. Aşağıda gösterilen anahtarlar yalnızca örnek amaçlıdır. Daha önce oluşturduğunuz anahtarları kullanın.

    Doğu ABD:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    Batı ABD:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Dosyaları kaydedin.

1. Her iki VM 'de, aşağıda gösterilen örnek klasöre gidin ve örneği derleyin.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Oluşturma başarılı olduktan sonra her bir bölgeden bir kiracı cihazının benzetimini yapmak için her iki VM 'de **prov \_ dev \_ Client \_sample.exe** çalıştırın. Her cihazın, sanal cihazın bölgelerine en yakın kiracı IoT Hub 'ına ayrıldığına dikkat edin.

    Simülasyonu çalıştırın:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Doğu ABD VM 'den örnek çıkış:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Batı ABD VM 'den örnek çıkış:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede oluşturulan kaynaklarla çalışmaya devam etmeyi planlıyorsanız, bunları bırakabilirsiniz. Kaynağı kullanmaya devam etmeyi planlamıyorsanız, gereksiz ücretlerden kaçınmak için bu makale tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

Buradaki adımlarda, bu makaledeki tüm kaynakları **contoso-US-Resource-Group** adlı aynı kaynak grubunda belirtildiği şekilde oluşturduğunuz varsayılır.

> [!IMPORTANT]
> Silinen kaynak grupları geri alınamaz. Kaynak grubu ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. IoT Hub'ı tutmak istediğiniz kaynakların bulunduğu mevcut bir kaynak grubunda oluşturduysanız kaynak grubunu silmek yerine IoT Hub kaynağını silin.
>

Kaynak grubunu ada göre silmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın ve **Kaynak grupları**’na tıklayın.

2. **Ada göre filtrele...** metin kutusuna kaynaklarınızı içeren kaynak grubunun adını yazın, **contoso-US-Resource-Group**. 

3. Sonuç listesinde kaynak grubunuzun sağ tarafında **...** ve sonra **Kaynak grubunu sil**'e tıklayın.

4. Kaynak grubunun silinmesini onaylamanız istenir. Onaylamak için kaynak grubunuzun adını tekrar yazın ve **Sil**'e tıklayın. Birkaç dakika sonra kaynak grubu ve içerdiği kaynakların tümü silinir.

## <a name="next-steps"></a>Sonraki adımlar

* Yeniden sağlama hakkında daha fazla bilgi için bkz.

> [!div class="nextstepaction"]
> [Cihaz yeniden sağlama kavramlarını IoT Hub](concepts-device-reprovision.md)

* Sağlamayı kaldırma hakkında daha fazla bilgi için bkz.
> [!div class="nextstepaction"]
> [Daha önce otomatik olarak sağlanan cihazların sağlamasını kaldırma](how-to-unprovision-devices.md)