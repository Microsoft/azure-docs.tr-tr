---
title: Yönetici Kılavuzu Azure Lab Services | Microsoft Docs
description: Bu kılavuz, Azure Lab Services kullanarak laboratuvar hesapları oluşturan ve yöneten yöneticilere yardımcı olur.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 3ad3ee38a6c08a6af85822d76012cc6dfc34ff4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462470"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services Yönetici Kılavuzu
Bir University 'in bulut kaynaklarını yöneten bilgi teknolojisi (BT) yöneticileri, okulunuzun laboratuvar hesabını ayarlamaktan genellikle sorumludur. Laboratuvar hesabı ayarladıktan sonra Yöneticiler veya eğitimciler, hesapta yer alan laboratuvarları oluşturur. Bu makalede, söz konusu Azure kaynaklarına ve bunları oluşturmaya yönelik kılavuza yönelik yüksek düzeyde bir genel bakış sunulmaktadır.

![Laboratuvar hesabındaki Azure kaynaklarının üst düzey görünümünün diyagramı.](./media/administrator-guide/high-level-view.png)

- Laboratuvarlar Azure Lab Services sahip bir Azure aboneliği içinde barındırılır.
- Laboratuvar hesapları, paylaşılan görüntü Galerisi ve görüntü sürümleri aboneliğinizde barındırılır.
- Laboratuvar hesabınıza ve paylaşılan görüntü galerinize aynı kaynak grubunda sahip olabilirsiniz. Bu diyagramda, farklı kaynak gruplarında yer alırlar.

Mimari hakkında daha fazla bilgi için bkz. [Labs mimari temelleri](./classroom-labs-fundamentals.md).

## <a name="subscription"></a>Abonelik
Üniversiteniz bir veya daha fazla Azure aboneliğine sahip olabilir. Laboratuvar hesapları da dahil olmak üzere, içinde kullanılan tüm Azure kaynakları ve hizmetleri için faturalandırma ve güvenliği yönetmek üzere abonelikleri kullanırsınız.

Laboratuvar hesabı ve aboneliği arasındaki ilişki önemlidir çünkü:

- Faturalandırma, laboratuvar hesabını içeren abonelikle raporlanır.
- Abonelik Azure Active Directory (Azure AD) kiracısındaki kullanıcılara Azure Lab Services için erişim izni verebilirsiniz. Bir kullanıcıyı laboratuvar hesabı sahibi veya katkıda bulunan veya laboratuvar Oluşturucu ya da laboratuvar sahibi olarak ekleyebilirsiniz.

