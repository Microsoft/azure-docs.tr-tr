---
title: Özel sınıflandırma ve sınıflandırma kuralı oluşturma (Önizleme)
description: Azure purview 'da kuruluşunuzda benzersiz olan veri ve veri türlerini tanımlamak için özel sınıflandırmalar oluşturmayı öğrenin.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/24/2021
ms.openlocfilehash: 7d6baee49250509e50cdeeea8cf8ca6cec5b362d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222183"
---
# <a name="custom-classifications-in-azure-purview"></a>Azure purview 'da özel sınıflandırmalar

Bu makalede, kuruluşunuzda benzersiz olan verilerinizde veri türlerini tanımlamak için nasıl özel sınıflandırmalar oluşturabileceğiniz açıklanır. Ayrıca, verilerinizin tamamında belirtilen verileri bulmanıza olanak sağlayan özel sınıflandırma kurallarının oluşturulmasını açıklar.

## <a name="default-classifications"></a>Varsayılan sınıflandırmalar

Azure purview Veri Kataloğu, verilerinize sahip olabileceğiniz tipik kişisel veri türlerini temsil eden büyük bir varsayılan sınıflandırmalar kümesi sağlar.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="sınıflandırmayı seçin" border="true":::

Ayrıca, varsayılan sınıflandırmaların herhangi biri gereksinimlerinizi karşılamıyorsa özel sınıflandırmalar oluşturabilirsiniz.

## <a name="steps-to-create-a-custom-classification"></a>Özel sınıflandırma oluşturma adımları

Özel bir sınıflandırma oluşturmak için aşağıdaki adımları izleyin:

1. Kataloğunuzda, sol taraftaki menüden **Yönetim Merkezi** ' ni seçin.

2. **Meta veri yönetimi** altında **sınıflandırmalar** ' ı seçin.

3. **+ Yeni** seçeneğini belirleyin

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="Yeni sınıflandırma" border="true":::

Sınıflandırmanızı bir ad ve açıklama verebileceğiniz **Yeni sınıflandırma Ekle** bölmesi açılır. Gibi bir ad boşluğu kuralı kullanmak iyi bir uygulamadır `your company name.classification name` .

Microsoft Sistem sınıflandırmaları, ayrılmış ad alanı altında gruplandırılır `MICROSOFT.` . MICROSOFT bir örnektir **. Devlet. ABD. Sosyal \_ Güvenlik \_ numarası**.

Sınıflandırmanın adı bir harfle başlamalı ve arkasından bir harf, rakam ve nokta (.) veya alt çizgi karakterlerinden oluşmalıdır. Boşluklara izin verilmez. Siz yazarken, UX otomatik olarak kolay bir ad oluşturur. Bu kolay ad, kullanıcıların katalogda bir varlığa uyguladığınızda gördükleri şeydir.

Adı kısa tutmak için, sistem aşağıdaki mantığa göre kolay bir ad oluşturur:

- Ancak, ad alanının son iki bölümü kırpılmakta.

- Büyük küçük harf, her sözcüğün ilk harfinin büyük harfli olması için ayarlanır. Diğer tüm harfler küçük harfe dönüştürülür.

- Tüm alt çizgiler ( \_ ) boşluklarla değiştirilmiştir.

Örnek olarak, sınıflandırmanızı CONTOSO.HR olarak adlandırdıysanız **. ÇALıŞAN \_ kimliği**, kolay ad sistemde **HR. Employee kimliği** olarak depolanır.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

**Tamam**' ı seçin ve yeni sınıflandırmanız sınıflandırma listenize eklenir.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="Özel sınıflandırma" border="true":::

Listedeki sınıflandırmanın seçilmesi, sınıflandırma ayrıntıları sayfasını açar. Burada, sınıflandırmayla ilgili tüm ayrıntıları bulabilirsiniz.

Bu ayrıntılar, kaç örnek olduğunu, biçimsel adı, ilişkili sınıflandırma kurallarını (varsa) ve sahip adını içerir.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="Sınıflandırmayı seçin" border="true":::

## <a name="custom-classification-rules"></a>Özel sınıflandırma kuralları

Katalog hizmeti, belirli veri türlerini otomatik olarak algılamak için tarayıcı tarafından kullanılan bir varsayılan sınıflandırma kuralları kümesi sağlar. Ayrıca, verileriniz genelinde bulma konusunda ilgilendiğiniz diğer veri türlerini algılamak için kendi özel sınıflandırma kurallarınızı ekleyebilirsiniz. Bu özellik, veri Emlak dahilinde veri bulmaya çalışırken güçlü olabilir.

