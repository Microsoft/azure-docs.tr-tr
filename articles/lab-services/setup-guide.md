---
title: Azure Lab Services için hızlandırılmış laboratuvar Kurulum Kılavuzu
description: Laboratuvar Oluşturucu kullanıyorsanız, bu kılavuz okulunuzda hızlı bir şekilde laboratuvar hesabı ayarlamanıza yardımcı olabilir.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95021751"
---
# <a name="lab-setup-guide"></a>Laboratuvar Kurulum Kılavuzu

Bu kılavuzda okulunuzdaki öğrenciler için laboratuvar oluşturmayı öğreneceksiniz.

Öğrencilerinize laboratuvar yayımlamaya yönelik işlem birkaç saate kadar sürebilir. Kurulum süresi miktarı, laboratuvarınızda oluşturmak istediğiniz sanal makinelerin (VM) sayısına bağlıdır. Laboratuvarın düzgün çalıştığından ve öğrencilerinizin sanal makinelerini yayımlaması için yeterli zamana izin veren en az bir güne izin verin.

## <a name="understand-the-lab-requirements-of-your-class"></a>Sınıfınızın laboratuvar gereksinimlerini anlayın

Yeni bir laboratuvar ayarlamadan önce aşağıdaki soruları göz önünde bulundurmanız gerekir.

### <a name="what-software-requirements-does-the-class-have"></a>Sınıfta hangi yazılım gereksinimleri vardır?

Laboratuvar VM 'lerine yüklemeniz gereken işletim sistemi, uygulama ve araçlara karar verirken sınıfınızın öğrenme hedeflerine bakın. Laboratuvar VM 'Leri ayarlamak için üç seçeneğiniz vardır:

- **Azure Marketi görüntüsünü kullanma**: Azure Marketi, bir laboratuvar oluştururken kullanabileceğiniz yüzlerce görüntü sağlar. Bazı sınıflarda, bu görüntülerden biri sınıfınız için ihtiyaç duyduğunuz her şeyi zaten içeriyor olabilir.

- **Yeni bir özel görüntü oluşturma**: bir Azure Marketi görüntüsünü başlangıç noktası olarak kullanarak kendi özel görüntünüzü oluşturabilirsiniz. Daha sonra ek yazılım yükleyerek ve yapılandırma değişiklikleri yaparak onu özelleştirebilirsiniz.

- **Mevcut bir özel görüntü kullan**: daha önce oluşturduğunuz özel görüntüleri veya okulunuzdaki diğer yöneticiler veya fakülte oluşturulan görüntüleri yeniden kullanabilirsiniz. Özel görüntüleri kullanmak için, yöneticileriniz paylaşılan bir görüntü Galerisi ayarlaması gerekir.  Paylaşılan görüntü Galerisi, özel görüntüleri kaydetmek için kullanılan bir depodur.

> [!NOTE]
> Yöneticileriniz, Azure Marketi görüntülerini ve özel görüntüleri, bunları kullanabilmeniz için etkinleştirmekten sorumludur. İhtiyacınız olan görüntülerin etkinleştirildiğinden emin olmak için BT departmanınızla koordine edin. Oluşturduğunuz özel görüntüler, sahip olduğunuz laboratuvarlarda kullanılmak üzere otomatik olarak etkinleştirilir.

### <a name="what-hardware-requirements-does-the-class-have"></a>Sınıfta hangi donanım gereksinimleri vardır?

Çeşitli bilgi işlem boyutları arasından seçim yapabilirsiniz:

- **Iç içe sanallaştırma boyutları**: öğrencilerden çoklu, iç Içe geçmiş VM 'leri barındırasağlayan bir VM 'ye erişim sağlamanıza olanak tanır. Örneğin, bu işlem boyutunu ağ veya ahlak olabilecek sınıflar için kullanabilirsiniz.

- **GPU boyutları**: öğrencilerinizin bilgisayar yoğunluklu uygulamalar türlerini kullanmasına izin verir. Örneğin, bu seçenek genellikle yapay zeka ve makine öğrenimi ile kullanılır.

