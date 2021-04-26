---
title: Azure Güvenlik Merkezi 'nde yanlış pozitifleri veya diğer istenmeyen güvenlik uyarılarını gizlemek için uyarı gizleme kurallarını kullanma.
description: Bu makalede, istenmeyen güvenlik uyarılarını gizlemek için Azure Güvenlik Merkezi 'nin gizleme kurallarının nasıl kullanılacağı açıklanmaktadır
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 02/17/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 646495597565bbb033ac3adaa15f3754f33e8fd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634179"
---
# <a name="suppress-alerts-from-azure-defender"></a>Azure Defender 'dan uyarıları gösterme

Bu sayfada, Azure Defender 'daki hatalı pozitif durumları veya diğer istenmeyen güvenlik uyarılarını gizlemek için uyarı gizleme kurallarını nasıl kullanabileceğiniz açıklanmaktadır.

## <a name="availability"></a>Kullanılabilirlik

|Görünüş|Ayrıntılar|
|----|:----|
|Yayın durumu:|Genel kullanılabilirlik (GA)|
|Fiyat|Ücretsiz<br>(Çoğu güvenlik uyarısı yalnızca Azure Defender ile kullanılabilir)|
|Gerekli roller ve izinler:|**Güvenlik Yöneticisi** ve **sahibi** kuralları oluşturabilir/silebilir.<br>**Güvenlik okuyucu** ve **okuyucu** , kuralları görüntüleyebilir.|
|Larının|![Yes](./media/icons/yes-icon.png) Ticari bulutlar<br>![Yes](./media/icons/yes-icon.png) Ulusal/Sogeign (US Gov, Çin gov, diğer gov)|
|||


## <a name="what-are-suppression-rules"></a>Gizleme kuralları nelerdir?

Çeşitli Azure Defender planları ortamınızın herhangi bir alanındaki tehditleri algılar ve güvenlik uyarıları oluşturur.

Tek bir uyarı ilgi çekici veya ilgili olmadığında bunu el ile kapatabilirsiniz. Alternatif olarak, daha sonra benzer uyarıları otomatik olarak kapatmak için gizleme kuralları özelliğini kullanın. Genellikle bir gizleme kuralı kullanarak şunları yapabilirsiniz:

- Yanlış pozitif sonuçlar olarak belirlediğiniz uyarıları gösterme

- Çok sık tetiklenen uyarıları, faydalı olması için gösterme

Gizleme kurallarınız, uyarıların otomatik olarak hangi ölçütlere göre kapatılacağını tanımlar.

> [!CAUTION]
> Güvenlik uyarılarını gizleme, Azure Defender 'ın tehdit koruması verimliliğini azaltır. Herhangi bir gizleme kuralının olası etkisini dikkatle denetlemeniz ve zaman içinde izlemeniz gerekir.

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="Uyarı gizleme kuralı oluştur":::

## <a name="create-a-suppression-rule"></a>Engelleme kuralı oluşturma

İstenmeyen güvenlik uyarılarını engellemek için kurallar oluşturmanın birkaç yolu vardır:

- Uyarıları yönetim grubu düzeyinde engellemek için Azure İlkesi'ni kullanın
- Uyarıları abonelik düzeyinde engellemek için, aşağıda açıklandığı gibi Azure portal veya REST API kullanabilirsiniz

Gizleme kuralları yalnızca seçili aboneliklerde zaten tetiklenen uyarıları kapatabilir.

Azure portal doğrudan bir kural oluşturmak için:

