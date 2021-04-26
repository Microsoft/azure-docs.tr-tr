---
title: GRPC çıkarım sunucusu geliştirme ve dağıtma-Azure
description: Bu makalede, bir gRPC çıkarım sunucusu geliştirme ve dağıtma konusunda rehberlik sunulmaktadır.
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: dbf46a26626a4143d76385968d092c4f238729da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034869"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>Nasıl yapılır Kılavuzu – gRPC çıkarım sunucusu geliştirme ve dağıtma

## <a name="overview"></a>Genel Bakış

Bu makalede, Graf uzantısı aracılığıyla canlı video analizi (LVA) ile tümleştirilebilmesi için bir gRPC çıkarımı sunucusu içindeki seçtiğiniz AI modellerini nasıl kaydırakullanabileceğinizi gösterir. 

## <a name="suggested-pre-reading"></a>Önerilen önceden okuma

* [Medya grafiği uzantıları](media-graph-extension-concept.md)
* [gRPC uzantısı protokolü](grpc-extension-protocol.md)
* [Çıkarım meta verileri şeması](inference-metadata-schema.md)
* [GRPC 'ye giriş](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [proto3 dil kılavuzu](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>Önkoşullar

* [Desteklenen Linux işletim sistemlerinden](../../iot-edge/support.md#operating-systems) veya bir Windows makinesinden birini çalıştıran x86-64 veya ARM64 bir cihaz.
* Makinenize [Docker 'Yi yüklemeyin](https://docs.docker.com/desktop/#download-and-install) .
* [IoT Edge çalışma zamanını](../../iot-edge/how-to-install-iot-edge.md?tabs=linux)yükler.

## <a name="grpc-implementation-steps"></a>gRPC uygulama adımları

GRPC çıkarımı sunucusu oluşturmak ve canlı video analiziyle bir uzantı olarak uygulamak için aşağıdaki adımlar kullanılacaktır:

### <a name="setup"></a>Kurulum:

[Canlı video analizi modülünün IoT Edge bir cihazda dağıtılmasını ve çalışmasını](deploy-iot-edge-device.md)sağlamak için gerekli adımları gerçekleştirin.

### <a name="high-level-implementation-steps"></a>Üst düzey uygulama adımları:

1. GRPC tarafından desteklenen birçok dilden birini seçin: C#, C++, Dart, Go, Java, Node, amaç-C, PHP, Python, Ruby.
1. [Proto3 dosyalarını](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)kullanarak canlı video analiziyle iletişim kuracak bir GRPC sunucusu uygulayın.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="proto3 dosyalarını kullanarak canlı video analiziyle iletişim kuracak gRPC sunucusu":::

    Bu hizmet içinde:
    1. Sunucu ve istemci arasındaki oturum açıklaması ileti değişimini işleyin.
    1. [Medya örnek iletilerini](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) işleyin ve sonuçları döndürün.

        1. Gelen iletilerde ınbu modeli oluşturmak için eğitilen bir model kullanan ınsıırm altyapısını çağırın.
        1. Altyapıdan ters sınırlama sonuçları alın, bunları bir medya örneği olarak geri paketleyin ve [ınsolıve. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) dosyasını kullanarak canlı video analizlerine geri gönderebilirsiniz.

            Alternatif olarak, medya örneğine herhangi bir medya dönüştürme işlevini çağırabilirsiniz.
1. GRPC sunucu uygulamasını dağıtın. Bunu yapmanın iki yolu vardır:

    1. Canlı video analizi modülü ile birlikte bulunan IoT modülü olarak dağıtma
    1. Canlı video analizi modülüyle verileri değiş tokuş eden ağ erişimli bir düğüme (Şirket içi veya bulutta) IoT modülü olarak dağıtın.
1. Canlı video analizi modülü ile canlı bir video analizi Graf topolojisi yapılandırın ve gRPC sunucusuna işaret edin.

### <a name="recommendation"></a>Önerilen

Aynı düğümde birlikte bulunurken, en iyi performans için paylaşılan bellek kullanılabilir. Bunun için programlama dili/ortamı tarafından kullanıma sunulan Linux paylaşılan bellek yeteneklerini kullanmanız gerekir.

1. Linux paylaşılan bellek tanıtıcısını açın.
1. Bir çerçeve alındıktan sonra, paylaşılan bellek içindeki adres uzaklığa erişin.
1. Gerçek zamanlı video analizi tarafından bellek kazanılabilmesi için çerçeve işleme tamamlamayı kabul edin.

## <a name="create-a-grpc-inference-server"></a>GRPC çıkarım sunucusu oluşturma

Artık paylaşılan bellek aracılığıyla [prototipsiz](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) Iletileri kullanarak canlı video analizinden video çerçevelerini kabul eden bir IoT Edge modülü (dış AI) oluşturacaksınız, kareleri "koyu" veya "hafif" olarak sınıflandırır ve çıkarım sonuçları, [çıkarım meta verileri şemasını](inference-metadata-schema.md)kullanarak canlı video Analytics 'teki IoT Hub ileti havuzuna geri döndürülür.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text="IoT Edge modülü oluşturma (dış AI)":::

Bu gRPC çıkarım sunucusu, canlı video analizi ve özel AI arasında gönderilen [prototipsiz](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) iletileri işleyen bir .NET Core konsol uygulamasıdır. Aşağıda, canlı video analizi ve gRPC çıkarım sunucusu arasında ileti akışı verilmiştir:

1. Canlı video analizi, bir medya akışı tanımlayıcısı (bkz [. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)), ardından, GRPC akış oturumu üzerinde bir [prototipli](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) ileti olarak sunucuya video çerçeveleri tarafından gönderilen medya akışı bilgilerini tanımlar. 
1. Sunucu, akış tanımlayıcısını doğrular ve onaylar ve istenen veri aktarımı yöntemini ayarlar.
1. Canlı video analizi daha sonra video çerçevelerini içeren MediaSample dosyalarını göndermeye başlar.
1. Sunucu, video çerçevelerini alır ve sizin tarafınızdan tanımlanan bir görüntü Işlemcisini kullanarak onları işlemeye başlar.
1. Daha sonra sunucu, çıkarım sonuçları kullanılabilir duruma geldiğinde [prototipsiz](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) iletileri olarak geri döndürür. 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="GRPC çıkarım sunucusu oluşturma":::

Video çerçeveleri [paylaşılan bellek](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) aracılığıyla aktarılabilir veya prototip ileti içine gömülebilir. Veri aktarım modu, karelerin nasıl aktarılacağını öğrenmek için LVA Graph topolojisinde yapılandırılabilir. Bu, aşağıda gösterildiği gibi MediaGraphGrpcExtension özelliğinin **DataTransfer** öğesi yapılandırılarak elde edilir:

Eklenen

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

Paylaşılan bellek:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> Paylaşılan bellek üzerinden iletişim kurarken, ıpcmode değeri **paylaşılabilir** olarak ayarlanmalıdır ve GRPC sunucu modülünde ıpcmode değerini **container: {CONTAINER_NAME}** olarak ayarlayın. Bu ayarlar, modülleri Azure IoT Hub dağıtmak için kullanılan dağıtım bildirimi dosyasında yapılır. IoT Edge modülleri ayarlanırken kullanılacak kapsayıcı seçeneklerinin bir örneği aşağıda verilmiştir.

Canlı video analizi modülü:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

gRPC uzantı modülü:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> GrpcExtension içindeki "Container: lvaEdge" öğesinin paylaşılan bellek alanına erişebildiğinizden emin olun.

## <a name="sample-grpc-server"></a>Örnek gRPC sunucusu

GRPC sunucusunun nasıl geliştirildiği hakkındaki ayrıntıları anlamak için, kod örneğimizi inceleyelim.

1. GitHub bağlantısından depoyu kopyalayın [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) .
1. VSCode 'u başlatın ve/src/edge/modules/grpcExtension klasörüne gidin.
1. Dosyaları hızlı bir şekilde gözden geçirelim:

    1. **Program. cs**: uygulamanın giriş noktasıdır. Bir konak olarak görev yapacak gRPC sunucusunu başlatmaktan ve yönetmekten sorumludur. Örneğimizde, üzerinde AppConfig.jsgrpcBindings yapılandırma öğesi tarafından bir gRPC istemcisinden (örneğin, canlı video analizi) gelen gRPC iletilerini dinlemek için bağlantı noktası belirtilir.
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\mediagraphextensionservice.cs**: Bu sınıf, [prototipleme](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) iletilerini işlemekten sorumludur. İletideki çerçeveyi okur, ImageProcessor 'ı çağırır ve çıkarım sonuçlarını yazar.
GRPC sunucu bağlantı noktası bağlantılarını yapılandırdığımıza ve kullanıma suntığımız için, gelen gRPC iletilerini nasıl işleyebiliriz bölümüne bakalım.

        * GRPC oturumu kurulduktan sonra, gRPC sunucusunun istemciden (canlı video analizi) alacağı ilk ileti, [extension. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) dosyasında tanımlanan bir MediaStreamDescriptor olur. 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * Sunucu uygulamamızda, yöntemi, `ProcessMediaStreamDescriptor` bir video dosyası Için MediaStreamDescriptor 'ın mediadescriptor özelliğini doğrular ve ardından veri aktarım modunu (paylaşılan belleği kullanan veya gömülü çerçeve aktarım modunu kullanarak), topolojide ve kullanılan dağıtım şablonu dosyasında belirtdiklerinize bağlı olarak ayarlar. 
        * İleti alındığında ve veri aktarımı modunu başarıyla ayarladıktan sonra, gRPC sunucusu daha sonra bir bildirim olarak istemciye MediaStreamDescriptor iletisini geri döndürür ve bu nedenle sunucu ile istemci arasında bir bağlantı oluşturur.    
        * Canlı video analizi bildirimi aldıktan sonra, medya akışını gRPC sunucusuna aktarmaya başlar. Sunucu uygulamamızda, yöntemi `ProcessMediaStream` gelen MediaStreamMessage öğesini işleyecek. MediaStreamMessage, [. proto uzantısında](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)de tanımlanır.

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * Appconfig.jsüzerindeki batchSize değerine bağlı olarak, sunucunuz iletileri almaya devam eder ve video çerçevelerini bir listede depolar. BatchSize sınırına ulaşıldığında, işlev işlevi veya görüntüyü işleyecek dosyayı çağırır. Bu durumda, yöntemi Batchımageprocessor. cs adlı bir dosya çağırır
    1. **Processors\batchımageprocessor.cs**: Bu sınıf, görüntü (ler) i işlemekten sorumludur. Bu örnekte bir görüntü sınıflandırma modeli kullandık. İşlenecek her görüntü için, kullanılan algoritma aşağıda verilmiştir:

        1. Bir bayt dizisindeki görüntüyü işlenmek üzere dönüştürür. Bkz. yöntemi: `GetBytes(Bitmap image)`
        
            Kullandığımız örnek işlemci yalnızca JPG kodlamalı görüntü çerçevesini destekliyor ve piksel biçiminde yok. Özel işlemcinizin farklı bir kodlamayı ve/veya biçimi desteklemesi durumunda, `IsMediaFormatSupported` işlemci sınıfının yöntemini güncelleştirin.
        1. [ColorMatrix sınıfını](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1)kullanarak görüntüyü gri ölçeğe dönüştürün. Bkz. Yöntem: `ToGrayScale(Image source)` .
        1. Gri ölçekli görüntüyü aldıktan sonra gri ölçekli baytların ortalamasını hesaplayabilirsiniz.
        1. Ortalama değer 127 <, görüntüyü "koyu" olarak sınıflandırırız; Aksi takdirde, bunları "açık" olarak, güvenilirlik değeri 1,0 olarak sınıflandırırız. Bkz. Yöntem: `ProcessImage(List<Image> images)` .

    Bu sınıfı değiştirerek ya da yeni bir sınıf ekleyerek ve yöntemi uygulayarak kendi işlemci mantığınızı ekleyebilirsiniz:

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    Yeni sınıfı ekledikten sonra, MediaGraphExtensionService. cs ' yi güncelleştirmeniz gerekir. bu nedenle, sınıfınızı bir kez başlatır ve işlem mantığınızı çalıştırmak için üzerinde ProcessImage yöntemini çağırır. 

## <a name="connect-with-live-video-analytics-module"></a>Live video Analytics modülü ile bağlanma

GRPC uzantı modülünüzü oluşturdığınıza göre artık medya grafiği topolojisini oluşturacağız ve dağıtacağız.

1. Visual Studio Code kullanarak Docker 'da oturum açmak için [Bu yönergeleri](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) izleyin.
1. Visual Studio Code, src/Edge bölümüne gidin. . Env dosyanızı ve birkaç dağıtım şablonu dosyasını görürsünüz.

    Dağıtım şablonu, Edge cihazının dağıtım bildirimini ifade eder. Bazı yer tutucu değerleri içerir. . Env dosyası bu değişkenlerin değerlerini içerir.
    
    Ardından IoT Edge çözüm oluştur ve Gönder ' i seçin. Bu adım için src/Edge/deployment.grpc.template.jskullanın.
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="Live video Analytics modülü ile bağlanma":::
    
    Bu eylem, GRPC sunucu modülünü oluşturur ve görüntüyü Azure Container Registry iter.
    Ortam değişkenlerinin. env dosyasında tanımlı CONTAINER_REGISTRY_USERNAME_myacr ve CONTAINER_REGISTRY_PASSWORD_myacr sahip olup olmadığını denetleyin.
1. Src/buluttan cihaza-Console-App klasörüne gidin. Burada dosya ve diğer birkaç dosya appsettings.jsgörürsünüz:

    * C2D-Console-App. csproj-Visual Studio Code için proje dosyası.
    * operations.js, programın çalıştırmasını istediğiniz işlemlerin bir listesi.
    * Program. cs-örnek program kodu. Bu kod:

        * Uygulama ayarlarını yükler.
        * IoT Edge modülündeki canlı video analizinin sunduğu doğrudan yöntemleri çağırır. [Doğrudan yöntemlerini](direct-methods.md)çağırarak canlı video akışlarını çözümlemek için modülünü kullanabilirsiniz.
        * Program çıkışını TERMINAL penceresinde incelemenize ve çıkış penceresinde modül tarafından oluşturulan olayları incelemenize olanak tanıyan duraklar.
        * Kaynakları temizlemek için doğrudan yöntemleri çağırır.
1. Dosyadaki operations.jsdüzenleyin:

    * Grafik topolojisine olan bağlantıyı değiştirin:

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * Graphınstanceset altında, grafik topolojisinin adını önceki bağlantıdaki değerle eşleşecek şekilde düzenleyin:<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * GraphTopologyDelete altında adı düzenleyin:<br/>`"name": "InferencingWithGrpcExtension"`

            Topoloji (örneğin, `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json` ) bir uzantı adresi tanımlamalıdır:
    * Uzantı adresi parametresi

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * Yapılandırma

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge dağıtım bildirimini oluşturma ve dağıtma

Dağıtım bildirimi, bir sınır cihazına hangi modüllerin dağıtıldığını ve bu modüllerin yapılandırma ayarlarını tanımlar. Şablon dosyasından bir bildirim oluşturmak ve ardından bunu Edge cihazına dağıtmak için bu adımları izleyin.
Bu adım, üzerinde src/Edge/config/deployment.grpc.amd64.jsüzerinde IoT Edge dağıtım bildirimi oluşturur. Bu dosyaya sağ tıklayın ve **tek cihaz Için dağıtım oluştur**' u seçin.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="IoT Edge dağıtım bildirimini oluşturma ve dağıtma":::

Sonra, Visual Studio Code IoT Hub bir cihaz seçmenizi ister. LVA-örnek-cihaz olması gereken IoT Edge cihazınızı seçin.
Bu aşamada, Edge modüllerinin IoT Edge cihazınıza dağıtılması başladı. Yaklaşık 30 saniye içinde, Visual Studio Code sol alt bölümdeki Azure IoT Hub yenileyin. LvaExtension adlı yeni bir modülün dağıtıldığını görmeniz gerekir.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="LvaExtension adlı yeni bir modül dağıtıldı":::

## <a name="next-steps"></a>Sonraki adımlar

Örneği çalıştırmak ve sonuçları yorumlamak için model hızlı başlangıç [ile canlı videoyu çözümle](use-your-model-quickstart.md) bölümünde bahsedilen **olayları izlemeye hazırlama** adımlarını izleyin. Ayrıca, örnek gRPC topolojilerimize göz atın: [Grpcextension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json), [cvrwithgrpcextension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json), [Evrtoassetsbygrpcextension ve [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json).