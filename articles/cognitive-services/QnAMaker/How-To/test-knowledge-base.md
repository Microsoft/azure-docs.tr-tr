---
title: Bilgi Bankası Soru-Cevap Oluşturma test etme-
description: Soru-Cevap Oluşturma bilgi tabanınızı test etmek, döndürülen yanıtların doğruluğunu artırmak için yinelemeli bir işlemin önemli bir parçasıdır. Bilgi bankasını, düzenleme yapmanıza olanak tanıyan gelişmiş bir sohbet arabiriminden test edebilirsiniz.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 273548ec095ce04772438a2d732b914d80d976cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353162"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Bilgi tabanınızı Soru-Cevap Oluşturma test edin

Soru-Cevap Oluşturma bilgi tabanınızı test etmek, döndürülen yanıtların doğruluğunu artırmak için yinelemeli bir işlemin önemli bir parçasıdır. Bilgi bankasını, düzenleme yapmanıza olanak tanıyan gelişmiş bir sohbet arabiriminden test edebilirsiniz.

## <a name="interactively-test-in-qna-maker-portal"></a>Soru-Cevap Oluşturma portalında etkileşimli test

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

1. Bilgi **tabanlarım** sayfasında adını seçerek bilgi tabanınızı erişin.
1. Test slayt paneli paneline erişmek için uygulamanızın üst panelinde **Test** ' i seçin.
1. Metin kutusuna bir sorgu girin ve ENTER ' u seçin.
1. Bilgi bankasından en iyi eşleşen yanıt yanıt olarak döndürülür.

### <a name="clear-test-panel"></a>Test panelini temizle

Tüm girilen test sorgularını ve sonuçlarını test konsolundan temizlemek için, test bölmesinin sol üst köşesinden **başla** ' yı seçin.

### <a name="close-test-panel"></a>Test panelini kapat

Test panelini kapatmak için, **Test** düğmesini yeniden seçin. Test paneli açıkken Bilgi Bankası içeriğini düzenleyemezsiniz.

### <a name="inspect-score"></a>Puanı İncele

Inceleme panelinde test sonucunun ayrıntılarını inceleyebilirsiniz.

1.  Test slayt paneli açık olduğunda, bu yanıt hakkında daha fazla ayrıntı için **İnceleme** ' yi seçin.

    ![Yanıtları İncele](../media/qnamaker-how-to-test-knowledge-bases/inspect.png)

2.  Inceleme paneli görüntülenir. Panel, en iyi Puanlama amacını ve tanımlanan tüm varlıkları içerir. Panel, seçili utterlik 'in sonucunu gösterir.

### <a name="correct-the-top-scoring-answer"></a>En üst Puanlama yanıtını düzeltin

En üst Puanlama yanıtı yanlışsa, listeden doğru yanıtı seçin ve **Kaydet ve eğit**' i seçin.

![En üst Puanlama yanıtını düzeltin](../media/qnamaker-how-to-test-knowledge-bases/choose-answer.png)

### <a name="add-alternate-questions"></a>Alternatif sorular ekleyin

Belirli bir yanıta bir sorunun alternatif formlarını ekleyebilirsiniz. Metin kutusuna alternatif yanıtları yazın ve eklemek için ENTER ' ı seçin. Güncelleştirmeleri depolamak için **Kaydet ve eğitme '** yi seçin.

![Alternatif sorular ekleyin](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Yeni bir yanıt ekleyin

Eşlenen mevcut yanıtlardan herhangi biri hatalıysa veya Yanıt Bilgi Bankası 'nda yoksa (KB 'de iyi eşleşme bulunamadıysanız) yeni bir yanıt ekleyebilirsiniz.

Yanıtlar listesinin en altında, yeni bir yanıt girmek için metin kutusunu kullanın ve eklemek için ENTER tuşuna basın.

Bu yanıtı kalıcı hale getirmek için **Kaydet ve eğitme** seçeneğini belirleyin. Bilgi tabanıza yeni bir soru-cevap çifti eklenmiştir.

