---
title: Azure Site Recovery 'de Hyper-V olağanüstü durum kurtarma mimarisi
description: Bu makalede, şirket içi Hyper-V VM 'Leri (VMM olmadan) Azure Site Recovery hizmetiyle Azure 'a olağanüstü durum kurtarma dağıtımı sırasında kullanılan bileşenlere ve mimariye ilişkin bir genel bakış sunulmaktadır.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 022d6edad1e907173dfde3481e60d2523be087a1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082660"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Hyper-V-Azure olağanüstü durum kurtarma mimarisi


Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanarak şirket içi Hyper-v konakları ve Azure arasında Hyper-v sanal makinelerini (VM) çoğalttığınızda, yük devretmek ve kurtardığınızda kullanılan mimari ve süreçler açıklanmaktadır.

Hyper-V konakları, isteğe bağlı olarak System Center Virtual Machine Manager (VMM) özel bulutlarında yönetilebilir.



## <a name="architectural-components---hyper-v-without-vmm"></a>Mimari bileşenler-VMM olmadan Hyper-V

Aşağıdaki tablo ve grafik, Hyper-V konakları VMM tarafından yönetilmediğinde Azure 'a Hyper-V çoğaltması için kullanılan bileşenlerin üst düzey bir görünümünü sağlar.

**Bileşen** | **Gereksinim** | **Ayrıntılar**
--- | --- | ---
**Azure** | Bir Azure aboneliği, Azure depolama hesabı ve Azure ağı. | Şirket içi VM iş yüklerinden çoğaltılan veriler depolama hesabında depolanır. Azure VM 'Ler, şirket içi sitenizden yük devretme gerçekleştiğinde çoğaltılan iş yükü verileriyle oluşturulur.<br/><br/> Azure VM’leri oluşturulduğunda Azure sanal ağına bağlanır.
**Hyper-V** | Site Recovery dağıtımı sırasında Hyper-v konakları ve kümeleri Hyper-V sitelerine toplamanız gerekir. Azure Site Recovery sağlayıcısı ve kurtarma hizmetleri aracısını her bir tek başına Hyper-V konağına veya her Hyper-V küme düğümüne yüklersiniz. | Sağlayıcı, İnternet üzerinden Site Recovery hizmetiyle gerçekleştirilen çoğaltma işlemini düzenler ve yönetir. Kurtarma Hizmetleri aracısı, veri çoğaltma işlemini gerçekleştirir.<br/><br/> Sağlayıcı ve aracı arasındaki iletişimler şifrelenir ve güvence altına alınır. Azure depolama alanında çoğaltılan veriler de şifrelenir.
**Hyper-V VM’leri** | Hyper-V üzerinde çalışan bir veya daha fazla VM. | VM 'lerde açık bir şekilde hiçbir şeyin yüklü olması gerekir.


**Hyper-V ' d e Azure mimarisine (VMM olmadan)**

