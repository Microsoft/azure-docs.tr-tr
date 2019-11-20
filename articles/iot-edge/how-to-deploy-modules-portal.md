---
title: Azure portal modüller dağıtma-Azure IoT Edge | Microsoft Docs
description: IoT Edge cihazına modül dağıtmak için Azure portal kullanma
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4c2adc8ef0d426617dc85dd507907d612bbdabaa
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964915"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure portal Azure IoT Edge modüllerini dağıtma

İş mantığınızla IoT Edge modüller oluşturduktan sonra, bunları kenarda çalıştırmak için cihazlarınıza dağıtmak istersiniz. Verileri toplamak ve işlemek için birlikte çalışan birden fazla modülünüz varsa, bunları tek seferde dağıtabilir ve bunları bağlayan yönlendirme kurallarını bildirebilirsiniz.

Bu makalede, Azure portal dağıtım bildirimi oluşturma ve dağıtımı bir IoT Edge cihazına gönderme işlemlerinde nasıl kılavuzluk gösterilmektedir. Paylaşılan etiketlerine göre birden çok cihazı hedefleyen bir dağıtım oluşturma hakkında bilgi için bkz. [IoT Edge modüllerini ölçeklendirerek dağıtma ve izleme](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğinizdeki bir [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) .
* IoT Edge çalışma zamanı yüklü [IoT Edge bir cihaz](how-to-register-device.md#register-in-the-azure-portal) .

## <a name="select-your-device"></a>Cihazınızı seçin

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.
1. Menüden **IoT Edge** seçin.
1. Cihaz listesinden hedef cihazın KIMLIĞINE tıklayın.
1. **Modülleri Ayarlama**'yı seçin.

## <a name="configure-a-deployment-manifest"></a>Dağıtım bildirimi yapılandırma

Dağıtım bildirimi, hangi modüllerin dağıtılacağını, modüller arasında verilerin nasıl akacağını ve modül TWINS 'in istenen özelliklerini tanımlayan bir JSON belgesidir. Dağıtım bildirimlerinin nasıl çalıştığı ve nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [IoT Edge modüllerinin nasıl kullanılabileceğini, yapılandırılacağını ve yeniden kullanıldığını anlayın](module-composition.md).

Azure portal, JSON belgesini el ile oluşturmak yerine dağıtım bildirimini oluşturma konusunda size yol gösteren bir sihirbaza sahiptir. Üç adım vardır: **modüller ekleme**, **rotalar belirtme**ve **dağıtımı İnceleme**.

### <a name="add-modules"></a>Modül Ekle

1. Sayfanın **Container Registry ayarları** bölümünde, modül görüntülerinizi içeren özel kapsayıcı kayıt defterlerine erişim için kimlik bilgilerini sağlayın.

1. Sayfanın **dağıtım modülleri** bölümünde **Ekle**' yi seçin.

1. Açılan listeden modül türlerine bakın:

   * **IoT Edge Module** -varsayılan seçenek.
   * Yalnızca **modül Azure Stream Analytics** bir Azure Stream Analytics iş yüküyle oluşturulan modüller.
   * Yalnızca **modül Azure Machine Learning** bir Azure Machine Learning çalışma alanından oluşturulan model görüntüleri.

1. **IoT Edge modülünü**seçin.

1. Modül için bir ad sağlayın ve ardından kapsayıcı görüntüsünü belirtin. Örnek:

   * **Ad** -SimulatedTemperatureSensor
   * **Görüntü URI 'si** -MCR.Microsoft.com/azureiotedge-Simulated-Temperature-Sensor:1.0

1. Gerekirse isteğe bağlı alanları doldurun. Kapsayıcı oluşturma seçenekleri hakkında daha fazla bilgi için, ilkeyi yeniden başlatın ve istenen durum bkz. [Edgeagent istenen özellikler](module-edgeagent-edgehub.md#edgeagent-desired-properties). Modül ikizi hakkında daha fazla bilgi için bkz. [istenen özellikleri tanımlama veya güncelleştirme](module-composition.md#define-or-update-desired-properties).

1. **Kaydet**’i seçin.

1. Dağıtımınıza ek modüller eklemek için 2-6 arası adımları tekrarlayın.

1. Rotalar bölümüne devam etmek için **İleri ' yi** seçin.

### <a name="specify-routes"></a>Rotaları belirtin

Varsayılan olarak, sihirbaz size **route** adlı bir yol sağlar ve **FROM/* to $upstream * * olarak tanımlanır, bu da herhangi bir modülle giden tüm iletilerin IoT Hub 'ınıza gönderilmesi anlamına gelir.  

Bilgileri [Declare rotalarındaki](module-composition.md#declare-routes)bilgilerle ekleyin veya güncelleştirin, ardından inceleme bölümüne devam etmek için **İleri** ' yi seçin.

### <a name="review-deployment"></a>Dağıtımı gözden geçir

İnceleme Bölümü, önceki iki bölümdeki seçimlerinize göre oluşturulan JSON dağıtım bildirimini gösterir. **$EdgeAgent** ve **$edgeHub**eklemediğiniz iki modül olduğunu unutmayın. Bu iki modül [IoT Edge çalışma zamanını](iot-edge-runtime.md) yapar ve her dağıtımda gerekli varsayılanlar olur.

Dağıtım bilgilerinizi gözden geçirin ve ardından **Gönder**' i seçin.

## <a name="view-modules-on-your-device"></a>Cihazınızdaki modülleri görüntüleme

Bir modülü cihazınıza dağıttıktan sonra, bunların tümünü portalın **cihaz ayrıntıları** sayfasında görüntüleyebilirsiniz. Bu sayfada dağıtılan her modülün adı ve dağıtım durumu ve çıkış kodu gibi yararlı bilgiler görüntülenir.

## <a name="deploy-modules-from-azure-marketplace"></a>Azure Marketi 'nden modüller dağıtma

Azure Marketi, [IoT Edge modüller](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)dahil olmak üzere Azure 'da çalışmak üzere sertifikalı ve iyileştirilmiş çok sayıda kurumsal uygulama ve çözüm arasında dolaşabileceğiniz çevrimiçi bir uygulamalar ve hizmetler marketi. Azure Market 'Te **kaynak oluştur**altında Azure Portal aracılığıyla da erişilebilir.

IoT Edge modülünü Azure Market 'ten veya Azure portal yükleyebilirsiniz:

1. Bir modül bulun ve dağıtım işlemine başlayın.

   * Azure portal: bir modül bulun ve **Oluştur**' u seçin.

   * Azure Marketi:

     1. Bir modül bulun ve **Şimdi al**' ı seçin.
     1. Devam ' i seçerek sağlayıcının kullanım koşullarını ve gizlilik ilkesini kabul **edin**.

1. Aboneliğinizi ve hedef cihazın eklendiği IoT Hub seçin.

1. **Cihaza dağıt**' ı seçin.

1. Cihazın adını girin veya hub 'a kayıtlı cihazlar arasında gezinmek için **Cihazı bul** ' u seçin.

1. İsterseniz başka modüller ekleme dahil olmak üzere, bir dağıtım bildirimini yapılandırma işlemi için standart işleme devam etmek için **Oluştur** ' u seçin. Görüntü URI 'SI, oluşturma seçenekleri ve istenen özellikler gibi yeni modülün ayrıntıları önceden tanımlanmıştır ancak değiştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge modüllerini ölçekli olarak dağıtmayı ve izlemeyi](how-to-deploy-monitor.md) öğrenin
