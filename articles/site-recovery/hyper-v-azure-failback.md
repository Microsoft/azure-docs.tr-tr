---
title: Azure 'dan Hyper-v VM 'lerini Azure Site Recovery ile yeniden çalıştırma
description: Azure Site Recovery hizmetiyle Azure 'a olağanüstü durum kurtarma sırasında Hyper-V sanal makinelerini şirket içi bir siteye nasıl geri yükleyeceğinizi öğrenin.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: b924c1424a309fb61f690c21e5665a70356c7a62
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084223"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Hyper-V VM 'Leri için yeniden çalışma çalıştırma

Bu makalede, Site Recovery tarafından korunan Hyper-V sanal makinelerinin nasıl başarısız olduğu açıklanır.

## <a name="prerequisites"></a>Ön koşullar

- [Farklı yeniden çalışma türleri](concepts-types-of-failback.md) ve ilgili uyarılar hakkındaki ayrıntıları okuduğunuzdan emin olun.
- Birincil site VMM sunucusunun veya Hyper-V ana bilgisayar sunucusunun Azure 'a bağlı olduğundan emin olun.
- Sanal makinede **yürütmeyi** gerçekleştirmelisiniz.
- Çoğaltma için bir depolama hesabı kullandığınızdan ve yönetilmeyen disklere sahip olduğunuzdan emin olun. Diskleri yönetme kullanılarak çoğaltılan Hyper-V VM 'lerinin yeniden çalışması desteklenmez.

## <a name="perform-failback"></a>Yeniden çalışma gerçekleştir
Birincil sunucudan ikincil konuma yük devretmeden sonra, çoğaltılan sanal makineler Site Recovery tarafından korunmaz ve ikincil konum artık etkin konum olarak davranır. Bir kurtarma planında VM 'Leri yeniden devretmek için, ikincil siteden birincil bir yük devretmeyi aşağıdaki gibi çalıştırın. 
1. *Recoveryplan_name* > **Kurtarma planlarını** seçin. **Planlı Yük devretme** > **Yük devretme** ' ya tıklayın.
2. **Planlı Yük devretmeyi Onayla** sayfasında, kaynak ve hedef konumları seçin. Yük devretme yönünü aklınızda edin. Birincil konumdaki yük devretme beklenen şekilde çalışmışsa ve tüm sanal makineler ikincil konumsa bu yalnızca bilgi amaçlıdır.
3. Azure 'dan geri yük devretmek için **veri eşitleme**' de ayarları seçin:
    - **Yük devretme işleminden önce verileri eşitleme (yalnızca Delta değişikliklerini eşitleme)** — Bu seçenek, sanal makinelerin kapanmadan eşitlendiği kapalı kalma süresini en aza indirir. Aşağıdaki adımları yapar:
        - 1\. Aşama: Azure 'da sanal makinenin anlık görüntüsünü alır ve şirket içi Hyper-V konağına kopyalar. Makine Azure 'da çalışmaya devam ediyor.
        - 2\. Aşama: Azure 'da sanal makineyi kapatır, bu sayede hiçbir yeni değişiklik gerçekleşmez. Son Delta değişikliği kümesi şirket içi sunucuya aktarılır ve şirket içi sanal makine başlatılır.

    - **Yalnızca yük devretme sırasında (tam indirme) verileri eşitler**— Bu seçenek daha hızlıdır.
        - Diskin çoğunu değiştirdiğimiz ve sağlama toplamı hesaplamasında zaman harcamasını istediğimizden Bu seçenek daha hızlıdır. Diskin indirilmesini gerçekleştirir. Ayrıca, şirket içi sanal makine silindiğinde de yararlı olur.
        - Azure 'u bir süredir (bir ay veya daha fazla) çalıştırıyorsanız veya şirket içi sanal makine silinmişse bu seçeneği kullanmanızı öneririz. Bu seçenek, sağlama toplamı hesaplamaları gerçekleştirmez.