![Mimari](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Mimari bileşenler-VMM ile Hyper-V

Aşağıdaki tablo ve grafik, Hyper-V konakları VMM bulutlarında yönetilmiyorsa Azure 'a Hyper-V çoğaltması için kullanılan bileşenlerin üst düzey bir görünümünü sağlar.

**Bileşen** | **Gereksinim** | **Ayrıntılar**
--- | --- | ---
**Azure** | Bir Azure aboneliği, Azure depolama hesabı ve Azure ağı. | Şirket içi VM iş yüklerinden çoğaltılan veriler depolama hesabında depolanır. Şirket içi sitenizden yük devretme gerçekleştiğinde, çoğaltılan verilerle Azure VM 'Leri oluşturulur.<br/><br/> Azure VM’leri oluşturulduğunda Azure sanal ağına bağlanır.
**VMM sunucusu** | VMM sunucusu, Hyper-V konakları içeren bir veya daha fazla bulut içerir. | Site Recovery sağlayıcıyı VMM sunucusuna yükleyerek, Site Recovery çoğaltmayı düzenleyebilir ve sunucuyu kurtarma hizmetleri kasasına kaydedebilirsiniz.
**Hyper-V konağı** | VMM tarafından yönetilen bir veya daha fazla Hyper-V konağı/kümesi. |  Kurtarma Hizmetleri aracısını her Hyper-V konağına veya küme düğümüne yüklersiniz.
**Hyper-V VM’leri** | Hyper-V konağı sunucusunda çalışan bir veya daha fazla VM. | VM'lere açıkça bir şey yüklenmesi gerekmez.
**Ağ** | VMM sunucusunda ayarlanmış mantıksal ağlar ve VM ağları. VM ağı, bulutla ilişkili bir mantıksal ağa bağlanmalıdır. | VM ağları Azure sanal ağlarına eşlenir. Yük devretmeden sonra Azure VM 'Leri oluşturulduğunda, VM ağıyla eşlenmiş Azure ağına eklenir.

**Hyper-V ' d e Azure mimarisine (VMM ile)**

![Bileşenler](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Çoğaltma işlemi

![Hyper-V-Azure arası çoğaltma](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Çoğaltma ve kurtarma işlemi**


### <a name="enable-protection"></a>Korumayı etkinleştir

1. Azure portalında veya şirket içinde bir Hyper-V VM’si için koruma etkinleştirdikten sonra, **Korumayı etkinleştir** başlatılır.
2. İş, makinenin önkoşullarla uyumlu olup olmadığını denetler, ardından, çoğaltmayı daha önce yapılandırdığınız ayarları uygulamak üzere [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) çağırır.
3. İş, tam bir VM çoğaltması başlatmak için [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) yöntemini çağırarak ilk çoğaltmayı başlatır ve VM’lerin sanal disklerini Azure’a gönderir.
4. İşi **işler** sekmesinde izleyebilirsiniz.      ![Işleri listesi](media/hyper-v-azure-architecture/image1.png) korumayı etkinleştirmek ![](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>İlk veri çoğaltma

1. İlk çoğaltma tetiklendiğinde, bir [Hyper-V VM anlık](https://technet.microsoft.com/library/dd560637.aspx) görüntüsü alınır.
2. VM 'deki sanal sabit diskler, hepsi Azure 'a kopyalanana kadar tek tek çoğaltılır. Bu işlem, VM boyutuna ve ağ bant genişliğine bağlı olarak biraz zaman alabilir. Ağ bant genişliğini artırma [hakkında bilgi edinin](https://support.microsoft.com/kb/3056159) .
3. İlk çoğaltma devam ederken disk değişiklikleri oluşursa, Hyper-V çoğaltma çoğaltma Izleyicisi değişiklikleri Hyper-V çoğaltma günlükleri (. HRL) olarak izler. Bu günlük dosyaları, disklerle aynı klasörde bulunur. Her diskin, ikincil depolamaya gönderilen ilişkili bir. HRL dosyası vardır. İlk çoğaltma sırasında anlık görüntü ve günlük dosyaları disk kaynaklarını kullanır.
4. İlk çoğaltma tamamlandığında, VM anlık görüntüsü silinir.
5. Günlükteki değişim disk değişiklikleri eşitlenir ve üst diske birleştirilir.


### <a name="finalize-protection-process"></a>Koruma işlemini Sonlandır

1. İlk çoğaltma tamamlandıktan sonra, **sanal makine işinin korumasını Sonlandır** işi çalışır. Ağ ve diğer çoğaltma sonrası ayarlarını yapılandırır, böylece VM korunur.
2. Bu aşamada, yük devretmeye uygun olduğundan emin olmak için VM ayarlarını kontrol edebilirsiniz. VM için bir olağanüstü durum kurtarma detayına (yük devretme testi), beklendiği gibi yük devretmesinin başarısız olduğunu kontrol etmek için çalıştırabilirsiniz. 


## <a name="delta-replication"></a>Değişim çoğaltması

1. İlk çoğaltmadan sonra Delta çoğaltma, çoğaltma ilkesine uygun olarak başlar.
2. Hyper-V çoğaltma çoğaltması Izleyici bir sanal sabit diskte. HRL dosyaları olarak yapılan değişiklikleri izler. Çoğaltma için yapılandırılmış her diskin ilişkili bir .hrl dosyası vardır.
3. Günlük, müşterinin depolama hesabına gönderilir. Bir günlük Azure 'a geçişte, birincil diskteki değişiklikler aynı klasördeki başka bir günlük dosyasında izlenir.
4. İlk ve değişim çoğaltması sırasında, Azure portal VM 'yi izleyebilirsiniz.

### <a name="resynchronization-process"></a>Yeniden eşitleme işlemi

1. Değişim çoğaltması başarısız olursa ve bant genişliği ile zaman açısından tam çoğaltma maliyetli olacaksa, bir VM yeniden eşitleme için işaretlenir.
    - Örneğin, .hrl dosyası disk boyutunun %50'sine ulaşırsa VM, yeniden eşitleme için işaretlenir.
    -  Varsayılan olarak yeniden eşitleme, ofis saatleri dışında otomatik olarak çalışacak şekilde zamanlanır.
1.  Yeniden eşitleme yalnızca Delta verilerini gönderir.
    - Kaynak ve hedef VM 'lerin sağlama toplamı hesaplanırken gönderilen veri miktarını en aza indirir.
    - Kaynak ve hedef dosyaların sabit parçalara bölünmesi durumunda sabit bir öbek oluşturma algoritması kullanır.
    - Her bir öbek için sağlama toplamı oluşturulur. Bunlar, kaynağın hedefe hangi blokların uygulanacağını belirleme ile karşılaştırılır.
2. Yeniden eşitleme sona erdikten sonra, normal değişim çoğaltması devam edecektir.
3. Saatlerin dışında varsayılan yeniden eşitleme beklemek istemiyorsanız, bir VM 'yi el ile yeniden eşitleyebilirsiniz. Örneğin, bir kesinti oluşursa. Bunu yapmak için, Azure portal yeniden **eşitle**> VM 'yi seçin.

    ![El ile yeniden eşitleme](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>İşlemi yeniden dene

Bir çoğaltma hatası meydana gelirse, yerleşik yeniden deneme işlevi vardır. Yeniden deneme, tabloda açıklandığı şekilde sınıflandırıldı.

**Kategori** | **Ayrıntılar**
--- | ---
**Kurtarılamaz hatalar** | Yeniden deneme yapılmaya çalışılmaz. VM durumu **Kritik** olacaktır ve yönetici müdahalesi gerekir.<br/><br/> Bu hatalara örnek olarak bozuk bir VHD zinciri, çoğaltma sanal makinesi için geçersiz bir durum, ağ kimlik doğrulama hataları, yetkilendirme hataları ve VM bulunamadı hataları (tek başına Hyper-V sunucuları için) sayılabilir.
**Kurtarılabilir hatalar** | Yeniden deneme aralığını ilk denemenin başlangıcına göre 1, 2, 4, 8 ve 10 dakika artıran bir üstel geri çekme kullanılarak her çoğaltma aralığında yeniden denemeler gerçekleşir. Hata devam ederse, 30 dakikada bir yeniden deneyin. Bunlara örnek olarak ağ hataları, yetersiz disk hataları ve düşük bellek koşulları dahildir.



## <a name="failover-and-failback-process"></a>Yük devretme ve yeniden çalışma işlemi

1. Şirket içi Hyper-V VM 'lerinden Azure 'a planlı veya planlanmamış bir yük devretme işlemi çalıştırabilirsiniz. Planlı bir yük devretme çalıştırırsanız, veri kaybı olmaması için kaynak VM’ler kapatılır. Birincil siteniz erişilebilir değilse planlanmamış bir yük devretme çalıştırın.
2. Birden fazla makinenin yükünü yönetmek için tek bir makinenin yükünü devreder veya kurtarma planları oluşturabilirsiniz.
3. Yük devretme çalıştırırsınız. Yük devretme işlemi tamamlandıktan sonra, oluşturulan çoğaltma sanal makinelerini Azure 'da görmeniz gerekir. Gerekli olursa VM’ye genel bir IP adresi atayabilirsiniz.
4. Daha sonra yük devretmeyi, çoğaltma Azure VM 'sinden iş yüküne erişmeye başlamak için yürütün.

Şirket içi altyapınız tekrar çalışır duruma geçtikten sonra yeniden çalıştırabilirsiniz. Yeniden çalışma üç aşamada gerçekleşir:

1. Azure 'dan şirket içi siteye planlanmış bir yük devretme başlatın:
    - **Kapalı kalma süresini en aza indir**: Bu Site Recovery seçeneği kullanırsanız, yük devretme işleminden önce verileri eşitler. Değiştirilen veri bloklarını denetler ve Azure VM çalışmaya devam ederken, kapalı kalma süresini en aza indirirken bunları şirket içi siteye indirir. Yük devretmenin tamamlanması gerektiğini el ile belirttiğinizde, Azure VM kapanır, son Delta değişikliği kopyalanır ve yük devretme başlatılır.
    - **Tam indirme**: Bu seçenek verileri, yük devretme sırasında eşitlenir. Bu seçenek tüm diski indirir. Bir sağlama toplamı hesaplanmadığından daha hızlıdır, ancak daha fazla kapalı kalma süresi vardır. Çoğaltma Azure VM 'lerini bir süre çalıştırıyorsanız veya şirket içi VM silinmişse bu seçeneği kullanın.
    - **VM oluşturma**: aynı VM 'ye veya ALTERNATIF bir VM 'ye yeniden yük devredeseçebilirsiniz. Zaten mevcut değilse Site Recovery VM 'nin oluşturulmasını belirtebilirsiniz.

2. İlk eşitleme bittikten sonra, yük devretmeyi tamamlamayı seçersiniz. Tamamlandıktan sonra, her şeyin beklendiği gibi çalıştığını denetlemek için şirket içi VM 'de oturum açabilirsiniz. Azure portal, Azure VM 'lerinin durdurulmuş olduğunu görebilirsiniz.
3.  Sonra, tamamlamak için yük devretmeyi işleyin ve şirket içi VM 'den iş yüküne yeniden erişmeye başlayabilirsiniz.
4. İş yükleri yeniden başlatıldıktan sonra, şirket içi VM 'Lerin tekrar Azure 'a çoğaltılması için çoğaltmayı tersine çevirmeyi etkinleştirmeniz gerekir.



## <a name="next-steps"></a>Sonraki adımlar


Hyper-V ' d e Azure Çoğaltmaya başlamak için [Bu öğreticiyi](tutorial-prepare-azure.md) izleyin.


