---
title: SSIS paketlerini SSDT 'den yürütme
description: SSDT 'den Azure 'da SSIS paketlerini yürütmeyi öğrenin.
ms.service: data-factory
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: f11a3845e8644f3f60425538b2ef32cff668d88d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384937"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSDT 'den Azure 'da SSIS paketlerini yürütme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, SQL Server Veri Araçları (SSDT) üzerinde Azure etkin SQL Server Integration Services (SSIS) projelerinin özelliği açıklanmaktadır. SSIS paketlerinizin bulut uyumluluğunu değerlendirmenizi ve bunları Azure Data Factory (ADF) Azure-SSIS Integration Runtime (IR) üzerinde çalıştırmanızı sağlar. Bu özelliği, mevcut paketlerinizi kaldırmadan önce test etmek için kullanabilirsiniz & Azure 'a geçiş yapmak veya Azure 'da çalıştırmak üzere yeni paketler geliştirmek için kullanabilirsiniz.

Bu özellikle, yeni oluşturulan/mevcut bir Azure-SSIS IR SSIS projelerine iliştirebilir ve sonra paketlerinizi yürütebilirsiniz.  Azure SQL veritabanı sunucunuz tarafından barındırılan SSIS kataloğuna (SSSıSDB) veya proje dağıtım modelindeki yönetilen örneğe dağıtılacak paketlerin çalıştırılmasını destekliyoruz. Paket dağıtım modelinde Azure SQL yönetilen örneğiniz tarafından barındırılan dosya sistemine/Azure dosyalarına/SQL Server veritabanına (MSDB) dağıtılacak paketlerin çalıştırılmasını da destekliyoruz. 

## <a name="prerequisites"></a>Önkoşullar

