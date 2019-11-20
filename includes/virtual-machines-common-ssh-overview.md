---
title: include dosyası
description: include dosyası
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168657"
---
## <a name="overview-of-ssh-and-keys"></a>SSH ve anahtarlara genel bakış

[SSH](https://www.ssh.com/ssh/) , güvenli olmayan bağlantılarda güvenli oturum açma işlemlerinin yapılmasına izin veren şifreli bir bağlantı protokolüdür. SSH, Azure 'da barındırılan Linux VM 'Leri için varsayılan bağlantı protokolüdür. SSH 'in kendisi şifreli bir bağlantı sağlamasına karşın, SSH bağlantılarıyla parolaların kullanılması, VM 'nin deneme yanılma saldırılarına veya parolaların tahmin edilmeye karşı savunmasız kalmasına neden olabilir. SSH kullanarak bir VM 'ye bağlanmak için daha güvenli ve tercih edilen bir yöntem, *SSH anahtarları*olarak da bilinen bir ortak özel anahtar çifti kullanmaktır. 

* *Ortak anahtar* , Linux sanal makinenize veya ortak anahtar şifrelemesi ile kullanmak istediğiniz başka bir hizmete yerleştirilir.

* *Özel anahtar* yerel sisteminizde kalır. Bu özel anahtarı koruyun. Özel anahtarı paylaşmayın.

Linux VM 'nize bağlanmak için bir SSH istemcisi kullandığınızda (ortak anahtara sahiptir), uzak VM, özel anahtara sahip olduğundan emin olmak için istemciyi sınar. İstemci özel anahtara sahipse, VM 'ye erişim izni verilir. 

Kuruluşunuzun güvenlik ilkelerine bağlı olarak, birden fazla Azure VM ve hizmetine erişmek için tek bir genel özel anahtar çiftini yeniden kullanabilirsiniz. Erişmek istediğiniz her VM veya hizmet için ayrı bir anahtar çiftine gerek yoktur. 

Ortak anahtarınız herkese paylaşılabilir, ancak yalnızca sizin (veya yerel güvenlik altyapınız) özel anahtarınıza sahip olmalıdır.