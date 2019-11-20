---
title: SSDT 'deki Azure Data Factory SSIS paketlerini yürütme
description: SSDT 'den Azure 'da SSIS paketlerini yürütmeyi öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 4c89bdddce7b7318e184994ddf627d853e29fd7e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673596"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSDT 'den Azure 'da SSIS paketlerini yürütme
Bu makalede, SQL Server Veri Araçları (SSDT) üzerindeki Azure özellikli SQL Server Integration Services (SSIS) projelerinin özelliği açıklanmaktadır. Bu, paketleri Azure Data Factory (ADF) üzerinde Azure-SSIS Integration Runtime (IR) üzerinde çalıştırmanızı sağlar.  Bu özelliği kullanarak mevcut SSIS paketlerinizi, Azure 'a geçiş yapmak veya Azure 'a geçirmek ya da Azure 'da çalıştırmak üzere yeni SSIS paketleri geliştirmek için kullanabilirsiniz &.

Bu özellikle, yeni bir Azure-SSIS IR oluşturabilir veya var olan bir dosyayı SSIS projelerine iliştirebilir ve sonra paketlerinizi yürütebilirsiniz.  Proje dağıtım modelindeki SSIS kataloğuna (SSıSDB) dağıtılacak paketleri ve paket dağıtım modelindeki dosya sistemlerine/dosya paylaşımlarına/Azure dosyalarına dağıtılmasını destekliyoruz. 

## <a name="prerequisites"></a>Ön koşullar
Bu özelliği kullanmak için lütfen Visual Studio için SSIS projeleri uzantısı 'nı [buradan veya buradan](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) tek başına bir yükleyici olarak indirip [yükleyin.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure-SSIS projelerini etkinleştir
SSDT 'de **Integration Services projesi (Azure-etkin)** şablonunu kullanarak yeni Azure özellikli SSIS projeleri oluşturabilirsiniz.

