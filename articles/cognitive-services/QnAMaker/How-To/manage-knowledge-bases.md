---
title: Bilgi temellerini yönetme-Soru-Cevap Oluşturma
description: Soru-Cevap Oluşturma, Bilgi Bankası ayarları ve içeriğine erişim sağlayarak bilgi tabanlarınızı yönetmenizi sağlar.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: c7bb79cbd67ba2fb151641ddeeb31b54e399b906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96938265"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Bilgi Bankası oluşturma ve ayarları yönetme

Soru-Cevap Oluşturma Bilgi Bankası ayarları ve veri kaynaklarına erişim sağlayarak bilgi tabanlarınızı yönetmenizi sağlar.

## <a name="prerequisites"></a>Önkoşullar

> [!div class="checklist"]
> * Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/cognitive-services/).
> * Azure portal oluşturulan [soru-cevap oluşturma kaynağı](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) . Kaynağı oluştururken seçtiğiniz Azure Active Directory KIMLIĞI, aboneliğiniz, QnA kaynak adınızı unutmayın.

## <a name="create-a-knowledge-base"></a>Bilgi bankası oluşturma

1. Azure kimlik bilgilerinizle [QnAMaker.ai](https://QnAMaker.ai) portalında oturum açın.

1. Soru-Cevap Oluşturma portalında **Bilgi Bankası oluştur**' u seçin.

1. Soru-Cevap Oluşturma kaynağınız zaten varsa **Oluştur** sayfasında **1. adımı** atlayın.

    Kaynağı henüz oluşturmadıysanız **QnA hizmeti oluştur**' u seçin. Aboneliğinizde bir Soru-Cevap Oluşturma hizmeti ayarlamak için [Azure portala](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) yönlendirilirsiniz. Kaynağı oluştururken seçtiğiniz Azure Active Directory KIMLIĞI, aboneliğiniz, QnA kaynak adınızı unutmayın.

    Azure portal kaynağı oluşturmayı tamamladıktan sonra, Soru-Cevap Oluşturma portalına geri dönün, tarayıcı sayfasını yenileyin ve **2. adıma** geçin.

1. **Adım 3**' te, etkin dizin, abonelik, hizmet (kaynak) ve hizmette oluşturulan tüm bilgi tabanlarının dilini seçin.

   ![Soru-Cevap Oluşturma Hizmeti Bilgi Bankası seçme ekran görüntüsü](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. **Adım 3**' te bilgi tabanınızı adlandırın `My Sample QnA KB` .

1. **4. adımda** ayarları aşağıdaki tabloyla yapılandırın:

    |Ayar|Değer|
    |--|--|
    |**URL 'Ler,. PDF veya. docx dosyalarından Çoklu açma ayıklamasını etkinleştirin.**|İşaretli|
    |**Varsayılan yanıt metni**| `Quickstart - default answer not found.`|
    |**+ URL Ekle**|`https://azure.microsoft.com/en-us/support/faq/`|
    |**Günlük konuşma**|**Profesyonel** seçin|

1. **5. adımda**, **KB 'nizi oluştur**' u seçin.

    Ayıklama işlemi birkaç dakika sürer ve belgeyi okuyup soruları ve yanıtları belirler.

    Bilgi Bankası Soru-Cevap Oluşturma başarıyla oluşturulduktan sonra **Bilgi Bankası** sayfası açılır. Bilgi Bankası 'nın içeriğini bu sayfada düzenleyebilirsiniz.

## <a name="edit-knowledge-base"></a>Bilgi bankasını düzenleme

1.  Üst gezinti çubuğunda **bilgi tabanlarımı** seçin.

       Oluşturduğunuz veya paylaştığınız tüm hizmetleri, **son değiştirilme** tarihinin azalan sırasına göre görebilirsiniz.

       ![Bilgi tabanlarım](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Üzerinde düzenleme yapmak için belirli bir Bilgi Bankası seçin.

1.  **Ayarlar**'ı seçin. Aşağıdaki liste değiştirebileceğiniz alanları içerir.

       |Hedef|Eylem|
       |--|--|
       |URL Ekle|Bilgi bankasını **Yönet-> ' + URL Ekle '** bağlantısına tıklayarak Bilgi Bankası 'na yenı bir SSS içeriği eklemek Için yeni URL 'ler ekleyebilirsiniz.|
       |URL 'YI Sil|Sil simgesini seçerek çöp kutusu ' nu seçerek mevcut URL 'Leri silebilirsiniz.|
       |İçeriği Yenile|Bilgi Bankalarınızın mevcut URL 'lerin en son içeriğini gezinmesi istiyorsanız **Yenile** onay kutusunu seçin. Bu işlem, Bilgi Bankası 'nı en son URL içeriğiyle bir kez güncelleştirir. Bu, güncelleştirmelerin düzenli bir zamanlamasını ayarlamadır.|
       |Dosya ekle|Bilgi bankasını **Yönet**' i seçip, ardından **+ Dosya Ekle** ' yi seçerek bir bilgi tabanının parçası olmak üzere desteklenen bir dosya belgesi ekleyebilirsiniz|
    |İçeri Aktar|Ayrıca, **bilgi tabanı al** düğmesini seçerek mevcut bilgi bankasını içeri aktarabilirsiniz. |
    |Güncelleştir|Bilgi bankasını güncelleştirme, Bilgi Bankası ile ilişkili Soru-Cevap Oluşturma Hizmeti oluştururken kullanılan **Yönetim fiyatlandırma katmanına** bağlıdır. Ayrıca, gerekirse yönetim katmanını Azure portal de güncelleştirebilirsiniz.

  1. Bilgi bankasında değişiklik yapmayı tamamladıktan sonra, değişiklikleri kalıcı hale getirmek için sayfanın sağ üst köşesindeki **Kaydet ve eğit** ' i seçin.

       ![Kaydet ve eğitme](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >**Kaydet ve eğitme** seçmeden önce sayfadan ayrılırsanız tüm değişiklikler kaybedilir.



## <a name="manage-large-knowledge-bases"></a>Büyük bilgi bankaslarını yönetme

* **Veri kaynağı grupları**: qnas, ayıklandıkları veri kaynağına göre gruplandırılır. Veri kaynağını genişletebilir veya daraltabilirsiniz.

    ![Veri kaynağı sorularını ve yanıtlarını daraltmak ve genişletmek için Soru-Cevap Oluşturma veri kaynağı çubuğunu kullanın](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Bilgi Bankası ara**: Bilgi Bankası tablosunun en üstündeki metin kutusuna yazarak bilgi bankasında arama yapabilirsiniz. Soru, cevap veya meta veri içeriğini aramak için ENTER ' a tıklayın. Arama filtresini kaldırmak için X simgesine tıklayın.

    ![Görünümü yalnızca filtre eşleme öğelerine düşürmek için soruların ve yanıtların üzerindeki Soru-Cevap Oluşturma arama kutusunu kullanın](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Sayfalandırma**: büyük bilgi bankaslarını yönetmek için veri kaynaklarından hızlıca geçiş yapın

    ![Soruların ve yanıtların sayfalarında gezinmek için soruların ve yanıtların üzerinde Soru-Cevap Oluşturma sayfalandırma özelliklerini kullanın](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Bilgi temellerini silme

Bilgi Bankası (KB) silindiğinde kalıcı bir işlemdir. Geri alınamaz. Bilgi Bankası 'nı silmeden önce, Bilgi Bankası 'nı Soru-Cevap Oluşturma portalının **Ayarlar** sayfasından dışarı aktarmanız gerekir.

Bilgi bankaınızı ortak çalışanlarla paylaşıyorsanız,] (işbirliği-bilgi-base.md) ve ardından bu dosyayı silerseniz, herkes KB 'ye erişimi kaybeder.

## <a name="next-steps"></a>Sonraki adımlar

Bir kaynaktaki tüm bilgi tabanlarının [dilini yönetme](../index.yml) hakkında bilgi edinin.

* QnA çiftlerini Düzenle
* Soru-Cevap Oluşturma tarafından kullanılan Azure kaynaklarını yönetme