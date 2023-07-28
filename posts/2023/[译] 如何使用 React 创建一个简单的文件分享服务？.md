翻译自：[How I Created a File Sharing Website using Simple REACT - DEV Community](https://dev.to/varshithvhegde/how-i-created-a-file-sharing-website-using-simple-react-41f)

## 介绍

欢迎开发爱好者！在这篇博客中，我将带您了解[FreeShare](https://freeshare.vercel.app/)的开发历程，这是一个免费的在线文件共享平台，用户只需5位代码即可轻松共享文件，而无需注册、电子邮件或电话号码验证。我们将深入了解FreeShare背后的功能、架构和代码，解释它是如何工作的，并重点介绍一些主要功能。为了分享我的知识，我在我的github中发布了代码。点击[点击此处查看](https://github.com/Varshithvhegde/FreeShare/)。

## 背景 - FreeShare 的需求

![Free Share](https://res.cloudinary.com/practicaldev/image/fetch/s--PFf1Akn0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_800/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/gavt9liitpwkoe9lvcc7.png)

FreeShare的想法源于上一个项目AnyShare的成功。虽然AnyShare是一个广受欢迎的文件共享平台，但它也有一些漏洞和局限性需要解决。有了FreeShare，其目的是在AnyShare成功的基础上再接再厉，同时提供更安全、更用户友好的体验。通过利用React和Firebase的力量，我们试图创建一个强大的平台，任rt何人都可以在没有技术专业知识的情况下使用。

## Freeshare 的特性

1. 漂亮而愉快的用户界面：FreeShare的用户界面设计在视觉上很有吸引力，易于导航，提供了令人愉快的用户体验。

2. 上传和下载文件：用户可以毫不费力地上传和共享文件，也可以使用5位数的PIN下载共享文件。

3. 免费使用：FreeShare是完全免费的，确保用户可以在没有任何经济负担的情况下共享文件。

4. 无电子邮件、注册或电话号码：隐私是首要任务，因此FreeShare不要求用户在文件共享过程中提供个人信息。

5. 安全文件共享：在FreeShare上共享的每个文件都与一个唯一的5位PIN相关联，确保只有授权用户才能访问这些文件。

6. 共享多个文件：FreeShare允许用户一次上传和共享多个档案，从而提高批量共享的效率。

## FreeShare背后的技术

FreeShare结合多种技术，提供无缝安全的文件共享体验：

1. React：FreeShare的用户界面是使用React构建的，React是一个流行的用于构建用户界面的JavaScript库。React允许我们创建交互式和动态组件，使平台易于使用。

2. Firebase：Firebase是谷歌提供的一个综合平台，提供各种构建web和移动应用程序的工具。我们使用Firebase存储用户数据，包括文件和元数据，确保快速可靠地访问文件。

## FreeShare 工作流程

FreeShare的工作涉及几个关键步骤，我们将对此进行详细解释：

### 步骤1：文件上传

当用户决定在FreeShare上共享文件时，他们会看到一个文件上传界面。用户可以选择同时上载单个文件或多个文件。我们已经从Material UI库中实现了DropzoneDialog来处理文件上传。一旦用户选择要上传的文件，上传过程就开始了。

### 步骤2：文件保存到 Firebase 存储

所有上传的文件都牢固地存储在Firebase存储中。Firebase Storage提供了强大的安全措施，包括在运输和休息中进行加密，以确保保护用户文件免于未经授权的访问。

### 步骤3：生成 5 位，唯一的编码

对于每个上传的文件，FreeShare都会生成一个唯一的5位PIN。此PIN充当文件的安全标识符，是用户下载共享文件所必需的。为了确保唯一性，我们使用了一个递归函数，该函数生成一个随机的5位PIN，并检查它是否已经存在于数据库中。如果是，该函数将生成一个新的PIN，直到找到唯一的PIN为止。



### 步骤4：使用Firebase实时数据库存储元数据



与每个共享文件相关联的元数据，包括下载URL、时间戳和唯一PIN，存储在Firebase实时数据库中。Firebase实时数据库使我们能够创建与前端的实时同步，提供即时更新和无缝的用户体验。



### 步骤5：下载和共享文件



若要下载共享文件，收件人需要输入与该文件关联的5位PIN。输入有效PIN后，FreeShare会从Firebase实时数据库中检索下载URL，并在新窗口中打开文件进行下载。此过程确保只有经过授权的用户才能访问共享文件。



### 代码解释



现在，让我们仔细看看FreeShare平台的一些主要功能和相应的代码：



#### 文件上载和进度栏



```
// File Upload and Progress Bar
const uploadFile = (file, filename) => {
    const storageRef = dbstorageref(storage, "images/" + filename);
    const uploadTask = uploadBytesResumable(storageRef, file);

    uploadTask.on(
      "state_changed",
      (snapshot) => {
        const progress = Math.round(
          (snapshot.bytesTransferred / snapshot.totalBytes) * 100
        );
        setPercentage(progress);
      },
      (error) => {
        console.error(error);
      },
      () => {
        getDownloadURL(uploadTask.snapshot.ref)
          .then((url) => {
            setDownloadUrl(url);
            console.log("File uploaded successfully");
            console.log("Download URL:", url);
            generateUniqueNumber()
              .then((uniqueNumber) => {
                console.log("Unique Number:", uniqueNumber);
                setUniqueID(uniqueNumber);
                return storeDataInDatabase(url, uniqueNumber, filename);
              })
              .then(() => {
                console.log("URL and Unique Number are stored in the database");
              })
              .catch((error) => {
                console.error("Error storing URL and Unique Number:", error);
              });
          })
          .catch((error) => {
            console.error(error);
          });
      }
    );
  };
```
