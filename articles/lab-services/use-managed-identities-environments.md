---
title: DevTest Labs 'de ortam oluşturmak için Azure yönetilen kimliklerini kullanma | Microsoft Docs
description: Azure DevTest Labs 'de laboratuarda ortamları dağıtmak için Azure 'da Yönetilen kimlikler kullanmayı öğrenin.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931149"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Laboratuvar ortamında ortamları dağıtmak için Azure yönetilen kimliklerini kullanma 
Laboratuvar sahibi olarak, bir laboratuvarda ortamları dağıtmak için yönetilen bir kimlik kullanabilirsiniz. Bu özellik, ortamda Anahtar Kasası, paylaşılan görüntü galerileri ve ortamın kaynak grubu dışındaki ağlar gibi Azure kaynaklarına başvuru içeren veya bu kaynaklara sahip olan senaryolarda faydalıdır. Bu ortamın kaynak grubuyla sınırlı olmayan korumalı alan ortamları oluşturulmasına olanak sağlar.

> [!NOTE]
> Şu anda, laboratuvar başına tek bir kullanıcı tarafından atanan kimlik desteklenir. 

## <a name="prerequisites"></a>Önkoşullar
- [Azure Portal kullanarak, Kullanıcı tarafından atanan yönetilen kimlik için rol oluşturma, listeleme, silme veya atama](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Azure portalı kullanma
Bu bölümde, laboratuvar sahibi olarak, laboratuvara Kullanıcı tarafından yönetilen bir kimlik eklemek için Azure portal kullanın. 

1. Laboratuvar sayfasında **yapılandırma ve ilkeler**' i seçin. 
1. **Ayarlar** bölümünde **kimlik** ' i seçin.
1. Kullanıcı tarafından atanan bir kimlik eklemek için araç çubuğunda **Ekle** ' yi seçin. 
1. Önceden doldurulmuş bir açılan listeden bir **kimlik** seçin.
1. **Tamam**’ı seçin.

    ![Kullanıcı tarafından yönetilen kimlik Ekle](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. Kullanıcı tarafından yönetilen eklenen kimliği listede görürsünüz. 

    ![Listedeki Kullanıcı tarafından yönetilen kimlik](./media/use-managed-identities-environments/identity-in-list.png)

Bu kimlik kaydedildikten sonra laboratuvar, tüm laboratuar ortamlarını dağıtmakta bu kimliği kullanır. Ayrıca, listeden kimlik ' i seçerek Azure 'daki kimlik kaynağına erişebilirsiniz. 

Laboratuvara eklenen kimliğin, ortamın erişmesi gereken dış kaynaklar için izinleri olduğu sürece laboratuvar sahibinin bir ortamı dağıtmada özel bir şey yapması gerekmez. 

Laboratuvara atanan kullanıcı tarafından yönetilen kimliği değiştirmek için, önce laboratuvara eklenmiş olan kimliği kaldırın ve ardından laboratuvara bir tane ekleyin. Laboratuvara eklenen bir kimliği kaldırmak için... seçeneğini belirleyin **. (üç nokta)** ve **Kaldır**' a tıklayın. 

![Listedeki Kullanıcı tarafından yönetilen kimlik](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>API kullanma

1. Kimlik oluşturduktan sonra, bu kimliğin kaynak KIMLIĞI ' ni aklınızda edin. Aşağıdaki örnekteki gibi görünmelidir: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Aşağıdaki örneğe benzer şekilde laboratuvara yeni bir `ServiceRunner` kaynağı eklemek için bir PUT https yöntemi gerçekleştirin. Hizmet Çalıştırıcısı kaynağı, DevTest Labs 'de yönetilen kimlikleri yönetmek ve denetlemek için bir ara sunucu kaynağıdır. Hizmet Çalıştırıcısı adı geçerli bir ad olabilir, ancak yönetilen kimlik kaynağının adını kullanmanızı öneririz. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Bir örneği aşağıda verilmiştir: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Kullanıcı tarafından atanan kimlik laboratuvara eklendikten sonra, Azure DevTest Labs hizmeti Azure Resource Manager ortamları dağıturken bunu kullanacaktır. Örneğin, bir dış paylaşılan görüntü Galerisi görüntüsüne erişmek için Kaynak Yöneticisi şablonunuz olması gerekiyorsa, laboratuvara eklediğiniz kimliğin paylaşılan görüntü Galerisi kaynağı için gereken en düşük izinlere sahip olduğundan emin olun. 
