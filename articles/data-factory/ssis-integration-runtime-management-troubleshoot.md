---
title: SSIS Integration Runtime yönetimi sorunlarını giderme
description: Bu makale, SSIS Integration Runtime (SSIS IR) yönetim sorunları için sorun giderme kılavuzu sağlar
ms.service: data-factory
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 917bcce641cfd5178f868e4338354edb78062ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361858"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Azure Data Factory 'de SSIS Integration Runtime yönetimi sorunlarını giderme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, SSIS IR olarak da bilinen Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) içindeki yönetim sorunlarına yönelik sorun giderme kılavuzu sunulmaktadır.

## <a name="overview"></a>Genel Bakış

SSIS IR sağlama veya sağlamayı kaldırma sırasında herhangi bir sorunla karşılaşırsanız, Microsoft Azure Data Factory portalında bir hata iletisi veya bir PowerShell cmdlet 'inden döndürülen bir hata görürsünüz. Hata her zaman, ayrıntılı bir hata iletisiyle birlikte hata kodu biçiminde görüntülenir.

Hata kodu ınternalservererror ise, hizmette geçici sorunlar olur ve işlemi daha sonra yeniden denemeniz gerekir. Yeniden deneme işlemi yardım vermezse, Azure Data Factory destek ekibine başvurun.

Aksi takdirde, üç önemli dış bağımlılıklar hatalara neden olabilir: Azure SQL veritabanı veya Azure SQL yönetilen örneği, özel bir kurulum betiği ve sanal ağ yapılandırması.

## <a name="sql-database-or-sql-managed-instance-issues"></a>SQL veritabanı veya SQL yönetilen örnek sorunları

SSIS IR 'yi bir SSIS Katalog veritabanıyla sağlıyorsanız SQL veritabanı veya SQL yönetilen örneği gereklidir. SSIS IR, SQL veritabanı veya SQL yönetilen örneğine erişebilmelidir. Ayrıca, SQL veritabanı veya SQL yönetilen örneği için oturum açma hesabının bir SSIS Katalog veritabanı (SSıSDB) oluşturma izni olmalıdır. Hata varsa, ayrıntılı SQL özel durum iletisiyle hata kodu Data Factory portalında gösterilir. Hata kodlarındaki sorunları gidermek için aşağıdaki listede yer alan bilgileri kullanın.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Yeni SSIS IR sağlarken veya IR çalıştırılırken bu hatayı görebilirsiniz. IR sağlama sırasında bu hatayla karşılaşırsanız hata iletisinde aşağıdaki sorunlardan birine işaret eden ayrıntılı bir hata alabilirsiniz:

* Ağ bağlantısı sorunu. SQL veritabanı veya SQL yönetilen örneği için konak adının erişilebilir olup olmadığını denetleyin. Ayrıca hiçbir güvenlik duvarı veya ağ güvenlik grubunun (NSG) SSIS IR'nin sunucuya erişimini engellemediğini doğrulayın.
* SQL kimlik doğrulaması sırasında oturum açma başarısız oldu. Sağlanan hesap SQL Server veritabanında oturum açamıyor. Doğru kullanıcı hesabını sağladığınızdan emin olun.
* Microsoft Azure Active Directory (Azure AD) kimlik doğrulaması (yönetilen kimlik) sırasında oturum açma başarısız oldu. Fabrikanızın yönetilen kimliğini bir AAD grubuna ekleyin ve yönetilen kimliğin katalog veritabanı sunucunuza erişim izinleri olduğundan emin olun.
* Bağlantı zaman aşımı. Bu hatanın nedeni her zaman güvenlikle ilgili yapılandırmadır. Şunları yapmanızı öneririz:
  1. Yeni bir VM oluşturun.
  1. IR bir sanal ağda ise, VM 'yi IR 'nin aynı Microsoft Azure Sanal Ağ birleştirin.
  1. SSMS 'yi yükleyip SQL veritabanı veya SQL yönetilen örnek durumunu denetleyin.

Diğer sorunlar için, ayrıntılı SQL Özel Durum hata iletisinde gösterilen sorunu çözün. Hala sorun yaşıyorsanız SQL veritabanı veya SQL yönetilen örnek destek ekibine başvurun.

