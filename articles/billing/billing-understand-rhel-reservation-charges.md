---
title: Red Hat rezervasyon planı indirimini ve kullanımını anlama - Azure | Microsoft Docs
description: Red Hat planı indirimlerinin sanal makinelerde Red Hat yazılımına nasıl uygulandığını öğrenin.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: cwatson
ms.openlocfilehash: 2dc0f9cbec96277e133f07d1c3846f2774add5a1
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71701966"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Red Hat Linux Enterprise yazılım rezervasyon planı indiriminin Azure için nasıl uygulandığını anlayın

Red Hat Linux planı satın almanızın ardından indirim, rezervasyonla eşleşen, dağıtılan Red Hat sanal makinelerine otomatik olarak uygulanır. Red Hat Linux planı, Red Hat yazılımını bir Azure sanal makinesinde çalıştırma maliyetini kapsar.

Doğru Red Hat Linux planını satın almak için hangi Red Hat sanal makinelerini çalıştırdığınızı ve bu sanal makinelerdeki vCPU sayısını anlamanız gerekir. Kullanım bilgilerini içeren CSV dosyanızdan, hangi planın satın alınacağını belirlemenize yardımcı olması için aşağıdaki bölümleri kullanın.

## <a name="discount-applies-to-different-vm-sizes"></a>Farklı sanal makine boyutları için indirim uygulanır

Ayrılmış Sanal Makine Örnekleri gibi Red Hat planı satın alımları da örnek boyutu esnekliği sunar. Başka bir deyişle, farklı bir vCPU sayısıyla bir sanal makine dağıttığınızda bile indiriminiz geçerli olur. Yazılım planı içindeki farklı sanal makine boyutları için indirim geçerlidir.

İndirim tutarı, aşağıdaki tablolarda listelenen orana bağlıdır. Bu oran, söz konusu gruptaki her bir ölçüm için ilgili ayak izini karşılaştırır. Bu oran, sanal makine vCPU’larına bağlıdır. Kaç tane sanal makine örneğinin Red Hat Linux planı indirimi aldığını hesaplamak için oran değerini kullanın.

Örneğin, 3 veya 4 vCPU içeren bir sanal makine için Red Hat Linux Enterprise Server planı satın alırsanız, bu rezervasyon için oran 2 olur. İndirim, şunlar için Red Hat yazılımı maliyetini kapsar:

- 1 veya 2 vCPU ile dağıtılan 2 sanal makine,
- 3 veya 4 vCPU ile dağıtılan 1 sanal makine,
- veya 5 ya da daha fazla vCPU ile dağıtılan bir sanal makinenin yaklaşık %77’si veya 0,77.

5 veya daha fazla vCPU için oran 2,6’dır. Bu nedenle 5 veya daha fazla vCPU içeren Red Hat rezervasyonu, yazılım maliyetini yalnızca bir kısmını kapsar; bu da yaklaşık %77’ye denk gelir.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>Satın almadan önce Red Hat sanal makine kullanımını anlayın

Aşağıdaki tablolarda, rezervasyon satın alabileceğiniz yazılım planları, bunların ilişkili kullanım ölçümleri ve her biri için oranlar gösterilmektedir.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure portalı market adları:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (en son lvm)

|Red Hat sanal makinesi | MeterId| Oran| Örnek sanal makine boyutu|
| -------| ------------------------| --- |--- |
|1-4 vCPU Sanal Makine Lisansı|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|1-4 vCPU Sanal Makine Lisansı|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|1-4 vCPU Sanal Makine Lisansı|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2,166666667|D8s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2,166666667|D8s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|037eddc0-fedd-4d73-b5d8-92fba9edb831|2,166666667|D8s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2,166666667|D8s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|794dcb90-0793-43e6-9909-70d29974e56d|2,166666667|D8s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2,166666667|D8s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|86c35ec3-0a48-426a-9625-22d80e6ea55b|2,166666667|D8s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2,166666667|D8s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2,166666667|D8s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2,166666667|D8s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2,166666667|D8s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|d09f877e-03b4-48b2-b11a-782b965cff19|2,166666667|D8s_v3|
|44 vCPU Sanal Makine Lisansı|6f44ae85-a70e-44be-83ec-153a0bc23979|2,166666667||
|60 vCPU Sanal Makine Lisansı|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2,166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>HA özellikli Red Hat Enterprise Linux for SAP

Azure portalı market adı:

|Red Hat sanal makinesi | MeterId | Oran|Örnek sanal makine boyutu|
| ------- | --- | ------------------------| --- | --- |
|1-4 vCPU Sanal Makine Lisansı |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|6dfb482b-23ea-487f-810c-e66360f025de|2,333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>HA özellikli Red Hat Enterprise Linux

Azure portalı market adları:

|Red Hat sanal makinesi | MeterId | Oran|Örnek sanal makine boyutu|
| ------- |------------------------| --- | --- |
|1-4 vCPU Sanal Makine Lisansı|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|5’ten fazla vCPU Sanal Makine Lisansı|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Azure portalı market adları:

