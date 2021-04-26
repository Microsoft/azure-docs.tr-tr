---
title: Azure 'dan Hyper-V VM 'lerini Azure Site Recovery ile yeniden çalıştırma
description: Azure Site Recovery ile Hyper-V VM 'lerini Azure 'dan şirket içi bir siteye nasıl geri devretmek.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: ramamill
ms.openlocfilehash: 96c3dce17ab78e08b28bb41c0100e51a72a666e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110262"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Hyper-V VM’leri için yeniden çalışma işlemi çalıştırma

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)ile şirket Içi siteden Azure 'a Hyper-V VM 'lerinden yük devretme yapıldıktan sonra oluşturulan Azure VM 'lerinin nasıl başarısız olduğu açıklanır.

- Azure 'dan şirket içi siteye planlanmış bir yük devretme çalıştırarak Azure 'dan Hyper-V VM 'lerini geri alabilirsiniz. Yük devretme yönü Azure 'dan şirket içine ise, yeniden çalışma olarak kabul edilir.
- Azure yüksek oranda kullanılabilir bir ortam olduğundan ve VM 'Ler her zaman kullanılabilir olduğundan, Azure 'dan yeniden çalışma planlanmış bir etkinliktir. İş yüklerinin şirket içinde yeniden çalışmaya başlayabilmesi için küçük bir kesinti süresi planlayabilir. 
- Planlı yeniden çalışma, Azure 'daki VM 'Leri kapatır ve en son değişiklikleri indirir. Veri kaybı beklenmez.

## <a name="before-you-start"></a>Başlamadan önce