IR çalışırken hata görürseniz, ağ güvenlik grubu veya güvenlik duvarı değişiklikleri büyük olasılıkla SSIS IR çalışan düğümünün SQL veritabanı 'na veya SQL yönetilen örneğine erişmesini engelleyebilir. SQL veritabanı veya SQL yönetilen örneği 'ne erişebilmeleri için SSIS IR çalışan düğümünün engellemesini kaldırın.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Bu tür bir hata iletisi şöyle görünebilir: "veritabanı ' SSSıSDB ', boyut kotasına ulaştı. Verileri bölümleyin veya silin, dizinleri bırakın veya olası çözümler için belgelere başvurun. " 

Olası çözümler şunlardır:
* SSSıSDB 'nizin kota boyutunu artırın.
* SSISDB'nin yapılandırmasını değiştirerek boyutu şöyle azaltın:
   * Saklama süresini ve proje sürümü sayısını azaltma.
   * Günlüğün Bekletme dönemi azaltıyor.
   * Günlüğün varsayılan düzeyini değiştirme.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Bu hata SQL veritabanı veya SQL yönetilen örneğinin zaten bir SSSıSDB 'ye sahip olduğu ve başka bir IR tarafından kullanıldığı anlamına gelir. Farklı bir SQL veritabanı veya SQL yönetilen örneği sağlamanız ya da var olan SSSıSDB 'yi silmeniz ve yeni IR 'yi yeniden başlatmanız gerekir.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Bu hatanın oluşmasının nedeni aşağıdakilerden biri olabilir:

* SSIS IR için yapılandırılan kullanıcı hesabının veritabanı oluşturma izni yoktur. Kullanıcıya veritabanını oluşturma izni verebilirsiniz.
* Veritabanı oluşturma sırasında yürütme zaman aşımı veya DB işlemi zaman aşımı gibi bir zaman aşımı oluşmuştur. İşlemi daha sonra yeniden denemelisiniz. Yeniden deneme işe yaramazsa SQL veritabanı veya SQL yönetilen örnek destek ekibine başvurun.

Diğer sorunlar için SQL Özel Durumu hata iletisini gözden geçirin ve hata ayrıntılarında belirtilen sorunu çözün. Hala sorun yaşıyorsanız SQL veritabanı veya SQL yönetilen örnek destek ekibine başvurun.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Bu tür bir hata iletisi şöyle görünür: "geçersiz nesne adı ' catalog.catalog_properties '." Bu durumda, zaten SSSıSDB adlı bir veritabanınız var, ancak SSIS IR tarafından oluşturulmamış veya veritabanı, son SSIS IR sağlama hatalarından kaynaklanan geçersiz bir durumda. Var olan veritabanını SSSıSDB adıyla bırakabilir veya IR için yeni bir SQL veritabanı ya da SQL yönetilen örneği yapılandırabilirsiniz.

## <a name="custom-setup-issues"></a>Özel Kurulum sorunları

SSIS IR'nizin sağlanması veya yeniden yapılandırılması sırasında özel kurulum kendi kurulum adımlarınızı eklemeniz için bir arabirim sağlar. Daha fazla bilgi için bkz. [Azure-SSIS Integration Runtime için kurulumu özelleştirme](./how-to-configure-azure-ssis-ir-custom-setup.md).

Kapsayıcınızın yalnızca gerekli kurulum dosyalarını içerdiğinden emin olun; kapsayıcıdaki tüm dosyalar SSIS IR çalışan düğümüne indirilecektir. Özel kurulum betiğini SSIS IR'de çalıştırmadan önce betik yürütme sorunlarını çözmek için betiği yerel makinede test etmenizi öneririz.

Özel kurulum betiği kapsayıcısı IR çalıştırılırken denetlenir çünkü SSIS IR düzenli aralıklarla güncelleştirilir. Bu güncelleştirme, özel kurulum betiğini indirmek ve yeniden yüklemek için kapsayıcıya erişim gerektirir. İşlemde kapsayıcının erişilebilir ve main.cmd dosyasının mevcut olup olmadığı da denetlenir.

Özel kurulumu içeren herhangi bir hata için, Customsetupscriptblobcontainererişilemeyen veya CustomSetupScriptNotFound gibi Sub Code ile bir CustomSetupScriptFailure hata kodu görürsünüz.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Bu hata SSIS IR'nin özel kurulum için Azure blob kapsayıcınıza erişemediği anlamına gelir. Kapsayıcının SAS URI'sinin ulaşılabilir olduğundan ve süresinin dolmadığından emin olun.

