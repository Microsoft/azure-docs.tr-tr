---
title: Kullanılabilirlik grubunu yapılandırma (Azure hızlı başlangıç şablonu)
description: Windows Yük devretme kümesi oluşturmak, kümeye SQL Server VM 'Leri eklemek, dinleyiciyi oluşturmak ve Azure 'da iç yük dengeleyiciyi yapılandırmak için Azure hızlı başlangıç şablonları 'nı kullanın.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022376"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Azure VM 'de SQL Server için bir kullanılabilirlik grubu yapılandırmak üzere Azure hızlı başlangıç şablonlarını kullanma
Bu makalede, Azure 'da SQL Server sanal makineler için her zaman açık kullanılabilirlik grubu yapılandırmasının dağıtımını kısmen otomatik hale getirmek üzere Azure hızlı başlangıç şablonlarının nasıl kullanılacağı açıklanır. Bu işlemde iki Azure hızlı başlangıç şablonu kullanılır: 

   | Şablon | Açıklama |
   | --- | --- |
   | [101-SQL-VM-AG-kurulum](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Windows Yük devretme kümesini oluşturur ve SQL Server VM 'lerine birleştirir. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Kullanılabilirlik grubu dinleyicisini oluşturur ve iç yük dengeleyiciyi yapılandırır. Bu şablon yalnızca Windows Yük devretme kümesi **101-SQL-VM-AG-Setup** şablonuyla oluşturulduysa kullanılabilir. |
   | &nbsp; | &nbsp; |

Kullanılabilirlik grubu yapılandırmasının diğer bölümlerinin, kullanılabilirlik grubu oluşturma ve iç yük dengeleyiciyi oluşturma gibi el ile yapılması gerekir. Bu makalede otomatik ve el ile yapılan adımların sırası sağlanmaktadır.
 

## <a name="prerequisites"></a>Önkoşullar 
Hızlı Başlangıç şablonlarını kullanarak her zaman açık kullanılabilirlik grubunun kurulumunu otomatik hale getirmek için aşağıdaki önkoşullara sahip olmanız gerekir: 
- Bir [Azure aboneliği](https://azure.microsoft.com/free/).
- Etki alanı denetleyicisi olan bir kaynak grubu. 
- Azure 'da, aynı Kullanılabilirlik kümesi veya kullanılabilirlik bölgesindeki ve [SQL VM kaynak sağlayıcısına kaydedilmiş](virtual-machines-windows-sql-register-with-resource-provider.md) [SQL Server 2016 (veya üzeri) Enterprise Edition çalıştıran](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) , etki alanına katılmış bir veya daha fazla VM.  
- Biri iç yük dengeleyici için, diğeri ise kullanılabilirlik grubuyla aynı alt ağdaki kullanılabilirlik grubu dinleyicisi için olmak üzere iki kullanılabilir (herhangi bir varlık tarafından kullanılmaz) IP adresleri. Mevcut bir yük dengeleyici kullanılıyorsa, yalnızca bir tane kullanılabilir IP adresi gerekir.  

## <a name="permissions"></a>İzinler
Azure hızlı başlangıç şablonlarını kullanarak Always on kullanılabilirlik grubunu yapılandırmak için aşağıdaki izinler gereklidir: 

- Etki alanında **bilgisayar nesnesi oluşturma** iznine sahip olan mevcut bir etki alanı kullanıcı hesabı.  Örneğin, bir etki alanı yönetici hesabı genellikle yeterli izne sahiptir (örneğin: account@domain.com). _Bu hesap, kümeyi oluşturmak için her VM 'de yerel yönetici grubunun da bir parçası olmalıdır._
- SQL Server hizmetini denetleyen etki alanı kullanıcı hesabı. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>1\. Adım: bir hızlı başlangıç şablonu kullanarak yük devretme kümesi oluşturma ve SQL Server VM 'Leri kümeye katma 
SQL Server sanal makinelerinizin SQL VM kaynak sağlayıcısına kaydolduktan sonra, SQL Server sanal *makinelerinizi Sqlvirtualmachinegroups*öğesine ekleyebilirsiniz. Bu kaynak Windows Yük devretme kümesinin meta verilerini tanımlar. Meta veriler, hem kümeyi hem de SQL Server hizmetini ve depolama hesabını bulut tanığı olarak yönetmek için sürüm, sürüm, tam etki alanı adı, Active Directory hesapları içerir. 

SQL Server VM 'Leri *Sqlvirtualmachinegroups* kaynak grubuna eklemek Için Windows Yük devretme kümesi hizmeti 'ni önyükleme ve ardından SQL Server bu sanal makineleri bu kümeye birleştirir. Bu adım **101-SQL-VM-AG-Setup** hızlı başlangıç şablonuyla otomatikleştirilir. Aşağıdaki adımları kullanarak gerçekleştirebilirsiniz:

1. [**101-SQL-VM-AG-Setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) hızlı başlangıç şablonuna gidin. Ardından, Azure portal hızlı başlangıç şablonunu açmak için **Azure 'A dağıt** ' ı seçin.
1. Windows Yük devretme kümesi için meta verileri yapılandırmak üzere gerekli alanları doldurun. İsteğe bağlı alanları boş bırakabilirsiniz.

   Aşağıdaki tabloda, şablon için gerekli değerler gösterilmektedir: 

   | **Alan** | Değer |
   | --- | --- |
   | **Abonelik** |  SQL Server sanal makinelerinizin bulunduğu abonelik. |
   |**Kaynak grubu** | SQL Server sanal makinelerinizin bulunduğu kaynak grubu. | 
   |**Yük devretme kümesi adı** | Yeni Windows Yük devretme kümeniz için istediğiniz ad. |
   | **Mevcut VM listesi** | Kullanılabilirlik grubuna katılmasını istediğiniz sanal makineler SQL Server ve bu yeni kümenin bir parçası olmalıdır. Bu değerleri virgül ve bir boşluk ile ayırın (örneğin: *SQLVM1, SQLVM2*). |
   | **SQL Server sürümü** | SQL Server sanal makinelerinizin SQL Server sürümü. Açılır listeden seçin. Şu anda yalnızca SQL Server 2016 ve SQL Server 2017 görüntü desteklenir. |
   | **Var olan tam etki alanı adı** | SQL Server sanal makinelerinizin bulunduğu etki alanı için mevcut FQDN. |
   | **Var olan etki alanı hesabı** | Şablon dağıtımı sırasında [CNO](/windows-server/failover-clustering/prestage-cluster-adds) olarak etki alanında **bilgisayar nesnesi oluşturma** iznine sahip olan mevcut bir etki alanı kullanıcı hesabı. Örneğin, bir etki alanı yönetici hesabı genellikle yeterli izne sahiptir (örneğin: account@domain.com). *Bu hesap, kümeyi oluşturmak için her VM 'de yerel yönetici grubunun da bir parçası olmalıdır.*| 
   | **Etki alanı hesabı parolası** | Daha önce bahsedilen etki alanı kullanıcı hesabının parolası. | 
   | **Mevcut SQL hizmeti hesabı** | Kullanılabilirlik grubu dağıtımı sırasında [SQL Server hizmetini](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) denetleyen etki alanı kullanıcı hesabı (örneğin: account@domain.com). |
   | **SQL hizmeti parolası** | SQL Server hizmetini denetleyen etki alanı kullanıcı hesabı tarafından kullanılan parola. |
   | **Bulut tanığı adı** | Bulut tanığı için oluşturulacak ve kullanılacak yeni bir Azure depolama hesabı. Bu adı değiştirebilirsiniz. |
   | **\_yapıt konumu** | Bu alan varsayılan olarak ayarlanır ve değiştirilmemelidir. |
   | **\_yapıt konumu SAS belirteci** | Bu alan kasıtlı olarak boş bırakılır. |
   | &nbsp; | &nbsp; |

1. Hüküm ve koşulları kabul ediyorsanız, **yukarıda belirtilen hüküm ve koşulları kabul** ediyorum onay kutusunu işaretleyin. Ardından, hızlı başlangıç şablonunun dağıtımını son vermek için **satın al** ' ı seçin. 
1. Dağıtımınızı izlemek için, üst gezinti başlığındaki **Bildirimler** Bell simgesinden dağıtımı seçin ya da Azure Portal **kaynak grubuna** gidin. **Ayarlar**' ın altında **dağıtımlar** ' ı seçin ve **Microsoft. Template** dağıtımını seçin. 

>[!NOTE]
> Şablon dağıtımı sırasında belirtilen kimlik bilgileri yalnızca dağıtım uzunluğu için depolanır. Dağıtım tamamlandıktan sonra, bu parolalar kaldırılır. Kümeye daha fazla SQL Server VM eklerseniz bunları yeniden sağlamanız istenecektir. 


## <a name="step-2-manually-create-the-availability-group"></a>2\. Adım: kullanılabilirlik grubunu el Ile oluşturma 
Kullanılabilirlik grubunu, [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)veya [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)kullanarak normal şekilde yaptığınız şekilde el ile oluşturun. 

>[!IMPORTANT]
> Şu anda bir *dinleyici oluşturmayın:* **101-SQL-VM-aglistener-Setup** hızlı başlangıç şablonu, adım 4 ' te otomatik olarak yapılır. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>3\. Adım: iç yük dengeleyiciyi El Ile oluşturma
Always on kullanılabilirlik grubu dinleyicisi bir Azure Load Balancer iç örneğini gerektirir. İç yük dengeleyici, daha hızlı yük devretme ve yeniden bağlantı sağlayan kullanılabilirlik grubu dinleyicisi için "kayan" bir IP adresi sağlar. Bir kullanılabilirlik grubundaki SQL Server VM 'Ler aynı Kullanılabilirlik kümesinin parçasıysa, temel bir yük dengeleyici kullanabilirsiniz. Aksi takdirde, standart yük dengeleyici kullanmanız gerekir. 

> [!IMPORTANT]
> İç yük dengeleyici SQL Server VM örneklerle aynı sanal ağda olmalıdır. 

Yalnızca iç yük dengeleyici oluşturmanız gerekir. 4\. adımda, **101-SQL-VM-aglistener-Setup** hızlı başlangıç şablonu, yapılandırmanın geri kalanını (arka uç havuzu, sistem durumu araştırması ve yük dengeleme kuralları gibi) işler. 

1. Azure portal, SQL Server sanal makinelerini içeren kaynak grubunu açın. 
2. Kaynak grubunda **Ekle**' yi seçin.
3. **Yük dengeleyici**için arama yapın. Arama sonuçlarında, **Microsoft**tarafından yayınlanan **Load Balancer**' yi seçin.
4. **Load Balancer** dikey penceresinde **Oluştur**' u seçin.
5. **Yük dengeleyici oluştur** iletişim kutusunda yük dengeleyiciyi şu şekilde yapılandırın:

   | Ayar | Değer |
   | --- | --- |
   | **Adı** |Yük dengeleyiciyi temsil eden bir metin adı girin. Örneğin, **Sqllb**yazın. |
   | **Tür** |**İç**: çoğu uygulama, aynı sanal ağ içindeki uygulamaların kullanılabilirlik grubuna bağlanmasına izin veren bir iç yük dengeleyici kullanır.  </br> **Harici**: uygulamaların genel bir internet bağlantısı aracılığıyla kullanılabilirlik grubuna bağlanmasına izin verir. |
   | **Sanal ağ** | SQL Server örneklerinin bulunduğu sanal ağı seçin. |
   | **Alt ağ** | SQL Server örneklerinin bulunduğu alt ağı seçin. |
   | **IP adresi ataması** |**Se** |
   | **Özel IP adresi** | Alt ağdan kullanılabilir bir IP adresi belirtin. |
   | **Abonelik** |Birden çok aboneliğiniz varsa, bu alan görünebilir. Bu kaynakla ilişkilendirmek istediğiniz aboneliği seçin. Normalde kullanılabilirlik grubu için tüm kaynaklarla aynı abonelikte olur. |
   | **Kaynak grubu** |SQL Server örneklerinin bulunduğu kaynak grubunu seçin. |
   | **Konum** |SQL Server örneklerinin bulunduğu Azure konumunu seçin. |
   | &nbsp; | &nbsp; |

6. **Oluştur**'u seçin. 


>[!IMPORTANT]
> Her bir SQL Server VM genel IP kaynağının standart yük dengeleyiciye uyumlu olması için standart bir SKU 'SU olmalıdır. SANAL makinenizin genel IP kaynağının SKU 'sunu belirlemek için **kaynak grubu**' na gidin, SQL Server VM IÇIN **genel IP adresi** kaynağınızı seçin ve **genel bakış** bölmesinde **SKU** altındaki değeri bulun. 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>4\. Adım: kullanılabilirlik grubu dinleyicisini oluşturma ve hızlı başlangıç şablonunu kullanarak iç yük dengeleyiciyi yapılandırma

Kullanılabilirlik grubu dinleyicisini oluşturun ve **101-SQL-VM-aglistener-Setup** hızlı başlangıç şablonunu kullanarak iç yük dengeleyiciyi otomatik olarak yapılandırın. Şablon, Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups/kullanılabilirliği Bilitygrouplistener kaynağını sağlar. **101-SQL-VM-aglistener-Setup** hızlı başlangıç şablonu, SQL VM kaynak sağlayıcısı aracılığıyla aşağıdaki eylemleri yapar:

- Dinleyici için yeni bir ön uç IP kaynağı (dağıtım sırasında belirtilen IP adresi değerine göre) oluşturur. 
- Küme ve iç yük dengeleyici için ağ ayarlarını yapılandırır. 
- İç yük dengeleyici, sistem durumu araştırması ve yük dengeleme kuralları için arka uç havuzunu yapılandırır.
- Verilen IP adresi ve adı ile kullanılabilirlik grubu dinleyicisi oluşturur.
 
>[!NOTE]
> **101-SQL-VM-aglistener-Setup** ' i yalnızca Windows Yük devretme kümesi **101-SQL-VM-AG-Setup** şablonuyla oluşturulduysa kullanabilirsiniz.
   
   
İç yük dengeleyiciyi yapılandırmak ve kullanılabilirlik grubu dinleyicisini oluşturmak için aşağıdakileri yapın:
1. [101-SQL-VM-aglistener-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) hızlı başlangıç şablonuna gidin ve Azure Portal Hızlı Başlangıç şablonunu başlatmak için **Azure 'a dağıt** ' ı seçin.
1. İç yük dengeleyiciyi yapılandırmak için gerekli alanları doldurun ve kullanılabilirlik grubu dinleyicisini oluşturun. İsteğe bağlı alanları boş bırakabilirsiniz. 

   Aşağıdaki tabloda, şablon için gerekli değerler gösterilmektedir: 

   | **Alan** | Değer |
   | --- | --- |
   |**Kaynak grubu** | SQL Server sanal makinelerinizin ve kullanılabilirlik grubunuzun bulunduğu kaynak grubu. | 
   |**Mevcut yük devretme kümesi adı** | SQL Server sanal makinelerinizin katıldığı kümenin adı. |
   | **Mevcut SQL kullanılabilirlik grubu**| SQL Server sanal makinelerinizin parçası olduğu kullanılabilirlik grubunun adı. |
   | **Mevcut VM listesi** | Daha önce bahsedilen kullanılabilirlik grubunun parçası olan SQL Server VM 'lerinin adları. Adları virgül ve boşluk ile ayırın (örneğin: *SQLVM1, SQLVM2*). |
   | **Oluşturulurken** | Dinleyiciye atamak istediğiniz DNS adı. Varsayılan olarak, bu şablon "aglistener" adını belirtir, ancak bunu değiştirebilirsiniz. Ad en fazla 15 karakter uzunluğunda olmalıdır. |
   | **Dinleyici bağlantı noktası** | Dinleyicisinin kullanmasını istediğiniz bağlantı noktası. Genellikle, bu bağlantı noktası varsayılan olarak 1433 olmalıdır. Bu, şablonun belirttiği bağlantı noktası numarasıdır. Ancak varsayılan bağlantı noktası değiştirildiyse, bunun yerine dinleyici bağlantı noktası bu değeri kullanmalıdır. | 
   | **Dinleyici IP 'si** | Dinleyicisinin kullanmasını istediğiniz IP adresi. Bu adres, şablon dağıtımı sırasında oluşturulacaktır, bu nedenle zaten kullanımda olmayan bir tane sağlayın.  |
   | **Mevcut alt ağ** | SQL Server sanal makinelerinizin iç alt ağının adı (örneğin: *varsayılan*). Bu değeri **kaynak grubuna**giderek, Sanal ağınızı seçerek, **Ayarlar** bölmesinde **alt ağlar** ' ı seçerek ve değeri **ad**' ın altına kopyalayarak belirleyebilirsiniz. |
   | **Mevcut Iç Load Balancer** | Adım 3 ' te oluşturduğunuz iç yük dengeleyicinin adı. |
   | **Araştırma bağlantı noktası** | İç yük dengeleyicinin kullanmasını istediğiniz araştırma bağlantı noktası. Şablon varsayılan olarak 59999 kullanır, ancak bu değeri değiştirebilirsiniz. |
   | &nbsp; | &nbsp; |

1. Hüküm ve koşulları kabul ediyorsanız, **yukarıda belirtilen hüküm ve koşulları kabul** ediyorum onay kutusunu işaretleyin. Hızlı başlangıç şablonunun dağıtımını son vermek için **satın al** ' ı seçin. 
1. Dağıtımınızı izlemek için, üst gezinti başlığındaki **Bildirimler** Bell simgesinden dağıtımı seçin ya da Azure Portal **kaynak grubuna** gidin. **Ayarlar**' ın altında **dağıtımlar** ' ı seçin ve **Microsoft. Template** dağıtımını seçin. 

>[!NOTE]
>Dağıtımınız yarı olarak başarısız olursa, **101-SQL-VM-aglistener-Setup** hızlı başlangıç şablonunu yeniden dağıtmadan önce PowerShell 'i kullanarak [Yeni oluşturulan dinleyiciyi](#remove-the-availability-group-listener) el ile kaldırmanız gerekir. 

## <a name="remove-the-availability-group-listener"></a>Kullanılabilirlik grubu dinleyicisini kaldır
Daha sonra şablonun yapılandırıldığı kullanılabilirlik grubu dinleyicisini kaldırmanız gerekirse, SQL VM kaynak sağlayıcısı ' na gitmeniz gerekir. Dinleyici SQL VM kaynak sağlayıcısı aracılığıyla kaydedildiğinden, SQL Server Management Studio aracılığıyla silmeniz yeterlidir. 

En iyi yöntem, PowerShell 'de aşağıdaki kod parçacığını kullanarak SQL VM kaynak sağlayıcısı aracılığıyla bunu silmektir. Bunun yapılması, kullanılabilirlik grubu dinleyicisi meta verilerini SQL VM kaynak sağlayıcısından kaldırır. Ayrıca, dinleyiciyi kullanılabilirlik grubundan fiziksel olarak siler. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Sık karşılaşılan hatalar
Bu bölümde bazı bilinen sorunlar ve olası çözümleri açıklanmaktadır. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>'\<AG-name > ' kullanılabilirlik grubunun kullanılabilirlik grubu dinleyicisi zaten var
Kullanılabilirlik grubu dinleyicisi için Azure hızlı başlangıç şablonunda kullanılan seçili kullanılabilirlik grubu zaten bir dinleyici içeriyor. Kullanılabilirlik grubu dahilinde fiziksel olarak veya meta verileri SQL VM kaynak sağlayıcısı içinde kalır. **101-SQL-VM-aglistener-Setup** hızlı başlangıç şablonunu yeniden dağıtmaya başlamadan önce [PowerShell](#remove-the-availability-group-listener) 'i kullanarak dinleyiciyi kaldırın. 

### <a name="connection-only-works-from-primary-replica"></a>Bağlantı yalnızca birincil çoğaltmadan kullanılabilir
Bu davranış, iç yük dengeleyicinin yapılandırmasını tutarsız bir durumda bıraktı, başarısız bir **101-SQL-VM-aglistener-Setup** şablon dağıtımından kaynaklanıyor olabilir. Arka uç havuzunun kullanılabilirlik kümesini listelediğinden ve sistem durumu araştırması ve yük dengeleme kuralları için bu kuralların mevcut olduğunu doğrulayın. Herhangi bir şey eksikse, iç yük dengeleyicinin yapılandırması tutarsız bir durumdur. 

Bu davranışı çözümlemek için, [PowerShell](#remove-the-availability-group-listener)'i kullanarak dinleyiciyi kaldırın, Azure Portal aracılığıyla iç yük dengeleyiciyi silin ve adım 3 ' te yeniden başlatın. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>Rozet Isteği-yalnızca SQL sanal makine listesi güncelleştirilemiyor
Bu hata, dinleyicinin SQL Server Management Studio (SSMS) ile silinmiş ancak SQL VM kaynak sağlayıcısından silinmediğinden **101-SQL-VM-aglistener-Setup** şablonunu dağıttığınızda meydana gelebilir. SSD 'ler aracılığıyla dinleyicinin silinmesi, SQL VM kaynak sağlayıcısından dinleyicinin meta verilerini kaldırmaz. Dinleyici, [PowerShell](#remove-the-availability-group-listener)aracılığıyla kaynak sağlayıcısından silinmelidir. 

### <a name="domain-account-does-not-exist"></a>Etki alanı hesabı yok
Bu hatanın iki nedeni olabilir. Belirtilen etki alanı hesabı yok veya [Kullanıcı asıl adı (UPN)](/windows/desktop/ad/naming-properties#userprincipalname) verileri eksik. **101-SQL-VM-AG-Setup** şablonu, UPN biçiminde (yani *user@domain.com* ) bir etki alanı hesabı bekler, ancak bazı etki alanı hesaplarında bu hesap eksik olabilir. Bu genellikle, sunucu bir etki alanı denetleyicisine yükseltildiğinde veya PowerShell aracılığıyla bir kullanıcı oluşturulduğunda, yerel bir Kullanıcı ilk etki alanı yönetici hesabı olacak şekilde geçirildiğinde gerçekleşir. 

Hesabın var olduğunu doğrulayın. Bu durumda, ikinci duruma çalışıyor olabilirsiniz. Bunu çözmek için aşağıdakileri yapın:

1. Etki alanı denetleyicisinde, **Sunucu Yöneticisi** **Araçlar** seçeneğinde **Active Directory Kullanıcıları ve bilgisayarları** penceresini açın. 
2. Sol bölmedeki **Kullanıcılar** ' i seçerek hesaba gidin.
3. Hesaba sağ tıklayın ve **Özellikler**' i seçin.
4. **Hesap** sekmesini seçin. **Kullanıcı oturum açma adı** kutusu boş ise, bu hatanın nedendir. 

    ![Boş Kullanıcı hesabı eksik UPN 'yi gösterir](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Kullanıcının adıyla eşleşecek şekilde **Kullanıcı oturum açma adı** kutusunu doldurup aşağı açılan listeden doğru etki alanını seçin. 
6. Değişikliklerinizi kaydetmek için **Uygula** ' yı seçin ve **Tamam**' ı seçerek iletişim kutusunu kapatın. 

Bu değişiklikleri yaptıktan sonra Azure hızlı başlangıç şablonunu daha fazla dağıtmayı deneyin. 



## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makalelere bakın: 

* [SQL Server VM 'lerine genel bakış](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server VM 'Ler için SSS](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server VM 'Leri için fiyatlandırma Kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server VM 'Ler için sürüm notları](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [SQL Server VM için lisans modellerini değiştirme](virtual-machines-windows-sql-ahb.md)