> [!NOTE]
> Bilgi bankasındaki tüm düzenlemeler yalnızca **Kaydet ve eğitme** düğmesine bastığınızda kaydedilir.

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

1. Bilgi **tabanlarım** sayfasında adını seçerek bilgi tabanınızı erişin.
1. Test slayt paneli paneline erişmek için uygulamanızın üst panelinde **Test** ' i seçin. 
1. En üstteki **görüntüleme kısa cevap** üzerinde, varsayılan olarak seçilen bir onay kutusu görürsünüz. Bu seçenek, test panelinizdeki MRC tabanlı yanıt yayma algılamasını etkinleştirmek için kullanılır. 
1. Metin kutusuna bir sorgu girin ve ENTER ' u seçin. 
1. Her sorgu için, yanıt sırasında, daha sonra bilgi tabanında bulunan en iyi eşleşen Yanıt ile birlikte kesin bir yanıt/kısa yanıt varsa, sorgunuz için de kısa bir yanıt alırsınız.
    ![Yönetilen etkin test bölmesi](../media/qnamaker-how-to-test-knowledge-bases/test-pane-with-managed-detail.png)
1. **Kısa yanıtın göster** seçimini kaldırırsanız, yanıt olarak yalnızca bilgi bankasındaki en iyi eşleşen yanıt döndürülür.

### <a name="clear-test-panel"></a>Test panelini temizle

Tüm girilen test sorgularını ve sonuçlarını test konsolundan temizlemek için, test bölmesinin sol üst köşesinden **başla** ' yı seçin.

### <a name="close-test-panel"></a>Test panelini kapat

Test panelini kapatmak için, **Test** düğmesini yeniden seçin. Test paneli açıkken Bilgi Bankası içeriğini düzenleyemezsiniz.

### <a name="inspect-score"></a>Puanı İncele

Inceleme panelinde test sonucunun ayrıntılarını inceleyebilirsiniz.

1.  Test slayt paneli açık olduğunda, bu yanıt hakkında daha fazla ayrıntı için **İnceleme** ' yi seçin.

    ![Yanıt önizlemeyi İncele](../media/qnamaker-how-to-test-knowledge-bases/inspect-with-managed.png)

2.  Inceleme paneli görüntülenir. Panel, en iyi Puanlama amacını ve tanımlanan tüm varlıkları içerir. Panel, seçili utterlik 'in sonucunu gösterir.
3. Bu panelde, algılanan yanıt yayılımının puanı ile birlikte yanıt yollamasının Güvenirlik puanı gösterilmektedir.

### <a name="correct-the-top-scoring-answer"></a>En üst Puanlama yanıtını düzeltin

En üst Puanlama yanıtı yanlışsa, listeden doğru yanıtı seçin ve **Kaydet ve eğit**' i seçin.

![En üst Puanlama yanıtı önizlemesini düzeltin](../media/qnamaker-how-to-test-knowledge-bases/choose-answer-managed.png)

### <a name="add-alternate-questions"></a>Alternatif sorular ekleyin

Belirli bir yanıta bir sorunun alternatif formlarını ekleyebilirsiniz. Metin kutusuna alternatif yanıtları yazın ve eklemek için ENTER ' ı seçin. Güncelleştirmeleri depolamak için **Kaydet ve eğitme '** yi seçin.

![Alternatif soru önizlemesi ekleme](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question-with-managed.png)

### <a name="add-a-new-answer"></a>Yeni bir yanıt ekleyin

Eşlenen mevcut yanıtlardan herhangi biri hatalıysa veya Yanıt Bilgi Bankası 'nda yoksa (KB 'de iyi eşleşme bulunamadıysanız) yeni bir yanıt ekleyebilirsiniz.

Yanıtlar listesinin en altında, yeni bir yanıt girmek için metin kutusunu kullanın ve eklemek için ENTER tuşuna basın.

Bu yanıtı kalıcı hale getirmek için **Kaydet ve eğitme** seçeneğini belirleyin. Bilgi tabanıza yeni bir soru-cevap çifti eklenmiştir.

