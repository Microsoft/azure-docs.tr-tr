---
title: Azure Site Recovery bir ikincil siteye Hyper-V olağanüstü durum kurtarma ayarlama
description: Azure Site Recovery ile şirket içi sitelerinizde bulunan Hyper-V VM'ler için olağanüstü durum kurtarmayı nasıl ayarlayacağınızı öğrenin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 8d89c7eda845f0e5ed9bc8ccc7b6b3812ba275f9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132998"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Hyper-V VM'leri için ikincil bir şirket içi siteye olağanüstü durum kurtarma ayarlama

[Azure Site Recovery](site-recovery-overview.md) hizmeti, şirket içi makinelerin ve Azure sanal makinelerinin çoğaltma, yük devretme ve geri döndürme işlemlerini yönetip düzenleyerek, olağanüstü durum kurtarma stratejinize katkıda bulunur.

Bu makalede System Center Virtual Machine Manager (VMM) bulutlarında yönetilen şirket içi Hyper-V VM'leri için ikincil siteye olağanüstü durum kurtarma ayarlama adımları gösterilmektedir. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Şirket içi VMM sunucularını ve Hyper-V konaklarını hazırlama
> * Site Recovery için Kurtarma Hizmetleri kasası oluşturma 
> * Kaynak ve hedef çoğaltma ortamlarını ayarlama. 
> * Ağ eşlemesini ayarlama 
> * Çoğaltma ilkesi oluşturma
> * Sanal makine için çoğaltmayı etkinleştirme


## <a name="prerequisites"></a>Önkoşullar

Bu senaryoyu tamamlamak için:

- [Senaryo mimarisini ve bileşenlerini](hyper-v-vmm-architecture.md) gözden geçirin.
- VMM sunucularının ve Hyper-V konaklarının [destek gereksinimlerine](hyper-v-vmm-secondary-support-matrix.md) uygun olduğundan emin olun.
- Çoğaltmak istediğiniz VM'lerin [çoğaltılan makine desteği](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support) için uygun olup olmadığını denetleyin.
- VMM sunucularını ağ eşlemesi için hazırlayın.

### <a name="prepare-for-network-mapping"></a>Ağ eşlemeye hazırlama

[Ağ eşlemesi](hyper-v-vmm-network-mapping.md), kaynak ve hedef bulutlardaki şirket içi VMM VM ağlarını eşler. Eşleme sürecinde şu işlemler gerçekleştirilir:

- VM'ler yük devretme sonrasında uygun hedef VM ağlara bağlanır. 
- Çoğaltma VM'lerini en uygun şekilde hedef Hyper-V konağı sunucularına yerleştirir. 
- Ağ eşlemesini yapılandırmazsanız, çoğaltma VM'leri yük devretme sonrasında herhangi bir VM’ye bağlanmaz.

VMM'yi şu şekilde hazırlayın:

