---
title: Virtuálisgép-rendszerkép létrehozása és felhasználó által hozzárendelt felügyelt identitás használata az Azure Storage-ban tárolt fájlok eléréséhez (előzetes verzió)
description: Hozzon létre egy virtuálisgép-rendszerképet az Azure rendszerkép-készítővel, amely az Azure Storage-ban tárolt fájlokat felhasználó által hozzárendelt felügyelt identitás használatával érheti el.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: f3990037d75f9f77eaedc7ec4049f14814216d9c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944961"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Rendszerkép létrehozása és felhasználó által hozzárendelt felügyelt identitás használata az Azure Storage-beli fájlokhoz való hozzáféréshez 

Az Azure rendszerkép-szerkesztő támogatja a parancsfájlok használatát, illetve a fájlok több helyről történő másolását, például a GitHub és az Azure Storage stb. Ezeknek az adatoknak a használatához külsőleg elérhetőnek kell lenniük az Azure-rendszerkép-készítő számára, de az Azure Storage-blobokat SAS-jogkivonatok használatával lehet védelemmel ellátni.

Ez a cikk bemutatja, hogyan hozhat létre testreszabott rendszerképeket az Azure VM rendszerkép-készítő használatával, ahol a szolgáltatás egy [felhasználó által hozzárendelt felügyelt identitással](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fér hozzá az Azure Storage-ban található fájlokhoz a rendszerkép testreszabásához, anélkül, hogy nyilvánosan elérhetővé kellene tennie a fájlokat, vagy sas-jogkivonatokat kell beállítania.

Az alábbi példában két erőforráscsoport jön létre, amelyeket az egyéni rendszerképhez fog használni, a másik pedig egy, a parancsfájlt tartalmazó Azure Storage-fiókot fog üzemeltetni. Ez valós helyzetet szimulál, ahol előfordulhat, hogy a rendszerkép-szerkesztőn kívül különböző tárolási fiókokban található összetevők vagy képfájlok is létrehozhatók. Létre kell hoznia egy felhasználó által hozzárendelt identitást, majd meg kell adnia az olvasási engedélyeket a parancsfájlhoz, de a fájlhoz nem fog nyilvános hozzáférést adni. Ezután a rendszerhéj-testreszabó használatával letöltheti és futtathatja a szkriptet a Storage-fiókból.


> [!IMPORTANT]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>A szolgáltatások regisztrálása
Ha az előzetes verzióban szeretné használni az Azure képszerkesztőt, regisztrálnia kell az új szolgáltatást.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

A szolgáltatás regisztrációjának állapotát vizsgálja meg.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Győződjön meg a regisztrációról.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Ha nem mondják a regisztrációt, futtassa a következőt:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Többször is fogjuk használni az adatokat, így az adatok tárolására néhány változót fogunk létrehozni.


```azurecli-interactive
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Hozzon létre egy változót az előfizetés-AZONOSÍTÓhoz. Ezt a `az account show | grep id`használatával érheti el.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Hozza létre az erőforráscsoportot a rendszerképhez és a parancsfájl-tárolóhoz is.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Hozza létre a tárolót, és másolja a minta parancsfájlt a GitHubról.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Adja meg a rendszerkép-készítő engedélyt a rendszerkép-erőforráscsoport erőforrásainak létrehozásához. A `--assignee` érték a rendszerkép-szerkesztő szolgáltatáshoz tartozó alkalmazás-regisztrációs azonosító. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozza létre az identitást, és rendeljen engedélyeket a script Storage-fiókhoz. További információ: [felhasználó által hozzárendelt felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Példa módosítása

Töltse le a example. JSON fájlt, és konfigurálja a létrehozott változókkal.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Küldje be a rendszerkép konfigurációját az Azure rendszerkép-szerkesztő szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Indítsa el a rendszerkép buildjét.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Várjon, amíg a Build befejeződik. Ez körülbelül 15 percet vesz igénybe.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a rendszerképből. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

A virtuális gép létrehozása után indítson el egy SSH-munkamenetet a virtuális géppel.

```azurecli-interactive
ssh aibuser@<publicIp>
```

A rendszerképet úgy kell megtekinteni, hogy az SSH-kapcsolatok létrehozása után a nap egy üzenete legyen.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha elkészült, akkor törölheti az erőforrásokat, ha már nincs rájuk szükség.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>További lépések

Ha bármilyen probléma merül fel az Azure rendszerkép-készítővel való együttműködés során, olvassa el a [hibaelhárítást](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)ismertető témakört.