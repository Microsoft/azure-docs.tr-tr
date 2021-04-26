---
title: Azure Stream Analytics Azure Data Lake Storage Gen 1 çıkışı
description: Bu makalede, Azure Stream Analytics için çıkış seçeneği olarak Azure Data Lake Storage Gen 1 açıklanmaktadır.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 629a154c89ad301a3e200b1d6cd04c62057d9959
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016550"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Azure Stream Analytics Azure Data Lake Storage Gen 1 çıkışı

Stream Analytics, [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) çıkışlarını destekler. Azure Data Lake Storage, büyük veri analizi iş yükleri için kurumsal çapta, hiper ölçekli bir depodur. İşletimsel ve araştırmacı analizler için herhangi bir boyut, tür ve Alım hızıyla ilgili verileri depolamak üzere Data Lake Storage kullanabilirsiniz. Stream Analytics Data Lake Storage erişmek için yetkilendirilmiş olması gerekir.

Azure Çin 21Vianet ve Azure Almanya (T-Systems Uluslararası) bölgelerinde Stream Analytics çıkış Azure Data Lake Storage yok.

## <a name="output-configuration"></a>Çıkış yapılandırması

Aşağıdaki tabloda Data Lake Storage Gen 1 çıktı yapılandırmak için özellik adları ve açıklamaları listelenmektedir.

| Özellik adı | Description |
| --- | --- |
| Çıktı diğer adı | Sorgu çıkışını Data Lake Store yönlendirmek için sorgularda kullanılan kolay bir ad. |
| Abonelik | Azure Data Lake Storage hesabınızı içeren abonelik. |
| Hesap adı | Çıktımız Data Lake Store hesabının adı. Aboneliğinizde kullanılabilir Data Lake Store hesapların bir açılır listesi sunulur. |
| Yol ön eki stili | Belirtilen Data Lake Store hesabı içinde dosyalarınızı yazmak için kullanılan dosya yolu. {Date} ve {Time} değişkenlerinin bir veya daha fazla örneğini belirtebilirsiniz:<br /><ul><li>Örnek 1: Klasör1/logs/{Date}/{Time}</li><li>Örnek 2: Klasör1/logs/{Date}</li></ul><br />Oluşturulan klasör yapısının zaman damgası UTC ve yerel saate göre değil.<br /><br />Dosya yolu deseninin sonunda eğik çizgi (/) yoksa, dosya yolundaki son model bir dosya adı öneki olarak değerlendirilir. <br /><br />Yeni dosyalar şu koşullarda oluşturulur:<ul><li>Çıkış şemasında değişiklik</li><li>Bir işin dış veya iç yeniden başlatılması</li></ul> |
| Tarih biçimi | İsteğe bağlı. Ön ek yolunda Tarih belirteci kullanılıyorsa, dosyalarınızın düzenlendiği tarih biçimini seçebilirsiniz. Örnek: YYYY/AA/GG |
|Saat biçimi | İsteğe bağlı. Ön ek yolunda zaman belirteci kullanılıyorsa, dosyalarınızın düzenlendiği saat biçimini belirtin. Şu anda desteklenen tek değer HH 'dir. |
| Olay serileştirme biçimi | Çıkış verileri için serileştirme biçimi. JSON, CSV ve avro desteklenir.|
| Encoding | CSV veya JSON biçimi kullanıyorsanız, bir kodlama belirtilmesi gerekir. Şu anda desteklenen tek kodlama biçimi UTF-8 ' i destekler.|
| Sınırlayıcı | Yalnızca CSV serileştirme için geçerlidir. Stream Analytics CSV verilerini seri hale getirmek için bir dizi ortak sınırlayıcıları destekler. Desteklenen değerler virgül, noktalı virgül, boşluk, sekme ve dikey çubuklardır.|
| Biçimlendir | Yalnızca JSON serileştirme için geçerlidir. **Satır ayrımı** , çıktının her bir JSON nesnesine yeni bir satırla ayrılmış şekilde biçimlendirildiğini belirtir. **Satır ayrılmış**' i SEÇERSENIZ, JSON tek seferde bir nesne okur. Tüm içerik geçerli bir JSON değil.  **Dizi** , çıktının JSON nesneleri dizisi olarak biçimlendirildiğini belirtir. Bu dizi yalnızca iş durdurulduğunda veya Stream Analytics bir sonraki zaman penceresine taşındığında kapatılır. Genel olarak, çıkış dosyası hala üzerine yazılırken özel bir işleme gerektirmediğinden, hat ayrımı olan JSON kullanılması tercih edilir.|
| Kimlik doğrulaması modu | [Yönetilen kimlik](stream-analytics-managed-identities-adls.md) (Önizleme) veya kullanıcı belirteci kullanarak Data Lake Storage hesabınıza erişim yetkisi verebilirsiniz. Erişim izni verdiğinizde, Kullanıcı hesabı parolasını değiştirerek, bu iş için Data Lake Storage çıkışını silerek veya Stream Analytics işini silerek erişimi iptal edebilirsiniz. |

## <a name="partitioning"></a>Bölümleme

Bölüm anahtarı için, yol öneki düzeninde {Date} ve {Time} belirteçlerini kullanın. YYYY/AA/GG, GG/AA/YYYY veya AA-GG-YYYY gibi bir tarih biçimi seçin. Saat biçimi için SS kullanın. Çıktı yazıcılarının sayısı, [tam paralelleştirilebilir sorgular](stream-analytics-scale-jobs.md)için giriş bölümlemesini izler.

## <a name="output-batch-size"></a>Çıkış toplu iş boyutu

En büyük ileti boyutu için bkz. [Data Lake Storage sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits). Toplu iş boyutunu iyileştirmek için yazma işlemi başına en fazla 4 MB kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Yönetilen kimlikler kullanarak Azure Data Lake Storage 1. Stream Analytics kimlik doğrulama (Önizleme)](stream-analytics-managed-identities-adls.md)
* [Hızlı başlangıç: Azure portalını kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-portal.md)