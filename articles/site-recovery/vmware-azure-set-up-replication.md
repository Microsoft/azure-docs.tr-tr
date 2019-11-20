---
title: VMware olağanüstü durum kurtarma için Azure Site Recovery ile çoğaltma ilkelerini ayarlama | Microsoft Docs
description: Azure Site Recovery ile Azure 'a VMware olağanüstü durum kurtarma için çoğaltma ayarlarının nasıl yapılandırılacağını açıklar.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954318"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>VMware olağanüstü durum kurtarma için çoğaltma ilkelerini yapılandırma ve yönetme

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak VMware VM 'lerini Azure 'a çoğaltırken bir çoğaltma ilkesinin nasıl yapılandırılacağı açıklanır.

## <a name="create-a-policy"></a>İlke oluşturma

1. **Yönet** > **Site Recovery Altyapısı**’nı seçin.
2. **VMware ve fiziksel makineler için**, **çoğaltma ilkeleri**' ni seçin.
3. **+ Çoğaltma İlkesi**' ne tıklayın ve ilke adını belirtin.
4. **RPO eşiği** bölümünde RPO limitini belirtin. Sürekli çoğaltma bu sınırı aştığında uyarılar oluşturulur.
5. **Kurtarma noktası bekletme** kısmında, her kurtarma noktası için bekletme süresini saat cinsinden belirtin. Korumalı makineler, bekletme penceresi içindeki herhangi bir noktaya kurtarılabilir. Premium depolama alanına çoğaltılan makineler için 24 saate kadar bekletme desteklenir. Standart depolama için en fazla 72 saat desteklenir.
6. **Uygulamayla tutarlı anlık görüntü sıklığı**' nda, uygulama ile tutarlı anlık görüntüleri içeren kurtarma noktalarının ne sıklıkta (saat cinsinden) oluşturulması gerektiğini açılan listeden seçin. Uygulama tutarlılığı noktalarını oluşturmayı devre dışı bırakmak istiyorsanız, açılan listeden "kapalı" değerini seçin.
7. **OK (Tamam)** düğmesine tıklayın. İlke, 30 ila 60 saniye içinde oluşturulur.

Bir çoğaltma ilkesi oluşturduğunuzda, eşleşen bir yeniden çalışma çoğaltma ilkesi otomatik olarak "yeniden çalışma" sonekiyle birlikte oluşturulur. İlkeyi oluşturduktan sonra, > **Ayarları Düzenle**' yi seçerek düzenleyebilirsiniz.

## <a name="associate-a-configuration-server"></a>Yapılandırma sunucusunu ilişkilendir

Çoğaltma ilkesini şirket içi yapılandırma sunucusu ile ilişkilendirin.

1. **İlişkilendir**' e tıklayın ve yapılandırma sunucusunu seçin.

    ![Yapılandırma sunucusunu ilişkilendir](./media/vmware-azure-set-up-replication/associate1.png)
2. **OK (Tamam)** düğmesine tıklayın. Yapılandırma sunucusu, bir ila iki dakika içinde ilişkilendirilir.

    ![Yapılandırma sunucusu ilişkilendirme](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Bir ilkeyi düzenleme

Bir çoğaltma ilkesini oluşturduktan sonra değiştirebilirsiniz.

- İlkedeki değişiklikler, ilke kullanılarak tüm makinelere uygulanır.
- Çoğaltılan makineleri farklı bir çoğaltma ilkesiyle ilişkilendirmek istiyorsanız ilgili makineler için korumayı devre dışı bırakıp yeniden etkinleştirmeniz gerekir.

İlkeyi aşağıdaki gibi düzenleyin:
1.  > **Site Recovery altyapıyı** > **çoğaltma ilkelerini** **Yönet** ' i seçin.
2. Değiştirmek istediğiniz çoğaltma ilkesini seçin.
3. **Ayarları Düzenle**' ye tıklayın ve RPO eşiği/kurtarma noktası bekletme saatleri/uygulamayla tutarlı anlık görüntü sıklığı alanlarını gerektiği şekilde güncelleştirin.
4. Uygulama tutarlılığı noktalarını oluşturmayı devre dışı bırakmak istiyorsanız, **uygulamayla tutarlı anlık görüntü sıklığı**alanının açılan menüsünde "kapalı" değerini seçin.
5. **Save (Kaydet)** düğmesine tıklayın. İlke, 30 ila 60 saniye içinde güncelleştirilmeleri gerekir.



## <a name="disassociate-or-delete-a-replication-policy"></a>Çoğaltma ilkesinin ilişkisini kaldır veya Sil

1. Çoğaltma ilkesini seçin.
    a. İlkenin yapılandırma sunucusundan ilişkilendirmesini kaldırmak için, ilkeyi hiçbir çoğaltılan makinede kullanmadığından emin olun. Ardından ilişkiyi **Kaldır**' a tıklayın.
    b. İlkeyi silmek için bir yapılandırma sunucusuyla ilişkili olmadığından emin olun. Ardından **Sil**' e tıklayın. Silmek için 30-60 saniye sürer.
2. **OK (Tamam)** düğmesine tıklayın.