Bu özelliği kullanmak için lütfen Visual Studio için SSIS projeleri uzantısı 'nı (VS.) [buradan](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)indirip yükleyin. Alternatif olarak, en son SSDT 'yi de tek başına yükleyici olarak indirip [yükleyebilirsiniz.](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure-SSIS projelerini etkinleştir

### <a name="creating-new-azure-enabled-ssis-projects"></a>Yeni Azure özellikli SSIS projeleri oluşturma

SSDT 'de **Integration Services projesi (Azure-etkin)** şablonunu kullanarak yeni Azure özellikli SSIS projeleri oluşturabilirsiniz.

   ![Yeni Azure özellikli SSIS projesi](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Azure etkin proje oluşturulduktan sonra, Azure Data Factory SSIS 'e bağlanmanız istenir.

   ![Connect Azure-SSIS IR istemi](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Azure-SSIS IR hemen bağlanmak istiyorsanız, daha fazla ayrıntı için bkz. [Azure-SSIS IR bağlanma](#connectssisir) . Ayrıca, bir menü açmak için SSDT Çözüm Gezgini penceresinde proje düğümünüzde sağ tıklayarak da bağlanabilirsiniz. Sonra, **SSIS** ' de Azure Data Factory alt menüdeki **SSIS Azure Data Factory** öğesini seçin.

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Azure-var olan SSIS projelerini etkinleştirme

Mevcut SSIS projeleri için, şu adımları izleyerek Azure 'u etkinleştirebilirsiniz:

1. Bir menü açmak için SSDT Çözüm Gezgini penceresinde proje düğümünüzde sağ tıklayın. Ardından, **Azure etkin proje Sihirbazı 'nı** başlatmak için Azure Data Factory alt menüsündeki **SSIS** 'de **Azure etkin proje** öğesini seçin.

   ![Azure-var olan SSIS projesini etkinleştir](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. **Visual Studio yapılandırması Seç** sayfasında, Azure 'da paket yürütme ayarlarını uygulamak IÇIN mevcut vs yapılandırmanızı seçin. Daha önce yapmadıysanız, yeni bir [vs yapılandırması oluşturma](/visualstudio/ide/how-to-create-and-edit-configurations)konusuna da yeni bir tane oluşturabilirsiniz. Yerel ve bulut ortamlarında paket yürütmeleri için en az iki farklı VS yapılandırmanıza sahip olmanız önerilir. böylece, projenizi bulut yapılandırmasında Azure için etkinleştirebilirsiniz. Bu şekilde, projenizi veya paketlerinizi parametreleştirdiyseniz, farklı yürütme ortamlarına (yerel makinenizde veya Azure 'da) göre çalışma zamanında projenize veya paket parametrelerine farklı değerler atayabilirsiniz. Örneğin, bkz. [paket yürütme ortamlarını değiştirme](#switchenvironment).

   ![Visual Studio yapılandırması seçin](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Azure-mevcut SSIS projelerinizi etkinleştirmek, hedef sunucu sürümünü Azure-SSIS IR tarafından desteklenen en son sürüm olacak şekilde ayarlamanızı gerektirir. Azure-SSIS IR Şu anda **SQL Server 2017** tabanlıdır. Lütfen paketlerinizin SQL Server 2017 ' de desteklenmeyen ek bileşenler içermediğinden emin olun. Ayrıca, tüm uyumlu ek bileşenlerin özel kurulumlardan Azure-SSIS IR yüklendiğinden de emin olun, bkz. [Azure-SSIS IR özelleştirme](./how-to-configure-azure-ssis-ir-custom-setup.md). Devam etmek için **İleri** düğmesini seçin.

   ![Hedef sunucu sürümünü değiştir](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Bkz. Azure-SSIS IR Azure-SSIS IR, projenizi bağlamayı tamamlamaya yönelik [bağlantı](#connectssisir) .

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Azure etkin projelerini SSIS 'ye bağlama Azure Data Factory

Azure etkin projelerinizi SSIS 'ye bağlanarak, paketlerinizi Azure dosyalarına yükleyebilir ve Azure-SSIS IR üzerinde çalıştırabilirsiniz. Bunu aşağıdaki adımları izleyerek yapabilirsiniz:

1. **ADF 'de SSIS giriş** sayfasında, giriş sayfasını inceleyin ve devam etmek için **İleri** düğmesini seçin.

   ![ADF 'de SSIS girişi](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. **ADF 'de SSIS IR seçin** sayfasında, mevcut ADF 'yi seçin ve paketleri çalıştırmak için Azure-SSIS IR. Ayrıca, yoksa yeni bir tane oluşturabilirsiniz.
   - Mevcut Azure-SSIS IR seçmek için önce ilgili Azure aboneliğini ve sonra ADF 'yi seçin.
   - Mevcut ADF 'yi bir Azure-SSIS IR seçerseniz, ADF portalında yeni bir tane oluşturmak için **SSIS IR oluştur** düğmesini seçin. Oluşturulduktan sonra yeni Azure-SSIS IR seçmek için bu sayfaya geri dönebilirsiniz.
   - ADF 'ye sahip olmayan mevcut Azure aboneliğinizi seçerseniz **Integration Runtime oluşturma Sihirbazı**'nı başlatmak için **SSIS IR oluştur** düğmesini seçin. Sihirbazda, aşağıdaki düzende adlandırılan yeni bir Azure Kaynak grubu, Data Factory ve SSIS IR 'yi sizin adınıza otomatik olarak oluşturmak için belirtilen konum ve ön eki girebilirsiniz: **yourprefix-RG/df/IR-YourCreationTime**. Oluşturulduktan sonra, yeni ADF ve Azure-SSIS IR seçmek için bu sayfaya geri dönebilirsiniz.

   ![ADF 'de SSIS IR seçin](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Azure **Storage 'ı seçin** sayfasında, Azure dosyalarına paket yüklemek Için mevcut Azure depolama hesabınızı seçin. Ayrıca, yoksa yeni bir tane oluşturabilirsiniz.
   - Mevcut Azure depolama hesabınızı seçmek için öncelikle ilgili Azure aboneliğini seçin.
   - Azure depolama hesabı olmayan Azure-SSIS IR aynı Azure aboneliğini seçerseniz, **Azure Storage oluştur** düğmesini seçin. Azure-SSIS IR adınızın bir önekini ve oluşturulma tarihini birleştirerek adlı Azure-SSIS IR aynı konumda sizin adınıza otomatik olarak yeni bir tane oluşturacağız. Oluşturulduktan sonra, yeni Azure depolama hesabınızı seçmek için bu sayfaya geri dönebilirsiniz.
   - Azure depolama hesabı bulunmayan farklı bir Azure aboneliği seçerseniz Azure portal yeni bir tane oluşturmak için **Azure depolama oluştur** düğmesini seçin. Oluşturulduktan sonra, yeni Azure depolama hesabınızı seçmek için bu sayfaya geri dönebilirsiniz.

   ![Azure Depolama Alanı'nı seçin](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Azure-SSIS IR için projenizi bağlamayı tamamladıktan sonra **Bağlan** düğmesini seçin. Seçtiğiniz Azure-SSIS IR ve Azure depolama hesabınızı, SSDT Çözüm Gezgini penceresindeki **bağlı Azure kaynakları** düğümü altında görüntüleriz. Ayrıca, Azure-SSIS IR düzenli olarak yenileyecek ve durumunu görüntüleriz. Bir menü açmak ve ardından bunu ADF Portal 'a alan **Start\stop\manage** öğesini seçmek için, düğümüne sağ tıklayıp Azure-SSIS IR yönetebilirsiniz.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Azure 'da yürütmeler için SSIS project\packages 'i değerlendirin

### <a name="assessing-single-or-multiple-packages"></a>Tek veya birden çok paket değerlendiriliyor

Paketlerinizi Azure 'da yürütmeden önce, olası bulut uyumluluk sorunlarını yüzeye ayırın. Bunlar geçiş engelleyicileri ve farkında olmanız gereken ek bilgiler içerir. 
-  Tek tek paketleri, projeniz kapsamında aynı anda tek tek paketleri veya tüm paketleri değerlendirme seçenekleriniz vardır.

   ![Paketi değerlendir](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![Projeyi değerlendir](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  SSDT **değerlendirme raporu** penceresinde, her biri kendi ayrıntılı açıklamasına ve önerisine sahip olan tüm olası bulut uyumluluk sorunlarını bulabilirsiniz. Değerlendirme raporunu, bu sorunları hafifletmek zorunda olan herkesle paylaşılabilen bir CSV dosyasına da dışarı aktarabilirsiniz. 

   ![Değerlendirme raporu](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>Değerlendirme kurallarını gizleme

Bazı potansiyel bulut uyumluluk sorunlarının geçerli olmadığından veya paketlerinizdeki düzgün şekilde azaltıldığından emin olduktan sonra, bunları yüzey halinde gösteren ilgili değerlendirme kurallarını gizleyebilirsiniz. Bu, sonraki değerlendirme raporlarınızdaki paraziti azaltır.
-  Bir değerlendirme **kuralı gizleme ayarları** penceresini seçmek için SSDT değerlendirme **raporundaki** değerlendirme **kuralı gizleme bağlantısını yapılandır** ' ı seçin, burada, bastırmaya yönelik değerlendirme kurallarını seçebilirsiniz.

   ![Değerlendirme kuralı gizleme ayarları](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  Alternatif olarak, bir menü açmak için SSDT Çözüm Gezgini penceresinde proje düğümünüzde sağ tıklayın. Proje özellik sayfalarınızın bulunduğu bir pencere açılır Azure Data Factory alt menüdeki **SSIS** 'de **Azure etkin ayarlar** öğesini seçin. **Azure etkin ayarlar** bölümünde **gizlenen değerlendirme kuralı kimlikleri** özelliğini seçin. Son olarak, ' ın üç nokta (**...**) düğmesini seçerek **değerlendirme kuralını gizleme ayarları** penceresini açabilir ve burada, bastırmaya yönelik değerlendirme kurallarını seçebilirsiniz.

   ![Azure etkin ayarları](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Azure etkin ayarları aracılığıyla değerlendirme kuralı gizleme ayarları](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>Azure 'da SSIS paketlerini yürütme

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Azure etkin ayarlarını yapılandırma

Paketlerinizi Azure 'da yürütmeden önce, Azure etkin ayarlarınızı bunlar için yapılandırabilirsiniz. Örneğin, aşağıdaki adımları izleyerek Azure-SSIS IR şirket içi/bulut veri depolarına erişmek için Windows kimlik doğrulamasını etkinleştirebilirsiniz:

1. Bir menü açmak için SSDT Çözüm Gezgini penceresinde proje düğümünüzde sağ tıklayın. Ardından, proje özellik sayfalarınızın bulunduğu bir pencere açılır Azure Data Factory alt menüdeki **SSIS** 'de **Azure etkin ayarlar** öğesini seçin.

   ![Azure etkin ayarları](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. **Azure etkin ayarlar** bölümünde **Windows kimlik doğrulaması özelliğini etkinleştir** ' i seçin ve ardından açılan menüsünde **doğru** ' ı seçin. Ardından, **Windows** kimlik doğrulama kimlik bilgileri özelliğini seçin ve sonra **Windows kimlik doğrulama kimlik bilgileri** penceresini seçmek için üç nokta (**...**) düğmesini seçin.

   ![Windows kimlik doğrulamasını etkinleştir](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Windows kimlik doğrulama kimlik bilgilerinizi girin. Örneğin, Azure dosyalarına erişmek için `Azure` `YourStorageAccountName` `YourStorageAccountKey` sırasıyla **etki alanı**, **Kullanıcı adı** ve **parola** girebilirsiniz.

   ![Windows kimlik doğrulama kimlik bilgileri](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Paket yürütmeleri başlatılıyor

Azure etkin projelerinizi ADF 'ye bağladıktan sonra bulut uyumluluğunu değerlendirdikten ve olası sorunları etkinleştirdikten sonra, Azure-SSIS IR paketlerinizi yürütebilir/test edebilirsiniz.
-  Bir menüyü aşağı eklemek için SSDT araç çubuğundaki **Başlat** düğmesini seçin. Ardından, **Azure 'Da Yürüt** öğesini seçin.

   ![Azure 'da Yürüt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Alternatif olarak, bir menü açmak için SSDT Çözüm Gezgini penceresindeki paket düğümünüzde sağ tıklayın. Ardından, Azure öğesinde **paketi Yürüt** öğesini seçin.

   ![Paketi Azure 'da Yürüt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Paketlerinizi Azure 'da çalıştırmak için çalışan bir Azure-SSIS IR olması gerekir, bu nedenle Azure-SSIS IR durdurulmuşsa, bir iletişim kutusu penceresi başlatılır. Özel kurulum süresini dışlayarak, bu işlem 5 dakika içinde tamamlanmalıdır, ancak sanal bir ağa katılmak Azure-SSIS IR için yaklaşık 20-30 dakika sürebilir. Paketlerinizi Azure 'da yürüttükten sonra, bir menü açmak ve ardından bunu yapmak için ADF Portal 'a alan **Start\stop\manage** öğesini seçerek, çalışma Çözüm Gezgini maliyetini yönetmek için Azure-SSIS IR durdurabilirsiniz.

### <a name="using-execute-package-task"></a>Paket yürütme görevini kullanma

Paketleriniz yerel dosya sistemlerinde depolanan alt paketlere başvuran paket yürütme görevleri içeriyorsa, bu ek adımları izleyin:

1. Alt paketleri, projelerinize bağlı aynı Azure depolama hesabı altında Azure dosyalarına yükleyin ve örneğin yeni evrensel adlandırma kuralı (UNC) yolunu alın. `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. Paket görevlerinin dosya Bağlantı Yöneticisinde bu alt paketlerin dosya yolunu yeni UNC yollarıyla değiştirin
   - SSDT çalıştıran yerel makineniz yeni UNC yoluna erişe, bunu dosya Bağlantı Yöneticisi 'nin Özellikler paneline girebilirsiniz
   - Alternatif olarak, çalışma zamanında doğru değeri atamak için dosya yolu için bir değişken kullanabilirsiniz

Paketleriniz aynı projedeki alt paketlere başvuran bir yürütme paketi görevi içeriyorsa ek bir adım gerekmez.

### <a name="switching-package-protection-level"></a>Paket koruma düzeyini değiştirme

Azure 'da SSIS paketlerini yürütmek, **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** koruma düzeylerini desteklemez. Sonuç olarak, paketleriniz bunları kullanacak şekilde yapılandırıldıysa, bunları sırasıyla **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** koruma düzeylerini kullanarak geçici olarak dönüştürüyoruz. Ayrıca, paketlerinizi Azure-SSIS IR yürütmeler için Azure dosyalarına yüklerken rastgele şifreleme parolaları oluşturacağız.

> [!NOTE]
> Paketleriniz, **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** koruma düzeylerini kullanacak şekilde yapılandırılmış alt paketlere başvuran paket çalıştır görevleri içeriyorsa, paketlerinizi yürütmeden önce, bu alt paketleri sırasıyla **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** koruma düzeylerini kullanacak şekilde yeniden yapılandırmanız gerekir.

Paketleriniz zaten **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** koruma düzeylerini kullanacak şekilde yapılandırıldıysa, bunları değişmeden tutacağız. Paketlerinizi Azure-SSIS IR yürütmeler için Azure dosyaları 'na yüklerken rastgele şifreleme parolaları oluşturmaya devam edeceğiz.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> Paket yürütme ortamlarını değiştirme

Proje dağıtım modelindeki proje/paketlerinizi parametreleştirebilirsiniz, paket yürütme ortamlarını değiştirmek için birden çok VS yapılandırması oluşturabilirsiniz. Bu şekilde, çalışma zamanında projenize/paket parametreleriniz için ortama özel değerler atayabilirsiniz. Yerel ve bulut ortamlarında paket yürütmeleri için en az iki farklı VS yapılandırmanıza sahip olmanız önerilir. böylece projelerinizi bulut yapılandırmasına karşı Azure için etkinleştirebilirsiniz. Aşağıda, yerel makineniz ve Azure arasında paket yürütme ortamlarını değiştirme adım adım bir örnek verilmiştir:

1. Paketinizin bir dosyanın özniteliklerini ayarlayan bir dosya sistemi görevi içerdiğini varsayalım. Yerel makinenizde çalıştırdığınızda yerel dosya sisteminizde depolanan bir dosyanın özniteliklerini ayarlar. Azure-SSIS IR çalıştırdığınız zaman, Azure dosyalarında depolanan bir dosyanın özniteliklerini ayarlamanızı istersiniz. İlk olarak, dize türünde bir paket parametresi oluşturun ve dosyayı hedef dosya yolunun değerini **tutacak şekilde adlandırın** .

   ![Paket parametresi oluştur](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Sonra, **dosya sistemi görev Düzenleyicisi** penceresinin **genel** sayfasında, **FilePath** Package parametresiyle **kaynak bağlantısı** bölümündeki **SourceVariable** özelliğini parametreleştirin. 

   ![Kaynak bağlantısı Parametreleştir](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Varsayılan olarak, **geliştirme** adlı yerel ortamda paket yürütmeleri için mevcut bir vs yapılandırmanıza sahip olursunuz. **Azure** adlı bulut ortamında paket yürütmeleri için yenı bir vs yapılandırması oluşturun, daha önce yapmadıysanız, [Yeni vs yapılandırması oluşturma](/visualstudio/ide/how-to-create-and-edit-configurations)bölümüne bakın.

4. Paketinizin parametrelerini görüntülerken, paketinizin **parametre değerlerini Yönet** penceresini açmak Için **parametrelere parametre Ekle** düğmesini seçin. Daha sonra, **geliştirme** ve **Azure** yapılandırmalarının altındaki **FilePath** Package parametresine hedef dosya yolunun farklı değerlerini atayın.

   ![Parametre değerlerini ata](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure-projenizi bulut yapılandırmasına karşı etkinleştirin, daha önce yapmadıysanız bkz. [Azure-var olan SSIS projelerini etkinleştirme](#azureenableproject). Daha sonra, Azure dosyaları Azure-SSIS IR için Windows kimlik doğrulamasını etkinleştirmek üzere Azure etkin ayarlarını yapılandırın, daha önce yapmadıysanız [Azure etkin ayarları yapılandırma](#azureenabledsettings)konusuna bakın.

6. Paketinizi Azure 'da yürütün. **Geliştirme** yapılandırmasını seçerek paket yürütme ortamınızı yerel makinenize geri geçirebilirsiniz.

   ![Visual Studio yapılandırmasını değiştirme](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>Paket yapılandırma dosyasını kullanma

Paket dağıtım modelinde paket yapılandırma dosyalarını kullanıyorsanız, çalışma zamanında paket özelliklerinizi ortama özel değerler atayabilirsiniz. Paketleriniz için bu dosyaları, Azure-SSIS IR yürütmeler için Azure dosyalarına otomatik olarak yükleyeceğiz.

### <a name="checking-package-execution-logs"></a>Paket yürütme günlükleri denetleniyor

Paket yürütmeyi başlattıktan sonra, günlüklerini SSDT 'nin **ilerleme** penceresinde biçimlendirir ve görüntüleriz. Uzun süre çalışan bir pakette, günlüklerini düzenli olarak dakikalar içinde güncelleştiririz. SSDT araç çubuğundaki **Durdur** düğmesini seçerek paket yürütmeyi hemen iptal edebilirsiniz. Ayrıca, günlüklerinin ham verilerini şu UNC yolunda geçici olarak bulabilirsiniz: `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , ancak bir gün sonra temizleyeceğiz.

## <a name="current-limitations"></a>Geçerli sınırlamalar

-  Azure özellikli SSDT yalnızca ticari/genel bulut bölgelerini destekler ve şimdilik kamu/Ulusal bulut bölgelerini desteklemez.

## <a name="next-steps"></a>Sonraki adımlar

SSDT 'den Azure 'da paketlerinizi çalıştırmaya memnun olduktan sonra, ADF 'yi ADF Işlem hatları 'nda SSIS paketi etkinliklerini [Yürüt olarak çalıştırıp çalıştırabilirsiniz.](./how-to-invoke-ssis-package-ssis-activity.md)