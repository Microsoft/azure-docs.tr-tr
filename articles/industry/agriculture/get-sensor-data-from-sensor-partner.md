---
title: İş ortaklarından algılayıcı verileri al
description: İş ortaklarından algılayıcı verilerinin nasıl alınacağını açıklar
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 33fd2221d3f3a0562abf83f014a7b84df972eebc
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128851"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Algılayıcı iş ortaklarından algılayıcı verileri al

Azure Farm, IoT cihazlarınızdan ve sensörlerden veri merkezine akış verileri almanıza yardımcı olur. Şu anda aşağıdaki algılayıcı cihaz iş ortakları desteklenir:

  ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

Cihaz verilerinin Azure Farmile tümleştirilmesi, grubunuzda veri hub 'ına dağıtılan IoT sensörlerinden veri almanıza yardımcı olur. Veriler, kullanılabilir olduğunda, Farmtts Hızlandırıcısı aracılığıyla görselleştirilir ve Farmtts kullanarak veri Fusion ve AI/ML modeli oluşturma için kullanılabilir.

Algılayıcı veri akışını başlatmak için aşağıdakilerden emin olun:

-  Azure Marketi 'nden Farmtts yüklediniz.
-  Grubunuza yüklemek istediğiniz sensörlerden ve cihazlara karar verdiniz.
-  Soil nemi sensörlerinden birini kullanmayı planlıyorsanız, algılayıcı sayısına ve tam olarak sensörler yerleştirmeli bir öneri almak için farmtts SOIL nemi algılayıcı yerleştirme haritasını kullanabilirsiniz. Daha fazla bilgi için bkz. [haritalar oluşturma](generate-maps.md).

- Grubunuzdaki cihaz/sensörlerden cihaz/algılayıcılar satın alıp dağıtın. Algılayıcı verilerine cihaz iş ortaklarınızın çözümü aracılığıyla erişebildiğinizden emin olun.

### <a name="enable-device-integration-with-farmbeats"></a>Cihaz tümleştirmesini Farmtempts ile etkinleştirme   

Algılayıcı verilerinin akışını başlattığınızda, verileri Farmtts sisteminize alma sürecini başlatabilirsiniz. Farmto 'Lar için tümleştirmeyi etkinleştirmek üzere cihaz sağlayıcınıza aşağıdaki bilgileri sağlamanız gerekir:  

 - API Uç Noktası  
 - Kiracı Kimliği  
 - İstemci Kimliği  
 - İstemci Gizli Anahtarı  
 - EventHub bağlantı dizesi

Yukarıdaki bilgiler, sistem tümleştiricisi tarafından size sunulmaktadır. Cihaz tümleştirmelerini etkinleştirirken oluşan herhangi bir sorun için sistem tümleştirmelerine başvurun.

Alternatif olarak, Azure Cloud Shell bu betiği çalıştırarak kimlik bilgilerini oluşturabilirsiniz. Aşağıdaki adımları izleyin:

