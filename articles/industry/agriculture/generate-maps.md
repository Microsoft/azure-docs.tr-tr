---
title: Haritalar oluştur
description: Farmtempts 'de eşlemelerin nasıl oluşturulacağını açıklar
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 635431fb87e5f164f92ab4b7a1027ee96e9d801a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891022"
---
# <a name="generate-maps"></a>Haritalar oluştur

Azure Farmıts 'yi kullanarak, uydu Imagery ve algılayıcı veri girişlerini kullanarak aşağıdaki haritaları oluşturabilirsiniz. Haritalar, grubunuzun coğrafi konumunu görüntülerken size yardımcı olur ve cihazlarınız için uygun konumu belirler.

  -  **Algılayıcı yerleştirme Haritası** : kaç sensörün ve bir gruba nereye yerleştirileceğini gösteren öneriler sağlar.
  - **Uydu endeksleri eşlemesi** – bir grup için vegetation dizinini ve su dizinini gösterir.
  - **SOIL nemi eşlemesi** : uydu verilerini ve algılayıcı verilerini kullanarak SOIL nemi dağıtımını gösterir.

## <a name="sensor-placement-maps"></a>Algılayıcı yerleştirme haritaları

Farmtts algılayıcı yerleştirme Haritası SOIL nemi algılayıcılarının yerleştirilmesi için size yardımcı olur. Harita çıktısı, algılayıcı dağıtımı için bir koordinat listesinden oluşur. Bu sensörlerden girişler, SOIL Moisture heatmap 'i oluşturmak için uydu canlandırın ile birlikte kullanılır.  

Bu harita, yıl boyunca birden çok tarih üzerinde görüldüğü gibi kanopi 'yi, hatta çıplak ve binaları Kanopi 'nin bir parçası olan şekilde parçalara ayırarak türetilir. Konumda gerekli olmayan sensörlerden çıkarabilirsiniz. Bu harita rehberlik içindir ve özel bilginiz temelinde konum ve sayıları biraz daha değiştirebilirsiniz (sensörler eklemek SOIL nemi ısı haritası sonuçlarını Reg, ancak algılayıcı numarası azaltıldığında, ısı haritası doğruluğuna bir sorun olma olasılığı vardır).  

## <a name="before-you-begin"></a>Başlamadan önce  

Bir algılayıcı yerleştirme eşlemesi oluşturmayı denemeden önce aşağıdakileri doğrulayın:

- Grup boyutu birden fazla Acre olmalıdır.
- Bulut içermeyen Sentinel sahneleri sayısı, seçilen tarih aralığı için altıdan büyük olmalıdır.  
- En az altı bulut ücretsiz Sentinel sahneleri NDVı > = 0,3 olmalıdır.

    > [!NOTE]
    > Sentinel Kullanıcı başına yalnızca iki eşzamanlı iş parçacığına izin verir. Sonuç olarak, işler kuyruğa alınır ve tamamlanması daha uzun sürebilir.

### <a name="dependencies-on-sentinel"></a>Sentinel üzerinde bağımlılıklar  

Aşağıdaki Sentinel bağımlılıklarıdır:

- Uydu görüntülerini indirmek için Sentinel performansına bağımlıdır. Sentinel performans durumunu ve bakım [etkinliklerini](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)denetleyin.
- Sentinel, Kullanıcı başına yalnızca iki eşzamanlı [indirme iş parçacığına](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) izin verir.
- Duyarlık eşleme oluşturma işlemi [Sentinel kapsam ve geri ödeme sıklığından]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)etkilenecek.

## <a name="create-sensor-placement-map"></a>Algılayıcı yerleştirme eşlemesi oluştur
Bu bölümde, algılayıcı yerleştirme haritaları oluşturma yordamlarına ayrıntılar yer almamıştır.

> [!NOTE]
> , **Haritalar** sayfasından veya **Grup ayrıntıları** sayfasındaki **duyarlık haritaları oluştur** açılır menüsünden algılayıcı yerleşimi başlatabilirsiniz.