Uygun VM boyutunu seçme kılavuzu için, bkz.:
- [VM boyutlandırma](./administrator-guide.md#vm-sizing)
- [Fiziksel laboratuvardan Azure Lab Services taşıyın](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> İşlem boyutu kullanılabilirliği bölgeye göre değiştiğinden, laboratuvarınız için daha az boyut kullanılabilir olabilir. Genellikle, gereksinimlerinize uyan en düşük işlem boyutunu seçmeniz gerekir. Azure Lab Services ile, daha sonra ihtiyacınız olursa daha sonra daha fazla işlem kapasitesine sahip yeni bir laboratuvar ayarlayabilirsiniz.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Sınıfın dış Azure veya ağ kaynaklarında hangi bağımlılıkları vardır?
Laboratuvar sanal makinelerinizin, bir veritabanı, dosya paylaşma veya bir lisanslama sunucusu gibi dış kaynaklara erişmesi gerekebilir.  Laboratuvar sanal makinelerinizin dış kaynakları kullanmasına izin vermek için BT yöneticilerinizle koordine edin.

> [!NOTE]
> VM üzerinde doğrudan ağ kaynakları sağlayarak laboratuvarınızın dış kaynaklardaki bağımlılığını azaltacağınızı düşünmeniz gerekir. Örneğin, bir dış veritabanından veri okuma gereksinimini ortadan kaldırmak için veritabanını doğrudan VM 'ye yükleyebilirsiniz.  

### <a name="how-will-you-control-costs"></a>Maliyetleri nasıl denetlersin?
Laboratuvar Hizmetleri bir Kullandıkça Öde fiyatlandırma modeli kullanır, bu da yalnızca bir laboratuvar VM 'sinin çalıştığı zaman için ödeme yaptığınız anlamına gelir. Maliyetleri denetlemek için aşağıdaki seçeneklerden herhangi birini veya tümünü kullanın:

- **Zamanlama**: Laboratuvar sanal makinelerinizin ne zaman başlatılacağını ve kapatıldığını otomatik olarak denetlemek için zamanlamalar kullanın.
- **Kota**: öğrencilerin zamanlanan saatler dışında bir VM 'ye erişiminin olduğu saat sayısını denetlemek için kotalar kullanın.  Bir öğrenci bir VM kullanırken ve bir kotaya ulaştığında, VM otomatik olarak kapanır.  Kotayı artırmadığınız müddetçe öğrenci sanal makineyi yeniden başlatamıyor.
- **Otomatik kapatma**: otomatik kapatma ayarını etkinleştirdiğinizde, bir öğrencinin Uzak Masaüstü Protokolü (RDP) oturumuna bağlantısı kesildikten sonra Windows VM 'leri otomatik olarak kapanır. Bu ayar varsayılan olarak devre dışıdır.

Maliyetleri denetleme hakkında daha fazla bilgi için bkz.:
- [Maliyetleri tahmin etme](./cost-management-guide.md#estimate-the-lab-costs)
- [Maliyetleri yönetme](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Öğrenciler işlerini nasıl kaydeder?
Her öğrenciye, laboratuvarın ömrü boyunca bir VM atanır. Öğrenciler işlerini kaydedebilir:

- VM 'ye gidin.
- OneDrive veya GitHub gibi bir dış konuma. OneDrive 'ı laboratuvar VM 'lerinde öğrenciler için otomatik olarak yapılandırmak mümkündür.

> [!NOTE]
> Öğrencilerinizin laboratuvar dışındaki kayıtlı çalışmalarına erişiminin devam ettiğinden ve sınıf bittikten sonra işlerini bir dış depoya kaydetmelerini öneririz.

### <a name="how-will-students-connect-to-their-vms"></a>Öğrenciler, VM 'lerine nasıl bağlanır?
Windows VM 'lerine RDP bağlantıları için öğrencilerin [Microsoft Uzak masaüstü istemcisini](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)kullanmasını öneririz. Uzak Masaüstü istemcisi Mac, Kmebook ve Windows cihazlarını destekler.

Linux VM 'Ler için, öğrenciler Secure Shell (SSH) veya RDP protokolünü kullanabilir. Öğrencilerin RDP kullanarak bağlanmasını sağlamak için gerekli RDP ve grafik kullanıcı arabirimi (GUI) paketlerini yükleyip yapılandırmanız gerekir.

### <a name="will-students-also-use-microsoft-teams"></a>Öğrenciler Microsoft ekipleri de kullanacaktır mi?
Azure Lab Services, Microsoft ekipleri ile tümleşir, böylece fakülte üyeleri ekiplerde laboratuvarlarını oluşturup yönetebilir.  Benzer şekilde, öğrenciler ekiplerde laboratularına erişebilir.

Daha fazla bilgi için bkz. [Microsoft ekiplerinde Azure Lab Services](./lab-services-within-teams-overview.md).

## <a name="set-up-your-lab"></a>Laboratuvarınızı kurma

Sınıfınızın laboratuvarınızın gereksinimlerini anladıktan sonra, ayarlamaya hazırsınız demektir. Nasıl yapılacağını öğrenmek için bu bölümdeki bağlantıları izleyin. Ekiplerde laboratuvarları ayarlamak için de yönergeler sağlanır.

1. **Laboratuvar oluşturun**. Aşağıdaki öğreticilere bakın:
    - [Sınıf laboratuvarı oluşturma](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Ekiplerde Laboratuvar oluşturma](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Sınıfınız iç içe sanallaştırma gerektiriyorsa bkz. [iç içe sanallaştırmayı etkinleştirme](./how-to-enable-nested-virtualization-template-vm.md).

1. **Görüntüleri özelleştirin ve laboratuvar VM 'leri yayımlayın**. Şablon VM adlı özel bir VM 'ye bağlanmak için bkz.:
    - [Şablon VM 'si oluşturma ve yönetme](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Paylaşılan görüntü galerisi kullanma](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Windows kullanıyorsanız, Ayrıca bkz. [Windows şablonu VM 'Si ayarlama](./how-to-prepare-windows-template.md). Bu yönergeler, öğrencileriniz için OneDrive ve Microsoft Office ayarlama adımlarını içerir.

1. **VM havuzunu ve kapasitesini yönetin**. Sınıfınızın gerektirdiği şekilde, sanal makine kapasitesini kolayca ölçeklendirebilir veya azaltabilirsiniz. Yeni VM 'Lerin ayarlandığı için VM kapasitesinin artmasının birkaç saat sürebileceğini aklınızda bulundurun. Aşağıdaki makalelere bakın:
    - [Bir VM havuzunu ayarlama ve yönetme](./how-to-set-virtual-machine-passwords.md)
    - [Ekiplerdeki laboratuvar hizmetlerinde bir VM havuzunu yönetme](./how-to-manage-vm-pool-within-teams.md)

1. **Laboratuvar kullanıcıları ekleyin ve yönetin**. Laboratuvarınızı Kullanıcı eklemek için bkz.:
   - [Laboratuvara Kullanıcı ekleme](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Kullanıcılara davet gönder](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Ekiplerde Laboratuvar Hizmetleri Kullanıcı listelerini yönetme](./how-to-manage-user-lists-within-teams.md)

    Öğrencilerin kullanabileceği hesap türleri hakkında daha fazla bilgi için bkz. [öğrenci hesapları](./how-to-configure-student-usage.md#student-accounts).
  
1. **Maliyet denetimlerini ayarlayın**. Bir zamanlama ayarlamak, Kotalar oluşturmak ve otomatik kapanışı etkinleştirmek için aşağıdaki öğreticilere bakın:

   - [Zamanlama ayarlama](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > Yüklediğiniz işletim sistemine bağlı olarak, bir VM 'nin başlaması birkaç dakika sürebilir. Bir laboratuvar VM 'sinin zamanlanan saatleriniz sırasında kullanıma hazırlandığından emin olmak için 30 dakika sonra yeniden başlatmanız önerilir.

   - [Kullanıcılar için kotalar ayarlama](./how-to-configure-student-usage.md#set-quotas-for-users) ve [belirli kullanıcılar için ek kotalar ayarlama](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [Bağlantı kesildiğinde otomatik kapatmayı etkinleştirme](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Zamanlamalar ve kotalar, şablon VM 'ye uygulanmaz, ancak otomatik kapatılma ayarları uygulanır. 
        > 
        > Laboratuvar oluşturduğunuzda, şablon VM oluşturulur ancak başlatılmaz. Şablon sanal makinesini başlatabilir, bu makineye bağlanabilir, laboratuvar için önkoşul yazılımlarını yükleyebilir ve sonra yayımlayabilirsiniz. Şablon VM 'yi yayımladığınızda, el ile yapmadıysanız, sizin için otomatik olarak kapatılır. 
        > 
        > Şablon VM 'Leri çalışırken *Maliyet* doğurur, bu nedenle, çalışıyor olması GEREKMIYORSA şablon VM 'nin kapatıldığından emin olun.

    - [Ekiplerde Laboratuvar Hizmetleri zamanlamaları oluşturma ve yönetme](./how-to-create-schedules-within-teams.md) 

1. **Panoyu kullanın**. Yönergeler için bkz. [derslik laboratuvar panosunu kullanma](./use-dashboard.md).

    > [!NOTE]
    > Panoda gösterilen tahmini maliyet, öğrenci laboratuvarı kullanımı için ödeme yapmak için tahmin edebileceğiniz maksimum maliyettir. Örneğin, öğrencileriniz tarafından  kullanılmayan kota saatleri için ücretlendirilmeyecektir. Tahmini maliyetler, şablon VM 'si, paylaşılan görüntü Galerisi veya laboratuvar Oluşturucu bir Kullanıcı makinesini başlattığında herhangi bir ücret *yansıtmazlar* .

## <a name="next-steps"></a>Sonraki adımlar

Laboratuvarlarınızı yönetmenin bir parçası olarak aşağıdaki makalelere bakın:
- [Sınıf Laboratuvarı kullanımını izle](tutorial-track-usage.md)  
- [Bir sınıf laboratuvarına erişim](tutorial-connect-virtual-machine-classroom-lab.md)