1. Yeniden [çalışma türlerini gözden geçirin](failover-failback-overview.md#hyper-v-reprotectionfailback) -özgün konum kurtarma ve alternatif konum kurtarma kullanabilirsiniz.
2. Azure VM 'lerinin yönetilen diskler olmadığından, depolama hesabı kullandığından emin olun. Yönetilen diskleri kullanarak Azure makinelerine yük devretmekte olan Hyper-V sanal makinelerinin yeniden çalışması desteklenmez.
3. Şirket içi Hyper-V konağının (veya Site Recovery ile kullanıyorsanız System Center VMM sunucusunun) çalışıp çalışmadığını ve Azure 'a bağlandığını kontrol edin. 
4. VM 'Ler için yük devretme ve tamamlama işleminin tamam olduğundan emin olun. Azure 'dan Hyper-V VM 'lerinin yeniden çalışma için belirli bir Site Recovery bileşeni ayarlamanız gerekmez.
5. Veri eşitlemesini tamamlaması ve şirket içi VM 'yi başlatmak için gereken süre, bir dizi etkene bağlı olacaktır. Veri indirmeyi hızlandırmak için, Microsoft Kurtarma Hizmetleri aracısını indirmeyi paralel hale getirmek için daha fazla iş parçacığı kullanacak şekilde yapılandırabilirsiniz. [Daha fazla bilgi edinin](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Özgün konuma geri dönme

Azure 'da Hyper-V VM 'lerini ilk şirket içi VM 'ye geri yüklemek için Azure 'dan şirket içi siteye planlı bir yük devretme çalıştırın:

1. Kasada **çoğaltılan öğeleri**> VM 'yi seçin. **Planlı Yük devretme**> VM 'ye sağ tıklayın. Kurtarma planını geri alıyorsa, plan adını seçin ve   >  **Planlı Yük** devretme yük devretmesi ' ne tıklayın.
2. **Planlı Yük devretmeyi Onayla** bölümünde kaynak ve hedef konumları seçin. Yük devretme yönünü aklınızda edin. Birincil sunucudan yük devretme beklenen şekilde çalıştıysa ve tüm sanal makineler ikincil konumdaysa bu yalnızca bilgi amaçlıdır.
3. **Veri eşitleme**' de bir seçenek belirleyin:
    - **Yük devretmeden önce verileri eşitleme (yalnızca Delta değişikliklerini eşitleme)**— Bu seçenek, kapatmadan eşitlendiğinde VM 'ler için kapalı kalma süresini en aza indirir.
        - **1. aşama**: Azure VM 'nin bir anlık görüntüsünü alır ve şirket içi Hyper-V konağına kopyalar. Makine Azure 'da çalışmaya devam ediyor.
        - **2. aşama**: hiçbir yeni değişiklik gerçekleşmemesi için Azure VM 'yi kapatır. Son Delta değişikliği kümesi şirket içi sunucuya aktarılır ve şirket içi VM başlatılır.
    - **Yalnızca yük devretme sırasında (tam indirme) veri eşitlemesi**— Bu seçenek daha hızlıdır çünkü diskin büyük bir kısmının değiştiğini ve sağlama toplamlarını hesaplama zamanının harcanmasını istemezsiniz. Bu seçenek, sağlama toplamı hesaplamaları gerçekleştirmez.
        - Diskin indirilmesini gerçekleştirir. 
        - Azure 'u bir süredir (bir ay veya daha fazla) çalıştırıyorsanız veya şirket içi VM silinirse bu seçeneği kullanmanız önerilir.

4. Yalnızca VMM için, veri şifrelemesi bulut için etkinse, **şifreleme anahtarı**' nda, VMM sunucusunda sağlayıcı yüklemesi sırasında veri şifrelemeyi etkinleştirdiğinizde verilen sertifikayı seçin.
5. Yük devretmeyi başlatın. Yük devretme işleminin ilerleme durumunu **İşler** sekmesinden takip edebilirsiniz.
6. Verilerin yük devretmeden önce eşitlenmesi seçeneğini belirlediyseniz, ilk veri eşitlemesi tamamlandıktan sonra ve Azure 'daki sanal makineleri kapatmaya hazırsanız **işler** > iş adı > **Yük devretmeyi tamamlayamadı**' a tıklayın. Bu, şunları yapar:
    - Azure makinesini kapatır.
    - En son değişiklikleri şirket içi VM 'ye aktarır.
    - Şirket içi VM 'yi başlatır.
7. Artık, şirket içi VM makinesinde oturum açarak beklendiği gibi kullanılabilir olup olmadığını kontrol edebilirsiniz.
8. Sanal makine, bir tamamlama bekleme durumunda. Yük devretmeyi yürütmek için **Yürüt** ' e tıklayın.
9. Yeniden çalışmayı gerçekleştirmek için, şirket içi VM 'yi tekrar Azure 'a Çoğaltmaya başlamak üzere **çoğaltmayı tersine çevir** ' e tıklayın.



## <a name="fail-back-to-an-alternate-location"></a>Alternatif bir konuma geri dönme 

Alternatif bir konuma aşağıdaki şekilde geri dönme:

1. Yeni donanım ayarlıyorsanız, [Windows 'un desteklenen bir sürümünü](hyper-v-azure-support-matrix.md#replicated-vms)ve makinede Hyper-V rolünü yükleyebilirsiniz.
2. Özgün sunucuda sahip olduğunuz adla aynı ada sahip bir sanal ağ anahtarı oluşturun.
3. **Korumalı öğeler**  >  **koruma grubunda**  >  \<ProtectionGroupName>  ->  \<VirtualMachineName> , yeniden yük devretmek istediğiniz VM 'yi seçin ve ardından **Planlı Yük devretme**' yı seçin.
4. **Planlı Yük devretmeyi Onayla** bölümünde, **yoksa, şirket Içi sanal makine oluştur**' u seçin.
5. **Ana bilgisayar adı**' nda, VM 'yi yerleştirmek Istediğiniz yeni Hyper-V ana bilgisayar sunucusunu seçin.
6. **Veri eşitleme**' de, yük devretmeden önce verileri eşitleme seçeneğini seçmenizi öneririz. Bu, VM 'Lerin kapanmadan eşitlendiği kapalı kalma süresini en aza indirir. Şunları yapar:
    - **1. aşama**: Azure VM 'nin anlık görüntüsünü alır ve şirket içi Hyper-V konağına kopyalar. Makine Azure 'da çalışmaya devam ediyor.
    - **2. aşama**: hiçbir yeni değişiklik gerçekleşmemesi için Azure VM 'yi kapatır. Son değişiklik kümesi şirket içi sunucuya aktarılır ve şirket içi sanal makine başlatılır.
    
7. Yük devretmeye (yeniden çalışma) başlamak için onay işaretine tıklayın.
8. İlk eşitleme bittikten ve Azure VM 'yi kapatmaya hazırsanız, **işler**  >  \<planned failover job>  >  **Yük devretmeyi tamamlayacak** öğesine tıklayın. Bu, Azure makinesini kapatır, en son değişiklikleri şirket içi VM 'ye aktarır ve başlatır.
9. Her şeyin beklendiği gibi çalıştığını doğrulamak için şirket içi VM 'de oturum açabilirsiniz.
10. Yük devretmeyi tamamlamak için **Yürüt** ' e tıklayın. Yürüt, Azure VM 'sini ve disklerini siler ve şirket içi VM 'yi yeniden korunacak şekilde hazırlar.
10. Şirket içi VM 'yi Azure 'a Çoğaltmaya başlamak için **çoğaltmayı tersine çevir** ' e tıklayın. Azure 'da VM kapatılmış olduğundan yalnızca Delta değişir.

    > [!NOTE]
    > Veri eşitleme sırasında yeniden çalışma işini iptal ederseniz şirket içi VM bozulmuş durumda olur. Bunun nedeni, veri eşitlemesinin en son verileri Azure VM disklerinden şirket içi veri disklerine kopyalama ve eşitleme tamamlanana kadar disk verilerinin tutarlı bir durumda olmaması olabilir. Veri eşitleme iptal edildikten sonra şirket içi VM başlatılırsa, önyüklenemeyebilir. Bu durumda, veri eşitlemesini tamamlamaya yönelik yük devretmeyi yeniden çalıştırın.


## <a name="next-steps"></a>Sonraki adımlar
Şirket içi VM Azure 'a çoğaltıldıktan sonra, gerektiğinde Azure 'a [başka bir yük devretme](site-recovery-failover.md) işlemi gerçekleştirebilirsiniz.
