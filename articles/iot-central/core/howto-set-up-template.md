---
title: Azure IoT Central uygulamasında bir cihaz şablonu ayarlama | Microsoft Docs
description: Ölçümler, ayarlar, özellikler, kurallar ve bir pano ile cihaz şablonu ayarlamayı öğrenin.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c4df07174a5d8826acd7682fa3035fcfd201c5c9
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953102"
---
# <a name="set-up-a-device-template"></a>Cihaz şablonu ayarlama

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bir cihaz şablonu, bir Azure IoT Central uygulamasına bağlanan bir cihaz türünün özelliklerini ve davranışlarını tanımlayan bir şema.

Örneğin, bir Oluşturucu, bağlı bir fan için aşağıdaki özelliklere sahip bir cihaz şablonu oluşturabilir:

- Sıcaklık telemetri ölçümü
- Konum ölçümü
- Fan Motoru hata olayı ölçümü
- Fan çalışma durumu ölçümü
- Fan hızı ayarı
- Uyarı Gönderen kurallar
- Size cihazın genel görünümünü veren Pano

Bu cihaz şablonundan bir operatör, **Fan-1** ve **fan-2**gibi adlarla gerçek fan cihazları oluşturup bağlayabilirler. Tüm bu fanların ölçümleri, ayarları, özellikleri, kuralları ve uygulamanızın kullanıcılarının izleyebileceğini ve yöneteceği bir panoyu vardır.

> [!NOTE]
> Yalnızca oluşturucular ve yöneticiler cihaz şablonları oluşturabilir, düzenleyebilir ve silebilir. Herhangi bir Kullanıcı, mevcut cihaz şablonlarından **Device Explorer** sayfasında cihaz oluşturabilir.

## <a name="create-a-device-template"></a>Cihaz şablonu oluşturma

1. **Cihaz şablonları** sayfasına gidin.

2. Şablon oluşturmak için **+ Yeni**' yi seçerek başlayın.

3. Hızlı bir başlangıç yapmak için varolan önceden oluşturulmuş şablonlar arasından seçim yapın. Aksi takdirde, **özel**' i seçin, bir ad girin ve sıfırdan kendi şablonunuzu oluşturmak için **Oluştur** ' a tıklayın.

   ![Cihaz şablonu kitaplığı](./media/howto-set-up-template/newtemplate.png)

4. Özel bir şablon oluşturduğunuzda, yeni cihaz şablonunuz için **cihaz ayrıntıları** sayfasını görürsünüz. IoT Central, bir cihaz şablonu oluşturduğunuzda otomatik olarak sanal bir cihaz oluşturur. Sanal cihaz, gerçek bir cihazı bağlanmadan önce uygulamanızın davranışını test etmenizi sağlar.

Aşağıdaki bölümlerde, **cihaz şablonu** sayfasındaki sekmelerin her biri açıklanır.

## <a name="measurements"></a>Ölçümler

Ölçümler, cihazınızdan gelen verileri içerir. Cihazınızın yeteneklerini eşleştirmek için cihaz şablonunuza birden çok ölçü ekleyebilirsiniz.

- **Telemetri** ölçümleri, cihazınızın zaman içinde topladığı sayısal veri noktalarıdır. Bunlar sürekli akış olarak temsil edilir. Sıcaklık bir örnektir.
- **Olay** ölçümleri, cihazdaki anlamlı bir şeyi temsil eden zaman içindeki bir veri noktasıdır. Önem düzeyi bir olayın önemini temsil eder. Örnek bir fan motoru hatasıdır.
- **Durum** ölçümleri, cihazın veya bileşenlerinin bir süre içinde durumunu temsil eder. Örneğin, fan modu, iki olası durum olarak **çalışan** ve **durdurulmuş** olarak tanımlanabilir.
- **Konum** ölçümleri, içindeki bir süre içinde cihazın boylam ve Latitude koordinatlarından oluşur. Örneğin, bir fan bir konumdan diğerine taşınabilir.

### <a name="create-a-telemetry-measurement"></a>Telemetri ölçümü oluşturma

