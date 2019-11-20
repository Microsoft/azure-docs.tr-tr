---
title: 'Öğretici: bir Azure zaman serisi öngörüleri Önizleme ortamı ayarlama'
description: 'Öğretici: ortamınızı Azure Time Series Insights önizlemede ayarlamayı öğrenin.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/11/2019
ms.custom: seodec18
ms.openlocfilehash: 49960ae91688b97a9d38a65e1b17fd277508b996
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114577"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Öğretici: bir Azure zaman serisi öngörüleri Önizleme ortamı ayarlama

Bu öğretici, bir Azure Time Series Insights Preview Kullandıkça Öde (PAYG) ortamı oluşturma sürecinde size rehberlik eder.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Azure Time Series Insights önizleme ortamı oluşturun.
> * Azure Time Series Insights Preview ortamını bir IoT Hub bağlayın.
> * Azure Time Series Insights önizleme ortamına veri akışı sağlamak için bir çözüm Hızlandırıcısı örneği çalıştırın.
> * Temel analiz verileri gerçekleştirin.
> * Bir zaman serisi model türü ve hiyerarşisi tanımlayın ve bunları örneklerle ilişkilendirin.
> * Power BI bağlayıcısını kullanın ve Power BI verileri görselleştirin.

>[!TIP]
> [IoT Çözüm Hızlandırıcıları](https://www.azureiotsolutions.com/Accelerators) , özel IoT çözümlerinin geliştirilmesini hızlandırmak için kullanabileceğiniz kurumsal sınıf önceden yapılandırılmış çözümler sunar.

Henüz yoksa ücretsiz bir [Azure aboneliğine](https://azure.microsoft.com/free/) kaydolun.

## <a name="prerequisites"></a>Önkoşullar

* En azından, Azure aboneliği için **katkıda bulunan** rolüne sahip olmanız gerekir. Daha fazla bilgi için bkz. [rol tabanlı erişim denetimi ve Azure Portal kullanarak erişimi yönetme](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Cihaz benzetimi oluşturma

Bu bölümde, bir Azure IoT Hub örneğine veri gönderen üç sanal cihaz oluşturacaksınız.

1. [Azure IoT Çözüm Hızlandırıcıları sayfasına](https://www.azureiotsolutions.com/Accelerators)gidin. Sayfada, önceden oluşturulmuş birkaç örnek görüntülenir. Azure hesabınızı kullanarak oturum açın. Sonra **cihaz benzetimi**' ni seçin.

   [![Azure IoT Çözüm Hızlandırıcıları sayfası](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

1. Sonraki sayfada **Şimdi dene**' yi seçin.

   [Cihaz benzetimi sayfası ![](media/v2-update-provision/device-two-try.png)](media/v2-update-provision/device-two-try.png#lightbox)

1. **Cihaz simülasyonu çözümü oluştur** sayfasında, aşağıdaki parametreleri ayarlayın:

    | Parametre | Eylem |
    | --- | --- |
    | **Dağıtım adı** | Yeni bir kaynak grubu için benzersiz bir değer girin. Listelenen Azure kaynakları oluşturulur ve kaynak grubuna atanır. |
    | **Azure aboneliği** | Time Series Insights ortamınızı oluşturacağınız aboneliği seçin. |
    | **Azure konumu** | Time Series Insights ortamınızı depolamayı planladığınız bölgeyi seçin. Cihaz benzeticisinin yalnızca sınırlı sayıda bölgede sunuldığına, bu nedenle istediğiniz bölgeyi görmüyorsanız yalnızca öğreticide bir konum seçin ve sonra da yeni bir TSI ortamı oluşturarak-b ' nin sonraki aşamasına geçmeye hazırsınız demektir. oarding.  |
    | **Dağıtım seçenekleri** | **Yeni IoT Hub sağla**' yı seçin. |

    1. **Oluştur**'u seçin.
    [Cihaz benzetimi sayfası ![](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Yaklaşık 20 dakika sonra çözüm hızlandırıcılarınız hazırlanacaktır.

    [Cihaz benzetimi sayfası ![](media/v2-update-provision/device-two-ready.png)](media/v2-update-provision/device-two-ready.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Önizleme PAYG ortamı oluşturma

Bu bölümde, Azure Time Series Insights önizleme ortamının nasıl oluşturulacağı ve [Azure Portal](https://portal.azure.com/)kullanılarak IoT Çözüm Hızlandırıcısı tarafından oluşturulan IoT Hub 'ına nasıl bağlanacağı açıklanır.

1. Azure portalında abonelik hesabınızı kullanarak oturum açın.

1. **Time Series Insights** > **nesnelerin interneti** > **kaynak oluştur** ' u seçin.

   [Nesnelerin İnterneti ' ![seçin ve Time Series Insights ' yı seçin.](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. **Time Series Insights ortamı oluştur** bölmesinde, **temel bilgiler** sekmesinde, aşağıdaki parametreleri ayarlayın:

    | Parametre | Eylem |
    | --- | ---|
    | **Ortam adı** | Azure Time Series Insights önizleme ortamı için benzersiz bir ad girin. |
    | **Abonelik** | Azure Time Series Insights Preview ortamını oluşturmak istediğiniz aboneliği girin. En iyi uygulama, cihaz simülatörü tarafından oluşturulan diğer IoT kaynaklarıyla aynı aboneliği kullanmaktır. |
    | **Kaynak grubu** | Mevcut bir kaynak grubunu seçin veya Azure Time Series Insights Preview ortamı kaynağı için yeni bir kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarına yönelik bir kapsayıcıdır. En iyi uygulama, cihaz simülatörü tarafından oluşturulan diğer IoT kaynaklarıyla aynı kaynak grubunu kullanmaktır. |
    | **Konum** | Azure Time Series Insights Preview ortamınız için bir veri merkezi bölgesi seçin. Ek gecikme süresini önlemek için, cihaz simülatörü tarafından oluşturulan IoT Hub 'ınız ile aynı bölgede Azure Time Series Insights önizleme ortamınızı oluşturmak en iyisidir. |
    | **Tier** |  **PAYG** (*Kullandıkça Öde*) öğesini seçin. Bu, Azure Time Series Insights önizleme ürünü için SKU 'dır. |
    | **Özellik KIMLIĞI** | Zaman serisi örneğinizi benzersiz bir şekilde tanımlayan bir değer girin. **ÖZELLIK kimliği** kutusuna girdiğiniz değer daha sonra değiştirilemez. Bu öğretici için **ıothub-Connection-Device-ID**girin. Zaman serisi KIMLIĞI hakkında daha fazla bilgi edinmek için bkz. [bir zaman SERISI kimliği seçmek Için en iyi uygulamalar](./time-series-insights-update-how-to-id.md). |
    | **Depolama hesabı adı** | Yeni bir depolama hesabı için genel olarak benzersiz bir ad girin.|
    |**Isınma deposunu etkinleştir**|Isınma mağazasını etkinleştirmek için **Evet** ' i seçin.|
    |**Veri bekletme (gün)**|Varsayılan 7 gün seçeneğini belirleyin. |

    **İleri ' yi seçin: olay kaynağı**.

   [Time Series Insights ortamı oluşturmak için![bölmesi](media/v2-update-provision/payg-two-2-create.png)](media/v2-update-provision/payg-two-2-create.png#lightbox)
   [Time Series Insights ortamı oluşturmaya yönelik![bölmesi](media/v2-update-provision/payg-two-1-create.png)](media/v2-update-provision/payg-two-1-create.png#lightbox)

1. **Olay kaynağı** sekmesinde, aşağıdaki parametreleri ayarlayın:

   | Parametre | Eylem |
   | --- | --- |
   | **Olay kaynağı oluşturulsun mu?** | **Evet**’i seçin.|
   | **Adı** | Olay kaynağı adı için benzersiz bir değer girin. |
   | **Kaynak türü** | **IoT Hub**seçin. |
   | **Bir hub seçin** | **Varolanı Seç ' i**seçin. |
   | **Abonelik** | Cihaz simülatörü için kullandığınız aboneliği seçin. |
   | **IoT Hub adı** | Cihaz simülatörü için oluşturduğunuz IoT Hub adını seçin. |
   | **IoT Hub erişim ilkesi** | **İothubowner**öğesini seçin. |
   | **IoT Hub Tüketici grubu** | **Yeni**' yi seçin, benzersiz bir ad girin ve ardından **Ekle**' yi seçin. Tüketici grubu Azure Time Series Insights önizlemede benzersiz bir değer olmalıdır. |
   | **TimeStamp Özelliği** | Bu değer, gelen telemetri verilerinizde **zaman damgası** özelliğini tanımlamak için kullanılır. Bu öğretici için bu kutuyu boş bırakın. Bu simülatör, Time Series Insights varsayılan olarak IoT Hub gelen zaman damgasını kullanır. |

   **İncele ve oluştur**’u seçin.

   [Olay kaynağını yapılandırma ![](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. **Oluştur**'u seçin.

    [![gözden geçir + oluştur düğmesi ile sayfa oluştur](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Dağıtımınızın durumunu görebilirsiniz:

    [dağıtımın tamamlandığını ![bildirimi](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Azure aboneliğinin sahibiyseniz, varsayılan olarak Azure Time Series Insights önizleme ortamınıza erişebilirsiniz. Erişiminizin olduğunu doğrulayın:

   1. Kaynak grubunuzu arayın ve ardından yeni oluşturduğunuz Azure Time Series Insights önizleme ortamınızı seçin. 
      [Seçili ortamı ![](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Azure Time Series Insights önizleme sayfasında, **veri erişim ilkeleri**: [![veri erişim ilkeleri](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox) ' ni seçin.

   1. Kimlik bilgilerinizin listelendiğini doğrulayın:

      [![listelenen kimlik bilgileri](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Kimlik bilgileriniz listelenmiyorsa, kimlik bilgileriniz için Ekle ve ara ' yı seçerek ortama erişmek için kendinize izin vermeniz gerekir. İzinleri ayarlama hakkında daha fazla bilgi edinmek için bkz. [veri erişimi izni](./time-series-insights-data-access.md)okuma.

## <a name="stream-data"></a>Veri akışı yapma

Time Series Insights ortamınızı dağıttığınıza göre, analiz için veri akışı oluşturmaya başlayabilirsiniz.

1. [Azure IoT Çözüm Hızlandırıcıları sayfasına](https://www.azureiotsolutions.com/Accelerators)geri gidin. Çözümünüzü Çözüm Hızlandırıcısı panosunda bulun ve **Başlat**' ı seçin:

    [cihaz benzetimi çözümünü ![başlatın](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. **Çözüm hızlandırıcısına git**' i seçin.

    [cihaz benzetimi çözümünü ![başlatın](media/v2-update-provision/device-accelerator.png)](media/v2-update-provision/device-accelerator.png#lightbox)

1. **Microsoft Azure IoT cihaz benzetimi** sayfasına yönlendirilirsiniz. Sayfanın sağ üst köşesinde **Yeni simülasyon**' ı seçin.

    [![Azure IoT benzetim sayfası](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. **Simülasyon kurulum** bölmesinde aşağıdaki parametreleri ayarlayın:

    | Parametre | Eylem |
    | --- | --- |
    | **Adı** | Simülatör için benzersiz bir ad girin. |
    | **Açıklama** | Bir tanım girin. |
    | **Benzetim süresi** | Süresiz olarak **çalışacak**şekilde ayarlayın. |
    | **Cihaz modeli** | \+ **Cihaz türü Ekle** ' ye tıklayın <br />**Ad**: **Asansör**girin. <br />**Tutar**: **3**girin. <br /> Kalan varsayılan değerleri bırakın |
    | **Hedef IoT Hub'ı** | **Önceden sağlanmış IoT Hub kullanılacak**şekilde ayarlayın. |

    [Ayarlanacak parametreleri ![](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    **Benzetimi Başlat**' ı seçin.

    Cihaz benzetimi panosunda, **etkin cihazlar** ve **Toplam mesaj**görürsünüz.

    [Azure IoT simülasyonu panosu ![](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Verileri çözümleme

Bu bölümde, [Azure Time Series Insights önizleme Gezginini](./time-series-insights-update-explorer.md)kullanarak zaman serisi verilerinizde temel analiz gerçekleştirirsiniz.

1. [Azure Portal](https://portal.azure.com/)kaynak sayfasından URL 'yi seçerek Azure Time Series Insights önizleme Gezgini 'ne gidin.

    [Time Series Insights önizleme Gezgini URL 'sini ![](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. Time Series Insights Gezgini 'nde, ekranın üst kısmına yayılan bir çubuk görürsünüz. Bu, kullanılabilirlik seçicinizdeki bir değer. En az iki 2m seçtiğinizden emin olun ve gerekirse, seçici tutamaçlarını seçip sola ve sağa sürükleyerek zaman çerçevesini genişletin.

1. Sol tarafta **zaman serisi örneklerini** görebileceksiniz.


    [üst öğe olmayan örneklerin ![listesi](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. İlk zaman serisi örneğini seçin. Ardından, **basınç göster**' i seçin.

    [Ortalama basıncı göstermek için menü komutuyla seçili zaman serisi örneğini ![](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Bir zaman serisi grafiği görüntülenir. **Aralığı** **30 saniye**olarak değiştirin.

    [![zaman serisi grafiği](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Bu grafikte gösterildiği gibi, üçü de görüntülemek için 3. adımı diğer iki zaman serisi örneğiyle yineleyin:

    [Tüm zaman serileri için grafik ![](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Sağ üst köşedeki zaman aralığı seçicisini seçin. Burada, belirli başlangıç ve bitiş zamanlarını milisaniyeye doğru seçebilirsiniz veya son saat gibi önceden yapılandırılmış seçenekler arasından seçim yapabilirsiniz. Varsayılan saat dilimini de değiştirebilirsiniz.

    [![saat aralığını bir saate ayarlayın](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

    Aşağıda bir saat için benzetim çalıştırıldıktan sonra Grafik bölmesinin ekran görüntüsü verilmiştir:

    [![grafik bölmesi](media/v2-update-provision/analyze-seven-time.png)](media/v2-update-provision/analyze-seven-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Model tanımlama ve uygulama

Bu bölümde, verilerinizi yapılandırmak için bir model uygularsınız. Modeli tamamlayabilmeniz için türler, hiyerarşiler ve örnekler tanımlarsınız. Veri modelleme hakkında daha fazla bilgi için bkz. [zaman serisi modeli](./time-series-insights-update-tsm.md).

1. Gezgin 'de **model** sekmesini seçin:

   [Gezgin 'de ![model sekmesi](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. **Türler** sekmesinde **Ekle**' yi seçin.

   [türler için Ekle düğmesini ![](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Aşağıdaki parametreleri girin:

    | Parametre | Eylem |
    | --- | ---|
    | **Adı** | **Asansör** girin |
    | **Açıklama** | **Bu, Asansör için bir tür tanımı** girin |

    [türler için Ekle düğmesini ![](media/v2-update-provision/define-three-properties.png)](media/v2-update-provision/define-three-properties.png#lightbox)

1. Sonra, **değişkenler** sekmesini seçin. [![değişkenler sekmesini seçin](media/v2-update-provision/define-four-variables.png)](media/v2-update-provision/define-four-variables.png#lightbox)

1. **+ Değişken Ekle** ' yi seçin ve Asansör türünün ilk değişkeni için aşağıdaki değerleri girin. Toplam olarak üç değişken yazardan olursunuz.

    | Parametre | Eylem |
    | --- | --- |
    | **Adı** | **Ort sıcaklık**girin. |
    | **Denetlenmesi** | **Sayısal** seçin |
    | **Değer** | Önayar arasından seç: **sıcaklık (Double)** seçeneğini belirleyin. <br /> Note: Azure Time Series Insights önizlemesi olayları almaya başladıktan sonra **değerin** otomatik olarak doldurulması birkaç dakika sürebilir.|
    | **Toplama Işlemi** | **Gelişmiş Seçenekler**' i genişletin. <br /> **Ort**' ı seçin. |

    [sıcaklık tanımlama ![seçimleri](media/v2-update-provision/define-five-variable.png)](media/v2-update-provision/define-five-variable.png#lightbox)

    **Uygula**’yı seçin.

    **+ Değişken Ekle** ' yi seçin ve aşağıdaki değerleri ayarlayın:

    | Parametre | Eylem |
    | --- | --- |
    | **Adı** | **Ortalama titreşim**girin. |
    | **Denetlenmesi** | **Sayısal** seçin |
    | **Değer** | Önayar arasından seç: **titreşim (Double)** seçeneğini belirleyin. <br /> Note: Azure Time Series Insights önizlemesi olayları almaya başladıktan sonra **değerin** otomatik olarak doldurulması birkaç dakika sürebilir.|
    | **Toplama Işlemi** | **Gelişmiş Seçenekler**' i genişletin. <br /> **Ort**' ı seçin. |

    [titreşimi tanımlamaya yönelik ![seçimleri](media/v2-update-provision/define-five-vibration.png)](media/v2-update-provision/define-five-vibration.png#lightbox)

    **Uygula**’yı seçin.

    **+ Değişken Ekle** ' yi seçin ve üçüncü ve son değişken için aşağıdaki değerleri ayarlayın:

    | Parametre | Eylem |
    | --- | --- |
    | **Adı** | **Kat**girin. |
    | **Denetlenmesi** | **Kategorik** seçin |
    | **Değer** | Önayar arasından seç: **kat (Double)** seçeneğini belirleyin. <br /> Note: Azure Time Series Insights önizlemesi olayları almaya başladıktan sonra **değerin** otomatik olarak doldurulması birkaç dakika sürebilir.|
    | **Kategorisine** | <span style="text-decoration: underline">Etiket</span>  - <span style="text-decoration: underline">değerleri</span> <br /> Düşük: 1, 2, 3, 4 <br /> Orta: 5, 6, 7, 8, 9 <br /> Üst: 10, 11, 12, 13, 14, 15 |
    | **Varsayılan kategori** | **Bilinmeyen** girin |

    [titreşimi tanımlamaya yönelik ![seçimleri](media/v2-update-provision/define-five-floor.png)](media/v2-update-provision/define-five-floor.png#lightbox)

    **Uygula**’yı seçin. Oluşturulan üç değişken görmeniz gerekir:

    [titreşimi tanımlamaya yönelik ![seçimleri](media/v2-update-provision/define-five-floor2.png)](media/v2-update-provision/define-five-floor2.png#lightbox)

   **Kaydet**’i seçin. Oluşturulan **türü** görürsünüz:

    [titreşimi tanımlamaya yönelik ![seçimleri](media/v2-update-provision/define-five-floor3.png)](media/v2-update-provision/define-five-floor3.png#lightbox)

1. **Hiyerarşiler** sekmesini seçin. **+ Ekle**' yi seçin.

    [Ekle düğmesi ile hiyerarşi sekmesi ![](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. **Hiyerarşiyi Düzenle** bölmesinde aşağıdaki parametreleri ayarlayın:

   | Parametre | Eylem |
   | --- | ---|
   | **Adı** | **Konum hiyerarşisini**girin. |
   |**Düzeyleri**| **Ülkeyi** ilk düzeyin adı olarak girin <br> **+ Düzey Ekle** seçeneğini belirleyin <br> İkinci düzey için **şehir** girin ve ardından **+ düzey Ekle** ' yi seçin. <br> Üçüncü ve son düzeyin adı olarak **oluşturma** girin |

   **Kaydet**’i seçin.

    [Oluşturma düğmesi olan hiyerarşi alanlarını ![](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Oluşturduğunuz hiyerarşiyi görebilirsiniz:

    [hiyerarşi hakkında bilgi ![](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. **Örneklere**gidin. En sağdaki **Eylemler** altında, ilk örneği aşağıdaki değerlerle düzenlemek için kurşun kalem simgesini seçin:

    | Parametre | Eylem |
    | --- | --- |
    | **Tür** | **Asansör**öğesini seçin. |
    | **Adı** | **Asansör 1** girin|
    | **Açıklama** | **Asansör 1 Için örnek** girin |

    [örnek için Düzenle düğmesini seçme ![](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

    **Örnek alanlarına** gidin ve aşağıdakileri girin:

    | Parametre | Eylem |
    | --- | --- |
    | **Hiyerarşileri** | **Konum hiyerarşisini** seçin |
    | **Ülke** | **USA** girin |
    | **Baş** | **Seattle** girin |
    | **Yapım** | **Space iğne** girin |

    [örnek için Düzenle düğmesini seçme ![](media/v2-update-provision/define-ten2-edit.png)](media/v2-update-provision/define-ten2-edit.png#lightbox)

    **Kaydet**’i seçin.

1. Aşağıdaki değerlere sahip diğer iki örnek ile 8. adımı yineleyin:

    <span style="text-decoration: underline;">Asansör 2 için</span>:

    | Parametre | Eylem |
    | --- | --- |
    | **Tür** | **Asansör**öğesini seçin. |
    | **Adı** | **Asansör 2** girin|
    | **Açıklama** | **Asansör Için örnek girin 2** |
    | **Hiyerarşileri** | **Konum hiyerarşisini** seçin |
    | **Ülke** | **USA** girin |
    | **Baş** | **Seattle** girin |
    | **Yapım** | **Pasifik bilimi merkezini** girin |

    <span style="text-decoration: underline;">Asansör 3 için</span>:

    | Parametre | Eylem |
    | --- | --- |
    | **Tür** | **Asansör**öğesini seçin. |
    | **Adı** | **Asansör 3** girin|
    | **Açıklama** | **Asansör 3 Için örnek** girin |
    | **Hiyerarşileri** | **Konum hiyerarşisini** seçin |
    | **Ülke** | **USA** girin |
    | **Baş** | **New York** girin |
    | **Yapım** | **Empire durum oluşturmayı** girin |

1. Grafik bölmesini görüntülemek için **Çözümle** sekmesine geri gidin. **Konum hiyerarşisi**altında, zaman serisi örneklerini göstermek için tüm hiyerarşi düzeylerini genişletin:

   [Çözümle sekmesine ![](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. **Pasifik bilimi merkezi**altında, **Asansör 2**zaman serisi örneğini seçin ve sonra **ortalama sıcaklığı göster**' i seçin.

    [![zaman serisi örneği ortalama sıcaklığın gösterilmesi](media/v2-update-provision/define-eleven-temperature.png)](media/v2-update-provision/define-eleven-temperature.png#lightbox)

1. Aynı örnek için, **Asansör 2**Için **tabanı göster**' i seçin.

    [![zaman serisi örneği Floor 'yi göster](media/v2-update-provision/define-twelve-floor.png)](media/v2-update-provision/define-twelve-floor.png#lightbox)

    Kategorik değişkeniniz sayesinde, Asansör üst, alt ve orta kat üzerinde ne kadar zaman harcadığını belirleyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiyi tamamladığınıza göre, oluşturduğunuz kaynakları temizleyin:

1. [Azure Portal](https://portal.azure.com)sol menüden **tüm kaynaklar**' ı seçin, Azure Time Series Insights kaynak grubunuzu bulun.
1. Her kaynağı ayrı ayrı **Sil** veya Kaldır ' a tıklayarak kaynak grubunun tamamını (ve içinde yer alan tüm kaynakları) silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:  

* Cihaz benzetimi hızlandırıcıyı oluşturun ve kullanın.
* Azure Time Series Insights Preview PAYG ortamı oluşturun.
* Azure Time Series Insights Preview ortamını bir IoT Hub 'ına bağlayın.
* Azure Time Series Insights önizleme ortamına veri akışı sağlamak için bir çözüm Hızlandırıcısı örneği çalıştırın.
* Verilerin temel bir analiz gerçekleştirin.
* Bir zaman serisi model türü ve hiyerarşisi tanımlayın ve bunları örneklerle ilişkilendirin.

Şimdi kendi Azure Time Series Insights önizleme ortamınızı oluşturmayı bildiğinize göre, Azure Time Series Insights temel kavramlar hakkında daha fazla bilgi edinin.

Azure Time Series Insights depolama yapılandırması hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [Azure zaman serisi öngörüleri Önizleme depolama ve giriş](./time-series-insights-update-storage-ingress.md)

Zaman serisi modelleri hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Azure zaman serisi öngörüleri Önizleme veri modelleme](./time-series-insights-update-tsm.md)