1. Kaynak ve hedef VMM sunucularında [VMM mantıksal ağları](https://docs.microsoft.com/system-center/vmm/network-logical) bulunduğundan emin olun.
    - Kaynak sunucusundaki mantıksal ağ, Hyper-V konaklarının bulunduğu kaynak bulutla ilişkilendirilmelidir.
    - Hedef sunucudaki mantıksal ağ, hedef bulutla ilişkilendirilmelidir.
1. Kaynak ve hedef VMM sunucularında [VM ağları](https://docs.microsoft.com/system-center/vmm/network-virtual) bulunduğundan emin olun. VM ağları, her bir konumdaki mantıksal ağa bağlanmış olmalıdır.
2. Kaynak Hyper-V konaklarındaki VM'leri kaynak VM ağına bağlayın. 


## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Koruma hedefi seçme

Neleri çoğaltmak istediğinizi ve bunları nereye çoğaltacağınızı seçin.

1. **Site Recovery** > **1. Adım: Altyapıyı Hazırlama** > **Koruma hedefi** seçeneklerine tıklayın.
2. **Kurtarma sitesine**'yi ve **Evet, Hyper-V ile**'yi seçin.
3. Hyper-V konaklarını yönetmek için VMM kullandığınızı belirtmek üzere **Evet**'i seçin.
4. İkincil bir VMM sunucunuz varsa **Evet**'i seçin. Çoğaltmayı tek bir VMM sunucusu üzerindeki bulutlar arasında dağıtıyorsanız **Hayır**'ı seçin. Daha sonra, **Tamam**'a tıklayın.


## <a name="set-up-the-source-environment"></a>Kaynak ortamı ayarlama

Azure Site Recovery Sağlayıcısı'nı VMM sunucularına yükleyin ve kasadaki sunucuları keşfedip kaydedin.

1. **Altyapıyı Hazırlama** > **Kaynak** seçeneklerine tıklayın.
2. **Kaynağı ayarla** kısmında, bir VMM sunucusu eklemek için **+ VMM**'ye tıklayın.
3. **Sunucu Ekle** bölümünde **Sunucu türü** olarak **System Center VMM sunucusu** girişinin olduğundan emin olun.
4. Azure Site Recovery Sağlayıcısı yükleme dosyasını indirin.
5. Kayıt anahtarını indirin. Sağlayıcı'yı yüklediğinizde buna ihtiyacınız olacak. Anahtar, oluşturulduktan sonra beş gün boyunca geçerlidir.

    ![Kaynağı ayarlama](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Sağlayıcı'yı VMM sunucularına yükleyin. Hyper-V konaklarına yüklemeniz gereken herhangi bir şey yoktur.

### <a name="install-the-azure-site-recovery-provider"></a>Azure Site Recovery Sağlayıcısı'nı yükleme

1. Sağlayıcı kurulum dosyasını tüm VMM sunucularında çalıştırın. VMM bir kümeye dağıtılmışsa ilk kez yüklerken şu adımları izleyin:
    -  Sağlayıcı'yı etkin bir düğüme yükleyin ve yükleme işlemini tamamlayarak VMM sunucusunu kasaya kaydedin.
    - Ardından, Sağlayıcı'yı diğer düğümlere yükleyin. Tüm küme düğümlerinin aynı Sağlayıcı sürümüne sahip olması gerekir.
2. Kurulum birkaç önkoşul denetimi gerçekleştirir ve VMM hizmetini durdurmak için izin ister. Kurulum bittiğinde VMM hizmeti otomatik olarak yeniden başlatılır. Yükleme işlemini bir VMM kümesinde gerçekleştiriyorsanız Küme rolünü durdurmanız istenir.
3. **Microsoft Update** sayfasından güncelleştirmeleri seçebilirsiniz. Böylece, Sağlayıcı güncelleştirmeleri Microsoft Update ilkenize uygun şekilde yüklenir.
4. **Yükleme** bölümünde varsayılan yükleme konumunu kabul edin veya değiştirin ve **Yükle**'ye tıklayın.
5. Yükleme tamamlandıktan sonra sunucuyu kasaya kaydetmek için **Kaydet**'e tıklayın.

    ![Yükleme konumu](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. **Kasa adı** alanında, sunucunun kayıtlı olduğu kasanın adını doğrulayın. **İleri**’ye tıklayın.
7. **Ara Sunucu Bağlantısı** bölümünde VMM sunucusunda çalışan Sağlayıcı'nın Azure'a nasıl bağlanacağını belirleyin.
   - Sağlayıcıyı doğrudan veya bir ara sunucu üzerinden internete bağlanacak şekilde ayarlayabilirsiniz. Ara sunucu ayarlarını gereken şekilde yapın.
   - Bir ara sunucu kullanıyorsanız belirtilen ara sunucu kimlik bilgileriyle otomatik olarak bir VMM Farklı Çalıştır hesabı (DRAProxyAccount) oluşturulur. Bu hesabın kimlik doğrulamasını başarıyla gerçekleştirebilmesi için ara sunucuyu yapılandırın. RunAs hesabı ayarları VMM konsolunun **Ayarlar** > **Güvenlik** > **Farklı Çalıştır Hesapları** sayfasından değiştirilebilir.
   - Değişiklikleri güncelleştirmek için VMM hizmetini yeniden başlatın.
8. **Kayıt Anahtarı** kısmında indirdiğiniz ve VMM sunucusuna kopyaladığınız anahtarı seçin.
9. Şifreleme ayarı, bu senaryo için geçerli değildir. 
10. **Sunucu adı** alanında, kasadaki VMM sunucusunu tanımlamak için bir kolay ad belirtin. Bir kümede VMM küme rolünün adını belirtin.
11. **Bulut meta verilerini eşitle** bölümünde VMM sunucusundaki tüm bulutlar için meta verileri eşitlemek isteyip istemediğinizi seçin. Bu eylemin her sunucuda yalnızca bir kez gerçekleştirilmesi gerekir. Tüm bulutları eşitlemek istemiyorsanız bu ayarı işaretlemeden geçebilirsiniz. VMM konsolunun bulut özellikleri sayfasından her bulutu ayrı bir şekilde eşitleyebilirsiniz.
12. İşlemi tamamlamak için **İleri**'ye tıklayın. Kayıttan sonra Site Recovery, VMM sunucusundan meta verileri alır. Sunucu kasanın **Sunucular** > **VMM Sunucuları** sayfasında görüntülenir.
13. Sunucu kasada göründükten sonra **Kaynak** > **Kaynağı hazırla** sayfasından VMM sunucusunu ve ardından Hyper-V konağının bulunduğu bulutu seçin. Daha sonra, **Tamam**'a tıklayın.


## <a name="set-up-the-target-environment"></a>Hedef ortamı ayarlama

Hedef VMM sunucusunu ve bulutu seçin:

1. **Altyapıyı hazırla** > **Hedef**'e tıklayın ve hedef VMM sunucusunu seçin.
2. Site Recovery ile eşitlenmiş olana VMM bulutları görüntülenir. Hedef bulutu seçin.

   ![Hedef](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Çoğaltma ilkesi ayarlama

Başlamadan önce ilkeyi kullanan tüm konakların aynı işletim sistemine sahip olduğundan emin olun. Konaklar farklı Windows Server sürümlerini çalıştırıyorsa birden fazla çoğaltma ilkesi oluşturmanız gerekir.

1. Yeni bir çoğaltma ilkesi oluşturmak için **Altyapıyı hazırlama** > **Çoğaltma Ayarları** >  **+Oluştur ve ilişkilendir** seçeneklerine tıklayın.
2. **İlke oluştur ve ilişkilendir** bölümünde bir ilke adı belirtin. Kaynak ve hedef türü **Hyper-V** olmalıdır.
3. **Hyper-V konak sürümü** alanında konakta çalışan işletim sistemini seçin.
4. **Kimlik doğrulaması türü** ve **Kimlik doğrulaması bağlantı noktası** alanlarında birincil ve kurtarma amaçlı Hyper-V konağı sunucuları arasındaki trafiğe nasıl kimlik doğrulaması uygulanacağını belirtin.
    - Çalışana bir Kerberos ortamınız yoksa **Sertifika**'yı seçin. Azure Site Recovery otomatik olarak HTTPS kimlik doğrulaması sertifikaları yapılandırır. El ile herhangi bir işlem yapmanız gerekmez.
    - Varsayılan olarak Hyper-V konağı sunucularının Windows Güvenlik Duvarı'nda 8083 ve 8084 numaralı bağlantı noktaları (sertifikalar için) açılır.
    - **Kerberos**'u seçerseniz konak sunucuların karşılıklı kimlik doğrulama gerçekleştirmesi için bir Kerberos bileti kullanılır. Kerberos yalnızca Windows Server 2012 R2 veya üzeri çalıştıran Hyper-V konak sunucuları için geçerlidir.
1. **Kopyalama sıklığı** kısmında, ilk çoğaltmadan sonra değişim verilerini ne sıklıkta çoğaltacağınızı belirleyin (30 saniyede, 5 veya 15 dakikada bir).
2. **Kurtarma noktası bekletme** bölümünde, her kurtarma noktası için bekletme süresinin ne kadar olacağını (saat) belirtin. Çoğaltılan makineler, bu süre içindeki herhangi bir noktaya kurtarılabilir.
3. **Uygulamayla tutarlı anlık görüntü sıklığı** kısmında, uygulamayla tutarlı anlık görüntüleri içeren kurtarma noktasının hangi sıklıkta oluşturulacağını (1-12 saat) belirtin. Hyper-V iki tür anlık görüntü kullanır:
    - **Standart anlık görüntü**: Sanal makinenin tamamı için artımlı anlık görüntü sunar.
    - **Uygulamayla tutarlı anlık görüntü**: VM’nin içindeki uygulama verilerinin belirli bir noktadaki anlık görüntüsünü alır. Birim Gölge Kopyası Hizmeti (VSS), anlık görüntü alınırken uygulamanın tutarlı bir durumda olmasını sağlar. Uygulamayla tutarlı anlık görüntüleri etkinleştirmek, kaynak VM'lerin uygulama performansını etkiler. Yapılandırdığınız ilave kurtarma noktası sayısından daha küçük bir değer belirleyin.
4. **Veri aktarımı sıkıştırma** bölümünde aktarılan çoğaltma verilerinin sıkıştırılıp sıkıştırılmayacağını belirtin.
5. Kaynak VM için korumayı devre dışı bırakırsanız çoğaltma sanal makinesinin silinmesini istiyorsanız **Çoğaltma VM'ini sil**'i seçin. Bu ayarı etkinleştirirseniz kaynak VM için korumayı devre dışı bıraktığınızda VM Site Recovery konsolundan kaldırılır, VMM Site Recovery ayarları VMM konsolundan kaldırılır ve çoğaltma silinir.
6. **İlk çoğaltma yöntemi** alanında ağ üzerinden çoğaltma gerçekleştiriyorsanız ilk çoğaltma işleminin başlatılmasını seçin veya bir zamanlama belirleyin. Ağ bant genişliğini tüketmemek için kullanımın az olduğu saatlere göre zamanlamak isteyebilirsiniz. Daha sonra, **Tamam**'a tıklayın.

     ![Çoğaltma ilkesi](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Yeni ilke otomatik olarak VMM bulutu ile ilişkilendirilir. **Çoğaltma ilkesi** bölümünde **Tamam**'a tıklayın. 


## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

1. **Uygulama çoğaltma** > **Kaynak** seçeneğine tıklayın. 
2. **Kaynak** bölümünde VMM sunucusunu ve çoğaltmak istediğiniz Hyper-V konaklarının bulunduğu bulutu seçin. Daha sonra, **Tamam**'a tıklayın.
3. **Hedef** bölümünde ikincil VMM sunucusunu ve bulutu doğrulayın.
4. **Sanal makineler** bölümündeki listeden korumak istediğiniz VM'leri seçin.


**İşler**Site Recovery işleri >  bölümünde **Korumayı Etkinleştir** eyleminin ilerleme durumunu izleyebilirsiniz. **Korumayı Sonlandır** işi tamamlandıktan sonra ilk çoğaltma tamamlanır ve VM yük devretme için hazır olur.

## <a name="next-steps"></a>Sonraki adımlar

[Olağanüstü durum kurtarma tatbikatı çalıştırma](hyper-v-vmm-test-failover.md)
