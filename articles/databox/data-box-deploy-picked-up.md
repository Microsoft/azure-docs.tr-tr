---
title: Microsoft Azure Data Box'ı geri göndermeye yönelik öğretici | Microsoft Docs
description: Azure Data Box’ınızı Microsoft'a nasıl geri göndereceğinizi öğrenin
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 28666aaac4ec221acca00d937d54a753a4e6a055
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172631"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Öğretici: Azure Data Box'ı iade etme ve Azure'a verilerin yüklendiğini doğrulama

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Data Box'ı iade etme ve Azure'a verilerin yüklendiğini doğrulama

::: zone-end

::: zone target="docs"

Bu öğretici, Azure Data Box’ın nasıl iade edileceğini ve yüklenen verileri nasıl doğrulayabileceğinizi anlatır.

Bu öğreticide şu gibi konular hakkında bilgi edineceksiniz:

> [!div class="checklist"]
> * Ön koşullar
> * Göndermeye hazırlama
> * Data Box'ı Microsoft'a gönderme
> * Azure'a verilerin yüklendiğini doğrulama
> * Data Box'tan verileri silme

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce şunlardan emin olun:

- [Öğretici: Verileri Azure Data Box'a kopyalama ve doğrulama](data-box-deploy-copy-data.md) bölümünü tamamladınız. 
- Kopyalama işleri tamamlandı. Kopyalama işleri devam ediyorsa Göndermeye hazırlama işi gerçekleştirilemez.

## <a name="prepare-to-ship"></a>Göndermeye hazırlama

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Veri kopyalama işlemi tamamlandıktan sonra cihazı hazırlayıp gönderin. Cihaz Azure veri merkezine ulaştığında veriler Azure'a otomatik olarak yüklenir.

## <a name="prepare-to-ship"></a>Göndermeye hazırlama

Göndermeye hazırlama işleminden önce kopyalama işlerinin tamamlandığından emin olun.

1. Yerel web kullanıcı arabirimindeki **Göndermeye hazırla** sayfasına gidin ve gönderme hazırlığına başlayın. 
2. Cihazı yerel web kullanıcı arabiriminden kapatın. Kabloları cihazdan çıkarın. 

Sonraki adımlar cihazın iade edildiği bölgeye göre belirlenir.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Data Box'ı geri gönderme

Cihaza veri kopyalamanın tamamlandığından ve **göndermeye hazırlama** işleminin başarılı olduğundan emin olun. Cihazı gönderdiğiniz bölgeye bağlı olarak yordam farklıdır.

::: zone-end

## <a name="in-us-canada-europetabin-us-canada-europe"></a>[ABD, Kanada, Avrupa](#tab/in-us-canada-europe)

Cihazı ABD, Kanada veya Avrupa’da iade ediyorsanız aşağıdaki adımları uygulayın.

1. Cihazın kapalı olduğundan ve kabloların çıkartılmış olduğundan emin olun. 
2. Cihaz ile beraber sağlanan güç kablosunu sararak emniyetli şekilde cihazın arkasına yerleştirin.
3. E-ink ekranda gönderi etiketinin görüntülendiğinden emin olun ve taşıyıcınızdan bir teslim alma randevusu alın. Etiket hasar görür veya kaybolursa ya da E-ink ekranında gösterilmezse Microsoft Desteği’ne başvurun. Destek öneride bulunursa Azure portalındaki **Genel Bakış > Gönderim etiketini indir** bölümüne gidebilirsiniz. Gönderim etiketini indirip cihaza yapıştırın. 
4. Cihazı iade ediyorsanız UPS ile bir toplama zamanı ayarlayın. Teslim alma planlamak için:

    - Yerel UPS şubesini (ülkeye/bölgeye özel ücretsiz hatlar) arayın.
    - Görüşme esnasında, E-ink ekranında veya basılı etiketinizde gördüğünüz iade izleme numarasını bildirin.
    - İzleme numarası bildirilmezse, teslim alma esnasında UPS’ye ek ücret ödemeniz gerekir.

    Teslim alma zamanlama yerine Data Box’ı en yakın teslim etme yerine bırakabilirsiniz.
4. Data Box nakliyeciniz tarafından toplandıktan ve tarandıktan sonra, portaldaki sipariş durumu **Toplandı** olarak güncelleştirilir. Ayrıca bir takip numarası da görüntülenir.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure'a verilerin yüklendiğini doğrulama

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box'tan verileri silme
 
Veriler Azure'a yüklendikten sonra Data Box disklerindeki veriyi [NIST SP 800-88 Revision 1 yönergelerine](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) uygun şekilde siler.

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]



