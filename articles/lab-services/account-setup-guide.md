---
title: Azure Lab Services için hızlandırılmış laboratuvar hesabı kurulum kılavuzu
description: Bu kılavuz, yöneticilerin okuldaki kullanım için hızlı bir şekilde laboratuvar hesabı ayarlamış yardımcı olur.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669398"
---
# <a name="lab-account-setup-guide"></a>Laboratuvar hesabı kurulum kılavuzu
Bir yöneticiyseniz, Azure Lab Services ortamınızı ayarlamadan önce, önce Azure aboneliğinizde bir *Laboratuvar hesabı* oluşturmanız gerekir. Laboratuvar hesabı bir veya daha fazla laboratuvarın bir kapsayıcısıdır ve yalnızca birkaç dakika sürer.

Bu kılavuz üç bölümden oluşur:
-  Önkoşullar
-  Laboratuvar hesabı ayarlarınızı planlayın
-  Laboratuvar hesabınızı ayarlama

## <a name="prerequisites"></a>Önkoşullar
Aşağıdaki bölümlerde, bir laboratuvar hesabı ayarlamadan önce yapmanız gerekenler ana hatlarıyla verilmiştir.


### <a name="access-your-azure-subscription"></a>Azure aboneliğinize erişin
Laboratuvar hesabı oluşturmak için okulunuz için zaten ayarlanmış olan bir Azure aboneliğine erişmeniz gerekir. Okulunuzda bir veya daha fazla abonelik olabilir. Laboratuvar hesapları da dahil olmak üzere tüm Azure kaynaklarınız ve hizmetleriniz için faturalandırma ve güvenliği yönetmek üzere bir abonelik kullanırsınız.  Azure abonelikleri genellikle BT departmanınız tarafından yönetilir.  Daha fazla bilgi için, [Azure Lab Services Yönetici kılavuzunun](./administrator-guide.md#subscription)"abonelik" bölümüne bakın.

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Kaç VM ve VM boyutuna ihtiyacınız olduğunu tahmin edin
Okul laboratuvarınızın kaç [sanal makine (VM) ve VM boyutu](./administrator-guide.md#vm-sizing) gerektirdiğini öğrenmek önemlidir. 

Laboratuvarlarınızı ve görüntülerinizi yapılandırma hakkında yönergeler için bkz. [bir fiziksel laboratuvardan Azure Lab Services için](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)blog gönderisi.

Laboratuvarların nasıl ayarlanacağı hakkında ek yönergeler için [Azure Lab Services Yönetici kılavuzunun](./administrator-guide.md#lab)"Laboratuvar" bölümüne bakın.

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Abonelik VM sınırlarını ve bölgesel VM kapasitesini anlayın
Laboratuvarlarınızın sanal makine sayısını ve VM boyutlarını tahmin ettikten sonra şunları yapmanız gerekir:

- Azure aboneliğinizin kapasite sınırının, laboratuvarlarda kullanmayı planladığınız sanal makine sayısı ve VM boyutu için izin verdiğinden emin olun.
- Laboratuvar hesabınızı, yeterli kullanılabilir VM kapasitesi olan bir bölge içinde oluşturun.

Daha fazla bilgi için bkz. [VM abonelik limitleri ve bölgesel kapasite](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Kaç tane laboratuvar hesabı oluşturulacağını belirleyin

Hızlıca kullanmaya başlamak için kendi kaynak grubu içinde tek bir laboratuar hesabı oluşturun.  Daha sonra, gerektiğinde ek laboratuvar hesapları ve kaynak grupları oluşturabilirsiniz. Örneğin, en sonunda, maliyetleri açık bir şekilde ayırmak için bir laboratuvar hesabına ve bir departman başına kaynak grubuna sahip olabilirsiniz. 

Laboratuvar hesapları, kaynak grupları ve maliyet ayrımı hakkında daha fazla bilgi için bkz.:
- [Azure Lab Services Yönetici kılavuzunun](./administrator-guide.md#resource-group) "kaynak grubu" bölümü
- [Azure Lab Services Yönetici kılavuzunun](./administrator-guide.md#lab-account) "Laboratuvar hesabı" bölümü 
- [Azure Lab Services için maliyet yönetimi](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>Laboratuvar hesabı ayarlarınızı planlayın

Laboratuvar hesabı ayarlarınızı planlamak için aşağıdaki soruları göz önünde bulundurun.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>Laboratuvar hesabının sahibi ve katkıda bulunanlar kim olmalıdır?

Okulunuzun BT yöneticileri, bir laboratuar hesabı için sahip ve katkıda bulunan rolleri genellikle alır. Bu roller, laboratuvar hesabındaki tüm laboratuvarlara uygulanan ilkeleri yönetmekten sorumludur. Laboratuvar hesabını oluşturan kişi otomatik olarak sahip olur. Aboneliğinizle ilişkili olan Azure Active Directory (Azure AD) kiracısından ek sahipler ve katkıda bulunanlar ekleyebilirsiniz. 

Laboratuvar hesabı sahibi ve katkıda bulunan rolleri hakkında daha fazla bilgi için, [Azure Lab Services Yönetici kılavuzunun](./administrator-guide.md#manage-identity)"kimliği Yönet" bölümüne bakın.

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Laboratuvar kullanıcıları yalnızca Azure Lab Services içindeki Azure AD kiracılarında erişimi olan VM 'lerin tek bir listesini görür.

### <a name="who-will-be-allowed-to-create-labs"></a>Laboratuvarların oluşturmasına izin verilecek kim?

BT takımınızın veya fakülte üyelerinizin laboratuvarları oluşturmasını tercih edebilirsiniz. Laboratuvarlar oluşturmak için bu kişileri laboratuvar hesabı içindeki laboratuvar Oluşturucu rolüne atarsınız. Bu rolü normalde okulunuzun aboneliğinizle ilişkili Azure AD kiracısından atarsınız. Her koşulda, laboratuvarın sahibi olarak otomatik olarak bir laboratuvar atanır.  

Laboratuvar Oluşturucu rolü hakkında daha fazla bilgi için, [Azure Lab Services Yönetici kılavuzunun](./administrator-guide.md#manage-identity)"kimliği Yönet" bölümüne bakın.

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Laboratuvarlara sahip ve bunları yönetmesine izin verilecek kim?

Ayrıca, Labs 'in laboratuvarları oluşturma *yeteneği vermeden own\manage Labs 'i* de ve fakülte olarak tercih edebilirsiniz.  Bu durumda, aboneliğinizin Azure AD kiracısındaki kullanıcılara mevcut laboratuvarlara sahip veya katılımcı atanır.  

Laboratuvar sahibi ve katkıda bulunan rolleri hakkında daha fazla bilgi için, [Azure Lab Services Yönetici kılavuzunun](./administrator-guide.md#manage-identity)"kimliği Yönet" bölümüne bakın.

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>Görüntüleri kaydetmek ve Labs arasında paylaşmak istiyor musunuz?

Paylaşılan görüntü Galerisi, görüntüleri kaydetmek ve paylaşmak için kullanabileceğiniz bir hizmettir. Aynı görüntüyü kullanması gereken sınıflar için, laboratuvar oluşturucuları görüntüyü oluşturup paylaşılan bir görüntü galerisine dışarı aktarabilir.  Paylaşılan görüntü galerisine bir görüntü verildikten sonra, yeni Labs oluşturmak için kullanılabilir.

Görüntülerinizi fiziksel ortamınızda oluşturup paylaşılan bir görüntü galerisine içeri aktarmanız gerekebilir. Daha fazla bilgi için bkz. blog gönderisi [özel bir görüntüyü paylaşılan bir görüntüye aktarma Galerisi](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353).

Paylaşılan görüntü Galerisi hizmetini kullanmaya karar verirseniz, laboratuvar hesabınıza paylaşılan bir görüntü Galerisi oluşturmanız veya eklemeniz gerekir. Bu kararı şimdilik erteleyebilir çünkü paylaşılan bir görüntü Galerisi, bir laboratuar hesabına dilediğiniz zaman eklenebilir.  

Daha fazla bilgi için bkz.
- [Azure Lab Services Yönetici kılavuzunun](./administrator-guide.md#shared-image-gallery) "paylaşılan görüntü Galerisi" bölümü
- [Azure Lab Services Yönetici kılavuzunun](./administrator-guide.md#pricing) "fiyatlandırma" bölümü

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Azure Marketi 'ndeki hangi görüntüler laboratuvarlarınız için kullanılır?

Azure Marketi, laboratuvar oluşturucularının laboratuvarlarını oluşturmak için kullanabilmesi için etkinleştirebileceğiniz yüzlerce görüntü sağlar. Bazı resimlerde, laboratuvarın zaten ihtiyacı olan her şey bulunabilir. Diğer durumlarda, bir görüntüyü başlangıç noktası olarak kullanabilir ve ardından Laboratuvar Oluşturucu ek uygulamalar veya Araçlar yükleyerek bunu özelleştirebilir.

Hangi görüntülerden ihtiyacınız olduğunu bilmiyorsanız, daha sonra bunları etkinleştirmek için daha sonra geri dönebilirsiniz. Hangi görüntülerin kullanılabilir olduğunu görmenin en iyi yolu, ilk olarak bir laboratuar hesabı oluşturmaktır. Bu, kullanılabilir görüntülerin ve içeriklerinin listesini gözden geçirebilmeniz için size erişmenizi sağlar.  

Daha fazla bilgi için bkz. [Laboratuvar oluşturucuları için kullanılabilen Azure Marketi görüntülerini belirtme](./specify-marketplace-images.md).
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>Laboratuvar VM 'lerinin diğer Azure veya şirket içi kaynaklara erişmesi gerekiyor mu?

Laboratuvar hesabı ayarladığınızda, laboratuvar hesabınızı bir sanal ağ ile de aktarabilirsiniz.  Hem sanal ağınızın hem de laboratuvar hesabınızın aynı bölgede bulunması gerektiğini aklınızda bulundurun.  Bir sanal ağla eş yapmanız gerekip gerekmediğine karar vermek için aşağıdaki senaryoları göz önünde bulundurun:

- **Bir lisans sunucusuna erişim**
  
   Azure Marketi görüntülerini kullandığınızda, işletim sistemi lisansının maliyeti Laboratuvar Hizmetleri fiyatlandırmasına paketlenmiştir. Ancak, işletim sisteminin kendisi için lisans sağlamanız gerekmez. Yüklü ek yazılım ve uygulamalar için, uygun şekilde bir lisans sağlamanız gerekir.  Bir lisans sunucusuna erişmek için:
   - Şirket içi lisans sunucusuna bağlanmayı seçebilirsiniz.  Şirket içi lisans sunucusuna bağlanmak ek kurulum gerektirir.
   - Daha hızlı bir şekilde ayarlanarak, bir Azure VM üzerinde barındırmanıza olanak tanıyan bir lisans sunucusu oluşturmaktır.  Azure VM, laboratuvar hesabınızla eşolduğunuz bir sanal ağ içinde bulunur.

- **Dosya paylaşma veya veritabanı gibi diğer şirket içi kaynaklara erişim**

   Genellikle siteden siteye sanal ağ geçidi kullanarak şirket içi kaynaklara erişim sağlamak için bir sanal ağ oluşturursunuz. Bu ortam türünün ayarlanması, ek zaman alır.

- **Bir sanal ağ dışında bulunan diğer Azure kaynaklarına erişim**

   Bir sanal ağ içinde güvenliği *olmayan* Azure kaynaklarına erişmeniz gerekiyorsa, herhangi bir eşleme yapmak zorunda kalmadan herkese genel İnternet üzerinden erişebilirsiniz.

   Sanal ağlar hakkında daha fazla bilgi için bkz.
   - [Azure Lab Services sürümündeki mimari temelleri](./classroom-labs-fundamentals.md#virtual-network) "sanal ağ" bölümü
   - [Laboratuvar ağınızı Azure Lab Services içindeki bir eş sanal ağla bağlama](./how-to-connect-peer-virtual-network.md)
   - [Azure Lab Services paylaşılan kaynakla bir laboratuvar oluşturma](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Laboratuvar hesabınızı ayarlama

Planlamayı tamamladıktan sonra laboratuvar hesabınızı ayarlamaya hazırsınız demektir. [Ekiplerde Azure Lab Services](./lab-services-within-teams-overview.md)ayarlamak için de aynı adımları uygulayabilirsiniz.

1. **Laboratuvar hesabınızı oluşturun**. Yönergeler için bkz. [Laboratuvar hesabı oluşturma](./tutorial-setup-lab-account.md#create-a-lab-account).
   
    Adlandırma kuralları hakkında daha fazla bilgi için, [Azure Lab Services Yönetici kılavuzunun](./administrator-guide.md#naming)"adlandırma" bölümüne bakın.

1. **Laboratuvar Oluşturucu rolüne kullanıcı ekleyin**. Yönergeler için bkz. [Laboratuvar Oluşturucu rolüne kullanıcı ekleme](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

1. **Eş sanal ağa bağlanın**. Yönergeler için bkz. [Laboratuvar ağınızı bir eş sanal ağ Ile bağlama](./how-to-connect-peer-virtual-network.md).

   Ayrıca [, laboratuvar VM 'leri adres aralığını yapılandırmak](./how-to-configure-lab-accounts.md)için yönergelere başvurmanız gerekebilir.

1. **Görüntüleri etkinleştirin ve gözden geçirin**. Yönergeler için bkz. [Laboratuvar oluşturucuları için hangi Azure Marketi görüntülerinin kullanılabilir olduğunu belirtme](./specify-marketplace-images.md).

   Her bir Azure Marketi görüntüsünün içeriğini gözden geçirmek için görüntü adını seçin. Örneğin, aşağıdaki ekran görüntüsünde Ubuntu Veri Bilimi VM'si görüntüsünün ayrıntıları gösterilmektedir:

   ![Azure Marketi 'nde gözden geçirilmek üzere kullanılabilen görüntülerin listesinin ekran görüntüsü.](./media/setup-guide/review-marketplace-images.png)

   Laboratuvar hesabınıza bir paylaşılan görüntü Galerisi eklenmişse ve özel görüntülerin laboratuvar oluşturucuları tarafından paylaşılmasını etkinleştirmek istiyorsanız, aşağıdaki ekran görüntüsünde gösterildiği gibi benzer adımları uygulayın:

   ![Paylaşılan görüntü galerisinde etkin özel görüntülerin listesinin ekran görüntüsü.](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Sonraki adımlar

Labs 'i ayarlama ve yönetme hakkında daha fazla bilgi için bkz.:

- [Laboratuvar hesaplarını yönetme](how-to-manage-lab-accounts.md)  
- [Laboratuvar Kurulum Kılavuzu](setup-guide.md)
