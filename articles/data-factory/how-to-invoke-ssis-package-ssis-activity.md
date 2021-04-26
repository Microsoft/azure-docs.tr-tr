---
title: SSIS paketi yürütme etkinliğiyle bir SSIS paketi çalıştırın
description: Bu makalede, SSIS paketi yürütme etkinliğini kullanarak bir Azure Data Factory işlem hattında SQL Server Integration Services (SSIS) paketinin nasıl çalıştırılacağı açıklanmaktadır.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: 40e7d5c11a2ebc62e59c3d5d20dfefe18a33f9a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100391618"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Data Factory'de SSIS Paketini Yürüt etkinliğiyle bir SSIS paketi çalıştırma

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, SSIS paketi yürütme etkinliğini kullanarak bir Azure Data Factory işlem hattında SQL Server Integration Services (SSIS) paketinin nasıl çalıştırılacağı açıklanmaktadır. 

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Öğreticide adım adım yönergeleri izleyerek bir Azure-SSIS tümleştirme çalışma zamanı (IR) oluşturun [: sağlama Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md).

## <a name="run-a-package-in-the-azure-portal"></a>Azure portal bir paket çalıştırın
Bu bölümde, SSIS paketinizi çalıştıran bir SSIS paketi yürütme etkinliği ile Data Factory işlem hattı oluşturmak için Data Factory Kullanıcı arabirimini (UI) veya uygulamayı kullanırsınız.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS paketi yürütme etkinliği ile işlem hattı oluşturma
Bu adımda, bir işlem hattı oluşturmak için Data Factory Kullanıcı arabirimini veya uygulamayı kullanırsınız. İşlem hattına bir SSIS paketi yürütme etkinliği ekleyin ve bunu SSIS paketinizi çalıştıracak şekilde yapılandırın. 

