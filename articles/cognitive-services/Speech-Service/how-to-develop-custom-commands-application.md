---
title: 'Nasıl yapılır: özel komutlar geliştirme uygulamalar-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Özel komutlar uygulamaları geliştirmeyi ve özelleştirmeyi öğrenin. Bu sesli komut uygulamaları, görev tamamlama veya komut ve denetim senaryoları için idealdir.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: ddf36530e52703ab1033b8e2e787b42b6dc60332
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553286"
---
# <a name="develop-custom-commands-applications"></a>Özel komut uygulamaları geliştirme

Bu nasıl yapılır makalesinde, özel komutlar uygulamaları geliştirmeyi ve yapılandırmayı öğreneceksiniz. Özel komutlar özelliği, sesli ilk etkileşim deneyimleri için iyileştirilmiş zengin sesli komut uygulamaları oluşturmanıza yardımcı olur. Özellik, görev tamamlamaya veya komut ve denetim senaryolarına en uygun seçenektir. Nesnelerin İnterneti (IoT) cihazları ve çevresel ve gözetimsiz cihazlar için oldukça uygundur.

Bu makalede, bir TV 'yi açıp kapatabilir, sıcaklığın ayarlanması ve bir alarm ayarlayabilmeniz için bir uygulama oluşturacaksınız. Bu temel komutları oluşturduktan sonra komutları özelleştirmek için aşağıdaki seçenekler hakkında bilgi edineceksiniz:

* Komutlara parametreler ekleme
* Komut parametrelerine yapılandırma ekleme
* Etkileşim kuralları oluşturma
* Konuşma yanıtları için dil oluşturma şablonları oluşturma
* Özel ses araçları kullanma

## <a name="create-an-application-by-using-simple-commands"></a>Basit komutları kullanarak uygulama oluşturma

Boş bir özel komutlar uygulaması oluşturarak başlayın. Ayrıntılar için [hızlı başlangıç](quickstart-custom-commands-application.md)bölümüne bakın. Bu uygulamada, bir projeyi içeri aktarmak yerine boş bir proje oluşturursunuz.

