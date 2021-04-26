---
title: VMware HCX ağ kesimleri
description: VMware HCX için dört ağ gerekir.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f2f5597471355bf4d74be7fbe370e6fa58770bf1
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251626"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Şirket içinde VMware HCX ağ kesimlerini yapılandırmanın çeşitli yolları vardır. Aşağıda, pilot veya küçük bir üretim kullanım durumunu destekleyen basit bir yapılandırma özetlenmektedir.  Yüzlerce veya binlerce iş yükü için tasarlarken, geçişin ihtiyaçlarına bağlı olarak bu yapılandırmanın değiştirilmesi gerekebilir.  

VMware HCX dağıtımı için pilot veya küçük üretim kullanım durumunu desteklemeye hazırlanın, şunları yapın:

- **Yönetim ağı:** VMware HCX 'i şirket içinde dağıttığınızda, VMware HCX için bir yönetim ağı belirlemeniz gerekir.  Genellikle, şirket içi VMware kümeniz tarafından kullanılan aynı yönetim ağı vardır.  En azından, VMware HCX için bu ağ segmentinde **iki** IP 'yi tanımla. Pilot veya küçük kullanım durumunun ötesinde dağıtımınızın ölçeğine bağlı olarak daha büyük sayılara ihtiyacınız bulunabilir.

  > [!NOTE]
  > Şirket içi VMware kümesi için kullanılan yönetim ağını kullanmak yerine, büyük ortamlara hazırlanma, yeni bir/26 ağı oluşturun ve bu ağı şirket içi VMware kümenize bir bağlantı noktası grubu olarak sunun.  Daha sonra, en fazla 10 hizmet kafesi ve 60 ağ Extender 'ı (hizmet ağı başına-1) oluşturabilirsiniz. Azure VMware Çözüm özel bulutlarını kullanarak her ağ genişletici için **sekiz** ağ uzatabilirsiniz.
  >

- **Yukarı bağlantı ağı:** VMware HCX 'i şirket içinde dağıttığınızda, VMware HCX için bir yukarı bağlantı belirlemeniz gerekir. Yönetim ağı için kullanacağınız ağı kullanın. 

- **vMotion ağı:** VMware HCX 'i şirket içinde dağıttığınızda, VMware HCX için bir vMotion ağı belirlemeniz gerekir.  Genellikle, şirket içi VMware kümeniz tarafından vMotion için kullanılan ağ aynıdır.  En azından, VMware HCX için bu ağ segmentinde **iki** IP 'yi tanımla. Pilot veya küçük kullanım durumunun ötesinde dağıtımınızın ölçeğine bağlı olarak daha büyük sayılara ihtiyacınız bulunabilir.

   VMotion ağı dağıtılmış bir sanal anahtarda veya vSwitch0 üzerinde kullanıma sunulmalıdır. Aksi takdirde, ortamı sığacak şekilde değiştirin.

   > [!NOTE]
   > Birçok VMware ortamı, hiçbir sorun ortaya çıkan vMotion için yönlendirilmeyen ağ kesimleri kullanır.
  
- **Çoğaltma ağı:** VMware HCX 'i şirket içinde dağıttığınızda, bir çoğaltma ağı tanımlamanız gerekir. Yönetim ve yukarı bağlantı ağlarınız için kullandığınız ağı kullanın.  Şirket içi küme Konakları ayrılmış bir VMkernel ağı kullanıyorsa, bu ağ kesiminde **iki** IP adresi ayırın ve çoğaltma ağı Için çoğaltma vmkernel ağını kullanın.
