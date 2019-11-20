---
title: Power BI çalışma alanı koleksiyonları bir veri kaynağına bağlanma | Microsoft Docs
description: Power BI çalışma alanı koleksiyonları içinde veri kaynağına bağlanmayı öğreneceksiniz.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 1ce666a017c1e5ac25b11fe1c22cdb37746f75df
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669148"
---
# <a name="connect-to-a-data-source"></a>Bir veri kaynağına bağlanma

İle **Power BI çalışma alanı koleksiyonları**, raporları kendi uygulamanıza ekleyebilirsiniz. Power BI raporunu uygulamanıza ekleme, temel alınan verileri raporun bağlandığı **alma** verilerin ya da bir kopyasını **doğrudan bağlanma** kullanarak veri kaynağına için **DirectQuery** .

> [!IMPORTANT]
> Power BI Çalışma Alanı Koleksiyonları kullanım dışı bırakılmıştır ve Haziran 2018'e kadar veya anlaşmanızda belirtilen süre boyunca kullanılabilecektir. Uygulamanızda kesinti yaşanmaması için Power BI Embedded'a geçirmeyi planlamanız önerilir. Verilerinizi Power BI Embedded'a nasıl taşıyacağınızı öğrenmek için bkz. [Power BI Çalışma Alanı Koleksiyonları'nı Power BI Embedded'a geçirme](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

**İçeri Aktarma** ve **DirectQuery**.arasındaki farklar burada yer almaktadır.

| İçeri Aktarma | DirectQuery |
| --- | --- |
| Tablolar, sütunlar *ve veri* içeri aktarılan veya raporun veri kümesine kopyalar. Temel alınan verilerde gerçekleşen değişiklikleri görmek için yenileyin veya içeri aktarma, bir tam, güncel veri kümesini tekrar gerekir. |Yalnızca *tablolar ve sütunlar* içeri aktarılan veya raporun veri kümesine kopyalar. Her zaman en güncel verileri görüntülediğiniz. |

Power BI çalışma alanı koleksiyonları ile DirectQuery ile bulut veri kaynakları kullanabilirsiniz ancak şirket içi veri kaynakları şu anda değil.

> [!NOTE]
> Şirket içi veri ağ geçidi, Power BI çalışma alanı koleksiyonları ile şu anda desteklenmiyor. Başka bir deyişle, şirket içi veri kaynakları ile DirectQuery kullanamazsınız.

## <a name="supported-data-sources"></a>Desteklenen veri kaynakları

**DirectQuery**
* Azure SQL veritabanı
* Azure SQL Veri Ambarı

**İçeri Aktar**

Tüm Power BI Desktop'ta kullanılabilir veri kaynakları kullanarak içeri aktarabilirsiniz. Şunları yapacaksınız **değil** Power BI çalışma alanı koleksiyonları içinde bu verileri yenileyemezsiniz. Değişiklikleri, Power BI çalışma alanı koleksiyonları, PBIX dosyanızı karşıya yüklemek sahip. Kullanılabilir hiçbir ağ geçidi nedeni budur.

## <a name="benefits-of-using-directquery"></a>DirectQuery kullanmanın avantajları

Kullanırken iki adet birincil avantaj olan **DirectQuery**:

* **DirectQuery** tüm verilerin burada Aksi durumda olacağını ilk içeri aktarma seçeneğinin büyük veri kümeleri üzerinde görselleştirmeler oluşturmanıza olanak sağlar.
* Veri değişikliklerini temel alınan veri yenilenmesini gerektirir ve bazı raporlarda, mevcut verilerin görüntülenmesi için gereken büyük veri aktarımları, verileri yeniden içe aktarılması olanaksız hale gerektirebilir. Bunun aksine, **DirectQuery** raporlar her zaman mevcut verileri kullanır.

## <a name="limitations-of-directquery"></a>DirectQuery kullanımıyla ilgili sınırlamalar

Kullanmaya ilişkin birkaç sınırlama vardır **DirectQuery**:

* Tüm tabloları, tek bir veritabanına ait olması gerekir.
* Fazla karmaşık bir sorgudur, bir hata meydana gelir. Hatayı düzeltmek için daha az karmaşık, bu nedenle sorguyu düzenlemelisiniz. Sorgu karmaşık kullanmak yerine, verileri içeri aktarmak gerekirse **DirectQuery**.
* İlişki filtreleme, her iki yön yerine tek bir yönde sınırlıdır.
* Bir sütunun veri türünü değiştiremezsiniz.
* Varsayılan olarak, ölçülerde izin verilen DAX ifadelerine sınırlamaları yerleştirilir. Bkz: [DirectQuery ve ölçüleri](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery ve ölçümler
Temel alınan veri kaynağına gönderilen sorguların kabul edilebilir performansa sahip olmak için sınırlamalar işbirliğiyle uygulanmaktadır. Kullanırken **Power BI Desktop**, Gelişmiş kullanıcıların seçerek bu sınırlamayı atlamayı tercih edebilir **Dosya > Seçenekler ve Ayarlar > Seçenekler**. İçinde **seçenekleri** iletişim kutusunda seçin **DirectQuery**ve seçeneğini **DirectQuery modunda Kısıtlanmamış ölçümlere izin verin**. Bu seçenek belirlendiğinde, bir ölçü için geçerli olan tüm DAX ifadeleri kullanılabilir. Kullanıcılar bilmeniz gerekir; Ancak, veri aktarıldığında iyi performans göstermelerini bazı ifadelerin yavaş neden olabileceğini arka ucuna sorgular zaman içinde kaynak **DirectQuery** modu. 

## <a name="see-also"></a>Ayrıca Bkz.

* [Microsoft Power BI çalışma alanı koleksiyonları ile çalışmaya başlama](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Başka sorunuz mu var? [Power BI Topluluğu'nu deneyin](https://community.powerbi.com/)