1. **Ad** kutusuna, *Akıllı Oda-Lite* (veya tercih ettiğiniz başka bir ad) proje adını girin.
1. **Dil** listesinde **İngilizce (Birleşik Devletler)** seçeneğini belirleyin.
1. Bir LUO kaynağı seçin veya oluşturun.

   > [!div class="mx-imgBorder"]
   > !["Yeni proje" penceresini gösteren ekran görüntüsü.](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>LUSıS kaynaklarını güncelleştirme (isteğe bağlı)

**Yeni proje** penceresinde seçtiğiniz yazma kaynağını güncelleştirebilirsiniz. Ayrıca, bir tahmin kaynağı da ayarlayabilirsiniz. 

Özel komutlar uygulamanız yayımlandığında, tanıma için bir tahmin kaynağı kullanılır. Geliştirme ve test aşamaları sırasında bir tahmin kaynağına ihtiyacınız yoktur.

### <a name="add-a-turnon-command"></a>TurnOn komutu ekleme

Oluşturduğunuz boş akıllı oda-Lite özel komutları uygulamasında bir komut ekleyin. Bu komut, bir utterance işlemini işleyecek `Turn on the tv` . İleti ile yanıt verir `Ok, turning the tv on` .

1. Sol bölmenin en üstünde bulunan **Yeni komut** ' i seçerek yeni bir komut oluşturun. **Yeni komut** penceresi açılır.
1. **Ad** alanı için değeri sağlayın `TurnOn` .
1. **Oluştur**’u seçin.

Orta bölmede komutun özellikleri listelenir. 

Aşağıdaki tabloda komutun yapılandırma özellikleri açıklanmaktadır. Daha fazla bilgi için bkz. [özel komutlar kavramları ve tanımları](./custom-commands-references.md).

| Yapılandırma            | Açıklama                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Örnek cümleler | Kullanıcının bu komutu tetikleyebilmesi için söyledikleri örnek.                                                                 |
| Parametreler       | Komutu tamamlaması için gereken bilgiler.                                                                                |
| Tamamlama kuralları | Komutu yerine getirmek için gerçekleştirilecek eylemler. Örnekler: kullanıcıya yanıt verme veya bir Web hizmetiyle iletişim kurma. |
| Etkileşim kuralları   | Daha belirgin veya karmaşık durumları işlemek için diğer kurallar.                                                              |


> [!div class="mx-imgBorder"]
> ![Komutun nerede oluşturulacağını gösteren ekran görüntüsü.](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Örnek cümleler ekleyin

**Örnek cümleler** bölümünde, kullanıcının söylediklerinize bir örnek sağlarsınız.

1. Orta bölmede **örnek tümceler**' ı seçin.
1. Sağdaki bölmede örnekleri ekleyin:

    ```
    Turn on the tv
    ```

1.  Bölmenin en üstünde **Kaydet**' i seçin.

Henüz parametre yok, bu nedenle **tamamlama kuralları** bölümüne geçebilirsiniz.

#### <a name="add-a-completion-rule"></a>Tamamlama kuralı ekleme

Ardından, komut bir tamamlama kuralına ihtiyaç duyuyor. Bu kural kullanıcıya bir karşılama eyleminin alındığını söyler. 

Kurallar ve tamamlama kuralları hakkında daha fazla bilgi için bkz. [özel komutlar kavramlar ve tanımlar](./custom-commands-references.md).

1. **Gerçekleştirilen** varsayılan tamamlama kuralını seçin. Ardından aşağıdaki gibi düzenleyin:

    
    | Ayar    | Önerilen değer                          | Açıklama                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Ad**       | `ConfirmationResponse`                  | Kuralın amacını açıklayan bir ad          |
    | **Koşullar** | Yok                                     | Kuralın ne zaman çalıştırılabilmesine belirleme koşulları    |
    | **Eylemler**    | **Konuşma yanıtı gönder**  >  **Basit düzenleyici**  >  **İlk değişim** > `Ok, turning the tv on` | Kural koşulu true olduğunda gerçekleştirilecek eylem |

   > [!div class="mx-imgBorder"]
   > ![Konuşma yanıtının nerede oluşturulacağını gösteren ekran görüntüsü.](media/custom-commands/create-speech-response-action.png)

1. Eylemi kaydetmek için **Kaydet** ' i seçin.
1. **Tamamlama kuralları** bölümüne geri döndüğünüzde tüm değişiklikleri kaydetmek için **Kaydet** ' i seçin. 

    > [!NOTE]
    > Komutuyla birlikte gelen varsayılan tamamlama kuralını kullanmanız gerekmez. Varsayılan tamamlama kuralını silebilir ve kendi kuralınızı ekleyebilirsiniz.

### <a name="add-a-settemperature-command"></a>Setsıcaklık komutu ekleme

Şimdi bir komut ekleyin `SetTemperature` . Bu komut, tek bir zaman alır `Set the temperature to 40 degrees` ve iletiyle yanıt verir `Ok, setting temperature to 40 degrees` .

Yeni komutu oluşturmak için, komutu için kullandığınız adımları izleyin `TurnOn` , ancak örnek cümlesini kullanın `Set the temperature to 40 degrees` .

Ardından **var olan** tamamlama kurallarını aşağıdaki gibi düzenleyin:

| Ayar    | Önerilen değer                          |
| ---------- | ---------------------------------------- |
| **Ad**  | `ConfirmationResponse`                  |
| **Koşullar** | Yok                                     |
| **Eylemler**    | **Konuşma yanıtı gönder**  >  **Basit düzenleyici**  >  **İlk değişim** > `Ok, setting temperature to 40 degrees` |

Komutta yapılan tüm değişiklikleri kaydetmek için **Kaydet** ' i seçin.

### <a name="add-a-setalarm-command"></a>SetAlarm komutu ekleme

Yeni bir `SetAlarm` komut oluşturun. Örnek cümlesini kullanın `Set an alarm for 9 am tomorrow` . Ardından **var olan** tamamlama kurallarını aşağıdaki gibi düzenleyin:

| Ayar    | Önerilen değer                          |
| ---------- | ---------------------------------------- |
| **Ad**  | `ConfirmationResponse`                  |
| **Koşullar** | Yok                                     |
| **Eylemler**    | **Konuşma yanıtı gönder**  >  **Basit düzenleyici**  >  **İlk değişim** > `Ok, setting an alarm for 9 am tomorrow` |

Komutta yapılan tüm değişiklikleri kaydetmek için **Kaydet** ' i seçin.

### <a name="try-it-out"></a>Deneyin

Test bölmesini kullanarak uygulamanın davranışını test edin: 

1. Bölmenin sağ üst köşesinde **eğitme** simgesini seçin. 
1. Eğitim bittiğinde **Test**' i seçin. 

Ses veya metin kullanarak aşağıdaki söylenişi örneklerini deneyin:

- Şunu yazın: *sıcaklığını 40 derece olarak ayarlayın*
- Beklenen yanıt: Tamam, sıcaklık 40 derece olarak ayarlanıyor
- Şunu yazın: *TV 'yi açın*
- Beklenen yanıt: Tamam, TV 'yi açma
- Şunu yazın: *9 yarın için bir alarm ayarlayın*
- Beklenen yanıt: Tamam, 9 yarın için bir alarm ayarlanıyor

> [!div class="mx-imgBorder"]
> ![Bir web sohbet arabirimindeki testi gösteren ekran görüntüsü.](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> Test bölmesinde, bu ses girişinin veya metin girişinin nasıl işlendiği hakkında bilgi için **ayrıntıları aç** ' ı seçebilirsiniz.

## <a name="add-parameters-to-commands"></a>Komutlara parametre ekleme

Bu bölümde, komutlarınıza nasıl parametre ekleneceğini öğrenirsiniz. Komutların bir görevi tamamlaması için parametrelerin olması gerekir. Karmaşık senaryolarda parametreler, özel eylemleri tetikleyen koşulları tanımlamak için kullanılabilir.

### <a name="configure-parameters-for-a-turnon-command"></a>TurnOn komutu için parametreleri yapılandırma

`TurnOn`Birden çok cihazı açmak ve kapatmak için mevcut komutu düzenleyerek başlayın.

1. Artık komut hem açık hem de kapalı senaryolarla işlem işleyisimize göre, komutu *Turnonoff* olarak yeniden adlandırın.
   1. Soldaki bölmede **turnOn** komutunu seçin. Sonra bölmenin üst kısmındaki **Yeni komut** ' nin yanındaki üç nokta (**...**) düğmesini seçin.
   
   1. **Yeniden Adlandır**' ı seçin. **Yeniden Adlandır komut** penceresinde, adı *turnonoff* olarak değiştirin.

1. Komutuna yeni bir parametre ekleyin. Parametresi, kullanıcının cihazı açmak mı yoksa kapatmak mı istediğini temsil eder.
   1. Orta bölmenin en üstünde  **Ekle**' yi seçin. Açılan menüden **parametre**' ı seçin.
   1. Sağdaki bölmede, **Parametreler** bölümünde, **ad** kutusuna ekleyin `OnOff` .
   1. **Gerekli**' yi seçin. **Gerekli bir parametre için yanıt Ekle** penceresinde **basit düzenleyici**' yi seçin. **İlk varyasyon** alanında, ekleme *veya kapatma yapılsın mı?*.
   1. **Güncelleştir**’i seçin.

       > [!div class="mx-imgBorder"]
       > ![' Basit Düzenleyici ' sekmesi seçiliyken ' gerekli parametre için yanıtı Ekle ' bölümünü gösteren ekran görüntüsü.](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Aşağıdaki tabloyu kullanarak parametrenin özelliklerini yapılandırın. Bir komutun tüm yapılandırma özellikleri hakkında daha fazla bilgi için bkz. [özel komutlar kavramlar ve tanımlar](./custom-commands-references.md).
      

       | Yapılandırma      | Önerilen değer     | Açıklama                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Ad**               | `OnOff`           | Parametre için açıklayıcı bir ad                                                                           |
       | **Geneldir**          | Değilken       | Bu parametre için bir değerin uygulamadaki tüm komutlara Global olarak uygulanıp uygulanmadığını belirten onay kutusu.|
       | **Gerekli**           | Seçili         | Komut tamamlanmadan önce Bu parametre için bir değer gerekip gerekmediğini belirten onay kutusu. |
       | **Gerekli parametre için yanıt**      |**Basit düzenleyici** > `On or Off?`      | Bilinmiyorsa bu parametrenin değerini soran bir istem. |
       | **Tür**               | **Dize**          | Sayı, dize, tarih saat veya Coğrafya gibi parametre türü.   |
       | **Yapılandırma**      | **Önceden tanımlanmış giriş değerlerini bir iç katalogdan kabul etme** | Dizeler için bu ayar girişleri olası bir değerler kümesiyle sınırlandırır. |
       | **Önceden tanımlanmış giriş değerleri**     | `on`, `off`           | Olası değerler ve bunların diğer adları kümesi.         |
       
        
   1. Önceden tanımlanmış giriş değerleri eklemek için **önceden tanımlanmış bir girdi Ekle**' yi seçin. **Yeni öğe** penceresinde, yukarıdaki tabloda gösterildiği gibi *ad* yazın. Bu durumda, diğer adları kullanmadığınız için bu alanı boş bırakabilirsiniz.
   
      > [!div class="mx-imgBorder"]
      > ![Bir parametre oluşturmayı gösteren ekran görüntüsü.](media/custom-commands/create-on-off-parameter.png)

   1. Parametrenin tüm yapılandırmasını kaydetmek için **Kaydet** ' i seçin.
 
#### <a name="add-a-subjectdevice-parameter"></a>SubjectDevice parametresi ekleme

1. Bu komut kullanılarak denetlenebileceği cihazların adını temsil eden ikinci bir parametre eklemek için **Ekle**' yi seçin. Aşağıdaki yapılandırmayı kullanın.


    | Ayar            | Önerilen değer       |
    | ------------------ | --------------------- |
    | **Ad**               | `SubjectDevice`         |
    | **Geneldir**          | Değilken             |
    | **Gerekli**           | Seçili               |
    | **Gerekli parametre için yanıt**     | **Basit düzenleyici** > `Which device do you want to control?`    | 
    | **Tür**               | **Dize**                |     
    | **Yapılandırma**      | **Önceden tanımlanmış giriş değerlerini bir iç katalogdan kabul etme** | 
    | **Önceden tanımlanmış giriş değerleri** | `tv`, `fan`               |
    | **Diğer adlar** ( `tv` )      | `television`, `telly`     |

1. **Kaydet**’i seçin.

#### <a name="modify-example-sentences"></a>Örnek cümleleri değiştirme

Parametreleri kullanan komutlar için, olası tüm birleşimleri kapsayan örnek cümleler eklemek yararlıdır. Örnek:

* Parametre bilgilerini doldurun: `turn {OnOff} the {SubjectDevice}`
* Kısmi parametre bilgileri: `turn it {OnOff}`
* Parametre bilgisi yok: `turn something`

Farklı bilgi dereceleri kullanan örnek cümleler, özel komutlar uygulamasının kısmi bilgiler kullanarak hem tek kararlı çözümler hem de birden çok çift çözünürlüklerini çözümlemesine izin verir.

Bu bilgiler göz önünde bulundurularak, önerilen parametreleri kullanmak için örnek cümleleri düzenleyin:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

**Kaydet**’i seçin.

> [!TIP]
> Örnek cümleler düzenleyicisinde, parametrelerinizi belirtmek için küme ayraçları kullanın. Örneğin, `turn {OnOff} the {SubjectDevice}`.
> Daha önce oluşturulmuş parametreler tarafından desteklenen otomatik tamamlama için bir sekme kullanın.

#### <a name="modify-completion-rules-to-include-parameters"></a>Tamamlama kurallarını parametreleri içerecek şekilde değiştirme

Mevcut tamamlama kuralını değiştirin `ConfirmationResponse` .

1. **Koşullar** bölümünde **Koşul Ekle**' yi seçin.
1. **Yeni koşul** penceresinde, **tür** listesinde, **gerekli parametreler**' i seçin. Aşağıdaki listede hem **OnOff** hem de **subjectdevice**' ı seçin.
1. **Iglobali** seçilmemiş olarak bırakın.
1. **Oluştur**’u seçin.
1. **Eylemler** bölümünde, **konuşma yanıtı gönder** eylemini, üzerine gelerek ve Düzenle düğmesini seçerek düzenleyin. Bu kez, yeni oluşturulan `OnOff` ve parametreleri kullanın `SubjectDevice` :

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. **Kaydet**’i seçin.

Sağdaki bölmenin üst kısmındaki **eğitme** simgesini seçerek değişiklikleri deneyin. 

Eğitim bittiğinde **Test**' i seçin. Uygulama pencerenizin bir **testi** görüntülenir. Aşağıdaki etkileşimleri deneyin:

- Giriş: *TV* 'yi kapatma
- Çıkış: Tamam, TV 'yi kapatma
- Giriş: *televizyon* kapatma
- Çıkış: Tamam, TV 'yi kapatma
- Giriş: *Kapat*
- Çıkış: hangi cihazı denetlemek istiyorsunuz?
- Giriş: *TV*
- Çıkış: Tamam, TV 'yi kapatma

### <a name="configure-parameters-for-a-settemperature-command"></a>Setsıcaklık komutunun parametrelerini yapılandırma

Kullanıcıyı, `SetTemperature` Kullanıcı yönlendirdiği gibi sıcaklığını ayarlamaya olanak tanımak için komutu değiştirin.

Bir `Temperature` parametre ekleyin. Aşağıdaki yapılandırmayı kullanın:

| Yapılandırma      | Önerilen değer     |
| ------------------ | ----------------|
| **Ad**               | `Temperature`           |
| **Gerekli**           | Seçili         |
| **Gerekli parametre için yanıt**      | **Basit düzenleyici** > `What temperature would you like?`
| **Tür**               | `Number`          |


Aşağıdaki değerleri kullanmak için örnek söyliği düzenleyin.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Mevcut tamamlama kurallarını düzenleyin. Aşağıdaki yapılandırmayı kullanın.

| Yapılandırma      | Önerilen değer     |
| ------------------ | ----------------|
| **Koşullar**         | **Gerekli parametre**  >  **Sıcaklık**           |
| **Eylemler**           | **Konuşma yanıtı gönder** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>SetAlarm komutu için parametreleri yapılandırma

Adlı bir parametre ekleyin `DateTime` . Aşağıdaki yapılandırmayı kullanın.

   | Ayar                           | Önerilen değer                     | 
   | --------------------------------- | ----------------------------------------|
   | **Ad**                              | `DateTime`                               |
   | **Gerekli**                          | Seçili                                 |
   | **Gerekli parametre için yanıt**   | **Basit düzenleyici** > `For what time?`            | 
   | **Tür**                              | **Tarih Saat**                                |
   | **Tarih Varsayılanları**                     | Tarih eksikse, bugün kullanın.            |
   | **Zaman Varsayılanları**                     | Süre eksikse Günün başlangıcını kullanın.     |


> [!NOTE]
> Bu makale çoğunlukla String, Number ve DateTime parametre türlerini kullanır. Desteklenen tüm parametre türlerinin ve bunların özelliklerinin bir listesi için bkz. [özel komutlar kavramlar ve tanımlar](./custom-commands-references.md).


Örnek utbotları düzenleyin. Aşağıdaki değerleri kullanın.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Mevcut tamamlama kurallarını düzenleyin. Aşağıdaki yapılandırmayı kullanın.

   | Ayar    | Önerilen değer                               |
   | ---------- | ------------------------------------------------------- |
   | **Eylemler**    | **Konuşma yanıtı gönder** > `Ok, alarm set for {DateTime}`  |

Farklı komutlarla ilgili söyleylerini kullanarak üç komutu birlikte test edin. (Farklı komutlar arasında geçiş yapabilirsiniz.)

- Giriş: *bir alarm ayarlama*
- Çıkış: hangi süre?
- Giriş: *TV 'Yi açın*
- Çıkış: Tamam, TV 'yi açma
- Giriş: *bir alarm ayarlama*
- Çıkış: hangi süre?
- Giriş: *5 pm*
- Çıkış: Tamam, 2020-05-01 17:00:00 için alarm kümesi

## <a name="add-configurations-to-command-parameters"></a>Komut parametrelerine yapılandırma ekleme

Bu bölümde, aşağıdakiler dahil olmak üzere gelişmiş parametre yapılandırması hakkında daha fazla bilgi edinebilirsiniz:

 - Parametre değerleri, özel komutlar uygulamasının dışında tanımlanan bir kümesine ait olabilir.
 - Parametre değerlerine doğrulama yan tümceleri ekleme.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Bir parametreyi dış Katalog varlığı olarak yapılandırma

Özel komutlar özelliği, bir Web uç noktası üzerinden barındırılan dış kataloglara başvuracak dize türü parametrelerini yapılandırmanıza olanak tanır. Bu nedenle, dış kataloğu özel komutlar uygulamasını düzenlemeden bağımsız olarak güncelleştirebilirsiniz. Bu yaklaşım, Katalog girişlerinin çok sayıda olduğu durumlarda faydalıdır.

`SubjectDevice`Komuttan parametreyi yeniden kullanın `TurnOnOff` . Bu parametrenin geçerli yapılandırması, **iç katalogdan önceden tanımlanmış girdileri kabul** ediyor. Bu yapılandırma, parametre yapılandırmasındaki bir cihaz statik listesini ifade eder. Bu içeriği bağımsız olarak güncelleştirilebilen bir dış veri kaynağına taşıyın.

İçeriği taşımak için yeni bir Web uç noktası ekleyerek başlayın. Soldaki bölmede, **Web uç noktaları** bölümüne gidin. Buradan yeni bir Web uç noktası ekleyin. Aşağıdaki yapılandırmayı kullanın.

| Ayar | Önerilen değer |
|----|----|
| **Ad** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **Yöntem** | **Al** |


URL için önerilen değer sizin için çalışmazsa, denetlenebilecek cihazların listesinden oluşan bir JSON dosyası döndüren bir Web uç noktası yapılandırın ve barındırın. Web uç noktası aşağıdaki gibi biçimlendirilen bir JSON dosyası döndürmelidir:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Daha sonra **Subjectdevice** parametre ayarları sayfasına gidin. Aşağıdaki özellikleri ayarlayın.

| Ayar | Önerilen değer |
| ----| ---- |
| **Yapılandırma** | **Dış katalogdan önceden tanımlanmış girişleri kabul et** |                               
| **Katalog uç noktası** | `getDevices` |
| **Yöntem** | **Al** |

Sonra **Kaydet**'i seçin.

> [!IMPORTANT]
> Sol taraftaki bölmedeki **Web uç** noktası bölümünde ayarlanmış Web uç noktası yoksa, bir dış katalogdan giriş kabul etmek için bir parametre yapılandırma seçeneği görmezsiniz.

**Eğitme**' i seçerek deneyin. Eğitim bittikten sonra **Test** ' i seçin ve birkaç etkileşimi deneyin.

* Giriş: *Aç*
* Çıkış: hangi cihazı denetlemek istiyorsunuz?
* Giriş: *ışıklar*
* Çıkış: Tamam, ışıkları açma

> [!NOTE]
> Artık Web uç noktasında barındırılan tüm cihazları kontrol edebilirsiniz. Ancak yine de uygulamayı yeni değişiklikleri test etmek için eğitmeniz ve uygulamayı yeniden yayımlamanız gerekir.

### <a name="add-validation-to-parameters"></a>Parametrelere doğrulama ekleme

*Doğrulamalar* , parametre değerindeki kısıtlamaları yapılandırmanıza olanak tanıyan belirli parametre türleri için uygulanan yapılardır. Bunlar, değerler kısıtlamaların içinde değilse düzeltmeler ister. Doğrulama yapısını genişleten parametre türlerinin bir listesi için bkz. [özel komutlar kavramlar ve tanımlar](./custom-commands-references.md).

Komutu kullanarak doğrulamaları test edin `SetTemperature` . Parametresi için bir doğrulama eklemek için aşağıdaki adımları kullanın `Temperature` .

1. Soldaki bölmede **Setsıcaklık** komutunu seçin.
1. Orta bölmede **sıcaklık**' yı seçin.
1. Sağdaki bölmede, **Doğrulama Ekle**' yi seçin.
1. **Yeni doğrulama** penceresinde, aşağıdaki tabloda gösterildiği gibi doğrulamayı yapılandırın. Ardından **Oluştur**’u seçin.


    | Parametre yapılandırması | Önerilen değer | Açıklama |
    | ---- | ---- | ---- |
    | **En düşük değer** | `60` | Sayı parametreleri için bu parametrenin varsaydığı en küçük değer |
    | **En büyük değer** | `80` | Sayı parametreleri için bu parametrenin varsaydığı en büyük değer |
    | **Hata yanıtı** |  **Basit düzenleyici**  >  **İlk değişim** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Doğrulama başarısız olursa yeni bir değer istemek için bir istem |

    > [!div class="mx-imgBorder"]
    > ![Aralık doğrulamasının nasıl ekleneceğini gösteren ekran görüntüsü.](media/custom-commands/add-validations-temperature.png)

Sağdaki bölmenin üst kısmındaki **eğitme** simgesini seçerek deneyin. Eğitim bittikten sonra **Test**' i seçin. Birkaç etkileşimi deneyin:

- Giriş: *sıcaklığı 72 derece olarak ayarlayın*
- Çıkış: Tamam, sıcaklık 72 derece olarak ayarlanıyor
- Giriş: *sıcaklığı 45 derece olarak ayarlayın*
- Çıkış: Üzgünüz, yalnızca 60 ve 80 derece arasında sıcaklık ayarlayabiliyorum
- Giriş: *bunun yerine 72 derece yapın*
- Çıkış: Tamam, sıcaklık 72 derece olarak ayarlanıyor

## <a name="add-interaction-rules"></a>Etkileşim kuralları ekleme

Etkileşim kuralları, belirli veya karmaşık durumları işleyen *ek* kurallardır. Kendi etkileşim kurallarınızı yazmak için de ücretsiz olursunuz, bu örnekte aşağıdaki senaryolar için etkileşim kurallarını kullanırsınız:

* Onaylama komutları
* Komutlara tek adımlı bir düzeltme ekleme

Etkileşim kuralları hakkında daha fazla bilgi için bkz. [özel komutlar kavramlar ve tanımlar](./custom-commands-references.md).

### <a name="add-confirmations-to-a-command"></a>Komuta onaylar ekleme

Bir onay eklemek için `SetTemperature` komutunu kullanın. Onay elde etmek için aşağıdaki adımları kullanarak etkileşim kuralları oluşturun:

1. Soldaki bölmede **Setsıcaklık** komutunu seçin.
1. Orta bölmede, **Ekle**' yi seçerek etkileşim kuralları ekleyin. Ardından **etkileşim kuralları**  >  **Onayla komutunu** seçin.

    Bu eylem üç etkileşim kuralı ekler. Kurallar kullanıcıdan alarm tarihini ve saatini onaylamasını ister. Bir sonraki sırayla onay (Evet veya Hayır) bekler.

    1. Aşağıdaki yapılandırmayı kullanarak **komutu Onayla** etkileşim kuralını değiştirin:
        1. **Sıcaklığını onaylamak** için adı değiştirin.
        1. Yeni koşul ekleyin: **gerekli parametre**  >  **sıcaklığı**.
        1. Yeni bir eylem ekleyin:   >  **konuşma yanıtı gönder** yazın  >  **sıcaklığı {sıcaklık} derece olarak ayarlamak istediğinizden emin misiniz?**
        1. **Beklentiler** bölümünde **kullanıcıdan onay bekleyen** varsayılan değerini bırakın.
      
         > [!div class="mx-imgBorder"]
         > ![Gerekli parametre yanıtının nasıl oluşturulacağını gösteren ekran görüntüsü.](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. **Onaylama** başarılı etkileşim kuralını, başarılı bir onaylamayı işleyecek şekilde değiştirin (Kullanıcı Evet diyor ki).
      
          1. Adı **onay sıcaklığı başarılı** olarak değiştirin.
          1. Mevcut **onay başarılı** olarak bırakın.
          1. Yeni koşul ekleyin:   >  **gerekli parametre**  >  **sıcaklığını** yazın.
          1. Varsayılan **yürütme sonrası durum** değerini yürütme **kuralları Yürüt** olarak bırakın.

    1. **Onay reddedildi etkileşim kuralını** , onay reddedildiğinde senaryoları işleyecek şekilde değiştirin (Kullanıcı Hayır olarak kabul edilir).

          1. Adı **onay sıcaklığı reddedildi** olarak değiştirin.
          1. Mevcut **onay reddedildi** durumuna ayrılın.
          1. Yeni koşul ekleyin:   >  **gerekli parametre**  >  **sıcaklığını** yazın.
          1. Yeni bir eylem ekleyin: **tür**  >  **konuşma yanıtı gönderme**  >  **sorunu yok. Ne kadar sıcaklık?**.
          1. Varsayılan **yürütme sonrası durumu** değerini **Kullanıcı girişini bekleyecek** şekilde değiştirin.

> [!IMPORTANT]
> Bu makalede, yerleşik onay özelliğini kullanırsınız. Etkileşim kurallarını bir tane de el ile ekleyebilirsiniz.
   
**Eğitme**' i seçerek değişiklikleri deneyin. Eğitim bittiğinde **Test**' i seçin.

- **Giriş**: *sıcaklığı 80 derece olarak ayarlayın*
- **Çıkış**: sıcaklığı 80 derece olarak ayarlamak istediğinizden emin misiniz?
- **Giriş**: *Hayır*
- **Çıkış**: sorun değil. Ne daha sonra sıcaklık?
- **Giriş**: *72 derece*
- **Çıkış**: sıcaklığı 72 derece olarak ayarlamak istediğinizden emin misiniz?
- **Giriş**: *Evet*
- **Çıkış**: Tamam, sıcaklık 72 derece olarak ayarlanıyor

### <a name="implement-corrections-in-a-command"></a>Bir komutta düzeltmeleri uygulama

Bu bölümde, tek adımlı bir düzeltme yapılandıracaksınız. Bu düzeltme, tamamlama eylemi çalıştırıldıktan sonra kullanılır. Ayrıca, komut henüz karşılanmazsa, bir düzeltmenin varsayılan olarak nasıl etkinleştirildiğini gösteren bir örnek görürsünüz. Komut bitmediğinde bir düzeltme eklemek için yeni parametresini ekleyin `AlarmTone` .

Sol bölmede **SetAlarm** komutunu seçin. Ardından, yeni **Alarmtone** parametresini ekleyin.
        
- **Ada** > `AlarmTone`
- **Tür**  >  **Dize**
- **Varsayılan değer**  >  **Oymes**
- **Yapılandırma**  >  **Önceden tanımlanmış giriş değerlerini iç katalogdan kabul etme**
- **Önceden tanımlanmış giriş değerleri**  >  **Oyırmes**, **jne** ve **yankı** (Bu değerler önceden tanımlanmış tek girdilerden oluşan değerlerdir.)


Sonra, **DateTime** parametresinin yanıtını, **alarmtone} olarak sesle ayarlamaya başlamaya hazırlanın. Ne zaman?** Sonra tamamlama kuralını aşağıdaki gibi değiştirin:

1. Mevcut tamamlama kuralını **ConfirmationResponse** seçin.
1. Sağdaki bölmede, var olan eylemin üzerine gelin ve **Düzenle**' yi seçin.
1. Konuşma yanıtını ' e güncelleştirin `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` .

> [!IMPORTANT]
> Uyarı tonu devam eden bir komutta herhangi bir açık yapılandırma olmadan değişebilir. Örneğin, komutun henüz bitmediğini değiştirebilir. Komutun henüz yerine getirilmesi durumunda, her komut parametresi için *Varsayılan olarak* bir düzeltme etkinleştirilir.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Komut bittiğinde bir düzeltme uygulama

Özel komutlar platformu, komut tamamlandığında bile tek adımlı düzeltme sağlar. Bu özellik varsayılan olarak etkin değildir. Açıkça yapılandırılması gerekir. 

Tek adımlı bir düzeltme yapılandırmak için aşağıdaki adımları kullanın:

1. **SetAlarm** komutunda, daha önce set alarmını güncelleştirmek Için **Öncekini Güncelleştir komutunu** yazın ' ın bir etkileşim kuralını ekleyin. Etkileşim kuralını **önceki alarmı Güncelleştir** olarak yeniden adlandırın.
1. Varsayılan koşulu bırakın: **Önceki komutun güncelleştirilmesi gerekir**.
1. Yeni koşul ekleyin:   >  **gerekli parametre**  >  **Tarih/saat** yazın.
1. Yeni bir eylem ekleyin: **tür**  >  **konuşma yanıtı**  >  **basit Düzenleyicisi**,  >  **önceki Alarm süresini {DateTime} olarak güncelleştirme**.
1. Varsayılan **yürütme sonrası durum** değerini **komut tamamlandı** olarak bırakın.

**Eğitme**' i seçerek değişiklikleri deneyin. Eğitimin bitmesini bekleyin ve **Test**' i seçin.

- **Giriş**: *bir alarm ayarlayın.*
- **Çıkış**: oyda oylarla alarm ayarlamaya hazırlanıyor. Ne zaman?
- **Giriş**: *9 yarın için jsıas ile bir alarm ayarlayın.*
- **Çıkış**: tamam, 2020-05-21 09:00:00 için alarm kümesi. Uyarı tonu, Jbir.
- **Giriş**: *Hayır, 8 har.*
- **Çıkış**: önceki alarm süresini 2020-05-29 08:00 olarak güncelleştirme.

> [!NOTE]
> Gerçek bir uygulamada, bu düzeltme kuralının **Eylemler** bölümünde, bir etkinliği istemciye geri göndermeniz veya sisteminizdeki alarm saatini güncelleştirmek IÇIN bir HTTP uç noktası çağırmanız gerekir. Bu eylemin yalnızca Alarm süresini güncelleştirmekten sorumlu olması gerekir. Komutun diğer herhangi bir özniteliğiyle sorumlu olmamalıdır. Bu durumda, bu öznitelik alarm tonu olacaktır.

## <a name="add-language-generation-templates-for-speech-responses"></a>Konuşma yanıtları için dil oluşturma şablonları ekleme

Dil oluşturma (LG) şablonları, istemciye gönderilen yanıtları özelleştirmenizi sağlar. Yanıtlara fark ederler. Kullanarak dil oluşturmayı elde edebilirsiniz:

* Dil oluşturma şablonları.
* Uyarlamalı ifadeler.

Özel komutlar şablonları, bot çerçevesinin [LG şablonlarını](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)temel alır. Özel komutlar özelliği gerektiğinde yeni bir LG şablonu oluşturduğundan (parametrelerde veya eylemlerdeki konuşma yanıtları için), LG şablonunun adını belirtmeniz gerekmez. 

Bu nedenle, şablonunuzu şöyle tanımlamanız gerekmez:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Bunun yerine, aşağıdaki gibi, şablon gövdesini adı olmadan tanımlayabilirsiniz:

> [!div class="mx-imgBorder"]
> ![Şablon Düzenleyicisi örneğini gösteren ekran görüntüsü.](./media/custom-commands/template-editor-example.png)


Bu değişiklik, istemciye gönderilen konuşma yanıtlarının çeşitlemesini sağlar. Utterlik için, ilgili konuşma yanıtı, belirtilen seçeneklerden rastgele olarak alınır.

LG şablonlarından yararlanarak, uyarlamalı ifadeler kullanarak komutlar için karmaşık konuşma yanıtlarını da tanımlayabilirsiniz. Daha fazla bilgi için bkz. [LG şablonları biçimi](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). 

Varsayılan olarak, özel komutlar özelliği, aşağıdaki küçük farklılıklar ile tüm özellikleri destekler:

* LG şablonlarında, varlıklar olarak temsil edilir `${entityName}` . Özel komutlar özelliği varlıkları kullanmaz. Ancak parametreleri `${parameterName}` temsili veya gösterimle değişken olarak kullanabilirsiniz `{parameterName}` .
* Özel komutlar özelliği şablon oluşturmayı ve genişletmeyi desteklemez, çünkü *. LG* dosyasını hiçbir şekilde doğrudan düzenleyemezsiniz. Yalnızca otomatik olarak oluşturulan şablonların yanıtlarını düzenleyebilirsiniz.
* Özel komutlar özelliği, düğüm kullanan özel işlevleri desteklemez. Önceden tanımlanmış işlevler desteklenir.
* Özel komutlar özelliği, ve gibi seçenekleri desteklemez `strict` `replaceNull` `lineBreakStyle` .

### <a name="add-template-responses-to-a-turnonoff-command"></a>Bir TurnOnOff komutuna şablon yanıtları ekleme

`TurnOnOff`Komutu yeni bir parametre eklemek için değiştirin. Aşağıdaki yapılandırmayı kullanın.

| Ayar            | Önerilen değer       | 
| ------------------ | --------------------- | 
| **Ad**               | `SubjectContext`         | 
| **Geneldir**          | Değilken             | 
| **Gerekli**           | Değilken               | 
| **Tür**               | **Dize**                |
| **Varsayılan değer**      | `all` |
| **Yapılandırma**      | **Önceden tanımlanmış giriş değerlerini iç katalogdan kabul et** | 
| **Önceden tanımlanmış giriş değerleri** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Tamamlama kuralını değiştirme

Mevcut tamamlama kuralı **ConfirmationResponse** için **Eylemler** bölümünü düzenleyin. **Eylemi Düzenle** penceresinde, **Şablon Düzenleyicisi**' ne geçin. Sonra metni aşağıdaki örnekle değiştirin.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Aşağıdaki giriş ve çıktıyı kullanarak uygulamanızı eğitme ve test etme. Yanıtların varyasyonuna dikkat edin. Çeşitleme, şablon değerinin birden çok alternatifine ve ayrıca Uyarlamalı ifadelerin kullanımına göre oluşturulur.

* Giriş: *TV 'yi açın*
* Çıkış: Tamam, TV 'yi açma
* Giriş: *TV 'yi açın*
* Çıkış: bitti, TV açık
* Giriş: *ışıkları* kapatma
* Çıkış: Tamam, tüm ışıkları kapatma
* Giriş: *Oda ışıklarını* kapatma
* Çıkış: Tamam, Oda ışıklarını kapatma

## <a name="use-a-custom-voice"></a>Özel bir ses kullanın

Özel komutlar yanıtlarını özelleştirmenin başka bir yolu da bir çıktı sesi seçkullanmaktır. Varsayılan sesi özel bir sese geçirmek için aşağıdaki adımları kullanın:

1. Özel komutlar uygulamanızda, sol taraftaki bölmede **Ayarlar**' ı seçin.
1. Orta bölmede **özel ses**' i seçin.
1. Tabloda, özel bir ses veya genel ses seçin.
1. **Kaydet**’i seçin.

> [!div class="mx-imgBorder"]
> ![Örnek cümleleri ve parametreleri gösteren ekran görüntüsü.](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> Genel sesler için sinir türleri yalnızca belirli bölgelerde kullanılabilir. Daha fazla bilgi için bkz. [konuşma hizmeti desteklenen bölgeler](./regions.md#neural-and-standard-voices).
>
> **Özel ses** projesi sayfasında özel sesler oluşturabilirsiniz. Daha fazla bilgi için bkz. [özel sesle çalışmaya başlama](./how-to-custom-voice.md).

Artık uygulama, varsayılan ses yerine seçili sesle yanıt verir.

## <a name="next-steps"></a>Sonraki adımlar

* Konuşma SDK 'sını kullanarak [özel komutlar uygulamanızı](how-to-custom-commands-setup-speech-sdk.md) bir istemci uygulamasıyla tümleştirmeyi öğrenin.
* Azure DevOps kullanarak özel komutlar uygulamanız için [sürekli dağıtım ayarlayın](how-to-custom-commands-deploy-cicd.md) . 
                      
