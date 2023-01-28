---
url: https://sspai.com/post/75885
title: Obsidian 修改 annotator 引用格式，外链图片转图床，根据字幕跳转视频
date: 2023-01-28 09:58:27
tag: 
summary: 使用 Obsidian 的过程中发现一些痛点：1 > 使用插件 annotator 时，引用的文本内不能添加链接如果强行将关键字改成链接，就会出现格式错乱我想要的效果如下：引用的文本能够给添加链接 2 > 使用简悦剪藏 ......
---
使用 Obsidian 的过程中发现一些痛点：

1> 使用插件 annotator 时，引用的文本内不能添加链接

![](https://cdn.sspai.com/2022/09/28/7be5bed4c98310e296015dcd231d965f.png)

如果强行将关键字改成链接，就会出现格式错乱

![](https://cdn.sspai.com/2022/09/28/dcd6a2cdda74dda9f752bb456495f16d.png)

我想要的效果如下：引用的文本能够给添加链接

![](https://cdn.sspai.com/2022/09/28/a776d9ddf1415a2e373e92e1c4de0fa9.png)

2> 使用简悦剪藏文章时 ，文章内的图片是外链。如果图片是外链，那么如果外链链接失效了就找不回图片了

![](https://cdn.sspai.com/2022/09/28/6e8eca73c7ebebe72f4beed23ad39fef.png)

我想要的效果如下：将外链转为图床的链接

![](https://cdn.sspai.com/2022/09/28/c7e30c1e77ea0b8b4579040bf95cf973.png)

3> Media Extended 虽然能使用链接跳转到视频指定时间点，但是手写链接太麻烦了

![](https://cdn.sspai.com/2022/09/28/348c38be0d673e85a238c707d2a5d6f5.png)

我想要的效果：自动生成一个视频的全部字幕和引用，且每句字幕和一点时间点引用呈一一对应的关系

![](https://cdn.sspai.com/2022/09/28/a63574a5b8522d9e058e8e62c47337fa.png)

❗❗❗为了节省大家的时间，我在文末写了我常用的 Obsidian 插件的下载链接，这些插件中有部分插件的源码都是被我修改过的，有两个需要注意的事项：

*   被我修改过源码的插件不全是可以直接用的，比如发送图片到 Picgo 时的 url 要换成你在 Picgo 内设置的 url
*   修改过源码的插件不要升级，如果升级了会导致修改失效

❗❗❗再附加一个注意事项：

*   入门简悦的时候如果运气不好（比如我），会遇到很多问题，如果看了入门文章还是解决不了就到 [https://github.com/Kenshin/simpread/issues](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2FKenshin%2Fsimpread%2Fissues) 提交自己的问题，简悦的开发者回复消息还是挺快的（顺便夸夸开发者很积极，这很难得）

## 修改 annotator 引用格式

### 使用工具

[Annotator](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2Felias-sundqvist%2Fobsidian-annotator)：是一个用于给 pdf 做标注和跳转的开源插件

[Linter](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2Fplaters%2Fobsidian-linter)：是一个用于格式化笔记的开源插件

### 配置 Linter

目标：修改其源代码，添加将 Annotator 样式修改为 📌

添加规则分类：

![](https://cdn.sspai.com/2022/09/23/article/8c6d19bdf00f6e09328c2da3b57515ef)

添加具体的规则处理：

![](https://cdn.sspai.com/2022/09/23/article/13c1370b9d9c3df4abd4bfc5d8b4f632)

代码如下：

```
new Rule('Reform Annotation', '修改 Annotation 链接的样式', RuleType.MINE, (text, options = {}) => {  
    const searchRegex = new RegExp(`${options['Find']}`, "gm");  
    text = text.replace(searchRegex, `${options['Replace']}`);  
    return text;  
},[  
], [  
    new TextOption('Find', 'regular expression for finding', "\\[\\[([^@]+)@annote#([^|]+)\\|([^📌\\]]+)\\]\\]"),  
    new TextOption('Replace', 'result of replacement', '[[$1@annote#$2|📌]]$3')  
])
```

保存后退出 ide，并重启 Obsidian，打开 Linter 的设置，并开启新功能的开关

![](https://cdn.sspai.com/2022/09/23/article/ae4f23ddea42de172776f5228edc5ef9)

测试一下：  
（测试前）

![](https://cdn.sspai.com/2022/09/23/article/5bb9337119603240f73f6d57b2b1ef0b)

  
（测试后）

![](https://cdn.sspai.com/2022/09/23/article/d2c292bf401f1708fd6edfbc840d975c)

## 外链图片转图床

### 使用工具

[简悦](https://sspai.com/link?target=http%3A%2F%2Fksria.com%2Fsimpread%2F)（版本为 2.2）：这是一个可以把网页文章保存为本地 markdown 格式笔记的工具

### 配置 Linter

修改 Linter 源代码，添加可以将图片外链转换成自己图床的链接  
ps. 我使用的图床是 [github](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2F)，并且使用 [PicGo](https://sspai.com/link?target=https%3A%2F%2Fpicgo.github.io%2FPicGo-Doc%2Fzh%2Fguide%2F%23%25E5%25BA%2594%25E7%2594%25A8%25E6%25A6%2582%25E8%25BF%25B0) 快速上传图片到图床。

首先引入 http、https、fs 库

![](https://cdn.sspai.com/2022/09/23/article/2a346f9dc90e6f01d9ad5d9cb2c23d25)

```
var fs = require('fs');  
var http = require('http');  
var https = require('https');
```

添加自定义的方法

![](https://cdn.sspai.com/2022/09/23/article/3507c524453b12ce8bcf54c4127c6aa6)

ps. 我的前端能力很菜很菜，献丑了。大佬们可以自己写这部分的功能。这段代码的逻辑就是先在本地新建一个文件夹 `temp_pic_folder`，然后通过正则表达式匹配到图片链接，然后通过这些链接把图片一个一个下载到文件夹 `temp_pic_folder` 中，然后再把下载下来的图片一个一个上传到用户设置的图床中，并修改文章中这些图片链接为最终图床上的图片链接，最后再把文件夹 `temp_pic_folder` 及其内部的图片都从本地删了。  
注意！我这段代码只能匹配末尾有图片文件后缀名的链接，如 `https://xxxx/xxx/.../xxx.jpg`，如果是 `https://xxxx/xxx/.../xxx` 就不能识别，别问我为什么不处理这种情况，懒且够用

```
/**  
 * 将外链图片链接转换为自己的图床链接  
 *  
 * @param text 文档的文本  
 * @param options 插件在 setting tab 的设置  
 */  
async changeOuterLink (text, options = {}, app) {  
    // 获取当前编辑文档的所在文件系统的绝对地址 
    const tempFolder = "temp_pic_folder";  
    const anchorFile = "anchor_file";  
    const parentPath = app.vault.getAbstractFileByPath(app.workspace.getActiveFile().path).parent.path;  
    // 以 \ 为分隔符的就当作是 path    const tempFolderRelativePath = parentPath + "\\" + tempFolder; 
    // 以 / 为分隔符的就当作是 url    const tempFolderRelativeUrl = parentPath + "/" + tempFolder; 
    const anchorFileRelativeUrl = tempFolderRelativeUrl + "/" + anchorFile;  
    const basePath = app.vault.adapter.getBasePath();  
    const absolutePath = basePath + "\\" + tempFolderRelativePath;  

    // 匹配图片网址的正则表达式（已测网站图床包括：知乎、CSDN等） 
    const searchRegex = new RegExp(`${options['Image regex']}`, "gm");  

    // 自己图床的 url    let myPicBedUrl = `${options['Host']}` + `${options['Repository']}`; 
    // 已经下载的文件的列表 
    let fileList = [];  
    // 文件名到其相应文件后缀的映射 
    let fileExtMap = {};  
    // 下载图片后返回的多个 promise    let promiseList = []; 
    // 存放图片的文件夹里的文件列表 
    let tempFileSet = new Set();  

    let folderAbstractFile = app.vault.getAbstractFileByPath(tempFolderRelativeUrl);  
    if (folderAbstractFile) {  
        // 将文件名保存到 set 中 
        let downloadedPicList = app.fileManager.getNewFileParent(anchorFileRelativeUrl).children;  
        for (let i = 0; i < downloadedPicList.length; i++) {  
            tempFileSet.add(downloadedPicList[i].basename);  
        }  
    } else {  
        // 创建存放图片的文件夹 
        await app.vault.createFolder(tempFolderRelativePath);  
        folderAbstractFile = app.vault.getAbstractFileByPath(tempFolderRelativeUrl);  
        await app.vault.create(anchorFileRelativeUrl, "");  
    }  

    text = text.replace(searchRegex, (rs, $1, $2) => {  
        // 检查是否是自己图床的图片 
        if ($1.indexOf(myPicBedUrl) === -1) {  
            // 检查本地是否已经下载 
            let picName = $1.slice($1.lastIndexOf('/') + 1).toString();  
            if (!tempFileSet.has(picName)) {  
                let perPromise = new Promise((resolve, reject) => {  
                    let request;  
                    if ($1[4] === "s") {  
                        request = https.get($1 + $2, (res) => {  
                            if (res.statusCode !== 200) {  
                                console.log("download image error!");  
                                return;                            }  
                            let ext = res.headers['content-type'].split('/')[1];  
                            let dest = absolutePath + "\\" + picName + "." + ext;  
                            let file = fs.createWriteStream(dest);  

                            res.on('end', () => {  
                                console.log("图片下载完毕");  
                            });  

                            // 进度、超时等 
                            file.on('finish', () => {  
                                file.close();  
                                fileList.push(dest);  
                                fileExtMap[picName] = ext;  
                                resolve();  
                            }).on('error', (err) => {  
                                fs.unlink(dest);  
                            });  
                            res.pipe(file);  
                        });  
                    } else {  
                        // todo http 的不管用 
                        // 和 https 一样的代码 
                    }  
                    request.on('error', reject);  
                    request.end();  
                });  
                promiseList.push(perPromise);  
            } else {  
                console.log("图片已存在");  
            }  
        } else {  
            console.log("自己的地址");  
        }  
        return rs;  
    });  

    return Promise.allSettled(promiseList)  
        .then((results) => {  
            console.log("文件列表 " + fileList);  
            if (fileList.length > 0) {  
                return new Promise((resolve, reject) => {  
                    const postOptions = {  
                        hostname: `${options['PicGoUrl']}`,  
                        port: `${options['PicGoPort']}`,  
                        path: `${options['PicGoPath']}`,  
                        method: 'POST',  
                        headers: {  
                            'Content-Type': 'application/json'  
                        }  
                    }  

                    const req = http.request(postOptions, async res => {  
                        if (res.statusCode === 200) {  
                            console.log("删除图片文件夹 " + folderAbstractFile.path);  
                            await app.vault.delete(folderAbstractFile, true);  
                            // 正则替换图片地址 
                            text = text.replace(searchRegex, (rs, $1) => {  
                                console.log("替换链接名");  
                                // 根据文件名在 map 中找到对应的后缀 
                                let picName = $1.slice($1.lastIndexOf('/') + 1);  
                                let ext = fileExtMap[picName];  
                                return "![](" + myPicBedUrl + picName + "." + ext;  
                            });  
                        } else {  
                            console.log("上传失败");  
                        }  
                        resolve(text);  
                    });  

                    req.on('error', error => {  
                        console.error(error);  
                        reject(error);  
                    });  

                    req.write(JSON.stringify({list: fileList}));  
                    req.end();  
                });  
            } else {  
                return new Promise((resolve, reject) => {  
                    resolve(text);  
                });  
            }  
        });  
}
```

该方法调用处也要做修改：

![](https://cdn.sspai.com/2022/09/23/article/754352495712a984479742bee7996fad)

因为 `changeOuterLink` 方法是异步的，所以调用该方法的地方要加上 `await`

![](https://cdn.sspai.com/2022/09/23/article/38eec744947a8792597a643847ede646)

![](https://cdn.sspai.com/2022/09/23/article/7e1e41caf6b05822b5267d1ed4ca0eb7)

![](https://cdn.sspai.com/2022/09/23/article/eee3ba81dbd7ca628eb79540036c5121)

然后在 rules 上添加新的规则

![](https://cdn.sspai.com/2022/09/23/article/7a85f39f7c17febf8d34246c2ad1ae6e)

```
new Rule('Upload Image', 'Switch', RuleType.MINE, (text, options = {}, app, changeOuterLink) => {  
    return changeOuterLink(text, options, app);  
},[  
], [  
    new TextOption('PicGoUrl', '', "127.0.0.1"), 
    //  PicGoPort 是 picgo 默认的端口号
    new TextOption('PicGoPort', '', "36677"),  
    new TextOption('PicGoPath', '', "/upload"),  
    new TextOption('Host', 'target host', "https://raw.githubusercontent.com"),  
    // Repository 为 github 的仓库名
    new TextOption('Repository', '', "/xxx/xx/"),  
    new TextOption('Image regex', '', '\\!\\[[^\\[\\]]*\\]\\(([a-zA-z]+://[^/]+/[^.?)]+)([^)?]*)')  
])
```

## 根据字幕跳转视频

### 使用工具

[Media Extended](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2Faidenlx%2Fmedia-extended)：使用这个插件能够播放网络视频并能跳转进度条（我已测试能行的网站有 youtube，其他没试过）

[Media Extended BiliBili Plugin](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2Faidenlx%2Fmx-bili-plugin)：这个插件是支持播放 bilibili 视频并能跳转进度条的插件

[Regex Find and Replace](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2FGru80%2Fobsidian-regex-replace)：该插件可用于正则表达式查找和替换

[Language Reactor](https://sspai.com/link?target=https%3A%2F%2Fwww.languagereactor.com%2F)：这是个 chrome 插件，可以在看 youtube 的时候把英文字幕翻译成中文（或其他语言），并且能选中字幕中的单词查看释义甚至可以把单词加入生词库，在生词库可以使用类似 anki 的复习方式背诵单词

### 安装 chrome 插件

![](https://cdn.sspai.com/2022/09/23/article/56037fc1e1d22c4aa00b5a4089567cc1)

### 设置 Regex Find and Replace

修改该插件的源代码

![](https://cdn.sspai.com/2022/09/23/article/6b6954aef79584aabfd2673b70111221)

```
// 匹配 xxs
let regDocumentText = documentText.replace(/(^[0-5]?[0-9])s$/gm, (rs, $1) => {  
 return ">👇[" + $1 + "s](" + replaceString + "#t=" + $1 + ")"; 
});  

// 匹配 mm:ssregDocumentText = regDocumentText.replace(/(^[0-5]?[0-9]):([0-5][0-9])$/gm, (rs, $1, $2) => {  
 return ">👇[" + $1 + ":" + $2 + "](" + replaceString + "#t=" + ($1 * 60 + $2 * 1) + ")"; 
});  

// 匹配 hh:mm:ssregDocumentText = regDocumentText.replace(/((^[0-1]?[0-9]|2[0-3]):([0-5][0-9]):([0-5][0-9]))$/gm, (rs, $1, $2, $3) => {  
 return ">👇[" + $1 + ":" + $2 + ":" + $3 + "](" + replaceString + "#t=" + ($1 * 60 * 60 + $2 * 60 + $3 * 1) + ")"; 
});  

editor.setValue(regDocumentText);
```

第一个正则表达式 `([0-5]?[0-9])s$` 是转换 `xxs\n` 格式的字符串  
第二个正则表达式 `([0-5]?[0-9]):([0-5][0-9])` 是转换 `m:ss` 或 `mm:ss` 格式的字符串  
第三个正则表达式 `(([0-1]?[0-9]|2[0-3]):([0-5][0-9]):([0-5][0-9]))$` 是转换 `hh:mm:ss` 格式的字符串  
以上统一最终转换为 `>👇[$1s](网址#t=$1)`

之所以要设置以上正则，是为了匹配 **Language Reactor** 生成的字幕文件，下面工作流会介绍到

然后设置一下该插件的快捷键：

![](https://cdn.sspai.com/2022/09/23/article/0ec234b6bfb3e2b19a2ccaa611b588b4)

### 导出视频的中英字幕

先打开 Language Reactor

![](https://cdn.sspai.com/2022/09/23/article/d5105f2d662a2da461d80898e26189c7)

一开始打开某个视频可能默认是中文字幕，要改成英文的：

![](https://cdn.sspai.com/2022/09/23/article/fa6a91929f6682e1c35d90188babb1af)

点击 **设置** 查看字幕情况：

![](https://cdn.sspai.com/2022/09/23/article/e4a6a3141976f9d3b7208ed117140d9e)

![](https://cdn.sspai.com/2022/09/23/article/76bd1646ca6847c17ff7258caf96177c)

（一般没有人类翻译的话相应的单选框是选择不了的）  
因为根据上图可以知道这个视频是有人类翻译的，所以导出字幕时可以仅仅导出英文字幕和人类翻译：  
 

![](https://cdn.sspai.com/2022/09/23/article/d18dd751fd3c36594c834c6b5bfa4b26)

![](https://cdn.sspai.com/2022/09/23/article/1c02aa566cd3a5ccbb7c5c4743a98e4d)

接着按 **导出** 就会弹出字幕的 html：

![](https://cdn.sspai.com/2022/09/23/article/1d8c55462fd72bf573e07e3bbddd440e)

接着 `ctrl + a` 全选整个 html 并且 `ctrl + c` 复制：

![](https://cdn.sspai.com/2022/09/23/article/6f8ffdfacb30ff47fa35f7145511fabe)

新建新的关于这个视频笔记的 markdown 并插入自己设置好的视频模板（其实就只是一些元数据）：

![](https://cdn.sspai.com/2022/09/23/article/74ba2a1793b5f6f6088ef6f1a19d1b1c)

![](https://cdn.sspai.com/2022/09/23/article/21bc06f4c9f699ddad589199e1bcc986)

接着在这个 markdown 里 `ctrl + v` 一下，可以看到复制进来了，并且把选中的这三行没用的信息删了：

![](https://cdn.sspai.com/2022/09/23/article/39e702af956dedc15e0cdf66458172ae)

### 使用正则替换修改字幕笔记的格式

复制一下视频的地址：

![](https://cdn.sspai.com/2022/09/23/article/d67206dbcd52388ce7801ed543d631e1)

回到刚刚新建的笔记，按 `ctrl + alt + f` 进行正则替换：

![](https://cdn.sspai.com/2022/09/23/article/ab0b38aa30b561ce10a66139f1c72d6c)

可以看到替换成功了 👇

![](https://cdn.sspai.com/2022/09/23/article/53c41cdfd7433ed301d27b9069db000e)

注意：想跳转视频一定要切换到 `查看视图`

![](https://cdn.sspai.com/2022/09/23/article/af647cbbd4fcc53459228d011a9c1e82)

结尾

这篇文章末尾就不写参考了，因为纯纯是我的经验分享，如有雷同是他抄我的。  
该给的链接都给了，希望大家不要忘了给那些开源插件点个 star 哦~

我常用的 Obsidian 插件链接：

链接：[https://pan.baidu.com/s/1i-eKm4UdqXAigvwUEQn8QQ](https://sspai.com/link?target=https%3A%2F%2Fpan.baidu.com%2Fs%2F1i-eKm4UdqXAigvwUEQn8QQ)

提取码：oyac

本博客所有文章除特别声明外，均采用 [CC BY-SA 4.0 协议](https://sspai.com/link?target=https%3A%2F%2Fcreativecommons.org%2Flicenses%2Fby-sa%2F4.0%2Fdeed.zh) ，转载请注明出处！