Yeni bir telemetri ölçümü eklemek için **+ yeni ölçüm**' i seçin, ölçüm türü olarak **telemetri** ' i seçin ve forma ayrıntıları girin.

> [!NOTE]
> Gerçek bir cihaz bağlıyken, telemetri ölçüsünün uygulamada görüntülenmesi için, cihaz şablonundaki alan adlarının ilgili cihaz kodundaki Özellik adlarıyla eşleşmesi gerekir. Aşağıdaki bölümlerde cihaz şablonunu tanımlamaya devam ederken ayarları, cihaz özelliklerini ve komutları yapılandırırken aynısını yapın.

Örneğin, yeni bir sıcaklık telemetri ölçümü ekleyebilirsiniz:

| Görünen Ad        | Alan Adı    |  Birimler    | Min   |Maks.|
| --------------------| ------------- |-----------|-------|---|
| Sıcaklık         | kopyalar          |  degC     |  0    |100|

![Sıcaklık ölçümü ayrıntıları içeren "Telemetri oluşturma" formu](./media/howto-set-up-template/measurementsform.png)

**Kaydet**' i seçtikten sonra, **sıcaklık** ölçümü ölçüm listesinde görünür. Kısa bir süre içinde, sanal cihazdaki sıcaklık verilerinin görselleştirilmesini görürsünüz.

Telemetriyi görüntülerken, şu toplama seçeneklerinden birini seçebilirsiniz: ortalama, minimum, maksimum, toplam ve sayı. **Ortalama** , grafikte varsayılan toplama olarak seçilidir.

> [!NOTE]
> Telemetri ölçüsünün veri türü bir kayan noktalı sayıdır.

### <a name="create-an-event-measurement"></a>Olay ölçümü oluşturma

Yeni bir olay ölçümü eklemek için **+ yeni ölçüm** ' i seçin ve ölçüm türü olarak **olay** ' ı seçin. **Olay oluştur** formundaki ayrıntıları girin.

Olay için **görünen ad**, **alan adı**ve **önem derecesi** ayrıntılarını girin. Kullanılabilir üç önem düzeyi arasından seçim yapabilirsiniz: **hata**, **Uyarı**ve **bilgi**.

Örneğin, yeni bir **fan motoru hata** olayı ekleyebilirsiniz.

| Görünen Ad        | Alan Adı    |  Varsayılan önem derecesi |
| --------------------| ------------- |-----------|
| Fan Motoru Hatası     | fanotohata |  Hata    |

![Fan Motoru olayının ayrıntılarını içeren "olay oluşturma" formu](./media/howto-set-up-template/eventmeasurementsform.png)

**Kaydet**' i seçtikten sonra, **fan listesinde fan motoru hata** ölçümü görüntülenir. Kısa bir süre içinde, sanal cihazdaki olay verilerinin görselleştirilmesini görürsünüz.

Bir olayla ilgili daha fazla ayrıntı görüntülemek için, grafikteki olay simgesini seçin:

