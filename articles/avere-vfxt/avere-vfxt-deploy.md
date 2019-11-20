---
title: Azure için avere vFXT dağıtma
description: Azure 'da avere vFXT kümesini dağıtma adımları
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: rohogue
ms.openlocfilehash: 6ddf950bf2d138a94675ee394109a0d227ea206b
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255460"
---
# <a name="deploy-the-vfxt-cluster"></a>VFXT kümesini dağıtma

Bu yordam, Azure Marketi 'nden sunulan Dağıtım Sihirbazı 'nı kullanarak size kılavuzluk eder. Sihirbaz Azure Resource Manager şablonu kullanarak kümeyi otomatik olarak dağıtır. Forma parametreleri girdikten ve **Oluştur**' a tıkladıktan sonra, Azure bu adımları otomatik olarak tamamlar:

* Kümeyi dağıtmak ve yönetmek için gereken yazılımları içeren temel bir VM olan küme denetleyicisini oluşturur.
* Yeni öğe oluşturma da dahil olmak üzere kaynak grubu ve sanal ağ altyapısını ayarlar.
* Küme düğümü sanal makinelerini oluşturur ve avere kümesi olarak yapılandırır.
* İsteniyorsa, yeni bir Azure Blob kapsayıcısı oluşturur ve bunu bir küme çekirdeği fili olarak yapılandırır.

Bu belgedeki yönergeleri uyguladıktan sonra, aşağıdaki diyagramda gösterildiği gibi bir sanal ağ, bir alt ağ, denetleyici ve bir vFXT kümesi olacaktır. Bu diyagramda yeni bir BLOB depolama kapsayıcısı (yeni bir depolama hesabında, gösterilmez) ve alt ağ içinde Microsoft Storage için bir hizmet uç noktası içeren isteğe bağlı Azure Blob çekirdek filleyicisi gösterilmektedir. 

