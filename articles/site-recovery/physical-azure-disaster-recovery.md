---
title: Azure Site Recovery ile fiziksel şirket içi sunucular için olağanüstü durum kurtarmayı ayarlama
description: Azure Site Recovery hizmeti ile şirket içi Windows ve Linux sunucuları için Azure 'da olağanüstü durum kurtarmayı ayarlamayı öğrenin.
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.openlocfilehash: 5775f8ed9b657502f4865f3c4f311ea0dcd269ea
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581543"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Şirket içi fiziksel sunucular için Azure 'da olağanüstü durum kurtarmayı ayarlama

[Azure Site Recovery](site-recovery-overview.md) hizmeti, şirket içi makinelerin ve Azure sanal makinelerinin (VM) çoğaltma, yük devretme ve yeniden çalışma işlemlerini yöneterek ve düzenleyerek olağanüstü durum kurtarma stratejinize katkı sağlar.

Bu öğreticide, şirket içi fiziksel Windows ve Linux sunucularının Azure 'a olağanüstü durum kurtarma işlemlerinin nasıl ayarlanacağı gösterilmektedir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure ve şirket içi önkoşulları ayarlama
> * Site Recovery için Kurtarma Hizmetleri kasası oluşturma 
> * Kaynak ve hedef çoğaltma ortamlarını ayarlama
> * Çoğaltma ilkesi oluşturma
> * Sunucu için çoğaltmayı etkinleştirme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- Bu senaryonun [mimarisini ve bileşenlerini](physical-azure-architecture.md) anladığınızdan emin olun.
- Tüm bileşenler için [destek gereksinimlerini](vmware-physical-secondary-support-matrix.md) gözden geçirin.
- Çoğaltmak istediğiniz sunucuların [Azure VM gereksinimleriyle](vmware-physical-secondary-support-matrix.md#replicated-vm-support)uyumlu olduğundan emin olun.
- Azure 'u hazırlayın. Bir Azure aboneliğiniz, bir Azure sanal ağı ve bir depolama hesabınız olması gerekir.
- Çoğaltmak istediğiniz her sunucuya Mobility hizmetinin otomatik olarak yüklenmesine yönelik bir hesap hazırlayın.

Başlamadan önce şunları unutmayın:

- Azure 'a yük devretmeden sonra, fiziksel sunucular şirket içi fiziksel makinelere geri alınamaz. Yalnızca VMware VM 'lerine geri dönebilirsiniz. 
- Bu öğretici, en basit ayarlarla Azure 'da fiziksel sunucu olağanüstü durum kurtarmayı ayarlar. Diğer seçenekler hakkında daha fazla bilgi edinmek istiyorsanız, nasıl yapılır kılavuzlarımızı okuyun:
    - Site Recovery yapılandırma sunucusu da dahil olmak üzere [çoğaltma kaynağını](physical-azure-set-up-source.md)ayarlayın.
    - [Çoğaltma hedefini](physical-azure-set-up-target.md) ayarlayın.
    - Bir [çoğaltma ilkesi](vmware-azure-set-up-replication.md) yapılandırın ve [çoğaltmayı etkinleştirin](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Azure hesabı ayarlama

Bir Microsoft [Azure hesabı](https://azure.microsoft.com/)alın.

- [Ücretsiz deneme sürümüyle](https://azure.microsoft.com/pricing/free-trial/) başlayabilirsiniz.
- [Site Recovery fiyatlandırma](site-recovery-faq.md#pricing)ve [fiyatlandırma ayrıntılarını](https://azure.microsoft.com/pricing/details/site-recovery/)alma hakkında bilgi edinin.
- Site Recovery için [desteklenen bölgeleri](https://azure.microsoft.com/pricing/details/site-recovery/) öğrenin.

### <a name="verify-azure-account-permissions"></a>Azure hesabı izinlerini doğrulama

Azure hesabınızın VM 'lerin Azure 'a çoğaltılması için izinlere sahip olduğundan emin olun.

- Makineleri Azure 'a çoğaltmak için gereken [izinleri](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) gözden geçirin.
- [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) izinlerini doğrulayın ve değiştirin. 



### <a name="set-up-an-azure-network"></a>Azure ağı ayarlama

Bir [Azure ağı](../virtual-network/quick-create-portal.md)kurun.

- Yük devretmeden sonra oluşturulan Azure VM 'Leri bu ağa yerleştirilir.
- Ağın, kurtarma hizmetleri kasasıyla aynı bölgede olması gerekir


## <a name="set-up-an-azure-storage-account"></a>Azure depolama hesabı ayarlama

Bir [Azure depolama hesabı](../storage/common/storage-account-create.md)ayarlayın.

- Site Recovery, şirket içi makineleri Azure Storage 'a çoğaltır. Yük devretme gerçekleştikten sonra Azure VM 'Leri depolamadan oluşturulur.
- Depolama hesabının, Kurtarma Hizmetleri kasasıyla aynı bölgede olması gerekir.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Bir hesabı Mobility hizmeti yüklemesi için hazırlama

Mobility hizmeti, çoğaltmak istediğiniz her bir sunucuda yüklü olmalıdır. Site Recovery, sunucu için çoğaltmayı etkinleştirdiğinizde bu hizmeti otomatik olarak yüklüyor. Otomatik olarak yüklemek için Site Recovery sunucuya erişmek üzere kullanacağı bir hesap hazırlamanız gerekir.

- Bir etki alanı veya yerel hesap kullanabilirsiniz
- Windows VM 'Ler için, bir etki alanı hesabı kullanmıyorsanız yerel makinede uzak kullanıcı erişim denetimini devre dışı bırakın. Bunu yapmak için, kayıt **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** altında, **LocalAccountTokenFilterPolicy** DWORD girişini 1 değeri ile ekleyin.
- Bir CLı 'dan ayarı devre dışı bırakmak üzere kayıt defteri girdisini eklemek için şunu yazın:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux için hesap, kaynak Linux sunucusunda kök olmalıdır.


## <a name="create-a-vault"></a>Kasa oluşturma

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Koruma hedefi seçme

Çoğaltılacak öğeleri seçin ve ' a çoğaltın.

1. **Kurtarma Hizmetleri kasaları** > kasa öğesine tıklayın.
2. Kaynak menüsünde, **Site Recovery**  >  **altyapı**  >  **koruma hedefini** hazırla ' ya tıklayın.
3. **Koruma hedefi**' nde, **Azure**  >  **sanallaştırılmamış/diğer**' i seçin.

## <a name="set-up-the-source-environment"></a>Kaynak ortamı ayarlama

Yapılandırma sunucusunu ayarlayın, kasaya kaydedin ve VM 'Leri bulun.

1.   >  **Altyapı**  >  **kaynağı** hazırlama Site Recovery ' ye tıklayın.
2. Bir yapılandırma sunucunuz yoksa **+ yapılandırma sunucusu**' na tıklayın.
3. **Sunucu Ekle**' de, **sunucu türü**' nde **Configuration Server** ' ın göründüğünden emin olun.
4. Site Recovery Birleşik kurulum yükleme dosyasını indirin.
5. Kasa kayıt anahtarını indirin. Birleşik kurulumu çalıştırdığınızda buna ihtiyacınız vardır. Anahtar, oluşturulduktan sonra beş gün boyunca geçerlidir.

   ![Yükleme dosyasını ve kayıt anahtarını indirme seçeneklerini gösteren ekran görüntüsü.](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Yapılandırma sunucusunu kasaya kaydetme

Başlamadan önce aşağıdakileri yapın: 

#### <a name="verify-time-accuracy"></a>Zaman doğruluğunu doğrulama
Yapılandırma sunucusu makinesinde, sistem saatinin bir [saat sunucusuyla](/windows-server/networking/windows-time-service/windows-time-service-top)eşitlendiğinden emin olun. Eşleşmelidir. Ön veya arka planda 15 dakika ise, kurulum başarısız olabilir.

#### <a name="verify-connectivity"></a>Bağlantıyı doğrulama
Makinenin ortamınıza bağlı olarak bu URL 'Lere erişebildiğinizden emin olun: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP adresi tabanlı güvenlik duvarı kuralları, HTTPS (443) bağlantı noktası üzerinden yukarıda listelenen tüm Azure URL 'Leriyle iletişime izin verir. IP aralıklarını basitleştirmek ve sınırlamak için, URL filtrelemesinin yapılması önerilir.

- **Ticari IP 'ler** - [Azure veri merkezi IP aralıklarına](https://www.microsoft.com/download/confirmation.aspx?id=41653)ve https (443) bağlantı noktasına izin verin. Aboneliğinizin Azure bölgesi için IP adresi aralıklarının AAD, yedekleme, çoğaltma ve depolama URL 'Lerini desteklemesini sağlar.  
- **Kamu IP 'leri** - [Azure Kamu veri merkezi IP aralıklarına](https://www.microsoft.com/en-us/download/details.aspx?id=57063)ve tüm Usgov bölgeleri (Virginia, Texas, Arizlona ve ıOWA) ait https (443) bağlantı noktasının AAD, yedekleme, çoğaltma ve depolama URL 'lerini desteklemesini sağlar.  

#### <a name="run-setup"></a>Kurulumu çalıştırma
Yapılandırma sunucusunu yüklemek için Birleşik kurulumu yerel yönetici olarak çalıştırın. İşlem sunucusu ve ana hedef sunucu, yapılandırma sunucusunda varsayılan olarak da yüklenir.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]


## <a name="set-up-the-target-environment"></a>Hedef ortamı ayarlama

Hedef kaynaklarını seçin ve doğrulayın.

1. **Altyapı hedefini hazırla**' ya tıklayın  >  ve kullanmak istediğiniz Azure aboneliğini seçin.
2. Hedef dağıtım modelini belirtin.
3. Site Recovery, bir veya birden çok uyumlu Azure depolama hesabınızın ve ağınızın olup olmadığını denetler.

   ![Hedef ortamı ayarlama seçeneklerinin ekran görüntüsü.](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Çoğaltma ilkesi oluşturma

1. Yeni bir çoğaltma ilkesi oluşturmak için, **Site Recovery altyapı**  >  **çoğaltma ilkeleri**  >  **+ Çoğaltma İlkesi**' ne tıklayın.
2. **Çoğaltma ilkesi oluştur** bölümünde bir ilke adı belirtin.
3. **RPO eşiği** bölümünde kurtarma noktası hedefi (RPO) sınırını belirtin. Bu değer, veri kurtarma noktalarının ne sıklıkta oluşturulacağını belirtir. Devamlı çoğaltma bu sınırı aşarsa bir uyarı oluşturulur.
4. **Kurtarma noktası bekletme** bölümünde, her kurtarma noktası için bekletme süresinin ne kadar olacağını (saat) belirtin. Çoğaltılan VM’ler bir aralıktaki herhangi bir noktaya kurtarılabilir. Premium depolama alanına çoğaltılan makineler için 24 saate, standart depolama için de 72 saate kadar bekletme desteklenir.
5. Uygulamayla **tutarlı anlık görüntü sıklığı**' nda, uygulamayla tutarlı anlık görüntüler içeren kurtarma noktalarının ne sıklıkta oluşturulacağını (dakika cinsinden) belirtin. İlkeyi oluşturmak için **Tamam**’a tıklayın.

    ![Çoğaltma ilkesi oluşturma seçeneklerinin ekran görüntüsü.](./media/physical-azure-disaster-recovery/replication-policy.png)


İlke, yapılandırma sunucusu ile otomatik olarak ilişkilendirilir. Eşleşen bir ilke, varsayılan olarak yeniden çalışma için otomatik oluşturulur. Örneğin, çoğaltma ilkesi **rep-Policy** ise, bir yeniden çalışma ilkesi olan **rep-Policy-yeniden çalışma** oluşturulur. Bu ilke Azure’dan bir yeniden çalışma başlatılana kadar kullanılmaz.

## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

Her sunucu için çoğaltmayı etkinleştirin.

- Site Recovery, çoğaltma etkinleştirildiğinde Mobility hizmetini yükleyecek.
- Bir sunucu için çoğaltmayı etkinleştirdiğinizde, değişikliklerin etkili olması 15 dakika veya daha uzun sürebilir ve portalda görüntülenir.

1. **Uygulama kaynağını Çoğalt**' a tıklayın  >  .
2. **Kaynak** bölümünde yapılandırma sunucusunu seçin.
3. **Makine türü**' nde **fiziksel makineler**' i seçin.
4. İşlem sunucusunu (yapılandırma sunucusu) seçin. Daha sonra, **Tamam**'a tıklayın.
5. **Hedef** bölümünde, yük devretmeden sonra Azure VM 'leri oluşturmak istediğiniz aboneliği ve kaynak grubunu seçin. Azure 'da kullanmak istediğiniz dağıtım modelini (klasik veya kaynak yönetimi) seçin.
6. Verileri çoğaltmak için kullanmak istediğiniz Azure depolama hesabını seçin. 
7. Yük devretme sonrasında oluşturulan Azure VM'lerinin bağlanacağı Azure ağını ve alt ağını seçin.
8. Koruma için seçtiğiniz tüm makinelere ağ ayarını uygulamak için **Seçili makineler için Şimdi Yapılandır**' ı seçin. Makineler için Azure ağını ayrı ayrı seçmek için **Daha sonra yapılandır**'ı seçin. 
9. **Fiziksel makinelerde** ve **+ fiziksel makine**' ye tıklayın. Adı ve IP adresini belirtin. Çoğaltmak istediğiniz makinenin işletim sistemini seçin. Sunucuların bulunması ve listelenmesi birkaç dakika sürer. 
10. **Özellikler**  >  **yapılandırma özellikleri**' nde, Mobility hizmetini makineye otomatik olarak yüklemek için işlem sunucusu tarafından kullanılacak hesabı seçin.
11. **Çoğaltma ayarları**  >  **çoğaltma ayarlarını yapılandır** bölümünde doğru çoğaltma ilkesinin seçildiğini doğrulayın. 
12. **Çoğaltmayı Etkinleştir**’e tıklayın. **Ayarlar** > **İşler** > **Site Recovery İşleri** bölümünden **Korumayı Etkinleştir** işinin ilerleme durumunu izleyebilirsiniz. **Korumayı Sonlandır** işi çalıştırıldıktan sonra makine yük devretme için hazırdır.


Eklediğiniz sunucuları izlemek için, **yapılandırma sunucuları**  >  **son iletişim** kutusunda bu süre için son keşfedilen zamanı kontrol edebilirsiniz. Zamanlanan bulma süresini beklemeden makineler eklemek için yapılandırma sunucusunu vurgulayın (tıklamayın) ve **Yenile**' ye tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

[Olağanüstü durum kurtarma ayrıntısı çalıştırın](tutorial-dr-drill-azure.md).