Çalışıyorsa IR'yi durdurun, IR'yi yeni özel kurulum kapsayıcısı SAS URI değeriyle yeniden yapılandırın ve sonra yeniden başlatın.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Bu hata SSIS IR'nin blob kapsayıcınızda özel kurulum betiğini (main.cmd) bulamadığı anlamına gelir. Kapsayıcıda, özel kurulum yüklemesinin giriş noktası olan main.cmd dosyasının mevcut olduğundan emin olun.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Bu hata özel kurulum betiğinin (main.cmd) yürütülemediği anlamına gelir. Betiği önce yerel makinenizde deneyin veya blob kapsayıcınızda özel kurulum yürütme günlüklerini gözden geçirin.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Bu hata özel kurulum betiğinin zaman aşımına uğradığını gösterir. Betiğinizin sessiz yürütülebildiğinden, etkileşimli giriş gerektirmediğinden ve blob kapsayıcınızda yalnızca gerekli özel kurulum dosyalarının bulunduğundan emin olun. Betiği öncelikle yerel makinede test etmeniz önerilir. Blob kapsayıcınızda özel kurulum yürütme günlüklerini gözden geçirmelisiniz. Özel kurulum için zaman aşımına uğramadan verilen maksimum süre 45 dakikadır ve maksimum süre kapsayıcınızdan tüm dosyaları indirme ve bunları SSIS IR'ye yükleme süresini içerir. Daha uzun süreye ihtiyacınız olursa bir destek bileti oluşturun.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Bu hata özel kurulum yürütme günlüklerini blob kapsayıcınıza yükleme girişiminin başarısız olduğu anlamına gelir. Bu sorun SSIS IR'nin blob kapsayıcınıza yazma izinleri olmadığı için ya da depolama veya ağ sorunlarından dolayı oluşabilir. Özel kurulum başarılı olursa bu hata hiçbir SSIS işlevini etkilemez ama günlükler eksik olur. Özel kurulum başka bir hatayla başarısız olursa ve günlük yüklenmezse, günlüğün karşıya yüklenip analizde kullanılabilmesi için önce bu hatayı bildiririz. Aynı zamanda, bu sorun çözüldükten sonra diğer belirli sorunları da bildiririz. Yeniden deneme sonrasında bu sorun çözülmezse Azure Data Factory destek takımına başvurun.

## <a name="virtual-network-configuration"></a>Sanal ağ yapılandırması

SSIS IR'yi Azure Sanal Ağıyla birleştirdiğinizde, SSIS IR kullanıcı aboneliği altındaki sanal ağı kullanır. Daha fazla bilgi için bkz. [Azure-SSIS Integration Runtime'ı sanal ağa ekleme](./join-azure-ssis-integration-runtime-virtual-network.md).
SSIS IR başarıyla başladıktan sonra, ağ bağlantısı sorunlarıyla karşılaşırsanız, sorunu tanılamak için [Tanıla bağlantı aracını](ssis-integration-runtime-diagnose-connectivity-faq.md) kullanmayı deneyebilirsiniz.
Sanal Ağ ile ilgili bir sorun olduğunda aşağıdaki hatalardan birini görürsünüz.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Bu hata çeşitli nedenlerle oluşabilir. Sorunu gidermek için [Yasak](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) ve [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) bölümlerine bakın.

### <a name="forbidden"></a>Yasak

Bu tür bir hata şuna benzeyebilir: "SubnetID geçerli hesap için etkinleştirilmemiş. Microsoft.Batch kaynak sağlayıcısı VNet 'in aynı abonelikte kayıtlı değil. "

Bu ayrıntılar Azure Batch'in sanal ağınıza erişemediği anlamına gelir. Microsoft.Batch kaynak sağlayıcısını Sanal Ağ ile aynı aboneliğe kaydedin.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Bu tür bir hata aşağıdakilerden birine benzer olabilir: 

- "Belirtilen sanal ağ yok ya da Batch hizmetinin erişimi yok."
- "Belirtilen alt ağ xxx yok."

