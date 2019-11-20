---
title: Dağıtıma genel bakış-Azure için avere vFXT
description: Azure için avere vFXT dağıtmaya genel bakış
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 9684b230b8790e36cd7442c65481c0c71ce185d6
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255416"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Azure için avere vFXT-dağıtıma genel bakış

Bu makale, Azure kümesi için avere vFXT 'nin çalışır duruma getirmek için gereken adımlara genel bir bakış sunar.

Azure Marketi 'nden vFXT kümesini oluşturmadan önce ve sonra birkaç görev gereklidir. Baştan sona işlemin bitmesine açık olması, gereken çabaların kapsamını belirlemenize yardımcı olur. 

## <a name="deployment-steps"></a>Dağıtım adımları

[Sisteminizi planlamadan](avere-vfxt-deploy-plan.md)sonra, avere vFXT kümenizi oluşturmaya başlayabilirsiniz. 

Azure Marketi 'ndeki bir Azure Resource Manager şablonu, gerekli bilgileri toplar ve tüm kümeyi otomatik olarak dağıtır. 

VFXT kümesi çalışır duruma geçtikten sonra, istemcilerin bu sunucuya nasıl bağlanacağını ve gerekirse verilerinizi yeni BLOB depolama kapsayıcısına nasıl taşıyabileceğinizi bilmeniz gerekir.  

Tüm adımlara genel bakış aşağıda verilmiştir.

1. Önkoşulları yapılandırma 

   Bir VM oluşturmadan önce, avere vFXT projesi için yeni bir abonelik oluşturmanız, abonelik sahipliğini yapılandırmanız, kotaları denetlemeniz ve gerekirse bir artış istemeniz ve avere vFXT yazılımının kullanımı için koşulları kabul etmeniz gerekir. Ayrıntılı yönergeler için [avere vFXT oluşturmayı hazırlama](avere-vfxt-prereqs.md) konusunu okuyun.

1. Avere vFXT kümesi oluşturma 

   Azure için avere vFXT 'yi oluşturmak için Azure Marketi 'ni kullanın. Şablon, gerekli bilgileri toplar ve son ürünü oluşturmak için betikleri yürütür.

   Küme oluşturma işlemi, Market şablonu tarafından gerçekleştirilen bu adımları içerir: 

   * Gerekirse yeni ağ altyapısı ve kaynak grupları oluşturuluyor
   * *Küme denetleyicisi* oluşturma  

     Küme denetleyicisi, avere vFXT kümesiyle aynı sanal ağda bulunan ve kümeyi oluşturmak ve yönetmek için gerekli özel yazılıma sahip olan basit bir VM 'dir. Denetleyici, vFXT düğümlerini oluşturur ve kümeyi oluşturur ve ayrıca, yaşam süresi boyunca kümeyi yönetmek için bir komut satırı arabirimi sağlar.

     Dağıtım sırasında yeni bir VNET oluşturursanız, denetleyicinizin genel bir IP adresi olur. Bu, denetleyicinin sanal ağın dışından kümeye bağlanmak için bir geçiş ana bilgisayarı olarak kullanılabileceği anlamına gelir.

   * Küme düğümü VM 'Leri oluşturma

   * Küme düğümü VM 'lerini kümeyi oluşturacak şekilde yapılandırma

   * İsteğe bağlı olarak, yeni bir blob kapsayıcısı oluşturup küme için arka uç depolama olarak yapılandırma

1. Kümeyi yapılandırma 

   Kümenin ayarlarını özelleştirmek için avere vFXT yapılandırma arabirimine (avere Denetim Masası) bağlanın. Destek izleme için kabul edin ve şirket içi veri merkezi kullanıyorsanız depolama sisteminizi ekleyin.

   * [VFXT kümesine erişme](avere-vfxt-cluster-gui.md)
   * [Desteği etkinleştir](avere-vfxt-enable-support.md)
   * [Depolamayı yapılandırma](avere-vfxt-add-storage.md) (gerekirse)

1. İstemcileri bağla

   Yük Dengeleme ve istemci makinelerin kümeyi nasıl bağlayacağınızı öğrenmek için [avere vFXT kümesini bağlama](avere-vfxt-mount-clients.md) bölümündeki yönergeleri izleyin.

1. Veri Ekle (gerekirse)

   Avere vFXT, ölçeklenebilir bir çoklu istemci önbelleği olduğundan, verileri yeni bir arka uç depolama kapsayıcısına taşımanın en iyi yolu çok istemcili, çok iş parçacıklı dosya kopyalama stratejisidir. Ayrıntılar için [verileri vFXT kümesine taşımayı](avere-vfxt-data-ingest.md) okuyun.

## <a name="next-steps"></a>Sonraki adımlar

Azure için avere vFXT 'yi dağıtmaya yönelik ön görevleri gerçekleştirmek üzere [avere vfxt oluşturmayı hazırlamaya](avere-vfxt-prereqs.md) devam edin. 