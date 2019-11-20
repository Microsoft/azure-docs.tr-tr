---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 7ac98f608cb1ca5bab590714623bc78036d46b81
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093142"
---
## <a name="configure-the-cloud-anchor-session"></a>Bulut bağlantısı oturumunu yapılandırma

Bulut bağlantısı oturumunu yapılandırmayı bir sonraki adımda ele alacağız. İlk satırda, oturumda algılayıcı sağlayıcısını ayarlayacağız. Bu andan itibaren, oturum sırasında oluşturduğumuz her bir tutturucu, bir algılayıcı okumaları kümesiyle ilişkilendirilir. Ardından, neredeyse cihaz bulma ölçütlerini örnekliyoruz ve uygulama gereksinimleriyle eşleşecek şekilde başlatın. Son olarak, neredeyse cihazımız ölçütlerimizden bir izleyici oluşturarak, bu oturum, bağlantıları bulmada, kullanıcıdan algılayıcı verileri kullanmasını sağlıyoruz.