Bu hatalar sanal ağın var olmadığı, Azure Batch hizmetinin bu sanal ağa erişemediği veya sağlanan alt ağın var olmadığı anlamına gelir. Sanal ağ ile alt ağın var olduğundan ve Azure Batch'in bunlara erişebildiğinden emin olun.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Bu tür bir hata iletisi şu şekilde görünebilir: "VNet 'te Integration Runtime sağlanamadı. DNS sunucusu veya NSG ayarları yapılandırılırsa, DNS sunucusunun erişilebilir olduğundan ve NSG 'nin doğru yapılandırıldığından emin olun. "

Bu durumda büyük olasılıkla DNS sunucusunda ve NSG ayarlarında SSIS IR için gereken Azure sunucu adının çözümlenmesini veya bu ada erişilmesini engelleyen özelleştirilmiş bir yapılandırma kullanıyorsunuzdur. Daha fazla bilgi için bkz. [SSIS IR Sanal Ağ yapılandırması](./join-azure-ssis-integration-runtime-virtual-network.md). Sorunlarınız devam ediyorsa Azure Data Factory destek takımına başvurun.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR düzenli aralıklarla otomatik olarak güncelleştirilecek. Yükseltme sırasında yeni bir Azure Batch havuzu oluşturulur ve eski Azure Batch havuzu silinir. Ayrıca, eski havuzun Sanal Ağ ile ilgili kaynakları da silinir ve aboneliğinizin altında yeni Sanal Ağ ile ilgili kaynaklar oluşturulur. Bu hata, abonelik veya kaynak grubu düzeyindeki bir silme kilidinden dolayı eski havuzun Sanal Ağ ile ilgili kaynaklarını silmenin başarısız olduğu anlamına gelir. Silme kilidini müşteri denetlediği ve ayarladığı için, bu durumda silme kilidini kaldırması gerekir.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

SSIS IR sağlama başarısız olursa, oluşturulan tüm kaynaklar silinir. Bununla birlikte abonelik veya kaynak grubu düzeyinde bir kaynak silme kilidi varsa Sanal Ağ kaynakları beklendiği gibi silinmez. Bu hatayı düzeltmek için silme kilidini kaldıın ve IR'yi yeniden başlatın.

### <a name="vnetresourcegrouplockedduringstopvnetdeletelock"></a>VNetResourceGroupLockedDuringStop/VNetDeleteLock

SSIS IR'yi durdurduğunuzda Sanal Ağ ile ilgili tüm kaynaklar silinir. Ama abonelik veya kaynak grubu düzeyinde bir kaynak silme kilidi olduğunda silme işlemi başarısız olur. Burada da silme kilidini müşteri denetler ve ayarlar. Bu nedenle silme kilidini kaldırması ve sonra SSIS IR'yi yeniden durdurması gerekir.

### <a name="nodeunavailable"></a>NodeUnavailable

Bu hata IR çalışırken oluşur ve IR'nin artık iyi durumda olmadığı anlamına gelir. Bu hata her zaman DNS sunucusunda veya NSG yapılandırmasında yapılan ve SSIS IR'nin gerekli hizmete bağlanmasını engelleyen bir değişiklikten kaynaklanır. DNS sunucusunun ve NSG'nin yapılandırması müşteri tarafından denetlendiğinden, müşterinin kendi tarafında bağlantıyı engelleyen sorunları düzeltmesi gerekir. Daha fazla bilgi için bkz. [SSIS IR Sanal Ağ yapılandırması](./join-azure-ssis-integration-runtime-virtual-network.md). Sorunlarınız devam ediyorsa Azure Data Factory destek takımına başvurun.

## <a name="static-public-ip-addresses-configuration"></a>Statik genel IP adresleri yapılandırması

Azure-SSIS IR Azure sanal ağına katdığınızda, IR 'nin, belirli IP adreslerine erişimi sınırlayan veri kaynaklarına erişebilmesi için kendi statik genel IP adreslerini de bir araya getirebileceksiniz. Daha fazla bilgi için bkz. [Azure-SSIS Integration Runtime'ı sanal ağa ekleme](./join-azure-ssis-integration-runtime-virtual-network.md).

Yukarıdaki sanal ağ sorunlarının yanı sıra statik ortak IP adresleriyle ilgili sorunu da karşılayabilirsiniz. Yardım için lütfen aşağıdaki hataları kontrol edin.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>Invalidpublicipbelirtilmiş

Bu hata, Azure-SSIS IR başlattığınızda çeşitli nedenlerden kaynaklanabilir:

