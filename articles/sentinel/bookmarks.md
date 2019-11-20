---
title: Arama yer işaretlerini kullanarak Azure Sentinel 'de arama yaparken verilerin izini sürme | Microsoft Docs
description: Bu makalede, Azure Sentinel arama yer işaretlerinin verileri izlemek için nasıl kullanılacağı açıklanır.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2019
ms.author: cabailey
ms.openlocfilehash: f4714dd09ada01f1adaa9081819e836601599a53
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935303"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Azure Sentinel ile arama sırasında verilerin izini sürme

Tehdit araması genellikle kötü amaçlı davranış kanıtlarını arayan günlük verilerinin dağlarını gözden geçirmeyi gerektirir. Bu işlem sırasında, araştırmacıya olası hipotezleri doğrulama işleminin bir parçası olarak anımsanması, geri yüklemek ve analiz etmek istedikleri olayları bulur ve bir güvenliğin bir bütün hikayesini anlayın.

Azure Sentinel 'de yer alan arama, **Azure Sentinel-logs**' da çalıştırdığınız sorguları koruyarak ve ilgili önemli sonuçlarla birlikte bu işlemi yapmanıza yardımcı olur. Ayrıca, bağlamsal gözlemlerinizi kaydedebilir ve notlar ve etiketler ekleyerek bulgularınızı başvurabilirsiniz. Yer işaretli veriler siz ve ekip arkadaşlarınız tarafından kolay işbirliği için görülebilir.

Yer Işareti olan verilerinizi, **arama bölmesinin** **yer işaretleri** sekmesinde dilediğiniz zaman yeniden ziyaret edebilirsiniz. Geçerli araştırmanıza yönelik belirli verileri hızlı bir şekilde bulmak için filtreleme ve arama seçeneklerini kullanabilirsiniz. Alternatif olarak, yer işaretli verilerinizi, Log Analytics çalışma alanınızdaki **Huntingbookmark** tablosunda doğrudan görüntüleyebilirsiniz. Örnek:

> [!div class="mx-imgBorder"]
> ![, HuntingBookmark tablo](./media/bookmarks/bookmark-table.png) görüntüle

Tablodaki yer imlerini görüntüleme, yer işaretlerinin diğer veri kaynaklarıyla filtrelenmesini, özetlemenizi ve birleştirilmesini sağlar ve bu da eş olmayan kanıtları kolayca arayabilir.

Şu anda önizleme aşamasında, günlüklerinizi yazarken, birkaç tıklamayla bir yer işareti oluşturabilir ve bir olaya yükseltebilir ya da yer işaretini var olan bir olaya ekleyebilirsiniz. Olaylar hakkında daha fazla bilgi için bkz. [öğretici: Azure Sentinel ile olayları araştırma](tutorial-investigate-cases.md). 

Ayrıca Önizleme ' de yer işareti ayrıntılarından **Araştır** ' a tıklayarak, yer işaretlenen verilerinizi görselleştirebilirsiniz. Bu, etkileşimli bir varlık grafik diyagramı ve zaman çizelgesi kullanarak bulgularınızı görüntüleyebilmeniz, araştırmanız ve görsel olarak iletişim kurabileceğiniz araştırma deneyimini başlatır.

## <a name="add-a-bookmark"></a>Yer işareti ekle

1. Azure portal, > **tehdit yönetimi** ** > '** na giderek şüpheli ve anormal **davranışlar için sorgular** çalıştırın.

2. Sorgu arama ayrıntılarından birini seçin ve sağ tarafta **sorgu ayrıntılarını Çalıştır**' ı seçin. 