1. Azure portal Data Factory genel bakış veya giriş sayfasında, Data Factory Kullanıcı arabirimini veya uygulamayı ayrı bir sekmede başlatmak için **yazar & izleyici** kutucuğunu seçin. 

   ![Data Factory giriş sayfası](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   **Kullanmaya başlama** sayfasında **İşlem hattı oluştur** seçeneğini belirleyin. 

   ![Başlarken sayfası](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. **Etkinlikler** araç kutusunda **genel**' i genişletin. Sonra bir **SSIS paketi yürütme etkinliğini işlem** hattı tasarımcı yüzeyine sürükleyin. 

   ![Bir SSIS paketi yürütme etkinliğini tasarımcı yüzeyine sürükleyin](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   **Genel**, **Ayarlar**, **SSIS parametreleri**, **bağlantı yöneticileri** ve **özellik geçersiz kılmaları** sekmelerini yapılandırmak için SSIS paketini Yürüt etkinlik nesnesini seçin.

#### <a name="general-tab"></a>Genel sekmesi

SSIS paketi yürütme etkinliğinin **genel** sekmesinde aşağıdaki adımları izleyin.

![Genel sekmesindeki özellikleri ayarla](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. **Ad** Için, Execute SSIS Package etkinliğinizin adını girin.

   1. **Açıklama** IÇIN, SSIS paketi yürütme etkinliğinizin açıklamasını girin.

   1. **Zaman aşımı** Için, yürütme SSIS paketi etkinliğinizin çalışacağı maksimum süreyi girin. Varsayılan değer 7 gündür, biçim D. HH: MM: SS şeklindedir.

   1. **Yeniden deneme** IÇIN, SSIS paketi yürütme etkinliğinizin en fazla yeniden deneme sayısını girin.

   1. **Yeniden deneme aralığı** Için, Execute SSIS paketi etkinliğinizdeki her yeniden deneme denemesi arasındaki saniye sayısını girin. Varsayılan değer 30 saniyedir.

   1. Execute SSIS paketi etkinliğinizdeki çıktıyı günlüğe kaydetme işleminden dışlamak isteyip istemediğinizi seçmek için **güvenli çıkış** onay kutusunu seçin.

   1. Execute SSIS paketi etkinliğinizin girişini günlüğe kaydetme işleminden dışlamak isteyip istemediğinizi seçmek için **güvenli giriş** onay kutusunu seçin.

#### <a name="settings-tab"></a>Ayarlar sekmesi

SSIS paketi yürütme etkinliğinin **Ayarlar** sekmesinde aşağıdaki adımları izleyin.

![Ayarlar sekmesinde özellikleri ayarlama-otomatik](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. **Azure-SSIS IR** Için, Execute SSIS paketi etkinliğinizi çalıştırmak için belirlenen Azure-SSIS IR seçin.

   1. **Açıklama** IÇIN, SSIS paketi yürütme etkinliğinizin açıklamasını girin.

   1. Şirket içi veya Azure dosyaları gibi veri depolarına erişmek için Windows kimlik doğrulaması kullanmak isteyip istemediğinizi seçmek üzere **Windows kimlik doğrulaması** onay kutusunu seçin.
   
      Bu onay kutusunu seçerseniz, paket yürütme kimlik bilgilerinizin değerlerini **etki alanı**, **Kullanıcı adı** ve **parola** kutularına girin. Örneğin, Azure dosyalarına erişmek için etki alanı, `Azure` Kullanıcı adı `<storage account name>` ve parola olur `<storage account key>` .

      Alternatif olarak, Azure Key Vault depolanan gizli dizileri değerler olarak kullanabilirsiniz. Bunu yapmak için, yanındaki **Azure Anahtar Kasası** onay kutusunu seçin. Mevcut Anahtar Kasası bağlı hizmetinizi seçin veya düzenleyin ya da yeni bir tane oluşturun. Daha sonra, değer için gizli adı ve sürümü seçin. Anahtar Kasası bağlı hizmetinizi oluştururken veya düzenlediğinizde, mevcut anahtar kasanızı seçebilir veya düzenleyebilir veya yeni bir tane oluşturabilirsiniz. Daha önce yapmadıysanız, anahtar kasanıza Data Factory yönetilen kimlik erişimi verdiğinizden emin olun. Gizli dizinizi doğrudan aşağıdaki biçimde de girebilirsiniz: `<key vault linked service name>/<secret name>/<secret version>` .
      
   1. Paketinizin 32 bit çalışma zamanının çalışmasına ihtiyacı olup olmadığını seçmek için **32-bit çalışma zamanı** onay kutusunu seçin.

   1. **Paket konumu** Için **SSISDB**, **dosya sistemi (paket)**, **dosya sistemi (proje)**, **katıştırılmış paket** veya **paket deposu**' nu seçin. 

##### <a name="package-location-ssisdb"></a>Paket konumu: SSSıSDB

Azure-SSIS IR, Azure SQL veritabanı sunucusu/yönetilen örneği tarafından barındırılan bir SSIS Kataloğu (SSSıSDB) ile sağlandıysa veya kendiniz seçerek, paket konumunuz olarak **Sssısdb** otomatik olarak seçilir. Seçilirse, aşağıdaki adımları uygulayın.

   1. Azure-SSIS IR çalışıyorsa ve **el ile girdiler** onay kutusu SILINIRSE, SSISDB 'den var olan klasörlerinizi, projelerinizi, paketlerinizi ve ortamlarınızı göz atın ve seçin. Yeni eklenen klasörlerinizi, projelerinizi, paketlerinizi veya ortamlarınızı SSıSDB 'den getirmek için **Yenile** ' yi seçin, böylece göz atma ve seçim için kullanılabilir olmaları gerekir. Paket yürütmelerinin ortamlarına gitmek ve bunları seçmek için, bu ortamları SSSıSDB altındaki aynı klasörlerden başvuru olarak eklemek üzere projelerinizi önceden yapılandırmanız gerekir. Daha fazla bilgi için bkz. [SSIS ortamlarını oluşturma ve eşleme](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

   1. **Günlüğe kaydetme düzeyi** için, paket yürütülemenize yönelik önceden tanımlanmış bir günlük kapsamı seçin. Bunun yerine özelleştirilmiş günlük adınızı girmek istiyorsanız **özelleştirilmiş** onay kutusunu seçin. 

   1. Azure-SSIS IR çalışmıyorsa veya **el ile girdiler** onay kutusu IŞARETLIYSE, SSISDB 'den paketinize ve ortam yollarınızı doğrudan şu biçimlerde girin: `<folder name>/<project name>/<package name>.dtsx` ve `<folder name>/<environment name>` .

      ![Ayarlar sekmesinde özellikleri ayarlama-El Ile](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>Paket konumu: dosya sistemi (paket)

Paket konumunuz olarak **dosya sistemi (paket)** , Azure-SSIS IR sssısdb olmadan sağlanmışsa otomatik olarak seçilir veya kendiniz seçebilirsiniz. Seçilirse, aşağıdaki adımları uygulayın.

![Ayarlar sekmesindeki özellikleri ayarlama-dosya sistemi (paket)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. Paket yolu kutusuna paket dosyanıza (ile) bir evrensel adlandırma kuralı (UNC) yolu sağlayarak çalıştırılacak paketinizi belirtin `.dtsx` .  **Dosya depolamasına gözatıp** yolunu el ile girerek paketinize gözatabilir ve seçim yapabilirsiniz. Örneğin, paketinizi Azure dosyalarında depolu, yolu ise olur `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` . 
   
   1. Paketinizi ayrı bir dosyada yapılandırırsanız, yapılandırma yolu kutusuna yapılandırma dosyanıza (ile) bir UNC yolu da sağlamanız gerekir `.dtsConfig` .  **Dosya depolama alanını görüntüle** ' yi seçerek yapılandırmanıza gözatıp seçebilirsiniz veya yolunu el ile girebilirsiniz. Örneğin, yapılandırmanızı Azure dosyalarında depolukaldırırsanız, yolu olur `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Paketinize ve yapılandırma dosyalarınıza erişmek için kimlik bilgilerini belirtin. Daha önce paket yürütme kimlik bilgileriniz için değerler girdiyseniz ( **Windows kimlik doğrulaması** için), **paket yürütme kimlik bilgileri ile aynı** onay kutusunu seçerek bunları yeniden kullanabilirsiniz. Aksi takdirde, paket erişim kimlik bilgilerinizin değerlerini **etki alanı**, **Kullanıcı adı** ve **parola** kutularına girin. Örneğin, paketinizi ve yapılandırmanızı Azure dosyalarında depolarsanız, etki alanı, `Azure` Kullanıcı adı `<storage account name>` ve parola olur `<storage account key>` . 

      Alternatif olarak, Azure Key Vault depolanan gizli dizileri değerler olarak kullanabilirsiniz. Bunu yapmak için, yanındaki **Azure Anahtar Kasası** onay kutusunu seçin. Mevcut Anahtar Kasası bağlı hizmetinizi seçin veya düzenleyin ya da yeni bir tane oluşturun. Daha sonra, değer için gizli adı ve sürümü seçin. Anahtar Kasası bağlı hizmetinizi oluştururken veya düzenlediğinizde, mevcut anahtar kasanızı seçebilir veya düzenleyebilir veya yeni bir tane oluşturabilirsiniz. Daha önce yapmadıysanız, anahtar kasanıza Data Factory yönetilen kimlik erişimi verdiğinizden emin olun. Gizli dizinizi doğrudan aşağıdaki biçimde de girebilirsiniz: `<key vault linked service name>/<secret name>/<secret version>` . 

      Bu kimlik bilgileri, kendi yolu ve paketleriniz tarafından belirtilen diğer konfigürasyonlar tarafından başvurulan paket yürütme görevinde alt paketlerinize erişmek için de kullanılır. 

   1. Paketinizi SQL Server Veri Araçları (SSDT) aracılığıyla oluşturduğunuzda **EncryptAllWithPassword** veya **EncryptSensitiveWithPassword** koruma düzeyini kullandıysanız, **şifreleme parolası** kutusuna parolanızın değerini girin. Alternatif olarak, Azure Key Vault depolanan bir gizli anahtarı, değeri olarak (yukarıya bakın) kullanabilirsiniz.
      
      **EncryptSensitiveWithUserKey** koruma düzeyini kullandıysanız, hassas değerleri yapılandırma dosyalarına ya da **SSIS parametreleri**, **bağlantı yöneticileri** veya **özellik geçersiz kılmalar** sekmelerine (aşağıya bakın) yeniden girin.
      
      **EncryptAllWithUserKey** koruma düzeyini kullandıysanız, bu desteklenmez. Paketinizi, SSDT veya komut satırı yardımcı programı aracılığıyla başka bir koruma düzeyi kullanacak şekilde yeniden yapılandırmanız gerekir `dtutil` . 

   1. **Günlüğe kaydetme düzeyi** için, paket yürütülemenize yönelik önceden tanımlanmış bir günlük kapsamı seçin. Bunun yerine özelleştirilmiş günlük adınızı girmek istiyorsanız **özelleştirilmiş** onay kutusunu seçin. 
   
   1. Paketinizdeki belirtime standart günlük sağlayıcılarını kullanarak paket yürütmelerinin daha fazla günlüğe kaydedilmesini istiyorsanız günlük **kaydı yolu** kutusuna UNC yolunu sağlayarak günlük klasörünüzü belirtin. **Dosya depolama alanını görüntüle** ' yi seçerek veya yolunu el ile girerek günlük klasörünüze gözatabilir ve seçim yapabilirsiniz. Örneğin, günlüklerinizi Azure dosyalarında depolu, günlük yolunuz olur `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Bu yolda her bir paket çalıştırması için bir alt klasör oluşturulur, SSIS paketini Yürüt etkinlik çalıştırma KIMLIĞI ve her beş dakikada bir günlük dosyası oluşturulur. 
   
   1. Günlük klasörünüze erişmek için kimlik bilgilerini belirtin. Daha önce paket erişim kimlik bilgilerinizin değerlerini girdiyseniz (yukarıya bakın), **paket erişimi kimlik bilgileri Ile aynı** onay kutusunu seçerek bunları yeniden kullanabilirsiniz. Aksi takdirde, oturum açma erişim kimlik bilgilerinizin değerlerini **etki alanı**, **Kullanıcı adı** ve **parola** kutularına girin. Örneğin, günlüklerinizi Azure dosyalarında depolarsanız, etki alanı, `Azure` Kullanıcı adı ise `<storage account name>` ve parola olur `<storage account key>` . Alternatif olarak, Azure Key Vault depolanan gizli dizileri değerler olarak kullanabilirsiniz (yukarıya bakın).
   
Daha önce bahsedilen tüm UNC yolları için, tam nitelikli dosya adı 260 karakterden kısa olmalıdır. Dizin adı 248 karakterden kısa olmalıdır.

##### <a name="package-location-file-system-project"></a>Paket konumu: dosya sistemi (proje)

Paket konumunuz olarak **dosya sistemi (proje)** seçeneğini belirlerseniz aşağıdaki adımları izleyin.

![Ayarlar sekmesindeki özellikleri ayarlama-dosya sistemi (proje)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. Proje dosyanıza (ile `.ispac` ) proje **yolu** kutusuna bir UNC yolu ve `.dtsx` **paket adı** kutusuna projenizden bir paket dosyası (ile) ekleyerek çalıştırılacak paketinizi belirtin. **Dosya depolama alanını görüntüle** ' yi seçerek projenize gözatıp seçebilirsiniz veya yolunu el ile girebilirsiniz. Örneğin, projenizi Azure dosyalarında depolukaldırırsanız, yolu olur `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` .

   1. Projenize ve paket dosyalarınıza erişmek için kimlik bilgilerini belirtin. Daha önce paket yürütme kimlik bilgileriniz için değerler girdiyseniz ( **Windows kimlik doğrulaması** için), **paket yürütme kimlik bilgileri ile aynı** onay kutusunu seçerek bunları yeniden kullanabilirsiniz. Aksi takdirde, paket erişim kimlik bilgilerinizin değerlerini **etki alanı**, **Kullanıcı adı** ve **parola** kutularına girin. Örneğin, projenizi ve paketinizi Azure dosyalarında depolarsanız, etki alanı, `Azure` Kullanıcı adı ise `<storage account name>` ve parola olur `<storage account key>` . 

      Alternatif olarak, Azure Key Vault depolanan gizli dizileri değerler olarak kullanabilirsiniz. Bunu yapmak için, yanındaki **Azure Anahtar Kasası** onay kutusunu seçin. Mevcut Anahtar Kasası bağlı hizmetinizi seçin veya düzenleyin ya da yeni bir tane oluşturun. Daha sonra, değer için gizli adı ve sürümü seçin. Anahtar Kasası bağlı hizmetinizi oluştururken veya düzenlediğinizde, mevcut anahtar kasanızı seçebilir veya düzenleyebilir veya yeni bir tane oluşturabilirsiniz. Daha önce yapmadıysanız, anahtar kasanıza Data Factory yönetilen kimlik erişimi verdiğinizden emin olun. Gizli dizinizi doğrudan aşağıdaki biçimde de girebilirsiniz: `<key vault linked service name>/<secret name>/<secret version>` . 

      Bu kimlik bilgileri aynı projeden başvurulan paket yürütme görevinde alt paketlerinize erişmek için de kullanılır. 

   1. Paketinizi SSDT aracılığıyla oluştururken **EncryptAllWithPassword** veya **EncryptSensitiveWithPassword** koruma düzeyini kullandıysanız, **şifreleme parolası** kutusuna parolanızın değerini girin. Alternatif olarak, Azure Key Vault depolanan bir gizli anahtarı, değeri olarak (yukarıya bakın) kullanabilirsiniz.
      
      **EncryptSensitiveWithUserKey** koruma düzeyini kullandıysanız, **SSIS parametreleri**, **bağlantı yöneticileri** veya **özellik geçersiz kılmalar** sekmelerinde hassas değerleri yeniden girin (aşağıya bakın).
      
      **EncryptAllWithUserKey** koruma düzeyini kullandıysanız, bu desteklenmez. Paketinizi, SSDT veya komut satırı yardımcı programı aracılığıyla başka bir koruma düzeyi kullanacak şekilde yeniden yapılandırmanız gerekir `dtutil` . 

   1. **Günlüğe kaydetme düzeyi** için, paket yürütülemenize yönelik önceden tanımlanmış bir günlük kapsamı seçin. Bunun yerine özelleştirilmiş günlük adınızı girmek istiyorsanız **özelleştirilmiş** onay kutusunu seçin. 
   
   1. Paketinizdeki belirtime standart günlük sağlayıcılarını kullanarak paket yürütmelerinin daha fazla günlüğe kaydedilmesini istiyorsanız günlük **kaydı yolu** kutusuna UNC yolunu sağlayarak günlük klasörünüzü belirtin. **Dosya depolama alanını görüntüle** ' yi seçerek veya yolunu el ile girerek günlük klasörünüze gözatabilir ve seçim yapabilirsiniz. Örneğin, günlüklerinizi Azure dosyalarında depolu, günlük yolunuz olur `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Bu yolda her bir paket çalıştırması için bir alt klasör oluşturulur, SSIS paketini Yürüt etkinlik çalıştırma KIMLIĞI ve her beş dakikada bir günlük dosyası oluşturulur. 
   
   1. Günlük klasörünüze erişmek için kimlik bilgilerini belirtin. Daha önce paket erişim kimlik bilgilerinizin değerlerini girdiyseniz (yukarıya bakın), **paket erişimi kimlik bilgileri Ile aynı** onay kutusunu seçerek bunları yeniden kullanabilirsiniz. Aksi takdirde, oturum açma erişim kimlik bilgilerinizin değerlerini **etki alanı**, **Kullanıcı adı** ve **parola** kutularına girin. Örneğin, günlüklerinizi Azure dosyalarında depolarsanız, etki alanı, `Azure` Kullanıcı adı ise `<storage account name>` ve parola olur `<storage account key>` . Alternatif olarak, Azure Key Vault depolanan gizli dizileri değerler olarak kullanabilirsiniz (yukarıya bakın).
   
Daha önce bahsedilen tüm UNC yolları için, tam nitelikli dosya adı 260 karakterden kısa olmalıdır. Dizin adı 248 karakterden kısa olmalıdır.

##### <a name="package-location-embedded-package"></a>Paket konumu: katıştırılmış paket

Paket konumunuz olarak **katıştırılmış paket** ' i seçerseniz, aşağıdaki adımları izleyin.

![Ayarlar sekmesi-katıştırılmış pakette özellikleri ayarlama](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. Paket dosyanızı (ile) sürükleyip bırakın `.dtsx` veya belirtilen kutuya bir dosya klasöründen **yükleyin** . Paketinizin otomatik olarak sıkıştırılması ve etkinlik yüküne katıştırılması gerekir. Ekli olduktan sonra, paketini daha sonra düzenlemeniz için **indirebilirsiniz** . Ayrıca, birden çok etkinlikte kullanılabilecek bir işlem hattı parametresine atayarak gömülü paketinizi **parametreleştirebilirsiniz** , bu nedenle işlem hattı yükünüzü en iyi duruma getirebilirsiniz. Proje dosyalarını (ile `.ispac` ) ekleme işlemi şu anda desteklenmiyor, bu nedenle SSIS parametrelerini/bağlantı yöneticilerini gömülü paketlerinizin proje düzeyi kapsamıyla kullanamazsınız.
   
   1. Katıştırılmış paketinizin tümü şifrelenmemişse ve bu pakette paket yürütme görevi (EPT) kullanımını tespit ettik, **paket görevini Çalıştır** onay kutusu otomatik olarak seçilir ve dosya sistemi yolu tarafından başvurulan alt paketleriniz otomatik olarak eklenir, böylece bunları ekleyebilirsiniz.
   
      EPT 'in kullanımını algılayamıyoruz, **paket görevini Yürüt** onay kutusunu el ile seçmeniz ve dosya sistemi yolları tarafından başvurulan alt paketlerinizi tek tek eklemeniz gerekir, böylece bunları ekleyebilirsiniz. Alt paketleriniz SQL Server veritabanı 'nda (MSDB) depolanıyorsa, bunları katıştırabilmeniz için Azure-SSIS IR SQL Server başvurularını kullanarak bunları getirmek üzere MSDB 'ye erişebildiğinden emin olmanız gerekir. Proje dosyalarını ekleme (ile `.ispac` ) Şu anda desteklenmiyor, bu nedenle alt paketleriniz için proje tabanlı başvuruları kullanamazsınız.
   
   1. Paketinizi SSDT aracılığıyla oluştururken **EncryptAllWithPassword** veya **EncryptSensitiveWithPassword** koruma düzeyini kullandıysanız, **şifreleme parolası** kutusuna parolanızın değerini girin. 
   
      Alternatif olarak, Azure Key Vault depolanan bir gizli anahtarı, değeri olarak da kullanabilirsiniz. Bunu yapmak için yanındaki **Azure Anahtar Kasası** onay kutusunu seçin. Mevcut Anahtar Kasası bağlı hizmetinizi seçin veya düzenleyin ya da yeni bir tane oluşturun. Daha sonra, değer için gizli adı ve sürümü seçin. Anahtar Kasası bağlı hizmetinizi oluştururken veya düzenlediğinizde, mevcut anahtar kasanızı seçebilir veya düzenleyebilir veya yeni bir tane oluşturabilirsiniz. Daha önce yapmadıysanız, anahtar kasanıza Data Factory yönetilen kimlik erişimi verdiğinizden emin olun. Gizli dizinizi doğrudan aşağıdaki biçimde de girebilirsiniz: `<key vault linked service name>/<secret name>/<secret version>` .
      
      **EncryptSensitiveWithUserKey** koruma düzeyini kullandıysanız, hassas değerleri yapılandırma dosyalarına ya da **SSIS parametreleri**, **bağlantı yöneticileri** veya **özellik geçersiz kılmalar** sekmelerine (aşağıya bakın) yeniden girin.
      
      **EncryptAllWithUserKey** koruma düzeyini kullandıysanız, bu desteklenmez. Paketinizi, SSDT veya komut satırı yardımcı programı aracılığıyla başka bir koruma düzeyi kullanacak şekilde yeniden yapılandırmanız gerekir `dtutil` .

   1. **Günlüğe kaydetme düzeyi** için, paket yürütülemenize yönelik önceden tanımlanmış bir günlük kapsamı seçin. Bunun yerine özelleştirilmiş günlük adınızı girmek istiyorsanız **özelleştirilmiş** onay kutusunu seçin. 
   
   1. Paketinizdeki belirtime standart günlük sağlayıcılarını kullanarak paket yürütmelerinin daha fazla günlüğe kaydedilmesini istiyorsanız günlük **kaydı yolu** kutusuna UNC yolunu sağlayarak günlük klasörünüzü belirtin. **Dosya depolama alanını görüntüle** ' yi seçerek veya yolunu el ile girerek günlük klasörünüze gözatabilir ve seçim yapabilirsiniz. Örneğin, günlüklerinizi Azure dosyalarında depolu, günlük yolunuz olur `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Bu yolda her bir paket çalıştırması için bir alt klasör oluşturulur, SSIS paketini Yürüt etkinlik çalıştırma KIMLIĞI ve her beş dakikada bir günlük dosyası oluşturulur. 
   
   1. **Etki alanı**, **Kullanıcı adı** ve **parola** kutularına değerlerini girerek günlük klasörünüze erişmek için kimlik bilgilerini belirtin. Örneğin, günlüklerinizi Azure dosyalarında depolarsanız, etki alanı, `Azure` Kullanıcı adı ise `<storage account name>` ve parola olur `<storage account key>` . Alternatif olarak, Azure Key Vault depolanan gizli dizileri değerler olarak kullanabilirsiniz (yukarıya bakın).
   
Daha önce bahsedilen tüm UNC yolları için, tam nitelikli dosya adı 260 karakterden kısa olmalıdır. Dizin adı 248 karakterden kısa olmalıdır.

##### <a name="package-location-package-store"></a>Paket konumu: paket deposu

Paket **deposu** ' nu paket konumunuz olarak seçerseniz, aşağıdaki adımları izleyin.

![Ayarlar sekmesinde Özellikler ayarlama-paket deposu](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. **Paket deposu adı** için Azure-SSIS IR bağlı var olan bir paket deposunu seçin.

   1. Paket `.dtsx` **yolu** kutusunda seçilen paket deposundan yolunu (olmadan) sağlayarak çalıştırılacak paketinizi belirtin. Seçilen paket deposu dosya sistemi/Azure dosyalarının üstünde ise, **dosya depolama alanını görüntüle**' yi seçerek paketinize gözatabilir ve seçim yapabilirsiniz; Aksi takdirde, yolunu biçiminde girebilirsiniz `<folder name>\<package name>` . Ayrıca, [eskı SSIS paket deposuna](/sql/integration-services/service/package-management-ssis-service)benzer SQL Server Management Studio (SSMS) aracılığıyla seçili paket deposuna yeni paketler aktarabilirsiniz. Daha fazla bilgi için bkz. [Azure-SSIS IR paketi depoları Ile SSIS paketlerini yönetme](./azure-ssis-integration-runtime-package-store.md).

   1. Paketinizi ayrı bir dosyada yapılandırırsanız, yapılandırma yolu kutusuna yapılandırma dosyanıza (ile) bir UNC yolu sağlamanız gerekir `.dtsConfig` .  **Dosya depolama alanını görüntüle** ' yi seçerek yapılandırmanıza gözatıp seçebilirsiniz veya yolunu el ile girebilirsiniz. Örneğin, yapılandırmanızı Azure dosyalarında depolukaldırırsanız, yolu olur `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Yapılandırma dosyanıza ayrı olarak erişmek için kimlik bilgilerini belirtmek isteyip istemediğinizi seçmek için **yapılandırma erişim kimlik bilgileri** onay kutusunu seçin. Seçilen paket deposu, Azure SQL yönetilen örneğiniz tarafından barındırılan SQL Server veritabanının (MSDB) üstünde olduğunda veya yapılandırma dosyanızı da depolayamadığında bu gereklidir.
   
      Daha önce paket yürütme kimlik bilgileriniz için değerler girdiyseniz ( **Windows kimlik doğrulaması** için), **paket yürütme kimlik bilgileri ile aynı** onay kutusunu seçerek bunları yeniden kullanabilirsiniz. Aksi takdirde, yapılandırma erişim kimlik bilgilerinizin değerlerini **etki alanı**, **Kullanıcı adı** ve **parola** kutularına girin. Örneğin, yapılandırmanızı Azure dosyalarında depolarsanız, etki alanı, `Azure` Kullanıcı adı `<storage account name>` ve parola olur `<storage account key>` . 

      Alternatif olarak, Azure Key Vault depolanan gizli dizileri değerler olarak kullanabilirsiniz. Bunu yapmak için, yanındaki **Azure Anahtar Kasası** onay kutusunu seçin. Mevcut Anahtar Kasası bağlı hizmetinizi seçin veya düzenleyin ya da yeni bir tane oluşturun. Daha sonra, değer için gizli adı ve sürümü seçin. Anahtar Kasası bağlı hizmetinizi oluştururken veya düzenlediğinizde, mevcut anahtar kasanızı seçebilir veya düzenleyebilir veya yeni bir tane oluşturabilirsiniz. Daha önce yapmadıysanız, anahtar kasanıza Data Factory yönetilen kimlik erişimi verdiğinizden emin olun. Gizli dizinizi doğrudan aşağıdaki biçimde de girebilirsiniz: `<key vault linked service name>/<secret name>/<secret version>` .

   1. Paketinizi SSDT aracılığıyla oluştururken **EncryptAllWithPassword** veya **EncryptSensitiveWithPassword** koruma düzeyini kullandıysanız, **şifreleme parolası** kutusuna parolanızın değerini girin. Alternatif olarak, Azure Key Vault depolanan bir gizli anahtarı, değeri olarak (yukarıya bakın) kullanabilirsiniz.
      
      **EncryptSensitiveWithUserKey** koruma düzeyini kullandıysanız, hassas değerleri yapılandırma dosyalarına ya da **SSIS parametreleri**, **bağlantı yöneticileri** veya **özellik geçersiz kılmalar** sekmelerine (aşağıya bakın) yeniden girin.
      
      **EncryptAllWithUserKey** koruma düzeyini kullandıysanız, bu desteklenmez. Paketinizi, SSDT veya komut satırı yardımcı programı aracılığıyla başka bir koruma düzeyi kullanacak şekilde yeniden yapılandırmanız gerekir `dtutil` . 

   1. **Günlüğe kaydetme düzeyi** için, paket yürütülemenize yönelik önceden tanımlanmış bir günlük kapsamı seçin. Bunun yerine özelleştirilmiş günlük adınızı girmek istiyorsanız **özelleştirilmiş** onay kutusunu seçin. 
   
   1. Paketinizdeki belirtime standart günlük sağlayıcılarını kullanarak paket yürütmelerinin daha fazla günlüğe kaydedilmesini istiyorsanız günlük **kaydı yolu** kutusuna UNC yolunu sağlayarak günlük klasörünüzü belirtin. **Dosya depolama alanını görüntüle** ' yi seçerek veya yolunu el ile girerek günlük klasörünüze gözatabilir ve seçim yapabilirsiniz. Örneğin, günlüklerinizi Azure dosyalarında depolu, günlük yolunuz olur `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Bu yolda her bir paket çalıştırması için bir alt klasör oluşturulur, SSIS paketini Yürüt etkinlik çalıştırma KIMLIĞI ve her beş dakikada bir günlük dosyası oluşturulur. 
   
   1. **Etki alanı**, **Kullanıcı adı** ve **parola** kutularına değerlerini girerek günlük klasörünüze erişmek için kimlik bilgilerini belirtin. Örneğin, günlüklerinizi Azure dosyalarında depolarsanız, etki alanı, `Azure` Kullanıcı adı ise `<storage account name>` ve parola olur `<storage account key>` . Alternatif olarak, Azure Key Vault depolanan gizli dizileri değerler olarak kullanabilirsiniz (yukarıya bakın).
   
Daha önce bahsedilen tüm UNC yolları için, tam nitelikli dosya adı 260 karakterden kısa olmalıdır. Dizin adı 248 karakterden kısa olmalıdır.

#### <a name="ssis-parameters-tab"></a>SSIS parametreleri sekmesi

SSIS paketi yürütme etkinliğinin **SSIS parametreleri** sekmesinde aşağıdaki adımları izleyin.

![SSIS parametreleri sekmesindeki özellikleri ayarlayın](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. Azure-SSIS IR çalışıyorsa, paket konumunuz olarak **Sssısdb** seçilidir ve **Ayarlar** sekmesinde **el ile girişler** onay kutusu işaretli değilse, seçili projenizdeki ve SSıSDB 'teki paketteki var olan SSIS parametreleri bunlara değer atamanız için görüntülenir. Aksi takdirde, bunlara el ile değer atamak için bunları tek tek girebilirsiniz. Bu koşulların ve paket yürütmenin başarılı olması için doğru girildiğinden emin olun. 
   
   1. Paketinizi SSDT ve **dosya sistemi (paket)** ve dosya sistemi **(proje)**, **katıştırılmış paket** veya paket **deposu** aracılığıyla oluşturduğunuzda **EncryptSensitiveWithUserKey** koruma düzeyini, paket konumunuz olarak kullandıysanız, bu sekmede bunlara değer atamak için de hassas parametrelerinizi yeniden girmeniz gerekir. 
   
Parametrelerinizi değer atarken, ifadeleri, işlevleri, Data Factory sistem değişkenlerini ve Data Factory işlem hattı parametrelerini veya değişkenlerini kullanarak dinamik içerik ekleyebilirsiniz.

Alternatif olarak, Azure Key Vault depolanan gizli dizileri değerler olarak kullanabilirsiniz. Bunu yapmak için, yanındaki **Azure Anahtar Kasası** onay kutusunu seçin. Mevcut Anahtar Kasası bağlı hizmetinizi seçin veya düzenleyin ya da yeni bir tane oluşturun. Daha sonra, değer için gizli adı ve sürümü seçin. Anahtar Kasası bağlı hizmetinizi oluştururken veya düzenlediğinizde, mevcut anahtar kasanızı seçebilir veya düzenleyebilir veya yeni bir tane oluşturabilirsiniz. Daha önce yapmadıysanız, anahtar kasanıza Data Factory yönetilen kimlik erişimi verdiğinizden emin olun. Gizli dizinizi doğrudan aşağıdaki biçimde de girebilirsiniz: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="connection-managers-tab"></a>Bağlantı yöneticileri sekmesi

SSIS paketi yürütme etkinliğinin **bağlantı yöneticileri** sekmesinde aşağıdaki adımları izleyin.

![Bağlantı yöneticileri sekmesindeki özellikleri ayarla](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. Azure-SSIS IR çalışıyorsa, paket konumunuz olarak **Sssısdb** seçilidir ve **Ayarlar** sekmesinde **el ile girişler** onay kutusu işaretli değilse, özellikleri için değerleri atamanız amacıyla, seçtiğiniz projedeki ve SSISDB 'teki paketlerdeki mevcut bağlantı yöneticileri görüntülenir. Aksi takdirde, özelliklerine el ile değer atamak için bunları tek tek girebilirsiniz. Bu koşulların ve paket yürütmenin başarılı olması için doğru girildiğinden emin olun. 
   
      SSDT 'de içeren paketi açarak herhangi bir bağlantı Yöneticisi için doğru **kapsam**, **ad** ve **özellik** adlarını elde edebilirsiniz. Paket açıldıktan sonra, SSDT 'nin **Özellikler** penceresinde tüm özelliklerinin adlarını ve değerlerini göstermek için ilgili bağlantı Yöneticisi ' ni seçin. Bu bilgiyle, çalışma zamanında herhangi bir bağlantı Yöneticisi özelliklerinin değerlerini geçersiz kılabilirsiniz. 

      ![SSDT 'den bağlantı Yöneticisi özelliklerini al](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      Örneğin, SSDT 'de orijinal paketinizi değiştirmeden, çalışma zamanında var olan bağlantı yöneticilerinde bulunan **Connectbyproxy**, **ConnectionString** ve **Connectusingmanagedıdentity** özelliklerinin değerlerini geçersiz kılarak ADF 'de bulunan şirket içi ŞIRKET içi SQL Server veri akışları 'nı ADF 'de SSIS IR üzerinde çalışan şirket içi veri akışlarına dönüştürebilirsiniz.
      
      Bu çalışma zamanı geçersiz kılma işlemleri, Şirket içindeki verilere erişirken SSIS IR için proxy olarak Self-Hosted IR (SHıR) etkinleştirebilir. bkz. ksıs [IR için ara sunucu olarak](./self-hosted-integration-runtime-proxy-ssis.md)Ksıs 'yi yapılandırma ve Azure SQL veritabanı/yönetilen örnek BAĞLANTıLARı, ADF yönetilen kimliği ile Azure ACTIVE DIRECTORY (AAD) kimlik doğrulamasını sağlayan en son MSOLEDBSQL sürücüsünü kullanarak Azure SQL veritabanı/yönetilen örnek bağlantıları, bkz. [OLEDB için ADF yönetilen kimliğiyle AAD kimlik doğrulamasını yapılandırma](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

      ![Bağlantı yöneticileri sekmesinde SSDT 'den özellikler ayarlama](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. Paketinizi SSDT ve **dosya sistemi (paket) ile oluşturduğunuzda (paket)**, **dosya sistemi (proje)**, **katıştırılmış paket** veya paket **deposu** için **EncryptSensitiveWithUserKey** koruma düzeyini, paket konumunuz olarak kullandıysanız, bu sekmede bunlara değer atamak için de hassas bağlantı Yöneticisi özelliklerinizi yeniden girmeniz gerekir. 

Bağlantı Yöneticisi özelliklerine değer atarken, ifadeleri, işlevleri, Data Factory sistem değişkenlerini ve Data Factory işlem hattı parametrelerini veya değişkenlerini kullanarak dinamik içerik ekleyebilirsiniz. 

Alternatif olarak, Azure Key Vault depolanan gizli dizileri değerler olarak kullanabilirsiniz. Bunu yapmak için, yanındaki **Azure Anahtar Kasası** onay kutusunu seçin. Mevcut Anahtar Kasası bağlı hizmetinizi seçin veya düzenleyin ya da yeni bir tane oluşturun. Daha sonra, değer için gizli adı ve sürümü seçin. Anahtar Kasası bağlı hizmetinizi oluştururken veya düzenlediğinizde, mevcut anahtar kasanızı seçebilir veya düzenleyebilir veya yeni bir tane oluşturabilirsiniz. Daha önce yapmadıysanız, anahtar kasanıza Data Factory yönetilen kimlik erişimi verdiğinizden emin olun. Gizli dizinizi doğrudan aşağıdaki biçimde de girebilirsiniz: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="property-overrides-tab"></a>Özellik geçersiz kılmaları sekmesi

Özelliği, SSIS paketi yürütme etkinliğinin **geçersiz kılmaları** sekmesinde aşağıdaki adımları izleyin.

![Özellik geçersiz kılmaları sekmesinde özellikleri ayarla](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. Seçtiğiniz paketteki mevcut özelliklerin yollarını, el ile değer atamak için birer birer girin. Bu koşulların ve paket yürütmenin başarılı olması için doğru girildiğinden emin olun. Örneğin, Kullanıcı değişkeninizin değerini geçersiz kılmak için yolunu aşağıdaki biçimde girin: `\Package.Variables[User::<variable name>].Value` . 

      SSDT 'de bulunan paketi açarak herhangi bir paket özelliği için doğru **ÖZELLIK yolunu** elde edebilirsiniz. Paket açıldıktan sonra, SSDT 'nin **Özellikler** penceresinde denetim akışı ve **Konfigürasyonlar** özelliğini seçin. Ardından, **yapılandırma** özelliğinin yanındaki üç nokta (**...**) düğmesini seçerek paket yapılandırma Düzenleyicisi [' ni paket dağıtım modelinde oluşturmak](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations)için kullanılan **paket yapılandırma düzenleyicisini** açın. 

      ![SSDT-Configurations özelliğinden paket özelliklerini al](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      Paket **yapılandırmaları Düzenleyicisi**' nde paket **yapılandırmalarını etkinleştir** onay kutusunu ve **Ekle...** düğmesini seçerek **paket Yapılandırma Sihirbazı**'nı açın. 
      
      **Paket Yapılandırma sihirbazında**, **yapılandırma türü** açılan menüsünde **XML yapılandırma dosyası** öğesini seçin ve **yapılandırma ayarlarını doğrudan belirtin** düğmesini seçin, yapılandırma dosyanızın adını girin ve **İleri >** düğmesini seçin. 

      ![SSDT-Configurations düzenleyicisinden paket özelliklerini al](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      Son olarak, yolunu istediğiniz paket özelliklerini ve **sonraki >** düğmesini seçin.  Artık, istediğiniz paket özelliği yollarını görebilir, kopyalayabilir & yapıştırabilir ve bunları yapılandırma dosyanıza kaydedebilirsiniz. Bu bilgiyle, çalışma zamanında herhangi bir paket özelliklerinin değerlerini geçersiz kılabilirsiniz. 

      ![SSDT yapılandırma sihirbazından paket özelliklerini al](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. Paketinizi SSDT ve **dosya sistemi (paket)** ile oluşturduğunuz sırada **EncryptSensitiveWithUserKey** koruma düzeyini, **dosya sistemi (proje)**, **katıştırılmış paket** veya paket **deposu** olarak kullandıysanız, bu sekmede bunlara değer atamak için de hassas paket özelliklerinizi yeniden girmeniz gerekir. 
   
Paket özelliklerine değer atarken, ifadeleri, işlevleri, Data Factory sistem değişkenlerini ve Data Factory işlem hattı parametrelerini veya değişkenlerini kullanarak dinamik içerik ekleyebilirsiniz.

Yapılandırma dosyalarında ve **SSIS parametreleri** sekmesinde atanan değerler, **bağlantı yöneticileri** veya **özellik geçersiz kılma** sekmeleri kullanılarak geçersiz kılınabilir. **Bağlantı yöneticileri** sekmesinde atanan değerler ayrıca **özellik geçersiz kılma** sekmesi kullanılarak geçersiz kılınabilir.

İşlem hattı yapılandırmasını doğrulamak için araç çubuğunda **Doğrula** ' yı seçin. İşlem **hattı doğrulama raporunu** kapatmak için öğesini seçin **>>** .

İşlem hattını Data Factory yayımlamak için **Tümünü Yayımla**' yı seçin. 

### <a name="run-the-pipeline"></a>İşlem hattını çalıştırma
Bu adımda bir işlem hattı çalıştırması tetiklersiniz. 

1. Bir işlem hattı çalıştırması tetiklemek için, araç çubuğunda **tetikleyici** ' i seçin ve **Şimdi Tetikle**' yi seçin. 

   ![Şimdi tetikle](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. **İşlem Hattı Çalıştırma** penceresinde **Son**’u seçin. 

### <a name="monitor-the-pipeline"></a>İşlem hattını izleme

1. Soldaki **İzleyici** sekmesine geçin. İşlem hattı çalıştırmasını ve durumunu **çalıştırma başlangıç** saati gibi diğer bilgilerle birlikte görürsünüz. Görünümü yenilemek için **Yenile**’yi seçin.

   ![İşlem hattı çalıştırmaları](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. **Eylemler** sütununda **Etkinlik Çalıştırma İşlemlerini Görüntüle**’yi seçin. İşlem hattının yalnızca bir etkinliği olduğundan yalnızca bir etkinlik çalıştırması görürsünüz. Bu, SSIS paketi yürütme etkinliğidir.

   ![Etkinlik çalıştırmaları](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Paketin yürütüldüğünü doğrulamak için SQL sunucunuzdaki SSıSDB veritabanında aşağıdaki sorguyu çalıştırın. 

   ```sql
   select * from catalog.executions
   ```

   ![Paket yürütmelerini doğrulama](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Ayrıca, işlem hattı etkinliğinin çıkışından SSıSDB yürütme KIMLIĞINI alabilir ve SQL Server Management Studio daha kapsamlı yürütme günlüklerini ve hata iletilerini denetlemek için KIMLIĞINI kullanabilirsiniz.

   ![Yürütme KIMLIĞINI alın.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>İşlem hattını bir tetikleyici ile zamanlama

İşlem hattınızı saatlik veya günlük gibi bir zamanlamaya göre çalışacak şekilde işlem hattı için de zamanlanmış bir tetikleyici oluşturabilirsiniz. Bir örnek için bkz. [Data Factory-Data Factory Kullanıcı arabirimi oluşturma](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>PowerShell ile bir paket çalıştırma
Bu bölümde, SSIS paketinizi çalıştıran bir SSIS paketi yürütme etkinliği ile Data Factory işlem hattı oluşturmak için Azure PowerShell kullanırsınız. 

[Azure PowerShell yüklemek ve yapılandırmak için](/powershell/azure/install-az-ps)adım adım yönergeleri izleyerek en son Azure PowerShell modüllerini yükler.

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Azure-SSIS IR ile veri fabrikası oluşturma
Zaten Azure-SSIS IR sağlanmış olan mevcut bir veri fabrikasını kullanabilir veya Azure-SSIS IR ile yeni bir veri fabrikası oluşturmuş olabilirsiniz. Öğreticideki Adım adım yönergeleri izleyin [: PowerShell aracılığıyla SSIS paketlerini Azure 'A dağıtma](./tutorial-deploy-ssis-packages-azure-powershell.md).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS paketi yürütme etkinliği ile işlem hattı oluşturma 
Bu adımda, SSIS paketi yürütme etkinliğine sahip bir işlem hattı oluşturacaksınız. Etkinlik SSSıS paketinizi çalıştırır. 

1. `RunSSISPackagePipeline.json` `C:\ADF\RunSSISPackage` Aşağıdaki örneğe benzer içeriğe sahip klasörde ADLı bir JSON dosyası oluşturun.

   > [!IMPORTANT]
   > Dosyayı kaydetmeden önce nesne adlarını, açıklamaları ve yolları, özellik veya parametre değerlerini, parolaları ve diğer değişken değerlerini değiştirin. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Dosya sistemi/Azure dosyalarında depolanan paketleri yürütmek için, paketinizin değerlerini ve günlük konumu özelliklerinizi aşağıdaki gibi girin:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Dosya sistemi/Azure dosyalarında depolanan projelerde paket yürütmek için, aşağıdaki gibi paket konumu özelliklerinizi değerlerini girin:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Katıştırılmış paketleri yürütmek için, paket konumu özelliklerinizi aşağıdaki şekilde değerleri girin:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   Paket depolarında depolanan paketleri yürütmek için, paketinizin ve yapılandırma konumu özellikleri için değerleri aşağıdaki gibi girin:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. Azure PowerShell ' de `C:\ADF\RunSSISPackage` klasöre geçin.

3. **Runssispackagepipeline** işlem hattını oluşturmak için **set-AzDataFactoryV2Pipeline** cmdlet 'ini çalıştırın.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Örnek çıktı aşağıda verilmiştir:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>İşlem hattını çalıştırma
İşlem hattını çalıştırmak için **Invoke-AzDataFactoryV2Pipeline** cmdlet 'ini kullanın. Cmdlet, gelecekte izlemek üzere işlem hattı çalıştırma kimliğini döndürür.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>İşlem hattını izleme

İşlem hattı çalıştırma durumunu, verileri kopyalama işlemi tamamlanıncaya kadar sürekli olarak denetlemek için aşağıdaki PowerShell betiğini çalıştırın. Aşağıdaki betiği kopyalayıp PowerShell penceresine yapıştırın ve ENTER ' u seçin. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Ardışık düzeni Azure portal kullanarak da izleyebilirsiniz. Adım adım yönergeler için bkz. işlem hattını [izleme](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>İşlem hattını bir tetikleyici ile zamanlama
Önceki adımda, işlem hattını isteğe bağlı olarak çalıştırdınız. İşlem hattını saatlik veya günlük gibi bir zamanlamaya göre çalıştırmak için bir zamanlama tetikleyicisi de oluşturabilirsiniz.

1. `MyTrigger.json`Aşağıdaki içeriğe sahip klasörde adlı BIR JSON dosyası oluşturun `C:\ADF\RunSSISPackage` : 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. Azure PowerShell ' de `C:\ADF\RunSSISPackage` klasöre geçin.
1. Tetikleyiciyi oluşturan **set-AzDataFactoryV2Trigger** cmdlet 'ini çalıştırın. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Varsayılan olarak, tetikleyici durdurulmuş durumda olur. **Start-AzDataFactoryV2Trigger** cmdlet 'ini çalıştırarak tetikleyiciyi başlatın. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Tetikleyici, **Get-AzDataFactoryV2Trigger** cmdlet 'ini çalıştırarak başlatıldığını onaylayın. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Sonraki saatten sonra aşağıdaki komutu çalıştırın. Örneğin, geçerli saat 3:25 PM UTC ise, komutu 4 PM UTC 'de çalıştırın. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Paketin yürütüldüğünü doğrulamak için SQL sunucunuzdaki SSıSDB veritabanında aşağıdaki sorguyu çalıştırın. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki blog gönderisine bakın:
- [Azure Data Factory işlem hatlarında SSIS etkinlikleriyle ETL/ELT iş akışlarınızı modernleştirin ve genişletme](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)