Aşağıdaki adımları kullanın:

1. Giriş sayfasında sol gezinti menüsünden **haritalar** ' a gidin.
2. **Haritalar oluştur** ' u seçin ve açılan menüden **algılayıcı yerleşimi** ' nı seçin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. **Algılayıcı yerleştirmesini**seçin.
  Algılayıcı yerleşimi penceresi görüntülenir.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. **Grup** açılan menüsünden bir grup seçin.  
   Grubunuzu aramak ve seçmek için, açılan listede kaydırma yapabilir veya metin kutusuna grubun adını yazabilirsiniz.
5. Son yıl için bir harita oluşturmak istiyorsanız **Önerilen** seçeneği belirleyin.
6. Özel bir tarih aralığı için bir eşleme oluşturmak istiyorsanız, **Tarih aralığını Seç**' i seçin ve algılayıcı yerleşimi eşlemesini oluşturmak istediğiniz başlangıç ve bitiş tarihini belirtin.
7. **Haritaları oluştur**' u seçin.
 İş ayrıntıları içeren bir onay iletisi görüntülenir.

  İş durumu hakkında bilgi için bkz. **Işleri görüntüleme**. İş durumu *başarısız*gösteriyorsa, *başarısız* durumun araç ipucunda ayrıntılı bir hata iletisi görüntülenir. Bu durumda, yukarıda listelenen adımları yineleyin ve yeniden deneyin.

  Sorun devam ederse, ilgili günlüklere yönelik destek için [sorun giderme](troubleshoot-project-farmbeats.md) bölümüne bakın veya [Azure farmtempts forumuna](https://aka.ms/FarmBeatsMSDN) başvurun.

### <a name="view-and-download-sensor-placement-map"></a>Algılayıcı yerleşimi haritasını görüntüle ve indir

Aşağıdaki adımları kullanın:

1. Giriş sayfasında sol gezinti menüsünden **haritalar** ' a gidin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Pencerenin sol gezinti bölmesinde **filtre** ' yi seçin.
  **Filtre** penceresi arama ölçütlerini görüntüler.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Açılır menüden **tür**, **Tarih** ve **ad** ' ı seçin ve ardından görüntülemek istediğiniz Haritayı aramak için **Uygula** ' yı seçin.
  İşin oluşturulduğu tarih type_farmname_YYYY-AA-GG biçiminde gösterilir.
4. Sayfanın sonundaki gezinti çubukları kullanılarak kullanılabilir haritalar listesinde gezinin.
5. Görüntülemek istediğiniz haritayı seçin. Bir açılır pencere, seçilen haritanın önizlemesini görüntüler.
6. **İndir**' i seçin ve sensör koordinatlarının geojson dosyasını indirin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Uydu dizinleri Haritası

Aşağıdaki bölümlerde, uydu dizinleri eşlemesini oluşturma ve görüntüleme ile ilgili yordamlar açıklanmaktadır.

> [!NOTE]
> **Haritalar** sayfasından veya **Grup ayrıntıları** sayfasındaki **duyarlık haritaları oluştur** açılan menüsünde uydu dizinleri eşlemesini başlatabilirsiniz.

Farmpts, gruplar için normalleştirilmiş fark vegetation dizini (NDVı), gelişmiş vegetation dizini (EVI) ve normalleştirilmiş fark su dizini (NDWI) haritaları oluşturma olanağı sağlar. Bu dizinler, kırpmadaki Şu anda büyümekte olan veya geçmişte büyümekte olan ya da zemin içindeki temsili su düzeylerini belirlemesine yardımcı olur.


> [!NOTE]
> Bir Sentinel görüntüsü, eşlemenin oluşturulduğu günler için gereklidir.


## <a name="create-satellite-indices-map"></a>Uydu dizinleri Haritası Oluştur

Aşağıdaki adımları kullanın:

1. Giriş sayfasında sol gezinti menüsünden **haritalar** ' a gidin.
2. **Haritalar oluştur** ' u seçin ve açılan menüden **uydu dizinleri** ' ni seçin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. **Uydu dizinleri**' ni seçin.
  Uydu dizinleri penceresi görüntülenir.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Açılan menüden bir grup seçin.  
   Grubunuzu aramak ve seçmek için, aşağı açılan listede kaydırma yapabilir veya grubun adını yazabilirsiniz.   
5. Geçen hafta için bir harita oluşturmak istiyorsanız **Bu hafta** seçeneğini belirleyin.
6. Özel tarih aralığı için bir harita oluşturmak istiyorsanız, **Tarih aralığı Seç**' i seçin ve uydu dizinleri eşlemesini oluşturmak istediğiniz başlangıç ve bitiş tarihini belirtin.
7. **Haritaları oluştur**' u seçin.
    İş ayrıntıları içeren bir onay iletisi görüntülenir.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    İş durumu hakkında bilgi için bkz. **Işleri görüntüleme**. İş durumu *başarısız*gösteriyorsa, *başarısız* durumundaki araç ipucunda ayrıntılı bir hata iletisi görüntülenir. Bu durumda, yukarıda listelenen adımları yineleyin ve yeniden deneyin.

    Sorun devam ederse, ilgili günlüklere yönelik destek için [sorun giderme](troubleshoot-project-farmbeats.md) bölümüne bakın veya [Azure farmtempts forumuna](https://aka.ms/FarmBeatsMSDN) başvurun.

### <a name="view-and-download-map"></a>Haritayı görüntüle ve indir

Aşağıdaki adımları kullanın:

1. Giriş sayfasında sol gezinti menüsünden **haritalar** ' a gidin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Pencerenin sol gezinti bölmesinde **filtre** ' yi seçin, **filtre** penceresi arama ölçütlerini görüntüler.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Açılır menüden **tür**, **Tarih** ve **ad** ' ı seçin ve ardından görüntülemek istediğiniz Haritayı aramak için **Uygula** ' yı seçin.
  İşin oluşturulduğu tarih type_farmname_YYYY-AA-GG biçiminde gösterilir.

4. Sayfanın sonundaki gezinti çubukları kullanılarak kullanılabilir haritalar listesinde gezinin.
5. Her **Grup adı** ve **tarihin**birleşimi için aşağıdaki üç eşleme kullanılabilir:
    - NDVı
    - EVI
    - NDWI
6. Görüntülemek istediğiniz haritayı seçin. Bir açılır pencere, seçilen haritanın önizlemesini görüntüler.
7. İndirme biçimini seçmek için **İndir** açılan menüsünü seçin ve eşleme indirilir ve bilgisayarınızdaki yerel klasörünüzde depolanır.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="get-soil-moisture-heatmap"></a>SOIL Moisture heatmap 'i al

SOIL nemi, SOIL parçacıkları arasındaki boşlukların tutulduğu su. SOIL nemi heatmap, SOIL nemi verilerini, gruplarınızın içinde yüksek çözünürlükte herhangi bir derinlikte anlamanıza yardımcı olur. Doğru ve kullanılabilir bir SOIL Moisture heatmap oluşturmak için, tüm sensörlerden aynı sağlayıcıdan olduğu tek bir algılayıcı dağıtımı gereklidir. Farklı sağlayıcılar, SOIL nemi 'in ayarlama farklılıklarıyla birlikte ölçüldüğü fark eder. Heatmap, bu derinlikte dağıtılan sensörler kullanılarak belirli bir derinlik için oluşturulur.

### <a name="before-you-begin"></a>Başlamadan önce  

SOIL Moisture heatmap oluşturmayı denemeden önce aşağıdakileri doğrulayın:

- En az üç Soil nemi sensörünün dağıtılması gerekir. Microsoft, algılayıcılar dağıtılmadan ve grupla ilişkilendirilmeden önce bir SOIL Moisture Haritası oluşturmayı denemenize olanak önerir.  
- SOIL Moisture heatmap, Sentinel 'in yol kapsamı, bulut kapağı ve bulut gölgesi tarafından etkilenir. En az bir adet bulut ücretsiz Sentinel görünümü, son 120 gün için, SOIL Moisture eşlemesinin istendiği günden kullanılabilir olmalıdır.
- Grupta dağıtılan sensörların en az yarısı çevrimiçi olmalıdır ve veri merkezine veri akışı olmalıdır.
- Heatmap 'in aynı sağlayıcıdan gelen algılayıcı ölçüleri kullanılarak oluşturulması gerekir.


## <a name="create-soil-moisture-heatmap"></a>SOIL Moisture heatmap oluştur

Aşağıdaki adımları kullanın:

1. Giriş sayfasında, haritalar sayfasını görüntülemek için sol gezinti menüsünden **haritalar** ' a gidin.
2. **Haritalar oluştur** ' u seçin ve açılan menüden **Soil Moisture** ' i seçin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. **SOIL Moisture**' i seçin.
    SOIL Moisture penceresi görüntülenir.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. **Grup** açılan menüsünden bir grup seçin.  
   Grubunuzu aramak ve seçmek için, açılan listeden kaydırma yapabilir veya Grup Seç açılan menüsünde grubun adını yazabilirsiniz.
5. **SOIL nemi algılayıcı ölçüsünü Seç** açılan menüsünde, eşlemesini oluşturmak istediğiniz SOIL nemi algılayıcı ölçüsünü (derinlik) seçin.
Algılayıcı ölçüsünü bulmak için **sensörler**' e gidin, herhangi bir SOIL nemi algılayıcısı seçin. Ardından, **algılayıcı özellikleri** bölümünde değeri **Ölçü adında** kullanın
6. **Bugün** veya **Bu hafta**için oluşturmak istiyorsanız seçeneklerden birini seçin.
7. Özel bir tarih aralığı için oluşturmak istiyorsanız, **Tarih aralığını Seç**' i seçin ve SOIL Moisture haritasını oluşturmak istediğiniz başlangıç ve bitiş tarihini belirtin.
8. **Haritaları oluştur**' u seçin.
 İş ayrıntıları içeren bir onay iletisi görüntülenir.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    İş durumu hakkında bilgi için bkz. **Işleri görüntüleme**. İş durumu *başarısız*gösteriyorsa, *başarısız* durumundaki araç ipucunda ayrıntılı bir hata iletisi görüntülenir. Bu durumda, yukarıda listelenen adımları yineleyin ve yeniden deneyin.

    Sorun devam ederse, ilgili günlüklere yönelik destek için [sorun giderme](troubleshoot-project-farmbeats.md) bölümüne bakın veya [Azure farmtempts forumuna](https://aka.ms/FarmBeatsMSDN) başvurun.

### <a name="view-and-download-map"></a>Haritayı görüntüle ve indir

Aşağıdaki adımları kullanın:

1. Giriş sayfasında sol gezinti menüsünden **haritalar** ' a gidin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Pencerenin sol gezinti bölmesinde **filtre** ' yi seçin, **filtre** penceresi haritalar arayınızdan görüntülenir.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Açılır menüden **tür**, **Tarih** ve **ad** ' ı seçin ve ardından görüntülemek istediğiniz Haritayı aramak için **Uygula** ' yı seçin. İşin oluşturulduğu tarih type_farmname_YYYY-AA-GG biçiminde gösterilir.
4. Grup, tarih, Oluşturulma tarihi, Iş KIMLIĞI ve Iş türüne göre sıralamak için tablo üst bilgilerinin yanındaki **sıralama** simgesini seçin.
5. Sayfanın sonundaki gezinti düğmelerini kullanarak kullanılabilir haritalar listesinde gezinin.
6. Görüntülemek istediğiniz haritayı seçin. Bir açılır pencere, seçilen haritanın önizlemesini görüntüler.
7. İndirme biçimini seçmek için **İndir** açılan menüsünü seçin ve eşleme indirilip belirtilen klasöre depolanır.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
