---
title: Platform tarafından desteklenen geçiş aracı.
description: Klasik dağıtım modelinden Azure Resource Manager için, platform tarafından desteklenen kaynak geçişi hakkında teknik kapsamlı bakış.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: tagore
ms.openlocfilehash: 43473b734968b25ca90bd8809c8291625011d46f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667010"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış

> [!IMPORTANT]
> Bugün, IaaS VM 'lerinin yaklaşık %90 ' u [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)kullanıyor. 28 Şubat 2020 itibariyle klasik VM 'Ler kullanımdan kaldırılmıştır ve 1 Mart 2023 tarihinde tamamen kullanımdan kaldırılacaktır. Bu kullanımdan kaldırma ve [nasıl etkilediği](./classic-vm-deprecation.md#how-does-this-affect-me)hakkında [daha fazla bilgi edinin]( https://aka.ms/classicvmretirement) .

Azure klasik dağıtım modelinden Azure Resource Manager dağıtım modeline geçiş konusunda ayrıntılı bilgi alalım. Azure platformunun kaynakları iki dağıtım modeli arasında nasıl geçirdiğini anlamanıza yardımcı olması için kaynak ve özellik düzeyindeki kaynaklara göz atacağız. Daha fazla bilgi için lütfen bkz. hizmet duyurusu makalesi: [IaaS kaynaklarının klasik sürümünden Azure Resource Manager geçişi](migration-classic-resource-manager-overview.md).


## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>IaaS kaynaklarını klasik dağıtım modelinden Azure Resource Manager geçirin
İlk olarak, hizmet olarak altyapı (IaaS) kaynakları üzerinde veri düzlemi ve yönetim düzlemi işlemleri arasındaki farkı anlamak önemlidir.

* *Yönetim/Denetim düzlemi* , yönetim/denetim düzlemi veya kaynak değiştirme API 'sine gelen çağrıları açıklar. Örneğin, VM oluşturma, bir sanal makineyi yeniden başlatma ve çalışmakta olan kaynakları yönetmek için bir sanal ağı yeni alt ağla güncelleştirme gibi işlemler. Bunlar VM 'lere bağlanmayı doğrudan etkilemez.
* *Veri düzlemi* (uygulama) uygulamanın kendisi için çalışma zamanını açıklar ve Azure API 'si üzerinden gitmediği örneklerle etkileşim içerir. Örneğin, Web sitenize erişmek veya çalışan bir SQL Server örneğinden ya da MongoDB sunucusundan veri çekmek için veri düzlemi veya uygulama etkileşimlerinin olması gerekir. Diğer örneklerde, bir depolama hesabından bir blob kopyalama ve bir genel IP adresine Uzak Masaüstü Protokolü (RDP) veya Secure Shell (SSH) kullanmak için sanal makineye erişme dahildir. Bu işlemler, uygulamanın işlem, ağ ve depolama kaynaklarında çalışmaya devam etmesini sağlar.

Veri düzlemi, klasik dağıtım modeliyle Kaynak Yöneticisi yığınları arasında aynıdır. Bunun farkı, geçiş işlemi sırasında Microsoft 'un kaynakları klasik dağıtım modelinden Kaynak Yöneticisi yığınında dönüştürmektir. Sonuç olarak, Kaynak Yöneticisi yığınında kaynaklarınızı yönetmek için yeni araçlar, API 'Ler ve SDK 'Lar kullanmanız gerekir.