::: zone-end


## <a name="in-australiatabin-australia"></a>[Avustralya](#tab/in-australia)

Avustralya’daki Azure veri merkezlerinde ek güvenlik bildirimi uygulanır. Gelen tüm sevkiyatların önceden bildirilmesi gerekir. Avustralya’da gönderme için aşağıdaki adımları uygulayın.


1. İade gönderimi için cihazı gönderirken kullanılan orijinal kutuyu saklayın.
2. Cihaza veri kopyalamanın tamamlandığından ve **göndermeye hazırlama** işleminin başarılı olduğundan emin olun.
3. Cihazı kapatın ve kabloları çıkarın.
4. Cihaz ile beraber sağlanan güç kablosunu sararak emniyetli şekilde cihazın arkasına yerleştirin.
5. Kargo toplama isteğinde bulunmak için Quantium Solutions şirketine e-posta gönderin. Azure portalında belirtilen hizmet başvuru numarasını referans gösterin. Şu e-posta şablonunu kullanın: - *TAU kodu ile ters gönderim etiketi isteyin*. E-postaya aşağıdaki ayrıntıları eklediğinizden emin olun: 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. Quantium Solutions Australia size e-posta ile bir iade gönderim etiketi gönderecektir.
7. İade etiketini yazdırıp gönderi kutusuna yapıştırın.
8. Paketi kargoya verin.

Gerekirse, Quantium Solution Desteği’ne Azure@quantiumsolutions.com adresinden e-posta ile veya telefonla ulaşabilirsiniz.

Telefonla siparişiniz hakkında soru sormak için:

- Öncelikle kargo toplama için bir e-posta gönderin.
- Telefonda siparişinizin adını verin.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure'a verilerin yüklendiğini doğrulama

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box'tan verileri silme
 
Veriler Azure'a yüklendikten sonra Data Box disklerindeki veriyi [NIST SP 800-88 Revision 1 yönergelerine](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) uygun şekilde siler.

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="in-japantabin-japan"></a>[Japonya](#tab/in-japan) 

1. İade gönderimi için cihazı gönderirken kullanılan orijinal kutuyu saklayın.
2. Cihazı kapatın ve kabloları çıkarın.
3. Cihaz ile beraber sağlanan güç kablosunu sararak emniyetli şekilde cihazın arkasına yerleştirin.
4. Sevk mektubuna, gönderici bilgisi olarak şirket adınızı ve adres bilgilerinizi yazın.
5. Aşağıdaki e-posta şablonunu kullanarak Quantium Solutions’a e-posta gönderin.

    - Japan Post Chakubarai sevk mektubu gönderilmediyse veya kaybolduysa, gönderdiğiniz e-postada bunu da bildirin. Quantium Solutions Japan, teslim alma esnasında Japan Post’un sevk mektubunu da getirmesini ister.
    - Birden fazla siparişiniz varsa her birinin ayrı olarak teslim alınması için e-posta gönderin.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. Teslim alma rezervasyonu yaptıktan sonra Quantium Solutions’tan e-posta onay iletisi alırsınız. E-posta onay iletisi, Chakubarai sevk mektubuna yönelik bilgileri de içerir.

Gerekirse, aşağıdaki bilgilerden Quantium Solutions Desteği (Japonca) ile iletişime geçebilirsiniz: 

- E-posta: Customerservice.JP@quantiumsolutions.com 
- Telefon：03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure'a verilerin yüklendiğini doğrulama

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box'tan verileri silme
 
Veriler Azure'a yüklendikten sonra Data Box disklerindeki veriyi [NIST SP 800-88 Revision 1 yönergelerine](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) uygun şekilde siler.

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end



