---
title: Azure Stack üzerinde Azure Stream Analytics Çalıştır
description: Azure Stream Analytics Edge işi oluşturun ve IoT Edge çalışma zamanı aracılığıyla Azure Stack hub 'a dağıtın.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 315b2efa042721a19fa779794c4921f9ced1fc83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589812"
---
# <a name="run-azure-stream-analytics-on-azure-stack"></a>Azure Stack üzerinde Azure Stream Analytics Çalıştır 

Azure Stack hub 'ında Azure Stream Analytics bir IoT Edge modülü olarak çalıştırabilirsiniz. IoT Edge modüle, her Azure Stack hub işte bulunan özel URL 'Ler sayesinde bir Azure Stack hub aboneliğinde çalışan BLOB depolama, Event Hubs ve IoT Hub 'Ları ile etkileşime geçmesini sağlayan yapılandırma eklenmiştir.

Azure Stack Stream Analytics ile, kendi özel, özerk bulutunuzda akış işleme için gerçekten karma mimariler oluşturabilirsiniz. Bu, şirket içi tutarlı Azure hizmetlerini kullanarak bulutta yerel uygulamalarla bağlanabilir veya bağlantısı kesilebilir. 

Bu makalede, IoT Hub veya Olay Hub 'ından Azure Stack hub aboneliğindeki başka bir olay hub 'ına veya blob depolama alanına veri akışı yapma ve Stream Analytics ile işleme işlemleri gösterilir.

## <a name="set-up-environments"></a>Ortamları ayarlama

Azure Stream Analytics, Azure Stack hub 'ında karma bir hizmettir. Azure 'da yapılandırılan ancak Azure Stack Hub üzerinde çalıştırılabilen bir IoT Edge modülüdür.  

Azure Stack hub veya IoT Edge yeni bir ortamınızdan, ortamları ayarlamak için lütfen aşağıdaki yönergeleri izleyin.

### <a name="prepare-the-azure-stack-hub-environment"></a>Azure Stack hub ortamını hazırlama

Azure Stack hub aboneliği oluşturun. Daha fazla bilgi için [Azure Stack hub aboneliği oluşturmaya yönelik öğreticiye bakın.](/azure-stack/user/azure-stack-subscribe-services/)