![Yönetim/Denetim düzlemi ve veri düzlemi arasındaki farkı gösteren diyagram](./media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Bazı geçiş senaryolarında, Azure platformu sanal makinenizi durdurur, serbest bırakır ve yeniden başlatır. Bu, kısa bir veri düzlemi kapalı kalma süresine neden olur.
>

## <a name="the-migration-experience"></a>Geçiş deneyimi
Geçişe başlamadan önce:

* Geçirmek istediğiniz kaynakların desteklenmeyen özellikleri veya yapılandırmaları olmadığından emin olun. Bu sorunlar genellikle platform tarafından algılanır ve bir hata oluşturulur.
* Sanal bir ağda olmayan VM 'leriniz varsa, hazırlama işleminin bir parçası olarak durdurulur ve serbest bırakılır. Genel IP adresini kaybetmek istemiyorsanız, hazırlama işlemini tetiklemeden önce IP adresini rezervasyonu göz önünde bulundurun. VM 'Ler bir sanal ağda ise durdurulmaz ve serbest bırakılır.
* Geçiş sırasında gerçekleşebilecek beklenmeyen hataları göz önünde bulundurarak geçişinizi çalışma saatleri dışına gerçekleşecek şekilde planlayın.
* Hazırlama adımı tamamlandıktan sonra doğrulamayı kolaylaştırmak için PowerShell’i, komut satırı arabirimi (CLI) komutlarını veya REST API’leri kullanarak VM’lerinizin geçerli yapılandırmasını indirin.
* Geçişe başlamadan önce Kaynak Yöneticisi dağıtım modelini işlemek için Otomasyon ve işlemleştirme betikleri güncelleştirin. İsteğe bağlı olarak, kaynaklar hazırlanmış durumdayken GET işlemleri gerçekleştirebilirsiniz.
* Klasik dağıtım modelindeki IaaS kaynaklarında yapılandırılan Azure rol tabanlı erişim denetimi (Azure RBAC) ilkelerini değerlendirin ve geçiş tamamlandıktan sonra için plan yapın.

Geçiş iş akışı aşağıdaki gibidir:

![Geçiş iş akışını gösteren diyagram](./media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Aşağıdaki bölümlerde açıklanan işlemler ıdempotent. Desteklenmeyen bir özellik veya yapılandırma hatası dışında bir sorununuz varsa, hazırlama, durdurma veya işleme işlemini yeniden deneyin. Azure, eylemi yeniden dener.
>
>

### <a name="validate"></a>Doğrulama
Doğrulama işlemi, geçiş sürecinin ilk adımıdır. Bu adımın amacı, klasik dağıtım modelinde geçirmek istediğiniz kaynakların durumunu analiz olmaktır. İşlem, kaynakların geçiş özelliğine sahip olup olmadığını değerlendirir (başarı veya başarısızlık).

Geçiş için doğrulamak istediğiniz sanal ağı veya bulut hizmetini (sanal ağ içinde değilse) seçersiniz. Kaynak geçiş özelliğine sahip değilse, Azure nedenleri bu nedenlerle listeler.

#### <a name="checks-not-done-in-the-validate-operation"></a>Doğrulama işleminde yapılan denetimler yapılmadı

Doğrulama işlemi yalnızca klasik dağıtım modelindeki kaynakların durumunu analiz eder. Klasik dağıtım modelindeki çeşitli yapılandırmalar nedeniyle tüm hatalara ve desteklenmeyen senaryolara işaret edebilir. Azure Resource Manager yığınının geçiş sırasında kaynakları üzerinde getirebileceğini belirten tüm sorunları denetlemek mümkün değildir. Bu sorunlar yalnızca, geçiş işleminin bir sonraki adımında (hazırlama işlemi) kaynak dönüştürmesi yapıldığında denetlenir. Aşağıdaki tabloda, doğrulama işleminde Denetlenmemiş tüm sorunlar listelenmektedir:


|Ağ denetimleri doğrulama işleminde yok|
|-|
|Hem ER hem de VPN ağ geçitlerine sahip bir sanal ağ.|
|Bağlantısı kesik durumda olan bir sanal ağ geçidi bağlantısı.|
|Tüm ER devreleri Azure Resource Manager yığınına önceden geçirilir.|
|Ağ kaynakları için kota denetimleri Azure Resource Manager. Örneğin: statik genel IP, dinamik genel IP 'Ler, yük dengeleyici, ağ güvenlik grupları, yol tabloları ve ağ arabirimleri. |
| Tüm yük dengeleyici kuralları dağıtım ve sanal ağ arasında geçerlidir. |
| Aynı sanal ağdaki durdurma serbest bırakılmış VM 'Ler arasında özel IP 'Ler çakışıyor. |

### <a name="prepare"></a>Hazırlama
Hazırlama işlemi, geçiş sürecinin ikinci adımıdır. Bu adımın amacı, kaynak Kaynak Yöneticisi için klasik dağıtım modelinden IaaS kaynaklarının dönüştürülmesine benzetim sağlamaktır. Ayrıca, hazırlama işlemi sizin görselleştirilecek şekilde bu yan yana sunulur.

> [!NOTE] 
> Klasik dağıtım modelindeki kaynaklarınız Bu adım sırasında değiştirilmez. Geçişe çalışıyorsanız, çalıştırmak için güvenli bir adımdır. 

Geçiş için hazırlamak istediğiniz sanal ağı veya bulut hizmetini (sanal ağ değilse) seçersiniz.

* Kaynak geçiş özelliğine sahip değilse, Azure geçiş işlemini sonlandırır ve hazırlama işleminin başarısız olma nedenini listeler.
* Kaynak geçiş özelliğine sahipse, Azure geçiş altındaki kaynaklar için yönetim düzlemi işlemlerini kilitler. Örneğin, geçirilmekte olan bir VM’ye veri diski ekleyemezsiniz.

Azure daha sonra, kaynak geçişi için Kaynak Yöneticisi için klasik dağıtım modelinden meta verilerin geçirilmesini başlatır.

Hazırlama işlemi tamamlandıktan sonra, hem klasik dağıtım modelinde hem de Kaynak Yöneticisi Kaynakları görselleştirme seçeneğiniz vardır. Azure platformu, klasik dağıtım modelindeki her bulut hizmeti için `cloud-service-name>-Migrated` deseninde bir kaynak grubu adı oluşturur.

> [!NOTE]
> Geçirilen kaynaklar için oluşturulan bir kaynak grubunun adını seçmek mümkün değildir (yani, "-geçirilmiş"). Geçiş tamamlandıktan sonra, kaynakları istediğiniz herhangi bir kaynak grubuna taşımak için Azure Resource Manager taşıma özelliğini kullanabilirsiniz. Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Aşağıdaki iki ekran görüntüsü başarılı bir hazırlama işleminden sonra sonucu gösterir. İlki, özgün bulut hizmetini içeren bir kaynak grubu gösterir. İkinci bir, eşdeğer Azure Resource Manager kaynaklarını içeren yeni "-geçirilmiş" kaynak grubunu gösterir.

![Orijinal bulut hizmetini gösteren ekran görüntüsü](./media/migration-classic-resource-manager/portal-classic.png)

![Hazırlama işleminde Azure Resource Manager kaynaklarını gösteren ekran görüntüsü](./media/migration-classic-resource-manager/portal-arm.png)

Hazırlama aşamasının tamamlanmasından sonra kaynaklarınızın gerisinde bir bakış aşağıda verilmiştir. Veri düzledeki kaynağın aynı olduğunu unutmayın. Hem yönetim düzlemi (klasik dağıtım modeli) hem de denetim düzlemi (Kaynak Yöneticisi) ile temsil edilir.

![Hazırlama aşamasının diyagramı](./media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Klasik dağıtım modelindeki bir sanal ağda olmayan VM 'Ler, geçişin bu aşamasında durdurulur ve serbest bırakılır.
>

### <a name="check-manual-or-scripted"></a>Denetim (el ile veya betikle)
Denetim adımında, geçişin doğru göründüğünü doğrulamak için daha önce indirdiğiniz yapılandırmayı kullanma seçeneğiniz vardır. Alternatif olarak, portalda oturum açabilir ve meta veri geçişinin iyi göründüğünü doğrulamak için özellikleri ve kaynakları kontrol edin.

Sanal ağ geçişi yapıyorsanız, sanal makinelerin çoğu yapılandırması yeniden başlatılmaz. Bu sanal makinelerdeki uygulamalar için uygulamanın hala çalıştığını doğrulayabilirsiniz.

Sanal makinelerin beklendiği gibi çalışıp çalışmadığını ve güncelleştirilmiş betiklerinizin doğru çalıştığını görmek için izleme ve işletimsel betiklerinizi test edebilirsiniz. Kaynaklar hazırlanmış durumdayken yalnızca GET işlemleri desteklenir.

Geçişi yürütmeniz gereken bir zaman kümesi yoktur. Bu durumdayken dilediğiniz kadar bekleyebilirsiniz. Bununla birlikte, bu kaynaklar için geçişi durdurana veya işleyene kadar yönetim düzlemi kilitli kalır.

Herhangi bir sorun yaşarsanız dilediğiniz zaman geçişi durdurabilir ve klasik dağıtım modeline dönebilirsiniz. Geri dönüp Azure, klasik dağıtım modelinde bu VM 'lerde normal işlemleri sürdürmenize olanak sağlamak üzere yönetim düzlemi işlemlerini kaynaklar üzerinde açar.

### <a name="abort"></a>Durdurma
Bu, değişikliklerinizi klasik dağıtım modeline dönüştürmek ve geçişi durdurmak istiyorsanız isteğe bağlı bir adımdır. Bu işlem kaynaklarınızın Kaynak Yöneticisi meta verilerini siler (hazırlama adımında oluşturulur). 

![Durdurma adımının diyagramı](media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Bu işlem, işleme işlemini tetikledikten sonra yapılamaz.     
>

### <a name="commit"></a>İşleme
Doğrulama adımını tamamladıktan sonra geçişi işleyebilirsiniz. Kaynaklar artık klasik dağıtım modelinde görünmez ve yalnızca Kaynak Yöneticisi dağıtım modelinde kullanılabilir. Geçirilen kaynaklar yalnızca yeni portalda yönetilebilir.

> [!NOTE]
> Bu, bir kere etkili olan bir işlemdir. Başarısız olursa, işlemi yeniden deneyin. Başarısız olmaya devam ederse, bir destek bileti oluşturun veya [Microsoft Q&a](/answers/index.html) 'da bir forum oluşturun
>
>

![Tamamlama adımının diyagramı](media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Geçiş akış çizelgesi

Geçişe nasıl devam edeceğine gösteren bir akış çizelgesi aşağıda verilmiştir:

![Geçiş adımlarını gösteren ekran görüntüsü](media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Kaynak Yöneticisi kaynaklara klasik dağıtım modelinin çevirisi
Aşağıdaki tabloda, klasik dağıtım modelini ve kaynakların Kaynak Yöneticisi gösterimlerini bulabilirsiniz. Diğer özellikler ve kaynaklar şu an desteklenmemektedir.

| Klasik gösterim | Resource Manager gösterimi | Notlar |
| --- | --- | --- |
| Bulut hizmeti adı (barındırılan hizmet adı) |DNS adı |Geçiş sırasında, her bulut hizmeti için `<cloudservicename>-migrated` adlandırma deseni kullanılarak yeni bir kaynak grubu oluşturulur. Bu kaynak grubu tüm kaynaklarınızı içerir. Bulut hizmeti adı, genel IP adresiyle ilişkili bir DNS adı olur. |
| Sanal makine |Sanal makine |Sanal makineye özgü özellikler değiştirilmeden geçirilir. Bilgisayar adı gibi bazı osProfile bilgileri klasik dağıtım modelinde depolanmaz ve geçişten sonra boş kalır. |
| Sanal makineye bağlı disk kaynakları |Sanal makineye bağlı örtük diskler |Resource Manager dağıtım modelinde diskler en üst düzey kaynaklar olarak modellenmez. Diskler, VM altındaki örtük diskler olarak geçirilir. Şu anda yalnızca sanal makineye bağlı diskler desteklenmektedir. Kaynak Yöneticisi VM 'Ler artık klasik dağıtım modelinde depolama hesapları kullanabilir ve böylece disklerin hiçbir güncelleştirme olmadan kolayca geçirilmesini sağlar. |
| VM uzantıları |VM uzantıları |Klasik dağıtım modelinden XML uzantıları dışındaki tüm kaynak uzantıları geçirilir. |
| Sanal makine sertifikaları |Azure Key Vault’ta Sertifikalar |Bir bulut hizmeti hizmet sertifikaları içeriyorsa, geçiş, bulut hizmeti başına yeni bir Azure Anahtar Kasası oluşturur ve sertifikaları anahtar kasasına taşımaktadır. VM’ler, anahtar kasasındaki sertifikalara başvuracak şekilde güncelleştirilir. <br><br> Anahtar kasasını silmeyin. Bu, VM 'nin başarısız durumuna geçmesine neden olabilir. |
| WinRM yapılandırması |osProfile altındaki WinRM yapılandırması |Windows Uzaktan Yönetimi yapılandırması, geçiş kapsamında değiştirilmeden taşınır. |
| Kullanılabilirlik kümesi özelliği |Kullanılabilirlik kümesi kaynağı | Kullanılabilirlik kümesi belirtimi, klasik dağıtım modelindeki VM 'deki bir özelliktir. Kullanılabilirlik kümeleri, geçiş kapsamında en üst düzey kaynağa dönüşür. Şu yapılandırmalar desteklenmez: bulut hizmeti başına birden çok kullanılabilirlik kümesi veya bir bulut hizmetindeki herhangi bir kullanılabilirlik kümesinde olmayan VM’lerle birlikte bir veya daha fazla kullanılabilirlik kümesi. |
| Bir VM’deki ağ yapılandırması |Birincil ağ arabirimi |Bir VM’deki ağ yapılandırması, geçişten sonra birincil ağ arabirimi kaynağı olarak gösterilir. Bir sanal ağda olmayan VM’lerin iç IP adresi geçiş sırasında değişir. |
| Bir VM’de birden çok ağ arabirimi |Ağ arabirimleri |Bir VM ile ilişkili birden çok ağ arabirimi varsa, her bir ağ arabirimi, geçişin bir parçası olarak, tüm özelliklerle birlikte en üst düzey bir kaynak haline gelir. |
| Yük dengeli uç nokta kümesi |Yük dengeleyici |Klasik dağıtım modelinde, platform tarafından her bulut hizmetine örtük bir yük dengeleyici atanıyordu. Geçiş sırasında yeni bir yük dengeleyici kaynağı oluşturulur ve yük dengeleme uç noktası kümesi, yük dengeleyici kurallarına dönüşür. |
| Gelen NAT kuralları |Gelen NAT kuralları |VM’de tanımlanmış giriş uç noktaları, geçiş sırasında yük dengeleyici altındaki gelen ağ adresi çevirisi kurallarına dönüştürülür. |
| VIP adresi |DNS adına sahip genel IP adresi |Sanal IP adresi genel bir IP adresi olur ve yük dengeleyici ile ilişkilendirilir. Yalnızca giriş uç noktası atanmış sanal IP’ler geçirilebilir. |
| Sanal ağ |Sanal ağ |Sanal ağ, tüm özellikleriyle birlikte Resource Manager dağıtım modeline geçirilir. `-migrated` adlı yeni bir kaynak grubu oluşturulur. |
| Ayrılmış IP’ler |Statik ayırma yöntemi kullanan genel IP adresi |Yük dengeleyiciyle ilişkili ayrılmış IP’ler, bulut hizmetinin veya sanal makinenin geçişiyle birlikte geçirilir. İlişkilendirilmemiş ayrılmış IP 'Ler [Move-AzureReservedIP](/powershell/module/servicemanagement/azure.service/move-azurereservedip)kullanılarak geçirilebilir.  |
| VM başına genel IP adresi |Dinamik ayırma yöntemi kullanan genel IP adresi |VM’le ilişkili genel IP adresi, ayırma yöntemi statik olarak ayarlanarak bir genel IP adresi kaynağı olarak dönüştürülür. |
| NSG'ler |NSG'ler |Bir alt ağla ilişkili ağ güvenlik grupları, geçiş kapsamında Resource Manager dağıtım modeline kopyalanır. Geçiş sırasında, klasik dağıtım modelindeki NSG kaldırılmaz. Bununla birlikte, geçiş sürdüğü sırada NSG için yönetim düzlemi işlemleri engellenir. İlişkilendirilmemiş NSG 'ler [Move-AzureNetworkSecurityGroup](/powershell/module/servicemanagement/azure.service/move-azurenetworksecuritygroup)kullanılarak geçirilebilir.|
| DNS sunucuları |DNS sunucuları |Bir sanal ağ veya VM’le ilişkili DNS sunucuları, kendilerine karşılık gelen kaynağın geçişi kapsamında, tüm özellikleriyle birlikte geçirilir. |
| UDR’ler |UDR’ler |Bir alt ağla ilişkili kullanıcı tanımlı rotalar, geçiş kapsamında Resource Manager dağıtım modeline kopyalanır. Geçiş sırasında, klasik dağıtım modelindeki UDR kaldırılmaz. Geçiş sürdüğü sırada UDR için yönetim düzlemi işlemleri engellenir. İlişkilendirilmemiş UDRs, [Move-AzureRouteTable](/powershell/module/servicemanagement/azure.service/Move-AzureRouteTable)kullanılarak geçirilebilir. |
| Bir sanal makinenin ağ yapılandırmasında IP iletme özelliği |NIC’de IP iletme özelliği |Bir VM’deki IP iletme özelliği, geçiş sırasında ağ arabirimindeki bir özelliğe dönüştürülür. |
| Birden çok IP’si olan yük dengeleyici |Birden çok genel IP kaynağı olan yük dengeleyici |Yük Dengeleyici ile ilişkili her genel IP, bir genel IP kaynağına dönüştürülür ve geçişten sonra yük dengeleyici ile ilişkilendirilir. |
| VM’deki iç DNS adları |NIC’deki iç DNS adları |Geçiş sırasında, VM’lerin iç DNS son ekleri NIC’deki “InternalDomainNameSuffix” adlı salt okunur bir özelliğe geçirilir. Son ek geçişten sonra değişmeden kalır ve VM çözümünün daha önce olduğu gibi çalışmaya devam etmesi gerekir. |
| Sanal ağ geçidi |Sanal ağ geçidi |Sanal ağ geçidi özellikleri değişmeden geçirilir. Ağ geçidiyle ilişkili VIP de değişmez. |
| Yerel ağ sitesi |Yerel ağ geçidi |Yerel ağ alanı özellikleri, yerel ağ geçidi adlı yeni bir kaynağa değiştirilmeden geçirilir. Bu, şirket içi adres öneklerini ve uzak ağ geçidi IP 'sini temsil eder. |
| Bağlantı başvuruları |Bağlantı |Ağ yapılandırmasındaki ağ geçidi ve yerel ağ sitesi arasındaki bağlantı başvuruları, bağlantı adlı yeni bir kaynak tarafından temsil edilir. Ağ yapılandırma dosyalarındaki bağlantı başvurusunun tüm özellikleri, bağlantı kaynağına değiştirilmeden kopyalanır. Klasik dağıtım modelindeki sanal ağlar arasındaki bağlantı, sanal ağları temsil eden yerel ağ sitelerine iki IPSec tüneli oluşturularak elde edilir. Bu, yerel ağ geçitleri gerekmeden Kaynak Yöneticisi modelinde sanal ağ-sanal ağ bağlantı türüne dönüştürülür. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Geçiş sonrası otomasyon ve araçlarınızda gerçekleşecek değişiklikler
Kaynaklarınızı klasik dağıtım modelinden Kaynak Yöneticisi dağıtım modeline geçirmenin bir parçası olarak, geçiş sonrasında çalışmaya devam ettiğinden emin olmak için mevcut otomasyonu veya araçları güncelleştirmeniz gerekir.


## <a name="next-steps"></a>Sonraki adımlar

* [IaaS kaynaklarının klasik ile Azure Resource Manager geçişine genel bakış](migration-classic-resource-manager-overview.md)
* [IaaS kaynaklarının Klasik’ten Azure Resource Manager’a geçişini planlama](migration-classic-resource-manager-plan.md)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için PowerShell 'i kullanma](migration-classic-resource-manager-ps.md)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için CLı kullanma](migration-classic-resource-manager-cli.md)
* [VPN Gateway klasik Kaynak Yöneticisi geçişe](../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [ExpressRoute devreleri ve ilişkili sanal ağları klasik 'dan Kaynak Yöneticisi dağıtım modeline geçirin](../expressroute/expressroute-migration-classic-resource-manager.md)
* [IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişine yardımcı olacak topluluk araçları](migration-classic-resource-manager-community-tools.md)
* [En sık karşılaşılan geçiş hatalarını gözden geçirme](migration-classic-resource-manager-errors.md)
* [IaaS kaynaklarını klasik konumundan Azure Resource Manager geçirme hakkında en sık sorulan soruları gözden geçirin](migration-classic-resource-manager-faq.md)
