---
title: Azure Veri Gezgini kümeniz için doğru VM SKU 'sunu seçin
description: Bu makalede, Azure Veri Gezgini kümesi için en uygun SKU boyutunu seçme açıklanmaktadır.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 8293fd2d84189cc1f1df3564abbfdcbf86e3543e
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186739"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Azure Veri Gezgini kümeniz için doğru VM SKU 'sunu seçin 

Yeni bir küme oluşturduğunuzda veya değişen bir iş yükü için bir kümeyi iyileştirirken Azure Veri Gezgini, aralarından seçim yapmak için birden çok sanal makine (VM) SKU 'su sunar. VM 'Ler size herhangi bir iş yükü için en uygun maliyeti sunacak şekilde dikkatlice seçildi. 

Veri yönetimi kümesinin boyutu ve VM SKU 'SU Azure Veri Gezgini hizmeti tarafından tam olarak yönetilir. Bu, altyapının VM boyutu ve Alım iş yükü gibi etkenlere göre belirlenir. 

[Küme ölçeğini](manage-cluster-vertical-scaling.md)değiştirerek motor KÜMESI IÇIN VM SKU 'sunu dilediğiniz zaman değiştirebilirsiniz. İlk senaryoya uyan en küçük SKU boyutuyla başlamak en iyisidir. Küme, yeni VM SKU 'SU ile yeniden oluşturulduğu sırada kümenin ölçeğini 30 dakikaya varan bir süre içinde azaltmaya dikkat edin.

