---
title: Sistem durumu kapsayıcısı için Docker çekme
titleSuffix: Azure Cognitive Services
description: Sistem durumu kapsayıcısı için Metin Analizi Docker Pull komutu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/25/2021
ms.author: aahi
ms.openlocfilehash: 8595af7f46b63f9991d6a02279ccad76afb38311
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089720"
---
Sistem durumu genel önizlemesi için Metin Analizi erişim istemek için bilişsel [Hizmetler istek formunu](https://aka.ms/csgate) doldurun ve iletin.  Bu uygulama hem kapsayıcı hem de barındırılan Web API genel önizlemesi için geçerlidir.
Form, siz, şirketiniz ve kapsayıcısını kullanacağınız kullanıcı senaryosu hakkında bilgi ister. Formu gönderdikten sonra, Azure bilişsel hizmetler ekibi, özel kapsayıcı kayıt defterine erişim ölçütlerini karşıladığınızdan emin olmak için bunu inceler.

> [!IMPORTANT]
> * Formunda, bir Azure aboneliği KIMLIĞIYLE ilişkili bir e-posta adresi kullanmanız gerekir.
> * Kapsayıcıyı çalıştırmak için kullandığınız Metin Analizi kaynak (Faturalandırma bitiş noktası ve apikey), onaylanan Azure abonelik KIMLIĞIYLE oluşturulmuş olmalıdır. 
> * Microsoft 'tan uygulamanızın durumuyla ilgili güncelleştirmeler için e-postanızı (hem gelen kutusu hem de önemsiz klasörler) denetleyin.
> * Bu kapsayıcı URL 'SI değişti, aşağıdaki örneklere bakın. Kapsayıcı, `containerpreview.azurecr.io` 26 nisan 2021 tarihinden itibaren indirilmek üzere kullanılamayacak.


Onaylandığında, [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Microsoft ortak kapsayıcı kayıt defterinden bu kapsayıcı görüntüsünü indirmek için komutunu kullanın.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```
