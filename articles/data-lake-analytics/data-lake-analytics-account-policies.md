---
title: Azure Data Lake Analytics hesap Ilkelerini yönetme
description: En yüksek Avustralya ve en fazla iş gibi Data Lake Analytics hesabının kullanımını denetlemek için hesap ilkelerini nasıl kullanacağınızı öğrenin.
services: data-lake-analytics
ms.service: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 4689714073047e383a53a04bd0069a8a27afdf9d
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966443"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Hesap Ilkelerini kullanarak Azure Data Lake Analytics yönetme

Hesap ilkeleri, kaynakların Azure Data Lake Analytics bir hesabının nasıl kullanıldığını denetlemenize yardımcı olur. Bu ilkeler Azure Data Lake Analytics kullanmanın maliyetini denetlemenize olanak tanır. Örneğin, bu ilkelerle, hesabın kaç au eşzamanlı olarak kullanılabileceğini sınırlayarak beklenmeyen maliyet artışlarını engelleyebilirsiniz.

## <a name="account-level-policies"></a>Hesap düzeyindeki ilkeler

Bu ilkeler bir Data Lake Analytics hesabındaki tüm işler için geçerlidir.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Data Lake Analytics hesapta maksimum AU sayısı
İlke, Data Lake Analytics hesabınızın kullanabileceği toplam analiz birimi sayısını (Avustralya) denetler. Varsayılan olarak, değer 250 olarak ayarlanır. Örneğin, bu değer 250 au olarak ayarlandıysa, BT 'ye atanan 250 au ile çalışan bir işiniz veya 25 au ile çalışan 10 işi kullanabilirsiniz. Gönderilen ek işler, çalışan işler bitene kadar sıraya alınır. İşleri çalıştırmak bittiğinde, sıraya alınan işlerin çalışması için Avustralya serbest bırakılır.

Data Lake Analytics hesabınız için AU sayısını değiştirmek için:

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. **Sınırlar ve ilkeler**' e tıklayın.
3. **Maksimum**au altında, bir değer seçmek için kaydırıcıyı taşıyın veya metin kutusuna değeri girin. 
4. **Kaydet** düğmesine tıklayın.

