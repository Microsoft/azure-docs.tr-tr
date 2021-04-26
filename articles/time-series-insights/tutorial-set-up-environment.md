---
title: 'Öğretici: Gen2 ortamı ayarlama-Azure Time Series Insights Gen2 | Microsoft Docs'
description: "Öğretici: Azure Time Series Insights Gen2 'de ortam ayarlamayı öğrenin."
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/25/2021
ms.custom: seodec18
ms.openlocfilehash: 76a33bdb773645c9e8f97a47b1378d813b165631
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464799"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>Öğretici: bir Azure Time Series Insights Gen2 ortamı ayarlama

Bu öğretici, bir Azure Time Series Insights Gen2 *Kullandıkça Öde* (PAYG) ortamı oluşturma sürecinde size rehberlik eder.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Azure Time Series Insights Gen2 ortamı oluşturun.
> * Azure Time Series Insights Gen2 ortamını bir IoT Hub bağlayın.
> * Azure Time Series Insights Gen2 ortamına veri akışı sağlamak için bir çözüm Hızlandırıcısı örneği çalıştırın.
> * Veriler üzerinde temel analiz gerçekleştirin.
> * Bir zaman serisi model türü ve hiyerarşisi tanımlayın ve bunları örneklerle ilişkilendirin.

>[!TIP]
> [IoT Çözüm Hızlandırıcıları](https://www.azureiotsolutions.com/Accelerators) , özel IoT çözümlerinin geliştirilmesini hızlandırmak için kullanabileceğiniz kurumsal sınıf önceden yapılandırılmış çözümler sunar.

Henüz yoksa ücretsiz bir [Azure aboneliğine](https://azure.microsoft.com/free/) kaydolun.

## <a name="prerequisites"></a>Önkoşullar

* En azından, Azure aboneliği için **katkıda bulunan** rolüne sahip olmanız gerekir. Daha fazla bilgi için [Azure Portal kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-portal.md)makalesini okuyun.

* [Azure Portal](#create-an-azure-time-series-insights-gen2-environment) veya [CLI](how-to-create-environment-using-cli.md)kullanarak bir ortam oluşturun.

## <a name="create-a-device-simulation"></a>Cihaz benzetimi oluşturma

Bu bölümde, bir Azure IoT Hub örneğine veri gönderen üç sanal cihaz oluşturacaksınız.

1. [Azure IoT Çözüm Hızlandırıcıları sayfasına](https://www.azureiotsolutions.com/Accelerators)gidin. Azure hesabınızı kullanarak oturum açın, sonra **cihaz benzetimi**' ni seçin.

   [![Azure IoT Çözüm Hızlandırıcıları sayfası.](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. [Genel bakış](https://github.com/Azure/azure-iot-pcs-device-simulation#overview) [ve Başlarken](https://github.com/Azure/azure-iot-pcs-device-simulation#getting-started) bölümlerini okumak için aşağı kaydırın.

1. Başlarken bölümündeki [dağıtım talimatlarını](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/deployment/README.md) izleyin.

   Bu işlemin tamamlanması 20 dakikaya kadar sürebilir.

1. Dağıtım tamamlandığında, simülasyonuna URL sunulur. Daha sonra geri döneceksiniz, bu sayfayı açık tutun.

   >[!IMPORTANT]
   > Çözüm hızlandırıcıyı henüz girmeyin! Daha sonra geri döneceksiniz, bu Web sayfasını açık tutun.

   [![Cihaz benzetimi çözüm sağlama işlemi tamam.](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png)](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png#lightbox)

1. Şimdi, Azure portal yeni oluşturulan kaynakları inceleyin. **Kaynak grupları** SAYFASıNDA, `solutionName` ARM şablon parametreleri dosyanızda sağladınız kullanılarak yeni bir kaynak grubunun oluşturulduğunu fark edersiniz. Cihaz benzetimi için oluşturulan kaynakları unutmayın.

   [![Cihaz benzetimi kaynakları.](media/tutorial-set-up-environment/device-sim-solution-resources.png)](media/tutorial-set-up-environment/device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 ortamı oluşturma

Bu bölümde, bir Azure Time Series Insights Gen2 ortamının nasıl oluşturulacağı ve [Azure Portal](https://portal.azure.com/)kullanılarak IoT Çözüm Hızlandırıcısı tarafından oluşturulan IoT Hub 'ına nasıl bağlanacağı açıklanır.

1. Azure abonelik hesabınızı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Üstteki menüden **+ Kaynak oluştur**'u seçin.
1. **Nesnelerin interneti** kategorisini seçin ve ardından **Time Series Insights**' ı seçin.

   [![Time Series Insights ortamı kaynağını seçin.](media/tutorial-set-up-environment/create-new-environment.png)](media/tutorial-set-up-environment/create-new-environment.png#lightbox)

1. **Time Series Insights ortamı oluştur** bölmesinde, **temel bilgiler** sekmesinde, aşağıdaki parametreleri ayarlayın:

    | Parametre | Eylem |
    | --- | ---|
    | **Ortam adı** | Azure Time Series Insights Gen2 ortamı için benzersiz bir ad girin. |
    | **Abonelik** | Azure Time Series Insights Gen2 ortamını oluşturmak istediğiniz aboneliği girin. En iyi uygulama, cihaz simülatörü tarafından oluşturulan diğer IoT kaynaklarıyla aynı aboneliği kullanmaktır. |
    | **Kaynak grubu** | Var olan bir kaynak grubunu seçin veya Azure Time Series Insights Gen2 ortam kaynağı için yeni bir kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarına yönelik bir kapsayıcıdır. En iyi uygulama, cihaz simülatörü tarafından oluşturulan diğer IoT kaynaklarıyla aynı kaynak grubunu kullanmaktır. |
    | **Konum** | Azure Time Series Insights Gen2 ortamınız için bir veri merkezi bölgesi seçin. Ek gecikme süresini önlemek için, Azure Time Series Insights Gen2 ortamınızı cihaz simülatörü tarafından oluşturulan IoT Hub 'ınız ile aynı bölgede oluşturmak en iyisidir. |
    | **Katman** |  **Gen2 (L1)** seçeneğini belirleyin. Bu, Azure Time Series Insights Gen2 ürünü için SKU 'dır. |
    | **Zaman serisi KIMLIĞI Özellik adı** | Zaman serisi örneklerinizi benzersiz bir şekilde tanımlayan değerleri içeren bir özelliğin adını girin. Zaman serisi KIMLIĞI olarak **özellik adı** kutusuna girdiğiniz değer daha sonra değiştirilemez. Bu öğretici için **_ıothub-Connection-Device-ID_** girin. Birleşik zaman serisi kimliği gibi zaman serisi KIMLIĞI hakkında daha fazla bilgi edinmek için, [zaman SERISI kimliği seçmek üzere en iyi uygulamaları](./how-to-select-tsid.md)okuyun. |
    | **Depolama hesabı adı** | Yeni bir depolama hesabı için genel olarak benzersiz bir ad girin.|
    | **Depolama hesabı türü** | Yeni bir depolama hesabı için depolama türünü seçin. StorageV2 önerilir|
    | **Depolama hesabı çoğaltma** | Yeni bir depolama hesabı için depolama türünü seçin. Konum seçiminize göre LRS, GRS ve ZRS arasından seçim yapabilirsiniz. Bu öğretici için LRS 'yi seçebilirsiniz|
    | **Hiyerarşik ad alanı** |Bu seçenek, StorageV2 olacak depolama türünü seçtiğinizde seçilebilir. Varsayılan olarak devre dışıdır. Bu öğreticide, varsayılan *devre dışı* durumunda bırakabilirsiniz|
    |**Isınma deposunu etkinleştir**|Isınma mağazasını etkinleştirmek için **Evet** ' i seçin. Bu ayar, ortam oluşturulduktan sonra devre dışı bırakılabilir ve yeniden etkinleştirilebilir. |
    |**Veri bekletme (gün)**|Varsayılan 7 gün seçeneğini belirleyin. |

    [![Yeni Azure Time Series Insights ortamı yapılandırması.](media/tutorial-set-up-environment/environment-configuration.png)](media/tutorial-set-up-environment/environment-configuration.png#lightbox)
    [![Yeni Azure Time Series Insights ortamı yapılandırması, devam eden.](media/tutorial-set-up-environment/environment-configuration2.png)](media/tutorial-set-up-environment/environment-configuration2.png#lightbox)

1. **İleri ' yi seçin: olay kaynağı**.

   [![Ortam için zaman serisi KIMLIĞINI yapılandırın.](media/tutorial-set-up-environment/time-series-id-selection.png)](media/tutorial-set-up-environment/time-series-id-selection.png#lightbox)

1. **Olay kaynağı** sekmesinde, aşağıdaki parametreleri ayarlayın:

   | Parametre | Eylem |
   | --- | --- |
   | **Olay kaynağı oluşturulsun mu?** | **Evet**’i seçin.|
   | **Kaynak türü** | **IoT Hub** seçin. |
   | **Ad** | Olay kaynağı adı için benzersiz bir değer girin. |
   | **Bir hub seçin** | **Varolanı Seç ' i** seçin. |
   | **Abonelik** | Cihaz simülatörü için kullandığınız aboneliği seçin. |
   | **IoT Hub adı** | Cihaz simülatörü için oluşturduğunuz IoT Hub adını seçin. |
   | **IoT Hub erişim ilkesi** | **İothubowner** öğesini seçin. |
   | **IoT Hub Tüketici grubu** | **Yeni**' yi seçin, benzersiz bir ad girin ve **+ Ekle**' yi seçin. Tüketici grubu Azure Time Series Insights Gen2 içinde benzersiz bir değer olmalıdır. |
   | **TimeStamp Özelliği** | Bu değer, gelen telemetri verilerinizde **zaman damgası** özelliğini tanımlamak için kullanılır. Bu öğretici için bu kutuyu boş bırakın. Bu simülatör, Azure Time Series Insights Gen2 varsayılan olarak IoT Hub gelen zaman damgasını kullanır. |

1. **Gözden geçir + Oluştur**’u seçin.

   [![Oluşturulan IoT Hub 'ını bir olay kaynağı olarak yapılandırın.](media/tutorial-set-up-environment/configure-event-source.png)](media/tutorial-set-up-environment/configure-event-source.png#lightbox)

1. **Gözden geçir + Oluştur**’u seçin.

    [![Oluştur düğmesi ile, ve oluştur sayfasını gözden geçirin.](media/tutorial-set-up-environment/environment-confirmation.png)](media/tutorial-set-up-environment/environment-confirmation.png#lightbox)

    Dağıtımınızın durumunu gözden geçirebilirsiniz:

    [![Dağıtımın tamamlandığını bildirme.](media/tutorial-set-up-environment/deployment-notification.png)](media/tutorial-set-up-environment/deployment-notification.png#lightbox)

1. Dağıtım ayrıntıları ' nı genişletin.

## <a name="stream-data"></a>Veri akışı yapma

Artık Azure Time Series Insights Gen2 ortamınızı dağıttığınıza göre, verileri analiz için akışa alma işlemini başlatın.

1. Çözüm Hızlandırıcı dağıtımı tamamlandıktan sonra bir URL verilecek.

1. Cihaz benzetimini başlatmak için URL 'ye tıklayın.

1. **+ Yeni benzetim**' i seçin.

    1. **Simülasyon kurulum** sayfası yüklendikten sonra gerekli parametreleri girin.

        | Parametre | Eylem |
        | --- | --- |
        | **Ad** | Simülatör için benzersiz bir ad girin. |
        | **Açıklama** | Bir tanım girin. |
        | **Benzetim süresi** | Süresiz olarak **çalışacak** şekilde ayarlayın. |
        | **Cihaz modeli** | + **Cihaz türü Ekle** ' ye tıklayın <br />**Ad**: **Asansör** girin. <br />**Tutar**: **3** girin. <br /> Kalan varsayılan değerleri bırakın |
        | **Hedef IoT Hub'ı** | **Önceden sağlanmış IoT Hub kullanılacak** şekilde ayarlayın. |

        [![Parametreleri yapılandırın ve başlatın.](media/tutorial-set-up-environment/launch-solution-accelerator.png)](media/tutorial-set-up-environment/launch-solution-accelerator.png#lightbox)

    1. **Benzetimi Başlat**' ı seçin. Cihaz benzetimi panosunda, **etkin cihazlar** ve **Toplam iletiler** görüntülenir.

        [![Azure IoT simülasyonu panosu.](media/tutorial-set-up-environment/see-active-devices-and-messages.png)](media/tutorial-set-up-environment/see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Verileri çözümleme

Bu bölümde, [Azure Time Series Insights Gen2 Gezginini](./concepts-ux-panels.md)kullanarak zaman serisi verilerinizde temel analiz gerçekleştirirsiniz.

1. [Azure Portal](https://portal.azure.com/)kaynak sayfasından URL 'yi seçerek Azure Time Series Insights Gen2 Explorer 'a gidin.

    [![Azure Time Series Insights Gen2 Explorer URL 'SI.](media/tutorial-set-up-environment/select-explorer-url.png)](media/tutorial-set-up-environment/select-explorer-url.png#lightbox)

1. Azure Time Series Insights Gen2 Explorer 'da, ekranın üst kısmına yayılan bir çubuk görüntülenir. Bu, kullanılabilirlik seçicinizdeki bir değer. En az 2 2 e seçtiğinizden emin olun ve gerekirse, seçici tutamaçlarını seçip sola ve sağa sürükleyerek zaman çerçevesini genişletin.

1. Sol tarafta **zaman serisi örnekleri** görüntülenecektir.

    [![Üst öğe olmayan örneklerin listesi.](media/tutorial-set-up-environment/explorer-unparented-instances.png)](media/tutorial-set-up-environment/explorer-unparented-instances.png#lightbox)

1. İlk zaman serisi örneğini seçin. Sonra **sıcaklık göster**' i seçin.

    [![Ortalama sıcaklığı göstermek için menü komutuyla birlikte seçili zaman serisi örneği.](media/tutorial-set-up-environment/select-instance-and-temperature.png)](media/tutorial-set-up-environment/select-instance-and-temperature.png#lightbox)

    Bir zaman serisi grafiği görüntülenir. **Aralığı** **30 saniye** olarak değiştirin.

1. Bu grafikte gösterildiği gibi, üçü de görüntülemek için önceki adımı diğer iki zaman serisi örneğiyle tekrarlayın:

    [![Tüm zaman serileri için grafik.](media/tutorial-set-up-environment/explorer-add-three-instances.png)](media/tutorial-set-up-environment/explorer-add-three-instances.png#lightbox)

1. Sağ üst köşedeki zaman aralığı seçicisini seçin. Burada, belirli başlangıç ve bitiş zamanlarını milisaniyeye doğru seçebilirsiniz veya **en son 30 dakika** gibi önceden yapılandırılmış seçenekler arasından seçim yapabilirsiniz. Varsayılan saat dilimini de değiştirebilirsiniz.

    [![Zaman aralığını son 30 dakikaya ayarlayın.](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png)](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png#lightbox)

    Çözüm hızlandırıcının **son 30 dakika** içinde ilerleme durumu artık Azure Time Series Insights Gen2 Explorer 'da görüntülenir.

## <a name="define-and-apply-a-model"></a>Model tanımlama ve uygulama

Bu bölümde, verilerinizi yapılandırmak için bir model uygularsınız. Modeli tamamlayabilmeniz için türler, hiyerarşiler ve örnekler tanımlarsınız. Veri modelleme hakkında daha fazla bilgi edinmek için [zaman serisi modelini](./concepts-model-overview.md)okuyun.

1. Gezgin 'de **model** sekmesini seçin:

   [![Gezgin 'deki Model sekmesini görüntüleyin.](media/tutorial-set-up-environment/select-model-view.png)](media/tutorial-set-up-environment/select-model-view.png#lightbox)

   **Türler** sekmesinde **+ Ekle**' yi seçin.

1. Aşağıdaki parametreleri girin:

    | Parametre | Eylem |
    | --- | ---|
    | **Ad** | **Asansör** girin |
    | **Açıklama** | **Bu, Asansör için bir tür tanımı** girin |

1. Sonra, **değişkenler** sekmesini seçin.

    1. **+ Değişken Ekle** ' yi seçin ve Asansör türünün ilk değişkeni için aşağıdaki değerleri girin. Toplam olarak üç değişken yazardan olursunuz.

        | Parametre | Eylem |
        | --- | --- |
        | **Ad** | **Ort sıcaklık** girin. |
        | **Tip** | **Sayısal** seçin |
        | **Değer** | Önayar arasından seç: **sıcaklık (Double)** seçeneğini belirleyin. <br /> Note: Azure Time Series Insights Gen2 olayları almaya başladıktan sonra **değerin** otomatik olarak doldurulması birkaç dakika sürebilir.|
        | **Toplama Işlemi** | **Gelişmiş Seçenekler**' i genişletin. <br /> **Ort**' ı seçin. |

    1. **Uygula**’yı seçin. Sonra, **+ değişkeni tekrar ekleyin** ve aşağıdaki değerleri ayarlayın:

        | Parametre | Eylem |
        | --- | --- |
        | **Ad** | **Ortalama titreşim** girin. |
        | **Tip** | **Sayısal** seçin |
        | **Değer** | Önayar arasından seç: **titreşim (Double)** seçeneğini belirleyin. <br /> Note: Azure Time Series Insights Gen2 olayları almaya başladıktan sonra **değerin** otomatik olarak doldurulması birkaç dakika sürebilir.|
        | **Toplama Işlemi** | **Gelişmiş Seçenekler**' i genişletin. <br /> **Ort**' ı seçin. |

    1. **Uygula**’yı seçin. Sonra, **+ değişkeni tekrar ekleyin** ve üçüncü ve son değişken için aşağıdaki değerleri ayarlayın:

        | Parametre | Eylem |
        | --- | --- |
        | **Ad** | **Kat** girin. |
        | **Tip** | **Kategorik** seçin |
        | **Değer** | Önayar arasından seç: **kat (Double)** seçeneğini belirleyin. <br /> Note: Azure Time Series Insights Gen2 olayları almaya başladıktan sonra **değerin** otomatik olarak doldurulması birkaç dakika sürebilir.|
        | **Kategoriler** | <span style="text-decoration: underline">Etiketi</span>   -  <span style="text-decoration: underline">Değerler</span> <br /> Düşük: 1, 2, 3, 4 <br /> Orta: 5, 6, 7, 8, 9 <br /> Üst: 10, 11, 12, 13, 14, 15 |
        | **Varsayılan kategori** | **Bilinmeyen** girin |

        [![Tür değişkenleri ekleyin.](media/tutorial-set-up-environment/add-type-variables.png)](media/tutorial-set-up-environment/add-type-variables.png#lightbox)

    1. **Uygula**’yı seçin.
    1. **Kaydet**’i seçin. Üç değişken oluşturulur ve görüntülenir.

        [![Tür eklendikten sonra Model görünümünde gözden geçirin.](media/tutorial-set-up-environment/add-type-and-view.png)](media/tutorial-set-up-environment/add-type-and-view.png#lightbox)

1. **Hiyerarşiler** sekmesini seçin. Ardından **+ Ekle**' yi seçin.

   1. **Hiyerarşiyi Düzenle** bölmesinde aşağıdaki parametreleri ayarlayın:

        | Parametre | Eylem |
        | --- | ---|
        | **Ad** | **Konum hiyerarşisini** girin. |
        |**Düzeyler**| **Ülkeyi** ilk düzeyin adı olarak girin<br />**+ Düzey Ekle** seçeneğini belirleyin<br />İkinci düzey için **şehir** girin ve ardından **+ düzey Ekle** ' yi seçin.<br />Üçüncü ve son düzeyin adı olarak **oluşturma** girin |

   1. **Kaydet**’i seçin.

        [![Yeni hiyerarşinizi Model görünümünde görüntüleyin.](media/tutorial-set-up-environment/add-hierarchy-and-view.png)](media/tutorial-set-up-environment/add-hierarchy-and-view.png#lightbox)

1. **Örneklere** gidin.

    1. En sağdaki **Eylemler** altında, ilk örneği aşağıdaki değerlerle düzenlemek için kurşun kalem simgesini seçin:

        | Parametre | Eylem |
        | --- | --- |
        | **Tür** | **Asansör** öğesini seçin. |
        | **Ad** | **Asansör 1** girin|
        | **Açıklama** | **Asansör 1 Için örnek** girin |

    1. **Örnek alanlarına** gidin ve aşağıdaki değerleri girin:

        | Parametre | Eylem |
        | --- | --- |
        | **Hiyerarşiler** | **Konum hiyerarşisini** seçin |
        | **Ülke** | **USA** girin |
        | **Şehir** | **Seattle** girin |
        | **Yapım** | **Space iğne** girin |

    1. **Kaydet**’i seçin.

1. Aşağıdaki değerleri kullanırken önceki adımı diğer iki örnek ile tekrarlayın:

    **Asansör 2 için:**

    | Parametre | Eylem |
    | --- | --- |
    | **Tür** | **Asansör** öğesini seçin. |
    | **Ad** | **Asansör 2** girin|
    | **Açıklama** | **Asansör Için örnek girin 2** |
    | **Hiyerarşiler** | **Konum hiyerarşisini** seçin |
    | **Ülke** | **USA** girin |
    | **Şehir** | **Seattle** girin |
    | **Yapım** | **Pasifik bilimi merkezini** girin |

    **Asansör 3 için:**

    | Parametre | Eylem |
    | --- | --- |
    | **Tür** | **Asansör** öğesini seçin. |
    | **Ad** | **Asansör 3** girin|
    | **Açıklama** | **Asansör 3 Için örnek** girin |
    | **Hiyerarşiler** | **Konum hiyerarşisini** seçin |
    | **Ülke** | **USA** girin |
    | **Şehir** | **New York** girin |
    | **Yapım** | **Empire durum oluşturmayı** girin |

    [![Güncelleştirilmiş örnekleri görüntüleyin.](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png)](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png#lightbox)

1. Grafik bölmesini görüntülemek için **Çözümle** sekmesine geri gidin. **Konum hiyerarşisi** altında, zaman serisi örneklerini göstermek için tüm hiyerarşi düzeylerini genişletin:

    [![Tüm hiyerarşileri grafik görünümünde görüntüleyin.](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png)](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. **Pasifik bilimi merkezi** altında, **Asansör 2** zaman serisi örneğini seçin ve sonra **ortalama sıcaklığı göster**' i seçin.

1. Aynı örnek için, **Asansör 2** Için **tabanı göster**' i seçin.

    Kategorik değişkeniniz sayesinde, Asansör üst, alt ve orta kat üzerinde ne kadar zaman harcadığını belirleyebilirsiniz.

    [![Hiyerarşi ve verilerle Asansör 2 ' i görselleştirin.](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png)](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiyi tamamladığınıza göre, oluşturduğunuz kaynakları temizleyin:

1. [Azure Portal](https://portal.azure.com)sol menüden **tüm kaynaklar**' ı seçin, Azure Time Series Insights Gen2 kaynak grubunuzu bulun.
1. Her kaynağı ayrı ayrı **Sil** veya Kaldır ' a tıklayarak kaynak grubunun tamamını (ve içinde yer alan tüm kaynakları) silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

* Cihaz benzetimi hızlandırıcıyı oluşturun ve kullanın.
* Azure Time Series Insights Gen2 PAYG ortamı oluşturun.
* Azure Time Series Insights Gen2 ortamını bir IoT Hub 'ına bağlayın.
* Azure Time Series Insights Gen2 ortamına veri akışı sağlamak için bir çözüm Hızlandırıcısı örneği çalıştırın.
* Verilerin temel analizini yapın.
* Bir zaman serisi model türü ve hiyerarşisi tanımlayın ve bunları örneklerle ilişkilendirin.

Artık kendi Azure Time Series Insights Gen2 ortamınızı oluşturmayı bildiğinize göre, Azure Time Series Insights Gen2 ' deki temel kavramlar hakkında daha fazla bilgi edinin.

Azure Time Series Insights Gen2 alma hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 veri alımı genel bakış](./concepts-ingestion-overview.md)

Azure Time Series Insights Gen2 Storage hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 veri depolama](./concepts-storage.md)

Zaman serisi modelleri hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 veri modelleme](./concepts-model-overview.md)

Ortamınızı Power BI bağlama hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Power BI Azure Time Series Insights Gen2 'tan verileri görselleştirme](./how-to-connect-power-bi.md)
