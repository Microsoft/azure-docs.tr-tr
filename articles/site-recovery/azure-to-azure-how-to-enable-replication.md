---
title: Azure Site Recovery | Azure VM 'Leri için çoğaltmayı yapılandırma | Microsoft Docs
description: Bu makalede, Azure VM 'Leri için Site Recovery kullanarak bir Azure bölgesinden diğerine çoğaltma yapılandırma açıklanmaktadır.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 7559bfd3d97f7b430b92578473501b519eb0a07f
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934555"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Azure VM 'lerini başka bir Azure bölgesine çoğaltma


Bu makalede, Azure VM 'lerinin bir Azure bölgesinden diğerine çoğaltılmasını nasıl etkinleştireceğinizi açıklanmaktadır.

## <a name="before-you-start"></a>Başlamadan önce

Bu makalede, [Azure 'Dan Azure 'a olağanüstü durum kurtarma öğreticisinde](azure-to-azure-tutorial-enable-replication.md)açıklandığı gibi Site Recovery dağıtım için hazırladığınız varsayılmaktadır.

Önkoşullar yerinde olmalıdır ve bir kurtarma hizmetleri Kasası oluşturmuş olmanız gerekir.


## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

Çoğaltmayı etkinleştirin. Bu yordam, birincil Azure bölgesinin Doğu Asya olduğunu ve ikincil bölgenin Güney Doğu Asya olduğunu varsayar.

