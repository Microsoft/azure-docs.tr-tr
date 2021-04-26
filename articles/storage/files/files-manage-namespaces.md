---
title: Azure dosyaları ile DFS-N kullanma
description: Azure dosyaları ile ortak DFS-N kullanım örnekleri
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a8f1b8c54ad4fd42cc8ebda4965aaf1233143f39
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741988"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>Azure dosyaları ile DFS ad alanlarını kullanma
Genellikle DFS ad alanları veya DFS-N olarak adlandırılan [Dağıtılmış dosya sistemi ad alanları](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview), üretimde SMB dosya paylaşımlarının dağıtımını ve bakımını kolaylaştırmak için yaygın olarak kullanılan bir Windows Server sunucu rolüdür. DFS ad alanları, dosya paylaşımlarınızın UNC yolu ve gerçek dosya paylaşımları arasında bir yöneltme katmanı sağlamanıza olanak tanıyan bir depolama ad alanı sanallaştırma teknolojisidir. DFS ad alanları, SMB dosya paylaşımları ile birlikte çalışarak, bu dosya paylaşımlarının belirsiz bir şekilde barındırılır: Azure Dosya Eşitleme, Azure dosya paylaşımları doğrudan, Azure NetApp Files veya diğer üçüncü taraf tekliflerinde barındırılan SMB dosya paylaşımları ve hatta diğer bulutlarda barındırılan dosya paylaşımları ile birlikte, şirket içi bir Windows dosya sunucusu üzerinde barındırılan SMB paylaşımları ile kullanılabilir. 

DFS ad alanları, Kullanıcı dostu bir UNC yolu `\\contoso\shares\ProjectX` (veya gıbı SMB paylaşımının temel UNC yolu) arasında bir eşleme sağlar `\\Server01-Prod\ProjectX` `\\storageaccount.file.core.windows.net\projectx` . Son Kullanıcı dosya paylaşımına gitmek istediğinde, Kullanıcı dostu UNC yolunu Yazırlar, ancak SMB istemcisi eşlemenin temel SMB yoluna erişir. Ayrıca, bu temel kavramı gibi var olan bir dosya sunucusu adını almak için genişletebilirsiniz `\\MyServer\ProjectX` . Aşağıdaki senaryolara ulaşmak için bu özelliği kullanabilirsiniz:

- Mantıksal veri kümesi için geçiş kanıtlama adı sağlayın. Bu örnekte, ile eşleşen bir eşlemeye sahipsiniz `\\contoso\shares\Engineering` `\\OldServer\Engineering` . Azure dosyalarına geçişinizi tamamladığınızda, eşlemenizi, Kullanıcı dostu UNC yolunuza işaret eden bir şekilde değiştirebilirsiniz `\\storageaccount.file.core.windows.net\engineering` . Son Kullanıcı, Kullanıcı dostu UNC yoluna eriştiğinde Azure dosya paylaşımının yoluna sorunsuz bir şekilde yönlendirilir.

- Azure Dosya Eşitleme gibi farklı fiziksel sitelerde birden çok sunucuya dağıtılan bir mantıksal veri kümesi için ortak bir ad oluşturun. Bu örnekte, gibi bir ad,,,, gibi `\\contoso\shares\FileSyncExample` birden çok UNC yoluna eşlenir `\\FileSyncServer1\ExampleShare` `\\FileSyncServer2\DifferentShareName` `\\FileSyncServer3\ExampleShare` . Kullanıcı, Kullanıcı dostu UNC 'ye eriştiğinde, olası UNC yollarının bir listesi verilir ve Windows Server Active Directory (AD) site tanımlarına göre bunlara en yakın olanı seçer.

- Bir mantıksal veri kümesini boyut, GÇ veya diğer ölçek eşiklerine göre genişletin. Bu, her kullanıcının bir paylaşımda kendi klasörünü aldığı veya karalama paylaşımlarıyla, kullanıcıların geçici veri ihtiyaçlarını işlemek için rastgele alan aldığı Kullanıcı dizinleriyle ilgilenirken yaygındır. DFS ad alanları ile birden çok klasörü birlikte bir ortak ad alanında birleştirmenize yardımcı olursunuz. Örneğin, ile `\\contoso\shares\UserShares\user1` eşlenir `\\storageaccount.file.core.windows.net\user1` , ile `\\contoso\shares\UserShares\user2` eşlenir `\\storageaccount.file.core.windows.net\user2` .  

Aşağıdaki videoda genel bakış bölümünde, Azure dosyaları dağıtımıyla birlikte DFS ad alanları 'nı nasıl kullanacağınızı gösteren bir örnek görebilirsiniz.

