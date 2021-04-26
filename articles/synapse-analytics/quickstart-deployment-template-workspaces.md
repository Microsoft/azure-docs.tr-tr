---
title: 'Hızlı başlangıç: Azure SYNAPSE çalışma alanı Azure Resource Manager şablonu oluşturma'
description: Azure Resource Manager şablonu kullanarak bir Synapse çalışma alanı oluşturmayı öğrenin (ARM şablonu).
services: azure-resource-manager
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 7317b7f51c6d0f9d72e3aad81794a569276d2145
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566129"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak Azure SYNAPSE çalışma alanı oluşturma

Bu Azure Resource Manager şablonu (ARM şablonu), temel alınan Data Lake Storage bir Azure SYNAPSE çalışma alanı oluşturur. Azure SYNAPSE çalışma alanı, Azure SYNAPSE Analytics 'teki analiz işlemlerine yönelik güvenli kılınabilir bir işbirliği sınırıdır.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure 'a dağıtma 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

**Görselleştir** bağlantısını seçerek şablonu gözden geçirebilirsiniz. Sonra **Şablonu Düzenle**' yi seçin.

[![Görselleştirme](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

Şablon iki kaynağı tanımlar:

- Depolama hesabı
- Çalışma alanı

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure 'da oturum açmak ve şablonu açmak için aşağıdaki görüntüyü seçin. Bu şablon bir Synapse çalışma alanı oluşturur.

   [![Azure 2 ' ye dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Aşağıdaki değerleri girin veya güncelleştirin:

   - **Abonelik**: bir Azure aboneliği seçin.
   - **Kaynak grubu**: **Yeni oluştur** ' u seçin ve kaynak grubu için benzersiz bir ad girin ve **Tamam**' ı seçin. Yeni bir kaynak grubu, kaynak temizlemeyi kolaylaştırır.
   - **Bölge**: bir bölge seçin.  Örneğin **Orta ABD**.
   - **Ad**: çalışma alanınız için bir ad girin.
   - **SQL Yöneticisi oturum açma**: SQL Server için yönetici kullanıcı adını girin.
   - **SQL yönetici parolası**: SQL Server için yönetici parolasını girin.
   - **Etiket değerleri**: varsayılanı kabul edin.
   - **Gözden geçirin ve oluşturun**: seçin.
   - **Oluştur**: seç.

## <a name="next-steps"></a>Sonraki adımlar

Azure SYNAPSE Analytics ve Azure Resource Manager hakkında daha fazla bilgi edinmek için aşağıdaki makalelere devam edin.

- [Azure SYNAPSE Analytics 'e genel bakış](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) konusunu okuyun
- [Azure Resource Manager](../azure-resource-manager/management/overview.md) hakkında daha fazla bilgi edinin
- [İlk ARM şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
