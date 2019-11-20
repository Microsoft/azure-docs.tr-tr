---
title: SSIS paketi yürütme etkinliği ile bir SSIS paketi çalıştırma-Azure
description: Bu makalede, SSIS paketi yürütme etkinliğini kullanarak bir Azure Data Factory işlem hattında SQL Server Integration Services (SSIS) paketinin nasıl çalıştırılacağı açıklanmaktadır.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 11/14/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ddb7cd06934c85243717dd2a34dc99bae582b6fa
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122972"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Azure Data Factory 'de SSIS paketi yürütme etkinliğiyle bir SSIS paketi çalıştırın
Bu makalede, SSIS paketi yürütme etkinliğini kullanarak bir Azure Data Factory işlem hattında SQL Server Integration Services (SSIS) paketinin nasıl çalıştırılacağı açıklanmaktadır. 

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Öğreticide adım adım yönergeleri izleyerek bir Azure-SSIS tümleştirme çalışma zamanı (IR) oluşturun [: sağlama Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Azure portal bir paket çalıştırın
Bu bölümde, SSIS paketinizi çalıştıran bir SSIS paketi yürütme etkinliği ile Data Factory işlem hattı oluşturmak için Data Factory Kullanıcı arabirimini (UI) veya uygulamayı kullanırsınız.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS paketi yürütme etkinliği ile işlem hattı oluşturma
Bu adımda, bir işlem hattı oluşturmak için Data Factory Kullanıcı arabirimini veya uygulamayı kullanırsınız. İşlem hattına bir SSIS paketi yürütme etkinliği ekleyin ve bunu SSIS paketinizi çalıştıracak şekilde yapılandırın. 

1. Azure portal Data Factory genel bakış veya giriş sayfasında, Data Factory Kullanıcı arabirimini veya uygulamayı ayrı bir sekmede başlatmak için **yazar & İzleyici** kutucuğunu seçin. 

   ![Data Factory giriş sayfası](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   **Kullanmaya başlama** sayfasında **İşlem hattı oluştur** seçeneğini belirleyin. 

   ![Başlarken sayfası](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. **Etkinlikler** araç kutusunda **genel**' i genişletin. Sonra bir **SSIS paketi yürütme etkinliğini işlem** hattı tasarımcı yüzeyine sürükleyin. 

   ![Bir SSIS paketi yürütme etkinliğini tasarımcı yüzeyine sürükleyin](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. SSIS paketi yürütme etkinliğinin **genel** sekmesinde, etkinlik için bir ad ve açıklama girin. İsteğe bağlı **zaman aşımını** ve **yeniden deneme** değerlerini ayarlayın.

   ![Genel sekmesindeki özellikleri ayarla](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. SSIS paketi yürütme etkinliğinin **Ayarlar** sekmesinde, paketinizi çalıştırmak istediğiniz Azure-SSIS IR seçin. Paketiniz veri depolarına erişmek için Windows kimlik doğrulaması kullanıyorsa (örneğin, SQL sunucuları veya şirket içi veya Azure dosyaları için dosya paylaşımları), **Windows kimlik doğrulaması** onay kutusunu seçin. Paket yürütme kimlik bilgilerinizin değerlerini **etki alanı**, **Kullanıcı adı**ve **parola** kutularına girin. 

    Alternatif olarak, Azure Anahtar Kasanızda depolanan gizli dizileri kendi değerleri olarak kullanabilirsiniz. Bunu yapmak için, ilgili kimlik bilgisinin yanındaki **Azure Anahtar Kasası** onay kutusunu seçin. Mevcut Anahtar Kasası bağlı hizmetinizi seçin veya düzenleyin ya da yeni bir tane oluşturun. Ardından, kimlik bilgileri değerinin gizli adını veya sürümünü seçin.

    Anahtar Kasası bağlı hizmetinizi oluştururken veya düzenlediğinizde, mevcut anahtar kasanızı seçebilir veya düzenleyebilir veya yeni bir tane oluşturabilirsiniz. Daha önce yapmadıysanız, anahtar kasanıza Data Factory yönetilen kimlik erişimi verdiğinizden emin olun. Gizli dizilerinizi doğrudan şu biçimde de girebilirsiniz: `<Key vault linked service name>/<secret name>/<secret version>`. Paketinizin çalışması için 32 bit çalışma zamanı gerekiyorsa, **32 bit çalışma zamanı** onay kutusunu seçin.

   **Paket konumu**Için **SSISDB**, **dosya sistemi (paket)** , **dosya sistemi (proje)** veya **katıştırılmış paket**' i seçin. Bir Azure SQL veritabanı sunucusu veya yönetilen örnek tarafından barındırılan SSIS Kataloğu (SSSıSDB) ile sağlanmışsa, Azure-SSIS IR otomatik olarak seçilen paket konumunuz olarak **Sssısdb** ' yi seçerseniz, dağıtılan çalıştırılacak paketinizi belirtin SSSıSDB 'ye dönüştürür. 

    Azure-SSIS IR çalışıyorsa ve **el ile girdiler** onay kutusu SILINIRSE, SSISDB 'den var olan klasörlerinizi, projelerinizi, paketlerinizi veya ortamlarınızı göz atın ve seçin. Yeni eklenen klasörlerinizi, projelerinizi, paketlerinizi veya ortamlarınızı, göz atma ve seçim için kullanılabilir olmaları için SSıSDB 'den getirmek için **Yenile** ' yi seçin. Paket yürütmelerinin ortamlarına gözatıp seçmek için, bu ortamları SSSıSDB altındaki aynı klasörlerden başvuru olarak eklemek üzere projelerinizi önceden yapılandırmanız gerekir. Daha fazla bilgi için bkz. [SSIS ortamlarını oluşturma ve eşleme](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   **Günlüğe kaydetme düzeyi**için, paket yürütülemenize yönelik önceden tanımlanmış bir günlük kapsamı seçin. Bunun yerine özelleştirilmiş günlük adınızı girmek istiyorsanız **özelleştirilmiş** onay kutusunu seçin. 

   ![Ayarlar sekmesinde özellikleri ayarlama-otomatik](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Azure-SSIS IR çalışmıyorsa veya **el ile girdiler** onay kutusu IŞARETLIYSE, SSISDB 'den paketinize ve ortam yollarınızı doğrudan şu biçimlerde girin: `<folder name>/<project name>/<package name>.dtsx` ve `<folder name>/<environment name>`.

   ![Ayarlar sekmesinde özellikleri ayarlama-El Ile](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Azure-SSIS IR SSSıSDB olmadan sağlanmışsa otomatik olarak seçilen paket konumunuz olarak **dosya sistemi (paket)** seçeneğini belirlerseniz, paket dosyanıza bir evrensel adlandırma KURALı (UNC) yolu sağlayarak çalıştırılacak paketinizi belirtin (@no __t_1_) **paket yolu** kutusunda.`.dtsx` Örneğin, paketinizi Azure dosyalarında depolarsanız, paket yolu `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Paketinizi ayrı bir dosyada yapılandırırsanız, yapılandırma **yolu** kutusunda yapılandırma dosyanıza (`.dtsConfig`) bir UNC yolu da sağlamanız gerekir. Örneğin, yapılandırmanızı Azure dosyalarında depolarsanız, yapılandırma yolu `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Ayarlar sekmesinde özellikleri ayarlama-El Ile](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Paket konumunuz olarak **dosya sistemi (proje)** seçeneğini belirlerseniz, proje **yolu** kutusunda proje dosyanıza (`.ispac`) ve **paket adındaki** bir paket dosyasına (`.dtsx`) bir UNC yolu sağlayarak çalıştırılacak paketinizi belirtin Kutudaki. Örneğin, projenizi Azure dosyalarında depolarsanız, proje yolu `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Ayarlar sekmesinde özellikleri ayarlama-El Ile](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Ardından, projenize, paketinize veya yapılandırma dosyalarınıza erişmek için kimlik bilgilerini belirtin. Daha önce paket yürütme kimlik bilgileriniz için değerler girdiyseniz (öncekini gör), **paket yürütme kimlik bilgileri Ile aynı** onay kutusunu seçerek bunları yeniden kullanabilirsiniz. Aksi takdirde, paket erişim kimlik bilgilerinizin değerlerini **etki alanı**, **Kullanıcı adı**ve **parola** kutularına girin. Örneğin, projenizi, paketinizi veya yapılandırmayı Azure dosyalarında depolarsanız, etki alanı `Azure`, Kullanıcı adı `<storage account name>`ve parola `<storage account key>`olur. 

   Alternatif olarak, anahtar kasasında depolanan gizli dizileri değerler olarak kullanabilirsiniz (öncekine bakın). Bu kimlik bilgileri, paket ve alt paketlerinize, tüm kendi yolundan veya aynı projeden, ayrıca paketleriniz içinde belirtilenler dahil olmak üzere tüm yapılandırma ve alt paketlere erişmek için kullanılır. 

   Paket konumunuz olarak **katıştırılmış paket** ' i seçerseniz, dosyayı çalıştırmak veya bir dosya klasöründen belirtilen kutuya **yüklemek** için paketinizi sürükleyip bırakın. Paketinizin otomatik olarak sıkıştırılması ve etkinlik yüküne katıştırılması gerekir. Ekli olduktan sonra, paketini daha sonra düzenlemeniz için **indirebilirsiniz** . Ayrıca, birden çok etkinlikte kullanılabilecek bir işlem hattı parametresine atayarak gömülü paketinizi **parametreleştirebilirsiniz** , bu nedenle işlem hattı yükünüzü en iyi duruma getirebilirsiniz. Katıştırılmış paketinizin tümü şifrelenmemişse ve paket yürütme görevinin kullanımını tespit ettik, **paket görevi çalıştır** onay kutusu otomatik olarak seçilir ve dosya sistemi başvurularına sahip ilgili alt paketler otomatik olarak eklenir. Paket yürütme görevinin kullanımını algılayamıyoruz, **paket görevini Yürüt** onay kutusunu el ile seçmeniz ve ilgili alt paketleri dosya sistemi başvurularına tek bir tane ekleyerek de eklemeniz gerekir. Alt paketler SQL Server başvurular kullanıyorsa, lütfen SQL Server Azure-SSIS IR tarafından erişilebildiğinden emin olun.  Alt paketlere yönelik proje başvurularının kullanımı Şu anda desteklenmiyor.
   
   ![Ayarlar sekmesinde özellikleri ayarlama-El Ile](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Paketinizi SQL Server Veri Araçları aracılığıyla oluştururken **EncryptAllWithPassword** veya **EncryptSensitiveWithPassword** koruma düzeyini kullandıysanız, **şifreleme parolası** kutusuna parolanızın değerini girin. Alternatif olarak, anahtar kasasında depolanan bir gizli anahtarı değer olarak kullanabilirsiniz (öncekine bakın). **EncryptSensitiveWithUserKey** koruma düzeyini kullandıysanız, hassas değerleri yapılandırma dosyalarına ya da **SSIS parametreleri**, **bağlantı yöneticileri**veya **özellik geçersiz kılmalar** sekmelerinde yeniden girin (daha sonra bkz.). 

   **EncryptAllWithUserKey** koruma düzeyini kullandıysanız, bu desteklenmez. Paketinizi SQL Server Veri Araçları veya `dtutil` komut satırı yardımcı programı aracılığıyla başka bir koruma düzeyi kullanacak şekilde yeniden yapılandırmanız gerekir. 
   
   **Günlüğe kaydetme düzeyi**için, paket yürütülemenize yönelik önceden tanımlanmış bir günlük kapsamı seçin. Bunun yerine özelleştirilmiş günlük adınızı girmek istiyorsanız **özelleştirilmiş** onay kutusunu seçin. Paketinizdeki belirtime standart günlük sağlayıcılarını kullanarak paket yürütmelerinin daha fazla günlüğe kaydedilmesini istiyorsanız günlük **kaydı yolu** kutusuna UNC yolunu sağlayarak günlük klasörünüzü belirtin. Örneğin, günlüklerinizi Azure dosyalarında depolarsanız, günlük yolunuz `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Bu yolda, her bir paket çalıştırması için bir alt klasör oluşturulur ve, her beş dakikada bir günlük dosyası oluşturulan SSIS paketini Çalıştır etkinlik çalıştırma KIMLIĞIYLE adlandırılır. 
   
   Son olarak, günlük klasörünüze erişmek için kimlik bilgilerini belirtin. Daha önce paket erişim kimlik bilgilerinizin değerlerini girdiyseniz (öncekine bakın), **paket erişimi kimlik bilgileri Ile aynı** onay kutusunu seçerek bunları yeniden kullanabilirsiniz. Aksi takdirde, oturum açma erişim kimlik bilgilerinizin değerlerini **etki alanı**, **Kullanıcı adı**ve **parola** kutularına girin. Örneğin, günlüklerinizi Azure dosyalarında depolarsanız, etki alanı `Azure`, Kullanıcı adı `<storage account name>`ve parola `<storage account key>`olur. 

    Alternatif olarak, anahtar kasasında depolanan gizli dizileri değerler olarak kullanabilirsiniz (öncekine bakın). Bu kimlik bilgileri günlüklerinizi depolamak için kullanılır. 
   
   Daha önce bahsedilen tüm UNC yolları için, tam nitelikli dosya adı 260 karakterden kısa olmalıdır. Dizin adı 248 karakterden kısa olmalıdır.

1. SSIS paketi yürütme etkinliğinin **SSIS parametreleri** sekmesinde, Azure-SSIS IR çalışıyorsa, paket konumunuz olarak **sssısdb** seçilidir ve **Ayarlar** sekmesinde **el ile girişler** onay kutusu işaretli değilse, var olan SSIS Seçtiğiniz proje veya pakette SSıSDB 'lerden parametreler, bunlara değer atamanız için görüntülenir. Aksi takdirde, bunlara el ile değer atamak için bunları tek tek girebilirsiniz. Bu koşulların ve paket yürütmenin başarılı olması için doğru girildiğinden emin olun. 
   
   Paketinizi SQL Server Veri Araçları ve **dosya sistemi (paket)** veya **dosya sistemi (proje)** aracılığıyla oluşturduğunuzda **EncryptSensitiveWithUserKey** koruma düzeyini, paket konumunuz olarak kullandıysanız, ayrıca yeniden girmeniz gerekir yapılandırma dosyalarında veya bu sekmede bunlara değer atamak için hassas parametreleriniz. 
   
   Parametrelerinizi değer atarken, ifadeleri, işlevleri, Data Factory sistem değişkenlerini ve Data Factory işlem hattı parametrelerini veya değişkenlerini kullanarak dinamik içerik ekleyebilirsiniz. Alternatif olarak, anahtar kasasında depolanan gizli dizileri değerler olarak kullanabilirsiniz (öncekine bakın).

   ![SSIS parametreleri sekmesindeki özellikleri ayarlayın](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. SSIS paketi yürütme etkinliğinin **bağlantı yöneticileri** sekmesinde, Azure-SSIS IR çalışıyorsa, paket konumunuz olarak **sssısdb** seçilidir ve **Ayarlar** sekmesinde **el ile girişler** onay kutusu işaretli değilse, mevcut seçtiğiniz projedeki veya paketteki SSSıSDB 'teki bağlantı yöneticileri, değerlerini özelliklerine atamanız için görüntülenir. Aksi takdirde, özelliklerine el ile değer atamak için bunları tek tek girebilirsiniz. Bu koşulların ve paket yürütmenin başarılı olması için doğru girildiğinden emin olun. 
   
   Paketinizi SQL Server Veri Araçları ve **dosya sistemi (paket)** veya **dosya sistemi (proje)** aracılığıyla oluşturduğunuzda **EncryptSensitiveWithUserKey** koruma düzeyini, paket konumunuz olarak kullandıysanız, ayrıca yeniden girmeniz gerekir hassas bağlantı Yöneticisi özelliklerinizi, yapılandırma dosyalarında veya bu sekmede bunlara değer atamaya yönelik özelliklerdir. 
   
   Bağlantı Yöneticisi özelliklerine değer atarken, ifadeleri, işlevleri, Data Factory sistem değişkenlerini ve Data Factory işlem hattı parametrelerini veya değişkenlerini kullanarak dinamik içerik ekleyebilirsiniz. Alternatif olarak, anahtar kasasında depolanan gizli dizileri değerler olarak kullanabilirsiniz (öncekine bakın).

   ![Bağlantı yöneticileri sekmesindeki özellikleri ayarla](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. **Özellik geçersiz kılmaları özelliği** , SSIS paketi yürütme etkinliğinin varolan özelliklerinin yollarını, el ile değer atamak için tek tek bir seçtiğiniz pakette bir tane olarak girin. Bu koşulların ve paket yürütmenin başarılı olması için doğru girildiğinden emin olun. Örneğin, Kullanıcı değişkeninizin değerini geçersiz kılmak için yolunu şu biçimde girin: `\Package.Variables[User::<variable name>].Value`. 
   
   Paketinizi SQL Server Veri Araçları ve **dosya sistemi (paket)** veya **dosya sistemi (proje)** aracılığıyla oluşturduğunuzda **EncryptSensitiveWithUserKey** koruma düzeyini, paket konumunuz olarak kullandıysanız, ayrıca yeniden girmeniz gerekir hassas özelliklerinizi yapılandırma dosyalarında veya bu sekmede bunlara değer atamak için. 
   
   Özelliklerine değer atarken, ifadeleri, işlevleri, Data Factory sistem değişkenlerini ve Data Factory işlem hattı parametrelerini veya değişkenlerini kullanarak dinamik içerik ekleyebilirsiniz.

   ![Özellik geçersiz kılmaları sekmesinde özellikleri ayarla](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Yapılandırma dosyalarında ve **SSIS parametreleri** sekmesinde atanan değerler, **bağlantı yöneticileri** veya **özellik geçersiz kılma** sekmeleri kullanılarak geçersiz kılınabilir. **Bağlantı yöneticileri** sekmesinde atanan değerler ayrıca **özellik geçersiz kılma** sekmesi kullanılarak geçersiz kılınabilir.

1. İşlem hattı yapılandırmasını doğrulamak için araç çubuğunda **Doğrula** ' yı seçin. İşlem **hattı doğrulama raporunu**kapatmak için **>>** ' yi seçin.

1. İşlem hattını Data Factory yayımlamak için **Tümünü Yayımla**' yı seçin. 

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
Zaten Azure-SSIS IR sağlanmış olan mevcut bir veri fabrikasını kullanabilir veya Azure-SSIS IR ile yeni bir veri fabrikası oluşturmuş olabilirsiniz. Öğreticideki Adım adım yönergeleri izleyin [: PowerShell aracılığıyla SSIS paketlerini Azure 'A dağıtma](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS paketi yürütme etkinliği ile işlem hattı oluşturma 
Bu adımda, SSIS paketi yürütme etkinliğine sahip bir işlem hattı oluşturacaksınız. Etkinlik SSSıS paketinizi çalıştırır. 

1. Aşağıdaki örneğe benzer içeriğe sahip *C:\adf\runssispackage* klasöründe *Runssıspackagepipeline. JSON* adlı bir JSON dosyası oluşturun.

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
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
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

   Dosya sistemlerinde, dosya paylaşımlarında veya Azure dosyalarında depolanan paketleri yürütmek için, paketinizin ve günlük konumu özellikleri için değerleri aşağıdaki gibi girin:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
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
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
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

   Dosya sistemlerinde, dosya paylaşımlarında veya Azure dosyalarında depolanan projeler içindeki paketleri yürütmek için, paket konumu özelliği için değerleri aşağıdaki gibi girin:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
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

   Katıştırılmış paketleri yürütmek için, paket konumu özelliği için değerleri aşağıdaki gibi girin:

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

2. Azure PowerShell, *C:\adf\runssispackage* klasörüne geçin.

3. **Runssispackagepipeline**işlem hattını oluşturmak için **set-AzDataFactoryV2Pipeline** cmdlet 'ini çalıştırın.

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

1. *C:\adf\runssispackage* klasöründe aşağıdaki Içeriğe sahip *mytrigger. JSON* adlı bir JSON dosyası oluşturun: 
        
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
    
1. Azure PowerShell, *C:\adf\runssispackage* klasörüne geçin.
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