Örnek olarak, \' contoso adlı bir şirketin şirketin tamamında standartlaştırılmış çalışan kimlikleri olduğunu ve bu çalışanın, çalışan \" \" {GUID} çalıştıran bir GUID 'den olduğunu varsayalım. Örneğin, bir çalışan KIMLIĞININ bir örneği gibi görünür `EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55` .

Contoso, özel bir sınıflandırma kuralı oluşturarak tarama sistemini bu kimliklerin örneklerini bulacak şekilde yapılandırabilir. Veri düzeniyle eşleşen bir normal ifade sağlayabilir, bu durumda `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` . İsteğe bağlı olarak, veriler genellikle adını bildiğiniz bir sütunindeyse (örneğin, çalışan \_ kimliği veya ÇalışanNo), taramanın daha doğru olmasını sağlamak için bir sütun deseninin normal ifadesi ekleyebilirler. Örnek bir Regex, çalışan \_ kimliği \| çalışanbir örneğidir.

Tarama sistemi daha sonra bu kuralı, çalışan KIMLIĞI deseninin bulunduğu her örneği tanımlamak üzere sütundaki gerçek verileri ve sütun adını incelemek için kullanabilir.

## <a name="steps-to-create-a-custom-classification-rule"></a>Özel sınıflandırma kuralı oluşturma adımları

Özel bir sınıflandırma kuralı oluşturmak için:

1. Önceki bölümdeki yönergeleri izleyerek özel bir sınıflandırma oluşturun. Bu özel sınıflandırmayı sınıflandırma kuralı yapılandırmasına ekleyerek sistem, sütunda bir eşleşme bulduğunda uygular.

2. **Yönetim Merkezi** simgesini seçin.

3. **Sınıflandırmalar kuralları** bölümünü seçin.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="Sınıflandırma kuralları kutucuğu" border="true":::

4. **Yeni**'yi seçin.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="Yeni sınıflandırma kuralı ekle" border="true":::

5. **Yeni sınıflandırma kuralı** iletişim kutusu açılır. Alanları doldurup bir **normal ifade kuralı** veya **Sözlük kuralı** oluşturup oluşturmayacağınıza karar verin.

   |Alan     |Açıklama  |
   |---------|---------|
   |Ad   |    Gereklidir. Maksimum değer 100 karakterdir.    |
   |Description      |İsteğe bağlı. Maksimum değer 256 karakterdir.    |
   |Sınıflandırma adı    | Gereklidir. Bir eşleşme bulunursa tarayıcıya uygulamayı bildirmek için açılan listeden sınıflandırmanın adını seçin.        |
   |Durum   |  Gereklidir. Seçenekler etkin veya devre dışı. Varsayılan değer etkindir.    |

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="Yeni sınıflandırma kuralı oluştur" border="true":::

### <a name="creating-a-regular-expression-rule"></a>Normal Ifade kuralı oluşturma

1. Bir normal ifade kuralı oluşturuyorsanız, aşağıdaki ekranı görürsünüz. Kuralınız için **Önerilen Regex desenleri oluşturmak** üzere, isteğe bağlı olarak kullanılacak bir dosyayı karşıya yükleyebilirsiniz.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="Yeni Regex kuralı oluştur" border="true":::

1. Önerilen bir Regex desenini oluşturmaya karar verirseniz, bir dosyayı karşıya yükledikten sonra önerilen desenlerden birini seçin ve önerilen veri ve sütun düzenlerini kullanmak için **desenlere Ekle** ' yi seçin. Önerilen desenleri ince ayar veya bir dosyayı karşıya yüklemeden kendi desenlerinizi de yazabilirsiniz.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="Önerilen Regex oluştur" border="true":::

   |Alan     |Açıklama  |
   |---------|---------|
   |Veri kalıbı    |İsteğe bağlı. Veri alanında depolanan verileri temsil eden bir normal ifade. Sınır çok büyük. Önceki örnekte, veri desenleri, sözcük olan bir çalışan KIMLIĞI için test `Employee{GUID}` .  |
   |Sütun kalıbı    |İsteğe bağlı. Eşleştirmek istediğiniz sütun adlarını temsil eden bir normal ifade. Sınır çok büyük. |

