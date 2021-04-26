---
title: Şirket içi SQL Server Integration Services (SSIS) işlerini Azure Data Factory geçirin
description: Bu makalede, SQL Server Integration Services (SSIS) işlerinin SQL Server Management Studio kullanarak Azure Data Factory işlem hattına/etkinliklere/tetikleyicilere nasıl geçirileceği açıklanır.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: ec10abfd6f2fc221a9e86203b2faa0d173d67675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100379599"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>SSMS ile SQL Server Agent işleri ADF 'ye geçirme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Şirket içi SQL Server Integration Services (SSIS) iş YÜKLERINI obsıs 'e taşırken](scenario-ssis-migration-overview.md), SSIS paketleri geçirildikten sonra, SQL Server Agent işlerin iş adımı türü SQL Server Integration Services paketi Azure Data Factory (ADF) işlem hattı/etkinlikleri/SQL Server Management Studio (SSMS) **SSIS iş Geçiş Sihirbazı** aracılığıyla Zamanlayıcı tetiklemeleri aracılığıyla toplu geçiş yapabilirsiniz.

Genel olarak, geçerli iş adımı türlerindeki seçili SQL Aracısı işleri için **SSIS Iş Geçiş Sihirbazı** şunları yapabilir:

- Şirket içi SSIS paketi konumunu paketlerin geçirildiği yere eşleyin ve bu, SSIS ADF 'de erişilebilir.
    > [!NOTE]
    > Dosya sisteminin paket konumu yalnızca desteklenir.
- geçerli iş adımları ile geçerli işleri aşağıdaki şekilde ilgili ADF kaynaklarına geçirin:

|SQL Aracısı iş nesnesi  |ADF kaynağı  |Notlar|
|---------|---------|---------|
|SQL Aracısı işi|konfigüre     |İşlem hattının adı *Için \<job name> oluşturulur*. <br> <br> Yerleşik aracı işleri geçerli değildir: <li> SSIS sunucu bakım Işi <li> syspolicy_purge_history <li> collection_set_ * <li> mdw_purge_data_ * <li> sysutility_ *|
|SSIS iş adımı|SSIS paketi yürütme etkinliği|<li> Etkinliğin adı olacaktır \<step name> . <li> İş adımında kullanılan ara sunucu hesabı, bu etkinliğin Windows kimlik doğrulaması olarak geçirilir. <li> İş adımında tanımlanan *32 bit çalışma zamanı kullanımı* hariç *yürütme seçenekleri* , geçiş sırasında yok sayılır. <li> İş adımında tanımlanan *doğrulama* , geçiş sırasında yok sayılacak.|
|schedule      |zamanlama tetikleyicisi        |Zamanlama tetikleyicisinin adı *Için \<schedule name> oluşturulur*. <br> <br> SQL Aracısı iş zamanlamasında aşağıdaki seçenekler, geçişte yok sayılacak: <li> İkinci düzey Aralık. <li> *SQL Server Agent başladığında otomatik olarak Başlat* <li> *CPU 'Ları her boşta duruma geldiğinde Başlat* <li> *hafta içi* ve *hafta sonu günü*<time zone> <br> Aşağıda, SQL Aracısı iş zamanlaması ADF zamanlama tetikleyicisine geçirildikten sonra farklılıklar verilmiştir: <li> ADF zamanlama tetikleyicisi sonraki çalıştırma, öncül tarafından tetiklenen çalıştırmanın yürütme durumundan bağımsızdır. <li> ADF zamanlama tetikleyicisi yineleme yapılandırması, SQL Aracısı işinde günlük sıklığından farklıdır.|

