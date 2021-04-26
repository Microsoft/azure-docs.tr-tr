---
title: Sanal makinelerin olağanüstü durum kurtarma işleminin tamamını
description: Bu makalede, Azure VMware çözümü kullanılarak sanal makinelerin olağanüstü durum kurtarmasının nasıl tamamlanacağı gösterilmektedir
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 688d91bc181e1479f5090a10af4b3b262d7ddb7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779620"
---
# <a name="complete-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Azure VMware çözümünü kullanarak sanal makinelerin olağanüstü durum kurtarmasını tamamlanma

Bu makale, VMware HCX çözümüne sahip sanal makinelerinizin (VM) olağanüstü durum kurtarma işlemini tamamlamaya ve kurtarma veya hedef site olarak bir Azure VMware Çözüm özel bulutu kullanmaya yönelik işlemi içerir.

VMware HCX, çoğaltma ilkelerinde ince denetim ve ayrıntı düzeyi sağlayan çeşitli işlemler sağlar. Kullanılabilir Işlemler şunları içerir:

- **Ters** : olağanüstü bir durum meydana geldi. Ters çevir, site B 'yi, korunan VM 'nin artık yaşadığı kaynak site ve site A olmasına yardımcı olur.

- **Duraklat** – seçili VM ile ilişkili geçerli çoğaltma ilkesini duraklatın.

- **Özgeçmişi** -seçili VM ile ilişkili geçerli çoğaltma ilkesini sürdürür.

- **Kaldır** -seçilen VM ile ilişkili geçerli çoğaltma ilkesini kaldırın.

- **Şimdi Eşitle** – sınırlı EŞITLEME kaynak VM 'SINI korunan VM 'ye geçirin.

Bu kılavuzda aşağıdaki çoğaltma senaryoları ele alınmaktadır:

- Bir VM 'yi veya bir VM grubunu koruyun.

- Bir sanal makinenin veya bir VM grubunun bir test kurtarmasına ilişkin bir sınama testi yapın.

- Bir VM 'yi veya bir VM grubunu kurtarın.

- Bir VM 'nin veya bir VM grubunun korumasını tersine çevirin.

## <a name="protect-vms"></a>VM’leri koruma

1. Kaynak sitede **vSphere istemcisinde** oturum açın ve **HCX eklentisine** erişin.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="VSphere içinde HCX seçeneği" border="true":::

1. **Olağanüstü durum kurtarma** alanını girin ve **VM 'leri koru**' yı seçin.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="VM 'leri koru seçeneğini belirleyin" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Kaynağı ve uzak siteleri seçin. Bu durumda uzak site, Azure VMware çözümü özel bulutu olmalıdır.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="VM 'Leri koruma penceresi" border="true":::

1. Gerekirse, **varsayılan çoğaltma** seçeneklerini belirleyin:

   - **Sıkıştırmayı etkinleştir:** Düşük aktarım hızı senaryoları için önerilir.

   - **Quiescence etkinleştir:** Uzak siteyle tutarlı bir kopya eşitlendiğinden emin olmak için sanal makineyi duraklatır.

   - **Hedef depolama alanı:** Korunan VM 'Ler için uzak veri deposu ve vSAN veri deposu olması gereken bir Azure VMware çözümü özel bulutu.

   - **Işlem kapsayıcısı:** Uzak vSphere kümesi veya kaynak havuzu.

   - **Hedef klasör:** Uzak hedef klasörü, isteğe bağlıdır ve hiçbir klasör seçilmezse VM 'Ler doğrudan seçilen kümenin altına yerleştirilir.

   - **RPO:** Kaynak VM ile korumalı VM arasındaki eşitleme aralığı. 5 dakika ile 24 saat arasında bir süre olabilir.

   - **Anlık görüntü aralığı:** Anlık görüntüler arasındaki Aralık.

   - **Anlık görüntü sayısı:** Yapılandırılan anlık görüntü aralığı içindeki toplam anlık görüntü sayısı.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="VM 'Ler seçeneklerini koruma" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. Listeden bir veya daha fazla sanal makine seçin ve çoğaltma seçeneklerini gereken şekilde yapılandırın.

   Varsayılan olarak, VM 'Ler varsayılan çoğaltma seçeneklerinde yapılandırılmış genel ayarlar Ilkesini alırlar. Seçili VM 'deki her bir ağ arabirimi için, uzak **ağ bağlantı noktası grubunu** yapılandırın ve **son** ' u seçerek koruma sürecini başlatın.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="Ağ arabirimi seçenekleri" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Aynı olağanüstü durum kurtarma alanında seçili VM 'lerin her biri için işlemi izleyin.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="korumanın ilerlemesini izleme" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. VM korunduktan sonra, **anlık** görüntüler sekmesinden farklı anlık görüntüleri görüntüleyebilirsiniz.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="anlık görüntülerin listesi" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   Sarı üçgen, anlık görüntüler ve sanal makinelerin test kurtarma işleminde sınanmadığını gösterir.

   Kapatılan ve bir sanal makine arasında önemli farklar vardır. Görüntüde, açık bir VM için eşitleme işlemi gösterilir. Bu, VM 'nin tam bir kopyası olan ilk anlık görüntüyü tamamlayana kadar eşitleme işlemini başlatır ve sonra yapılandırılan aralıkta bir sonraki işlemleri tamamlar. Kapalı bir VM için bir kopyayı eşitler ve ardından VM etkin değil olarak görünür ve koruma işlemi tamamlandı olarak gösterilir.  VM açık olduğunda, uzak siteye eşitleme işlemini başlatır.

