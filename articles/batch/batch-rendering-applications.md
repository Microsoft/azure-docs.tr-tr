---
title: Uygulamaları işleme
description: Azure Batch ile herhangi bir işleme uygulaması kullanmak mümkündür. Ancak, Azure Market VM görüntüleri önceden yüklenmiş ortak uygulamalar ile kullanılabilir.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: c98e2e0a81051dad47c201de9eda9f89cc311cf2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496652"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>Toplu işleme VM görüntülerinde önceden yüklenmiş uygulamalar

Azure Batch ile herhangi bir işleme uygulaması kullanmak mümkündür. Ancak, Azure Market VM görüntüleri önceden yüklenmiş ortak uygulamalar ile kullanılabilir.

Uygun olduğunda, önceden yüklenmiş işleme uygulamaları için kullanım için ödeme lisansı kullanılabilir. Bir Batch havuzu oluşturulduğunda, gerekli uygulamalar belirtilebilir ve her iki VM ve uygulama maliyeti de dakika başına faturalandırılır. Uygulama fiyatları [Azure Batch fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)listelenir.

Bazı uygulamalar yalnızca Windows 'u destekler, ancak çoğu hem Windows hem de Linux üzerinde desteklenir.

> [!IMPORTANT]
> İşlenen VM görüntüleri ve kullanım için ödeme lisansı [kullanım dışı bırakılmıştır ve 29 şubat 2024 tarihinde kullanımdan](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/)kaldırılacaktır. Toplu Işi işlemek için kullanmak üzere [Özel BIR VM görüntüsü ve standart uygulama lisanslama kullanılmalıdır.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

## <a name="applications-on-latest-centos-7-rendering-image"></a>En son CentOS 7 işleme görüntüsündeki uygulamalar

Aşağıdaki liste CentOS işleme görüntüsü olan sürüm 1.2.0 için geçerlidir.

* Autodesk Maya g/ç 2020 güncelleştirme 4,6
* Maya 2020 için Autodesk Arnold (Arnold sürüm 6.2.0.0) MtoA-4.2.0-2020
* Chaos Group V-Ray for Maya 2020 (sürüm 5.00.21)
* Blender (2,80)
* AZ 10

## <a name="applications-on-latest-windows-server-rendering-image"></a>En son Windows Server işleme görüntüsündeki uygulamalar

Aşağıdaki liste Windows Server işleme görüntüsü, sürüm 1.5.0 için geçerlidir.

* Autodesk Maya g/ç 2020 güncelleştirme 4,4
* Autodesk 3ds Max g/ç 2021 güncelleştirme 3
* Maya 2020 için Autodesk Arnold (Arnold sürüm 6.1.0.1) MtoA-4.1.1.1-2020
* 3ds Max 2021 için Autodesk Arnold (Arnold sürüm 6.1.0.1) MAXtoA-4.2.2.20-2021
* Chaos Group V-Ray for Maya 2020 (sürüm 5.00.21)
* Chaos Group V-Ray For 3ds Max 2021 (sürüm 5.00.05)
* Blender (2.79)
* Blender (2,80)
* AZ 10

> [!IMPORTANT]
> [Azure Batch uzantısı şablonlarının](https://github.com/Azure/batch-extension-templates)dışında Maya ile V-Ray çalıştırmak için, `vrayses.exe` işlemeyi çalıştırmadan önce ' yi başlatın. vrayses.exe şablonların dışında başlatmak için aşağıdaki komutu kullanabilirsiniz `%MAYA_2020%\vray\bin\vrayses.exe"` .
>
> Bir örnek için GitHub 'da [Maya ve V-Ray şablonunun](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) başlangıç görevine bakın.

## <a name="applications-on-previous-windows-server-rendering-images"></a>Önceki Windows Server işleme görüntülerinde uygulamalar

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

## <a name="applications-on-previous-centos-rendering-images"></a>Önceki CentOS işleme görüntülerinde uygulamalar

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

## <a name="next-steps"></a>Sonraki adımlar

Oluşturma VM görüntülerini kullanmak için bir havuz oluşturulduğunda havuz yapılandırmasında belirtilmesi gerekir; [işleme Için Batch havuzu özelliklerine](./batch-rendering-functionality.md)bakın.