- Red Hat Enterprise Linux 6.8 for SAP Business Apps
- Red Hat Enterprise Linux 7.3 for SAP Business Apps
- Red Hat Enterprise Linux 7.4 for SAP
- Red Hat Enterprise Linux 7.5 for SAP

|Red Hat sanal makinesi | MeterId | Oran|Örnek sanal makine boyutu|
| ------- |------------------------| --- |--- |
|1 vCPU Sanal Makine Lisansı|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|2 vCPU Sanal Makine Lisansı|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|4 vCPU Sanal Makine Lisansı|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|6 vCPU Sanal Makine Lisansı|69a140fa-e08e-415c-85f2-48158e4c73a0|2,166666667||
|8 vCPU Sanal Makine Lisansı|777a5a74-22d6-48c9-9705-ac38fe05a278|2,166666667|D8s_v3|
|12 vCPU Sanal Makine Lisansı|d6b8917a-5127-497a-9f48-1e959df98812|2,166666667||
|16 vCPU Sanal Makine Lisansı|03667e82-e009-425a-83f7-8ebddbca5af4|2,166666667|D16s_v3|
|20 vCPU Sanal Makine Lisansı|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2,166666667||
|24 vCPU Sanal Makine Lisansı|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2,166666667|ND24s|
|32 vCPU Sanal Makine Lisansı|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2,166666667|D32s_v3|
|40 vCPU Sanal Makine Lisansı|737142c3-8e4f-4fc1-aa41-05b1661edff8|2,166666667||
|44 vCPU Sanal Makine Lisansı|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2,166666667||
|60 vCPU Sanal Makine Lisansı|a22bb342-ba9a-4529-a178-39a92ce770b6|2,166666667||
|64 vCPU Sanal Makine Lisansı|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2,166666667|64s_v3|
|72 vCPU Sanal Makine Lisansı|14341b96-e92c-4dca-ba66-322c88a79aa6|2,166666667|F72s_v2|
|96 vCPU Sanal Makine Lisansı|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2,166666667||
|128 vCPU Sanal Makine Lisansı|9b198a68-974a-47a7-9013-49169ac0f2e9|2,166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Azure portalı market adları:

- Red Hat Enterprise Linux 6.7 for SAP HANA
- Red Hat Enterprise Linux 7.2 for SAP HANA
- Red Hat Enterprise Linux 7.3 for SAP HANA

|Red Hat sanal makinesi | MeterId | Oran|Örnek sanal makine boyutu|
| ------- |------------------------| --- |--- |
|1 vCPU Sanal Makine Lisansı|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|2 vCPU Sanal Makine Lisansı|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|4 vCPU Sanal Makine Lisansı|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|6 vCPU Sanal Makine Lisansı|a5963812-0f5a-4053-8ace-2b5babd15ed8|2,166666667||
|8 vCPU Sanal Makine Lisansı|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2,166666667|D8s_v3|
|12 vCPU Sanal Makine Lisansı|0e3bc72d-a888-4bcf-8437-119f763a3215|2,166666667||
|16 vCPU Sanal Makine Lisansı|b40e95d8-3176-42f0-967c-497785c031b2|2,166666667|D16s_v3|
|20 vCPU Sanal Makine Lisansı|81f34277-499d-40a3-a634-99adc08e2d45|2,166666667||
|24 vCPU Sanal Makine Lisansı|e03f1906-d35d-4084-b2cd-63281869c8ee|2,166666667|ND24s|
|32 vCPU Sanal Makine Lisansı|0a58c082-ceb8-4327-9b64-887c30dddb23|2,166666667|D32s_v3|
|40 vCPU Sanal Makine Lisansı|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2,166666667||
|44 vCPU Sanal Makine Lisansı|378b8125-d8a5-4e09-99bc-c1462534ffb0|2,166666667||
|60 vCPU Sanal Makine Lisansı|5d7db11a-54e9-404e-aaa8-509fac7c0638|2,166666667||
|64 vCPU Sanal Makine Lisansı|3c8157b2-a57d-45ce-ba02-bd86e9209795|2,166666667|64s_v3|
|72 vCPU Sanal Makine Lisansı|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2,166666667|F72s_v2|
|96 vCPU Sanal Makine Lisansı|b13895fc-0d06-4de9-b860-627c471cd247|2,166666667||
|128 vCPU Sanal Makine Lisansı|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2,166666667| M128ms|

## <a name="next-steps"></a>Sonraki adımlar

Rezervasyonlar hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure rezervasyonları nedir?](billing-save-compute-costs-reservations.md)
- [Azure rezervasyonları ile Red Hat yazılım planları için ön ödeme yapma](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure Ayrılmış VM Örnekleri ile Sanal Makinelere ön ödeme yapma](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure rezervasyonlarını yönetme](billing-manage-reserved-vm-instance.md)
- [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
- [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
