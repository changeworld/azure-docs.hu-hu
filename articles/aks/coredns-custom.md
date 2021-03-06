---
title: CoreDNS testreszabása az Azure Kubernetes szolgáltatáshoz (ak)
description: Megtudhatja, hogyan szabhatja testre a CoreDNS az altartományok hozzáadásához vagy az egyéni DNS-végpontok kibővítéséhez az Azure Kubernetes Service (ak) használatával
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595824"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>A CoreDNS testreszabása az Azure Kubernetes szolgáltatással

Az Azure Kubernetes Service (ak) a [CoreDNS][coredns] -projektet használja a fürt DNS-kezeléséhez és feloldásához az összes *1,12. x* és újabb fürtök esetében. Korábban a rendszer a Kube-DNS-projektet használta. Ez a Kube-DNS-projekt már elavult. A CoreDNS testreszabásával és Kubernetes kapcsolatos további információkért tekintse meg a [hivatalos upstream dokumentációját][corednsk8s].

Mivel az AK felügyelt szolgáltatás, nem módosíthatja a CoreDNS (a *alapfájl*) fő konfigurációját. Ehelyett Kubernetes- *ConfigMap* használ az alapértelmezett beállítások felülbírálásához. Az alapértelmezett AK-CoreDNS ConfigMaps megtekintéséhez használja az `kubectl get configmaps --namespace=kube-system coredns -o yaml` parancsot.

Ebből a cikkből megtudhatja, hogyan használhatja a ConfigMaps-t az CoreDNS alapszintű, az AK-ban található testreszabási lehetőségei Ez a megközelítés különbözik a CoreDNS más kontextusokban való konfigurálásának, például a alapfájl használatával. Ellenőrizze, hogy a futtatott CoreDNS verziója módosul-e a verziók között.

> [!NOTE]
> a `kube-dns` különböző [testreszabási lehetőségeket][kubednsblog] kínál a Kubernetes konfigurációs térképén keresztül. A CoreDNS visszafelé **nem** kompatibilis a Kube-DNS szolgáltatással. A korábban használt testreszabásokat frissíteni kell a CoreDNS-mel való használatra.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Támogatás/nem támogatott

A beépített CoreDNS beépülő modulok támogatottak. A bővítmények és a harmadik féltől származó beépülő modulok nem támogatottak.

## <a name="rewrite-dns"></a>DNS újraírása

Az egyik forgatókönyv az, ha az on-the-fly DNS-név újraírását végzi. Az alábbi példában cserélje le a `<domain to be written>`t a saját teljes tartománynevére. Hozzon létre egy `corednsms.yaml` nevű fájlt, és illessze be a következő példa konfigurációt:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Hozza létre a ConfigMap a [kubectl Apply ConfigMap][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f corednsms.yaml
```

A testreszabások alkalmazásának ellenőrzéséhez használja a [kubectl Get configmaps][kubectl-get] , és adja meg a *coredns-Custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Most kényszerítse a CoreDNS a ConfigMap újratöltését. A [kubectl delete Pod][kubectl delete] parancs nem ártalmas, és nem okoz leállási időt. A `kube-dns` hüvelyek törlődnek, és a Kubernetes Scheduler ezután újra létrehozza őket. Ezek az új hüvelyek a TTL-érték változását tartalmazzák.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> A fenti parancs helyes. A `coredns`módosítása közben az üzemelő példány a **Kube-DNS-** név alatt található.

## <a name="custom-forward-server"></a>Egyéni továbbító kiszolgáló

Ha meg kell adnia egy továbbító kiszolgálót a hálózati forgalomhoz, létrehozhat egy ConfigMap a DNS testreszabásához. A következő példában frissítse a `forward` nevét és a címeit a saját környezetének értékeivel. Hozzon létre egy `corednsms.yaml` nevű fájlt, és illessze be a következő példa konfigurációt:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Ahogy az előző példákban is, hozza létre a ConfigMap a [kubectl Apply ConfigMap][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét. Ezután kényszerítse a CoreDNS a ConfigMap újratöltésére a [kubectl delete Pod][kubectl delete] használatával a Kubernetes Scheduler létrehozásához:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Egyéni tartományok használata

Előfordulhat, hogy olyan egyéni tartományokat szeretne konfigurálni, amelyek csak belsőleg oldhatók fel. Előfordulhat például, hogy fel szeretné oldani a *puglife. local*egyéni tartományt, amely nem érvényes legfelső szintű tartomány. Egyéni tartományi ConfigMap nélkül az AK-fürt nem tudja feloldani a címeket.

Az alábbi példában frissítse az egyéni tartományt és az IP-címet, hogy a forgalmat a saját környezetéhez tartozó értékekre irányítsa. Hozzon létre egy `corednsms.yaml` nevű fájlt, és illessze be a következő példa konfigurációt:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Ahogy az előző példákban is, hozza létre a ConfigMap a [kubectl Apply ConfigMap][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét. Ezután kényszerítse a CoreDNS a ConfigMap újratöltésére a [kubectl delete Pod][kubectl delete] használatával a Kubernetes Scheduler létrehozásához:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Helyettes tartományok

A CoreDNS a helyettes tartományok konfigurálására is használható. Az alábbi példában frissítse az egyéni tartományokat és IP-címeket a saját környezete értékeivel. Hozzon létre egy `corednsms.yaml` nevű fájlt, és illessze be a következő példa konfigurációt:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Ahogy az előző példákban is, hozza létre a ConfigMap a [kubectl Apply ConfigMap][kubectl-apply] parancs használatával, és adja meg a YAML-jegyzékfájl nevét. Ezután kényszerítse a CoreDNS a ConfigMap újratöltésére a [kubectl delete Pod][kubectl delete] használatával a Kubernetes Scheduler létrehozásához:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Gazdagépek beépülő modul

Mivel az összes beépített beépülő modul támogatott, az azt jelenti, hogy a CoreDNS- [gazdagépek][coredns hosts] beépülő modul a testreszabáshoz is elérhető:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>DNS-lekérdezési hibakeresés naplózásának engedélyezése 

A DNS-lekérdezések naplózásának engedélyezéséhez alkalmazza a következő konfigurációt a coredns-Custom ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk néhány példát mutat be a CoreDNS testreszabásához. A CoreDNS projekttel kapcsolatos további információkért tekintse meg [a CoreDNS felső projektje lapot][coredns].

Az alapvető hálózati fogalmakkal kapcsolatos további tudnivalókért tekintse meg a [hálózati fogalmak az AK-beli alkalmazásokhoz][concepts-network]című témakört.

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