![avere küme bileşenleriyle üç eşmerkezli dikdörtgeni gösteren diyagram. Dış dikdörtgen ' Resource Group ' olarak etiketlidir ve bir altıon etiketli ' Blob depolama alanı (isteğe bağlı) ' içeriyor. ' Deki sonraki dikdörtgen ' sanal ağ: 10.0.0.0/16 ' etiketlidir ve hiçbir benzersiz bileşen içermez. En içteki dikdörtgen ' subnet: 10.0.0.0/24 ' olarak etiketlidir ve ' küme denetleyicisi ' etiketli bir VM, ' vFXT düğümleri (vFXT kümesi) ' etiketli üç VM 'nin bir yığını ve ' hizmet uç noktası ' etiketli bir altıon ' ı içerir. Hizmet uç noktasını (alt ağ içinde olan) ve BLOB depolama alanını (kaynak grubunda alt ağ ve VNET dışında) bağlayan bir ok vardır. Ok, alt ağ ve sanal ağ sınırları üzerinden geçer.](media/avere-vfxt-deployment.png)  

Oluşturma şablonunu kullanmadan önce, şu önkoşullara değindiğinizden emin olun:  

1. [Yeni abonelik](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Abonelik sahibi izinleri](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [VFXT kümesi için kota](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Depolama hizmeti uç noktası (gerekirse)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) -var olan bir sanal ağ kullanılarak dağıtımlar için gereklidir ve BLOB depolama alanı oluşturuluyor

Küme dağıtım adımları ve planlaması hakkında daha fazla bilgi için [avere vFXT sisteminizin](avere-vfxt-deploy-plan.md) ve [dağıtıma genel bakış](avere-vfxt-deploy-overview.md)konusunu okuyun.

## <a name="create-the-avere-vfxt-for-azure"></a>Azure için avere vFXT 'yi oluşturma

Avere araması yaparak Azure portal oluşturma şablonuna erişin ve "Azure ARM şablonu için avere vFXT" seçeneğini seçin. 

![Crumbs "Yeni > Market > her şeyi Azure portal gösteren tarayıcı penceresi. Her şey sayfasında, arama alanı "avere" terimini ve ikinci sonuç olarak "avere vFXT for Azure ARM şablonu", vurgulamak için kırmızı renkle özetlenmektedir.](media/avere-vfxt-template-choose.png)

Azure ARM şablonu için avere vFXT ' deki ayrıntıları okuduktan sonra başlamak için **Oluştur** ' a tıklayın. 

![Dağıtım şablonunun ilk sayfasıyla Azure Marketi](media/avere-vfxt-deploy-first.png)

Şablon dört adımdan ayrılır-iki bilgi toplama sayfası, ayrıca doğrulama ve onay adımları. 

* Tek sayfa, küme denetleyicisi sanal makinesinin ayarlarına odaklanır. 
* İkinci sayfa, küme oluşturmak için parametreleri ve alt ağlar ve depolama gibi ilişkili kaynakları toplar. 
* Sayfa üç, ayarları özetler ve yapılandırmayı doğrular. 
* Sayfa dört, yazılım hüküm ve koşullarını açıklar ve küme oluşturma işlemini başlatabilmeniz için izin verir. 

## <a name="page-one-parameters---cluster-controller-information"></a>Sayfa bir parametre-küme denetleyicisi bilgileri

Dağıtım şablonunun ilk sayfası, küme denetleyicisi hakkında bilgi toplar. 

![Dağıtım şablonunun ilk sayfası](media/avere-vfxt-deploy-1.png)

Aşağıdaki bilgileri girin:

* **Küme denetleyicisi adı** -küme denetleyicisi sanal makinesinin adını ayarlayın.

* **Denetleyici Kullanıcı adı** -küme denetleyicisi sanal makinesi için kök Kullanıcı adı ' nı girin. 

* **Kimlik doğrulama türü** -denetleyiciye bağlanmak için parola veya SSH ortak anahtar kimlik doğrulaması ' nı seçin. SSH ortak anahtar yöntemi önerilir; yardıma ihtiyacınız varsa [SSH anahtarlarını oluşturma ve kullanma hakkında](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) bilgi edinin.

* **Parola** veya **SSH ortak anahtarı** -seçtiğiniz kimlik doğrulama türüne bağlı olarak, bir RSA ortak anahtarı veya bir parolayı bir sonraki alanlarda belirtmeniz gerekir. Bu kimlik bilgisi, daha önce sağlanmış olan kullanıcı adıyla birlikte kullanılır.

* **Abonelik** -avere vFXT için aboneliği seçin. 

* **Kaynak grubu** -avere vFXT kümesi için mevcut bir boş kaynak grubu seçin veya "Yeni oluştur" a tıklayın ve yeni bir kaynak grubu adı girin. 

* **Konum** -kümeniz ve kaynaklarınız için Azure konumunu seçin.

Bittiğinde **Tamam** ' a tıklayın. 

> [!NOTE]
> Küme denetleyicisinin herkese açık bir IP adresi olmasını istiyorsanız, var olan bir ağ seçmek yerine küme için yeni bir sanal ağ oluşturun. Bu ayar iki sayfada bulunur.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Sayfa iki parametre-vFXT küme bilgileri

Dağıtım şablonunun ikinci sayfası, küme boyutunu, düğüm türünü, önbellek boyutunu ve depolama parametrelerini diğer ayarlar arasında ayarlamanıza olanak sağlar. 

![Dağıtım şablonunun ikinci sayfası](media/avere-vfxt-deploy-2.png)

* **Avere vFXT küme düğümü sayısı** -kümede kullanılacak düğüm sayısını seçin. En az üç düğüm ve en yüksek değer on iki. 

* **Küme yönetimi parolası** -küme yönetimi için parola oluşturun. Bu parola, kümeyi izlemek ve ayarları yapılandırmak üzere küme Denetim Masası 'nda oturum açmak için Kullanıcı adı ```admin``` ile birlikte kullanılacaktır.

* **Avere vFXT küme adı** -kümeye benzersiz bir ad verin. 

* **Boyut** -Bu bölüm, küme düğümleri IÇIN kullanılacak VM türünü gösterir. Yalnızca önerilen bir seçenek olsa da, **değişiklik boyutu** bağlantısı bu örnek türüyle ilgili ayrıntıları ve Fiyatlandırma Hesaplayıcı bağlantısını içeren bir tablo açar.  

* **Düğüm başına önbellek boyutu** -küme önbelleği küme düğümlerine yayılır, bu nedenle avere vFXT kümenizdeki toplam önbellek boyutu düğüm sayısıyla çarpılan düğüm başına önbellek boyutu olur. 

  Önerilen yapılandırma, Standard_E32s_v3 düğümleri için düğüm başına 4 TB kullanmaktır.

* **Sanal ağ** -kümeyi barındırmak için yeni bir VNET tanımlayın veya [avere Vfxt sisteminizi planlayın](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure)bölümünde açıklanan önkoşulları karşılayan mevcut bir sanal ağı seçin. 

  > [!NOTE]
  > Yeni bir sanal ağ oluşturursanız, yeni özel ağa erişebilmek için küme denetleyicisinin ortak bir IP adresi olur. Mevcut bir sanal ağı seçerseniz, küme denetleyicisi genel bir IP adresi olmadan yapılandırılır. 
  > 
  > Küme denetleyicisindeki genel olarak görünen bir IP adresi, vFXT kümesine daha kolay erişim sağlar, ancak küçük bir güvenlik riski oluşturur. 
  >  * Küme denetleyicisindeki genel bir IP adresi, özel alt ağın dışından avere vFXT kümesine bağlanmak için bir geçiş ana bilgisayarı olarak kullanmanıza olanak tanır.
  >  * Denetleyicide genel bir IP adresi ayarlanmamışsa, kümeye erişmek için başka bir geçiş ana bilgisayarı, bir VPN bağlantısı veya ExpressRoute kullanmanız gerekir. Örneğin, denetleyiciyi, zaten yapılandırılmış bir VPN bağlantısı olan bir sanal ağ içinde oluşturun.
  >  * Genel IP adresi olan bir denetleyici oluşturursanız, denetleyici VM 'sini bir ağ güvenlik grubuyla korumanız gerekir. Varsayılan olarak, Azure dağıtımı için avere vFXT bir ağ güvenlik grubu oluşturur ve genel IP adreslerine sahip denetleyiciler için yalnızca bağlantı noktası 22 ' ye gelen erişimi kısıtlar. IP kaynak adreslerinize erişimi kilitleyerek, diğer bir deyişle, yalnızca küme erişimi için kullanmayı düşündüğünüz makinelerden gelen bağlantılara izin vererek sistemi daha da koruyabilirsiniz.

  Dağıtım şablonu Ayrıca yeni VNET 'i Azure Blob depolama için bir depolama hizmeti uç noktası ile ve ağ erişim denetimi 'nin yalnızca küme alt ağındaki IP 'lere kilitlediği şekilde yapılandırır. 

* **Alt ağ** -var olan sanal ağınızdan bir alt ağ seçin veya yeni bir tane oluşturun. 

* **BLOB depolama oluşturma ve kullanma** -yeni bir Azure Blob kapsayıcısı oluşturmak ve bunu yeni avere vFXT kümesi için arka uç depolama olarak yapılandırmak için **true** seçeneğini belirleyin. Bu seçenek ayrıca küme ile aynı kaynak grubunda ve küme alt ağı içindeki bir Microsoft Storage hizmeti uç noktası içinde yeni bir depolama hesabı oluşturur. 
  
  Var olan bir sanal ağı sağlarsanız, kümeyi oluşturmadan önce bir depolama hizmeti uç noktası olması gerekir. (Daha fazla bilgi için [avere vFXT sisteminizin planını](avere-vfxt-deploy-plan.md)okuyun.)

  Yeni bir kapsayıcı oluşturmak istemiyorsanız bu alanı **false** olarak ayarlayın. Bu durumda, kümeyi oluşturduktan sonra depolamayı iliştirmeli ve yapılandırmalısınız. Yönergeler için [depolamayı yapılandırma](avere-vfxt-add-storage.md) makalesini okuyun. 

* **(Yeni) depolama hesabı** -yeni bir Azure Blob kapsayıcısı oluşturuyorsanız, yeni depolama hesabı için bir ad girin. 

## <a name="validation-and-purchase"></a>Doğrulama ve satın alma

Üç sayfa yapılandırmayı özetler ve parametreleri doğrular. Doğrulama başarılı olduktan sonra devam etmek için **Tamam** düğmesine tıklayın. 

![Dağıtım şablonunun üçüncü sayfası-doğrulama](media/avere-vfxt-deploy-3.png)

4\. sayfada, gerekli iletişim bilgilerini girin ve **Oluştur** düğmesine tıklayarak koşulları kabul edin ve Azure kümesi Için avere vFXT 'yi oluşturun. 

![Dağıtım şablonunun dördüncü sayfası-hüküm ve koşullar, Oluştur düğmesi](media/avere-vfxt-deploy-4.png)

Küme dağıtımı 15-20 dakika sürer.

## <a name="gather-template-output"></a>Şablon çıktısını topla

Avere vFXT şablonu kümeyi oluşturmayı bitirdiğinde, yeni küme hakkında bazı önemli bilgiler verir. 

> [!TIP]
> Şablon çıktısından yönetim IP adresini kopyalamadığınızdan emin olun. Kümeyi yönetmek için bu adrese ihtiyacınız vardır.

Bu bilgileri bulmak için şu yordamı izleyin:

1. Küme denetleyicinizin kaynak grubuna gidin.

1. Sol tarafta **dağıtımlar**' a ve ardından **Microsoft-avere. vfxt-Template**' e tıklayın.

   ![Solda ve Microsoft-avere. vfxt-Template seçili dağıtımlar içeren kaynak grubu portalı sayfası dağıtım adı altındaki bir tabloda gösteriliyor](media/avere-vfxt-outputs-deployments.png)

1. Sol tarafta, **çıktılar**' e tıklayın. Her alandaki değerleri kopyalayın. 

   ![etiketlerin sağ tarafındaki alanlarda SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs ve MGMT_IP değerlerini gösteren sayfa çıkışları](media/avere-vfxt-outputs-values.png)

## <a name="next-step"></a>Sonraki adım

Artık küme çalışıyor olduğuna ve yönetim IP adresini bildiğinize göre, desteği etkinleştirmek, gerekirse depolama eklemek ve diğer küme ayarlarını özelleştirmek için [küme yapılandırma aracına bağlanabilirsiniz](avere-vfxt-cluster-gui.md) .
