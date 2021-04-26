---
title: EDIFACT iletilerinin kodunu çözme
description: EDI doğrulama ve Enterprise Integration Pack Azure Logic Apps için EDIOLGU ileti kod çözücüsüyle bildirimler oluşturma
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b36641677dbf36402c7f578b9b1887c52f441afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000020"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Enterprise Integration Pack ile Azure Logic Apps için EDIOLGU iletilerinin kodunu çözün

Kod Çözme EDIFACT ileti bağlayıcısıyla EDI ve iş ortağına özgü özellikleri doğrulayabilir, değişimleri işlem kümelerine ayırabilir veya değişimlerin tamamını koruyabilir ve yapılan işlemler için onaylar oluşturabilirsiniz. Bu bağlayıcıyı kullanmak için bunu mantıksal uygulamanızdaki tetikleyicilerden birine eklemeniz gerekir.

## <a name="before-you-start"></a>Başlamadan önce

İhtiyacınız olan öğeler aşağıda verilmiştir:

* Bir Azure hesabı; [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturabilirsiniz
* Zaten tanımlanmış ve Azure aboneliğinizle ilişkilendirilen bir [tümleştirme hesabı](logic-apps-enterprise-integration-create-integration-account.md) . Kod çözme EDIOLGU ileti bağlayıcısını kullanmak için bir tümleştirme hesabınız olmalıdır. 
* Tümleştirme hesabınızda zaten tanımlanmış olan en az iki [iş ortağı](logic-apps-enterprise-integration-partners.md)
* Tümleştirme hesabınızda zaten tanımlanmış olan bir [Ediolgu sözleşmesi](logic-apps-enterprise-integration-edifact.md)

## <a name="decode-edifact-messages"></a>EDIFACT iletilerinin kodunu çözme

> [!IMPORTANT]
> EDIOLGU Bağlayıcısı yalnızca UTF-8 karakterlerini destekler.
> Çıktılarınız beklenmeyen karakterler içeriyorsa, EDIOLGU iletilerinizin UTF-8 karakter kümesini kullanıp kullandığından emin olun. 

1. [Mantıksal uygulama oluşturun](quickstart-create-first-logic-app-workflow.md).

2. Kod çözme EDIOLGU ileti bağlayıcısının tetikleyicisi yok, bu nedenle bir Istek tetikleyicisi gibi mantıksal uygulamanızı başlatmak için bir tetikleyici eklemeniz gerekir. Mantıksal uygulama tasarımcısında bir tetikleyici ekleyin ve sonra mantıksal uygulamanıza bir eylem ekleyin.

3. Arama kutusuna filtreniz olarak "EDIOLGU" yazın. **EDIOLGU Iletisinin kodunu çöz**' ü seçin.
   
    ![EDIOLGU ara](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Daha önce tümleştirme hesabınızla bağlantı oluşturmadıysanız, bu bağlantıyı şimdi oluşturmanız istenir. Bağlantınızı adlandırın ve bağlamak istediğiniz tümleştirme hesabını seçin.
   
    ![Tümleştirme hesabı oluştur](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Yıldız işareti olan özellikler gereklidir.

    | Özellik | Ayrıntılar |
    | --- | --- |
    | Bağlantı adı * |Bağlantınız için bir ad girin. |
    | Tümleştirme hesabı * |Tümleştirme hesabınız için bir ad girin. Tümleştirme hesabınızın ve mantıksal uygulamanızın aynı Azure konumunda olduğundan emin olun. |

4. Bağlantınızı oluşturmayı bitirmeyi bitirdiğinizde **Oluştur**' u seçin. Bağlantı ayrıntılarınız Şu örneğe benzer görünmelidir:

    ![Tümleştirme hesabı ayrıntıları](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Bağlantınız oluşturulduktan sonra, bu örnekte gösterildiği gibi, kodunun çözülmesi için EDIOLGU düz dosya iletisini seçin.

    ![Tümleştirme hesabı bağlantısı oluşturuldu](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Örnek:

    ![Kod çözme için EDIOLGU düz dosya iletisini seçin](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT kod çözücü ayrıntıları

Kod çözme EDIOLGU Bağlayıcısı şu görevleri gerçekleştirir: 

* Zarfı, ticaret ortağı sözleşmesine karşı doğrular.
* , Gönderici niteleyicisi & tanımlayıcı ve alıcı niteleyicisi & tanımlayıcısı ile eşleştirerek sözleşmeyi çözer.
* Değişim, anlaşmanın alma ayarları yapılandırmasına göre birden fazla işlem olduğunda, bir değişimi birden çok işlem içine böler.
* Değişimi ayrıştırır.
* EDI ve ortağa özgü özellikleri doğrular, örneğin:
  * Değişim zarfı yapısının doğrulanması
  * Zarfın denetim şemasına karşı şema doğrulaması
  * İleti şemasına karşı işlem kümesi veri öğeleri için şema doğrulaması
  * EDI doğrulaması işlem kümesi veri öğelerinde gerçekleştirildi
* Değişim, Grup ve işlem kümesi denetim numaralarının tekrarlanmadığını doğrular (yapılandırıldıysa) 
  * Daha önce alınan karşılıklı değişikliklere karşı değişim denetim numarasını denetler. 
  * Grup denetim numarasını, değişim içindeki diğer grup denetim numaralarına karşı denetler. 
  * İşlem kümesi denetim numarasını bu gruptaki diğer işlem kümesi denetim numaralarına karşı denetler.
* Değişimi işlem kümelerine böler veya tüm değişimi korur:
  * Değişimi işlem kümeleri olarak Böl-hata durumunda işlem kümelerini askıya al: değişimi işlem kümelerine böler ve her işlem kümesini ayrıştırır. 
  EDIOLGU kod çözme eylemi yalnızca doğrulama başarısız olan işlem kümelerini çıkış yapar `badMessages` ve kalan işlem kümelerini olarak verir `goodMessages` .
  * Değişimi işlem kümeleri olarak Böl-hata durumunda değişimi askıya al: değişimi işlem kümelerine böler ve her işlem kümesini ayrıştırır. 
  Değişim başarısız doğrulamasında bir veya daha fazla işlem kümesi, EDIOLGU kod çözme eylemi bu değişim içindeki tüm işlem kümelerini olarak çıktı `badMessages` .
  * Değiş tokuş etme-işlem kümelerini askıya alma hata durumunda: değişimi koruma ve toplu tüm değişimi işleme. 
  EDIOLGU kod çözme eylemi yalnızca doğrulama başarısız olan işlem kümelerini çıkış yapar `badMessages` ve kalan işlem kümelerini olarak verir `goodMessages` .
  * Değişimi koru-hata durumunda değişimi askıya al: değişimi koruma ve toplu tüm değişimi işleme. 
  Değişim başarısız doğrulamasında bir veya daha fazla işlem kümesi, EDIOLGU kod çözme eylemi bu değişim içindeki tüm işlem kümelerini olarak çıktı `badMessages` .
* Teknik bir (denetim) ve/veya Işlevsel onay (yapılandırıldıysa) oluşturur.
  * Teknik bir bildirim veya CONSI ACK, alınan tüm değiş tokuş için sözdizimsel bir denetim sonucunu raporlar.
  * Bir işlevsel bildirim alınan bir değişimi veya grubu kabul eder veya reddeder

## <a name="view-swagger-file"></a>Swagger dosyasını görüntüle
EDIOLGU bağlayıcısının Swagger ayrıntılarını görüntülemek için, bkz. [ediolgu](/connectors/edifact/).

## <a name="next-steps"></a>Sonraki adımlar
[Enterprise Integration Pack hakkında daha fazla bilgi edinin](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack hakkında bilgi edinin") 