> [!NOTE]
> Varsayılan (250) Avustralya 'dan daha fazlasına ihtiyacınız varsa, portalda destek isteği göndermek için **Yardım + Destek** ' e tıklayın. Data Lake Analytics hesabınızda kullanılabilen AU sayısı artırılabilir.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Aynı anda çalışabilecek en fazla iş sayısı
Bu ilke, kaç işin aynı anda çalışacağını sınırlandırır. Varsayılan olarak, bu değer 20 olarak ayarlanır. Data Lake Analytics au varsa, yeni işler, çalışan işlerin toplam sayısı bu ilkenin değerine ulaşıncaya kadar hemen çalışacak şekilde zamanlanır. Aynı anda çalışabilecek en fazla iş sayısına ulaştığınızda, bir veya daha fazla çalışan iş tamamlanana kadar sonraki işler öncelik sırasına göre sıraya alınır (kullanılabilir au 'ye bağlı olarak).

Aynı anda çalışabilecek işlerin sayısını değiştirmek için:

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. **Sınırlar ve ilkeler**' e tıklayın.
3. **En fazla çalışan Iş sayısı**altında, kaydırıcıyı bir değer seçmek için taşıyın veya metin kutusuna değeri girin. 
4. **Kaydet** düğmesine tıklayın.

> [!NOTE]
> Varsayılan (20) iş sayısından fazlasını çalıştırmanız gerekiyorsa, portalda destek isteği göndermek için **Yardım + Destek** ' e tıklayın. Data Lake Analytics hesabınızda aynı anda çalışabilecek işlerin sayısı artırılabilir.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>İş meta verileri ve kaynaklarının saklanacağı süre 
Kullanıcılarınız U-SQL işleri çalıştırdıklarında, Data Lake Analytics hizmeti tüm ilgili dosyaları korur. Bu dosyalar U-SQL betiğini, U-SQL betiğine başvuruda bulunulan DLL dosyalarını, derlenmiş kaynakları ve istatistikleri içerir. Dosyalar varsayılan Azure Data Lake Storage hesabının/System/klasöründedir. Bu ilke, bu kaynakların otomatik olarak silinmeden önce ne kadar süreyle depolandığını denetler (varsayılan değer 30 gündür). Bu dosyaları hata ayıklama için kullanabilir ve gelecekte yeniden çalıştıracağımız işlerin performans ayarları için kullanabilirsiniz.

İş meta verilerinin ve kaynaklarının saklanacağı süreyi değiştirmek için:

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. **Sınırlar ve ilkeler**' e tıklayın.
3. **Iş sorgularını**bekletmek için, kaydırıcıyı hareket ettirmek için bir değer seçin veya metin kutusuna değeri girin.  
4. **Kaydet** düğmesine tıklayın.

## <a name="job-level-policies"></a>İş düzeyi ilkeleri

İş düzeyi ilkeleri, gönderdikleri işlere bireysel kullanıcıların (veya belirli güvenlik gruplarının üyeleri) ayarlayabilecekleri maksimum au ve en yüksek öncelik düzeyini denetlemenizi sağlar. Bu ilke, kullanıcılar tarafından tahakkuk eden maliyetleri denetlemenizi sağlar. Aynı zamanda, zamanlanan işlerin aynı Data Lake Analytics hesabında çalışan yüksek öncelikli üretim işlerinde sahip olabileceği etkiyi denetlemenizi de sağlar.

Data Lake Analytics, iş düzeyinde ayarlayabileceğiniz iki ilkeye sahiptir:

* **İş başına AU sınırı**: kullanıcılar yalnızca bu sayıda au 'ya kadar olan işleri gönderebilirler. Varsayılan olarak, bu sınır hesap için maksimum AU sınırı ile aynıdır.
* **Öncelik**: kullanıcılar yalnızca önceliği bu değere eşit veya daha düşük olan işleri gönderebilir. Daha yüksek bir sayı, daha düşük bir öncelik gösterir. Varsayılan olarak, bu sınır en yüksek öncelik olan 1 ' e ayarlanır.

Her hesapta ayarlanmış bir varsayılan ilke vardır. Varsayılan ilke, hesabın tüm kullanıcıları için geçerlidir. Belirli kullanıcılar ve gruplar için ek ilkeler oluşturabilirsiniz. 

> [!NOTE]
> Hesap düzeyindeki ilkeler ve iş düzeyi ilkeleri aynı anda geçerlidir.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Belirli bir kullanıcı veya grup için ilke ekleme

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. **Sınırlar ve ilkeler**' e tıklayın.
3. **Iş gönderme sınırları**altında, **ilke Ekle** düğmesine tıklayın. Ardından, aşağıdaki ayarları seçin veya girin:
    1. **Işlem Ilkesi adı**: ilkenin amacını hatırlatmak için bir ilke adı girin.
    2. **Kullanıcı veya grup seçin**: Bu ilkenin uygulandığı Kullanıcı veya grubu seçin.
    3. **Iş au sınırını ayarla**: seçili kullanıcı veya grup için GEÇERLI olan au sınırını ayarlayın.
    4. **Öncelik sınırını ayarla**: seçili kullanıcı veya grup için geçerli olan öncelik sınırını ayarlayın.

4. **Tamam**’a tıklayın.

5. Yeni ilke, **varsayılan** Ilke tablosunda **iş gönderme sınırları**altında listelenir. 

### <a name="delete-or-edit-an-existing-policy"></a>Var olan bir ilkeyi silme veya düzenleme

1. Azure portal Data Lake Analytics hesabınıza gidin.
2. **Sınırlar ve ilkeler**' e tıklayın.
3. **Iş gönderme sınırları**' nın altında, düzenlemek istediğiniz ilkeyi bulun.
4.  **Silme** ve **düzenleme** seçeneklerini görmek için, tablonun en sağdaki sütununda `...`' ye tıklayın.

## <a name="additional-resources-for-job-policies"></a>İş ilkeleri için ek kaynaklar
* [İlkeye genel bakış blog gönderisi](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Hesap düzeyindeki ilkeler blog gönderisi](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [İş düzeyi ilkeleri blog gönderisi](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Lake Analytics genel bakış](data-lake-analytics-overview.md)
* [Azure portal kullanarak Data Lake Analytics kullanmaya başlayın](data-lake-analytics-get-started-portal.md)
* [Azure PowerShell kullanarak Azure Data Lake Analytics yönetme](data-lake-analytics-manage-use-powershell.md)