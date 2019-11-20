---
title: Değerlendirme için VM'ler için Azure İzleyici (Önizleme) etkinleştirme | Microsoft Docs
description: Tek bir Azure sanal makinesinde veya bir sanal makine ölçek kümesinde VM'ler için Azure İzleyici değerlendirmeyi öğrenin.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: 3ab3164db7702566e62b2eedf2a9a03e9f7fb55c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109138"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Değerlendirme için VM'ler için Azure İzleyici (Önizleme) etkinleştir

Az sayıda Azure sanal makinesi (VM) veya tek bir VM ya da sanal makine ölçek kümesi üzerinde VM'ler için Azure İzleyici (Önizleme) değerlendirebilirsiniz. İzlemeyi etkinleştirmenin en kolay ve en dolaysız yolu Azure portal. Amacınız, sanal makinelerinizi izlemek ve tüm performans veya kullanılabilirlik sorunlarını bulmaktır. 

Başlamadan önce [önkoşulları](vminsights-enable-overview.md) gözden geçirin ve aboneliğinizin ve kaynaklarınızın gereksinimleri karşıladığından emin olun.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Tek bir Azure VM için izlemeyi etkinleştirme
Azure VM 'nizi izlemeyi etkinleştirmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Seçin **sanal makineler**.

1. Listeden bir VM seçin.

1. VM sayfasında içinde **izleme** bölümünden **Insights (Önizleme)** .

1. Üzerinde **Insights (Önizleme)** sayfasında **şimdi deneyin**.

    ![Bir VM için sanal makineler için Azure İzleyici etkinleştir](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Üzerinde **Azure İzleyici İçgörüler ekleme** sayfasında mevcut bir Log Analytics varsa, aynı abonelikte çalışma alanı, aşağı açılan listeden seçin.  

    Liste, varsayılan çalışma alanını ve VM 'nin abonelikte dağıtıldığı konumu önceden seçer. 

    >[!NOTE]
    >İzleme verilerini VM 'den depolamak üzere yeni bir Log Analytics çalışma alanı oluşturmak için, bkz. [Log Analytics çalışma alanı oluşturma](../../azure-monitor/learn/quick-create-workspace.md). Log Analytics çalışma alanınız [desteklenen bölgelerden](vminsights-enable-overview.md#log-analytics)birine ait olmalıdır.

İzlemeyi etkinleştirdikten sonra, VM 'nin sistem durumu ölçümlerini görüntüleyebilmeniz için yaklaşık 10 dakika beklemeniz gerekebilir.

![Dağıtım işlemi izlemeyi VM'ler için Azure İzleyicisi'ni etkinleştirme](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Tek bir sanal makine ölçek kümesi için izlemeyi etkinleştir

Azure sanal makine ölçek kümesini izlemeyi etkinleştirmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. **Sanal makine ölçek kümelerini**seçin.

3. Listeden bir sanal makine ölçek kümesi seçin.

4. Sanal makine ölçek kümesi sayfasında, **izleme** bölümünde Öngörüler ' i **(Önizleme)** seçin.

5. **Öngörüler (Önizleme)** sayfasında, mevcut bir Log Analytics çalışma alanını kullanmak istiyorsanız, açılır listeden seçin.

    Liste, varsayılan çalışma alanını ve VM 'nin abonelikte dağıtıldığı konumu önceden seçer. 

    ![Bir sanal makine ölçek kümesi için VM'ler için Azure İzleyici etkinleştir](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Sanal makine ölçek kümesinden izleme verilerini depolamak üzere yeni bir Log Analytics çalışma alanı oluşturmak için, bkz. [Log Analytics çalışma alanı oluşturma](../learn/quick-create-workspace.md). Log Analytics çalışma alanınız [desteklenen bölgelerden](vminsights-enable-overview.md#log-analytics)birine ait olmalıdır.

İzlemeyi etkinleştirdikten sonra, ölçek kümesi için izleme verilerini görüntüleyebilmeniz için yaklaşık 10 dakika beklemeniz gerekebilir.

>[!NOTE]
>Ölçek kümesi için el ile yükseltme modeli kullanıyorsanız, kurulumu tamamladıktan sonra örnekleri yükseltin. **Ayarlar** bölümünde, **örnekler** sayfasından yükseltmeleri başlatabilirsiniz.

![Dağıtım işlemi izlemeyi VM'ler için Azure İzleyicisi'ni etkinleştirme](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

VM 'niz veya sanal makine ölçek kümesi için izlemeyi etkinleştirmiş olduğunuza göre, izleme bilgileri VM'ler için Azure İzleyici analiz için kullanılabilir. 

## <a name="next-steps"></a>Sonraki adımlar

* Bulunan uygulama bağımlılıklarını görüntülemek için bkz. [VM'ler için Azure izleyici haritasını kullanma](vminsights-maps.md). 
* Performans sorunlarını, genel kullanımı ve sanal makinenizin performansını belirlemek için bkz. [Azure VM performansını görüntüleme](vminsights-performance.md).