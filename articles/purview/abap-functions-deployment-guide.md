---
title: SAP R3 'de meta veri ayıklama ABAP işlev modülü-Azure purview
description: Bu makalede, SAP sunucusunda ABAP işlev modülünü dağıtma adımları özetlenmektedir
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 9bd3c315fcc15317a9fa483289fdc326ca6aa47f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614369"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>SAP R3 for köprülerine yönelik meta veri ayıklama ABAP işlev modülünü dağıtın

Bu makalede, SAP sunucusunda ABAP işlev modülünü dağıtma adımları özetlenmektedir.

## <a name="overview"></a>Genel Bakış

SAP Business Suite 4 HANA (S/4HANA), ECC ve R/3 ERP Köprüsü, SAP sunucusundan meta verileri ayıklamak için kullanılabilir. Bu, SAP sunucusuna ABAP Function modülü yerleştirilerek elde edilir. Bu işlev modülüne, SAP sunucusu içinde bulunan meta verileri sorgulamak ve indirmek (bir metin dosyası olarak) için köprü tarafından uzaktan erişilebilir.

Yürütüldüğünde, her iki köprü de:

1. Önceki bir köprüden yürütmeden yerel olarak zaten indirilmiş olan bir dosyanın meta verilerini içeri aktarır.

2. ABAP Module API 'sini çağırır, indirmeyi bekleyin ve sonra bu dosyadan meta verileri içeri aktarın.

Bu belge, Bu modülün dağıtılması için gereken adımların ayrıntılarını sağlar.

> [!Note]
> Aşağıdaki yönergeler SAP GUI v. 7.2 temelinde derlendi

## <a name="deployment-of-the-module"></a>Modülün dağıtımı

### <a name="create-a-package"></a>Paket oluşturma

Bu adım isteğe bağlıdır ve var olan bir paket kullanılabilir.

1. SAP S/4HANA veya SAP ECC sunucusunda oturum açın ve **Nesne Gezgini** 'ni (SE80 Transaction) açın.

2. Listeden seçenek **paketi** ' ni seçin ve yeni paket için bir ad girin (örneğin, Z \_ miti) ve ardından düğme **görüntüle**' ye basın.

3. **Paket oluştur** penceresinde **Evet** ' i seçin. Sonuç olarak, bir pencere **paketi Oluşturucusu: oluşturma paketi** açılır. **Kısa açıklama** alanına değer girin ve **devam** simgesini seçin.

4. **Yerel çalışma ekranı isteği** penceresinde **kendi isteklerini** seçin. **Geliştirme** isteği seçin.

### <a name="create-a-function-group"></a>Işlev grubu oluşturma

Nesne Gezgini ' nde listeden **Işlev grubu** ' nu seçin ve adını aşağıdaki giriş alanına yazın (örneğin, Z \_ miti \_ fgroup). **Görünüm** simgesini seçin.

1. **Nesne oluştur** penceresinde, yeni bir işlev grubu oluşturmak için **Evet** ' i seçin.

2. **Kısa metin** alanında uygun bir açıklama belirtin ve düğme **Kaydet**' e basın.

3. Önceki adımda hazırlanan paketi seçin **bir paket oluşturun** ve **Kaydet**' i seçin.

4. Simgeye **devam et** düğmesine basarak bir isteği onaylayın.

5. Işlev grubunu etkinleştirin.

### <a name="create-the-abap-function-module"></a>ABAP Function modülünü oluşturma

1. İşlev grubu oluşturulduktan sonra, bunu seçin.

2. Depo tarayıcısında işlev grubu adına sağ tıklayın ve **Oluştur**' u ve ardından **işlev modülü**' nü seçin.

3. **Işlev modülü** alanına, girin `Z_MITI_DOWNLOAD` . **Kısa metin** girişini uygun açıklamayla doldurun.

Modül oluşturulduğunda, aşağıdaki bilgileri belirtin:

1. **Öznitelikler** sekmesine gidin.

2. **Işlem türünü** **uzak özellikli işlev modülü** olarak seçin.

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Kaynakları Kaydet seçeneği-Remote-Enabled Işlev modülü" border="true":::

3. **Kaynak kodu** sekmesine gidin. İşlevi için kodu dağıtmanın iki yolu vardır:

   a. Ana menüden [Z \_ mitı \_ indirme](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) dosyası metin dosyasını karşıya yükleyin. Bunu yapmak için **yardımcı programlar**, **diğer yardımcı programlar**' ı seçin, ardından **karşıya yükleyin/indirin** ve **karşıya** yükleyin.

   b. Alternatif olarak, dosyayı açın, içeriğini kopyalayın ve **kaynak kodu** alanına yapıştırın.

4. **Içeri aktar** sekmesine gidin ve aşağıdaki parametreleri oluşturun:

   a.  P \_ alanı türü DD02L-TABNAME (Isteğe bağlı = true)

   b.  *P \_ Yerel \_ yol türü dizesi* (isteğe bağlı = true)

   c.  *P \_ DIL türü L001TAB-VERI varsayılan \' E\'*

   d.  *ROWCOMP TYPE, bu yüzden \_ Int varsayılan 0*

   e.  *ROWCOUNT türü, \_ Int varsayılan 0*

   > [!Note]
   > Hepsi için **değer geçir** ' i seçin

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Kaynakları kaydetme seçeneği-parametreleri Içeri aktar" border="true":::

5. "Tablolar" sekmesine gidin ve aşağıdakileri tanımlayın:

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="Kaynakları kaydetme seçenekleri-tablolar sekmesi" border="true":::

6. **Özel durumlar** sekmesine gidin ve aşağıdaki özel durumu tanımlayın:`E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="Kaynakları kaydetme seçenekleri-özel durumlar sekmesi" border="true":::

7. İşlevi kaydedin (CTRL + S tuşlarına basın veya **Işlev modülünü** seçin, sonra ana menüye **kaydedin** ).

8. Araç çubuğunda **Etkinleştir** simgesine tıklayın (CTRL + F3) ve Iletişim penceresinde  **devam** düğmesini seçin. İstenirse, ana işlev modülüyle birlikte etkinleştirilmesi için oluşturulan eklemeleri seçmelisiniz.

### <a name="testing-the-function"></a>Işlevi test etme

Tüm önceki adımlar tamamlandığında, işlevi test etmek için aşağıdaki adımları izleyin:

1. Z \_ mitı \_ indirme işlevi modülünü açın.

2. **Işlev modülünü** seçin ve ardından ana menüden test **işlevi modülünü** **test edin (** veya F8 tuşuna basın).

3. Yerel dosya sistemindeki klasörün yolunu P \_ yerel \_ yoluna girin ve araç çubuğundaki **yürütme** simgesine basın (veya F8 tuşuna basın).

4. \_Meta veri içeren bir dosya indirilmeli veya güncelleniyorsa, ilgilendiğiniz alanın adını P alanı alanına koyun. İşlev çalışmayı bitirdiğinde, P \_ yerel \_ yol parametresinde belirtilen klasör içinde meta verileri olan birkaç dosya içermelidir. Dosya adları, P alanı alanında belirtime alanları taklit edebilir \_ .

İşlev, yürütmesini tamamlayacak ve meta verileri, SAP S/4HANA veya ECC Server ile yüksek hızda ağ bağlantısı olan bir makinede başlatılması durumunda çok daha hızlı indirilir.

## <a name="next-steps"></a>Sonraki adımlar

- [SAP ECC kaynağını kaydetme ve tarama](register-scan-sapecc-source.md)
- [SAP S/4HANA kaynağını kaydedin ve tarayın](register-scan-saps4hana-source.md)
