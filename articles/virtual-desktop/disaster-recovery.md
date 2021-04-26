---
title: Windows sanal masaüstü olağanüstü durum kurtarma planını ayarlama-Azure
description: Windows sanal masaüstü dağıtımınız için iş sürekliliği ve olağanüstü durum kurtarma planı ayarlama.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 18089bc00e9d02087acb149511fbc2c55077c153
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446936"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>İş sürekliliği ve olağanüstü durum kurtarma planı ayarlama

Kuruluşunuzun verilerini güvende tutmak için, bir iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejisini benimsemeniz gerekebilir. Bir Sound BCDR stratejisi, planlı ve plansız hizmet veya Azure kesintileri sırasında uygulamalarınızı ve iş yükünüzü çalışır durumda tutar.

Windows sanal masaüstü, kesintiler sırasında müşteri meta verilerini korumak için Windows sanal masaüstü hizmeti için BCDR sağlar. Bölgede bir kesinti oluştuğunda, hizmet altyapısı bileşenleri ikincil konuma yük devreder ve normal olarak çalışmaya devam eder. Hizmetle ilgili meta verilere erişmeye devam edebilirsiniz ve kullanıcılar hala kullanılabilir konaklara bağlanabilir. Kiracı ortamı veya Konakları erişilebilir durumda kaldığı sürece son kullanıcı bağlantıları çevrimiçi kalacaktır.