3. **Sorgu sonuçlarını görüntüle**' yi seçin. Örnek:
    
    > [!div class="mx-imgBorder"]
    > Azure Sentinel arama](./media/bookmarks/new-processes-observed-example.png) ![sorgu sonuçlarını görüntüleme
    
    Bu eylem, sorgu sonuçlarını **Günlükler** bölmesinde açar.

4. Günlük sorgusu sonuçları listesinden, ilgi çekici bulduğunuz bilgileri içeren bir veya daha fazla satır seçmek için onay kutularını kullanın.

5. **Yer Işareti Ekle**öğesini seçin:
    
    > [!div class="mx-imgBorder"]
    > sorguya ![yer işareti ekleme](./media/bookmarks/add-hunting-bookmark.png)

6. Sağ tarafta, **yer Işareti Ekle** bölmesindeki isteğe bağlı olarak, yer işareti adını güncelleştirin, Etiketler ekleyin ve öğe hakkında ne ilginç olduğunu belirlemenize yardımcı olması için notları ekleyin.

7. **Sorgu bilgileri** bölümünde, **Hesap**, **konak**ve **IP adresi** varlık türleri için sorgu sonuçlarından bilgi ayıklamak üzere açılan kutuları kullanın. Bu eylem, seçili varlık türünü sorgu sonucundan belirli bir sütuna eşler. Örnek:
    
    > [!div class="mx-imgBorder"]
    > yer işareti](./media/bookmarks/map-entity-types-bookmark.png) için ![eşleme varlık türleri
    
    Araştırma grafiğinde (Şu anda önizleme aşamasında) yer işaretini görüntülemek için, **Hesap**, **konak**veya **IP adresi**olan en az bir varlık türünü eşlemeniz gerekir. 

5. Değişikliklerinizi uygulamak ve yer işaretini eklemek için **Kaydet** ' e tıklayın. Tüm yer işaretli veriler diğer araştırmacıya paylaşılır ve işbirlikçi araştırma deneyimine yönelik bir ilk adımdır.

 
> [!NOTE]
> Günlük sorgusu sonuçları, bu bölme Azure Sentinel 'ten her açıldığında yer işaretlerini destekler. Örneğin, gezinti çubuğundan **genel** > **günlüklerini** seçer, araştırmalar grafiğinde olay bağlantıları ' nı seçersiniz veya bir olayın tüm ayrıntılarından (Şu anda önizleme AŞAMASıNDA) bir uyarı kimliği seçersiniz. **Günlükler** bölmesi, doğrudan Azure izleyici 'den farklı konumlardan açıldığında yer işaretleri oluşturamazsınız.

## <a name="view-and-update-bookmarks"></a>Yer imlerini görüntüleme ve güncelleştirme 

1. Azure portal, **Sentinel** > **tehdit yönetimi** ** > arama**' ya gidin. 

2. Yer işaretleri listesini görüntülemek için yer **işaretleri** sekmesini seçin.

3. Belirli bir yer işaretini bulmanıza yardımcı olmak için arama kutusunu veya filtre seçeneklerini kullanın.

4. Tek yer imlerini seçin ve sağ Ayrıntılar bölmesinde yer işareti ayrıntılarını görüntüleyin.

5. Değişikliklerinizi gerektiği gibi yapın, bu da otomatik olarak kaydedilir.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Araştırma grafiğinde yer imlerini keşfetme

> [!IMPORTANT]
> Araştırma grafiğinde yer işaretlerinin incelenmesi ve araştırma grafiğinin Şu anda genel önizleme aşamasındadır.
> Bu özellikler, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Azure portal, **Sentinel** > **tehdit yönetimi** ** > arama** > **yer işaretleri** sekmesine gidin ve araştırmak istediğiniz yer işaretlerini veya yer imlerini seçin.

2. Yer işareti ayrıntılarında, en az bir varlığın eşlendiğinden emin olun. Örneğin, **varlıklar**için **IP**, **makine**veya **Hesap**girdilerini görürsünüz.

3. Araştırma grafiğinde yer işaretini görüntülemek için **Araştır** ' a tıklayın.

Araştırma grafiğini kullanma hakkında yönergeler için bkz. [araştırma grafiğini kullanarak derinlemesine](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)bakış.

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Yeni veya mevcut bir olaya yer işaretleri ekleme

> [!IMPORTANT]
> Yeni veya mevcut bir olaya yer işaretlerini ekleme Şu anda genel önizlemededir.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Azure portal, **Sentinel** > **tehdit yönetimi** ** > arama** > **yer işaretleri** sekmesine gidin ve bir olaya eklemek istediğiniz yer işaretlerini veya yer imlerini seçin.

2. Komut çubuğundan **olay eylemlerini (Önizleme)** seçin:
    
    > [!div class="mx-imgBorder"]
    > olaya yer işaretleri eklemek ![](./media/bookmarks/incident-actions.png)

3. Gerektiğinde **Yeni olay oluştur** veya **var olan olaya Ekle**' yi seçin. Ardından:
    
    - Yeni bir olay için: Isteğe bağlı olarak olay ayrıntılarını güncelleştirin ve ardından **Oluştur**' u seçin.
    - Var olan bir olaya bir yer işareti eklemek için: bir olay seçin ve ardından **Ekle**' yi seçin. 

Olay içindeki yer işaretini görüntülemek için: **Sentinel** > **tehdit yönetimi** > **Olaylar** ' a gidin ve yer işaretinizdeki olayı seçin. **Tüm ayrıntıları görüntüle**' yi seçin ve ardından **yer imleri** sekmesini seçin.

> [!TIP]
> Komut çubuğundaki **olay eylemleri (Önizleme)** seçeneğine alternatif olarak, **Yeni olay oluşturma**, **var olan olaya ekleme**ve öğesinden kaldırma seçeneklerini belirlemek için bir veya daha fazla yer işareti için bağlam menüsünü ( **...** ) kullanabilirsiniz.  **olay**. 

## <a name="view-bookmarked-data-in-logs"></a>Günlüklerde yer işaretli verileri görüntüleme

Yer işareti yapılan sorguları, sonuçları veya onların geçmişini görüntülemek için **, > ** **yer işaretleri** sekmesinden yer işaretini seçin ve Ayrıntılar bölmesinde sunulan bağlantıları kullanın: 

- Kaynak sorguyu **Günlükler** bölmesinde görüntülemek için **kaynak sorgusunu görüntüleyin** .

- Güncelleştirmeyi kimin yaptığını, güncelleştirilmiş değerleri ve güncelleştirmenin gerçekleştiği saati içeren tüm yer işareti meta verilerini görmek için **yer işareti günlüklerini görüntüleyin** .

Ayrıca **, > ** **yer işaretleri** sekmesindeki komut çubuğundan **yer işareti günlükleri** ' ni seçerek tüm yer işaretlerine ait ham yer işareti verilerini görüntüleyebilirsiniz:

> [!div class="mx-imgBorder"]
> ![yer Işareti günlükleri](./media/bookmarks/bookmark-logs.png)

Bu görünüm, ilişkili meta verilerle tüm yer işaretlerinizi gösterir. Aradığınız belirli yer işaretinin en son sürümüne filtre uygulamak için [anahtar sözcük sorgu dili](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) (KQL) sorgularını kullanabilirsiniz.

> [!NOTE]
> Bir yer işareti oluşturduğunuz zaman ve **yer işaretleri** sekmesinde görüntülendiğinde önemli bir gecikme (dakika cinsinden ölçülür) olabilir.

## <a name="delete-a-bookmark"></a>Yer işaretini silme
 
1.  Azure portal, **Sentinel** > **tehdit yönetimi** ** >  > ** **yer işaretleri** sekmesine gidin ve silmek istediğiniz yer işaretlerini veya yer imlerini seçin. 

2. Seçimlerinizi sağ tıklatın ve yer işaretini veya yer imlerini silme seçeneğini belirleyin. Örneğin, yalnızca bir yer işareti seçtiyseniz ve iki yer işaretini seçtiyseniz **2 yer Işaretini silerek** **yer işaretini silin** .
    
Yer işaretini silmek yer işaretini **yer** işareti sekmesindeki listeden kaldırır. Log Analytics çalışma alanınızın **Huntingbookmark** tablosu, önceki yer işareti girdilerini içermeye devam edecektir, ancak en son giriş **softdelete** değerini doğru olarak değiştirecek ve eski yer işaretlerinin filtrelemesine olanak sağlar. Bir yer işaretinin silinmesi, diğer yer işaretleri veya uyarılarla ilişkili araştırma deneyiminden hiçbir varlığı kaldırmaz. 


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Sentinel 'de yer işaretleri kullanarak bir araştırma araştırması çalıştırmayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:


- [Tehditler için proaktif araya](hunting.md)
- [Otomatik arama kampanyalarını çalıştırmak için not defterlerini kullanın](notebooks.md)
