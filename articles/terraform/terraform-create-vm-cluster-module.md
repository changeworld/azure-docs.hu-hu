---
title: Oktatóanyag – Azure virtuálisgép-fürt létrehozása a Terraform a modul beállításjegyzékének használatával
description: Ebben az oktatóanyagban a Terraform-modulok használatával hoz létre egy Windows rendszerű virtuálisgép-fürtöt az Azure-ban
keywords: Azure devops Terraform virtuális gép virtuálisgép-fürt moduljának beállításjegyzéke
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9faeee9bb2f0fb6dc148a3868f6fc0dae3833a2a
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945288"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>Oktatóanyag: Azure-beli virtuálisgép-fürt létrehozása a Terraform a modul beállításjegyzékének használatával

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre kisméretű virtuálisgép-fürt a Terraform [Azure Compute-moduljával](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). Ennek az oktatóanyagnak a segítségével megtanulhatja a következőket: 

> [!div class="checklist"]
> * Hitelesítés beállítása az Azure-ral
> * Terraform-sablon létrehozása
> * A módosítások vizualizációja egy tervben
> * A konfiguráció alkalmazása a virtuálisgép-fürt létrehozásához

A Terraformról a [Terraform dokumentációjában](https://www.terraform.io/docs/index.html) talál további információt.

## <a name="set-up-authentication-with-azure"></a>Hitelesítés beállítása az Azure-ral

> [!TIP]
> Ha [Terraform környezeti változókat használ](terraform-install-configure.md) vagy az [Azure Cloud Shellben](/azure/cloud-shell/overview) futtatja ezt az oktatóanyagot, hagyja ki ezt a lépést.

 Tekintse át a [Terraform telepítésével és az Azure-hoz való hozzáféréssel](terraform-install-configure.md) foglalkozó cikket az Azure-szolgáltatásnév létrehozásához. Használja ezt a szolgáltatásnevet egy új `azureProviderAndCreds.tf` fájl kitöltéséhez egy üres könyvtárban az alábbi kóddal:

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    version = "~>1.40"

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>A sablon létrehozása

Hozzon létre egy új, `main.tf` nevű Terraform-sablont az alábbi paranccsal:

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    is_windows_image = "true"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = module.network.vnet_subnets[0]
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = module.mycompute.public_ip_dns_name
}

output "vm_public_ip" {
    value = module.mycompute.public_ip_address
}

output "vm_private_ips" {
    value = module.mycompute.network_interface_private_ip
}
```

Futtassa a `terraform init` parancsot a konfiguráció könyvtárában. Ha a Terraform legalább 0.10.6-os verzióját használja, az alábbi kimenet jelenik meg:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>A módosítások vizualizációja egy tervben

Futtassa a `terraform plan` parancsot a sablon által létrehozott virtuálisgép-infrastruktúra előnézetéhez.

![Terraform-terv](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Virtuális gépek létrehozása a sablon alkalmazásával

Futtassa a `terraform apply` parancsot a virtuális gépek kiépítéséhez az Azure-on.

![Terraform alkalmazása](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Az Azure Terraform-modulok listájának tallózása](https://registry.terraform.io/modules/Azure)