Kullanıcıların bir bölge kesintisi sırasında hala bağlanabilmelerini sağlamak için, sanal makinelerini (VM 'Ler) farklı bir konumda çoğaltmanız gerekir. Kesintiler sırasında birincil site, ikincil konumdaki çoğaltılan VM 'lere devreder. Kullanıcılar, kesintiye uğramadan ikincil konumdan uygulamalara erişmeye devam edebilir. VM çoğaltmasının en üstünde, Kullanıcı kimliklerini ikincil konumdan erişilebilir tutmanız gerekir. Profil kapsayıcıları kullanıyorsanız, bunları da çoğaltmanız gerekir. Son olarak, birincil konumdaki verileri kullanan iş uygulamalarınızın, verilerin geri kalanı ile yük devredebildiğini doğrulayın.

Özetlemek gerekirse, kullanıcılarınızın bir kesinti sırasında bağlanmasını sağlamak için aşağıdaki işlemleri bu sırayla gerçekleştirmeniz gerekir:

- VM 'Leri ikincil bir konumda çoğaltın.
- Profil kapsayıcıları kullanıyorsanız, ikincil konumda veri çoğaltmasını ayarlayın.
- Birincil konumda ayarladığınız kullanıcı kimliklerinin ikincil konumda kullanılabilir olduğundan emin olun.
- Birincil konumunuzda bulunan verilere bağlı olan tüm iş kolu uygulamalarının ikincil konuma yük devretmeden emin olun.

## <a name="vm-replication"></a>VM çoğaltması

İlk olarak, VM 'lerinizi ikincil konuma çoğaltmanız gerekir. Bunu yapmak için seçenekleriniz, sanal makinelerinizin nasıl yapılandırıldığına bağlıdır:

- Hem havuza alınan hem de kişisel ana bilgisayar havuzlarının tüm VM 'lerinizi Azure Site Recovery ile yapılandırabilirsiniz. Bu yöntemle yalnızca bir konak havuzu ve ilgili uygulama gruplarını ve çalışma alanlarını ayarlamanız gerekir.
- Yük devretme konumunuzdaki tüm kaynakların kapalı tutulması sırasında yük devretme bölgesinde yeni bir konak havuzu oluşturabilirsiniz. Bu yöntemde, yük devretme bölgesinde yeni uygulama grupları ve çalışma alanları ayarlamanız gerekir. Daha sonra konak havuzlarını açmak için bir Azure Site Recovery planı kullanabilirsiniz.
- Yük devretme bölgesindeki VM 'Lerin kapalı tutulması sırasında hem birincil hem de yük devretme bölgelerinde oluşturulan VM 'Ler tarafından doldurulan bir konak havuzu oluşturabilirsiniz. Bu durumda, yalnızca bir konak havuzu ve ilgili uygulama gruplarını ve çalışma alanlarını ayarlamanız gerekir. Bu yöntemle konak havuzlarında güç sağlamak için bir Azure Site Recovery planı kullanabilirsiniz.

[Azure 'Dan Azure 'a olağanüstü durum kurtarma mimarisi](../site-recovery/azure-to-azure-architecture.md)' nde açıklandığı gibi diğer Azure konumlarındaki VM 'leri çoğaltmayı yönetmek için [Azure Site Recovery](../site-recovery/site-recovery-overview.md) kullanmanızı öneririz. Azure Site Recovery hem [sunucu tabanlı hem de istemci tabanlı SKU 'ları](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems)desteklediğinden, özellikle kişisel ana bilgisayar havuzları için Azure Site Recovery kullanmanızı öneririz.

Azure Site Recovery kullanıyorsanız, bu VM 'Leri el ile kaydetmeniz gerekmez. İkincil VM 'deki Windows sanal masaüstü Aracısı, en yakın hizmet örneğine bağlanmak için en son güvenlik belirtecini otomatik olarak kullanacaktır. İkincil konumdaki VM (oturum ana bilgisayarı) otomatik olarak konak havuzunun bir parçası olur. Son kullanıcının işlem sırasında yeniden bağlantısı olması gerekir, ancak bunun dışında, başka bir el ile işlem yoktur.

Kesinti sırasında mevcut kullanıcı bağlantıları varsa, yöneticinin ikincil bölgeye yük devretmeye başlayabilmesi için, geçerli bölgedeki Kullanıcı bağlantılarını sonlandıralmanız gerekir.

Windows sanal masaüstündeki (klasik) kullanıcıların bağlantısını kesmek için şu cmdlet 'i çalıştırın:

```powershell
Invoke-RdsUserSessionLogoff
```

Windows sanal masaüstü 'nün Azure tümleşik sürümündeki kullanıcıların bağlantısını kesmek için şu cmdlet 'i çalıştırın:

```powershell
Remove-AzWvdUserSession
```

Birincil bölgedeki tüm kullanıcıları imzaladıktan sonra, birincil bölgedeki VM 'Lerin yükünü devreder ve kullanıcıların ikincil bölgedeki sanal makinelere bağlanmasını sağlayabilirsiniz. Bu işlemin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure VM 'lerini başka bir Azure bölgesine çoğaltma](../site-recovery/azure-to-azure-how-to-enable-replication.md).

## <a name="virtual-network"></a>Sanal ağ

Sonra, kesinti sırasında ağ bağlantınızı göz önünde bulundurun. İkincil bölgenizde bir sanal ağ (VNET) ayarladığınızdan emin olmanız gerekir. Kullanıcılarınızın şirket içi kaynaklara erişmesi gerekiyorsa, bu VNET 'e erişmek için bu VNET 'i yapılandırmanız gerekir. VPN, ExpressRoute veya sanal WAN ile şirket içi bağlantılar kurabilirsiniz.

Birincil ağınızın ayarlarını koruyan ve eşleme gerektirmeyen VNET 'i kurmak için Azure Site Recovery kullanmanızı öneririz.

## <a name="user-identities"></a>Kullanıcı kimlikleri

Daha sonra, etki alanı denetleyicisinin ikincil konumda kullanılabildiğinden emin olun. 

Etki alanı denetleyicisini kullanılabilir tutmanın üç yolu vardır:

   - İkincil konumda Active Directory Etki Alanı denetleyicisi var
   - Şirket içi Active Directory Etki Alanı Denetleyicisi kullanma
   - [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) kullanarak Active Directory etki alanı denetleyicisini çoğaltma

## <a name="user-and-app-data"></a>Kullanıcı ve uygulama verileri

Profil kapsayıcıları kullanıyorsanız, sonraki adım ikincil konumda veri çoğaltmayı kurmak olur. FSLogix profillerini depolamak için beş seçeneğiniz vardır:

   - Depolama Alanları Doğrudan (S2D)
   - Ağ sürücüleri (ek sürücülerle VM)
   - Azure Dosyaları
   - Azure NetApp Files
   - Çoğaltma için bulut önbelleği

Daha fazla bilgi için [Windows sanal masaüstündeki FSLogix profil kapsayıcıları Için depolama seçeneklerini](store-fslogix-profile.md)inceleyin.

Profiller için olağanüstü durum kurtarma ayarlıyorsanız bunlar seçenekleriniz şunlardır:

   - Yerel Azure çoğaltmasını ayarlayın (örneğin, Azure dosyaları standart depolama hesabı çoğaltma, Azure NetApp Files çoğaltma veya dosya sunucuları için Azure dosya eşitleme).
    
     >[!NOTE]
     >Newtapp çoğaltması, ilk kez ayarlandıktan sonra otomatik hale gelir. Azure Site Recovery planlarla, VM dışı kaynakların yük devretme öncesi, Azure depolama kaynaklarını çoğaltabilmek için komut dosyaları ve komut dosyaları ekleyebilirsiniz.

   - Hem uygulama hem de Kullanıcı verileri için FSLogix bulut önbelleğini ayarlayın.
   - Yalnızca iş açısından kritik verilere her zaman erişim sağlamak için, uygulama verileri için olağanüstü durum kurtarmayı ayarlayın. Bu yöntemde, kesinti bittikten sonra Kullanıcı verilerini alabilirsiniz.

Her seçenek için olağanüstü durum kurtarma ayarlamak üzere FSLogix yapılandırma bölümüne göz atalım.

### <a name="fslogix-configuration"></a>FSLogix yapılandırması

FSLogix için kayıt defteri girdilerini yapılandırırsanız FSLogix Aracısı birden çok profil konumunu destekleyebilir.

Kayıt defteri girdilerini yapılandırmak için:

1. **Kayıt Defteri Düzenleyicisi**'ni açın.
2. **Bilgisayar**  >  **HKEY_LOCAL_MACHINE**  >  **Software**  >  **fslogix**  >  **profilleri**' ne gidin.
   
     > [!div class="mx-imgBorder"]
     > ![Kayıt defteri düzenleyicisinde profiller penceresinin ekran görüntüsü. VHDLocation seçildi.](media/regedit-profiles.png)

3. **Vhdlocations** öğesine sağ tıklayın ve çok dizeli **Düzenle**' yi seçin.

     > [!div class="mx-imgBorder"]
     > ![Çok dizeli düzenleme penceresinin ekran görüntüsü. Değer verisi, Centrual US ve Doğu ABD konumlarını listeler.](media/multi-string-edit.png)

4. **Değer verisi** alanına, kullanmak istediğiniz konumları girin.
5. İşiniz bittiğinde **Tamam**’ı seçin.

İlk konum kullanılamıyorsa, FSLogix Aracısı otomatik olarak saniyede yük devreder ve bu şekilde devam eder.

FSLogix aracısını ana bölgedeki ikincil konumun bir yolu ile yapılandırmanızı öneririz. Birincil konum kapatıldıktan sonra, FLogix Aracısı VM Azure Site Recovery çoğaltması kapsamında çoğaltılır. Çoğaltılan VM 'Ler hazırlandıktan sonra, aracı otomatik olarak ikincil bölgenin yolunu dener.

Örneğin, birincil oturum ana bilgisayar sanal makinelerinizin Orta ABD bölgede olduğunu varsayalım, ancak profil Kapsayıcınız performans nedenleriyle Orta ABD bölgedeyse.

Bu durumda, FSLogix aracısını Orta ABD ' deki depolamanın bir yolu ile yapılandırırsınız. Batı ABD çoğaltma için oturum ana bilgisayar VM 'lerini yapılandırırsınız. Orta ABD yolu başarısız olduktan sonra, aracı bunun yerine Batı ABD depolama için yeni bir yol oluşturmaya çalışır.

### <a name="s2d"></a>S2D

S2D bölgeler arasında çoğaltmayı dahili olarak yaptığından, ikincil yolu el ile ayarlamanız gerekmez.

### <a name="network-drives-vm-with-extra-drives"></a>Ağ sürücüleri (ek sürücülerle VM)

Ağ depolama VM 'lerini, oturum ana bilgisayar VM 'Leri gibi Azure Site Recovery kullanarak çoğaltıyorsanız, kurtarma aynı yolu korur, yani FSlogix ' i yeniden yapılandırmanız gerekmez.

### <a name="azure-files"></a>Azure Dosyaları

Azure dosyaları, depolama hesabını oluştururken belirtebileceğiniz, siteler arası zaman uyumsuz çoğaltmayı destekler. Azure dosyalarının zaman uyumsuz doğası, olağanüstü durum kurtarma hedeflerinizi zaten kapsıyorsa, ek yapılandırma yapmanız gerekmez.

Veri kaybını en aza indirmek için zaman uyumlu çoğaltmaya ihtiyacınız varsa, bunun yerine FSLogix bulut önbelleğini kullanmanızı öneririz.

>[!NOTE]
>Bu bölüm, Azure dosyaları için yük devretme kimlik doğrulama mekanizmasını kapsamaz.

### <a name="azure-netapp-files"></a>Azure NetApp Files

[Azure NetApp Files için çoğaltma eşlemesi oluşturma](../azure-netapp-files/cross-region-replication-create-peering.md)hakkında daha fazla bilgi Azure NetApp Files.

## <a name="app-dependencies"></a>Uygulama bağımlılıkları

Son olarak, birincil bölgede yer alan verileri kullanan tüm iş uygulamalarının ikincil konuma yük devredebildiğini doğrulayın. Ayrıca, uygulamaların yeni konumda çalışması için gereken ayarları yapılandırmayı unutmayın. Örneğin, uygulamalardan biri SQL arka uca bağımlıysa, SQL 'i ikincil konumda çoğaltdığınızdan emin olun. Uygulamayı, yük devretme işleminin parçası olarak veya varsayılan yapılandırması olarak ikincil konumu kullanacak şekilde yapılandırmanız gerekir. Azure Site Recovery planlarında uygulama bağımlılıklarını modelleyebilirsiniz. Daha fazla bilgi için bkz. [kurtarma planları hakkında](../site-recovery/recovery-plan-overview.md).

## <a name="disaster-recovery-testing"></a>Olağanüstü durum kurtarma testi

Olağanüstü durum kurtarmayı ayarlamayı tamamladıktan sonra, çalıştığından emin olmak için planınızı test etmek isteyeceksiniz.

Planınızı test etme hakkında bazı öneriler aşağıda verilmiştir:

- Test VM 'lerinin internet erişimi varsa, yeni bağlantılar için var olan herhangi bir oturum konağını alırlar, ancak özgün oturum ana bilgisayarına olan tüm mevcut bağlantılar etkin kalır. Testi çalıştıran yöneticinin, planı test etmeden önce tüm etkin kullanıcıları imzalayadığına emin olun. 
- Yalnızca bir bakım penceresi sırasında kullanıcılarınızı kesintiye uğramamaları için tam olağanüstü durum kurtarma testleri yapmanız gerekir. Test için doğrulama ortamında bir konak havuzu da kullanabilirsiniz. 
- Testinizin iş açısından kritik tüm uygulamaları kapsadığından emin olun.
- Tek seferde 100 VM 'ye kadar yük devretmenizi öneririz. Bundan daha fazla sanal makine varsa, bunların 10 dakika içinde yük devredebilmeniz önerilir.

## <a name="next-steps"></a>Sonraki adımlar

Kesintileri planlamaya ek olarak verilerinizin güvenliğini nasıl güvende tutacağız hakkında sorularınız varsa [güvenlik kılavuzumuzu](security-guide.md)inceleyin.