Azure Stack hub 'ını kendi sunucunuzda değerlendirmek isterseniz, Azure Stack Geliştirme Seti (ASDK) kullanabilirsiniz.  ASDK hakkında daha fazla bilgi için bkz. [asdk 'ye genel bakış](/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>IoT Edge çalışma zamanını yükleme

Azure Stack hub 'ında Azure Stream Analytics çalıştırmak için, cihazınızın IoT Edge çalışma zamanı olması ve Azure Stack hub 'ına ağ bağlantısı olması veya Azure Stack hub 'ında çalışan bir VM olması gerekir. IoT Edge çalışma zamanı Stream Analytics Edge işlerinin, Azure Stack hub 'ında çalışan Azure depolama ve Azure Event Hubs ile tümleşmesine olanak tanır. Daha fazla bilgi için bkz. [IoT Edge üzerinde Azure Stream Analytics](stream-analytics-edge.md) 

IoT Edge cihazının (veya VM) Azure Stack hub kaynaklarına ağ erişiminin olmasına ek olarak, Stream Analytics modülünü yapılandırmak için Azure genel bulutundaki Azure IoT Hub 'e erişmesi gerekir. 

Aşağıdaki kılavuzlar cihazınızda veya VM 'de IoT Edge çalışma zamanının nasıl ayarlanacağını göstermektedir:

* [Windows'a Azure IoT Edge çalışma zamanını yükleme](../iot-edge/how-to-install-iot-edge.md)
* [Debian tabanlı Linux sistemlerine Azure IoT Edge çalışma zamanını yükleme](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Azure Stream Analytics Edge işi oluşturma

ASA Edge işleri Azure IoT Edge cihazlara dağıtılan kapsayıcılar üzerinde çalışır. Bunlar iki bölümden oluşur:
* İş tanımından sorumlu bir bulut bölümü: kullanıcılar, bulutta giriş, çıkış, sorgu ve diğer ayarları (sıra dışı olaylar vb.) tanımlar.
* IoT cihazlarınızda çalışan bir modül. ASA altyapısını içerir ve buluttan iş tanımını alır.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

IoT Edge cihazında çalıştırmak üzere oluşturduğunuz Azure Stream Analytics işinin cihazdan çağrılabilecek bir şekilde depolanması gerekir. Mevcut bir Azure Depolama hesabını kullanabilir veya yeni bir tane oluşturabilirsiniz.
1. Azure portal **kaynak oluşturma > depolama > depolama hesabı-blob, dosya, tablo, kuyruk ' a** gidin.
2. Depolama hesabınızı oluşturmak için aşağıdaki değerleri girin:

   | Alan | Değer |
   | --- | --- |
   | Ad | Depolama hesabınıza benzersiz bir ad verin. |
   | Konum | Size yakın bir konum seçin.|
   | Abonelik | IoT hub'ınızla aynı aboneliği seçin.|
   | Kaynak Grubu | [Hızlı başlangıç](../iot-edge/quickstart.md) ve öğreticiler IoT Edge için oluşturduğunuz tüm test kaynakları için aynı kaynak grubunu kullanmanızı öneririz. Örneğin, **IoTEdgeResources**. |

3. Diğer alanlar için varsayılan değerleri kullanın ve **Oluştur**'u seçin.


### <a name="create-a-new-job"></a>Yeni bir iş oluşturma

1. Azure portal **> Nesnelerin İnterneti > kaynak oluştur ' a gidin Stream Analytics işi**.
2. Depolama hesabınızı oluşturmak için aşağıdaki değerleri girin:

   | Alan | Değer |
   | --- | --- |
   | İş Adı | İşinize bir ad verin. Örneğin, **IoTEdgeJob** |
   | Abonelik | IoT hub'ınızla aynı aboneliği seçin.|
   | Kaynak Grubu | [Hızlı başlangıç](../iot-edge/quickstart.md) ve öğreticiler IoT Edge için oluşturduğunuz tüm test kaynakları için aynı kaynak grubunu kullanmanızı öneririz. Örneğin, **IoTEdgeResources**. |
   | Konum | Size yakın bir konum seçin. |
   | Barındırma ortamı | **Kenar**'ı seçin. |

3. **Oluştur**’u seçin.

### <a name="configure-your-job"></a>İşinizi yapılandırma

Azure portalda Stream Analytics işiniz oluşturulduktan sonra iletilen verilerle çalışması için giriş, çıkış ve sorgu ile yapılandırabilirsiniz. Azure Stack hub aboneliğinde bir IoT Hub veya Olay Hub 'ından el ile giriş belirtebilirsiniz.

1. Azure portalda Stream Analytics işinize gidin.
2. **Yapılandır** altında **depolama hesabı ayarları** ' nı seçin ve önceki adımda oluşturduğunuz depolama hesabını seçin.
   > [!div class="mx-imgBorder"]
   > [![İş depolama hesabı ayarı ](media/on-azure-stack/storage-account-settings.png)](media/on-azure-stack/storage-account-settings.png#lightbox)
3. **Iş topolojisi** altında **girişler** ' i ve ardından **akış girişi Ekle** ' yi seçin.
4. Aşağı açılan listeden **IoT Hub**, **Olay Hub**'ı veya **kenar hub 'ını** seçin. 
5. Giriş bir olay hub 'ı veya bir Azure Stack hub aboneliğinde IoT Hub, lütfen aşağıda gösterildiği gibi el ile bilgi sağlayın.

   #### <a name="event-hub"></a>Olay Hub'ı

   | Alan | Değer |
   | --- | --- |
   | Girdi diğer adı | Bu girişe başvurmak için iş sorgusunda kullandığınız kolay bir ad. |
   | Service Bus ad alanı | Ad alanı, bir mesajlaşma varlıkları kümesi için bir kapsayıcıdır. Yeni bir olay hub 'ı oluşturduğunuzda ad alanını da oluşturursunuz. (Örnek: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Olay Hub'ı adı | Giriş olarak kullanılacak Olay Hub 'ının adı. |
   | Olay Hub'ı ilke adı | Olay Hub 'ına erişim sağlayan paylaşılan erişim ilkesi. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. Bu seçenek, Olay Hub 'ı ayarlarını el ile sağlama seçeneğini seçmediğiniz takdirde otomatik olarak doldurulur. |
   | Olay Hub 'ı ilke anahtarı | Olay Hub 'ına erişim yetkisi vermek için kullanılan paylaşılan erişim anahtarı. Bu seçenek, Olay Hub 'ı ayarlarını el ile sağlama seçeneğini seçmediğiniz takdirde otomatik olarak doldurulur. Bunu, Olay Hub 'ı ayarlarında bulabilirsiniz. |
   | Olay Hub 'ı Tüketici grubu (isteğe bağlı) | Her Stream Analytics işi için ayrı bir tüketici grubu kullanmanız önemle önerilir. Bu dize, Olay Hub 'ından veri almak için kullanılacak tüketici grubunu tanımlar. Hiçbir tüketici grubu belirtilmemişse, Stream Analytics işi $Default tüketici grubunu kullanır. |
   | Bölüm sayısı | Bölüm sayısı bir olay hub 'ındaki bölüm sayısıdır. |

   > [!div class="mx-imgBorder"]
   > [![Olay Hub 'ı girişi ](media/on-azure-stack/event-hub-input.png)](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT Hub

   | Alan | Değer |
   | --- | --- |
   | Girdi diğer adı | Bu girişe başvurmak için iş sorgusunda kullandığınız kolay bir ad. |
   | IoT Hub | Giriş olarak kullanılacak IoT Hub adı. (Örnek:*<IoT Hub Name> . Shanghai.azurestack.Corp.Microsoft.com*) |
   | Paylaşılan erişim ilkesi adı | IoT Hub erişim sağlayan paylaşılan erişim ilkesi. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. |
   | Paylaşılan erişim ilkesi anahtarı | IoT Hub erişimi yetkilendirmek için kullanılan paylaşılan erişim anahtarı. IoT Hub ayarlarını el ile sağlama seçeneğini seçmediğiniz takdirde bu seçenek otomatik olarak doldurulur. |
   | Tüketici grubu (isteğe bağlı) | Her Stream Analytics işi için farklı bir tüketici grubu kullanmanız önemle tavsiye edilir. Tüketici grubu IoT Hub verileri almak için kullanılır. Stream Analytics, aksi belirtilmedikçe $Default tüketicisi grubunu kullanır. |
   | Bölüm sayısı | Bölüm sayısı bir olay hub 'ındaki bölüm sayısıdır. |

   > [!div class="mx-imgBorder"]
   > [![IoT Hub girişi ](media/on-azure-stack/iot-hub-input.png)](media/on-azure-stack/iot-hub-input.png#lightbox)

6. Diğer alanların varsayılan değerlerini tutun ve Kaydet’i seçin.
7. İş Topolojisi'nin altında Çıkışlar'ı açın ve Ekle'yi seçin.
8. Açılan listeden BLOB depolama, Olay Hub 'ı veya uç hub 'ı seçin.
9. Çıktı bir Azure Stack hub aboneliğinde bir olay hub 'ı veya blob depolama ise, lütfen aşağıda gösterildiği gibi el ile bilgi sağlayın.

   #### <a name="event-hub"></a>Olay Hub'ı

   | Alan | Değer |
   | --- | --- |
   | Çıktı diğer adı | Sorgu çıkışını bu olay hub 'ına yönlendirmek için sorgularda kullanılan kolay bir ad. |
   | Service Bus ad alanı | Bir mesajlaşma varlıkları kümesi için kapsayıcı. Yeni bir olay hub 'ı oluşturduğunuzda bir Service Bus ad alanı da oluşturdunuz. (Örnek: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Olay Hub'ı adı | Olay Hub 'ınız çıktısının adı. |
   | Olay Hub'ı ilke adı | Olay Hub 'ının Yapılandır sekmesinde oluşturabileceğiniz paylaşılan erişim ilkesi. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. |
   | Olay Hub 'ı ilke anahtarı | Olay Hub 'ı ad alanına erişimin kimliğini doğrulamak için kullanılan paylaşılan erişim anahtarı. |

   > [!div class="mx-imgBorder"]
   > [![Olay Hub 'ı çıkışı ](media/on-azure-stack/event-hub-output.png)](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob Depolama 

   | Alan | Değer |
   | --- | --- |
   | Çıktı diğer adı | Sorgu çıkışını bu blob depolamaya yönlendirmek için sorgularda kullanılan kolay bir ad. |
   | Depolama hesabı | Çıktlarınızı gönderdiğiniz depolama hesabının adı. (Örnek: *<Storage Account Name> . blob.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Depolama hesabı anahtarı | Depolama hesabıyla ilişkili gizli anahtar. BLOB depolama ayarlarını el ile sağlama seçeneğini seçmediğiniz takdirde bu seçenek otomatik olarak doldurulur. |

> [!NOTE]
> Parquet biçimi Azure Stack hub 'daki Edge işleri için desteklenmez. Minimum satırlar ve en uzun süre için lütfen 0 kullanın veya boş bırakın.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Azure Stack bağlı bir VM veya cihazda Stream Analytics dağıtın

1. Azure portal, IoT Hub açın. **IoT Edge** gidin ve bu dağıtım için hedeflemek istediğiniz CIHAZA (VM) tıklayın.
2. **Modül ayarla**' yı seçin. Ardından **+ Ekle** ' yi seçin ve **Azure Stream Analytics modülü**' nü seçin. 
3. Oluşturduğunuz aboneliği ve Steme Analizi kenar işini seçin. **Kaydet** ' e tıklayın ve **İleri: rotalar**' ı seçin.

   > [!div class="mx-imgBorder"]
   > [![Modül ](media/on-azure-stack/edge-modules.png) Ekle](media/on-azure-stack/edge-modules.png#lightbox)

4. **Gözden geçir + >oluştur**' a tıklayın.
5. **Gözden geçir + oluştur** adımında **Oluştur**' u seçin. 
   > [!div class="mx-imgBorder"]
   > [![Bildirim ](media/on-azure-stack/module-content.png)](media/on-azure-stack/module-content.png#lightbox)
6. Modülün listeye eklendiğini doğrulayın.
   > [!div class="mx-imgBorder"]
   > [![Dağıtım sayfası ](media/on-azure-stack/edge-deployment.png)](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar
- [IoT Edge üzerinde Azure Stream Analytics](./stream-analytics-edge.md)
- [Stream Analytics Edge işleri geliştirme](/stream-analytics-query/stream-analytics-query-language-reference)
