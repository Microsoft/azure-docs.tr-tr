---
title: 'Öğretici: Azure IoT Central yeni bir cihaz türü tanımlama'
description: Bu öğreticide, bir oluşturucu olarak Azure IoT Central uygulamanızda yeni bir cihaz türünü nasıl tanımlayacağınız gösterilir. Türü için telemetri, durum, özellik ve ayarları tanımlarsınız.
author: dominicbetts
ms.author: dobett
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 3d79e710f2c71d96179ab8774ee89c90cb462ca4
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112358"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Öğretici: Azure IoT Central uygulamanızda yeni bir cihaz türü tanımlama

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu öğreticide, bir oluşturucu olarak Microsoft Azure IoT Central uygulamanızda yeni bir cihaz türü tanımlamak için cihaz şablonunu nasıl tanımlayacağınız gösterilir. Cihaz şablonu, cihaz türünüz için telemetriyi, durumu, özellikleri ve ayarları tanımlar.

Gerçek bir cihaza bağlamadan önce uygulamanızı test edebilmenize olanak tanımak için, siz cihaz şablonunu oluşturduğunuzda IoT Central şablondan bir simülasyon cihazı oluşturur.

Bu öğreticide, bir **Bağlı Klima** cihaz şablonu oluşturursunuz. Bağlı klima cihazı:

* Sıcaklık ve nem gibi telemetri verilerini gönderir.
* , Açık veya kapalı olsun gibi bir durum bildirir.
* Üretici yazılımı sürümü ve seri numarası gibi cihaz özelliklerine sahiptir.
* Hedef sıcaklık gibi ayarlara sahiptir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yeni cihaz şablonu oluşturma
> * Cihazınıza telemetri verileri ekleme
> * Simülasyon telemetrisini görüntüleme
> * Olay ölçümünü tanımlama
> * Simülasyon olaylarını görüntüleme
> * Durum ölçümünü tanımlama
> * Simülasyon durumunu görüntüleme
> * Ayarları ve özellikleri kullanma
> * Komutları kullanma
> * Simülasyon cihazınızı panoda görüntüleme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için, **özel uygulama** şablonundan oluşturulmuş bir Azure IoT Central uygulamasına ihtiyacınız vardır. Uygulamanız yoksa, **özel uygulama** şablonunu seçtiğinizden emin olmak için [Azure IoT Central uygulaması oluşturma](quick-deploy-iot-central.md) hızlı başlangıcı ' nı doldurun.

## <a name="create-a-device-template"></a>Cihaz şablonu oluşturma

Bir oluşturucu olarak, uygulamanızda cihaz şablonları oluşturabilir ve bunları düzenleyebilirsiniz. Cihaz şablonunu oluşturduğunuzda, Azure IoT Central şablon için bir simülasyon cihazı oluşturur. Sanal cihaz, gerçek bir cihaza bağlanmadan önce uygulamanızın davranışını test etmenizi sağlayan telemetri üretir.

Uygulamanıza yeni bir cihaz şablonu eklemek için **cihaz şablonları** sayfasına gitmeniz gerekir. Bunu yapmak için sol bölmedeki **cihaz şablonlarını** seçin.

![Cihaz şablonları sayfası](./media/tutorial-define-device-type/devicetemplates.png)

## <a name="add-a-device-template"></a>Cihaz şablonu ekleme

Aşağıdaki adımlar, uygulamanıza sıcaklık telemetrisi gönderen cihazlar için yeni bir **Bağlı Klima** cihaz şablonunu nasıl oluşturacağınızı gösterir:

1. **Cihaz şablonları** sayfasında **+ Yeni**' yi seçin:

    ![Cihaz şablonları sayfası, cihaz şablonu oluştur](./media/tutorial-define-device-type/newtemplate.png)

2. Sayfa, aralarından seçim yapabileceğiniz şablonları gösterir.

    ![Cihaz şablonu kitaplığı](./media/tutorial-define-device-type/devicetemplatelibrary.png)

3. **Özel**' i seçin, **bağlı hava koşullayıcı** ' i cihaz şablonunuzun adı olarak girin ve ardından **Oluştur**' u seçin. Device Explorer'da cihazınızın operatörlerin görebileceği bir resmini de karşıya yükleyebilirsiniz:

    ![Özel Cihaz](./media/tutorial-define-device-type/createcustomdevice.png)