## <a name="complete-a-test-recover-of-vms"></a>VM 'lerin bir test kurtarmalarını tamammayın

1. Azure VMware çözümü özel bulutu olan uzak sitede **vSphere istemcisinde** oturum açın. 
1. **HCX eklentisi** Içinde, olağanüstü durum kurtarma alanında, işlemler menüsünü göstermek için HERHANGI bir VM 'deki dikey üç nokta simgesini seçin ve ardından **Kurtarma VM 'sini sına**' yı seçin.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="Test kurtarma VM 'sini seçin" border="true":::

1. Sanal makinenin farklı durumlarını test etmek için kullanmak istediğiniz test ve anlık görüntü seçeneklerini belirleyin.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="bir anlık görüntü seçin ve test 'i seçin" border="true":::

1. **Test**' i seçtikten sonra kurtarma işlemi başlar.

1. İşiniz bittiğinde, Azure VMware çözümü özel bulutu vCenter ' daki yeni VM 'yi kontrol edebilirsiniz.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="kurtarma işlemini denetle" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. VM 'de veya üzerinde çalışan herhangi bir uygulamada test yapıldıktan sonra, test örneğini silmek için bir temizlik yapın.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="test örneğini temizle" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>VM 'Leri kurtarma

1. Azure VMware çözümü özel bulutu olan uzak sitede **vSphere istemcisinde** oturum açın ve **HCX eklentisine** erişin.

   Kurtarma senaryosunda, bu örnek için kullanılan bir VM grubu.

1. Listeden kurtarılacak VM 'yi seçin, **Eylemler** menüsünü açın ve **VM 'leri kurtar**' ı seçin.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="VM 'Leri kurtarma" border="true":::

1. Her örnek için kurtarma seçeneklerini yapılandırın ve kurtarma işlemini başlatmak için **kurtar** ' ı seçin.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="VM 'Leri kurtarma onayı" border="true":::

1. Kurtarma işlemi tamamlandıktan sonra, yeni VM 'Ler uzak vCenter Server envanterinde görünür.

## <a name="complete-a-reverse-replication-on-vms"></a>VM 'lerde ters çoğaltmayı tamamlar

1. Azure VMware çözümünüz özel bulutunuzda **vSphere istemcisinde** oturum açın ve **HCX eklentisine** erişin.
   
   >[!NOTE]
   > Ters çoğaltmaya başlamadan önce kaynak sitedeki özgün VM 'Lerin kapalı olduğundan emin olun. VM 'Ler kapalıyken işlem başarısız olur.

1. Listeden, kaynak siteye geri çoğaltılacak VM 'Leri seçin, **Eylemler** menüsünü açın ve **ters**' yı seçin. 
1. Çoğaltmayı başlatmak için **ters** ' yı seçin.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="Koruma işlemleri altında tersine eylem ' i seçin" border="true":::

1. Her VM 'nin Ayrıntılar bölümünde izleyin.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="ters işlemin sonuçlarını gözden geçirme" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Olağanüstü durum kurtarma planı Otomasyonu

VMware HCX Şu anda bir olağanüstü durum kurtarma planı oluşturmak ve otomatikleştirmek için yerleşik bir mekanizmaya sahip değildir. Ancak, VMware HCX, olağanüstü durum kurtarma işlemi için API 'Ler dahil olmak üzere bir dizi REST API 'si sağlar. API belirtimine URL 'de VMware HCX Yöneticisi içinden erişilebilir.

Bu API 'Ler olağanüstü durum kurtarma 'da aşağıdaki işlemleri kapsar.

- Koruma

- Kurtar

- Sınama kurtar

- Planlı kurtarma

- Reverse

- Sorgu

- Testi Temizleme

- Duraklat

- Sürdür

- Korumayı kaldır

- Yeniden Yapılandırma

JSON 'da kurtarma işlemi yükünün bir örneği aşağıda gösterilmiştir.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Bu API 'lerle, bir olağanüstü durum kurtarma planının oluşturulmasını ve çalıştırılmasını otomatikleştirmek için özel bir mekanizma oluşturabilirsiniz.