[![Azure dosyaları ile DFS-N ayarlama hakkında tanıtım-oynatmak için tıklayın!](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> DFS ad alanlarını ayarlamayı öğrenmek için videoda 10:10 'e atlayın.

Zaten bir DFS ad alanınız varsa, bunu Azure dosyaları ve Dosya Eşitleme ile kullanmak için özel bir adım gerekmez. Azure dosya paylaşımınıza Şirket içinden erişiyorsanız, normal ağ hususları geçerlidir; daha fazla bilgi için bkz. [Azure dosyaları ağ iletişimi konuları](./storage-files-networking-overview.md) .

## <a name="namespace-types"></a>Ad alanı türleri
DFS ad alanları iki ana ad alanı türü sağlar:

- **Etki alanı tabanlı ad** alanı: WINDOWS Server ad etki alanınız kapsamında barındırılan bir ad alanı. AD 'nin bir parçası olarak barındırılan ad alanları, etki alanının adını (örneğin, etki alanınız) içeren bir UNC yoluna sahip olur `\\contoso.com\shares\myshare` `contoso.com` . Etki alanı tabanlı ad alanları, AD aracılığıyla daha büyük ölçek sınırlarını ve yerleşik artıklığı destekler. Etki alanı tabanlı ad alanları, bir yük devretme kümesinde kümelenmiş kaynak olamaz. 
- **Tek başına ad alanı**: WINDOWS Server ad 'nin bir parçası olarak barındırılmayan tek bir sunucuda barındırılan bir ad alanı. Tek başına ad alanları, tek başına sunucunuzun adı olduğu gibi tek başına sunucunun adına göre bir ada sahip olacaktır `\\MyStandaloneServer\shares\myshare` `MyStandaloneServer` . Tek başına ad alanları, etki alanı tabanlı ad alanlarından daha düşük ölçekli hedefleri destekler, ancak bir yük devretme kümesinde kümelenmiş kaynak olarak barındırılabilir.

## <a name="requirements"></a>Gereksinimler
DFS ad alanlarını Azure dosyaları ve Dosya Eşitleme kullanmak için aşağıdaki kaynaklara sahip olmanız gerekir:

- Active Directory etki alanı. Bu, şirket içi, Azure sanal makinesi (VM) veya başka bir bulutta olduğu gibi dilediğiniz yerde barındırılabilir.
- Ad alanını barındırasağlayan bir Windows Server. DFS ad alanları için ortak bir model dağıtım düzeni, ad alanlarını barındırmak için Active Directory etki alanı denetleyicisini kullanmaktır, ancak ad alanları, DFS ad alanları sunucu rolü yüklü olan herhangi bir sunucudan ayarlanabilir. DFS ad alanları, desteklenen tüm Windows Server sürümlerinde kullanılabilir.
- Etki alanına katılmış bir ortamda barındırılan, etki alanına katılmış bir depolama hesabında barındırılan bir Azure dosya paylaşımıyla veya Azure Dosya Eşitleme kullanarak etki alanına katılmış bir Windows dosya sunucusunda barındırılan bir dosya paylaşımında barındırılan bir SMB dosya paylaşımıdır. Depolama hesabınızda etki alanına katılma hakkında daha fazla bilgi için bkz. [kimlik tabanlı kimlik doğrulaması](storage-files-active-directory-overview.md). Windows dosya sunucuları, Azure Dosya Eşitleme kullanıyor olmalarından bağımsız olarak, etki alanına katılmış bir yoldur.
- DFS ad alanları ile birlikte kullanmak istediğiniz SMB dosya paylaşımları, şirket içi ağlarınızdan ulaşılabilir olmalıdır. Bu, genellikle Azure dosya paylaşımları için bir konudur, ancak teknik olarak Azure 'da veya başka bir bulutta barındırılan herhangi bir dosya paylaşımı için geçerlidir. Ağ hakkında daha fazla bilgi için bkz. [doğrudan erişim Için ağ değerlendirmeleri](storage-files-networking-overview.md).

## <a name="install-the-dfs-namespaces-server-role"></a>DFS ad alanları sunucu rolünü yükler
Zaten DFS ad alanları kullanıyorsanız veya etki alanı denetleyicinizde DFS ad alanları ayarlamak istiyorsanız, bu adımları güvenle atlayabilirsiniz.

# <a name="portal"></a>[Portal](#tab/azure-portal)
DFS ad alanları sunucu rolünü yüklemek için sunucunuzda Sunucu Yöneticisi açın. **Yönet**' i seçin ve ardından **rol ve Özellik Ekle**' yi seçin. Elde edilen sihirbaz, DFS ad alanlarını çalıştırmak ve yönetmek için gerekli Windows bileşenlerinin yüklenmesinde size kılavuzluk eder. 

Yükleme sihirbazının **yükleme türü** bölümünde **rol tabanlı veya özellik tabanlı yükleme** radyo düğmesini seçin ve **İleri ' yi** seçin. **Sunucu seçimi** bölümünde, DFS ad alanları sunucu rolünü yüklemek istediğiniz istediğiniz sunucuları seçin ve **İleri**' yi seçin. 

**Sunucu rolleri** bölümünde, rol listesinden **DFS ad alanları** rolünü seçin ve denetleyin. Bunu, **dosya ve depolama hizmetleri**  >  **dosyası ve iSCSI Hizmetleri** altında bulabilirsiniz. DFS ad alanları sunucu rolünü seçtiğinizde, zaten yüklü olmayan gerekli destekleyici sunucu rollerini veya özellikleri de ekleyebilir.

![* * DFS ad alanları * * rolü seçiliyken * * rol ve Özellik Ekleme * * Sihirbazı 'nın ekran görüntüsü.](./media/files-manage-namespaces/dfs-namespaces-install.png)

**DFS ad alanları** rolünü denetledikten sonra, sonraki tüm ekranlarda **İleri** ' yi seçebilir ve sihirbaz düğmeyi etkinleştirdiğinde hemen, **yükleyebilirsiniz** ' i seçebilirsiniz. Yükleme tamamlandığında, ad alanınızı yapılandırabilirsiniz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Yükseltilmiş bir PowerShell oturumundan (veya PowerShell uzaktan iletişimini kullanarak) aşağıdaki komutları yürütün.

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>Kök birleştirme ile mevcut sunucu adlarını alın
DFS ad alanları için önemli bir kullanım, dosya paylaşımlarının fiziksel yerleşimini yeniden düzenleme amacıyla var olan sunucu adından yararlanmalıdır. Örneğin, bir modernleştirme geçişi sırasında birden fazla eski dosya sunucusundan dosya paylaşımlarını tek bir dosya sunucusunda birleştirmek isteyebilirsiniz. Geleneksel olarak, Son Kullanıcı bilgisi ve belge bağlama sınırı, farklı dosya sunucularından dosya paylaşımlarını tek bir konakta birleştirme yeteneğinizi, ancak DFS ad alanları kök birleştirme özelliği, tek bir sunucuyu birden çok sunucu adına oluşturmanıza ve uygun paylaşım adına yönlendirmenize olanak tanır.

Çeşitli veri merkezi geçiş senaryolarında yararlı olsa da, kök birleştirme özellikle bulutta yerel Azure dosya paylaşımlarını benimseme için yararlıdır, çünkü:

- Azure dosya paylaşımları, mevcut şirket içi sunucu adlarını tutmanıza izin vermez.
- Azure dosya paylaşımlarına depolama hesabının tam etki alanı adı (FQDN) üzerinden erişilmelidir. Örneğin, depolama hesabında çağrılan bir Azure dosya paylaşımının `share` `storageaccount` her zaman UNC yolu aracılığıyla erişilebilir olması gerekir `\\storageaccount.file.core.windows.net\share` . Bu, kısa adı bekleyen son kullanıcılara kafa karıştırıcı olabilir (örn. `\\MyServer\share`) veya kuruluşun etki alanı adının (örn. bir alt etki alanı) bir adı. `\\MyServer.contoso.com\share`).

Kök birleştirme, yalnızca tek başına ad alanları ile kullanılabilir. Dosya paylaşımlarınız için mevcut bir etki alanı tabanlı ad alanınız zaten varsa, kök birleştirilmiş ad alanı oluşturmanız gerekmez.

### <a name="enabling-root-consolidation"></a>Kök birleştirmeyi etkinleştirme
Kök birleştirme, yükseltilmiş bir PowerShell oturumundan (veya PowerShell uzaktan iletişim kullanılarak) aşağıdaki kayıt defteri anahtarları ayarlanarak etkinleştirilebilir.

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>Mevcut dosya sunucusu adları için DNS girişleri oluşturma
DFS ad alanlarının mevcut dosya sunucusu adlarına yanıt vermesi için, DFS ad alanları sunucu adına işaret eden mevcut dosya sunucularınız için diğer ad (CNAME) kayıtları oluşturun. DNS kayıtlarınızı güncelleştirmeye yönelik tam yordam, kuruluşunuzun kullandığı sunuculara ve kuruluşunuzun DNS yönetimini otomatikleştirmek için özel araç kullanıyorsa, buna bağlı olarak değişebilir. Windows Server 'a dahil edilen ve Windows AD tarafından otomatik olarak kullanılan DNS sunucusu için aşağıdaki adımlar gösterilmiştir.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Bir Windows DNS sunucusunda, DNS Yönetim konsolunu açın. Bu, **Başlat** düğmesini seçerek ve **DNS** yazarak bulunabilir. Etki alanınız için ileriye doğru arama bölgesine gidin. Örneğin, etki alanınız ise ileriye doğru `contoso.com` arama bölgesi yönetim konsolundaki **ileriye doğru arama bölgeleri** altında bulunabilir  >  **`contoso.com`** . Bu iletişim kutusunda gösterilen tam hiyerarşi ağınızın DNS yapılandırmasına bağlı olacaktır.

İleriye doğru arama bölgenize sağ tıklayıp **Yeni diğer ad (CNAME)** seçeneğini belirleyin. Elde edilen iletişim kutusunda, değiştirdiğiniz dosya sunucusu için kısa adı girin (tam etki alanı adı, **tam etki alanı adı** olarak etiketlenmiş metin kutusunda otomatik olarak doldurulur). **Hedef ana bilgisayar Için tam etki alanı adı (FQDN)** etiketli metin kutusunda, AYARLAMıŞ olduğunuz DFS-N sunucusunun adını girin. İsterseniz sunucuyu seçmenize yardımcı olması için, **Araştır** düğmesini kullanabilirsiniz. Sunucunuzun CNAME kaydını oluşturmak için **Tamam ' ı** seçin.

![CNAME DNS girişi için * * yeni kaynak kaydını * * gösteren bir ekran görüntüsü.](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Bir Windows DNS sunucusunda, aşağıdaki komutları yürütmek için bir PowerShell oturumu açın (veya PowerShell uzaktan iletişimi kullanın) `$oldServer` ve `$dfsnServer` ortamınız için ilgili değerleri kullanarak ve, `$domain` etki alanı adıyla otomatik olarak doldurulur, ancak aynı zamanda el ile de yazabilirsiniz.

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>Ad alanı oluşturma
DFS ad alanları için temel yönetim birimi ad alanıdır. Ad alanı kökü veya adı, ad alanının başlangıç noktasıdır; Örneğin, UNC yolunda `\\contoso.com\Public\` , ad alanı kökü olur `Public` . 

Kök birleştirme ile var olan bir sunucu adını almak için DFS ad alanları kullanıyorsanız, ad alanının adı, almak istediğiniz sunucu adının adı olmalıdır ve karakteriyle sona erer `#` . Örneğin, adlı var olan bir sunucuyu almak isterseniz `MyServer` , adlı BIR DFS-N ad alanı oluşturursunuz `#MyServer` . Aşağıdaki PowerShell bölümü, ön bekleyen ' ın önüne girer `#` , ancak DFS yönetim konsolu aracılığıyla oluşturursanız, uygun şekilde sonuna kadar bir değer eklemeniz gerekir. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Yeni bir ad alanı oluşturmak için **DFS yönetim** konsolunu açın. Bu, **Başlat** düğmesini seçerek ve **DFS Yönetimi** yazarak bulunabilir. Elde edilen yönetim konsolunda iki bölüm **ad alanı** ve **çoğaltma** bulunur ve bunlar sırasıyla dfs ad ALANLARı ve DFS çoğaltma (DFS-R) öğesine başvurur. Azure Dosya Eşitleme, çoğaltma da istenirse, DFS-R yerine kullanılabilecek modern bir çoğaltma ve eşitleme mekanizması sağlar.

**Ad alanları** bölümünü seçin ve **Yeni ad alanı** düğmesini seçin (aynı zamanda, **ad alanları** bölümüne sağ tıklayabilirsiniz). Ortaya çıkan **Yeni ad alanı Sihirbazı** , ad alanı oluşturma işleminde size yol gösterir. 

Sihirbazın ilk bölümü, ad alanını barındıracak DFS ad alanı sunucusunu seçmenizi gerektirir. Birden çok sunucu bir ad alanını barındırabilir, ancak aynı anda tek bir sunucu ile DFS ad alanları ayarlamanız gerekir. İstenen DFS ad alanı sunucusunun adını girip **İleri ' yi** seçin. Ad **alanı adı ve ayarları** bölümünde, ad alanınız için istenen adı girip **İleri**' yi seçebilirsiniz. 

**Ad alanı türü** bölümü, **etki alanı tabanlı ad** alanı ve **tek başına ad alanı** arasında seçim yapmanıza olanak sağlar. Var olan bir dosya sunucusu/NAS cihaz adını korumak için DFS ad alanları kullanmayı planlıyorsanız, tek başına ad alanı seçeneğini seçmeniz gerekir. Diğer senaryolar için, etki alanı tabanlı bir ad alanı seçmelisiniz. Ad alanı türleri arasında seçim yapma hakkında daha fazla bilgi için yukarıdaki [ad alanı türlerine](#namespace-types) bakın.

![Etki alanı tabanlı ad alanı ve * * yeni ad alanı Sihirbazı * * içindeki tek başına ad alanı arasında seçim yapma ekran görüntüsü.](./media/files-manage-namespaces/dfs-namespace-type.png)

Ortamınız için istenen ad alanı türünü seçin ve Ileri ' **yi** seçin. Sihirbaz daha sonra oluşturulacak ad alanını özetler. Ad alanını oluşturmak için **Oluştur** ' u seçin ve iletişim kutusu tamamlandığında **kapatın** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
DFS ad alanı sunucusundaki bir PowerShell oturumunda, aşağıdaki PowerShell komutlarını yürütün, `$namespace` `$type` ve, ve `$takeOverName` ortamınız için ilgili değerleri ile birlikte koyun.

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>Klasörleri ve klasör hedeflerini yapılandırma
Bir ad alanının yararlı olması için klasörlerin ve klasör hedeflerinin olması gerekir. Her klasörün bir veya daha fazla klasör hedefi olabilir. Bu, bu içeriği barındıran SMB dosya paylaşımının işaretçileridir. Kullanıcılar klasör hedefleri olan bir klasöre gözattıklarında istemci bilgisayar, istemci bilgisayarı bir klasör hedeflerinden birine saydam bir şekilde yeniden yönlendiren bir başvuru alır. Ayrıca, ad alanına yapı ve hiyerarşi eklemek için klasör hedefleri olmayan klasörlere sahip olabilirsiniz.

DFS ad alanları klasörlerini dosya paylaşımlarına benzer şekilde düşünebilirsiniz. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
DFS yönetim konsolunda, az önce oluşturduğunuz ad alanını seçin ve **Yeni klasör**' ü seçin. Elde edilen **Yeni klasör** iletişim kutusu, hem klasörü hem de onun hedeflerini oluşturmanıza olanak sağlar.

![* * Yeni klasör * * iletişim kutusunun ekran görüntüsü.](./media/files-manage-namespaces/dfs-folder-targets.png)

Etiketli metin kutusunda klasörün **adını belirtin** . Bu klasör için klasör hedefleri eklemek için **Ekle...** öğesini seçin. Elde edilen **klasör hedefi Ekle** iletişim kutusu, Istenen klasöre UNC yolunu sağlayabileceğiniz **klasör hedefinin yolu** etiketli bir metin kutusu sağlar. **Klasör hedefi Ekle** Iletişim kutusunda **Tamam ' ı** seçin. Bir Azure dosya paylaşımında bir UNC yolu ekliyorsanız, sunucunun iletişim olmadığını bildiren bir ileti alabilirsiniz `storageaccount.file.core.windows.net` . Bu beklenen bir değer; devam etmek için **Evet** ' i seçin. Son olarak, klasör ve klasör hedeflerini oluşturmak için **Yeni klasör** Iletişim kutusunda **Tamam** ' ı seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

Artık bir ad alanı, bir klasör ve bir klasör hedefi oluşturduğunuza göre, DFS ad alanları aracılığıyla dosya paylaşımınızı bağlayabilmelisiniz. Etki alanı tabanlı bir ad alanı kullanıyorsanız, paylaşımınızın tam yolu olmalıdır `\\<domain-name>\<namespace>\<share>` . Tek başına ad alanı kullanıyorsanız, paylaşımınızın tam yolu olmalıdır `\\<DFS-server>\<namespace>\<share>` . Kök birleştirme ile tek başına ad alanı kullanıyorsanız, eski sunucu adınızla (gibi) doğrudan erişim sağlayabilirsiniz `\\<old-server>\<share>` .

## <a name="see-also"></a>Ayrıca bkz.
- Azure dosya paylaşımının dağıtımı: [bir Azure dosyaları dağıtımını planlama](storage-files-planning.md) ve [bir dosya paylaşımının oluşturulması](storage-how-to-create-file-share.md).
- Dosya paylaşma erişimini yapılandırma: [doğrudan erişim Için](storage-files-networking-overview.md) [kimlik tabanlı kimlik doğrulama](storage-files-active-directory-overview.md) ve ağ değerlendirmeleri.
- [Windows Server Dağıtılmış Dosya Sistemi ad alanları](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)