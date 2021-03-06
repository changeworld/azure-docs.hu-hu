---
title: Azure Data Lake Storage Gen2 .NET SDK a fájlokhoz & ACL-ek (előzetes verzió)
description: Az Azure Storage ügyféloldali kódtár segítségével kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.service: storage
ms.date: 01/09/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 0c279118df3a9205e82f8444b261922c688578da
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969087"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>A .NET használata könyvtárak, fájlok és ACL-ek kezelésére Azure Data Lake Storage Gen2ban (előzetes verzió)

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet a .NET használatával könyvtárakat, fájlokat és engedélyeket olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS). 

> [!IMPORTANT]
> A cikkben Kiemelt [Azure. Storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) NuGet csomag jelenleg nyilvános előzetes verzióban érhető el.

A [Package (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API-referenciák](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Gen1 Gen2-megfeleltetésre](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [visszajelzés küldése](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.

## <a name="set-up-your-project"></a>A projekt beállítása

Első lépésként telepítse az [Azure. Storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet csomagot.

A NuGet-csomagok telepítésével kapcsolatos további információkért lásd: [csomagok telepítése és kezelése a Visual Studióban a NuGet csomagkezelő használatával](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Ezután adja hozzá ezeket az utasításokat a programkód elejéhez.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

A cikkben szereplő kódrészletek használatához létre kell hoznia egy [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -példányt, amely a Storage-fiókot jelképezi. A legegyszerűbb módja, ha egy fiókot használ. 

Ez a példa a [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) egy példányát hozza létre a fiók kulcsa alapján.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer tárolóként működik a fájlok számára. A [DataLakeServiceClient. CreateFileSystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) metódus meghívásával létrehozhat egyet.

Ez a példa egy `my-file-system`nevű fájlrendszert hoz létre. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-referenciát a [DataLakeFileSystemClient. CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) metódus meghívásával.

Ez a példa egy `my-directory` nevű könyvtárat telepít egy fájlrendszerbe, majd hozzáadja a `my-subdirectory`nevű alkönyvtárat. 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat úgy, hogy meghívja a [DataLakeDirectoryClient. RenameAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) metódust. Adja meg a kívánt könyvtár elérési útját (a paramétert). 

Ez a példa egy alkönyvtárat nevez át a `my-subdirectory-renamed`névre.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Ez a példa egy `my-subdirectory-renamed` nevű könyvtárat helyez át egy `my-directory-2`nevű könyvtár alkönyvtárába. 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Könyvtár törlése

A [DataLakeDirectoryClient. Delete](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) metódus meghívásával törölhet egy könyvtárat.

Ez a példa töröl egy `my-directory`nevű könyvtárat.  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Címtár-ACL kezelése

A címtár hozzáférés-vezérlési listájának (ACL) beszerzéséhez hívja meg a [DataLakeDirectoryClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) metódust, és állítsa be az ACL-t úgy, hogy meghívja a [DataLakeDirectoryClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metódust.

> [!NOTE]
> Ha az alkalmazás a Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ismertető témakört. 

Ez a példa lekérdezi és beállítja egy `my-directory`nevű könyvtár ACL-listáját. Az olvasási, írási és végrehajtási jogosultságokat tartalmazó karakterlánc `user::rwx,group::r-x,other::rw-` megadja a tulajdonosi csoport számára az olvasási és végrehajtási engedélyeket, és minden más olvasási és írási engedélyt ad.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Először hozzon létre egy fájlt a célhelyen a [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) osztály egy példányának létrehozásával. Töltsön fel egy fájlt a [DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) metódus meghívásával. Ügyeljen arra, hogy a feltöltést a [DataLakeFileClient. FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) metódus meghívásával végezze el.

Ez a példa egy szövegfájlt tölt fel egy `my-directory`nevű könyvtárba.    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

## <a name="manage-a-file-acl"></a>Fájl hozzáférés-vezérlési listájának kezelése

Egy fájl hozzáférés-vezérlési listájának (ACL) beszerzéséhez hívja meg a [DataLakeFileClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) metódust, és állítsa be az ACL-t úgy, hogy meghívja a [DataLakeFileClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) metódust.

> [!NOTE]
> Ha az alkalmazás a Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ismertető témakört. 

Ez a példa lekérdezi és beállítja a `my-file.txt`nevű fájl hozzáférés-vezérlési listáját. Az olvasási, írási és végrehajtási jogosultságokat tartalmazó karakterlánc `user::rwx,group::r-x,other::rw-` megadja a tulajdonosi csoport számára az olvasási és végrehajtási engedélyeket, és minden más olvasási és írási engedélyt ad.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Letöltés egy címtárból 

Először hozzon létre egy [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) -példányt, amely a letölteni kívánt fájlt jelöli. Használja a [DataLakeFileClient. ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) metódust, és elemezze a visszatérési értéket egy [stream](https://docs.microsoft.com/dotnet/api/system.io.stream) -objektum beszerzéséhez. A .NET-fájlok feldolgozására szolgáló API-k használatával az adatfolyamból fájlba mentheti a bájtokat. 

Ez a példa egy [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) és egy [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) használ a bájtok fájlba mentéséhez. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

A könyvtár tartalmának listázásához hívja meg a [FileSystemClient. GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) metódust, majd az eredmények alapján sorolja fel azokat.

Ez a példa kinyomtatja a `my-directory`nevű könyvtárban található egyes fájlok nevét.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>Lásd még

* [API-referenciák dokumentációja](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Minták](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-net/issues)

