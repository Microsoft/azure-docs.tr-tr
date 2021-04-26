---
title: 'Öğretici: video analizlerini dağıtma-nesne ve hareket algılama Azure IoT Central uygulama şablonu'
description: Öğretici-bu kılavuzda, video analizi-nesne ve hareket algılama uygulama şablonu kullanılarak bir Azure IoT Central uygulaması dağıtma adımları özetlenmektedir.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: c578da7e83a39f84e72b550038bd87dde3c61d28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727473"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Öğretici: video analizi-nesne ve hareket algılama uygulama şablonunu kullanarak bir IoT Central uygulaması dağıtma

Temel *video analizi-nesne ve hareket algılama* uygulaması bileşenlerine genel bakış için bkz. [nesne ve hareket algılama video analizi uygulama mimarisi](architecture-video-analytics.md).

Aşağıdaki videoda, bir IoT Central çözümü dağıtmak için _video Analytics-Object ve Motion Detection uygulama şablonunun_ nasıl kullanılacağına ilişkin bir anlatım verilmiştir:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

Bu öğretici kümesinde şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> * Uygulamayı dağıtma
> * Uygulamaya bağlanan bir IoT Edge örneğini dağıtma
> * Uygulamayı izleme ve yönetme

## <a name="prerequisites"></a>Önkoşullar

Bir Azure aboneliği önerilir. Alternatif olarak, 7 günlük ücretsiz deneme sürümünü kullanabilirsiniz. Azure aboneliğiniz yoksa, [Azure kaydolma sayfasında](https://aka.ms/createazuresubscription)bir tane oluşturabilirsiniz.

## <a name="deploy-the-application"></a>Uygulamayı dağıtma

Video analizi uygulama şablonunu kullanarak bir IoT Central uygulamasını dağıtmak için aşağıdaki adımları izleyin:

1. [Azure IoT Central bir video Analytics uygulaması oluşturma (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) veya [Azure 'Da video analizi oluşturma IoT Central (openvino &trade; )](tutorial-video-analytics-create-app-openvino.md) öğreticisini şunları yapın:
    - Azure Media Services hesabı oluşturun.
    - Video analizi-nesne ve hareket algılama uygulama şablonundan IoT Central uygulamayı oluşturun.
    - IoT Central uygulamasında bir ağ geçidi cihazı yapılandırın. Ağ Geçidi, kamera cihazlarının uygulamaya bağlanmasına olanak sağlar.

1. [Video analizi için IoT Edge örneğini oluşturma (LINUX VM)](tutorial-video-analytics-iot-edge-vm.md) veya [öğretici: video analizi için bir IoT Edge örneği oluşturma (Intel nuc)](tutorial-video-analytics-iot-edge-nuc.md) öğreticisini şu şekilde yapın:
    - Azure IoT Edge çalışma zamanı yüklü bir Azure VM oluşturun.-IoT Edge yüklemeyi video analizi modülünü barındıracak şekilde hazırlayın.
    - IoT Edge cihazını IoT Central uygulamanıza bağlayın.

1. [İzleme ve video analizi uygulamasını yönetme](tutorial-video-analytics-manage.md) öğreticisini doldurun ve şunları yapın:
    - IoT Central uygulamanızdaki ağ geçidine nesne ve hareket algılama kameraları ekleyin.
    - Kamera işlemesini başlatın.
    - Yakalanan Videoyu AMS 'de görüntülemek için yerel bir medya oynatıcı yükler.
    - Algılanan nesneleri gösteren yakalanan videoyu görüntüleyin.
    - Tidy.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulamayı tamamladığınızda, oluşturduğunuz tüm kaynakları aşağıdaki şekilde kaldırabilirsiniz:

1. IoT Central uygulamasında, **Yönetim** bölümünde **uygulamanızın** sayfasına gidin. Ardından **Sil**’i seçin.
1. Azure portal, **LVA-RG** kaynak grubunu silin.
1. Yerel makinenizde **amp-Viewer** Docker kapsayıcısını durdurun.

## <a name="next-steps"></a>Sonraki adımlar

Artık, video analizi uygulama şablonunu dağıtma ve kullanma adımlarına genel bir bakış sunulmaktadır.

> [!div class="nextstepaction"]
> [Azure IoT Central (YOLO v3) üzerinde bir video analizi uygulaması oluşturun](tutorial-video-analytics-create-app-yolo-v3.md) veya

> [!div class="nextstepaction"]
> Başlamak için [Azure IoT Central 'da (OpenVINO &trade; ) bir video analizi oluşturun](tutorial-video-analytics-create-app-openvino.md) .