1. [ZIP dosyasını](https://aka.ms/farmbeatspartnerscript) indirin ve yerel sürücünüze ayıklayın. ZIP dosyasının içinde iki dosya bulacaksınız.
2. https://portal.azure.com/ oturum açın ve Cloud Shell açın (Bu seçenek portalın sağ üst çubuğunda bulunur)  

    ![Proje grubu ları](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Ortamın **PowerShell** olarak ayarlandığından ve varsayılan olarak Bash olarak ayarlandığından emin olun.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. Cloud Shell iki dosyayı (yukarıdaki 1. adımdan) karşıya yükleyin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. Dosyaların karşıya yüklendiği dizine gidin (varsayılan olarak, > Kullanıcı adı giriş dizinine yüklenir).
6. Şu betiği çalıştırın:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```
7. Değerleri yakalamak için ekrandaki yönergeleri izleyin. (API uç noktası, kiracı KIMLIĞI, Istemci KIMLIĞI, Istemci parolası ve EventHub bağlantı dizesi).

**Oluşturulan kimlik bilgilerini kullanarak cihaz verilerini tümleştirme**

Önceki bölümde oluşturduğunuz kimlik bilgileri kümesini kullanarak Farmtts 'yi bağlamak için cihaz iş ortağı portalını ziyaret edin.

 - API Uç Noktası  
 - EventHub bağlantı dizesi  
 - İstemci Kimliği  
 - İstemci Gizli Anahtarı  
 - Kiracı Kimliği  

 Cihaz sağlayıcısı başarılı bir tümleştirmeyi onaylar. Onay sonrasında, tüm cihazları ve algılayıcıları Azure Farmtts üzerinde görüntüleyebilirsiniz.

## <a name="view-devices-and-sensors"></a>Cihazları ve algılayıcıları görüntüleme

Grubunuzdaki cihazları ve algılayıcıları görüntülemek için aşağıdaki bölümü kullanın.

### <a name="view-devices"></a>Cihazları görüntüleme

Şu anda Farmtts aşağıdaki cihazları destekler:

- **Düğüm**: bir veya daha fazla sensörün eklendiği cihaz.
- **Ağ geçidi**: bir veya daha fazla düğümün eklendiği cihaz.

Aşağıdaki adımları kullanın:

1. Giriş sayfasında, menüden **cihazlar** ' ı seçin.
  Cihazlar sayfası cihaz türünü, modeli, durumu, yerleştirildiği grubu ve meta verilerin son güncelleştirilme tarihini görüntüler. Varsayılan olarak, Grup sütunu NULL olarak ayarlanır. Bir gruba bir cihaz atamayı seçebilirsiniz. Daha fazla bilgi için bkz. [cihaz atama](#assign-devices).
2. Cihaza bağlı cihaz özelliklerini, telemetri ve alt cihazları görüntülemek için cihazı seçin.  

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Algılayıcıları görüntüle

Aşağıdaki adımları kullanın:

1. Giriş sayfasında, menüden **Algılayıcılar** ' ı seçin.
  Algılayıcılar sayfasında, algılayıcı türü, bağlandığı grup, ana cihaz, bağlantı noktası adı, bağlantı noktası türü ve son güncel durum hakkındaki ayrıntılar gösterilir.
2. Sensörden algılayıcı özelliklerini, etkin uyarıları ve telemetrisini görüntülemek için sensör seçin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Cihazları ata  

Algılayıcı verilerinin akışını yaptıktan sonra, Sensörlerinizi dağıttığınız gruba atayabilirsiniz.

1. Giriş sayfasında, menüden **gruplar** ' ı seçin, **gruplar** listesi sayfası görüntülenir.  
2. Cihazı atamak istediğiniz grubu seçin ve cihaz **Ekle**' yi seçin.  
3. **Cihaz Ekle** penceresi görüntülenir. Gruba atamak istediğiniz cihazı seçin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. **Cihaz Ekle**' yi seçin. Alternatif olarak, **cihazlar** menüsüne gidin, bir gruba atamak istediğiniz cihazları seçin ve **cihazları ilişkilendir**' i seçin.  
5. **Cihazları ilişkilendir** penceresinde, açılan listeden grubu seçin ve grubu tüm seçili cihazlarla Ilişkilendirmek Için **Tümüne Uygula** ' yı seçin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Her bir cihazı farklı bir grupla ilişkilendirmek için, **gruba ata** sütunundaki açılan eklentiyi seçin ve her bir cihaz satırı için bir grup seçin.  
7. Cihaz atamasını tamamlamaya **ata** ' yı seçin.

### <a name="visualize-sensor-data"></a>Algılayıcı verilerini görselleştirin

Aşağıdaki adımları kullanın:

1. Giriş sayfasında, **gruplar** sayfasını görüntülemek Için menüden **gruplar** ' ı seçin.  
2. Algılayıcı verilerini görmek istediğiniz **grubu** seçin.  
3. **Grup** panosunda telemetri verilerini görüntüleyebilirsiniz. Canlı Telemetriyi görüntülemeyi veya **özel Aralık** kullanmayı seçerek belirli bir tarih aralığında görüntüleme yapabilirsiniz.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>Algılayıcıyı Sil

Şu adımları uygulayın:

1. Giriş sayfasında, **sensör** sayfasını görüntülemek Için menüden **Algılayıcılar** ' ı seçin.  
2. Silmek istediğiniz cihazı seçin ve onay penceresinden **Sil** ' i seçin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Bir onay iletisi, sensör başarıyla silindiğini gösterir.  

## <a name="delete-devices"></a>Cihazları Sil

Şu adımları uygulayın:

1. Giriş sayfasında, cihazlar sayfasını görüntülemek için menüdeki **cihazlar** ' ı seçin.  
2. Silmek istediğiniz cihazı seçin ve onay penceresinden **Sil** ' i seçin.

    ![Proje grubu ları](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure Farmtts örneğiniz için sensör verileri akar. Şimdi, gruplar için [haritalar oluşturmayı](generate-maps.md#generate-maps) öğrenin.
