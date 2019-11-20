---
title: Azure VM olağanüstü durum kurtarma detayına gidin Azure Site Recovery
description: Azure Site Recovery hizmetini kullanarak Azure VM 'Leri için ikincil bir bölgeye olağanüstü durum kurtarma detayına nasıl çalıştırılacağını öğrenin.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 817a220e36ac250b1d5a5aa90d0bddbfb155cc26
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091329"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Azure VM 'Leri için ikincil bir bölgeye olağanüstü durum kurtarma ayrıntısı çalıştırma 

[Azure Site Recovery](site-recovery-overview.md) hizmeti, planlı ve plansız kesintiler sırasında iş uygulamalarınızı çalışır durumda tutarak, iş sürekliliğinize ve olağanüstü durum kurtarma (BCDR) stratejinize katkıda bulunur. Site Recovery, şirket içi makinelerin ve Azure sanal makinelerinin çoğaltma, yük devretme ve kurtarma gibi olağanüstü durum kurtarma işlemlerini yönetir ve düzenler.

Bu öğretici, bir Azure VM için yük devretme testiyle bir Azure bölgesinden diğerine nasıl olağanüstü durum kurtarma tatbikatı çalıştıracağınızı gösterir. Tatbikat, çoğaltma stratejinizi veri kaybı veya kesinti süresi olmadan doğrular ve üretim ortamınızı etkilemez. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Önkoşulları denetleme
> * Tek bir VM için yük devretme testi çalıştırma

> [!NOTE]
> Bu öğretici, en az adımla bir DR ayrıntısı gerçekleştirmenize yardımcı olur; Ağ değerlendirmeleri, Otomasyon veya sorun giderme dahil olmak üzere bir DR detayını gerçekleştirmeye ilişkin çeşitli yönler hakkında daha fazla bilgi edinmek istiyorsanız, Azure VM 'Leri için ' nasıl yapılır ' altındaki belgelere bakın.

## <a name="prerequisites"></a>Önkoşullar

- Yük devretme testini çalıştırmadan önce, her şeyin beklenildiği gibi gittiğinden emin olmak için VM özelliklerini doğrulamanızı öneririz.  **Çoğaltılmış öğeler** bölümünde VM özelliklerine erişin. **Temel bileşenler** dikey penceresi, makinelerin ayarları ve durumuyla ilgili bilgileri gösterir.
- Çoğaltmayı etkinleştirdiğinizde ayarlanmış varsayılan ağ yerine **yük devretme testi için ayrı bir Azure VM ağını kullanmanızı öneririz**.
- Her NIC için kaynak ağ yapılandırmanıza bağlı olarak, isteğe bağlı olarak, DR detayına başlamadan önce Işlem & ağındaki test yük devretmesi ayarları altındaki her bir NIC 'ye iliştirilecek **alt ağ, IP adresi, genel IP, ağ güvenlik grubu veya dahili Load Balancer** belirtebilirsiniz.


## <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma

1. **Ayarlar** > **Çoğaltılmış Öğeler** bölümünde, **+Yük Devretme Testi** simgesine tıklayın.

2. **Yük Devretme Testi** bölümünde, yük devretmede kullanılması için bir kurtarma noktası seçin:

    - **En son**: Site Recovery tüm verileri işler ve en düşük RTO (kurtarma süresi hedefi) sağlar.
    - **En son işlenen**: VM yükü, Site Recovery tarafından işlenen en son kurtarma noktasına devredilir. Zaman damgası gösterilir. Bu seçenekle veri işlemeye zaman harcanmadığından düşük RTO sağlanılır (Kurtarma Süresi Hedefi)
   - **Uygulamayla tutarlı olan son**: Bu seçenek, tüm VM’lerin yükünü uygulamayla tutarlı olan en son kurtarma noktasına devreder. Zaman damgası gösterilir.
   - **Özel**: yük devretme, belirli bir kurtarma noktasına devredildi. Özel yalnızca tek bir VM 'nin yükünü devretmek ve kurtarma planıyla yük devretme için değil, kullanılabilir.

3. Yük devretme gerçekleştikten sonra ikincil bölgedeki Azure VM’lerin bağlanacağı hedef Azure sanal makinelerini seçin.

    > [!NOTE]
    > Test yük devretmesi ayarları çoğaltılan öğe için önceden yapılandırıldıysa, Azure sanal ağ ' i seçmek için açılan menü görünür olmayacaktır.

4. Yük devretmeyi başlatmak için, **Tamam**’a tıklayın. İlerleme durumunu izlemek için, VM’ye tıklayarak özelliklerini açın. Ya da kasa adı > **Ayarlar**İşler > Site Recovery işleri >  bölümünde **Yük Devretme Testi** işine tıklayabilirsiniz.
5. Yük devretme bittikten sonra, çoğaltma Azure VM, Azure portalı > **Sanal Makineler** bölümünde görünür. VM’nin çalıştığından, uygun şekilde boyutlandırıldığından ve uygun ağa bağlı olduğundan emin olun.
6. Yük devretme testi sırasında oluşturulan sanal makineleri silmek için, çoğaltılmış öğede veya kurtarma planında **Yük devretme testini temizle**’ye tıklayın. Yük devretme testiyle ilişkili gözlemlerinizi **Notlar**’da kaydedin veya saklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Üretim yük devretmesini çalıştırma](azure-to-azure-tutorial-failover-failback.md)