4. **Bağlı hava** cihazı cihaz şablonunda, Telemetriyi tanımladığınız **ölçümler** sekmesinde olduğunuzdan emin olun. Tanımladığınız her cihaz şablonunun sizin için ayrı sekmeleri vardır:

   * Cihaz tarafından gönderilen telemetri, olay ve durum gibi _ölçümleri_belirtin.

   * Cihazı denetlemek için kullanılan _ayarları_ tanımlayın.

   * Cihaz meta verileri olan _özellikleri_ tanımlayın.

   * Doğrudan cihazda çalıştırılacak _komutları_ tanımlayın.

   * Cihazla ilişkili _kuralları_ tanımlayın.

   * Operatörlerinizin cihaz _panosunu_ özelleştirin.

     ![Klima ölçümleri](./media/tutorial-define-device-type/airconmeasurements.png)

     > [!NOTE]
     > Cihaz şablonunun adını değiştirmek için sayfanın üst kısmındaki şablon adını seçin.

5. Sıcaklık telemetri ölçümünü eklemek için **+ yeni ölçüm**' i seçin. Ardından ölçüm türü olarak **Telemetri** seçin:

    ![Bağlı klima ölçümleri](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Cihaz şablonu için oluşturduğunuz her telemetri türü aşağıdakiler gibi [yapılandırma seçenekleri](howto-set-up-template.md) içerir:

   * Görüntü seçenekleri.

   * Telemetrinin ayrıntıları.

   * Simülasyon parametreleri.

     **Sıcaklık** telemetrinizi yapılandırmak için aşağıdaki tabloda yer alan bilgileri kullanın:

     | Ayar              | Değer         |
     | -------------------- | -----------   |
     | Görünen Ad         | Sıcaklık   |
     | Alan Adı           | sıcaklık   |
     | Birimler                | F             |
     | Min                  | 60            |
     | Maks                  | 110           |
     | Ondalık basamak sayısı       | 0             |

     Telemetri görüntüsü için renk de seçebilirsiniz. Telemetri tanımını kaydetmek için **Kaydet**' i seçin:

     ![Sıcaklık simülasyonunu yapılandırma](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Daha kısa bir süre sonra **ölçümler** sekmesi, sanal cihaz koşullarınızdan gelen sıcaklık telemetrinin bir grafiğini gösterir. Görünürlüğü, toplamayı yönetmek veya telemetri tanımını düzenlemek için denetimleri kullanın:
 
    > [!NOTE]
    > Telemetri için, **Ortalama** varsayılan toplama olarak ayarlanır. 

    ![Sıcaklık simülasyonunu görüntüleme](./media/tutorial-define-device-type/viewsimulation.png)

8. Ayrıca, **Çizgi**, **Yığılmış** ve **Zaman Aralığını Düzenle** denetimlerini kullanarak grafiği de özelleştirebilirsiniz:

    ![Grafiği özelleştirme](./media/tutorial-define-device-type/customizechart.png)

## <a name="add-an-event-measurement"></a>Olay ölçümü ekleme

Bir hata veya bileşen hatası gibi bir olay olduğunda cihazın gönderdiği zaman içindeki verileri tanımlamak için olayları kullanın. Azure IoT Central, gerçek bir cihaza bağlanmadan önce uygulamanızın davranışını sınamanızı sağlamak üzere cihaz olaylarının benzetimini yapabilir. **Ölçüm** görünümündeki cihaz şablonunuz için olay ölçümlerini tanımlayın.

1. **Fan motoru hata** olayı ölçümünü eklemek Için **+ yeni ölçüm**' i seçin. Ardından ölçüm türü olarak **Olay** seçin:

    ![Bağlı klima ölçümleri](./media/tutorial-define-device-type/eventnew.png)

2. Cihaz şablonu için oluşturduğunuz her Olay türü aşağıdakiler gibi [yapılandırma seçenekleri](howto-set-up-template.md) içerir:

   * Görünen Ad

   * Alan Adı.

   * Önem Derecesi.

     **Fan Motoru Hatası** olayınızı yapılandırmak için aşağıdaki tabloda yer alan bilgileri kullanın:

     | Ayar              | Değer             |
     | -------------------- | -----------       |
     | Görünen Ad         | Fan Motoru Hatası   |
     | Alan Adı           | fanmotorerr       |
     | Severity             | Hata             |

     Olay tanımını kaydetmek için **Kaydet**' i seçin:

     ![Olay ölçümünü yapılandırma](./media/tutorial-define-device-type/eventconfiguration.png)

3. Kısa bir süre sonra **ölçümler** sekmesi, sanal olarak bağlı hava koşullayıcı cihazınızdan rastgele oluşturulan olayların bir grafiğini gösterir. Görünürlüğü yönetmek veya olay tanımını düzenlemek için denetimleri kullanın:

    ![Olay simülasyonunu görüntüleme](./media/tutorial-define-device-type/eventview.png)

1. Olayla ilgili ek ayrıntıları görmek için grafikteki olayı seçin:

    ![Olay Ayrıntılarını Görüntüleme](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Durum ölçümü tanımlama

Durumu kullanarak cihazın veya bileşenin durumunu bir süre içinde tanımlayabilir ve görselleştirebilirsiniz. Azure IoT Central, gerçek bir cihaza bağlanmadan önce uygulamanızın davranışını sınamanızı sağlamak üzere cihaz durumunun benzetimini yapabilir. Cihaz türünüzün durum ölçümlerini **Ölçümler** görünümünde tanımlarsınız.

1. **Fan modu** durum ölçümü eklemek Için **+ yeni ölçüm**' i seçin. Ardından ölçüm türü olarak **Durum** seçin:

    ![Bağlı klima durum ölçümleri](./media/tutorial-define-device-type/statenew.png)

2. Bir cihaz şablonu için tanımladığınız her durum türü, aşağıdakiler gibi [yapılandırma seçeneklerini](howto-set-up-template.md) içerir:

   * Görünen Ad

   * Alan Adı.

   * İsteğe bağlı görünen etiketlerin değerleri.

   * Her değerin rengi.

     **Fan Modu** durumunuzu yapılandırmak için, aşağıdaki tabloda yer alan bilgileri kullanın:

     | Ayar              | Değer             |
     | -------------------- | -----------       |
     | Görünen Ad         | Fan Modu          |
     | Alan Adı           | fanmode           |
     | Değer                | 1                 |
     | Görünen etiket        | İşletim         |
     | Değer                | 0                 |
     | Görünen etiket        | Durduruldu           |

     Durum ölçümü tanımını kaydetmek için **Kaydet**' i seçin:

     ![Durum ölçümünü yapılandırma](./media/tutorial-define-device-type/stateconfiguration.png)

3. Daha kısa bir süre sonra **ölçüler** sekmesi, sanal olarak bağlı hava koşullayıcı cihazınızdan rastgele oluşturulan durumların bir grafiğini gösterir. Görünürlüğü yönetmek veya durum tanımını düzenlemek için denetimleri kullanın:

    ![Durum simülasyonunu görüntüleme](./media/tutorial-define-device-type/stateview.png)

4. Cihaz tarafından küçük bir süre içinde gönderilen çok fazla veri noktası varsa, durum ölçümü farklı bir görselle gösterilir. Bu süre içindeki tüm veri noktalarını kronolojik sırada görüntülemek için grafiği seçin. Ayrıca zaman aralığını daraltarak ölçümü daha ayrıntılı bir şekilde de görebilirsiniz.

## <a name="settings-properties-and-commands"></a>Ayarlar, özellikler ve komutlar

Ayarlar, özellikler ve komutlar, cihaz şablonunda tanımlanan ve tek tek her cihazla ilişkilendirilen farklı değerlerdir:

* Uygulamanızdan cihaza yapılandırma verilerini göndermek için _ayarları_ kullanırsınız. Örneğin, operatör bir ayar kullanarak cihazın iki saniye olan telemetri aralığını beş saniye olarak değiştirebilir. Bir işleci bir ayarı değiştirdiğinde, cihaz bir bildirim ile yanıt verinceye kadar bu ayar Kullanıcı arabiriminde bekliyor olarak işaretlenir.

* Cihazınızla ilişkilendirilmiş meta verileri tanımlamak için _özellikleri_ kullanırsınız. İki özellik kategorisi vardır:
    
  * Uygulamanızda cihazınız hakkındaki bilgileri kaydetmek için _uygulama özelliklerini_ kullanırsınız. Örneğin uygulama özelliklerini kullanarak bir cihazın konumunu ve son servis tarihini kaydedebilirsiniz. Bu özellikler uygulamada depolanır ve cihazla eşitlenmez. Bir operatör özelliklere değerler atayabilir.

  * Cihazın uygulamanıza özellik değerlerini göndermesini sağlamak için _cihaz özelliklerini_ kullanırsınız. Bu özellikler yalnızca cihaz tarafından değiştirilebilir. Operatör açısından cihaz özellikleri salt okunurdur. Bu bağlantılı klima senaryosunda üretici yazılımı sürümü ve cihaz seri numarası, cihaz tarafından bildirilen cihaz özellikleridir.
    
    Daha fazla bilgi için, bkz. bir cihaz şablonu ayarlama hakkında nasıl yapılır kılavuzundaki [Özellikler](howto-set-up-template.md#properties) .

* Uygulamanız üzerinden cihazınızı uzaktan yönetmek için _komutlar_ kullanırsınız. Cihazlarınızı denetlemek için komutları doğrudan buluttan çalıştırabilirsiniz. Örneğin bir operatör, cihazı yeniden başlatmak için yeniden başlat gibi bir komut çalıştırabilir.

## <a name="use-settings"></a>Ayarları kullanma

Operatörün cihaza yapılandırma verilerini gönderebilmesini sağlamak için *ayarları* kullanırsınız. Bu bölümde, **Bağlı Klima** cihaz şablonunuza operatörün bağlı klimanın hedef sıcaklığını ayarlamasına olanak tanıyan bir ayar ekleyeceksiniz.

1. **Bağlı hava koşullayıcı** cihaz şablonunuz için **Ayarlar** sekmesine gidin.

2. Sayılar veya metinler gibi farklı türlerin ayarlarını oluşturabilirsiniz. Cihazınıza sayı ayarı eklemek için **sayı** ' yı seçin.

3. **Sıcaklık Ayarlama** ayarınızı yapılandırmak için aşağıdaki tabloda yer alan bilgileri kullanın:

    | Alan                | Değer           |
    | -------------------- | -----------     |
    | Görünen Ad         | Sıcaklığı Ayarla |
    | Alan Adı           | setTemperature  |
    | Ölçü Birimi      | F               |
    | Ondalık Basamak Sayısı       | 1               |
    | En Küçük Değer        | 20              |
    | En Büyük Değer        | 200             |
    | İlk Değer        | 80              |
    | Açıklama          | Klimanın hedef sıcaklığını ayarlayın |

    Sonra **Kaydet**' i seçin:

    ![Sıcaklığı Ayarla ayarını yapılandırma](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Cihaz bir ayar değişikliğini kabul ettikten sonra, ayarın durumu **eşitlendi** olarak değişir.

4. Ayarlar kutucuklarını taşıyarak ve yeniden boyutlandırarak **ayarlar sekmesinin yerleşimini** özelleştirebilirsiniz:

    ![Ayarlar düzenini özelleştirme](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Özellikleri kullanma

*Uygulama özelliklerini*, cihazınız hakkındaki bilgileri uygulamada depolamak için kullanırsınız. Bu bölümde, cihazın konumu ve son hizmet tarihini depolamak için **Bağlı Klima** cihaz şablonunuza uygulama özellikleri eklersiniz. Bu özellikler uygulamada düzenlenebilir. Cihaz ayrıca, uygulamada salt okunan seri numarası ve bellenim sürümü gibi özellikleri de raporlar.

1. **Bağlı hava koşullayıcı** cihaz şablonunuz için **Özellikler** sekmesine gidin.

1. Sayılar veya metinler gibi farklı türlerde cihaz özellikleri oluşturabilirsiniz. Cihaz şablonunuza konum özelliği eklemek için **Konum**'u seçin. Konum özelliğinizi yapılandırmak için aşağıdaki tabloda yer alan bilgileri kullanın:

    | Alan                | Değer                |
    | -------------------- | -------------------- |
    | Görünen Ad         | Konum             |
    | Alan Adı           | location             |
    | İlk Değer        | Seattle, WA          |
    | Açıklama          | Cihaz konumu      |

    Diğer alanları varsayılan değerlerinde bırakın.

    ![Cihaz özelliklerini yapılandırma](./media/tutorial-define-device-type/configureproperties.png)

    **Kaydet**’i seçin.

1. Cihaz şablonunuza son hizmet tarihi özelliği eklemek için **Tarih**'i seçin.

1. Son hizmet tarihi özelliğinizi yapılandırmak için aşağıdaki tabloda yer alan bilgileri kullanın:

    | Alan                | Değer                   |
    | -------------------- | ----------------------- |
    | Görünen Ad         | Son Hizmet Tarihi       |
    | Alan Adı           | serviceDate             |
    | İlk Değer        | 1/1/2019                |
    | Açıklama          | Son hizmet tarihi           |

    ![Cihaz özelliklerini yapılandırma](./media/tutorial-define-device-type/configureproperties2.png)

    **Kaydet**’i seçin.

1. Özellik kutucuklarını taşıyarak ve yeniden boyutlandırarak **Özellikler** sekmesinin yerleşimini özelleştirebilirsiniz.

1. Cihaz şablonunuza üretici yazılımı sürümü gibi bir cihaz özelliği eklemek için **Cihaz Özelliği**'ni seçin.

1. Üretici yazılımı sürümünü yapılandırmak için aşağıdaki tabloda yer alan bilgileri kullanın:

    | Alan                | Değer                   |
    | -------------------- | ----------------------- |
    | Görünen Ad         | Üretici yazılımı sürümü        |
    | Alan Adı           | firmwareVersion         |
    | Veri Türü            | metin                    |
    | Açıklama          | Klimanın üretici yazılımı sürümü |

    ![Üretici yazılımı sürümünü yapılandırma](./media/tutorial-define-device-type/configureproperties3.png)

    **Kaydet**’i seçin.

1. Cihaz şablonunuza seri numarası gibi bir cihaz özelliği eklemek için **Cihaz Özelliği**'ni seçin.

1. Seri numarasını yapılandırmak için aşağıdaki tabloda yer alan bilgileri kullanın:

    | Alan                | Değer                   |
    | -------------------- | ----------------------- |
    | Görünen Ad         | Seri numarası           |
    | Alan Adı           | serialNumber            |
    | Veri Türü            | metin                    |
    | Açıklama          | Klimanın seri numarası  |

    ![Seri numarasını yapılandırma](./media/tutorial-define-device-type/configureproperties4.png)

    **Kaydet**’i seçin.

    > [!NOTE]
    > Cihaz Özelliği, cihazdan uygulamaya gönderilir. Üretici yazılımı sürümü ve seri numarası, gerçek cihazınız IoT Central'a bağlandığında güncelleştirilir.

## <a name="use-commands"></a>Komutları kullanma

Operatörün doğrudan cihazda komut çalıştırabilmesi için _komutları_ kullanırsınız. Bu bölümde, **Bağlı Klima** cihaz şablonunuza, operatörün bağlı klimada belirli bir ileti yankısı yapmasını sağlayan bir komut eklersiniz.

1. Şablonu düzenlemek için **bağlantılı hava koşullayıcı** cihaz şablonunuzun **Komutlar** sekmesine gidin.

1. Cihazınıza bir komut eklemek ve yeni komutunuz yapılandırmaya başlamak için **+ yeni komutunu** seçin.

1. Yeni komutunuzu yapılandırmak için, aşağıdaki tabloda yer alan bilgileri kullanın:

    | Alan                | Değer           |
    | -------------------- | -----------     |
    | Görünen Ad         | Yankı Komutu    |
    | Alan Adı           | echo            |
    | Varsayılan Zaman Aşımı      | 30              |
    | Veri Türü         | metin            |
    | Açıklama          | Cihaz Komutu  |  

    **Giriş alanları**için **+** ' i seçerek komuta ek girişler ekleyebilirsiniz.

    ![Ayar eklemeye hazırlanma](./media/tutorial-define-device-type/commandsecho1.png)

     **Kaydet**’i seçin.

1. Komut kutucuklarını taşıyarak ve yeniden boyutlandırarak **Komutlar** sekmesinin yerleşimini özelleştirebilirsiniz.

## <a name="view-your-simulated-device"></a>Simülasyon cihazınızı görüntüleme

Artık **bağlı hava koşullayıcı** cihaz şablonunuzu tanımladınız, kendi **panosunu** , tanımladığınız ölçümleri, ayarları ve özellikleri içerecek şekilde özelleştirebilirsiniz. Ardından bir operatör olarak panonun önizlemesine bakabilirsiniz:

1. **Bağlı hava koşullayıcı** cihaz şablonunuz için **Pano** sekmesini seçin.

1. **Pano**üzerine bileşen eklemek Için **çizgi grafik** ' i seçin.

1. Aşağıdaki tabloda yer alan bilgileri kullanarak **Çizgi Grafik** bileşenini yapılandırın:

    | Ayar      | Değer       |
    | ------------ | ----------- |
    | Başlık        | Sıcaklık |
    | Zaman Aralığı   | Son 30 dakika |
    | Ölçüler     | Sıcaklık ( **sıcaklık**yanındaki **görünürlüğü** seçin) |

    ![Çizgi grafik ayarları](./media/tutorial-define-device-type/linechartsettings.png)

    Daha sonra **Kaydet**’e tıklayın.

1. Aşağıdaki tablodaki bilgileri kullanarak **olay geçmişi** bileşenini seçin:

    | Ayar      | Değer       |
    | ------------ | ----------- |
    | Başlık        | Fan Motoru etkinlikleri |
    | Zaman Aralığı   | Son 30 dakika |
    | Ölçüler     | Fan Motoru hatası ( **Fan Motoru hatasının**yanındaki **görünürlüğü** seçin) |

    ![Olay grafiği ayarları](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Daha sonra **Kaydet**’e tıklayın.

1. Aşağıdaki tabloda yer alan bilgileri kullanarak **Durum Geçmişi** bileşenini yapılandırın:

    | Ayar      | Değer       |
    | ------------ | ----------- |
    | Başlık        | Fan Modu |
    | Zaman Aralığı   | Son 30 dakika |
    | Ölçüler | Fan modu ( **fan modunun**yanındaki **görünürlüğü** seçin) |

    ![Çizgi grafik ayarları](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Daha sonra **Kaydet**’e tıklayın.

1. Panoya cihaz ayarları ve özellikler eklemek için **Ayarlar ve Özellikler**' i seçin. Panoda görmek istediğiniz ayarları veya özellikleri eklemek için **Ekle/Kaldır** ' ı seçin.

1. Aşağıdaki tabloda yer alan bilgileri kullanarak **Ayarlar ve Özellikler** bileşenini yapılandırın:

    | Ayar                 | Değer         |
    | ----------------------- | ------------- |
    | Başlık                   | Cihaz özellikleri |
    | Ayarlar ve Özellikler | Sıcaklığı Ayarla<br/>Seri numarası<br/>Üretici yazılımı sürümü |

    **Ayarlar ve Özellikler** sayfalarında daha önce tanımladığınız ayarlar ve Özellikler **kullanılabilir sütunlarda**gösterilmektedir.

    ![Sıcaklık özelliği ayarlarını belirleme](./media/tutorial-define-device-type/propertysettings4.png)

    Daha sonra **Kaydet**’e tıklayın.

1. Artık panodaki bağlantılı hava koşullarınız için sanal verileri görebilirsiniz. Pano için kutucukları ve düzeni düzenleyebilirsiniz:

    ![Panoyu görüntüleme](./media/tutorial-define-device-type/dashboard.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Yeni cihaz şablonu oluşturma
> * Cihazınıza telemetri verileri ekleme
> * Simülasyon telemetrisini görüntüleme
> * Cihaz olaylarını tanımlama
> * Simülasyon olaylarını görüntüleme
> * Durumunuzu tanımlama
> * Simülasyon durumunu görüntüleme
> * Ayarları ve özellikleri kullanma
> * Komutları kullanma
> * Simülasyon cihazınızı panoda görüntüleme

Azure IoT Central uygulamanızda bir cihaz şablonu tanımladığınıza göre, önerilen sonraki adımlar aşağıda verilmiştir:

* [Cihazınız için kurallar ve eylemler yapılandırma](tutorial-configure-rules.md)
* [Operatörün görünümlerini özelleştirme](tutorial-customize-operator.md)