4. Bulut için veri şifreleme etkinse, **şifreleme anahtarı** ' nda, VMM sunucusunda sağlayıcı yüklemesi sırasında veri şifrelemeyi etkinleştirdiğinizde verilen sertifikayı seçin.
5. Yük devretmeyi başlatın. Yük devretme işleminin ilerleme durumunu **İşler** sekmesinden takip edebilirsiniz.
6. Verilerin yük devretmeden önce eşitlenmesi seçeneğini belirlediyseniz, ilk veri eşitlemesi tamamlandıktan sonra Azure 'daki sanal makineleri kapatmaya hazırsanız Işler > iş adı > **Yük devretmeyi tamamladıktan**sonra **işler** ' e tıklayın. Bu, Azure makinesini kapatır, en son değişiklikleri şirket içi sanal makineye aktarır ve şirket içi VM 'yi başlatır.
7. Artık beklendiği gibi kullanılabilir olduğunu doğrulamak için sanal makinede oturum açabilirsiniz.
8. Sanal makine, bir tamamlama bekleme durumunda. Yük devretmeyi yürütmek için **Yürüt** ' e tıklayın.
9. Yeniden çalışmayı gerçekleştirmek için, birincil sitede sanal makineyi korumaya başlamak üzere **çoğaltmayı tersine çevir** ' e tıklayın.


Özgün birincil siteye yeniden yük devretmek için bu yordamları izleyin. Bu yordamda, bir kurtarma planı için planlı yük devretmenin nasıl çalıştırılacağı açıklanmaktadır. Alternatif olarak, **sanal makineler** sekmesinde tek bir sanal makine için yük devretmeyi çalıştırabilirsiniz.


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Hyper-V ortamında alternatif bir konuma yeniden çalışma
Bir [Hyper-V sitesi Ile Azure](site-recovery-hyper-v-site-to-azure.md) arasında koruma dağıttıysanız, Azure 'dan alternatif bir şirket içi konuma yeniden çalışma yeteneği uygulamanız gerekir. Yeni şirket içi donanım ayarlamanız gerekiyorsa bu faydalıdır. Bunu yapabilirsiniz.

1. Yeni donanım ayarlıyorsanız, Windows Server 2012 R2 'yi ve sunucuda Hyper-V rolünü yükleyebilirsiniz.
2. Özgün sunucuda sahip olduğunuz adla aynı ada sahip bir sanal ağ anahtarı oluşturun.
3. **Korunan öğeler** -> **koruma grubu** -> \<protectiongroupname >-> \<virtualmachinename > yeniden devretmek ve **Planlı Yük devretme**seçeneğini belirleyin.
4. **Planlı Yük devretmeyi Onayla** bölümünde, **yoksa Şirket Içi sanal makine oluştur**' u seçin.
5. Ana bilgisayar adı ' nda, * * sanal makineyi yerleştirmek istediğiniz yeni Hyper-V ana bilgisayar sunucusunu seçin.
6. Veri eşitleme ' de, yük devretmeden önce verileri eşitleme seçeneğini seçmenizi öneririz. Bu, sanal makinelerin kapanmadan eşitlendiği kapalı kalma süresini en aza indirir. Şunları yapar:

    - 1\. Aşama: Azure 'da sanal makinenin anlık görüntüsünü alır ve şirket içi Hyper-V konağına kopyalar. Makine Azure 'da çalışmaya devam ediyor.
    - 2\. Aşama: Azure 'da sanal makineyi kapatır, bu sayede hiçbir yeni değişiklik gerçekleşmez. Son değişiklik kümesi şirket içi sunucuya aktarılır ve şirket içi sanal makine başlatılır.
    
7. Yük devretmeye (yeniden çalışma) başlamak için onay işaretine tıklayın.
8. İlk eşitleme bittikten ve Azure 'da sanal makineyi kapatmaya hazırsanız Işler > planlı yük devretme işi \<> > **Yük devretmeyi tamamladıktan**sonra **işler** ' e tıklayın. Bu, Azure makinesini kapatır, en son değişiklikleri şirket içi sanal makineye aktarır ve başlatır.
9. Her şeyin beklendiği gibi çalıştığını doğrulamak için şirket içi sanal makinede oturum açabilirsiniz. Ardından, yük devretmeyi tamamlamak için **Yürüt** ' e tıklayın. Yürüt, Azure sanal makinesini ve disklerini siler ve VM 'yi yeniden korunacak şekilde hazırlar.
10. Şirket içi sanal makineyi korumaya başlamak için **tersine Çoğalt** ' a tıklayın.

    > [!NOTE]
    > Veri eşitleme adımında yeniden çalışma işini iptal ederseniz şirket içi VM bozuk bir durumda olacaktır. Bunun nedeni, veri eşitlemesinin en son verileri Azure VM disklerinden şirket içi veri disklerine kopyalama ve eşitleme tamamlanana kadar disk verilerinin tutarlı bir durumda olmaması olabilir. Veri eşitleme iptal edildikten sonra şirket içi VM önyüklenir, önyükleme olmayabilir. Veri eşitlemesini tamamlamaya yönelik yük devretmeyi yeniden tetikle.


