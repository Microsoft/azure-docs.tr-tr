---
title: Application Insights ve Log Analytics tarafından kullanılan IP adresleri | Microsoft Docs
description: Application Insights için gereken sunucu güvenlik duvarı özel durumları
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/09/2019
ms.openlocfilehash: 54dcef5f0ddbf09d076b1ab7f7955b4681e63c83
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047202"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>Application Insights ve Log Analytics tarafından kullanılan IP adresleri
[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) hizmeti, birkaç IP adresi kullanır. İzlemekte olduğunuz uygulama bir güvenlik duvarının arkasında barındırılıyorsa, bu adresleri bilmeniz gerekebilir.

> [!NOTE]
> Bu adresler statik olsa da, bunları zaman zaman değiştirmek zorunda olduğumuz olasıdır. Tüm Application Insights trafiği, gelen güvenlik duvarı kuralları gerektiren kullanılabilirlik izleme ve Web kancaları dışında giden trafiği temsil eder.
> 
> 

> [!TIP]
> En son değişiklikler hakkında bilgi almak için sık kullandığınız RSS/ATOM okuyucusuna https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom ekleyerek bu sayfaya bir RSS akışı olarak abone olun.
> 
> 

## <a name="outgoing-ports"></a>Giden bağlantı noktaları
Application Insights SDK ve/veya Durum İzleyicisi portala veri göndermesini sağlamak için sunucunuzun güvenlik duvarında bazı giden bağlantı noktalarını açmanız gerekir:

| Amaç | URL'si | IP | Bağlantı Noktaları |
| --- | --- | --- | --- |
| Telemetri |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235 | 443 |
| Canlı Ölçüm Akışı (Doğu ABD) |use.rt.prod.applicationinsights.trafficmanager.net |23.96.28.38<br/>13.92.40.198<br/>52.226.139.56<br/>52.226.140.207 |443 |
| Canlı Ölçüm Akışı (Orta Güney ABD) |ussc.rt.prod.applicationinsights.trafficmanager.net |157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113 |443 |
| Canlı Ölçüm Akışı (Kuzey Avrupa) |eun.rt.prod.applicationinsights.trafficmanager.net |40.115.103.168<br/>40.115.104.31<br/>40.87.140.215<br/>40.87.138.220 |443 |
| Canlı Ölçüm Akışı (Batı Avrupa) |euw.rt.prod.applicationinsights.trafficmanager.net |13.80.134.255<br/>40.68.61.229<br/>23.101.69.223<br/>52.232.106.242 |443 |
| Canlı Ölçüm Akışı (Doğu Asya) |ase.rt.prod.applicationinsights.trafficmanager.net |23.100.90.7<br/>23.101.13.65<br/>23.101.0.142<br/>23.101.9.4 |443 |
| Canlı Ölçüm Akışı (Güneydoğu Asya) |asse.rt.prod.applicationinsights.trafficmanager.net |207.46.224.101<br/>207.46.236.191<br/>137.116.151.139<br/>13.76.87.86 |443 |

## <a name="status-monitor"></a>Durum İzleyicisi
Durum İzleyicisi yapılandırma-yalnızca değişiklik yapıldığında gereklidir.

| Amaç | URL'si | IP | Bağlantı Noktaları |
| --- | --- | --- | --- |
| Yapılandırma |`management.core.windows.net` | |`443` |
| Yapılandırma |`management.azure.com` | |`443` |
| Yapılandırma |`login.windows.net` | |`443` |
| Yapılandırma |`login.microsoftonline.com` | |`443` |
| Yapılandırma |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Yapılandırma |`auth.gfx.ms` | |`443` |
| Yapılandırma |`login.live.com` | |`443` |
| Yükleme | `globalcdn.nuget.org`, `packages.nuget.org`,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org``dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>Kullanılabilirlik testleri
Bu, [kullanılabilirlik Web testlerinin](../../azure-monitor/app/monitor-web-app-availability.md) çalıştırıldığı adreslerin listesidir. Uygulamanızda Web testleri çalıştırmak istiyorsanız, ancak Web sunucunuz belirli istemcilere hizmet vermeye karşı kısıtlanmışsa, kullanılabilirlik testi sunucularımızdan gelen trafiğe izin vermeniz gerekir.

Bu adreslerden gelen trafik için 80 (http) ve 443 (https) bağlantı noktalarını açın (IP adresleri konuma göre gruplandırılır):

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

## <a name="application-insights-api"></a>Application Insights API 'SI
| Amaç | URI | IP | Bağlantı Noktaları |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| API belgeleri |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Azure işlem hattı ek açıklaması uzantısı |aigs1.aisvc.visualstudio.com |tir|443 |

## <a name="log-analytics-api"></a>Log Analytics API 'SI

| Amaç | URI | IP | Bağlantı Noktaları |
| --- | --- | --- | --- |
| API |api.loganalytics.io<br/>*. api.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| API belgeleri |dev.loganalytics.io<br/>docs.loganalytics.io<br/>www.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |

## <a name="application-insights-analytics"></a>Application Insights Analizi

| Amaç | URI | IP | Bağlantı Noktaları |
| --- | --- | --- | --- |
| Analiz portalı | analytics.applicationinsights.io | tir | 80,443 |
| CDN | applicationanalytics.azureedge.net | tir | 80,443 |
| Medya CDN | applicationanalyticsmedia.azureedge.net | tir | 80,443 |

Note: *. applicationinsights.io etki alanının sahibi Application Insights ekibine aittir.

## <a name="log-analytics-portal"></a>Log Analytics portalı

| Amaç | URI | IP | Bağlantı Noktaları |
| --- | --- | --- | --- |
| Portal | portal.loganalytics.io | tir | 80,443 |
| CDN | applicationanalytics.azureedge.net | tir | 80,443 |

Note: *. loganalytics.io etki alanının sahibi Log Analytics ekibine aittir.

## <a name="application-insights-azure-portal-extension"></a>Application Insights Azure portal uzantısı

| Amaç | URI | IP | Bağlantı Noktaları |
| --- | --- | --- | --- |
| Application Insights uzantısı | stamp2.app.insightsportal.visualstudio.com | tir | 80,443 |
| Application Insights uzantısı CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | tir | 80,443 |

## <a name="application-insights-sdks"></a>Application Insights SDK 'lar

| Amaç | URI | IP | Bağlantı Noktaları |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net | tir | 80,443 |
| Application Insights Java SDK 'Sı | aijavasdk.blob.core.windows.net | tir | 80,443 |

## <a name="alert-webhooks"></a>Web kancalarını uyar

| Amaç | IP | Bağlantı Noktaları
| --- | --- | --- |
| Uyarı | 23.96.11.4 | 443 |

## <a name="profiler"></a>Profil Oluşturucu

| Amaç | URI | IP | Bağlantı Noktaları |
| --- | --- | --- | --- |
| Aracı | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portal | gateway.azureserviceprofiler.net | tir | 443
| Depolama | *. core.windows.net | tir | 443

## <a name="snapshot-debugger"></a>Anlık Görüntü Hata Ayıklayıcı

> [!NOTE]
> Profil Oluşturucu ve Snapshot Debugger aynı IP adresleri kümesini paylaşır.

| Amaç | URI | IP | Bağlantı Noktaları |
| --- | --- | --- | --- |
| Aracı | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portal | ppe.gateway.azureserviceprofiler.net | tir | 443
| Depolama | *. core.windows.net | tir | 443