1. Kasada **+ Çoğalt**' a tıklayın.
2. Aşağıdaki alanlara göz önünde edin:
   - **Kaynak**: Bu örnekte **Azure**olan VM 'lerin kaynak noktası.
   - **Kaynak konumu**: VM 'lerinizi korumak istediğiniz Azure bölgesi. Bu çizim için kaynak konumu ' Doğu Asya '
   - **Dağıtım modeli**: Kaynak makinelerin Azure dağıtım modeli.
   - **Kaynak abonelik**: Kaynak sanal makinelerinizin ait olduğu abonelik. Bu abonelik, kurtarma hizmetleri kasanızın bulunduğu Azure Active Directory kiracısında bulunan aboneliklerden biri olabilir.
   - **Kaynak grubu**: Kaynak sanal makinelerinizin ait olduğu kaynak grubu. Seçilen kaynak grubundaki tüm VM 'Ler, bir sonraki adımda koruma için listelenir.

     ![Çoğaltmayı etkinleştirme](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Sanal **makineler > sanal makineler**' i seçin ve çoğaltmak ISTEDIĞINIZ her VM 'yi seçin. Yalnızca çoğaltmanın etkinleştirildiği makineleri seçebilirsiniz. Daha sonra, **Tamam**'a tıklayın.
    ![Çoğaltmayı etkinleştirme](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. **Ayarlar**' da, hedef site ayarlarını isteğe bağlı olarak yapılandırabilirsiniz:

   - **Hedef konum**: Kaynak sanal makine verilerinizin çoğaltılacağı konum. Seçtiğiniz makine konumunuza bağlı olarak Site Recovery, size uygun hedef bölgelerin listesini sağlar. Hedef konumu kurtarma hizmetleri Kasası konumuyla aynı tutmanız önerilir.
   - **Hedef abonelik**: Olağanüstü durum kurtarma için kullanılan hedef abonelik. Hedef abonelik varsayılan olarak kaynak abonelikle aynı olur.
   - **Hedef kaynak grubu**: Tüm çoğaltılan sanal makinelerinizin ait olduğu kaynak grubu.
       - Varsayılan olarak Site Recovery, hedef bölgede, adında bir "ASR" sonekiyle yeni bir kaynak grubu oluşturur.
       - Site Recovery tarafından oluşturulan kaynak grubu zaten varsa, yeniden kullanılır.
       - Kaynak grubu ayarlarını özelleştirebilirsiniz.
       - Kaynak VM 'Lerin barındırıldığı bölge dışında, hedef kaynak grubunun konumu herhangi bir Azure bölgesi olabilir.
   - **Hedef sanal ağ**: Site Recovery, varsayılan olarak hedef bölgede, adında bir "ASR" sonekine sahip yeni bir sanal ağ oluşturur. Bu, kaynak ağınıza eşlenir ve gelecekteki koruma için kullanılır. Ağ eşlemesi hakkında [daha fazla bilgi edinin](site-recovery-network-mapping-azure-to-azure.md) .
   - **Hedef depolama hesapları (kaynak VM yönetilen diskleri kullanmıyor)** : Varsayılan olarak Site Recovery, kaynak VM depolama yapılandırmanızı inceleyerek yeni bir hedef depolama hesabı oluşturur. Depolama hesabı zaten mevcut olduğunda, yeniden kullanılır.
   - **Çoğaltma ile yönetilen diskler (kaynak VM, yönetilen diskleri kullanır)** : Site Recovery, kaynak VM 'nin yönetilen diskiyle aynı depolama türüyle (Standart veya Premium) kaynak sanal makinenin yönetilen disklerini yansıtmak için hedef bölgede yeni bir çoğaltma tarafından yönetilen diskler oluşturur.
   - **Önbellek depolama hesapları**: Site Recovery, kaynak bölgede önbellek depolaması adlı ek depolama hesabı gerektirir. Kaynak VM 'lerde oluşan tüm değişiklikler, hedef konuma çoğaltılmadan önce izlenir ve önbellek depolama hesabına gönderilir. Bu depolama hesabı standart olmalıdır.
   - **Hedef kullanılabilirlik kümeleri**: Varsayılan olarak Site Recovery, kaynak bölgedeki bir kullanılabilirlik kümesinin parçası olan VM 'Ler için, hedef bölgede "ASR" sonekine sahip yeni bir kullanılabilirlik kümesi oluşturur. Site Recovery tarafından oluşturulan kullanılabilirlik kümesi zaten mevcutsa, yeniden kullanılır.
   - **Hedef kullanılabilirlik alanları**: Varsayılan olarak, hedef bölge kullanılabilirlik bölgelerini destekliyorsa, Site Recovery hedef bölgedeki kaynak bölgeyle aynı bölge numarasını atar.

     Hedef bölge kullanılabilirlik bölgelerini desteklemiyorsa, hedef VM 'Ler varsayılan olarak tek örnek olarak yapılandırılır. Gerekirse, ' Özelleştir ' öğesine tıklayarak bu tür VM 'Leri hedef bölgedeki kullanılabilirlik kümelerinin bir parçası olacak şekilde yapılandırabilirsiniz.

     >[!NOTE]
     >Çoğaltmayı etkinleştirdikten sonra kullanılabilirlik türü-tek örnek, kullanılabilirlik kümesi veya kullanılabilirlik bölgesi değiştirilemez. Kullanılabilirlik türünü değiştirmek için çoğaltmayı devre dışı bırakıp yeniden etkinleştirmeniz gerekir.
     >
    
   - **Çoğaltma İlkesi**: Kurtarma noktası bekletme geçmişi ve uygulamayla tutarlı anlık görüntü sıklığı ayarlarını tanımlar. Varsayılan olarak Azure Site Recovery, kurtarma noktası bekletmesi için ' 24 saat ' varsayılan ayarlarına sahip yeni bir çoğaltma ilkesi oluşturur ve uygulamayla tutarlı anlık görüntü sıklığı için ' 4 saat '.

     ![Çoğaltmayı etkinleştirme](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Eklenen diskler için çoğaltmayı etkinleştir

Çoğaltmanın etkinleştirildiği bir Azure VM 'sine disk eklerseniz, aşağıdakiler gerçekleşir:
-   VM için çoğaltma sistem durumu bir uyarı gösterir ve bir veya daha fazla diskin koruma için kullanılabildiğini bildiren bir uyarı verir.
-   Eklenen diskler için korumayı etkinleştirirseniz, diskin ilk çoğaltmadan sonra uyarı kaybolacaktır.
-   Disk için çoğaltmayı etkinleştiremeyebilir ' i seçerseniz, uyarıyı kapatmak için seçeneğini belirleyebilirsiniz.

    
    ![Yeni disk eklendi](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Eklenen bir disk için çoğaltmayı etkinleştirmek üzere aşağıdakileri yapın:

1.  Kasaya **çoğaltılan öğeleri**>, DISKI eklediğiniz VM 'ye tıklayın.
2.  **Diskler**' e tıklayın ve ardından çoğaltmayı etkinleştirmek istediğiniz veri diskini seçin (bu disklerde **korumalı olmayan** bir durum vardır).
3.  **Disk ayrıntıları**' nda **çoğaltmayı etkinleştir**' e tıklayın.

    ![Eklenen disk için çoğaltmayı etkinleştir](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Çoğaltma işini etkinleştirme işi çalıştıktan sonra ve ilk çoğaltma tamamlandıktan sonra, disk sorunu için çoğaltma sistem durumu uyarısı kaldırılır.


  
## <a name="customize-target-resources"></a>Hedef kaynakları özelleştirme

Site Recovery tarafından kullanılan varsayılan hedef ayarlarını değiştirebilirsiniz.

1. Varsayılan hedef aboneliği değiştirmek için Özelleştir ' e tıklayın **:** ' hedef abonelik ' seçeneğinin yanındaki. Aynı Azure Active Directory (AAD) kiracısında bulunan tüm abonelikler listesinden aboneliği seçin.

2. Özelleştir ' e tıklayın **:** varsayılan ayarları değiştirmek için:
    - **Hedef kaynak grubu**' nda, aboneliğin hedef konumundaki tüm kaynak grupları listesinden kaynak grubunu seçin.
    - **Hedef sanal ağ**' da, hedef konumdaki tüm sanal ağ listesinden ağı seçin.
    - **Kullanılabilirlik**kümesi ' nde, kaynak bölgedeki bir kullanılabilirlik KÜMESININ parçasıysa VM 'ye kullanılabilirlik kümesi ayarları ekleyebilirsiniz.
    - **Hedef depolama hesapları**' nda, kullanmak istediğiniz hesabı seçin.

        ![Çoğaltmayı etkinleştirme](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. **Yapılandır** ' a tıklayarak çoğaltma ayarlarını değiştirin.
4. **Çoklu VM tutarlılığı**' nda, birlikte çoğaltmak Istediğiniz VM 'leri seçin.
    - Bir çoğaltma grubundaki tüm makineler, yük devredildiğinde paylaşılan kilitlenmeyle tutarlı ve uygulamayla tutarlı kurtarma noktalarına sahip olur.
    - Çoklu VM tutarlılığını etkinleştirmek, iş yükü performansını etkileyebilir (CPU yoğun olduğundan). Yalnızca makineler aynı iş yükünü çalıştırıyorsa ve birden çok makine arasında tutarlılık gerekiyorsa etkinleştirilmelidir.
    - Örneğin, bir uygulamanın 2 SQL Server sanal makinesi ve iki Web sunucusu varsa, bir çoğaltma grubuna yalnızca SQL Server VM 'Leri eklemeniz gerekir.
    - Bir çoğaltma grubunda en fazla 16 VM 'yi seçebilirsiniz.
    - Çoklu VM tutarlılığını etkinleştirirseniz çoğaltma grubundaki makineler birbiriyle 20004 numaralı bağlantı noktası üzerinden iletişim kurar.
    - 20004 numaralı bağlantı noktası üzerinden VM 'Ler arasındaki iç iletişimi engelleyen bir güvenlik duvarı gereci bulunmadığından emin olun.
    - Linux VM 'lerinin bir çoğaltma grubunun parçası olmasını istiyorsanız, bağlantı noktası 20004 ' deki giden trafiğin, belirli bir Linux sürümüne yönelik kılavuza göre el ile açıldığından emin olun.
![Çoğaltmayı etkinleştirme](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
5. **Hedef kaynak** > oluştur**çoğaltmasını etkinleştir**' e tıklayın.
6. VM 'Ler çoğaltma için etkinleştirildikten sonra, **çoğaltılan öğeler** altında VM sistem durumunun durumunu kontrol edebilirsiniz

>[!NOTE]
>İlk çoğaltma sırasında durumun yenilenmesi zaman alabilir ve devam etmeden devam edebilir. En son durumu almak için **Yenile** düğmesine tıklayın.
>

## <a name="next-steps"></a>Sonraki adımlar

Yük devretme testi çalıştırma hakkında [daha fazla bilgi edinin](site-recovery-test-failover-to-azure.md) .
