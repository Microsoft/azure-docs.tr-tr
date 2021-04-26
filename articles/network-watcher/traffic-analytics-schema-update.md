---
title: Azure Trafik Analizi şema güncelleştirmesi-Mart 2020 | Microsoft Docs
description: Trafik Analizi şemasında yeni alanlarla örnek sorgular.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: d7c4f1853ff8dcb9249ab6ec4f536e1f8cfa10e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018233"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>Trafik Analizi şemasında yeni alanlarla örnek sorgular (Ağustos 2019 şema güncelleştirmesi)

[Trafik Analizi log şeması](./traffic-analytics-schema.md) şu yeni alanları içerir: **SrcPublicIPs_s**, **DestPublicIPs_s**, **NSGRule_s**. Yeni alanlar, kaynak ve hedef IP 'Ler hakkında bilgi sağlar ve sorguları basitleştirir.

Önümüzdeki birkaç ay içinde şu eski alanlar kullanım dışı olacaktır: **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d**.

Aşağıdaki üç örnek, eski alanların yeni olanlarla nasıl değiştirileceğini gösterir.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>Örnek 1: VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s ve PublicIPs_s alanları

Azucumhuriyeti ve ExternalPublic akışları için **FlowDirection_s** alanından kaynak ve hedef durumları çıkarması gerekmez. Ayrıca, bir ağ sanal gereci için **FlowDirection_s** alanını kullanmak uygun olmayabilir.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```

## <a name="example-2-nsgrules_s-field"></a>Örnek 2: NSGRules_s alanı

Eski alan şu biçimi kullandı:

`<Index value 0)>|<NSG_ RuleName>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>`

Artık bir ağ güvenlik grubu (NSG) genelinde verileri toplamıyoruz. Güncelleştirilmiş şemada, **NSGList_s** yalnızca bir NSG içerir. Ayrıca **Nsgrules** yalnızca bir kural içerir. Burada ve diğer alanlarda, örnekte gösterildiği gibi karmaşık biçimlendirmeyi kaldırdık.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3-flowcount_d-field"></a>Örnek 3: FlowCount_d alanı

NSG genelinde verileri kulüettiğimiz için **FlowCount_d** yalnızca şu şekilde olur:

**AllowedInFlows_d**  +  **DeniedInFlows_d**  +  **AllowedOutFlows_d**  +  **DeniedOutFlows_d**

Dört alandan yalnızca biri sıfır dışı olacaktır. Diğer üç alan sıfır olacaktır. Alanlar, akışın yakalandığı NIC 'de durum ve sayı belirtmek için doldurulur.

Bu koşulları göstermek için:

- Akışa izin verildiyse, "Izin verilen" önekli alanlardan biri doldurulur.
- Akış reddedildiyse, "reddedilen" önekli alanlardan biri doldurulur.
- Akış geldiği takdirde, "InFlows_d" soneki sabit alanlarından biri doldurulur.
- Akış giden bir ise, "OutFlows_d" sonekli alanlardan biri doldurulur.

Koşullara bağlı olarak, dört alandan hangisini doldurabileceğinizi biliyoruz.

## <a name="next-steps"></a>Sonraki adımlar

- Sık sorulan soruların yanıtlarını almak için [trafık ANALIZI SSS](traffic-analytics-faq.md)bölümüne bakın.
- İşlevlerle ilgili ayrıntıları görmek için bkz. [Trafik Analizi belgeleri](traffic-analytics.md).