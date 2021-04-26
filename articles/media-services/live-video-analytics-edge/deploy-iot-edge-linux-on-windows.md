---
title: Windows-Azure 'da Linux için bir IoT Edge dağıtma
description: Bu makale, Windows cihazında Linux için bir IoT Edge dağıtma hakkında rehberlik sağlar.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: d5c3d89ae7447b062714ad90be117a6426a39581
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561092"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Windows (EFLOW) cihazında Linux için IoT Edge dağıtma

Bu makalede, [Windows 'da (EFLOW) Linux için IoT Edge](../../iot-edge/iot-edge-for-linux-on-windows.md)olan bir uç cihazda canlı video analizinin nasıl dağıtılacağını öğreneceksiniz. Bu belgedeki adımları tamamladıktan sonra, bir videoda hareketi algılayan ve bu gibi olayları buluttaki IoT Hub 'ına yayan bir [medya grafiği](media-graph-concept.md) çalıştırabileceksiniz. Daha sonra Gelişmiş senaryolar için medya grafiğini değiştirebilir ve canlı video analizinin gücünü Windows tabanlı IoT Edge cihazınıza getirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar 

* Etkin aboneliği olan bir Azure hesabı. Henüz bir [hesabınız yoksa ücretsiz olarak bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

    > [!NOTE]
    > Hizmet sorumluları oluşturma izinlerine sahip bir Azure aboneliğine ihtiyacınız olacak (**sahip rolü** bunu sağlar). Doğru izinleriniz yoksa, size doğru izinleri vermek için hesap yöneticinize ulaşın.
* Geliştirme makinenizde [Visual Studio Code](https://code.visualstudio.com/) . [Azure IoT araçları uzantısına](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)sahip olduğunuzdan emin olun.
* [EFLOW nedir?](../../iot-edge/iot-edge-for-linux-on-windows.md)konusunu okuyun.

## <a name="deployment-steps"></a>Dağıtım adımları

Aşağıdaki, belgenin genel akışını ve 5 basit adımda, canlı video analizlerini EFLOW içeren bir Windows cihazında çalıştırmak üzere ayarlamış olmanız gerekir:

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="Windows (EFLOW) diyagramında Linux için IoT Edge":::

1. Windows cihazınıza [EFLOW 'U yükler](../../iot-edge/how-to-install-iot-edge-on-windows.md) . 

    1. Windows BILGISAYARıNıZı kullanıyorsanız, Windows [Yönetici Merkezi](/windows-server/manage/windows-admin-center/overview) başlangıç sayfasında, bağlantı listesi altında, Windows yönetici MERKEZINI çalıştırdığınız bilgisayarı temsil eden bir yerel ana bilgisayar bağlantısı görürsünüz. 
    1. Yönettiğiniz tüm ek sunucular, bilgisayarlar veya kümeler burada da görünür.
    1. Yerel cihazınıza veya uzaktan yönetilen cihazlarınıza Azure EFLOW yüklemek ve yönetmek için Windows yönetici merkezini kullanabilirsiniz. Bu kılavuzda, Windows üzerinde Linux için Azure IoT Edge dağıtımı için hedef cihaz olarak sunulan yerel ana bilgisayar bağlantısı. Bu nedenle, IoT Edge bir cihaz olarak da listelenen localhost 'u görürsünüz.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="Dağıtımlar adımları-Windows Yönetim Merkezi":::
1. Bağlanmak için IoT Edge cihaza tıklayın ve bir genel bakış ve komut kabuğu sekmesi görmeniz gerekir. Komut kabuğu sekmesi, Edge cihazınıza komutlar oluşturabileceğiniz yerdir.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="Dağıtımlar adımları-Azure IoT Edge Yöneticisi":::
1. Komut kabuğu 'na gidin ve aşağıdaki komutu yazın:
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    Canlı video analizi modülü, sınır cihazında ayrıcalıklı olmayan [Yerel Kullanıcı hesaplarıyla](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment)çalışır. Ayrıca, uygulama yapılandırma verilerini depolamak için [belirli yerel klasörlere ihtiyacı vardır](deploy-iot-edge-device.md#granting-permissions-to-device-storage) . Son olarak, bu nasıl yapılır kılavuzunda, analiz için bir video akışını gerçek zamanlı olarak LVA modülüne aktaran bir [RTSP simülatörü](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) geliştirdik. Bu Benzetici, giriş dizininden girdi önceden kaydedilmiş video dosyaları olarak alır. 
    
    Yukarıda kullanılan Prep-Device betiği bu görevleri dışarıda otomatikleştirir, bu nedenle bir komut çalıştırabilir ve ilgili tüm giriş ve yapılandırma klasörlerine, video giriş dosyalarına ve ayrıcalıkları sorunsuz şekilde oluşturulan kullanıcı hesaplarına sahip olabilirsiniz. Komut başarıyla tamamlandıktan sonra Edge cihazınızda oluşturulan aşağıdaki klasörleri görmeniz gerekir. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    /Home/lvaedgeuser/Samples/Input klasöründeki video dosyalarını (*. MKV) analiz edilecek giriş dosyaları olarak sunan bir yere göz önünde bulabilirsiniz. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="Çözümlemeleri":::
1. Artık uç cihaz ayarlamış olduğunuza göre hub 'a kaydolup doğru klasör yapılarıyla başarıyla çalışıyor olduğuna göre, sonraki adım aşağıdaki ek Azure kaynaklarını kurmak ve LVA modülünü dağıtmaktır. 

    * Depolama hesabı
    * Azure Media Services hesabı

    Bunun için, Azure aboneliğinizde gerekli kaynakları dağıtmak üzere [canlı video analizi kaynakları kurulum betiğini](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) kullanmanızı öneririz. Bunu yapmak için aşağıdaki adımları izleyin:

    1. [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/)’i açın.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud Shell":::
    1. İlk kez Cloud Shell kullanıyorsanız, bir depolama hesabı ve bir Microsoft Azure dosya paylaşımının oluşturulması için bir abonelik seçmeniz istenir. Cloud Shell oturum bilgileriniz için bir depolama hesabı oluşturmak üzere **depolama oluştur** ' u seçin. Bu depolama hesabı, komut dosyasının Azure Media Services hesabınızla kullanılmak üzere oluşturulacağı hesaptan ayrıdır.
    1. Cloud Shell penceresinin sol tarafındaki açılan menüde, ortamınız olarak Bash ' i seçin.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Bash ortamı":::
    1. Aşağıdaki komutu çalıştırın.

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        Hem cihaz hem de IoT Hub daha önce oluşturduğunuz için kendi Edge cihazınızı IoT Edge cihaz olarak seçmeniz istendiğinde **Y** ' yi seçtiğinizden emin olun. Ayrıca, IoT Hub adı ve IoT Edge cihaz kimliği istenir. Her ikisini de Azure portal oturum açarak ve IoT Hub tıklatıp IoT Hub portalı dikey penceresindeki IoT Edge seçeneğine giderek alabilirsiniz.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="Bkz. IoT Edge cihazları":::

    Tamamlandıktan sonra, IoT Edge cihaz komut kabuğu 'nda yeniden oturum açıp aşağıdaki komutu çalıştırabilirsiniz.
    
    `sudo iotedge list`
    
    Aşağıdaki dört modülün, Edge cihazınızda dağıtıldığını ve çalıştığını görmeniz gerekir. Kaynak oluşturma betiğinin, sanal RTSP video akışını sağlamak için IoT Edge modüllerle birlikte LVA modülünü (edgeAgent ve edgeHub) ve bir RTSP Benzetici modülünü dağıtdığına lütfen unutmayın.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="Bkz. durum":::
1. Dağıtılan modüller ve ayarlanmış olarak, ilk LVA medya grafınızı EFLOW 'da çalıştırmaya hazırlanın. Aşağıdaki adımları gerçekleştirerek basit bir hareket algılama grafiğini aşağıdaki şekilde çalıştırabilir ve sonuçları görselleştirebilirsiniz:

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Hareket algılama grafiği":::

    1. Azure IoT araçları uzantısını [yapılandırın](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) .
    
        > [!Note]
        > Şu yolu kullanın: `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json` .
    1. Topolojiyi ayarlayın, bir grafik örneği oluşturun ve bu [doğrudan Yöntem çağrıları](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls)aracılığıyla etkinleştirin.
    1. Hub 'daki [sonuçları gözlemleyin](get-started-detect-motion-emit-events-quickstart.md#observe-results) .
    1. [Temizleme yöntemlerini](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate)çağır.
    1. Daha fazla gerekmiyorsa kaynaklarınızı silin.

        > [!IMPORTANT]
        > Silinmeyen kaynaklar etkin olmaya devam edebilir ve Azure maliyetlerine tabi olabilir.
    
## <a name="next-steps"></a>Sonraki adımlar

* Bulutta ilgili videoları kaydetme ile birlikte hareket algılamayı deneyin. İzleme [hareketini, video kliplerini Media Services](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) hızlı başlangıç adımlarını izleyin.
* [Canlı videoda AI](use-your-model-quickstart.md#overview) çalıştırın (yukarıda zaten yapıldığı üzere Önkoşul kurulumunu atlayabilirsiniz)
* Ek medya grafiklerini görüntülemek için [vs Code uzantımızı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) kullanın.
* RTSP simülatörü kullanmak yerine RTSP 'yi destekleyen bir [IP kamera](https://en.wikipedia.org/wiki/IP_camera)  kullanın. [ONVIF uyumlu ürünler](https://www.onvif.org/conformant-products/) sayfasında RTSP 'YI destekleyen IP kameralarını bulabilirsiniz. Profiller G, S veya T ile uyumlu olan cihazları arayın.