| Hata iletisi | Çözüm|
|:--- |:--- |
| Belirtilen statik genel IP adresi zaten kullanılıyor, lütfen Azure-SSIS Integration Runtime için kullanılmamış iki tane belirtin. | İki kullanılmamış statik genel IP adresi seçmeniz veya belirtilen genel IP adresine yönelik geçerli başvuruları kaldırmanız ve ardından Azure-SSIS IR yeniden başlatmanız gerekir. |
| Belirtilen statik genel IP adresi, DNS adına sahip değil, lütfen Azure-SSIS Integration Runtime DNS adına sahip iki tane belirtin. | Aşağıdaki resimde gösterildiği gibi Azure portal genel IP adresinin DNS adını ayarlayabilirsiniz. Belirli adımlar şunlardır: (1) Azure portal aç ve bu genel IP adresinin kaynak sayfasına git; (2) **yapılandırma** bölümünü SEÇIN ve DNS adını ayarlayıp **Kaydet** düğmesine tıklayın; (3) Azure-SSIS IR yeniden başlatın. |
| Azure-SSIS Integration Runtime için sunulan VNet ve statik genel IP adresleri aynı konumda olmalıdır. | Azure ağının gereksinimlerine göre, statik genel IP adresi ve sanal ağ aynı konum ve abonelikte olmalıdır. Lütfen iki geçerli statik genel IP adresi sağlayın ve Azure-SSIS IR yeniden başlatın. |
| Belirtilen statik genel IP adresi temel bir tane, lütfen Azure-SSIS Integration Runtime için iki standart değer sağlayın. | Yardım için [genel IP adresi SKU 'larına](../virtual-network/public-ip-addresses.md#sku) bakın. |

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>Publicıpresourcegrouplockedduringstart

Azure-SSIS IR sağlama başarısız olursa, oluşturulan tüm kaynaklar silinir. Ancak, abonelikte veya kaynak grubunda (statik genel IP adresiniz içeren) bir kaynak silme kilidi varsa, ağ kaynakları beklendiği gibi silinmez. Hatayı düzeltemedi, lütfen silme kilidini kaldırın ve IR 'yi yeniden başlatın.

### <a name="publicipresourcegrouplockedduringstop"></a>Publicıpresourcegrouplockedduringstop

Azure-SSIS IR durdurduğunuzda, genel IP adresinizi içeren kaynak grubunda oluşturulan tüm ağ kaynakları silinir. Ancak abonelikte veya kaynak grubunda (statik genel IP adresiniz içeren) bir kaynak silme kilidi varsa silme işlemi başarısız olabilir. Lütfen silme kilidini kaldırın ve IR 'yi yeniden başlatın.

### <a name="publicipresourcegrouplockedduringupgrade"></a>Publicıpresourcegrouplockedduringupgrade

Azure-SSIS IR düzenli aralıklarla otomatik olarak güncelleştirilir. Yükseltme sırasında yeni IR düğümleri oluşturulur ve eski düğümler silinir. Ayrıca, eski düğümlerin oluşturduğu ağ kaynakları (örneğin, yük dengeleyici ve ağ güvenlik grubu) silinir ve yeni ağ kaynakları aboneliğiniz altında oluşturulur. Bu hata, abonelik veya kaynak grubundaki (statik genel IP adresi içeren) silme kilidi nedeniyle eski düğümlerin ağ kaynaklarının silinmesinin başarısız olduğu anlamına gelir. Eski düğümleri temizleyebilmemiz ve eski düğümlerin statik genel IP adresini serbest bırakabilmeleri için silme kilidini kaldırın. Aksi takdirde, statik genel IP adresi yayınlanamaz ve diğer bir deyişle, IR 'yi yükseltemeyeceksiniz.

### <a name="publicipnotusableduringupgrade"></a>Publicıpnotusableduringupgrade

Kendi statik genel IP adreslerinizi getirmek istediğinizde, iki genel IP adresi sağlanmalıdır. Bunlardan biri, IR düğümlerini hemen oluşturmak için kullanılır ve IR 'nin yükseltilmesi sırasında başka bir tane kullanılır. Bu hata, diğer genel IP adresi yükseltme sırasında kullanılabilir olmadığında ortaya çıkabilir. Olası nedenler için lütfen  [ınvalidpublicıpbelirtilen](#InvalidPublicIPSpecified) bölümüne bakın.