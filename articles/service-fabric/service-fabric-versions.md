---
title: Azure Service Fabric desteklenen küme sürümleri | Microsoft Docs
description: Azure Service Fabric 'de küme sürümleri hakkında bilgi edinin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2019
ms.author: atsenthi
ms.openlocfilehash: 6b29eb31f5d425fc063c8c42e7911272e63167c8
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331094"
---
# <a name="supported-service-fabric-versions"></a>Desteklenen Service Fabric sürümleri

Kümenizin her zaman desteklenen bir Azure Service Fabric sürümünü çalıştırdığından emin olun. Yeni bir Service Fabric sürümünün yayınlanmasıyla ilgili en az 60 gün sonra, önceki sürüm için destek sona erer. [Service Fabric ekip bloguna](https://azure.microsoft.com/updates/?product=service-fabric)yeni yayınlar hakkında duyurular bulacaksınız.

Kümenizin desteklenen bir Service Fabric sürümünü çalıştırmasını sağlama hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

- [Azure Service Fabric kümesini yükseltme](service-fabric-cluster-upgrade.md)
- [Tek başına Windows Server kümenizde çalışan Service Fabric sürümünü yükseltme](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Desteklenen sürümler

Aşağıdaki tabloda Service Fabric sürümleri ve destek bitiş tarihleri listelenmektedir.

| Kümede Service Fabric çalışma zamanı | Doğrudan küme sürümünden yükseltebilir |Uyumlu SDK veya NuGet paketi sürümü | Destek sonu |
| --- | --- |--- | --- |
| 5\.3.121 önceki tüm küme sürümleri | 5.1.158.* |Sürüm 2,3 ' den küçük veya buna eşit |20 Ocak 2017 |
| 5,3. * | 5.1.158.* |Sürüm 2,3 ' den küçük veya buna eşit |24 Şubat 2017 |
| 5,4. * | 5.1.158.* |Sürüm 2,4 ' den küçük veya buna eşit |10 Mayıs 2017       |
| 5,5. * | 5.4.164.* |Sürüm 2,5 ' den küçük veya buna eşit |10 Ağustos 2017    |
| 5,6. * | 5.4.164.* |Sürüm 2,6 ' den küçük veya buna eşit |13 Ekim 2017   |
| 5,7. * | 5.4.164.* |Sürüm 2,7 ' den küçük veya buna eşit |15 Aralık 2017  |
| 6,0. * | 5.6.205.* |Sürüm 2,8 ' den küçük veya buna eşit |30 Mart 2018     |
| 6,1. * | 5.7.221.* |Sürüm 3,0 ' den küçük veya buna eşit |15 Temmuz 2018      |
| 6,2. * | 6.0.232.* |Sürüm 3,1 ' den küçük veya buna eşit |26 Ekim 2018   |
| 6,3. * | 6.1.480.* |Sürüm 3,2 ' den küçük veya buna eşit |31 Mart 2019  |
| 6,4. * | 6.2.301.* |Sürüm 3,3 ' den küçük veya buna eşit |15 Eylül 2019 |
| 6,5. * | 6.4.617.* |Sürüm 3,4 ' den küçük veya buna eşit |Geçerli sürüm, bu nedenle bitiş tarihi yok |

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Aşağıdaki tabloda desteklenen Service Fabric sürümleri için desteklenen işletim sistemleri listelenmektedir.

| İşletim sistemi | Desteklenen en erken Service Fabric sürümü |
| --- | --- |
| Windows Server 2012 R2 | Tüm sürümler |
| Windows Server 2016 | Tüm sürümler |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16,04 | 6.0 |

## <a name="supported-version-names"></a>Desteklenen sürüm adları

Aşağıdaki tabloda Service Fabric sürüm adları ve bunlara karşılık gelen sürüm numaraları listelenmektedir.

| Sürüm adı | Windows sürüm numarası | Linux sürüm numarası |
| --- | --- | --- |
| 5,3 RTO | 5.3.121.9494 | Yok |
| 5,3 CU1 | 5.3.204.9494 | Yok |
| 5,3 CU2 UYGULAMAZSANıZ | 5.3.301.9590 | Yok |
| 5,3 CU3 | 5.3.311.9590 | Yok |
| 5,4 CU2 UYGULAMAZSANıZ | 5.4.164.9494 | Yok |
| 5,5 CU1 | 5.5.216.0    | Yok |
| 5,5 CU2 UYGULAMAZSANıZ | 5.5.219.0    | Yok |
| 5,5 CU3 | 5.5.227.0    | Yok |
| 5,5 CU4 | 5.5.232.0    | Yok |
| 5,6 RTO | 5.6.204.9494 | Yok |
| 5,6 CU2 UYGULAMAZSANıZ | 5.6.210.9494 | Yok |
| 5,6 CU3 | 5.6.220.9494 | Yok |
| 5,7 RTO | 5.7.198.9494 | Yok |
| 5,7 CU4 | 5.7.221.9494 | Yok |
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 UYGULAMAZSANıZ | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 UYGULAMAZSANıZ | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | Yok |
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 UYGULAMAZSANıZ | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 UYGULAMAZSANıZ | 6.4.622.9590 | Yok |
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | Yok |
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | Yok |
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 UYGULAMAZSANıZ | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |