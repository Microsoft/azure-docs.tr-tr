---
title: Uygulamaları işleme-Azure Batch
description: Önceden yüklenmiş toplu işleme uygulamaları
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 30a365b36645dfe79f35b4bb889c0a06535a4c73
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212716"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>VM görüntülerini işlemeye önceden yüklenmiş uygulamalar

Azure Batch ile herhangi bir işleme uygulaması kullanmak mümkündür. Ancak, Azure Market VM görüntüleri önceden yüklenmiş ortak uygulamalar ile kullanılabilir.

Uygun olduğunda, önceden yüklenmiş işleme uygulamaları için kullanım başına ödeme lisansı kullanılabilir. Bir Batch havuzu oluşturulduğunda, gerekli uygulamalar belirtilebilir ve her iki VM ve uygulama maliyeti de dakika başına faturalandırılır. Uygulama fiyatları [Azure Batch fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)listelenir.

Bazı uygulamalar yalnızca Windows 'u destekler, ancak çoğu hem Windows hem de Linux üzerinde desteklenir.

## <a name="applications-on-centos-7-rendering-images"></a>CentOS 7 işleme görüntülerinde uygulamalar

Aşağıdaki liste, CentOS 7,6, sürüm 1.1.6 işleme görüntüleri için geçerlidir.

* Autodesk Maya I/O 2017 Güncelleştirme 5 (cut 201708032230)
* Autodesk Maya g/ç 2018 güncelleştirme 2 (201711281015)
* Autodesk Maya g/ç 2019 güncelleştirme 1
* Maya 2017 için Autodesk Arnold (Arnold sürüm 5.3.1.1) MtoA-3.2.1.1-2017
* Maya 2018 için Autodesk Arnold (Arnold sürüm 5.3.1.1) MtoA-3.2.1.1-2018
* Maya 2019 için Autodesk Arnold (Arnold sürüm 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (sürüm 3.60.04)
* Chaos Group V-Ray for Maya 2018 (sürüm 3.60.04)
* Blender (2.68)
* Blender (2,8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>En son Windows Server 2016 üzerinde uygulamalar görüntü işleme

Aşağıdaki liste Windows Server 2016, sürüm 1.3.8 işleme görüntüleri için geçerlidir.

* Autodesk Maya I/O 2017 Güncelleştirme 5 (sürüm 17.4.5459)
* Autodesk Maya g/ç 2018 güncelleştirme 6 (sürüm 18.4.0.7622)
* Autodesk Maya g/ç 2019
* Autodesk 3ds Max I/O 2018 Güncelleştirme 4 (sürüm 20.4.0.4254)
* Autodesk 3ds Max g/ç 2019 güncelleştirme 1 (sürüm 21.2.0.2219)
* Autodesk 3ds Max g/ç 2020 güncelleştirme 2
* Maya 2017 için Autodesk Arnold (Arnold sürüm 5.3.0.2) MtoA-3.2.0.2-2017
* Maya 2018 için Autodesk Arnold (Arnold sürüm 5.3.0.2) MtoA-3.2.0.2-2018
* Maya 2019 için Autodesk Arnold (Arnold sürüm 5.3.0.2) MtoA-3.2.0.2-2019
* 3ds Max 2018 için Autodesk Arnold (Arnold sürüm 5.3.0.2) (sürüm 1.2.926)
* 3ds Max 2019 için Autodesk Arnold (Arnold sürüm 5.3.0.2) (sürüm 1.2.926)
* 3ds Max 2020 için Autodesk Arnold (Arnold sürüm 5.3.0.2) (sürüm 1.2.926)
* Chaos Group V-Ray for Maya 2017 (sürüm 4.12.01)
* Chaos Group V-Ray for Maya 2018 (sürüm 4.12.01)
* Chaos Group V-Ray for Maya 2019 (sürüm 4.04.03)
* Chaos Group V-Ray For 3ds Max 2018 (sürüm 4.20.01)
* Chaos Group V-Ray For 3ds Max 2019 (sürüm 4.20.01)
* Chaos Group V-Ray For 3ds Max 2020 (sürüm 4.20.01)
* Blender (2.79)
* Blender (2,80)
* AZ 10

> [!IMPORTANT]
> [Azure Batch uzantısı şablonlarının](https://github.com/Azure/batch-extension-templates)dışında Maya ile V-Ray çalıştırmak için, işlemeyi çalıştırmadan önce `vrayses.exe` ' yi başlatın. Vrayses. exe ' yi şablonların dışında başlatmak için aşağıdaki komutu `%MAYA_2017%\vray\bin\vrayses.exe"`kullanabilirsiniz.
>
> Bir örnek için GitHub 'da [Maya ve V-Ray şablonunun](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) başlangıç görevine bakın.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Önceki Windows Server 2016 işleme görüntüleri üzerinde uygulamalar

Aşağıdaki liste Windows Server 2016, sürüm 1.3.7 işleme görüntüleri için geçerlidir.

* Autodesk Maya I/O 2017 Güncelleştirme 5 (sürüm 17.4.5459)
* Autodesk Maya g/ç 2018 güncelleştirme 4 (sürüm 18.4.0.7622)
* Autodesk 3ds Max g/ç 2019 güncelleştirme 1 (sürüm 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Güncelleştirme 4 (sürüm 20.4.0.4254)
* Maya 2017 için Autodesk Arnold (Arnold sürüm 5.2.0.1) MtoA-3.1.0.1-2017
* Maya 2018 için Autodesk Arnold (Arnold sürüm 5.2.0.1) MtoA-3.1.0.1-2018
* 3ds Max 2018 için Autodesk Arnold (Arnold sürüm 5.0.2.4) (sürüm 1.2.926)
* 3ds Max 2019 için Autodesk Arnold (Arnold sürüm 5.0.2.4) (sürüm 1.2.926)
* Chaos Group V-Ray for Maya 2018 (sürüm 3.52.03)
* Chaos Group V-Ray For 3ds Max 2018 (sürüm 3.60.02)
* Chaos Group V-Ray for Maya 2019 (sürüm 3.52.03)
* Chaos Group V-Ray For 3ds Max 2019 (sürüm 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray For 3ds Max 2019 (sürüm 4.10.01), V-Ray üzerinde önemli değişiklikler sunar. Önceki sürümü (sürüm 3.60.02) kullanmak için Windows Server 2016, sürüm 1.3.2 işleme düğümlerini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Oluşturma VM görüntülerini kullanmak için bir havuz oluşturulduğunda havuz yapılandırmasında belirtilmesi gerekir; [işleme Için Batch havuzu özelliklerine](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools)bakın.
