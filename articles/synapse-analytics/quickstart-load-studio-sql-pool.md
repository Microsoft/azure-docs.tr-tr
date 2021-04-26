---
title: 'Hızlı başlangıç: adanmış bir SQL havuzuyla verileri toplu yükleme'
description: Azure SYNAPSE Analytics 'te özel bir SQL havuzunda verileri toplu olarak yüklemek için SYNAPSE Studio 'Yu kullanın.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 838138fb6ca6f64b4296b54a81bc2764c3f1399c
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567919"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>Hızlı başlangıç: SYNAPSE Studio ile toplu yükleme

SYNAPSE Studio 'daki toplu yükleme Sihirbazı ile veri yükleme kolaydır. SYNAPSE Studio, Azure SYNAPSE Analytics 'in bir özelliğidir. Toplu Yükleme Sihirbazı, verileri ayrılmış bir SQL havuzunda toplu olarak yüklemek için [Copy ifadesiyle](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) bir T-SQL betiği oluşturma sürecinde size rehberlik eder. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Toplu yükleme sihirbazına giriş noktaları

SYNAPSE Studio içinde aşağıdaki alana sağ tıklayarak verileri toplu olarak yükleyebilirsiniz: çalışma alanınıza bağlı bir Azure depolama hesabındaki dosya veya klasör.

![Depolama hesabından bir dosya veya klasöre sağ tıklanarak görüntülenen ekran görüntüsü.](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Önkoşullar

- Sihirbaz, kimlik doğrulaması için Azure Active Directory (Azure AD) doğrudan geçişli bir kopyalama ekstresi oluşturur. Azure AD kullanıcılarınız, Azure Data Lake Storage 2. hesabı için en azından Depolama Blobu veri katılımcısı Azure rolüyle çalışma alanına [erişebilmelidir](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) . 

- ' Ye yüklenecek yeni bir tablo oluşturuyorsanız [Copy ifadesini kullanmak](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) ve tablo oluşturmak için gerekli izinlere sahip olmanız gerekir.

- Data Lake Storage 2. hesabıyla ilişkili bağlı hizmetin yüklenecek *dosya veya klasöre erişimi olmalıdır* . Örneğin, bağlı hizmet için kimlik doğrulama mekanizması yönetilen bir kimlik ise, çalışma alanının yönetilen kimliği, depolama hesabında en az Depolama Blobu veri okuyucusu iznine sahip olmalıdır.

- Çalışma alanınızda bir sanal ağ etkinse, kaynak veriler ve hata dosyası konumu için Data Lake Storage 2. hesabının bağlı hizmetleriyle ilişkili tümleşik çalışma zamanının etkileşimli yazma özelliğinin etkinleştirildiğinden emin olun. Etkileşimli yazma, yeniden şema algılama, kaynak dosya içeriklerinin önizlemesi ve sihirbazın içindeki Data Lake Storage 2. depolama hesaplarına göz atmak için gereklidir.

## <a name="steps"></a>Adımlar

1. **Kaynak depolama konumu** panelinde, depolama hesabını ve yükleme yaptığınız dosyayı veya klasörü seçin. Sihirbaz, kaynak alanları dosyadan uygun hedef SQL veri türlerine eşleme de dahil olmak üzere Parquet dosyalarını ve sınırlandırılmış metin (CSV) dosyalarını algılamaya çalışır. 

   ![Kaynak konumu seçmeyi gösteren ekran görüntüsü.](./sql/media/bulk-load/bulk-load-source-location.png)

2. Toplu yükleme işlemi sırasında reddedilen satırlar olduğunda hata ayarlarınıza dahil olmak üzere dosya biçimi ayarlarını seçin. Ayrıca, kopya ifadesinin dosya biçimi ayarlarını yapılandırmanıza yardımcı olmak üzere dosyayı nasıl ayrıştırarak olduğunu görmek için **Verileri Önizle** ' yi de seçebilirsiniz. KOPYA ifadesinin dosyayı güncelleştirilmiş ayarla nasıl ayrıştıracağını görmek için, bir dosya biçimi ayarını her değiştirdiğiniz zaman **Önizleme verilerini** seçin.

   ![Verilerin önizlemesini gösteren ekran görüntüsü.](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - Toplu Yükleme Sihirbazı, çok karakterli alan sonlandırıcılarını içeren verilerin önizlemesini desteklemez. Çok karakterli bir alan Sonlandırıcı belirttiğinizde, sihirbaz verileri tek bir sütun içinde önizleyebilir. 
   > - **Sütun adlarını** çıkar ' ı seçtiğinizde, toplu yükleme Sihirbazı **ilk satır** alanı tarafından belirtilen ilk satırdaki sütun adlarını ayrıştırır. Toplu Yükleme Sihirbazı, `FIRSTROW` Bu üst bilgi satırını yoksaymak IÇIN Copy deyimindeki değeri otomatik olarak 1 artırır. 
   > - COPY ifadesinde çok karakterli satır sonlandırıcıları belirtmek desteklenir. Ancak toplu yükleme Sihirbazı bunu desteklemez ve hata oluşturur.

3. Yükün mevcut bir tablo veya yeni bir tablo için olup olmayacağını dahil olmak üzere, yüklemek için kullandığınız adanmış SQL havuzunu seçin.
   ![Hedef konum seçmeyi gösteren ekran görüntüsü.](./sql/media/bulk-load/bulk-load-target-location.png)
4. Uygun sütun eşlemesine sahip olduğunuzdan emin olmak için **Sütun eşlemeyi Yapılandır** ' ı seçin. **Sütun adlarını çıkarsandıysanız**, sütun adları otomatik olarak algılanır. Yeni tablolar için, sütun eşlemesini yapılandırmak, hedef sütun veri türlerini güncelleştirmek açısından önemlidir.

   ![Sütun eşlemeyi yapılandırmayı gösteren ekran görüntüsü.](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. **Betiği aç**' ı seçin. Bir T-SQL betiği, Data Lake 'ınızdan yüklenecek COPY ifadesiyle birlikte oluşturulur.
   ![SQL betiğini açmayı gösteren ekran görüntüsü.](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Sonraki adımlar

- KOPYALAMA özellikleri hakkında daha fazla bilgi için [kopyalama ekstresi](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) makalesine bakın.
- Ayıklama, dönüştürme ve yükleme (ETL) işlemini kullanma hakkında bilgi edinmek için [veri yüklemeye genel bakış](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) makalesini denetleyin.
