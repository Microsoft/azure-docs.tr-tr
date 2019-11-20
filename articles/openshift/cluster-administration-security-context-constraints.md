---
title: Azure Red Hat OpenShift 'te güvenlik bağlamı kısıtlamalarını yönetme | Microsoft Docs
description: Azure Red Hat OpenShift küme yöneticileri için güvenlik bağlamı kısıtlamaları
services: container-service
author: troy0820
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f98f55dca8b3dbbfbe03cb8c79691cedb63335a0
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168987"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 'te güvenlik bağlamı kısıtlamalarını yönetme 

Güvenlik bağlamı kısıtlamaları (SCCs), küme yöneticilerinin pods izinlerini denetlemesine olanak tanır. Bu API türü hakkında daha fazla bilgi edinmek için bkz. [SCCs için mimari belgeleri](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html). CLı kullanarak, örneğiniz içindeki SCCs 'YI normal API nesneleri olarak yönetebilirsiniz.

## <a name="list-security-context-constraints"></a>Güvenlik bağlamı kısıtlamalarını listeleme

Geçerli SCCs listesini almak için şu komutu kullanın: 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>Bir nesneyi güvenlik bağlamı kısıtlamaları için inceleyin

Belirli bir SCC 'i incelemek için `oc get`, `oc describe` veya `oc edit` kullanın.  Örneğin, **Kısıtlanmış** SCC 'yi incelemek için şu komutu kullanın:
```bash
$ oc describe scc restricted
Name:                   restricted
Priority:               <none>
Access:
  Users:                <none>
  Groups:               system:authenticated
Settings:
  Allow Privileged:         false
  Default Add Capabilities:     <none>
  Required Drop Capabilities:       KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:         <none>
  Allowed Seccomp Profiles:     <none>
  Allowed Volume Types:         configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:           false
  Allow Host Ports:         false
  Allow Host PID:           false
  Allow Host IPC:           false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:          <none>
    UID Range Max:          <none>
  SELinux Context Strategy: MustRunAs
    User:               <none>
    Role:               <none>
    Type:               <none>
    Level:              <none>
  FSGroup Strategy: MustRunAs
    Ranges:             <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:             <none>
```
## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift kümesi oluşturma](tutorial-create-cluster.md) 