![Yeni Azure özellikli SSIS projesi](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Alternatif olarak, bir menü açmak ve ardından **Azure etkin** menü öğesini seçmek için ssdt Çözüm Gezgini panelinde proje düğümüne sağ TıKLAYARAK mevcut SSIS projelerinizi etkinleştirin.

![Azure-var olan SSIS projesini etkinleştir](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Azure-mevcut SSIS projelerinizi etkinleştirmek, hedef sunucu sürümünü, şu anda **SQL Server 2017**olan Azure-SSIS IR tarafından desteklenen en son bir sürüm olacak şekilde ayarlamanızı gerektirir, bu nedenle daha önce yapmadıysanız, bunu yapmak için bir iletişim kutusu penceresi açılır.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Azure etkin projelerini SSIS 'ye bağlama Azure Data Factory
Azure etkin projelerinizi SSIS 'ye bağlanarak, paketlerinizi Azure dosyalarına yükleyebilir ve Azure-SSIS IR üzerinde çalıştırabilirsiniz.  Bunu yapmak için lütfen şu adımları izleyin:

1. SSDT 'nin Çözüm Gezgini panelinde proje veya **bağlı Azure kaynakları** düğümüne sağ tıklayarak bir menü açın ve SSIS 'Yi **ADF bağlantı Sihirbazı**' nı başlatmak Için **Azure Data Factory SSIS 'e Bağlan** menü öğesini seçin.

   ![ADF 'de SSIS 'e bağlanma](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. **ADF 'de SSIS giriş** sayfasında, giriş sayfasını gözden geçirin ve **İleri** düğmesine tıklayarak devam edin.

   ![ADF 'de SSIS girişi](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. **ADF 'de SSIS IR seçin** sayfasında, mevcut ADF 'yi seçip Azure-SSIS IR paketleri çalıştırın veya yoksa yeni bir tane oluşturun.
   - Mevcut Azure-SSIS IR seçmek için önce ilgili Azure aboneliğini ve sonra ADF 'yi seçin.
   - Azure-SSIS IR olmayan mevcut ADF 'yi seçerseniz, ADF portalında/uygulamada yeni bir tane oluşturmak için **SSIS IR oluştur** düğmesine tıklayın.
   - ADF 'ye sahip olmayan mevcut Azure aboneliğinizi seçerseniz Integration Runtime oluşturma Sihirbazı 'nı başlatmak için **SSIS IR oluştur** düğmesine tıklayın. burada, otomatik olarak yeni bir Azure oluşturmak için bize ait konumu ve öneki girebileceğiniz **oluşturma Sihirbazı**'nı başlatın. Aşağıdaki düzende adlandırılan kaynak grubu, Data Factory ve SSIS IR, sizin adınıza: **Yourprefix-RG/df/IR-YourCreationTime**.
   
   ![ADF 'de SSIS IR seçin](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. **Azure Storage 'ı seçin** sayfasında, mevcut Azure depolama hesabınızı seçerek paketleri Azure dosyalarına yükleyin veya yoksa yeni bir tane oluşturun.
   - Mevcut Azure depolama hesabınızı seçmek için öncelikle ilgili Azure aboneliğini seçin.
   - Azure depolama hesabı olmayan Azure-SSIS IR aynı Azure aboneliğini seçerseniz, Azure-SSIS IR tarafından adlandırılan aynı konumda sizin adınıza otomatik olarak yeni bir tane oluşturmak için **Azure depolama oluştur** düğmesine tıklayın. Azure-SSIS IR adınızın bir önekini ve oluşturulma tarihini birleştirme.
   - Azure depolama hesabı bulunmayan farklı bir Azure aboneliği seçerseniz Azure portal yeni bir tane oluşturmak için **Azure depolama oluştur** düğmesine tıklayın.
   
   ![Azure Depolama Alanı'nı seçin](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Bağlantınızı gerçekleştirmek için **Bağlan** düğmesine tıklayın.  Seçtiğiniz Azure-SSIS IR ve Azure depolama hesabınızı SSDT Çözüm Gezgini panelinde **bağlı Azure kaynakları** düğümü altında görüntüleyeceğiz.  Ayrıca, bir menü açmak ve ardından bunu ADF Portal/uygulama ' ya tıklayarak **Start\stop\yönet** menü öğesini seçmek için, Azure-SSIS IR durumunu yenileyecektir.

## <a name="execute-ssis-packages-in-azure"></a>Azure 'da SSIS paketlerini yürütme
### <a name="starting-package-executions"></a>Paket yürütmeleri başlatılıyor
Projelerinizi ADF 'de SSIS 'e bağladıktan sonra, Azure-SSIS IR paketleri çalıştırabilirsiniz.  Paket yürütmelerini başlatmak için iki seçeneğiniz vardır:
-  Bir menü açmak ve **Azure 'Da Yürüt** menü öğesini seçmek için SSDT araç çubuğundaki **Başlat** düğmesine tıklayın 

   ![Azure 'da Yürüt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Bir menü açmak için SSDT Çözüm Gezgini panelinde paket düğümüne sağ tıklayın ve **paketi Azure** menü öğesinde Yürüt ' ü seçin.

   ![Paketi Azure 'da Yürüt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Paketlerinizi Azure 'da çalıştırmak için çalışan bir Azure-SSIS IR olması gerekir, bu nedenle Azure-SSIS IR durdurulmuşsa, bir iletişim kutusu penceresi başlatılır.  Özel kurulum süresini dışlayarak, bu işlem 5 dakika içinde tamamlanmalıdır, ancak sanal bir ağa katılmak Azure-SSIS IR için yaklaşık 20-30 dakika sürebilir.  Paketlerinizi Azure 'da yürüttükten sonra, bir menü açmak ve ardından sizi ADF portalına götüren **Start\stop\manage** menü öğesini seçmek IÇIN, ssdt Çözüm Gezgini panelinde düğümüne sağ tıklayıp, çalışma maliyetini yönetmek için Azure-SSIS IR durdurabilirsiniz. Bunu yapmak için uygulama.

### <a name="checking-package-execution-logs"></a>Paket yürütme günlükleri denetleniyor
Paket yürütmeyi başlattığınızda, SSDT Ilerleme penceresinde günlüğünü biçimlendireceğiz ve görüntüleriz.  Uzun süre çalışan bir paket için günlük dosyasını dakikalar içinde düzenli olarak güncelleştireceğiz.  SSDT araç çubuğundaki **Durdur** düğmesine tıklayarak ve hemen iptal edecek şekilde paket yürütmeyi durdurabilirsiniz.  Günlük ham verilerini evrensel adlandırma kuralı (UNC) yolunda da geçici olarak bulabilirsiniz: `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`, ancak bir gün sonra temizleyeceğiz.

### <a name="switching-package-protection-level"></a>Paket koruma düzeyini değiştirme
Azure 'da SSIS paketlerini yürütme, **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** koruma düzeylerini desteklemez.  Sonuç olarak, paketleriniz bunlarla yapılandırıldıysa, bunları karşıya yüklerken rastgele oluşturulan parolalarla birlikte **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**'e geçici olarak geçeceğiz. Azure-SSIS IR yürütmek için Azure dosyalarına paketler.

> [!NOTE]
> Paketleriniz, **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** koruma düzeyleriyle yapılandırılmış diğer paketlere başvuran paket görevlerini içeriyorsa, bu diğer paketleri kullanmak **üzere el ile yeniden yapılandırmanız gerekir** Paketlerinizi yürütmeden önce, sırasıyla EncryptSensitiveWithPassword/**EncryptAllWithPassword**.

Paketleriniz zaten **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** koruma düzeyiyle yapılandırıldıysa, bunları değişmeden tutacağız, ancak paketlerinizi karşıya yüklerken rastgele oluşturulan parolalar kullanmaya devam eder. Azure-SSIS IR yürütmek için Azure dosyaları.

### <a name="using-package-configuration-file"></a>Paket yapılandırma dosyasını kullanma
Çalışma zamanında değişken değerlerini değiştirmek için paket dağıtım modelinde paket yapılandırma dosyalarını kullanırsanız, bu dosyaları paketlerinizle birlikte Azure-SSIS IR yürütmek üzere Azure dosyalarına otomatik olarak yükleyeceğiz.

### <a name="using-execute-package-task"></a>Paket yürütme görevini kullanma
Paketleriniz yerel dosya sistemlerinde depolanan diğer paketlere başvuran paket yürütme görevleri içeriyorsa, aşağıdaki ek kurulumları gerçekleştirmeniz gerekir:

1. Diğer paketleri, projelerinize bağlı aynı Azure depolama hesabı altında Azure dosyalarına yükleyin ve örneğin yeni UNC yollarını alın, örn. `\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Paket görevlerinin dosya Bağlantı Yöneticisinde bu diğer paketlerin dosya yolunu yeni UNC yollarıyla değiştirin
   - SSDT çalıştıran makineniz yeni UNC yoluna erişe, dosya Bağlantı Yöneticisi 'nin Özellikler panelinde dosya yolunu değiştirebilirsiniz
   - Alternatif olarak, çalışma zamanında doğru değeri atamak için dosya yolu için bir değişken kullanabilirsiniz

Paketleriniz aynı projedeki diğer paketlere başvuran paket yürütme görevlerini içeriyorsa, ek bir kurulum gerekmez.

## <a name="next-steps"></a>Sonraki adımlar
SSDT 'den Azure 'da paketlerinizi çalıştırmaya memnun olduktan sonra, ADF 'yi ADF Işlem hatlarında SSIS paket etkinliklerini Yürüt olarak [çalıştırma](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)ve çalıştırma konusuna bakın.