1. Güvenlik Merkezi'nin güvenlik uyarıları sayfasından:

    - Artık görmek istemediğiniz belirli uyarıyı seçin ve Ayrıntılar bölmesinden **eylem al**' ı seçin.

    - Ya da sayfanın üst kısmındaki **gizleme kuralları** bağlantısını ve gizleme kuralları sayfasında **Yeni gizleme kuralı oluştur**' u seçin:

        ![Yeni gizleme kuralı oluştur * * düğmesi](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. Yeni gizleme kuralı bölmesinde yeni kuralınızın ayrıntılarını girin.
    - Kuralınız, daha sonra bu gibi bir uyarı alabilmek için **tüm kaynaklardaki** uyarıları kapatabilir.     
    - Kuralınız belirli bir **ölçütte** uyarıyı kapatabilir; belırlı bir IP adresi, işlem adı, Kullanıcı hesabı, Azure kaynağı veya konum ile ilgilidir.

    > [!TIP]
    > Yeni kural sayfasını belirli bir uyarıdan açtıysanız, uyarı ve abonelik yeni kuralınızdan otomatik olarak yapılandırılır. **Yeni gizleme kuralı oluştur** bağlantısını kullandıysanız, seçili abonelikler portaldaki geçerli filtreyle eşleşir.

    [![Kural oluşturma bölmesinin gizleme](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. Kuralın ayrıntılarını girin:
    - **Ad** -kural için bir ad. Kural adları bir harf veya sayı ile başlamalı, 2 ila 50 karakter uzunluğunda olmalı ve tire (-) ya da alt çizgi (_) dışında bir simge içermemelidir. 
    - **Durum** -etkin veya devre dışı.
    - **Neden** -gereksinimlerinizi karşılamıyorsa, yerleşik nedenlerden birini veya ' diğer ' seçeneğini belirleyin.
    - **Sona erme tarihi** -kuralın bitiş tarihi ve saati. Kurallar en fazla altı ay çalıştırılabilir.
1. İsteğe bağlı olarak, bu kural etkin olduğunda kaç uyarı kapatılacağını görmek için **Benzetim** düğmesini kullanarak kuralı test edin.
1. Kuralı kaydedin. 


## <a name="edit-a-suppression-rule"></a>Gizleme kuralını Düzenle

Oluşturduğunuz bir kuralı düzenlemek için gizleme kuralları sayfasını kullanın.

1. Güvenlik Merkezi 'nin güvenlik uyarıları sayfasında, sayfanın üst kısmındaki **gizleme kuralları** bağlantısını seçin.
1. Gizleme kuralları sayfası, seçili abonelikler için tüm kurallarla birlikte açılır.

    [![Gizleme kuralları listesi](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Tek bir kuralı düzenlemek için, kural için üç nokta menüsünü (...) açın ve **Düzenle**' yi seçin.
1. Gerekli değişiklikleri yapın ve **Uygula**' yı seçin. 

## <a name="delete-a-suppression-rule"></a>Gizleme kuralını Sil

Oluşturduğunuz bir veya daha fazla kuralı silmek için gizleme kuralları sayfasını kullanın.

1. Güvenlik Merkezi 'nin güvenlik uyarıları sayfasında, sayfanın üst kısmındaki **gizleme kuralları** bağlantısını seçin.
1. Gizleme kuralları sayfası, seçili abonelikler için tüm kurallarla birlikte açılır.
1. Tek bir kuralı silmek için, kural için üç nokta menüsünü (...) açın ve **Sil**' i seçin.
1. Birden çok kuralı silmek için, kuralların silineceği onay kutularını seçin ve **Sil**' i seçin.
    ![Bir veya daha fazla gizleme kuralını silme](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="create-and-manage-suppression-rules-with-the-api"></a>API ile gizleme kuralları oluşturma ve yönetme

Güvenlik Merkezi 'nin REST API aracılığıyla uyarı gizleme kuralları oluşturabilir, görüntüleyebilir veya silebilirsiniz. 

REST API kuralları gizleme için ilgili HTTP yöntemleri şunlardır:

- **PUT**: belirtilen abonelikte bir gizleme kuralı oluşturmak veya güncelleştirmek için.

- **Al**:

    - Belirtilen bir abonelik için yapılandırılmış tüm kuralları listelemek için. Bu yöntem, uygulanabilir kuralların dizisini döndürür.

    - Belirtilen bir abonelikte belirli bir kuralın ayrıntılarını almak için. Bu yöntem bir gizleme kuralı döndürür.

    - Gizleme kuralının etkisinin hala tasarım aşamasında benzetimini yapmak. Bu çağrı, kuralın etkin olması durumunda mevcut uyarılarınızın hangisinin kapatılacağını tanımlar.

- **Sil**: varolan bir kuralı siler (ancak tarafından zaten kapatılan uyarıların durumunu değiştirmez).

Tam ayrıntılar ve kullanım örnekleri için [API belgelerine](/rest/api/securitycenter/)bakın. 


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Güvenlik Merkezi 'nde istenmeyen uyarıları otomatik olarak kapatmak için gizleme kuralları açıklanmaktadır.

Azure Defender güvenlik uyarıları hakkında daha fazla bilgi için aşağıdaki sayfalara bakın:

- [Güvenlik uyarıları ve amaç sonlandırma zinciri](alerts-reference.md) -Azure Defender 'dan alabileceğiniz güvenlik uyarılarına yönelik bir başvuru kılavuzu.