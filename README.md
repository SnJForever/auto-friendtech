![](https://i.ibb.co/nz5X3Lm/20230907173458.png)
![](https://i.ibb.co/pr54WvL/20230914233252.png)

# Friend Tech 自动交易脚本

## 功能

- Twitter 关注数检测
- 链上交易监控
- 自动获利
- 低手续费买卖

## 启动前准备

确保你的网络能够通畅的访问 Twitter 等网站，并且已经安装了最新版本的 NodeJS.

### 获取你的 `authorization` 和 `private key`

如果你是懂抓包的技术人员，则自行通过抓包工具抓包，如果不是，可以通过访问网页版的 friendtech 来获取你的 `authorization`，具体步骤如下

1. 使用 `chrome` 访问 [https://www.friend.tech/](https://www.friend.tech/)
2. 按 `F12` 打开开发者工具，点击以下图标切换到手机预览模式

    ![](https://i.ibb.co/x6D3827/20230914234941.png)

3. 切换之后 `F5` 刷新网页，把 开发者工具切换到 `Console`，输入代码 `document.querySelector('[class^=Home_modalCustomContainer]').remove()` 后回车
    ![](https://i.ibb.co/6vjwhpb/20230914235906.png)

4. 此时你的网页已经可以正常操作了，点击 `Sign In`` 进行登录
5. 登录进来之后，同样在 Console 面板下输入代码 `localStorage.getItem("privy:token")`，双引号内的那串文字就是你的 `authorization`
    ![](https://i.ibb.co/Xjy2mRD/20230915000338.png)

6. 接着你需要导出你的钱包私钥复制出来备用
    ![](https://i.ibb.co/M8QhDtS/20230915000539.png)

7. 得到 `authorization` 和 `private key` 之后，下载代码

### 加密你的 `authorization` 和 `private key`

1. 出于安全考虑，我不建议你以明文形式存储密码，代码中 `utils` 目录下提供了 `encrypt.js` 和 `decrypt.js` 进行加解密，
    - 将 `// console.log(encrypt('your key', 'password1', 'password2'));` 的注释 `//` 删掉，
    - 填充你要加密的信息和密码
    - 执行 `npm run encrypt`，控制台输出的就是加密后的信息，把加密后的信息填入 `wallets.example.json` 即可

2. 如果你不想要加密，则删除 index.js 代码底部代码由原来的

```js
const password1 = readlineSync.question("Password1: ", {
    hideEchoBack: true, // The typed text on screen is hidden by `*` (default).
});
const password2 = readlineSync.question("Password2: ", {
    hideEchoBack: true, // The typed text on screen is hidden by `*` (default).
});
process.env.pw1 = password1;
process.env.pw2 = password2;
if (password1 && password2) {
    for (let index = 0; index < wallets.length; index++) {
        const wallet = wallets[index];
    main({
        ...wallet,
      pk: decrypt(wallet.pk, password1, password2),
      authorization: decrypt(wallet.authorization, password1, password2),
    });
  }
}
```

替换为:

```js
  for (let index = 0; index < wallets.length; index++) {
      const wallet = wallets[index];
    main({
        ...wallet,
    });
  }
```

3. 在填好了 `wallets.example.json` 需要的信息之后，将 `wallets.example.json` 改名 `wallets.json`

## 自定义配置

目前代码中的买入策略是关注数大于 5000 并且价格低于 0.0005 ETH 或者关注数大于 40000 并且价格低于 0.004 ETH，才会触发买入，你可以在 `wallets.json` 中调整这两个策略，如果想要增加其他策略，自行修改 `index.js` 中的 `checkIfBuy` 代码

卖出策略只有一个，每隔 30 秒会查以下你持有的 key 有没有新的买入，如果有，并且利润大于 2，则执行卖出，你可以在 `wallets.json` 中通过修改 `sellBenefit` 的值改变卖出策略，值可以是复数，比如 -99999, 可以卖出你持有的所有 key

`whiteList` 是你不想要卖出的 key 的白名单，通常写自己的地址

`blockList` 是你不想要买入的 key 的禁买名单，没有填 `[]` 即可

## 脚本启动

1. 安装 [Nodejs](https://nodejs.org/en/download)
2. 下载代码
3. 使用 cmd，将路径导航到代码文件夹下，执行 `npm install`
        ![](https://i.ibb.co/G7JX1jv/20230915100955.png)

4. 执行 `npm run start`，你可以看到这个界面，输入你的解密密码
        ![](https://i.ibb.co/L9bcBzV/20230915101119.png)

5. 看到像这样的界面说明执行成功了
        ![](https://i.ibb.co/XL30Nh6/20230915103229.png)

## 注意事项

此脚本仅用于学习交流目的，不保证盈利，甚至会有亏损风险，请基于个人意愿决定是否使用。代码完全开源无后门，使用的依赖库也是都是开源库，有代码能力请自行进行审查

如果你有任何问题，可以提 Issue，或者在在推特上关注我 [@zmzimpl](https://twitter.com/zmzimpl) 在推文下留言询问，我看到了会回复