---
title: Hyper-V VM disklerini olağanüstü durum kurtarma 'dan Azure 'a Azure Site Recovery hariç tutma
description: Hyper-V VM disklerini Azure Site Recovery Azure 'a çoğaltmadan dışlama.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86131250"
---
# <a name="exclude-disks-from-replication"></a>Diskleri çoğaltmanın dışında tutma

Bu makalede, Hyper-V VM 'lerini Azure 'a çoğaltırken disklerin nasıl hariç tutulacağı açıklanır. Birçok nedenden dolayı diskleri çoğaltmadan dışlamak isteyebilirsiniz:

- Çıkarılan disk üzerinde önemli olmayan verilerin çoğaltılmadığından emin olun.
- Çoğaltmak zorunda olmadığınız diskleri dışlayarak tüketilen çoğaltma bant genişliğini veya hedef tarafı kaynakları iyileştirin.
- İhtiyaç duymamakta olduğunuz verileri çoğaltmayana depolama ve ağ kaynaklarını kaydedin.

Diskleri çoğaltmadan hariç tutmak için:

- Diskleri dışarıda bırakma hakkında [daha fazla bilgi edinin](exclude-disks-replication.md) .
- Bir diskin çoğaltma, yük devretme ve yeniden çalışma işlemlerini nasıl etkilediğini gösteren [tipik dışlama senaryolarını](exclude-disks-replication.md#typical-scenarios) ve [örnekleri](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) gözden geçirin.

## <a name="before-you-start"></a>Başlamadan önce

Başlamadan önce aşağıdakilere dikkat edin:

- **Çoğaltma**: varsayılan olarak bir makinedeki tüm diskler çoğaltılır.
- **Disk türü**:
    - Temel diskleri çoğaltmanın dışında bırakabilirsiniz.
    - İşletim sistemi diskleri dışarıda tutulamaz.
    - Dinamik diskleri dışarıda tutmamanızı öneririz. Site Recovery Konuk sanal makinede temel veya dinamik olan VHD 'YI tanımlayamıyor.  Tüm bağımlı dinamik birim disklerini dışlamazsanız, korunan dinamik disk yük devredilen VM 'de başarısız bir disk haline gelir ve diskteki verilere erişilemez.
- **Diskleri Ekle/Kaldır/çıkar**: çoğaltmayı etkinleştirdikten sonra çoğaltma için disk ekleyemez/kaldıramaz/dışlayabilirsiniz. Bir diski eklemek/kaldırmak veya dışlamak istiyorsanız, VM 'nin korumasını devre dışı bırakmanız ve yeniden etkinleştirmeniz gerekir.
- **Yük devretme**: yük devretmenin ardından, yük devretme sonrasında uygulamaların çalışması için diskleri hariç tutmak gerekiyorsa, bu diskleri el ile oluşturmanız gerekir. Alternatif olarak, makinenin yük devretmesi sırasında diski oluşturmak için Azure Otomasyonu 'nu bir kurtarma planıyla tümleştirebilirsiniz.
- Yeniden **çalışma**: yük devretmeden sonra şirket içi sitenize geri döndüğünüzde, Azure 'da el ile oluşturduğunuz diskler yeniden başarısız olur. Örneğin, üç disk üzerinde yük devretmek ve doğrudan bir Azure VM üzerinde iki disk oluşturursanız, yük devredilen yalnızca üç disk yeniden başarısız olur. Yeniden çalışma sırasında veya VM 'lerin tersine çoğaltılmasıyla el ile oluşturulan diskleri dahil edebilirsiniz.

## <a name="exclude-disks"></a>Diskleri dışlama

1. Diskleri dışlamak için bir Hyper-V VM için [çoğaltmayı etkinleştirdiğinizde](./hyper-v-azure-tutorial.md) , çoğaltmak istediğiniz VM 'leri seçtikten sonra, **çoğaltma**  >  **özelliklerini**  >  **Yapılandır Özellikler** sayfasında, sütunu **çoğaltmak için diskleri** gözden geçirin. Varsayılan olarak, tüm diskler çoğaltma için seçilir.
2. Belirli bir diski çoğaltmak istemiyorsanız, **diskler** ' de dışlamak istediğiniz disklerin seçimini temizleyin. 

    ![Diskleri çoğaltmanın dışında tutma](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Sonraki adımlar
Dağıtımınız ayarlandıktan ve çalışmaya başladıktan sonra farklı türdeki yük devretmeler hakkında [daha fazla bilgi edinebilirsiniz](failover-failback-overview.md).
