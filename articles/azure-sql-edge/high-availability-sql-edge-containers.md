---
title: Azure SQL Edge kapsayıcıları için yüksek kullanılabilirlik-Azure SQL Edge
description: Azure SQL Edge kapsayıcıları için yüksek kullanılabilirlik hakkında bilgi edinin
keywords: SQL Edge, kapsayıcılar, yüksek kullanılabilirlik
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90941124"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Azure SQL Edge kapsayıcıları için yüksek kullanılabilirlik

Azure SQL Edge örneklerinizi Kubernetes 'te yerel olarak oluşturun ve yönetin. Azure SQL Edge 'i [Kubernetes](https://kubernetes.io/)tarafından yönetilen Docker kapsayıcılarına dağıtın. Kubernetes 'de, bir küme düğümünün başarısız olması durumunda, Azure SQL Edge örneğine sahip bir kapsayıcı otomatik olarak kurtarabilir. SQL Edge kapsayıcı görüntüsünü bir Kubernetes kalıcı birim talebi (PVC) ile yapılandırabilirsiniz. Kubernetes, kapsayıcıda Azure SQL Edge işlemini izler. İşlem, Pod, kapsayıcı veya düğüm başarısız olursa, Kubernetes otomatik olarak başka bir örnek önyükleme ve depoya yeniden bağlanır.

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Kubernetes üzerinde Azure SQL Edge kapsayıcıları

Kubernetes 1,6 ve üzeri, [*Depolama sınıfları*](https://kubernetes.io/docs/concepts/storage/storage-classes/)ve [*kalıcı birim talepleri*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)için destek içerir.

Bu yapılandırmada, Kubernetes kapsayıcı Orchestrator rolünü yürütür. 

![Bir Kubernetes kümesinde Azure SQL Edge diyagramı](media/deploy-kubernetes/kubernetes-sql-edge.png)

Yukarıdaki diyagramda, `azure-sql-edge` [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)içindeki bir kapsayıcıdır. Kubernetes kümedeki kaynakları düzenler. Bir [çoğaltma kümesi](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) , bir düğüm hatasından sonra Pod 'un otomatik olarak kurtarılmasını sağlar. Uygulamalar hizmete bağlanır. Bu durumda hizmet, hatasından sonra aynı kalan bir IP adresini barındıran bir yük dengeleyiciyi temsil eder `azure-sql-edge` .

Aşağıdaki diyagramda `azure-sql-edge` kapsayıcı başarısız oldu. Orchestrator olarak Kubernetes, çoğaltma kümesindeki sağlıklı örneklerin doğru sayısını garanti eder ve yapılandırmaya göre yeni bir kapsayıcı başlatır. Orchestrator aynı düğümde yeni bir pod başlatır ve `azure-sql-edge` aynı kalıcı depolamaya yeniden bağlanır. Hizmet yeniden oluşturulmuş olarak bağlanır `azure-sql-edge` .

![Pod sonrasında bir Kubernetes kümesinde Azure SQL Edge hatası](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

Aşağıdaki diyagramda, kapsayıcıyı barındıran düğüm `azure-sql-edge` başarısız oldu. Orchestrator yeni Pod 'u farklı bir düğümde başlatır ve `azure-sql-edge` aynı kalıcı depolamaya yeniden bağlanır. Hizmet yeniden oluşturulmuş olarak bağlanır `azure-sql-edge` .

![Düğüm başarısız olduktan sonra bir Kubernetes kümesinde Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

Kubernetes 'te bir kapsayıcı oluşturmak için bkz. [Kubernetes 'Te Azure SQL Edge kapsayıcısı dağıtma](deploy-Kubernetes.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure Kubernetes Service (AKS) ' de Azure SQL Edge kapsayıcıları dağıtmak için aşağıdaki makalelere bakın:
- [Kubernetes 'te Azure SQL Edge kapsayıcısı dağıtma](deploy-Kubernetes.md)
- [SQL Edge 'de ONNX ile Machine Learning ve yapay zeka](onnx-overview.md).
- [IoT Edge kullanarak SQL Edge ile uçtan uca IoT çözümü oluşturma](tutorial-deploy-azure-resources.md).
- [Azure SQL Edge 'de veri akışı](stream-data.md)