!["Fan Motoru hatası" olayının ayrıntıları](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> Olay ölçüsünün veri türü dizedir.

### <a name="create-a-state-measurement"></a>Durum ölçümü oluşturma

Yeni bir durum ölçümü eklemek için **+ yeni ölçüm** düğmesini seçin ve ölçü türü olarak **durum** ' u seçin. **Durum oluştur** formundaki ayrıntıları girin.

**Görünen ad**, **alan adı**ve durum **değerleri** için ayrıntıları sağlayın. Her bir değer aynı zamanda değer grafiklerde ve tablolarda göründüğünde kullanılacak görünen bir ada sahip olabilir.

Örneğin, cihazın gönderebileceği, **çalışan** ve **durdurduğu**iki olası değere sahip yeni bir **fan modu** durumu ekleyebilirsiniz.

| Görünen Ad | Alan Adı    |  Değer 1   | Görünen Ad | Değer 2    |Görünen Ad  |
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Fan Modu     | fanmode       |  1         | İşletim    |     0      | Durdurulan      |

![Fan modunun ayrıntıları içeren "durumu düzenleme" formu](./media/howto-set-up-template/statemeasurementsform.png)

**Kaydet**' i seçtikten sonra, ölçüm listesinde **fan modu** durum ölçümü görüntülenir. Kısa bir süre içinde, sanal cihazdaki durum verilerinin görselleştirilmesini görürsünüz.

Cihaz kısa bir süre içinde çok fazla veri noktası gönderirse, durum ölçümü farklı bir görselle görünür. Bu zaman dilimi içindeki tüm veri noktalarını kronolojik sırada görüntülemek için grafiği seçin. Ayrıca, grafikte çizilen ölçüyü görmek için zaman aralığını daraltabilirsiniz.

> [!NOTE]
> Durum ölçüsünün veri türü dizedir.

### <a name="create-a-location-measurement"></a>Konum ölçümü oluşturma

Yeni bir konum ölçümü eklemek için **+ yeni ölçüm**' i seçin, ölçü türü olarak **konum** ' u seçin ve **ölçüm oluştur** formundaki ayrıntıları girin.

Örneğin, yeni bir konum telemetri ölçümü ekleyebilirsiniz:

| Görünen Ad        | Alan Adı    |
| --------------------| ------------- |
| Varlık konumu      |  assetloc     |

![Konum ölçümü ayrıntılarının bulunduğu "konum oluşturma" formu](./media/howto-set-up-template/locationmeasurementsform.png)

**Kaydet**' i seçtikten sonra, ölçüm listesinde **konum** ölçümü görüntülenir. Kısa bir süre içinde, sanal cihazdaki konum verilerinin görselleştirilmesini görürsünüz.

Konum görüntülenirken, şu seçeneklerden birini seçebilirsiniz: en son konum ve konum geçmişi. **Konum geçmişi** yalnızca seçili zaman aralığında uygulanır.

Konum ölçüsünün veri türü, Boylam, enlem ve isteğe bağlı bir yükseklik içeren bir nesnedir. Aşağıdaki kod parçacığı JavaScript yapısını gösterir:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

Gerçek cihaz bağlandıktan sonra, ölçüm olarak eklediğiniz konum cihaz tarafından gönderilen değerle güncelleştirilir. Konum ölçülerinizi yapılandırdıktan sonra, [cihaz panosundaki konumu görselleştirmek için bir harita ekleyebilirsiniz](#add-a-location-measurement-in-the-dashboard).

## <a name="settings"></a>Ayarlar

Ayarları bir cihazı denetler. Bunlar, operatörlerin cihaza giriş sağlamasına imkan tanır. Cihaz şablonunuza, işleçlerin kullanması için **Ayarlar** sekmesinde kutucuk olarak görünen birden çok ayar ekleyebilirsiniz. Birçok tür ayar ekleyebilirsiniz: sayı, metin, tarih, iki durumlu ve bölüm etiketi.

Ayarlar üç durumdan birinde olabilir. Cihaz bu durumları raporlar.

- **Eşitlenmiş**: cihaz, ayar değerini yansıtacak şekilde değiştirilmiştir.

- **Bekliyor**: cihaz şu anda ayar değerine değiştiriyor.

- **Hata**: cihaz bir hata döndürdü.

Örneğin, **Ayarlar** ' ı seçip yeni **sayı** ayarını girerek yeni bir fan hızı ayarı ekleyebilirsiniz:

| Görünen Ad  | Alan Adı    |  Birimler  | ın |Başlatma|
| --------------| ------------- |---------| ---------|---- |
| Fan hızı     | Fanın hızı      | RPM     | 2        | 0   |

![Hız ayarları ayrıntılarını içeren "sayı yapılandırma" formu](./media/howto-set-up-template/settingsform.png)

**Kaydet**' i seçtikten sonra **fan hızı** ayarı bir kutucuk olarak görünür. Operatör, cihazın fan hızını değiştirmek için **Device Explorer** sayfasındaki ayarı kullanabilir.

## <a name="properties"></a>Özellikler

Özellikler, cihazla ilişkili, sabit bir cihaz konumu ve seri numarası gibi meta verilerlerdir. Cihaz şablonunuza, **Özellikler** sekmesinde kutucuk olarak görünen birden çok özellik ekleyin. Bir özelliğin sayı, metin, tarih, geçiş, cihaz özelliği, etiket veya sabit bir konum gibi bir türü vardır. Bir işleç, bir cihaz oluşturduklarında özellikler için değerleri belirtir ve bu değerleri istediğiniz zaman düzenleyebilirler. Cihaz özellikleri salt okunurdur ve cihazdan uygulamaya gönderilir. Bir operatör cihaz özelliklerini değiştiremiyor. Gerçek bir cihaz bağlandığı zaman, cihazdaki Device özelliği güncellenir.

İki özellik kategorisi vardır:

- Cihazın IoT Central uygulamasına rapor veren _cihaz özellikleri_ . Cihaz özellikleri, cihaz tarafından raporlanan salt yazılır değerlerdir ve gerçek bir cihaz bağlandığında uygulamada güncelleştirilir.
- Uygulamada depolanan ve işleç tarafından düzenlenebilecek _uygulama özellikleri_ . Uygulama özellikleri yalnızca uygulamada depolanır ve bir cihaz tarafından hiç görülmez.

Örneğin, **Özellikler** sekmesinde cihaz için son hizmet verilen tarihi yeni bir **Tarih** özelliği (bir uygulama özelliği) olarak ekleyebilirsiniz:

| Görünen Ad  | Alan Adı | İlk Değer   |
| --------------| -----------|-----------------|
| Son hizmet tarihi      | Lastservise alınmış        | 01/29/2019     |

!["Özellikler" sekmesinde "en son hizmet verilen hizmeti yapılandırma" formu](./media/howto-set-up-template/propertiesform.png)

**Kaydet**' i seçtikten sonra, cihaz için son hizmet verilen tarih bir kutucuk olarak görüntülenir.

Kutucuğu oluşturduktan sonra **Device Explorer**uygulama özelliği değerini değiştirebilirsiniz.

### <a name="create-a-location-property"></a>Konum özelliği oluşturma

Azure IoT Central konum verilerinize coğrafi bağlam verebilir ve tüm Enlem ve boylam koordinatlarını veya sokak adresini eşleyebilirsiniz. Azure haritalar IoT Central bu özelliği sunar.

İki tür konum özelliği ekleyebilirsiniz:

- Uygulamada depolanan **uygulama özelliği olarak konum**. Uygulama özellikleri yalnızca uygulamada depolanır ve bir cihaz tarafından hiç görülmez.
- Cihazın uygulamayı bildirdiği **cihaz özelliği olarak konum**. Bu özellik türü, en iyi bir statik konum için kullanılır.

> [!NOTE]
> Özellik olarak konum bir geçmişi kaydetmez. Geçmiş istenirse, bir konum ölçümü kullanın.

#### <a name="add-location-as-an-application-property"></a>Uygulama özelliği olarak konum ekleme

IoT Central uygulamanızda Azure haritalar 'ı kullanarak bir uygulama özelliği olarak bir konum özelliği oluşturabilirsiniz. Örneğin, cihaz yükleme adresini ekleyebilirsiniz:

1. **Özellikler** sekmesine gidin.

2. Kitaplıkta **konum**' u seçin.

3. Konum için **görünen adı**, **alan adını**ve (Isteğe bağlı olarak) **başlangıç değerini** yapılandırın.

    | Görünen Ad  | Alan Adı | İlk Değer |
    | --------------| -----------|---------|
    | Yükleme adresi | ınstalladdress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Konum ayrıntıları içeren "konumu Yapılandır" formu](./media/howto-set-up-template/locationcloudproperty2.png)

   Bir konum eklemek için desteklenen iki biçim vardır:
   - **Adres olarak konum**
   - **Koordinat olarak konum**

4. **Kaydet**’i seçin. Bir işleç **Device Explorer**konum değerini güncelleştirebilir.

#### <a name="add-location-as-a-device-property"></a>Bir cihaz özelliği olarak konum ekleme

Cihazın rapor aldığı bir cihaz özelliği olarak bir Location özelliği oluşturabilirsiniz. Örneğin, cihaz konumunu izlemek istiyorsanız:

1. **Özellikler** sekmesine gidin.

2. Kitaplıktan **cihaz özelliği** ' ni seçin.

3. Görünen adı ve alan adını yapılandırın ve veri türü olarak **konum** ' u seçin:

    | Görünen Ad  | Alan Adı | Veri Türü |
    | --------------| -----------|-----------|
    | Cihaz konumu | deviceLocation | location  |

   > [!NOTE]
   > Alan adları, karşılık gelen aygıt kodundaki Özellik adlarıyla eşleşmelidir

   !["Cihaz özelliklerini yapılandırma" formu konum ayrıntıları](./media/howto-set-up-template/locationdeviceproperty2.png)

Gerçek cihaz bağlandıktan sonra, cihaz özelliği olarak eklediğiniz konum cihaz tarafından gönderilen değerle güncelleştirilir. Konum özelliğini yapılandırdıktan sonra, [cihaz panosundaki konumu görselleştirmek için bir harita ekleyebilirsiniz](#add-a-location-property-in-the-dashboard).

## <a name="commands"></a>Komutlar

Komutları, bir cihazı uzaktan yönetmek için kullanılır. Bunlar, operatörlerin cihazda komutları çalıştırmasına olanak tanır. Cihaz şablonunuza, işleçlerin kullanması için **Komutlar** sekmesinde kutucuk olarak görünen birden çok komut ekleyebilirsiniz. Cihazın Oluşturucusu olarak, gereksinimlerinize göre komut tanımlama esnekliği vardır.

Bir komut bir ayardan farklı midir?

- **Ayar**: bir ayar, cihaza uygulamak istediğiniz bir yapılandırmadır. Cihazın bu yapılandırmayı değiştirene kadar kalıcı hale getirmek istiyorsunuz. Örneğin, boş bir cihaz için sıcaklığın sıcaklığını ayarlamak ve bu ayarı, serbest hale getirici yeniden başlatıldığında bile kullanmak istiyorsunuz.

- **Komut**: bir komutu cihazda IoT Central uzaktan çalıştırmak için komutları kullanırsınız. Bir cihaz bağlı değilse, komut zaman aşımına uğrar ve başarısız olur. Örneğin, bir cihazı yeniden başlatmak istiyorsunuz.

Örneğin, **Komutlar** sekmesini seçip **+ Yeni komut**' yi seçerek ve yeni komut ayrıntılarını girerek yeni bir **echo** komutu ekleyebilirsiniz:

| Görünen Ad  | Alan Adı | Varsayılan Zaman Aşımı | Veri Türü |
| --------------| -----------|---------------- | --------- |
| Yankı Komutu  | echo       |  30             | metin      |

![Echo ayrıntılarını içeren "komutu yapılandırma" formu](./media/howto-set-up-template/commandsecho1.png)

**Kaydet**' i seçtikten sonra, **echo** komutu bir kutucuk olarak görünür ve gerçek cihazınız bağlantı kurduğunda **Device Explorer** kullanılmak üzere hazırlanmalıdır. Komutların başarıyla çalışması için komutlarınızın alan adları karşılık gelen aygıt kodundaki Özellik adlarıyla eşleşmelidir.

[Örnek C cihaz kodunun bağlantısı aşağıda verilmiştir.](https://github.com/Azure/iot-central-firmware/blob/ad40358906aeb8f2040a822ba5292df866692c16/MXCHIP/mxchip_advanced/src/AzureIOTClient.cpp#L34)

## <a name="rules"></a>Kurallar

Kurallar, operatörlerin neredeyse gerçek zamanlı olarak izlenmesini sağlar. Kurallar, kural tetiklendiğinde e-posta gönderme gibi eylemleri otomatik olarak çağırır. Bir kural türü bugün kullanılabilir:

- Seçilen cihaz telemetrisi belirtilen eşikten kesişiyorsa tetiklenen **telemetri kuralı**. [Telemetri kuralları hakkında daha fazla bilgi edinin](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Pano

Pano, bir işlecin bir cihaz hakkındaki bilgileri görmek için gittiği yerdir. Bir Oluşturucu olarak, bu sayfaya kutucuk ekleyerek, işleçlerin cihazın nasıl davrandıktan yardımcı olur. Resim, çizgi grafik, çubuk grafik, ana performans göstergesi (KPI), ayarlar ve Özellikler ve etiket gibi birçok Pano kutucuğu türü ekleyebilirsiniz.

Örneğin, **Pano** sekmesini ve kitaplıktan kutucuğu seçerek ayarların ve özelliklerin geçerli değerlerinin bir seçimini göstermek Için bir **Ayarlar ve Özellikler** kutucuğu ekleyebilirsiniz:

![Ayarlar ve özellikler hakkındaki ayrıntılarla "cihaz ayrıntılarını yapılandırma" formu](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Artık bir operatör **Device Explorer**panoyu görüntülediğinde kutucuğu görebilirler.

### <a name="add-a-location-measurement-in-the-dashboard"></a>Panoya bir konum ölçümü ekleme

Bir konum ölçümü yapılandırdıysanız, konumunu cihaz panonuzda bir harita ile görselleştirebilirsiniz. Konum ölçümleri için, konum geçmişini çizdirme seçeneğiniz vardır.

1. **Pano** sekmesine gidin.

1. Cihaz panosunda, kitaplıktan **eşle** ' yi seçin.

1. Haritaya bir başlık verin. Aşağıdaki örnekte, başlık **cihazının geçerli konumu**bulunur. Daha sonra **ölçümler** sekmesinde daha önce yapılandırdığınız konum ölçümünü seçin. Aşağıdaki örnekte, **varlık konum** ölçümü seçilidir:

   !["Eşlemeyi yapılandırma" formu, başlık ve özellikler ile ilgili ayrıntılarla](./media/howto-set-up-template/locationcloudproperty5map.png)

1. **Kaydet**’i seçin. Harita kutucuğu artık seçtiğiniz konumu görüntüler.

Harita kutucuğunu yeniden boyutlandırabilirsiniz. Bir operatör **Device Explorer**panoyu görüntülediğinde, bir konum haritası da dahil olmak üzere yapılandırdığınız tüm Pano kutucukları görünür.

### <a name="add-a-location-property-in-the-dashboard"></a>Panoda bir konum özelliği ekleyin

Bir konum özelliği yapılandırdıysanız, konumu cihaz panonuzda bir harita ile görselleştirebilirsiniz.

1. **Pano** sekmesine gidin.

1. Cihaz panosunda, kitaplıktan **eşle** ' yi seçin.

1. Haritaya bir başlık verin. Aşağıdaki örnekte, başlık **cihazının geçerli konumu**bulunur. Daha sonra **Özellikler** sekmesinde daha önce yapılandırdığınız Location özelliğini seçin. Aşağıdaki örnekte, **Cihaz konumu** ölçümü seçilidir:

   ![Harita formunu başlık ve özelliklerin ayrıntılarıyla yapılandırın](./media/howto-set-up-template/locationcloudproperty6map.png)

1. **Kaydet**’i seçin. Harita kutucuğu artık seçtiğiniz konumu görüntüler.

Harita kutucuğunu yeniden boyutlandırabilirsiniz. Bir operatör **Device Explorer**panoyu görüntülediğinde, bir konum haritası da dahil olmak üzere yapılandırdığınız tüm Pano kutucukları görünür.

Azure IoT Central 'de kutucukları kullanma hakkında daha fazla bilgi için bkz. [Pano kutucukları kullanma](howto-use-tiles.md).

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure IoT Central uygulamanızda bir cihaz şablonu ayarlamayı öğrendiğinize göre şunları yapabilirsiniz:

- [Yeni bir cihaz şablonu sürümü oluştur](howto-version-device-template.md)
- [Bir Mxyonga IoT DevKit cihazını Azure IoT Central uygulamanıza bağlama](howto-connect-devkit.md)
- [Genel bir istemci uygulamasını Azure IoT Central uygulamanıza bağlama (node. js)](howto-connect-nodejs.md)