---

### <a name="test-the-published-knowledge-base"></a>Yayınlanan Bilgi Bankası 'nı test etme

Bilgi Bankası 'nın yayımlanmış sürümünü test bölmesinde test edebilirsiniz. KB 'yi yayımladıktan **sonra yayımlanmış KB kutusunu seçin** ve yayımlanan KB 'den sonuçları almak için bir sorgu gönderin.

![Yayımlanan bir KB 'ye karşı test](../media/qnamaker-how-to-test-knowledge-bases/test-against-published-knowledge-base.png)

## <a name="batch-test-with-tool"></a>Araçla Batch testi

Şunları yapmak istediğinizde Batch test aracını kullanın:

* bir soru kümesinin en iyi yanıtını ve Puanını belirleme
* soru kümesi için beklenen yanıtı doğrulama

### <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Soru-cevap oluşturma bir hizmet oluşturun](../Quickstarts/create-publish-knowledge-base.md) ya da İngilizce dilini kullanan mevcut bir hizmeti kullanın.
* [Çoklu çift örnek `.docx` dosyasını](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) indirin
* [Batch test aracı](https://aka.ms/qnamakerbatchtestingtool)'nı indirin, dosyadan yürütülebilir dosyayı ayıklayın `.zip` .

### <a name="sign-into-qna-maker-portal"></a>Soru-Cevap Oluşturma portalında oturum açın

Soru-Cevap Oluşturma portalında [oturum açın](https://www.qnamaker.ai/) .

### <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Çoklu açma sample.docx dosyasından yeni bir Bilgi Bankası oluşturun

1. Araç çubuğundan **Bilgi Bankası oluştur** ' u seçin.
1. **1. adımı** atlayın, var olan kaynak bilgilerinizi seçmek için **Adım 2** ' ye kadar bir soru-cevap oluşturma kaynağınız olması gerekir:
    * Azure Active Directory KIMLIĞI
    * Azure abonelik adı
    * Azure QnA hizmeti adı
    * Dil-Ingilizce dili
1. `Multi-turn batch test quickstart`Bilgi Bankalarınızın adı olarak adı girin.

1. **4. adımda** ayarları aşağıdaki tabloyla yapılandırın:

    |Ayar|Değer|
    |--|--|
    |**URL 'Ler,. PDF veya. docx dosyalarından Çoklu açma ayıklamasını etkinleştirin.**|İşaretli|
    |**Varsayılan yanıt metni**| `Batch test - default answer not found.`|
    |**+ Dosya Ekle**|Ön koşullarda indirilen `.docx` dosya listesini seçin.|
    |**Günlük konuşma**|**Profesyonel** seçin|

1. **5. adımda**, **KB 'nizi oluştur**' u seçin.

    Oluşturma işlemi tamamlandığında Portal, düzenlenebilir Bilgi Bankası 'nı görüntüler.

### <a name="save-train-and-publish-knowledge-base"></a>Bilgi Bankası 'nı kaydetme, eğitme ve yayımlama

1. Bilgi Bankası 'nı kaydetmek için araç çubuğundan **Kaydet ve eğitme '** yi seçin.
1. Araç çubuğundan **Yayımla** ' yı seçin ve ardından Bilgi Bankası 'nı yayımlamak Için yeniden **Yayımla** ' yı seçin. Yayımlama, bilgi bankasını ortak bir URL uç noktasından sorgular için kullanılabilir hale getirir. Yayımlama tamamlandığında, **Yayımla** sayfasında gösterilen ana bilgisayar URL 'sini ve uç nokta anahtarı bilgilerini kaydedin.

    |Gerekli veriler| Örnek|
    |--|--|
    |Yayınlanan konak|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Yayınlanan anahtar|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (32 karakter dizesi sonra gösteriliyor `Endpoint` )|
    |Uygulama Kimliği|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (36 bir parçası olarak gösterilen karakter dizesi `POST` ) |

### <a name="create-batch-test-file-with-question-ids"></a>Soru kimlikleriyle Batch test dosyası oluşturma

Batch test aracını kullanmak için metin düzenleyici ile adlı bir dosya oluşturun `batch-test-data-1.tsv` . Dosya UTF-8 biçiminde olmalıdır ve bir sekme ile ayrılmış olan aşağıdaki sütunların olması gerekir.

|TSV giriş dosyası alanları|Notlar|Örnek|
|--|--|--|
|Bilgi Bankası KIMLIĞI|Bilgi Bankası KIMLIĞINIZ Yayımla sayfasında bulunur. Tek bir dosyada farklı Bilgi Bankası kimliklerini kullanarak tek bir dosyada aynı hizmette bulunan birçok bilgi bankasını test edin.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (36 bir parçası olarak gösterilen karakter dizesi `POST` ) |
|Soru|Kullanıcının girebileceği soru metni. en fazla 1.000 karakter.|`How do I sign out?`|
|Meta veri etiketleri|isteğe bağlı|`topic:power`biçimini kullanır `key:value`|
|Üst parametre|isteğe bağlı|`25`|
|Beklenen yanıt KIMLIĞI|isteğe bağlı|`13`|

Bu Bilgi Bankası için, dosyaya yalnızca iki gerekli sütunun üç satırını ekleyin. İlk sütun Bilgi Bankası KIMLIĞINIZ ve ikinci sütun aşağıdaki soru listesi olmalıdır:

|Sütun 2-sorular|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Bu sorular bilgi bankasındaki tam bir ifade olup, Güvenirlik puanı olarak 100 döndürmelidir.

Daha sonra, bu sorulara benzer ancak aynı Bilgi Bankası kimliği kullanılarak tam olarak aynı üç satıra sahip olmamak üzere birkaç soru ekleyin:

|Sütun 2-sorular|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Her sütunun yalnızca bir sekme sınırlayıcısıyla ayrıldığından emin olun. Sütun verilerine baştaki veya sondaki boşluklar eklenir ve tür veya boyut yanlış olduğunda programın özel durum oluşturmasına neden olur.

Excel 'de açıldığında, toplu işlem test dosyası aşağıdaki görüntüde gibi görünür. Bilgi Bankası KIMLIĞI `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` güvenlik için değiştirilmiştir. Kendi Batch testiniz için, sütunun Bilgi Bankası KIMLIĞINIZI görüntülediğinden emin olun.

> [!div class="mx-imgBorder"]
> ![Toplu iş testinde. tsv dosyasının ilk sürümünü girin](../media/batch-test/batch-test-1-input.png)

### <a name="test-the-batch-file"></a>Toplu iş dosyasını test etme

Komut satırında aşağıdaki CLı biçimini kullanarak Batch test programı 'nı çalıştırın.

`YOUR-RESOURCE-NAME`Ve `ENDPOINT-KEY` değerlerini hizmet adı ve uç nokta anahtarı için kendi değerlerinizle değiştirin. Bu değerler Soru-Cevap Oluşturma portalındaki **Ayarlar** sayfasında bulunur.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
Test tamamlanır ve `out.tsv` dosyayı oluşturur:

> [!div class="mx-imgBorder"]
> ![Batch testinde. tsv dosyasının ilk sürümünü çıkar](../media/batch-test/batch-test-1-output.png)

Bilgi Bankası KIMLIĞI `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` güvenlik için değiştirilmiştir. Kendi Batch testiniz için, sütun Bilgi Bankası KIMLIĞINIZI görüntüler.

Güvenilir puanın test çıktısı dördüncü sütununda, her bir soru bilgi tabanında göründüğü gibi tam olarak aynı olduğu için, her biri beklenen şekilde 100 puanı döndüren en üstteki üç soruyu gösterir. Son üç soru, söz konusu sorudan yeni bir deyişle, Güvenirlik puanı olarak 100 döndürmez. Hem test hem de kullanıcılarınız için puanı artırmak üzere Bilgi Bankası 'na daha fazla başka soru eklemeniz gerekir.

### <a name="testing-with-the-optional-fields"></a>İsteğe bağlı alanlarla test etme

Biçim ve işlemi anladıktan sonra, sohbet günlüklerinden gibi bir veri kaynağından bilgi tabanınızdan çalıştırmak için bir test dosyası oluşturabilirsiniz.

Veri kaynağı ve işlemi otomatikleştiriltiğinden, doğru değerleri belirleyebilmek için test dosyası farklı ayarlarla birçok kez çalıştırılabilir.

Örneğin, bir sohbet günlüğü varsa ve hangi sohbet günlüğü metninin hangi meta veri alanlarına uygulanacağını öğrenmek istiyorsanız, bir test dosyası oluşturun ve her satır için meta veri alanlarını ayarlayın. Testi çalıştırın, sonra meta verilerle eşleşen satırları gözden geçirin. Genellikle eşleşmeler pozitif olmalıdır, ancak hatalı pozitif sonuçlar için sonuçları gözden geçirmeniz gerekir. Yanlış pozitif değeri, meta verilerle eşleşen ancak metne göre eşleşmeyen bir satırdır.

### <a name="using-optional-fields-in-the-input-batch-test-file"></a>Giriş toplu işlem test dosyasında isteğe bağlı alanlar kullanma

İsteğe bağlı veriler için alan değerlerinin nasıl bulunacağını anlamak için aşağıdaki grafiği kullanın.

|Sütun numarası|İsteğe bağlı sütun|Veri konumu|
|--|--|--|
|3|meta veriler|Mevcut çiftler için mevcut bilgi bankasını dışarı aktarın `key:value` .|
|4|top|Varsayılan değeri `25` önerilir.|
|5|Soru ve yanıt kümesi KIMLIĞI|KIMLIK değerleri için mevcut bilgi bankasını dışarı aktarın. Ayrıca, kimlikler çıkış dosyasında döndürülmediğine dikkat edin.|

### <a name="add-metadata-to-the-knowledge-base"></a>Bilgi Bankası 'na meta veri ekleme

1. QnA portalında, **düzenleme** sayfasında, aşağıdaki sorulara meta verileri ekleyin `topic:power` :

    |Sorular|
    |--|
    |Surface Pro 4 ' ü ücretlendirin|
    |Pil düzeyini denetleme|

    İki QnA çiftinin meta veri kümesi var.

    > [!TIP]
    > Her bir küme için meta verileri ve QnA kimliklerini görmek üzere Bilgi Bankası 'nı dışarı aktarın. **Ayarlar** sayfasını seçin ve ardından dosya olarak **Dışa Aktar** ' ı seçin `.xls` . Bu indirilen dosyayı bulun ve meta veriler ve KIMLIK için Excel gözden geçirimiyle açın.

1. **Kaydet ve eğit**' i seçin, ardından **Yayımla** sayfasını seçin ve ardından **Yayınla** düğmesini seçin. Bu eylemler, değişikliği toplu test için kullanılabilir hale getirir. Bilgi Bankası 'nı **Ayarlar** sayfasından indirin.

    İndirilen dosyanın meta veriler için doğru biçimi ve doğru soru ve yanıt kümesi KIMLIĞI vardır. Sonraki bölümde bu alanları kullan

    > [!div class="mx-imgBorder"]
    > ![Meta verilerle dışarıya aktarılmış bilgi tabanı](../media/batch-test/exported-knowledge-base-with-metadata.png)

### <a name="create-a-second-batch-test"></a>İkinci bir Batch testi oluşturma

Toplu test için iki ana senaryo vardır:
* **Sohbet günlüğü dosyalarını işle** -daha önce görülmeyen bir soru için en iyi yanıtı belirleme-en yaygın durum, bir sohbet bot 'un Kullanıcı soruları gibi, sorguların günlük dosyası olan bir işlemdir. Yalnızca gerekli sütunlarla bir toplu iş dosyası testi oluşturun. Test, her soru için en iyi yanıtı döndürür. Bu, en iyi yanıtın doğru yanıt olduğu anlamına gelmez. Bu testi tamamladıktan sonra doğrulama testine geçin.
* **Doğrulama testi** -beklenen yanıtı doğrulayın. Bu test, toplu iş testinde tüm soruların ve beklenen yanıtları eşleştirmesinin doğrulanmasını gerektirir. Bu, bazı el ile işlem gerektirebilir.

Aşağıdaki yordamda, senaryonun sohbet günlüklerini işlemek için olduğu varsayılmaktadır

1. İsteğe bağlı verileri dahil etmek için yeni bir Batch test dosyası oluşturun `batch-test-data-2.tsv` . Orijinal Batch testi giriş dosyasından altı satır ekleyin, ardından her satır için meta veri, üst ve QnA çifti KIMLIĞINI ekleyin.

    Bilgi Bankası 'nda sohbet günlüklerinden yeni metin denetleme işleminin benzetimini yapmak için, her bir sütunun meta verilerini aynı değere ayarlayın: `topic:power` .

    > [!div class="mx-imgBorder"]
    > ![Batch testinde. tsv dosyasının ikinci sürümünü girin](../media/batch-test/batch-test-2-input.png)

1. Testi yeniden çalıştırın, giriş ve çıkış dosyası adlarını değiştirerek ikinci test olduğunu belirtin.

    > [!div class="mx-imgBorder"]
    > ![Batch testinde. tsv dosyasının ikinci sürümünü çıktı](../media/batch-test/batch-test-2-output.png)

### <a name="test-results-and-an-automated-test-system"></a>Test sonuçları ve otomatikleştirilmiş bir test sistemi

Bu test çıkış dosyası otomatik bir sürekli test işlem hattının parçası olarak ayrıştırılabilir.

Bu özel test çıkışı şu şekilde okunmalıdır: her satır meta verilerle filtrelenmiştir ve her satır bilgi bankasındaki meta verilerle eşleşmediğinden, bu eşleşmeyen satırların varsayılan yanıtı ("KB 'de iyi eşleşme bulunamadı") döndürülür. Eşleşen bu satırlardan, QnA KIMLIĞI ve puanı döndürüldü.

Beklenen yanıt KIMLIĞIYLE eşleşen satır olmadığından tüm satırlar yanlış etiketini döndürdü.

Bu sonuçlarla, sohbet günlüğü uygulayabileceğiniz ve metni her bir satırın sorgusu olarak kullanabileceğiniz şekilde görebilmelisiniz. Veriler hakkında herhangi bir şey bilmeden sonuçlar, daha sonra ilerlemeden daha fazla bilgi elde etmek için size çok fazla bilgi bildirir:

* meta veriler
* QnA KIMLIĞI
* puan

Test için tam bir fikir olan meta verilerle filtreleniyor mu? Evet ve hayır. Test sistemi her meta veri çiftinin test dosyalarını ve meta veri çiftlerine sahip olmayan bir testi oluşturacaktır.

### <a name="clean-up-resources"></a>Kaynakları temizleme

Bilgi bankasını sınamaya devam edemeyecekleriniz, toplu iş dosyası aracını ve test dosyalarını silin.

Bu Bilgi Bankası 'nı kullanmaya devam edemeyecekleriniz, Bilgi Bankası 'nı aşağıdaki adımlarla silin:

1. Soru-Cevap Oluşturma portalında, üst menüden **bilgi tabanlarım** ' ı seçin.
1. Bilgi tabanı listesinde, bu hızlı başlangıçta Bilgi Bankası 'nın satırındaki **Sil** simgesini seçin.

[Araçla Ilgili başvuru belgeleri](../reference-tsv-format-batch-testing.md) şunları içerir:

* aracın komut satırı örneği
* TSV giriş ve çıkışdosyası dosyaları için biçim

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bilgi bankası yayımlama](../quickstarts/create-publish-knowledge-base.md)