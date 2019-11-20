---
title: SAP için Microsoft Azure sertifikaları | Microsoft Docs
description: Azure platformunda geçerli yapılandırmaların ve SAP sertifikalarının güncelleştirilmiş listesi.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: 38e63d8ca7d9db5247bd5fe07fbe59452a106cf6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098661"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Microsoft Azure üzerinde çalışan SAP Sertifikaları ve yapılandırması

SAP ve Microsoft, müşterileri için karşılıklı avantajlara sahip güçlü bir iş ortaklığında birlikte çalışan uzun bir geçmişe sahiptir. Microsoft, SAP iş yüklerinizin çalıştırılacağı en iyi platform olduğundan emin olmak için, platformunu sürekli olarak güncelleştiriyor ve yeni sertifika ayrıntılarını SAP 'ye gönderen Microsoft Azure. Aşağıdaki tablolarda Azure desteklenen konfigürasyonlar ve büyüyen SAP sertifikalarının listesi ana hatlarıyla verilmiştir. 

## <a name="sap-hana-certifications"></a>SAP HANA sertifikaları
Başvur

- Yerel Azure VM 'Leri ve HANA büyük örnekleri için SAP HANA destek için [sertifikalı IaaS platformları SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) .

| SAP Ürünü | Desteklenen İşletim Sistemi | Azure Teklifleri |
| --- | --- | --- |
| SAP HANA geliştirici sürümü (SQLODBC, ODBO-Windows only, ODBC, JDBC sürücüleri, HANA Studio ve HANA veritabanından oluşan HANA istemci yazılımı dahil) | Red Hat Enterprise Linux, SUSE Linux Enterprise | D serisi VM ailesi |
| HANA 'da birinci departman | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [Sertifikalı IaaS platformlarını SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 için denetlenen kullanılabilirlik. M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, için tam destek <br /> Azure 'da SAP HANA (büyük örnekler) [sertifikalı IaaS platformları SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | Azure 'da M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, SAP HANA (büyük örnekler) <br /> [Sertifikalı IaaS platformlarını SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| BW için HANA Enterprise, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> Azure 'da SAP HANA (büyük örnekler) [sertifikalı IaaS platformları SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> Azure’da SAP HANA (Büyük örnekler) <br /> [Sertifikalı IaaS platformlarını SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

SAP 'ın, ' genişleme ' için [SAP HANA sertifikalı IaaS platformlarında](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) ' Kümeleme ' terimini ' genişleme ' için eş anlamlı olarak kullandığını ve yüksek kullanılabilirlik ' Kümelemesi ' için değil

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver sertifikaları
Microsoft Azure, aşağıdaki SAP ürünleri için Microsoft ve SAP'den tam destek alacak şekilde sertifikaya sahiptir.
Başvur

- [1928533-Azure 'da SAP uygulamaları: SAP trex, SAP livecache ve SAP içerik sunucusu dahil tüm SAP NetWeaver tabanlı uygulamalar için desteklenen ürünler ve Azure VM türleri](https://launchpad.support.sap.com/#/notes/1928533) . Ve SAP HANA hariç tüm veritabanları.


| SAP Ürünü | Konuk işletim sistemi | RDBMS | Sanal Makine Türleri |
| --- | --- | --- | --- |
| SAP Business Suite Yazılımı | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux Oracle Linux |SQL Server, Oracle (yalnızca Windows ve Oracle Linux), DB2, SAP ATıCı |A5 A11, D11 to D14, DS11 to DS14, DS11_v2, DS15_v2, GS1, GS5, D2s_v3, D64s_v3, E2s_v3, E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2,, |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux Oracle Linux |SQL Server, Oracle (yalnızca Windows ve Oracle Linux), DB2, SAP ATıCı |A5 A11, D11 to D14, DS11 to DS14, DS11_v2, DS15_v2, GS1, GS5, D2s_v3, D64s_v3, E2s_v3, E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2,, |
| SAP BusinessObjects BI | Windows |Yok |A5 A11, D11 to D14, DS11 to DS14, DS11_v2, DS15_v2, GS1, GS5, D2s_v3, D64s_v3, E2s_v3, E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2,, |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux Oracle Linux |SQL Server, Oracle (yalnızca Windows ve Oracle Linux), DB2, SAP ATıCı |A5 A11, D11 to D14, DS11 to DS14, DS11_v2, DS15_v2, GS1, GS5, D2s_v3, D64s_v3, E2s_v3, E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2,, |

## <a name="other-sap-workload-supported-on-azure"></a>Azure 'da desteklenen diğer SAP Iş yükü

| SAP Ürünü | Konuk işletim sistemi | RDBMS | Sanal Makine Türleri |
| --- | --- | --- | --- |
| SAP Business One SQL Server | Windows  | SQL Server | Tüm NetWeaver sertifikalı VM türleri<br /> [SAP Note #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows ve Linux | | Tüm NetWeaver sertifikalı VM türleri<br /> SAP Note #2451795 |
| SAP Business Objects bı platformu | Windows ve Linux | | SAP Note #2145537 |
| SAP Data Services 4,2 | | | SAP Note #2288344 |
| SAP Hybrsıs ticaret platformu 5. x ve 6. x | Windows | SQL Server, Oracle | Tüm NetWeaver sertifikalı VM türleri<br /> [Hybrsıs wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
