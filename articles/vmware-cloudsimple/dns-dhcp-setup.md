---
title: CloudSimple tarafından Azure VMware çözümü-özel bulut için iş yükünü ayarlama DNS ve DHCP
description: CloudSimple özel bulut ortamınızda çalışan uygulamalar ve iş yükleri için DNS ve DHCP ayarlamayı açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ce2394805bfc302179da807c798445c30ce9daff
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619553"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>CloudSimple özel bulutunuzda DNS ve DHCP uygulamalarını ve iş yüklerini ayarlama

Özel bir bulut ortamında çalışan uygulamalar ve iş yükleri, arama ve IP adresi ataması için ad çözümlemesi ve DHCP hizmetleri gerektirir.  Bu hizmetleri sağlamak için uygun bir DHCP ve DNS altyapısı gereklidir.  Bu hizmetleri özel bulut ortamınızda sağlamak için bir sanal makine yapılandırabilirsiniz.  

## <a name="prerequisites"></a>Önkoşullar

* VLAN yapılandırılmış bir dağıtılmış bağlantı noktası grubu
* Kurulumu şirket içi veya Internet tabanlı DNS sunucularına yönlendirin
* Bir sanal makine oluşturmak için sanal makine şablonu veya ISO

## <a name="linux-based-dns-server-setup"></a>Linux tabanlı DNS sunucusu kurulumu

Linux, DNS sunucularını ayarlamak için çeşitli paketler sunar.  Aşağıda, bir açık kaynak bağlama DNS sunucusu ayarlamaya yönelik yönergelerden oluşan [Digitalocea 'dan örnek bir kurulum](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7) verilmiştir.

## <a name="windows-based-setup"></a>Windows tabanlı kurulum

Bu Microsoft konuları, bir Windows Server 'ı bir DNS sunucusu olarak ve bir DHCP sunucusu olarak ayarlamayı açıklamaktadır.

* [DNS sunucusu olarak Windows Server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [DHCP sunucusu olarak Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
