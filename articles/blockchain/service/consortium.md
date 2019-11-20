---
title: Azure blok zinciri hizmeti Consortium
description: Azure blok zinciri hizmeti 'nin nasıl özel bir konsorsiyum kullandığını anlama
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: ddb3f02662c0c71ebc90e1a740b4068d6fbcded4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577502"
---
# <a name="azure-blockchain-service-consortium"></a>Azure blok zinciri hizmeti Consortium

Azure blok zinciri hizmeti 'ni kullanarak, her bir blok zinciri ağının ağdaki belirli katılımcılarla sınırlı olabilecek özel konsorsiyum blok ağları oluşturabilirsiniz. Yalnızca özel konsorsiyum blok zinciri ağındaki katılımcılar, blok zincirini görüntüleyebilir ve bunlarla etkileşime geçebilir. Azure blok zinciri hizmeti 'nde bulunan konsorsiyum ağları, iki tür üye katılımcı rolü içerebilir:

* Konsorsiyumun yönetim eylemlerini gerçekleştirebileceği ve blok zinciri işlemlerine katılabilen, **yönetici** ayrıcalıklı katılımcılar.

* **Kullanıcı** katılımcıları, herhangi bir konsorsiyum yönetim eylemi alamaz ancak blok zinciri işlemlerine katılabilir.

Konsorsiyum ağları katılımcı rollerinin bir karışımı olabilir ve rastgele sayıda rol türüne sahip olabilir. En az bir yönetici olması gerekir.

Aşağıdaki diyagramda birden çok katılımcı içeren bir konsorsiyum ağı gösterilmektedir:

![Özel konsorsiyum ağ diyagramı](./media/consortium/network-diagram.png)

Azure blok zinciri hizmeti 'nde konsorsiyum yönetimi ile, konsorsiyum ağı 'ndaki katılımcıları yönetebilirsiniz. Konsorsiyumun yönetimi, ağın konsensus modelini temel alır. Geçerli önizleme sürümünde Azure blok zinciri hizmeti, konsorsiyum yönetimi için merkezi bir konsensus modeli sağlar. Bir yönetim rolü olan tüm ayrıcalıklı katılımcılar, bir ağdan katılımcı ekleme veya kaldırma gibi bir konsorsiyum yönetim eylemi gerçekleştirebilir.

## <a name="roles"></a>Roller

Bir konsorsiyumun katılımcıları bireyler veya kuruluşlar olabilir ve bir kullanıcı rolü ya da yönetici rolü atanabilir. Aşağıdaki tabloda, iki rol arasındaki üst düzey farklılıklar listelenmektedir:

| Eylem | Kullanıcı rolü | Yönetici rolü
|--------|:----:|:------------:|
| Yeni üye Oluştur | Evet | Evet |
| Yeni üyeler davet et | Hayır | Evet |
| Üye katılımcı rolünü ayarlama veya değiştirme | Hayır | Evet |
| Üye görünen adını değiştir | Yalnızca kendi üyesi için | Yalnızca kendi üyesi için |
| Üyeleri kaldır | Yalnızca kendi üyesi için | Evet |
| Blok zinciri işlemlerine katılın | Evet | Evet |

### <a name="user-role"></a>Kullanıcı rolü

Kullanıcılar, yönetici özelliği olmayan konsorsiyum katılımcıları. Bu kişiler, konsorsiyumyla ilgili üyelerin yönetilmesine katılamaz. Kullanıcılar, üye görünen adlarını değiştirebilir ve kendilerini bir konsorsiyumdan kaldırabilir.

### <a name="administrator"></a>Yönetici

Bir yönetici, konsorsiyumun içindeki üyeleri yönetebilir. Bir yönetici, konsorsiyumun içinde üye davet edebilir, üye kaldırabilir veya üye rollerini güncelleştirebilir.
Bir konsorsiyumun içinde her zaman en az bir yönetici olması gerekir. Son yöneticinin bir konsorsiyumdan çıkmadan önce yönetici rolü olarak başka bir katılımcı belirtmesi gerekir.

## <a name="managing-members"></a>Üyeleri yönetme

Yalnızca yöneticiler diğer katılımcıları konsorsiya davet edebilir. Yöneticiler Azure abonelik KIMLIKLERINI kullanarak katılımcıları davet edebilir.

Davet edildikten sonra katılımcılar, Azure blok zinciri hizmeti 'nde yeni bir üye dağıtarak blok zinciri konsorsiyumunun katılılabilmesi için kullanılabilir. Davet eden Konsorsiyumu görüntülemek ve katmak için, ağ yöneticisi tarafından yapılan davette kullanılan Azure abonelik KIMLIĞINI belirtmeniz gerekir.

Yöneticiler, diğer yöneticiler dahil olmak üzere konsorsiyumdan herhangi bir katılımcıyı kaldırabilir. Üyeler kendilerini bir konsorsiyumun içinden kaldırabilir.

## <a name="consortium-management-smart-contract"></a>Konsorsiyum yönetimi akıllı sözleşmesi

Azure blok zinciri hizmeti 'nde konsorsiyum yönetimi, konsorsiyum yönetimi akıllı sözleşmeleri aracılığıyla yapılır. Yeni bir blok zinciri üyesi dağıttığınızda akıllı sözleşmeler düğümlere otomatik olarak dağıtılır.

Kök Consortium yönetimi akıllı sözleşmesinin adresi Azure portal görüntülenebilir. **Rootcontract adresi** blok zinciri üyesinin genel bakış bölümünde bulunur.

![RootContract adresi](./media/consortium/rootcontract-address.png)

Consortium Management [PowerShell modülünü](manage-consortium-powershell.md), Azure Portal veya doğrudan akıllı sözleşme aracılığıyla Azure blok zinciri hizmeti tarafından oluşturulan Ethereum hesabını kullanarak, konsorsiyum yönetimi akıllı anlaşmayla etkileşime geçebilirsiniz.

## <a name="ethereum-account"></a>Ethereum hesabı

Bir üye oluşturulduğunda, bir Ethereum hesap anahtarı oluşturulur. Azure blok zinciri hizmeti, konsorsiyum yönetimiyle ilgili işlemler oluşturmak için anahtarı kullanır. Ethereum hesap anahtarı, Azure blok zinciri hizmeti tarafından otomatik olarak yönetilir.

Üye hesabı Azure portal görüntülenebilir. Üye hesabı blok zinciri üyesinin genel bakış bölümünde bulunur.

![Üye hesabı](./media/consortium/member-account.png)

Üye hesabınıza tıklayıp yeni bir parola girerek, Ethereum hesabınızı sıfırlayabilirsiniz. Ethereum hesap adresi ve parola sıfırlanır.  

## <a name="next-steps"></a>Sonraki adımlar

[PowerShell kullanarak Azure blok zinciri hizmeti 'nde üyeleri yönetme](manage-consortium-powershell.md)
