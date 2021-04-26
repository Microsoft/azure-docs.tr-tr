---
title: Azure Stack Edge Pro GPU cihazınızda GPU modülünü dağıtma | Microsoft Docs
description: İşlemin nasıl etkinleştirileceğini ve Azure Stack Edge Pro cihazının, yerel kullanıcı arabirimi aracılığıyla işlem için nasıl hazırlanılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: af44912edf3ce98ceb71bd34388543f7652c2181
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568469"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazında GPU özellikli IoT modülünü dağıtma

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Bu makalede, Azure Stack Edge Pro GPU cihazınızda GPU özellikli bir IoT Edge modülünün nasıl dağıtılacağı açıklanır. 

Bu makalede şunları öğreneceksiniz:
  - Azure Stack Edge Pro 'YU bir GPU modülünü çalıştırmaya hazırlayın.
  - Örnek kodu bir git deposundan indirin ve yükleyin.
  - Çözümü oluşturun ve bir dağıtım bildirimi oluşturun.
  - Çözümü Azure Stack Edge Pro cihazına dağıtın.
  - Modül çıkışını izleyin.


## <a name="about-sample-module"></a>Örnek modül hakkında

Bu makaledeki GPU örnek modülü,, GPU 'ya karşı CPU için örnek kodu PyTorch ve TensorFlow benchişaretlemesini içerir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakilere sahip olduğunuzdan emin olun:

- GPU etkin 1 düğümlü Azure Stack Edge Pro cihazına erişirsiniz. Bu cihaz, Azure'da bir kaynakla birlikte etkinleştirilir. Bkz. [cihazı etkinleştirme](azure-stack-edge-gpu-deploy-activate.md).
- Bu cihazda işlem yapılandırdınız. Öğreticideki adımları izleyin [: Azure Stack Edge Pro cihazınızda Işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md).
- Bir Azure Container Registry (ACR). **Erişim tuşları** dikey penceresine gidin ve ACR oturum açma sunucusunu, Kullanıcı adını ve parolayı bir yere getirin. Daha fazla bilgi için [hızlı başlangıç: Azure Portal kullanarak özel kapsayıcı kayıt defteri oluşturma](../container-registry/container-registry-get-started-portal.md#create-a-container-registry)sayfasına gidin.
- Bir Windows istemcisinde aşağıdaki geliştirme kaynakları:
    - [Azure CLı 2,0 veya üzeri](https://aka.ms/installazurecliwindows)
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Yazılımı indirmek ve yüklemek için bir hesap oluşturmanız gerekebilir.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Visual Studio Code için Azure IoT Edge uzantısı](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).    
    - [Visual Studio Code için Python uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3 karşılaştırması](https://www.python.org/)    
    - Python paketlerini yüklemek için PIP (genellikle Python yüklemenize dahildir)

## <a name="get-the-sample-code"></a>Örnek kodunu alma

1. [Azure örnekleri ' nde Azure akıllı kenar desenleri '](https://github.com/azure-samples/azure-intelligent-edge-patterns)ne gidin. Kod için zip dosyasını kopyalayın veya indirin. 

    ![Zip dosyasını indir](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    Dosyaları zip 'ten ayıklayın. Örnekleri de kopyalayabilirsiniz.

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>Derleme ve dağıtma modülü

1. Visual Studio Code 'de **Gpureferencemodules** klasörünü açın.

    ![VS Code 'de GPUReferenceModules 'i açın](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. *deployment.template.js* açın ve kapsayıcı kayıt defteri için başvurduğu parametreleri yapın. Aşağıdaki dosyada CONTAINER_REGISTRY_USERNAME, CONTAINER_REGISTRY_PASSWORD ve CONTAINER_REGISTRY_NAME kullanılır.

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. Yeni bir dosya oluşturun. Kapsayıcı kayıt defteri parametrelerinizin değerlerini (önceki adımda tanımlananları kullanın) aşağıdaki gibi doldurun: 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    Örnek bir *. env* dosyası aşağıda gösterilmiştir:
    
    ![. Env dosyası oluşturma ve kaydetme](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. Dosyayı **Samplesolution** klasörüne *. env* olarak kaydedin.

5. Docker 'da oturum açmak için, Visual Studio Code tümleşik terminalde aşağıdaki komutu girin. 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    Azure portal kapsayıcı kayıt defterinizin **erişim tuşları** bölümüne gidin. Kayıt defteri adını, parolayı ve oturum açma sunucusunu kopyalayın ve kullanın.

    ![Kapsayıcı kayıt defterinizde anahtarlara erişim](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    Kimlik bilgileri sağlandıktan sonra oturum açma işlemi başarılı olur.

    ![Başarılı oturum açma](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. Görüntünüzü Azure Container Registry 'nize gönderin. VS Code Gezgini ' nde, dosya **deployment.template.js** seçin ve sağ tıklayın ve sonra **IoT Edge çözümü oluştur ve Gönder**' i seçin. 

    ![IoT Edge çözümü oluşturun ve gönderin](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Python ve Python uzantısı yüklü değilse, çözümü derleyip gönderdiğinizde bunlar yüklenir. Ancak bu, daha uzun derleme sürelerine neden olur. 

    Bu adım tamamlandıktan sonra, kapsayıcı kayıt defterinizde modülü görürsünüz.

    ![Kapsayıcı kayıt defterinde modül](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. Bir dağıtım bildirimi oluşturmak için, **üzerindedeployment.template.js** sağ tıklayın ve sonra **IoT Edge dağıtım bildirimi oluştur**' u seçin. 

    ![IoT Edge dağıtım bildirimi oluştur](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    Bildirim, dağıtım bildiriminin oluşturulduğu yolu bilgilendirir. Bildirim, `deployment.amd64.json` **yapılandırma** klasöründe oluşturulan dosyadır. 

8. **Config** klasöründeki dosya **deployment.amd64.js** seçin ve ardından **tek cihaz için dağıtım oluştur**' u seçin. Dosyasında **deployment.template.js** kullanmayın. 

    ![Tek bir cihaz için dağıtım oluşturma](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    **Çıkış** penceresinde, dağıtımın başarılı olduğunu belirten bir ileti görmeniz gerekir.

    ![Dağıtım çıkışta başarılı oldu](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>Modülü izleme  

1. VS Code komut paletinde **Azure IoT Hub: Select IoT Hub** komutunu çalıştırın.

2. Yapılandırmak istediğiniz IoT Edge cihazını barındıran aboneliği ve IoT hub'ını seçin. Bu durumda, Azure Stack Edge Pro cihazını dağıtmak için kullanılan aboneliği seçin ve Azure Stack Edge Pro cihazınız için oluşturulan IoT Edge cihazı seçin. Bu, önceki adımlarda Azure portal aracılığıyla işlem yapılandırdığınızda oluşur.

3. VS Code Gezgini ' nde Azure IoT Hub bölümünü genişletin. **Cihazlar**' ın altında, Azure Stack Edge Pro cihazınıza karşılık gelen IoT Edge cihazını görmeniz gerekir. 

    1. Bu cihazı seçin, sağ tıklayın ve **yerleşik olay uç noktasını Izlemeye başla**' yı seçin.
  
        ![İzlemeyi Başlat](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. **Cihazlar > modüller** ' e gidin ve **GPU modülünün** çalıştığını görmeniz gerekir.

        ![IoT Hub modül](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. VS Code Terminal, Azure Stack Edge Pro cihazınız için izleme çıktısı olarak IoT Hub olaylarını da göstermelidir.

        ![İzleme çıktısı](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        GPU 'ya göre aynı işlem kümesini (şekil dönüştürme 5000 yinelemesi) yürütmek için geçen sürenin, CPU 'nun çok daha küçük olduğunu görebilirsiniz.

## <a name="next-steps"></a>Sonraki Adımlar

- [GPU 'yu bir modül kullanacak şekilde yapılandırma](./azure-stack-edge-gpu-configure-gpu-modules.md)hakkında daha fazla bilgi edinin.