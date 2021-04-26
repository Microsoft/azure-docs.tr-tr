---
title: "Öğretici: Azure purview 'da Sözlük terimleri oluşturma ve içeri aktarma (Önizleme)"
description: Bu öğreticide, Sözlük terimleri oluşturma, bir varlığa Sözlük terimleri ekleme ve sözlük koşullarını içeri aktarma açıklanır.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: b240806b934cae54810cf9dfe1a6c1f369cede7e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587519"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Öğretici: Azure purview 'da Sözlük terimleri oluşturma ve içeri aktarma (Önizleme)

> [!IMPORTANT]
> Azure takip görünümü şu anda ÖNIZLEME aşamasındadır. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) , Beta, önizleme veya başka bir şekilde genel kullanıma sunulmayan Azure özelliklerine uygulanan ek yasal koşulları içerir.

Sözlük, kataloğunuzu sürdürmek ve düzenlemek için önemli bir araçtır. Yeni terimleri tanımlayarak veya bir terim listesi içeri aktararak ve sonra bu koşulları varlıklarınıza uygulayarak sözlüğlerinizi derleyebilirsiniz.

Bu öğretici, Azure purview kullanarak bir veri yönetiminde veri yönetimini yönetme temellerini öğrentiğinizde *beş bölümlü bir öğretici serisinin 5. bölümüdür* . Bu öğretici, [5. Bölüm: Azure purview (Önizleme) içinde kaynak kümelerini, ayrıntıları, şemaları ve sınıflandırmaları keşfetmeye](tutorial-schemas-and-classifications.md)yönelik bu öğreticide oluşturulur.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Sözlük terimleri oluşturun.
> * Bir varlığa Sözlük terimleri ekleyin.
> * Sözlük koşullarını içeri aktarın.

## <a name="prerequisites"></a>Önkoşullar

