---
title: Azure Data Lake Analytics U-SQL çalışma zamanı hatalarında sorun giderme
description: U-SQL çalışma zamanı hatalarıyla ilgili sorunları nasıl giderebileceğinizi öğrenin.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648460"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Çalışma zamanı değişiklikleri nedeniyle U-SQL çalışma zamanı hatalarıyla ilgili sorunları nasıl giderebileceğinizi öğrenin

Derleyici, iyileştirici ve iş Yöneticisi de dahil olmak üzere Azure Data Lake U-SQL çalışma zamanı, U-SQL kodunuzu işler.

## <a name="choosing-your-u-sql-runtime-version"></a>U-SQL çalışma zamanı sürümünüzü seçme

U-SQL işlerini Visual Studio 'dan, ADL SDK veya Azure Data Lake Analytics portalından gönderdiğinizde, işiniz Şu anda kullanılabilir olan varsayılan çalışma zamanını kullanır. U-SQL çalışma zamanının yeni sürümleri düzenli aralıklarla yayımlanır ve hem küçük güncelleştirmeler hem de güvenlik düzeltmelerini içerir.

Ayrıca, özel bir çalışma zamanı sürümü de seçebilirsiniz; Yeni bir güncelleştirmeyi denemek istediğiniz için, bir çalışma zamanının daha eski bir sürümü üzerinde kalmak veya düzenli yeni güncelleştirme için bekleyemez, bildirilen bir sorun için bir düzeltme ile sağlanması gerekir.

> [!CAUTION]
> Varsayılandan farklı bir çalışma zamanı seçilmesi, U-SQL işlerinizi bölmek için olası olur. Bu diğer sürümleri yalnızca test için kullanın.

Nadir durumlarda Microsoft Desteği, çalışma zamanının farklı bir sürümünü hesabınız için varsayılan olarak sabitleyebilir. Lütfen bu PIN 'i mümkün olan en kısa sürede döndürtığınızdan emin olun. Bu sürüme sabitlenmiş olarak kaldıysanız, daha sonraki bir tarihte sona erer.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>İşleri izleme U-SQL çalışma zamanı sürümü

Geçmiş işlerinizin, Visual Studio 'nun iş tarayıcısı veya Azure portal iş geçmişi aracılığıyla hesabınızın iş geçmişinde hangi çalışma zamanı sürümünün kullanıldığını görebilirsiniz.

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. **Tüm Işleri görüntüle**' yi seçin. Hesaptaki tüm etkin ve son tamamlanan işlerin bir listesi görüntülenir.
3. İsteğe bağlı olarak, **zaman aralığı**, **Iş adı**ve **Yazar** değerlerine göre işleri bulmanıza yardımcı olması için **filtre** ' ye tıklayın.
4. Tamamlanan işlerde kullanılan çalışma zamanını görebilirsiniz.

![Geçmiş bir işin çalışma zamanı sürümünü görüntüleme](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Kullanılabilir çalışma zamanı sürümleri zaman içinde değişir. Varsayılan çalışma zamanı her zaman "varsayılan" olarak adlandırılır ve bir süre içinde en az bir önceki çalışma zamanı ve çeşitli nedenlerle özel çalışma zamanları kullanılabilir hale getirir. Açıkça adlandırılmış çalışma zamanları genellikle aşağıdaki biçimi izler (örneğin, değişken parçaları için italik kullanılır ve [] isteğe bağlı bölümleri gösterir):

release_YYYYMMDD_adl_buildno [_değiştirici]

Örneğin, release_20190318_adl_3394512_2, 18 2019 Mart ve release_20190318_adl_3394512_private sürümünün çalışma zamanı sürümünün ikinci 3394512 sürümü aynı sürümün özel bir derlemesini gösterir. Not: Tarih, bu yayın için son iade edildiğinde ve resmi yayın tarihi olması halinde ile ilgilidir.

Şu anda kullanılabilir çalışma zamanı sürümleri aşağıda verilmiştir.

- release_20190318_adl_3394512
- geçerli varsayılan release_20190318_adl_5832669
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>U-SQL çalışma zamanı sürüm sorunlarını giderme

Karşılaşabileceğiniz iki olası çalışma zamanı sürümü sorunu vardır:

1. Bir komut dosyası veya bazı Kullanıcı kodu, bir sürümden bir sonrakine kadar davranış değiştiriyor. Bu tür önemli değişiklikler normalde sürüm notları yayınlanmasıyla zaman önce iletilir. Bu tür bir son değişiklik yaşarsanız, bu davranışı raporlamak için lütfen Microsoft Desteği başvurun (henüz açıklanmamıştır) ve işlerinizi eski çalışma zamanı sürümüne göre gönderebilirsiniz.

2. Hesabınız için sabitlenmiş ve bu çalışma zamanının bir süre sonra kaldırılmasıyla, varsayılan olmayan bir çalışma zamanını açıkça veya örtük olarak kullanıyorsunuz. Eksik çalışma zamanları ile karşılaşırsanız, lütfen betikleri geçerli varsayılan çalışma zamanına göre çalışacak şekilde yükseltin. Ek zamana ihtiyacınız varsa lütfen Microsoft Desteği başvurun

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Data Lake Analytics genel bakış](data-lake-analytics-overview.md)
- [Azure Data Lake Analytics'i Azure portalını kullanarak yönetme](data-lake-analytics-manage-use-portal.md)
- [Azure Data Lake Analytics Azure portal kullanarak işleri izleme](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