1. **Veri deseninin** altında ayarlayabileceğiniz iki eşik vardır:

   - **Ayrı eşleşme eşiği**: tarayıcıdan veri modelini çalıştırmadan önce bir sütunda bulunması gereken ayrı veri değerlerinin toplam sayısı. Önerilen değer 8 ' dir. Bu değer, 2 ile 32 arasında el ile ayarlanabilir. Bu değer, sütunun tarayıcıya doğru bir şekilde sınıflandırmasına yetecek kadar veri içerdiğinden emin olmak için bu değeri gerektirir. Örneğin, 1 değerini içeren birden çok satır içeren bir sütun sınıflandırılmayacaktır. Değer içeren bir satır içeren sütunlar ve satırların geri kalanı null değerleri de sınıflandırılmayacaktır. Birden çok desen belirtirseniz bu değer bunların her biri için geçerlidir.

   - **En düşük eşleşme eşiği**: Bu ayarı, sınıflandırmanın uygulanması için tarayıcı tarafından bulunması gereken bir sütundaki farklı veri değeri eşleştirmelerinin minimum yüzdesini ayarlamak için kullanabilirsiniz. Önerilen değer %60 ' dir. Bu ayarla dikkatli olmanız gerekir. %60 altındaki düzeyi azaltmanız halinde kataloğunuza hatalı pozitif sınıflandırmalar ekleyebilirsiniz. Birden çok veri deseni belirtirseniz bu ayar devre dışıdır ve değer %60 ' de düzeltilir.

1. Şimdi kuralınızı doğrulayabilmeniz ve **oluşturabilirsiniz** .

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/verify-rule.png" alt-text="Oluşturmadan önce kuralı doğrula" border="true":::

1. Varlıklarınıza Etiketler uygulayacağını doğrulamak için oluşturma işlemini tamamlamadan önce sınıflandırma kuralını test edin. Kuraldaki sınıflandırmalar, bir taramada olduğu gibi karşıya yüklenen örnek verilere uygulanır. Bu, tüm sistem sınıflandırmalarının ve özel sınıflandırmanın dosyanızdaki verilerle eşleştirileceği anlamına gelir.

   Giriş dosyaları sınırlandırılmış dosyalar (CSV, PSV, SSV, TSV), JSON veya XML içeriği içerebilir. İçerik, giriş dosyasının dosya uzantısına göre ayrıştırılacaktır. Sınırlandırılmış veriler, belirtilen türlerden biriyle eşleşen bir dosya uzantısına sahip olabilir. Örneğin, TSV verileri MySampleData.csv adlı bir dosyada bulunabilir. Sınırlandırılmış içerik de en az 3 sütun içermelidir.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-screen.png" alt-text="Oluşturmadan önce test kuralı" border="true":::

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-uploaded-file-result-screen.png" alt-text="Test dosyasını karşıya yükledikten sonra uygulanan sınıflandırmaları görüntüle" border="true":::

### <a name="creating-a-dictionary-rule"></a>Sözlük kuralı oluşturma

1. Sözlük kuralı oluşturuyorsanız, aşağıdaki ekranı görürsünüz. Oluşturmakta olduğunuz sınıflandırmanın tüm olası değerlerini içeren bir dosyayı tek bir sütunda karşıya yükleyin.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="Sözlük kuralı oluştur" border="true":::

1. Sözlük oluşturulduktan sonra, farklı eşleştirme ve en düşük eşleşme eşiklerini ayarlayabilir ve kuralı gönderebilirsiniz.

- **Ayrı eşleşme eşiği**: tarayıcıdan veri modelini çalıştırmadan önce bir sütunda bulunması gereken ayrı veri değerlerinin toplam sayısı. DISTINCT eşleşme eşiğinin, model eşleştirme ile hiçbir şey yok, ancak bu, bir model eşleştirme için önkoşul. Önerilen değer 8 ' dir. Bu değer, 2 ile 32 arasında el ile ayarlanabilir. Bu değer, sütunun tarayıcıya doğru bir şekilde sınıflandırmasına yetecek kadar veri içerdiğinden emin olmak için bu değeri gerektirir. Örneğin, 1 değerini içeren birden çok satır içeren bir sütun sınıflandırılmayacaktır. Değer içeren bir satır içeren sütunlar ve satırların geri kalanı null değerleri de sınıflandırılmayacaktır. Birden çok desen belirtirseniz bu değer bunların her biri için geçerlidir.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Dictionary-Generated onay işaretiyle sözlük kuralı oluşturma." border="true":::

## <a name="next-steps"></a>Sonraki adımlar

Sınıflandırma kuralınızı oluşturduğunuza göre, taramanın tarama sırasında kuralı kullanması için bir tarama kuralı kümesine eklenmeye hazırdır. Daha fazla bilgi için bkz. [tarama kuralı kümesi oluşturma](create-a-scan-rule-set.md).
