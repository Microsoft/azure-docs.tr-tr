---
title: Var olan sanal ağa dağıt
description: Yönetilen uygulamanızın kullanıcılarının var olan bir sanal ağı seçmesini nasıl etkinleştirtireceğinizi açıklar. Sanal ağ, yönetilen uygulamanın dışında olabilir.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84261292"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Azure yönetilen uygulamalarla mevcut sanal ağı kullan

Bu makalede, tüketicinin aboneliğindeki mevcut bir sanal ağla tümleşen bir Azure yönetilen uygulamasının nasıl tanımlanacağı gösterilmektedir. Yönetilen uygulama, tüketiciye yeni bir sanal ağ oluşturulup oluşturulmayacağını veya var olan bir ağ oluşturma kararı verir. Mevcut sanal ağ, yönetilen kaynak grubunun dışında olabilir.

## <a name="main-template"></a>Ana şablon

İlk olarak, dosyasında **mainTemplate.js** bakalım. Bir sanal makineyi ve ilişkili kaynaklarını dağıtmaya yönelik tüm şablon aşağıda gösterilmiştir. Daha sonra, var olan bir sanal ağı kullanmayla ilgili olan şablonun parçalarını daha yakından inceleyebilirsiniz.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

Sanal ağın [koşullu olarak dağıtıldığına](../templates/conditional-resource-deployment.md)dikkat edin. Tüketici, yeni bir oluşturma veya var olan bir sanal ağın kullanılıp kullanılmayacağını belirten bir parametre değeri geçirir. Tüketici yeni bir sanal ağ seçerse, kaynak dağıtılır. Aksi takdirde, dağıtım sırasında kaynak atlanır.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

Sanal ağ KIMLIĞI değişkeninin iki özelliği vardır. Bir özellik, yeni bir sanal ağ dağıtıldığında kaynak KIMLIĞINI döndürür. Diğer özellik, var olan bir sanal ağ kullanıldığında kaynak KIMLIĞINI döndürür. Var olan sanal ağın kaynak KIMLIĞI, sanal ağı içeren kaynak grubunun adını içerir.

Alt ağ KIMLIĞI, sanal ağ KIMLIĞI değerinden oluşturulur. Bu değer, tüketicilerle eşleşen değeri kullanır.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

Ağ arabirimi alt ağ KIMLIĞI değişkenine ayarlanır.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>UI tanımı

Şimdi dosyada **createUiDefinition.js** bakalım. Dosyanın tamamı:

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

Dosya bir sanal ağ öğesi içerir.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

Bu öğe, tüketicinin yeni veya var olan bir sanal ağ seçmesini sağlar.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="Yeni veya var olan sanal ağ":::

Çıktılarında, tüketicinin yeni veya mevcut bir sanal ağ seçmediğini belirten bir değer dahil edersiniz. Ayrıca, yönetilen bir kimlik değeri de vardır.

> [!NOTE]
> Yönetilen kimliğin çıkış değeri **Managedıdentity** olarak adlandırılmalıdır.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>Sonraki adımlar

UI tanım dosyası oluşturma hakkında daha fazla bilgi için bkz. [ Azure yönetilen uygulamanın oluşturma deneyimi içinCreateUiDefinition.js](create-uidefinition-overview.md).