- Yerel çıkış klasöründe Azure Resource Manager (ARM) şablonları oluşturun ve doğrudan veya sonraki bir sürüme el ile dağıtın. ADF Kaynak Yöneticisi şablonları hakkında daha fazla bilgi için bkz. [Microsoft. DataFactory kaynak türleri](/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Önkoşullar

Bu makalede açıklanan özellik SQL Server Management Studio sürüm 18,5 veya üstünü gerektirir. SSMS 'nin en son sürümünü almak için bkz. [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="migrate-ssis-jobs-to-adf"></a>SSIS işlerini ADF 'ye geçirme

1. SSMS 'de, Nesne Gezgini SQL Server Agent ' i seçin, Işler ' i seçin, ardından sağ tıklayıp **SSIS IŞLERINI ADF 'ye geçir**' i seçin.
![Ekran görüntüsü SQL Server Management Studio Nesne Gezgini gösterir, burada Işler ' i seçebilir ve sonra s s I Işleri bir D F 'ye geçirebilirsiniz.](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Azure 'Da oturum açın, Azure aboneliği, Data Factory ve Integration Runtime seçin. ' Nin geçirilmesi gereken SSIS dosya sistemi paketlerine sahip olması durumunda, paket konumu eşleme adımında kullanılan Azure depolama isteğe bağlıdır.
![Menü](media/how-to-migrate-ssis-job-ssms/step1.png)

1. SSIS işlerinde SSIS paketlerinin ve yapılandırma dosyalarının yollarını, geçirilen Işlem hatlarının erişebileceği hedef yollarla eşleyin. Bu eşleme adımında şunları yapabilirsiniz:

    1. Bir kaynak klasör seçip **eşleme Ekle**' ye tıklayın.
    1. Kaynak klasör yolunu güncelleştirme. Geçerli yollar, paketlerin klasör yolları veya üst klasör yollarıdır.
    1. Hedef klasör yolunu güncelleştirme. Varsayılan, 1. adımda seçilen varsayılan depolama hesabının göreli yoludur.
    1. Seçili eşlemeyi **silme eşlemesi** aracılığıyla silin.
![Ekran görüntüsü, eşleme ekleyebileceğiniz, harita s s paket ve yapılandırma yolları sayfasını gösterir. ](media/how-to-migrate-ssis-job-ssms/step2.png)
 ![ Ekran görüntüsünde, kaynak ve hedef klasör yollarını güncelleştirebileceğiniz harita s s paketi ve yapılandırma yolları görüntülenir.](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Geçirilecek geçerli işleri seçin ve ilgili *yürütülen SSIS paketi etkinliğinin* ayarlarını yapılandırın.

    - *Varsayılan ayar*, varsayılan olarak seçilen tüm adımlar için geçerlidir. Her özellik hakkında daha fazla bilgi için, paket konumu *dosya sistemi (paket)* olduğunda [SSIS paketi yürütme etkinliğinin](how-to-invoke-ssis-package-ssis-activity.md) *Ayarlar sekmesi* bölümüne bakın.
    ![Ekran görüntüsü, ilgili yürütülen SSIS paketi etkinliğinin ayarlarını yapılandırabileceğiniz, S s I Işleri Seç sayfasını gösterir.](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Adım ayarı*, seçili bir adım için ayarı yapılandırın.
        
        **Varsayılan ayarı uygula**: varsayılan seçilidir. Ayarı yalnızca seçilen adım için yapılandırmak üzere seçimini kaldırın.  
        Diğer özellikler hakkında daha fazla bilgi için, paket konumu *dosya sistemi (paket)* olduğunda [SSIS paketi yürütme etkinliğinin](how-to-invoke-ssis-package-ssis-activity.md) *Ayarlar sekmesi* bölümüne bakın.
    ![Ekran görüntüsü, varsayılan ayarları uygulayabileceğiniz, S s I Işleri Seç sayfasını gösterir.](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. ARM şablonu oluşturun ve dağıtın.
    1. Geçirilen ADF işlem hatlarının ARM şablonlarının çıkış yolunu seçin veya girin. Klasör yoksa, otomatik olarak oluşturulur.
    2. **ARM şablonlarını veri fabrikanıza dağıtma** seçeneğini belirleyin:
        - Varsayılan değer seçilmemiş. Oluşturulan ARM şablonlarını daha sonra el ile dağıtabilirsiniz.
        - Oluşturulan ARM şablonlarını Data Factory 'ye doğrudan dağıtmayı seçin.
    ![Ekran görüntüsü geçişi Yapılandır sayfasını gösterir; burada, geçirilen ADF işlem hatlarının ARM şablonlarının çıkış yolunu seçebilir veya girebilirsiniz ve veri fabrikanıza ARM şablonları dağıtma seçeneğini belirleyebilirsiniz.](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Geçirin ve sonuçları denetleyin.
![Ekran görüntüsü, geçişin ilerlemesini görüntüleyen geçiş sonucu sayfasını gösterir.](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Sonraki adımlar

[Çalıştırma ve izleme işlem hattı](how-to-invoke-ssis-package-ssis-activity.md)