## <a name="why-is-there-no-button-called-failback"></a>Neden yeniden çalışma olarak adlandırılan hiçbir düğme yok?
Portalda, yeniden çalışma adlı açık bir hareket yoktur. Yeniden çalışma, birincil siteye geri dönebileceğiniz bir adımdır. Tanım olarak, yeniden çalışma, sanal makinelerin kurtarma işleminden birincili geri yük devretmesine yönelik olarak gerçekleştirilir.

Bir yük devretme başlattığınızda, dikey pencere, sanal makinelerin taşınacağı yönle ilgili olarak size bildirir. bu yön, Azure 'dan şirket içine, yeniden çalışır duruma gelir.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Yeniden çalışma için neden yalnızca planlanmış bir yük devretme hareketi var?
Azure, yüksek oranda kullanılabilir bir ortamdır ve sanal makineleriniz her zaman kullanılabilir. Yeniden çalışma, iş yüklerinin şirket içinde çalışmaya başlamasını sağlamak için küçük bir kesinti süresine karar verirken planlanmış bir etkinliktir. Bu, veri kaybı beklemiyor. Bu nedenle, Azure 'daki VM 'Leri kapatan yalnızca planlanmış bir yük devretme hareketi kullanılabilir, en son değişiklikleri indirir ve veri kaybı olmadığından emin olur.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Hyper-v ' y e yeniden çalışma için Azure 'da bir işlem sunucusu gerekir mi?
Hayır, yalnızca VMware sanal makinelerini koruduğunuzda bir işlem sunucusu gerekir. Hyper-v sanal makinelerinin koruması/yeniden çalışma sırasında dağıtılması gereken ek bir bileşen yoktur.


## <a name="time-taken-to-failback"></a>Yeniden çalışma için geçen süre
Veri eşitlemesini tamamlamaya yönelik geçen süre ve sanal makine önyüklemesi, çeşitli faktörlere bağlıdır. Geçen süre hakkında bir fikir vermek için veri eşitleme sırasında ne olduğunu açıkladık.

Veri eşitleme, sanal makine disklerinin anlık görüntüsünü alır ve blok tarafından blok denetimini başlatır ve sağlama toplamını hesaplar. Bu hesaplanan sağlama toplamı, aynı bloktaki şirket içi sağlama toplamıyla karşılaştırmak için şirket içine gönderilir. Sağlama toplamı eşleşiyorsa, veri bloğu aktarılmaz. Eşleşmiyorsa, veri bloğu şirket içine aktarılır. Bu aktarım süresi, kullanılabilir bant genişliğine bağlıdır. Sağlama hızının hızı, dakikada birkaç GB olur. 

Verilerin indirilmesini hızlandırmak için, MARS aracınızı, indirmeyi paralel hale getirmek için daha fazla iş parçacığı kullanacak şekilde yapılandırabilirsiniz. Aracıdaki indirme iş parçacıklarını değiştirme hakkında [Buradaki belgeye](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) başvurun.


## <a name="next-steps"></a>Sonraki Adımlar

**İşlemeden**sonra, *çoğaltmayı tersine çevirme*işlemini başlatabilirsiniz. Bu, sanal makinenin Şirket içinden Azure 'a korunmasını başlatır. Bu, VM 'yi yalnızca Azure 'da devre dışı bırakıldıklarından ve bu nedenle yalnızca fark değişikliklerini gönderdiğinden, değişiklikleri çoğaltacaktır.
