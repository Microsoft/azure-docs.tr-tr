---
title: Sözlük terimleri oluşturma, içeri aktarma ve dışarı aktarma
description: Azure purview 'da sözlük koşullarını oluşturma, içeri aktarma ve dışarı aktarma hakkında bilgi edinin.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/02/2021
ms.openlocfilehash: 6796a53ac05e8db606b29447a8792fa509c03d7e
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284114"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>Sözlük terimleri oluşturma, içeri aktarma ve dışarı aktarma

Bu makalede, Azure purview Veri Kataloğu 'nda bir iş sözlüğü terimi oluşturma ve. csv dosyalarını kullanarak sözlük koşullarını içeri aktarma ve dışarı aktarma işlemlerinin nasıl yapılacağı açıklanır.

## <a name="create-a-new-term"></a>Yeni bir terim oluşturun

Yeni bir sözlük terimi oluşturmak için aşağıdaki adımları uygulayın:

1. Giriş sayfasında sol gezinti bölmesinde, terim listesi sayfasına gitmek için sözlük simgesini seçin.

2. **Sözlük terimleri** sayfasında **+ Yeni terim**' i seçin. **Sistem varsayılan** şablonu seçili olarak bir sayfa açılır. Sözlük terimi oluşturmak istediğiniz şablonu seçin ve **devam**' ı seçin.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="Yeni terim oluşturma ekranının ekran görüntüsü." border="true":::

3. Yeni teriminizi katalogda benzersiz olması gereken bir ad verin. Terim adı büyük/küçük harfe duyarlıdır, yani katalogda **örnek** ve **örnek** adlı bir terime sahip olabilirsiniz.

4. Bir **tanım** ekleyin.

5. Terimin **durumunu** ayarlayın. Yeni şartlar varsayılan **taslak** durumundadır.

   :::image type="content" source="media/how-to-create-import-export-glossary/overview-tab.png" alt-text="Durum seçeneklerinin ekran görüntüsü.":::

   Bu durum işaretçileri, terimiyle ilişkili meta verileridir. Şu anda her bir dönemde aşağıdaki durumu ayarlayabilirsiniz:

   - **Taslak**: Bu terim henüz resmi olarak uygulanmadı.
   - **Onaylandı**: Bu terim resmi/standart/onaylanmış olur.
   - **Süresi dolduğunda**: Bu terim artık kullanılmamalıdır.
   - **Uyarı**: Bu terimin ilgilenilmesi gerekiyor.

6. **Kaynak** ve **kısaltma** ekleyin. Terim hiyerarşinin bir parçasıysa, Genel Bakış sekmesinde üst terimleri **üst** kısmına ekleyebilirsiniz.

7. **Eş anlamlıları** ve ilgili **Koşulları** ilgili sekmeye ekleyin.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="Yeni terim > Ilgili sekmenin ekran görüntüsü." border="true":::

8. İsteğe bağlı olarak, terimlerinize uzmanlar ve ödüller eklemek için **kişiler** sekmesini seçin.

9. Teriminizi oluşturmak için **Oluştur** ' u seçin.

## <a name="import-terms-into-the-glossary"></a>Koşulları sözlüğe aktar

Azure purview Veri Kataloğu, koşullarınızı sözlükte içeri aktarmanız için bir Template. csv dosyası sağlar.

Terimleri katalogda içeri aktarabilirsiniz. Dosyadaki yinelenen terimlerin üzerine yazılacak.

Terim adlarının büyük/küçük harfe duyarlı olduğuna dikkat edin. Örneğin, `Sample` ve `saMple` her ikisi de aynı sözlükte bulunabilir.

### <a name="to-import-terms-follow-these-steps"></a>Terimleri içeri aktarmak için şu adımları izleyin

1. **Sözlük terimleri** sayfasında, **Koşulları içeri aktar**' ı seçin.

2. Şablon terimi sayfası açılır. Terim şablonunu türüyle eşleştirin. İçeri aktarmak istediğiniz CSV.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="Sözlük terimleri sayfasının, terimleri Içeri aktarma düğmesinin ekran görüntüsü.":::

3. CSV şablonunu indirin ve eklemek istediğiniz terimlerinizi girmek için kullanın.

   > [!Important]
   > Sistem yalnızca şablonda kullanılabilir olan sütunların içeri aktarılmasını destekler. "Sistem varsayılanı" şablonunun tüm varsayılan öznitelikleri olacaktır.
   > Ancak, özel terim şablonlarının, şablonda tanımlanmış olan ve ek özel özniteliklerin dışında, özel terim şablonları olacaktır. Bu nedenle,. CSV dosyası, seçilen terim şablonuna bağlı olarak hem toplam sütun sayısından hem de sütun adlarından farklıdır. Karşıya yükledikten sonra sorunu gidermek için dosyayı da gözden geçirebilirsiniz.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="Sözlük terimleri sayfasının ekran görüntüsü, Içeri aktarma için dosya ' yı seçin.":::

4. . Csv dosyanızı doldurmayı tamamladıktan sonra, içeri aktarılacak dosyanızı seçin ve ardından **Tamam**' ı seçin.

5. Sistem, dosyayı karşıya yükler ve tüm terimleri kataloğunuza ekler.
 
   > [!Important]
   > Kullanıcılar ve uzmanlar için e-posta adresi, AAD grubundan kullanıcının birincil adresi olmalıdır. Alternatif e-posta, Kullanıcı asıl adı ve AAD olmayan e-postalar henüz desteklenmiyor. 

## <a name="export-terms-from-glossary-with-custom-attributes"></a>Özel özniteliklere sahip sözlükte terimleri dışarı aktarma

Seçili şartlar aynı terim şablonuna ait olduğu sürece, Sözlükteki koşulları dışarı aktarabilirsiniz.

1. Sözlükte olduğunuzda, varsayılan olarak **dışarı aktar** düğmesi devre dışıdır. Dışarı aktarmak istediğiniz terimleri seçtikten sonra, seçili şartlar aynı şablona aitse **dışarı aktar** düğmesi etkinleştirilir.

2. Seçili koşulları indirmek için **dışarı aktar** ' ı seçin.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-export.png" lightbox="media/how-to-create-import-export-glossary/select-term-template-for-export.png" alt-text="Sözlük terimleri sayfasının ekran görüntüsü, dışarı aktarma için dosya ' yı seçin.":::

   > [!Important]
   > Bir hiyerarşideki şartlar farklı terim şablonlarına aitse, bunları farklı olarak bölmeniz gerekir. İçeri aktarma için CSV dosyaları. Ayrıca, bir terimin üst öğesini güncelleştirmek, içeri aktarma işlemi kullanılarak Şu anda desteklenmiyor.

## <a name="next-steps"></a>Sonraki adımlar

Öğreticiyi izleyin: daha fazla bilgi edinmek için [Sözlük terimleri oluşturun ve içeri aktarın](tutorial-import-create-glossary-terms.md) .