Labs ve sanal makineleri (VM 'Ler), Azure Lab Services sahip olduğu bir abonelikte sizin için yönetilir ve barındırılır.

## <a name="resource-group"></a>Kaynak grubu
Bir abonelik bir veya daha fazla kaynak grubu içeriyor. Kaynak grupları, aynı çözüm içinde birlikte kullanılan Azure kaynakları mantıksal gruplandırmaları oluşturmak için kullanılır.  

Laboratuvar hesabı oluşturduğunuzda, laboratuvar hesabını içeren kaynak grubunu yapılandırmanız gerekir. 

Bir kaynak grubu, [paylaşılan bir görüntü Galerisi](#shared-image-gallery)oluşturduğunuzda da gereklidir. Laboratuvar hesabınızı ve paylaşılan görüntü galerinizi aynı kaynak grubuna veya iki ayrı kaynak grubuna yerleştirebilirsiniz. Görüntü galerisini çeşitli çözümler arasında paylaşmayı planlıyorsanız bu ikinci yaklaşımı düşünebilirsiniz.

Bir laboratuvar hesabı oluşturduğunuzda, paylaşılan bir görüntü galerisini aynı anda otomatik olarak oluşturup ekleyebilirsiniz.  Bu seçenek, laboratuvar hesabı ve paylaşılan görüntü galerisinin ayrı kaynak gruplarında oluşturulmasını de sonuçlanır. [Laboratuvar hesabı oluşturma öğreticisinde paylaşılan görüntüyü Yapılandır galerisinde](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) açıklanan adımları izlediğinizde bu davranışı görürsünüz. Bu makalenin başındaki görüntü bu yapılandırmayı kullanır. 

Bir laboratuvar hesabını veya paylaşılan görüntü Galerisi kaynak grubunu oluşturulduktan sonra *değiştirmek mümkün olmadığından* , kaynak gruplarınızın yapısını planlamak için zaman önünü yatırmanız önerilir. Bu kaynaklar için kaynak grubunu değiştirmeniz gerekiyorsa, laboratuvar hesabınızı veya paylaşılan görüntü galerinizi silip yeniden oluşturmanız gerekir.

## <a name="lab-account"></a>Laboratuvar hesabı

Laboratuvar hesabı bir veya daha fazla laboratuvarda kapsayıcı görevi görür. Azure Lab Services kullanmaya başladıysanız, en yaygın olarak tek bir laboratuar hesabı vardır. Laboratuvar kullanımınız ölçeklenirken daha sonra daha fazla laboratuvar hesabı oluşturmayı tercih edebilirsiniz.

Aşağıdaki listede birden çok laboratuvar hesabının faydalı olabileceği senaryolar vurgulanmıştır:

- **Laboratuvarlar genelinde farklı ilke gereksinimlerini yönetme**

    Bir laboratuvar hesabı ayarlarken, laboratuvar hesabı altındaki *Tüm* laboratuvarlara uygulanan ilkeler ayarlarsınız; örneğin:
    - Laboratuvarın erişebileceği paylaşılan kaynaklarla Azure sanal ağı. Örneğin, bir sanal ağ içindeki paylaşılan bir veri kümesine erişmesi gereken bir Labs kümesine sahip olabilirsiniz.
    - Laboratuvarların VM oluşturmak için kullanabileceği sanal makine görüntüleri. Örneğin, Linux Azure Market görüntüsü [için veri bilimi VM'si](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) erişmesi gereken bir laboratuvar kümesine sahip olabilirsiniz.

    Laboratuvarlarınızın her birinde benzersiz ilke gereksinimleri varsa, her bir laboratuvarı ayrı ayrı yönetmek için ayrı laboratuvar hesapları oluşturmak yararlı olabilir.

- **Her laboratuvar hesabına ayrı bir bütçe atayın**
  
    Tek bir laboratuar hesabı aracılığıyla tüm Laboratuvar maliyetlerini raporlamak yerine, daha açık bir bütçeye daha açık bir bütçe olması gerekebilir. Örneğin, bir bütçeyi Departmanlar arasında dağıtmak için University 'in Math departmanı, bilgisayar bilimi departmanı ve benzeri ayrı laboratuvar hesapları oluşturabilirsiniz.  Böylece, [Azure maliyet yönetimi](../cost-management-billing/cost-management-billing-overview.md)'ni kullanarak her bir laboratuvar hesabının maliyetini görüntüleyebilirsiniz.

- **Pilot laboratuvarları etkin veya üretim laboratuvarlarından yalıtın**
  
    Etkin veya üretim Laboratuvarlarınızı etkilemeksizin bir laboratuvar hesabı için ilke değişikliklerini pilot yapmak istediğiniz durumlar olabilir. Bu senaryoda, pilot çalışması amaçlarıyla ayrı bir laboratuvar hesabı oluşturmak, değişiklikleri yalıtmanızı sağlar. 

## <a name="lab"></a>Laboratuvar

Laboratuvar, her biri tek bir öğrenciye atanmış olan VM 'Leri içerir.  Genel olarak şunları yapabilirsiniz:

- Her sınıf için bir laboratuvarınız vardır.
- Her Semester, çeyrek veya kullandığınız diğer akademik sistem için yeni bir laboratuvar kümesi oluşturun. Aynı görüntüyü kullanması gereken sınıflar için, [paylaşılan bir görüntü Galerisi](#shared-image-gallery)kullanmanız gerekir. Bu şekilde, laboratuvarları ve akademik dönemleri kapsayan görüntüleri yeniden kullanabilirsiniz.

Laboratuvarlarınızı nasıl yapılandıracağınızı belirlerken, aşağıdaki noktaları göz önünde bulundurun:

- **Bir laboratuvardaki tüm VM 'Ler yayımlanan aynı görüntüyle dağıtılır**

    Sonuç olarak, aynı anda farklı laboratuvar görüntülerinin yayımlanmasını gerektiren bir sınıfınız varsa, her bir görüntü için ayrı bir laboratuvar oluşturulması gerekir.
  
- **Kullanım kotası laboratuvar düzeyinde ayarlanır ve laboratuvardaki tüm kullanıcılar için geçerlidir**

    Kullanıcılara farklı kotalar ayarlamak için ayrı Labs oluşturmanız gerekir. Ancak, kotayı ayarladıktan sonra belirli kullanıcılara daha fazla saat eklemek mümkündür.
  
- **Başlatma veya kapatılma zamanlaması laboratuvar düzeyinde ayarlanır ve laboratuvardaki tüm VM 'lere uygulanır**

    Kota ayarına benzer şekilde, kullanıcılar için farklı zamanlamalar ayarlamanız gerekiyorsa, her zamanlama için ayrı bir laboratuvar oluşturmanız gerekir.

Varsayılan olarak, her laboratuvarın kendi sanal ağı vardır.  Sanal ağ eşlemesi etkinse, her laboratuvarın belirtilen sanal ağla birlikte kendi alt ağı olur.

## <a name="shared-image-gallery"></a>Paylaşılan görüntü Galerisi

Paylaşılan görüntü Galerisi bir laboratuvar hesabına iliştirilir ve görüntüleri depolamak için merkezi bir depo işlevi görür. Bir eğitimci, bir laboratuvarın şablon VM 'sinden dışarı aktarmayı seçtiğinde galeriye kaydedilir. Bir eğitimci, şablon VM 'de değişiklik yaptığında ve dışarı aktardığında görüntünün yeni sürümleri kaydedilir ve önceki sürümler korunur.

Eğitmenler, yeni bir laboratuvar oluşturduklarında paylaşılan görüntü galerisinden bir görüntü sürümü yayımlayabilir. Galeri bir görüntünün birden çok sürümünü depolasa da, eğitimciler Laboratuvar oluşturma sırasında yalnızca en son sürümü seçebilir.

Paylaşılan görüntü Galerisi hizmeti, yalnızca birkaç laboratuvardan başlatıyorsanız, hemen ihtiyacınız olmayan isteğe bağlı bir kaynaktır. Ancak, paylaşılan görüntü Galerisi, ek laboratuvarlara kadar ölçeklendirme yaparken yararlı olan birçok avantaj sunar:

- **Bir şablon VM görüntüsünün sürümlerini kaydedebilir ve yönetebilirsiniz**

    Genel Azure Marketi galerisindeki bir görüntüye özel bir görüntü oluşturmak veya değişiklikler (yazılım, yapılandırma vb.) yapmak yararlı olabilir.  Örneğin, eğitimciler 'in farklı yazılımlar veya Araçlar yüklenmesi gerekir. Öğrencilerin bu önkoşulları kendi kendilerine el ile yüklemesini gerektirmek yerine, şablon VM görüntüsünün farklı sürümleri paylaşılan bir görüntü galerisine aktarılabilir. Daha sonra, yeni Labs oluştururken bu görüntü sürümlerini kullanabilirsiniz.

- **Laboratuvar genelinde şablon VM görüntülerini paylaşabilir ve yeniden kullanabilirsiniz**

    Her yeni laboratuvar oluşturduğunuzda sıfırdan yapılandırma yapmanız gerekmiyorsa bir görüntüyü kaydedebilir ve yeniden kullanabilirsiniz. Örneğin, birden çok sınıfın aynı görüntüyü kullanması gerekiyorsa, bunu bir kez oluşturup paylaşılan görüntü galerisine dışarı aktarabilirsiniz; böylece Labs arasında paylaşılabilir.

- **Görüntü kullanılabilirliği otomatik çoğaltma aracılığıyla başarılı oldu**

    Bir laboratuvardan paylaşılan görüntü galerisine bir görüntü kaydettiğinizde, otomatik olarak [aynı coğrafya içindeki diğer bölgelere](https://azure.microsoft.com/global-infrastructure/regions/)çoğaltılır. Bir bölge için kesinti varsa, başka bir bölgeden bir görüntü çoğaltması kullanılabilir olduğundan, görüntüyü laboratuvarınızda yayımlıyoruz.  VM 'Lerin birden çok çoğaltmalardan yayımlanması performansa da yardımcı olabilir.

Paylaşılan görüntüleri mantıksal olarak gruplamak için aşağıdakilerden birini yapabilirsiniz:

- Birden çok paylaşılan görüntü galerisi oluşturun. Her laboratuvar hesabı yalnızca bir paylaşılan görüntü galerisine bağlanabilir, bu nedenle bu seçenek ayrıca birden çok laboratuvar hesabı oluşturmanızı gerektirir.
- Birden çok laboratuvar hesabı tarafından paylaşılan tek bir paylaşılan görüntü Galerisi kullanın. Bu durumda, her laboratuvar hesabı yalnızca söz konusu hesaptaki laboratuvarlara uygulanabilen görüntüleri etkinleştirebilir.

## <a name="naming"></a>Adlandırma

Azure Lab Services kullanmaya başlarken, kaynak grupları, laboratuvar hesapları, laboratuvarlar ve paylaşılan görüntü Galerisi için adlandırma kuralları oluşturmanızı öneririz. Oluşturduğunuz adlandırma kuralları, kuruluşunuzun ihtiyaçlarına göre benzersiz olacaktır, ancak aşağıdaki tabloda genel yönergeler sunulmaktadır:

| Kaynak türü | Rol | Önerilen düzen | Örnekler |
| ------------- | ---- | ----------------- | -------- | 
| Kaynak grubu | Bir veya daha fazla laboratuvar hesabı ve bir veya daha fazla paylaşılan görüntü Galerisi içerir | \<organization short name\>-\<environment\>-RG<ul><li>**Kuruluş kısa adı** , kaynak grubunun desteklediği kuruluşun adını tanımlar.</li><li>**Ortam** , kaynak için *pilot* veya *Üretim* gibi ortamı tanımlar.</li><li>**RG** , kaynak türü *kaynak grubu* için temsil eder.</li></ul> | contosoüniversıtylabs-RG<br/>contosoüniversıtylabs-pilot-RG<br/>contosoüniversıtylabs-prod-RG |
| Laboratuvar hesabı | Bir veya daha fazla Laboratuvarı içerir | \<organization short name\>-\<environment\>-La<ul><li>**Kuruluş kısa adı** , kaynak grubunun desteklediği kuruluşun adını tanımlar.</li><li>**Ortam** , kaynak için *pilot* veya *Üretim* gibi ortamı tanımlar.</li><li>**La** , kaynak türü *Laboratuvar hesabı* için temsil eder.</li></ul> | contosoüniversıtylabs-La<br/>mathdeptlabs-La<br/>bilimsel deptlabs-pilot-La<br/>bilimsel deptlabs-prod-La |
| Laboratuvar | Bir veya daha fazla sanal makine içeriyor |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Sınıf adı** , laboratuvarın desteklediği sınıfın adını tanımlar.</li><li>**Zaman çerçevesi** , sınıfın sunulduğu zaman dilimini tanımlar.</li>**Eğitimci tanımlayıcısı** , laboratuvarın sahibi olan eğitimci 'yi tanımlar.</li></ul> | CS1234-fall2019-johntikan<br/>CS1234-spring2019-johntikan |
| Paylaşılan görüntü Galerisi | Bir veya daha fazla VM görüntüsü sürümü içeriyor | \<organization short name\>'ndeki | contosoüniversıtylabsgallery |

Diğer Azure kaynaklarını adlandırma hakkında daha fazla bilgi için bkz. [Azure kaynakları Için adlandırma kuralları](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regions\locations

Azure Lab Services kaynaklarınızı ayarlarken, kaynakları barındıracak veri merkezinin bir bölgesini veya konumunu sağlamanız gerekir. Sonraki bölümlerde, bir bölgenin veya konumun bir laboratuvarı ayarlamayla ilgili her kaynağı nasıl etkilediği açıklanır.

### <a name="resource-group"></a>Kaynak grubu

Bölge, bir kaynak grubuyla ilgili bilgilerin depolandığı veri merkezini belirtir. Kaynak grubu içinde yer alan Azure kaynakları, üst öğesinden farklı bir bölgede olabilir.

### <a name="lab-account"></a>Laboratuvar hesabı

Bir laboratuvar hesabının konumu, içinde bir kaynağın bulunduğu bölgeyi gösterir.  

### <a name="lab"></a>Laboratuvar

Bir laboratuvarın bulunduğu konum aşağıdaki faktörlere bağlı olarak farklılık gösterir:

  - **Laboratuvar hesabı bir sanal ağla eşlenmez**
  
    [Bir laboratuvar hesabını aynı bölgede olduklarında sanal bir ağ ile eşleyebilir](./how-to-connect-peer-virtual-network.md) .  Laboratuvar hesabı bir sanal ağla eşlenirse, laboratuvarlar hem laboratuvar hesabı hem de sanal ağ ile aynı bölgede otomatik olarak oluşturulur.

    > [!NOTE]
    > Bir laboratuvar hesabı bir sanal ağla eşlenirse, laboratuvar **oluşturucusunun laboratuvar konumunu seçmesine Izin ver** ayarı devre dışı bırakılır. Daha fazla bilgi için bkz. laboratuvar [oluşturucusunun laboratuvar için konum seçmesine Izin verme](./allow-lab-creator-pick-lab-location.md).
    
  - **Sanal ağ eşlenmiyor *ve* laboratuvar oluşturucularının laboratuvar konumunu seçmesini izin verilmiyor**
  
    Laboratuvar hesabıyla bir sanal ağ *eşlenmeden* ve [Laboratuvar oluşturucularının laboratuvar konumunu seçmesini *izin verilmediği*](./allow-lab-creator-pick-lab-location.md)durumlarda, laboratuvarlar, kullanılabilir VM kapasitesi olan bir bölgede otomatik olarak oluşturulur.  Özellikle Azure Lab Services, [Laboratuvar hesabıyla aynı coğrafya içinde olan bölgelerde](https://azure.microsoft.com/global-infrastructure/regions)kullanılabilirliği arar.

  - **Sanal ağ eşlenmez *ve* laboratuvar oluşturucuları laboratuvar konumunu seçmelerine izin verilir**
       
    Sanal *ağ eşlenmez ve* [Laboratuvar oluşturucuları laboratuvar konumunu seçmelerine *Izin verildiğinde*](./allow-lab-creator-pick-lab-location.md), laboratuvar Oluşturucu tarafından seçilebilecek konumlar kullanılabilir kapasiteye bağlıdır.

> [!NOTE]
> Bir bölgede yeterli VM kapasitesi olduğundan emin olmak için, Laboratuvarı oluştururken laboratuvar hesabı aracılığıyla kapasiteyi istemek önemlidir.

Genel bir kural, bir kaynağın bölgesini kullanıcılarına en yakın olan bir kaynak olarak ayarlamak. Laboratuvarlar için bu, öğrencilerinize en yakın olan Laboratuvarı oluşturma anlamına gelir. Öğrenciler dünyanın her yerinden bulunan çevrimiçi kurslar için, merkezi olarak bulunan bir laboratuvar oluşturmak üzere en iyi kararlarınızı kullanın. Ya da bir sınıfı, öğrenciler bölgelerine göre birden çok laboratuvarya bölebilirsiniz.

## <a name="vm-sizing"></a>VM boyutlandırma

Yöneticiler veya laboratuvar oluşturucuları bir laboratuvar oluşturduklarında, derslik gereksinimlerine bağlı olarak çeşitli VM boyutları arasından seçim yapabilir. İşlem boyutu kullanılabilirliğinin, laboratuvar hesabınızın bulunduğu bölgeye bağlı olduğunu unutmayın.

| Boyut | Özellikler | Seriler | Önerilen kullanım |
| ---- | ----- | ------ | ------------- |
| Küçük| <ul><li>2 &nbsp; çekirdek</li><li>3,5 gigabayt (GB) RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Komut satırı, Web tarayıcısı, düşük trafikli web sunucuları, küçük ve orta ölçekli veritabanları için idealdir. |
| Orta | <ul><li>4 &nbsp; çekirdek</li><li>7 &nbsp; GB &nbsp; RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | İlişkisel veritabanları, bellek içi önbelleğe alma ve analiz için idealdir. |
| Orta (iç içe sanallaştırma) | <ul><li>4 &nbsp; çekirdek</li><li>16 &nbsp; GB &nbsp; RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | İlişkisel veritabanları, bellek içi önbelleğe alma ve analiz için idealdir.
| Büyük | <ul><li>8 &nbsp; çekirdek</li><li>16 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | Daha hızlı CPU 'Lar, daha iyi yerel disk performansı, büyük veritabanları, büyük bellek önbellekler gerektiren uygulamalar için idealdir.  Bu boyut, iç içe sanallaştırmayı da destekler. |
| Büyük (iç içe sanallaştırma) | <ul><li>8 &nbsp; çekirdek</li><li>32 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Daha hızlı CPU 'Lar, daha iyi yerel disk performansı, büyük veritabanları, büyük bellek önbellekler gerektiren uygulamalar için idealdir. |
| Küçük GPU (görselleştirme) | <ul><li>6 &nbsp; çekirdek</li><li>56 &nbsp; GB &nbsp; RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | OpenGL ve DirectX gibi çerçeveleri kullanarak uzaktan görselleştirme, akış, oyun ve kodlama için idealdir. |
| Küçük GPU (Işlem) | <ul><li>6 &nbsp; çekirdek</li><li>56 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |AI ve derin öğrenme gibi bilgisayar açısından yoğun uygulamalar için idealdir. |
| Orta ölçekli GPU (görselleştirme) | <ul><li>12 &nbsp; çekirdek</li><li>112 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | OpenGL ve DirectX gibi çerçeveleri kullanarak uzaktan görselleştirme, akış, oyun ve kodlama için idealdir. |

## <a name="manage-identity"></a>Kimliği Yönet

Laboratuvar hesaplarına ve laboratuvarlarına erişim için [Azure rol tabanlı erişim denetimi 'ni (RBAC)](../role-based-access-control/overview.md) kullanarak aşağıdaki rolleri atayabilirsiniz:

- Laboratuvar hesabı **sahibi**

    Laboratuvar hesabı oluşturan bir yönetici, laboratuvar hesabı sahibi rolüne otomatik olarak atanır. Sahip rolü şunları yapabilir:
     - Laboratuvar hesabı ayarlarını değiştirin.
     - Diğer yöneticilere laboratuvar hesabına sahip veya katılımcı olarak erişim izni verin.
     - Bir Oluşturucu, sahip veya katkıda bulunan olarak laboratuvarlara eğitimciler erişimi verin.
     - Laboratuvar hesabındaki tüm laboratuvarları oluşturun ve yönetin.

- Laboratuvar hesabı **katılımcısı**

    Katkıda bulunan rolünü atayan bir yönetici şunları yapabilir:
    - Laboratuvar hesabı ayarlarını değiştirin.
    - Laboratuvar hesabındaki tüm laboratuvarları oluşturun ve yönetin.

    Ancak *katkıda bulunan* , diğer kullanıcılara laboratuvar hesaplarına veya laboratuvarlara erişim izni veremez.

- **Laboratuvar Oluşturucu**

    Laboratuvar hesabı içinde Labs oluşturmak için, bir eğitimci, laboratuvar Oluşturucu rolünün bir üyesi olmalıdır.  Laboratuvar oluşturan bir eğitimci, otomatik olarak laboratuvar sahibi olarak eklenir. Daha fazla bilgi için bkz. [Laboratuvar Oluşturucu rolüne kullanıcı ekleme](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role). 

- Laboratuvar **sahibi** veya **katkıda bulunan**
  
    Laboratuvar sahibi veya katkıda bulunan rolünde bir eğitimci, bir laboratuvarın ayarlarını görüntüleyebilir ve değiştirebilir. Bu kişi Ayrıca laboratuvar hesabı okuyucu rolünün bir üyesi olmalıdır.

    Laboratuvar sahibi ve katkıda bulunan rolleri arasındaki önemli bir fark, yalnızca bir sahibin bir laboratuvarı yönetmek için diğer kullanıcılara erişim izni verebilir. *Katkıda bulunan* , başka kullanıcılara bir laboratuvarı yönetmek için erişim izni veremez.

- **Paylaşılan görüntü Galerisi**

    Bir laboratuvar hesabına paylaşılan bir görüntü Galerisi eklediğinizde, laboratuvar hesabı sahipleri ve katkıda bulunanlar ve laboratuvar oluşturucuları, laboratuvar sahipleri ve laboratuvar katkı sağlayanlar, galerideki görüntüleri görüntülemek ve kaydetmek için otomatik olarak erişim izni verilir.

Rolleri atarken, bu ipuçlarını izlemeye yardımcı olur:

   - Normalde, yalnızca Yöneticiler laboratuvar hesabı sahibinin veya katkıda bulunan rolünün üyesi olmalıdır. Laboratuvar hesabında birden fazla sahip veya katkıda bulunan olabilir.
   - Eğitimciler yeni Labs oluşturma ve oluşturdukları laboratuvarları yönetme olanağı vermek için, yalnızca laboratuvar Oluşturucu rolünü atamanız gerekir.
   - Eğitimciler belirli laboratuvarları yönetme özelliği vermek *, ancak yeni* laboratuvarlar oluşturma yeteneği sağlamak için, yönetecekleri her laboratuvar için sahip veya katkıda bulunan rolü atayın. Örneğin, bir Mesleme ve eğitim Yardımcısı ile bir laboratuvarın ortak olmasını sağlamak isteyebilirsiniz. Daha fazla bilgi için bkz. [bir laboratuvara sahipler ekleme](./how-to-add-user-lab-owner.md).

## <a name="pricing"></a>Fiyatlandırma

### <a name="azure-lab-services"></a>Azure Lab Services

Fiyatlandırma hakkında bilgi edinmek için bkz. [Azure Lab Services fiyatlandırması](https://azure.microsoft.com/pricing/details/lab-services/).


### <a name="shared-image-gallery"></a>Paylaşılan Görüntü Galerisi

Görüntü sürümlerini depolamak ve yönetmek için paylaşılan görüntü galerileri kullanmayı planlıyorsanız, paylaşılan görüntü Galerisi hizmetinin fiyatlandırmasını de göz önünde bulundurmanız gerekir. 

Paylaşılan bir görüntü galerisi oluşturup laboratuvar hesabınıza ekleme ücretsizdir. Bir görüntü sürümü galeriye kaydedilinceye kadar hiçbir ücret alınmaz. Paylaşılan görüntü Galerisi kullanmaya yönelik fiyatlandırma, genellikle oldukça göz ardı edilebilir, ancak Azure Lab Services fiyatlandırmasına dahil olmadığından nasıl hesaplanacağını anlamak önemlidir.  

#### <a name="storage-charges"></a>Depolama ücretleri

Görüntü sürümlerini depolamak için, paylaşılan bir görüntü Galerisi standart sabit disk sürücüsü (HDD) tarafından yönetilen diskleri kullanır. Kullanılan HDD tarafından yönetilen diskin boyutu, depolanan görüntü sürümünün boyutuna bağlıdır. Fiyatlandırma hakkında bilgi edinmek için bkz. [yönetilen diskler fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Çoğaltma ve ağ çıkış ücretleri

Bir laboratuvar şablonu VM kullanarak bir görüntü sürümünü kaydettiğinizde Azure Lab Services önce onu bir kaynak bölgede depolar ve ardından kaynak görüntü sürümünü otomatik olarak bir veya daha fazla hedef bölgeye çoğaltır. 

Azure Lab Services, kaynak görüntü sürümünü laboratuvarın bulunduğu [coğrafya içindeki tüm hedef bölgelere](https://azure.microsoft.com/global-infrastructure/regions/) otomatik olarak çoğalttığını unutmayın. Örneğin, laboratuvarınız ABD coğrafi Coğrafya içindeyse, bir görüntü sürümü ABD içinde bulunan sekiz bölgenin her birine çoğaltılır.

Bir görüntü sürümü kaynak bölgeden ek hedef bölgelere çoğaltıldığında bir ağ çıkış ücreti oluşur. Ücretlendirilen miktar, görüntünün verileri ilk başta kaynak bölgeden dışarıya aktarıldığında görüntü sürümünün boyutunu temel alır.  Fiyatlandırma ayrıntıları için bkz. [bant genişliği fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/bandwidth/).

[Eğitim çözümleri](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) müşterileri için çıkış ücretleri alınmaz olabilir. Daha fazla bilgi edinmek için hesap yöneticinize başvurun. 

Daha fazla bilgi için bkz. "akademik müşteriler için hangi veri aktarımı programları var ve nasıl uygun hale yaparım?" [eğitim kurumları Için programlar](https://azure.microsoft.com/pricing/details/bandwidth/) sayfasının SSS bölümünde.

#### <a name="pricing-example"></a>Fiyatlandırma örneği

Bir şablon VM görüntüsünü paylaşılan bir görüntü galerisine kaydetme maliyetine bir örnek bakalım. Aşağıdaki senaryolar varsayılmaktadır:

- Bir özel VM Görüntünüz vardır.
- Görüntünün iki sürümünü kaydediyorsunuz.
- Laboratuvarınız ABD 'de ve toplam sekiz bölgeye sahiptir.
- Her görüntü sürümü 32 GB boyutdir; Sonuç olarak, HDD ile yönetilen disk fiyatı ayda $1,54 ' dir.

Aylık toplam maliyet tahmini şöyle olur:

* *Görüntü sayısı &times; sürüm sayısı &times; çoğaltma &times; yönetilen disk fiyatı = aylık toplam maliyet*

Bu örnekte, maliyet Şu şekilde olur:

* 1 özel görüntü (32 GB) &times; 2 sürüm &times; 8 abd bölge &times; $1,54 = $24,64/ay

> [!NOTE]
> Önceki hesaplama yalnızca örnek amaçlıdır. Paylaşılan görüntü Galerisi kullanılarak ilişkili depolama *maliyetlerini kapsar ve çıkış maliyetlerini içermez.* Depolama için gerçek fiyatlandırma için bkz. [yönetilen diskler fiyatlandırması](https://azure.microsoft.com/en-us/pricing/details/managed-disks/).

#### <a name="cost-management"></a>Maliyet yönetimi

Laboratuvar hesabı yöneticilerinin, gereksiz görüntü sürümlerini Galeriden düzenli olarak silerek maliyetleri yönetmesi önemlidir. 

Bu seçenek paylaşılan görüntü galerisinde mevcut olsa da, maliyetleri azaltmanın bir yolu olarak belirli bölgelere yönelik çoğaltmayı silmeyin. Çoğaltma değişiklikleri, paylaşılan görüntü galerisinde kaydedilmiş görüntülerden VM yayımlama Azure Lab Services imkanına olumsuz etkileri olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Labs 'i ayarlama ve yönetme hakkında daha fazla bilgi için bkz.:

- [Laboratuvar hesabı kurulum kılavuzu](account-setup-guide.md)  
- [Laboratuvar Kurulum Kılavuzu](setup-guide.md)  
- [Laboratuvarlar için maliyet yönetimi](cost-management-guide.md)  
- [Ekiplerde Azure Lab Services kullanma](lab-services-within-teams-overview.md)