> [!TIP]
> İşlem için [ayrılmış örnekler (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) Azure Veri Gezgini kümesi için geçerlidir.  

Bu makalede çeşitli VM SKU seçenekleri açıklanmakta ve en iyi seçim yapmanıza yardımcı olabilecek teknik ayrıntılar sağlanmaktadır.

## <a name="select-a-cluster-type"></a>Küme türünü seçin

Azure Veri Gezgini iki tür küme sunar:

* **Üretim**: Üretim kümeleri, motor ve veri yönetimi kümeleri için iki düğüm içerir ve Azure Veri Gezgini [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)altında işletilebilir.

* **Geliştirme/test (SLA yok)** : Geliştirme ve test kümelerinde, altyapı kümesi için tek bir D11 v2 düğümü ve veri yönetimi kümesi için tek bir D1 düğümü vardır. Bu küme türü, düşük örnek sayısı ve motor işaretleme ücreti olmadığından en düşük maliyetli bir yapılandırmadır. Artıklık olmadığından, bu küme yapılandırması için SLA yoktur.

## <a name="sku-types"></a>SKU türleri

Bir Azure Veri Gezgini kümesi oluşturduğunuzda, planlı iş yükü için *en iyi* VM SKU 'sunu seçin. Aşağıdaki iki Azure Veri Gezgini SKU aileleri arasından seçim yapabilirsiniz:

* **D v2**: D SKU, işlem için iyileştirilmiştir ve iki şekilde sunulur:
    * VM 'nin kendisi
    * Premium Depolama diskleriyle paketlenmiş VM

* **LS**: L SKU, depolama için iyileştirilmiştir. Benzer şekilde ücretlendirilen D SKU 'sundan daha büyük bir SSD boyutu vardır.

Kullanılabilir SKU türleri arasındaki temel farklılıklar aşağıdaki tabloda açıklanmıştır:
 
| Öznitelik | D SKU 'SU | L SKU 'SU |
|---|---|---
|**Küçük SKU 'Lar**|En küçük boyut, iki çekirdekli D11|Dört çekirdekle en az boyut L4 |
|**Kullanılabilirlik**|Tüm bölgelerde kullanılabilir (DS + PS sürümü daha sınırlı kullanılabilirliğe sahiptir)|Birkaç bölgede kullanılabilir |
|**Çekirdek başına&nbsp;GB başına maliyet**|D SKU 'SU ile yüksek, DS + PS sürümü ile düşük|Kullandıkça öde seçeneği ile en düşük |
|**Ayrılmış örnekler (RI) fiyatlandırması**|Yüksek indirim (üç yıllık&nbsp;taahhüt için yüzde 55 üzerinden)|Düşük indirim (üç&nbsp;yıllık taahhüt için yüzde 20) |  

## <a name="select-your-cluster-vm"></a>Küme VM 'nizi seçme 

Küme VM 'nizi seçmek için [Dikey ölçeklendirmeyi yapılandırın](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Arasından seçim yapabileceğiniz çeşitli VM SKU seçenekleri sayesinde, senaryonuza yönelik performans ve etkin önbellek gereksinimlerinin maliyetlerini iyileştirebilirsiniz. 
* Yüksek bir sorgu birimi için en iyi performansa ihtiyaç duyuyorsanız ideal SKU, işlem için iyileştirilmiş olmalıdır. 
* Görece daha düşük sorgu yüküne sahip büyük hacimlerde veri sorgulaması yapmanız gerekiyorsa, depolama için iyileştirilmiş SKU, maliyetleri azaltmaya ve yine de mükemmel performans sağlamaya yardımcı olabilir.

Küçük SKU 'Lar için küme başına örnek sayısı sınırlı olduğundan, RAM daha büyük olan büyük VM 'Lerin kullanılması tercih edilir. RAM kaynağına daha fazla talep döndüren bazı sorgu türleri için, kullanılan `joins`sorgular gıbı daha fazla RAM gerekir. Bu nedenle, ölçekleme seçeneklerini düşünürken daha fazla örnek ekleyerek ölçeği genişletmek yerine daha büyük bir SKU 'ya ölçeklendirmenizi öneririz.

## <a name="vm-options"></a>VM seçenekleri

Azure Veri Gezgini kümesi VM 'lerinin teknik belirtimleri aşağıdaki tabloda açıklanmıştır:

|**Name**| **Kategori** | **SSD boyutu** | **Sayısı** | **KOÇ** | **Premium depolama diskleri (1&nbsp;TB)**| **Küme başına en az örnek sayısı** | **Küme başına en fazla örnek sayısı**
|---|---|---|---|---|---|---|---
|D11 v2| işlem için iyileştirilmiş | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1\. | 8 (1 olan geliştirme ve test SKU 'SU hariç)
|D12 v2| işlem için iyileştirilmiş | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| işlem için iyileştirilmiş | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1000
|D14 v2| işlem için iyileştirilmiş | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| depolama ile iyileştirilmiş | 1&nbsp;TB | 8 | 56&nbsp;GB | 1\. | 2 | 1000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| depolama ile iyileştirilmiş | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| depolama ile iyileştirilmiş | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| depolama ile iyileştirilmiş | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1000
|L4s v1| depolama ile iyileştirilmiş | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| depolama ile iyileştirilmiş | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1000
|L16s_1| depolama ile iyileştirilmiş | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1000

* Azure Veri Gezgini [Listsku 'larını](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)kullanarak bölge BAŞıNA GÜNCELLEŞTIRILMIŞ VM SKU listesini görüntüleyebilirsiniz. 
* [Çeşitli SKU 'lar](/azure/virtual-machines/windows/sizes)hakkında daha fazla bilgi edinin. 

## <a name="next-steps"></a>Sonraki adımlar

* Farklı gereksinimlere bağlı olarak, sanal makine SKU 'sunu değiştirerek motor kümesini istediğiniz zaman [ölçeklendirebilir veya](manage-cluster-vertical-scaling.md) azaltabilirsiniz. 

* Değişiklik taleplerine bağlı olarak kapasiteyi değiştirmek için motor kümesi boyutunu [ölçeklendirebilir veya ölçeğini](manage-cluster-horizontal-scaling.md) değiştirebilirsiniz.