* Tüm [öğretici: Azure purview (Önizleme) içinde kaynak kümelerini, ayrıntıları, şemaları ve sınıflandırmaları araştırma](tutorial-schemas-and-classifications.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-glossary-terms"></a>Sözlük terimleri oluşturma

Sözlükte iş veya teknik koşullar oluşturabilirsiniz. Ayrıca, bunlara hüküm uygulayarak varlıklara açıklama ekleyebilirsiniz.

**Sözlük terimi** sayfasındaki en yaygın alanlardan bazılarının özeti aşağıda verilmiştir:

* **Durum**: terime bir durum atayın (**taslak**, **onaylanan**, **süresi bitmiş** veya **Uyarı**).

* **Tanım**: terimin tanımını girin.

* **Kısaltma**: her bir sözcüğün ilk harflerini kullanarak, terimin kısaltılmış bir sürümünü girin.

* **Eş anlamlılar**: aynı veya benzer tanımlara sahip diğer terimleri seçin.

* **Ilgili terimler**: Bu konuyla ilgili olan, ancak farklı tanımlara sahip olan sözlükte diğer terimleri seçin. Örnek olarak, bir iş terimi, bir kod adı veya terimiyle ilişkilendirilmesi gereken diğer şartlar ile ilgili teknik şartlar verilebilir.

Aşağıdaki adımları izleyerek bir sözlük terimi oluşturun:

1. Azure portal Azure purview kaynağına gidin ve **purview Studio 'Yu aç**' ı seçin. Otomatik olarak purview Studio 'nun giriş sayfasına yönlendirilirsiniz.

1. Sol **bölmeden Sözlük ' i seçerek** **Sözlük terimleri** sayfasını açın.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Sözlük terimleri sayfasını gösteren ekran görüntüsü.":::

1. **Yeni dönem**  >  **sistemi varsayılan**  >  **devam et**' i seçin.

1. **Yeni dönem** sayfasının **genel bakış** sekmesinde yeni dönem için bir **ad** girin: *finans*.

1. **Tanımı** girin: *Bu terim contoso Inc için finansal bilgileri temsil eder.*

1. **Durum** açılan listesinde, **Onaylandı**' yı seçin.

1. İşiniz bittiğinde **Oluştur**' u seçin.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Yeni bir sözlük teriminin nasıl oluşturulacağını gösteren ekran görüntüsü.":::

## <a name="add-glossary-terms-to-an-asset"></a>Bir varlığa Sözlük terimleri ekleme

1. Bir varlık bulmak için [Bu öğretici serisinin 1. bölümünde](tutorial-scan-data.md) öğrendiğiniz adımları kullanın. Örneğin, **{N}. csv Contoso_CashFlow_**.

1. Varlık ayrıntıları sayfasında, **Düzenle**' yi seçin.

1. **Genel bakış** sekmesindeki **Sözlük terimleri** açılan listesinde **finans**' ı seçin ve ardından **Kaydet**' i seçin.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Bir varlığa nasıl sözlük terimi ekleneceğini gösteren ekran görüntüsü.":::

1. **Genel bakış** sekmesindeki **Sözlük terimleri** bölümüne gidin ve *Mali* dönemin kıymete uygulandığını unutmayın.

## <a name="import-glossary-terms"></a>Sözlük koşullarını içeri aktar

Terimleri toplu olarak içeri aktarmak veya mevcut koşulları güncelleştirmek için, sözlükte önceden doldurulmuş bir şablonu karşıya yükleyin.

Bu yordamda, sözlük koşullarını bir. csv dosyası aracılığıyla içeri aktarırsınız:

1. [Bu öğretici serisinin 1. bölümünde](tutorial-scan-data.md)indirdiğiniz Başlatıcı Kit 'in bir parçası olan *StarterKitTerms.csv* adlı dosyayı depoladığınız yere göz önünde bulabilirsiniz.

   Bu dosya, verilerinize uygun olan önceden doldurulmuş terimlerin bir listesini içerir.

 > [!Important]
   > İçindeki ödüller ve uzmanlar için e-posta adresi. CSV dosyası, AAD grubundan kullanıcının birincil adresi olmalıdır. Alternatif e-posta, Kullanıcı asıl adı ve AAD olmayan e-postalar henüz desteklenmiyor. E-posta adreslerini, kuruluşunuzdaki AAD birincil adresi ile değiştirmeniz gerekir.

1. İçeri aktarmaya başlamak için **Sözlük**' i seçin ve ardından **Koşulları içeri aktar**' ı seçin.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Sözlük koşullarının nasıl içeri aktarılacağını gösteren ekran görüntüsü.":::

1. **Terimleri Içeri aktar** sayfasında, **sistem varsayılanı**' nı seçin ve ardından **devam**' ı seçin.

1. **Araştır**' ı seçin, *StarterKitTerms.csv* Indirdiğiniz konuma gidin ve **Aç**' ı seçin.

1. Koşulları içeri aktarmaya başlamak için **Tamam ' ı** seçin.

   İçeri aktarma işlemi tamamlandıktan sonra, yeni Sözlük terimleri **Sözlük terimleri** sayfasında görüntülenir.

1. Nasıl tanımlandığına bakmak için, içeri aktarılan yeni terimlerin her birini görüntüleyin.

## <a name="create-custom-term-templates"></a>Özel terim şablonları oluşturma

Bu bölümde özel özniteliklerle özel bir terim şablonu oluşturmayı ve bir şablon CSV dosyası kullanarak verileri içeri aktarmayı öğreneceksiniz.

**Sözlük**  >  **yönetme terimi şablonlar**  >  **sistemi**' ni seçerek görüntüleyebileceğiniz birkaç mevcut sistem terimi şablonu vardır.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="Sistem terimi şablonları.":::

Yeni bir özel terim şablonu oluşturmak için aşağıdaki adımları uygulayın:

1. Sol taraftaki menüden **Sözlük** ' i seçin.
1. **Terimi Yönet şablonlar**  >  **özel**  >  **Yeni terim şablonu** ' nu seçin

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="Syeni bir özel terim şablonu oluşturun.":::

**Yeni terim şablonu** ekranında, aşağıdaki adımları uygulayın:

1. **Şablon adı** girin: `Sensitivity level` .
1. İstediğiniz açıklamayı girin, örneğin `Indicates the level of sensitivity for this data.`
1. Öznitelik eklemek için bir iletişim kutusu açmak için **+ Yeni özniteliğini** seçin.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="Yeni terim şablonu ekranı başladı.":::

1. **Yeni öznitelik** ekranında aşağıdaki parametreleri girin:

   |Ayar|Önerilen değer|
   |---------|-----------|
   |Öznitelik adı |hassas bilgiler|
   |Alan türü | Tek seçim|
   |Gerekli olarak işaretle | Bu kutuyu işaretleyin.|
   |+ Bir seçenek ekleyin | İki seçenek ekleyin. "Evet" ve "Hayır".|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="Yeni bir öznitelik ekleyin.":::

1. Diğer bir özniteliği ada sahip başka bir öznitelik eklemek için önceki adımları tekrarlayın `can be shared externally` . Her iki öznitelik eklendikten sonra, son olarak **Oluştur**' u seçin.

## <a name="import-terms-from-a-template"></a>Bir şablondan terimleri içeri aktarma

Daha sonra, oluşturduğunuz **duyarlılık düzeyi** şablonunu kullanarak yeni bir terim içeri aktarırsınız. 

1. **Sözlük terimleri** ekranından **içeri aktarma koşulları**' nı seçin.

1. **Terimleri Içeri aktarma** ekranından **duyarlılık düzeyi** ' ni seçin. **Devam**’ı seçin.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Duyarlılık düzeyini seçin.":::

1. **Duyarlılık düzeyi** için terim şablonu, varsayılan sistem özniteliklerinin yanı sıra eklediğiniz yeni öznitelikleri içerir: `can be shared externally` ve `is sensitive information` . Örnek Indir ' i seçin **. CSV** dosyası.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Örnek CSV dosyasını indirin.":::

1. Yeni sözlük terimini düzenlemeniz ve müşteri öznitelikleriyle karşıya yüklemeniz için bir şablon dosyası indirilir. İndirdiğiniz CSV dosyasını açın. Yeni terimleri ve uygun değerlerini CSV dosyasına yeni satırlar olarak ekleyin.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="CSV dosyasında terim oluştur.":::

   Zaten mevcut olmayan **Ilgili şartlar** veya **eş anlamlılar** sütununda listelenen herhangi bir terim, dosya karşıya yüklenirken oluşturulacaktır. Bunlar, **sistem varsayılan** şablonu kullanılarak oluşturulur.

1. Dosyayı karşıya yüklemek için **terimleri Içeri aktarma** ekranına dönün ve Tamam ' ı seçerek  Tamam ' ı seçin **. Karşıya yüklenecek CSV dosyası** kutusu. Açılan iletişim kutusunda dosyanızı seçin ve ardından **Tamam**' ı seçin.

1. Yeni terimler artık **Sözlük terimleri** ekranında listelenmiştir. Listedeki terim adına tıklayarak yeni koşullara ilişkin ayrıntıları görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
>
> * Sözlük terimleri oluşturun.
> * Bir varlığa Sözlük terimleri ekleyin.
> * Sözlük koşullarını içeri aktarın.

Farklı Katalog öngörüleri hakkında bilgi edinmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure Purview'da İçgörüleri Anlama](concept-insights.md)
