# 6.1 介绍 Web3.js

完成第五课以后，我们的僵尸 DApp 的 Solidity 合约部分就完成了。现在我们来做一个基本的网页好让你的用户能玩它。 要做到这一点，我们将使用以太坊基金发布的 JavaScript 库 —— ***Web3.js\***.

## 什么是 Web3.js?

还记得么？以太坊网络是由节点组成的，每一个节点都包含了区块链的一份拷贝。当你想要调用一份智能合约的一个方法，你需要从其中一个节点中查找并告诉它:

1. 智能合约的地址
2. 你想调用的方法，以及
3. 你想传入那个方法的参数

以太坊节点只能识别一种叫做 ***JSON-RPC\*** 的语言。这种语言直接读起来并不好懂。当你你想调用一个合约的方法的时候，需要发送的查询语句将会是这样的：

```json
// 哈……祝你写所有这样的函数调用的时候都一次通过
// 往右边拉…… ==>
{"jsonrpc":"2.0","method":"eth_sendTransaction","params":[{"from":"0xb60e8dd61c5d32be8058bb8eb970870f07233155","to":"0xd46e8dd67c5d32be8058bb8eb970870f07244567","gas":"0x76c0","gasPrice":"0x9184e72a000","value":"0x9184e72a","data":"0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"}],"id":1}
```

幸运的是 Web3.js 把这些令人讨厌的查询语句都隐藏起来了， 所以你只需要与方便易懂的 JavaScript 界面进行交互即可。

你不需要构建上面的查询语句，在你的代码中调用一个函数看起来将是这样：

```js
CryptoZombies.methods.createRandomZombie("Vitalik Nakamoto 🤔")
  .send({ from: "0xb60e8dd61c5d32be8058bb8eb970870f07233155", gas: "3000000" })
```

我们将在接下来的几章详细解释这些语句，不过首先我们来把 Web3.js 环境搭建起来。

## 准备好了么？

取决于你的项目工作流程和你的爱好，你可以用一些常用工具把 Web3.js 添加进来：

```bash
// 用 NPM
npm install web3

// 用 Yarn
yarn add web3

// 用 Bower
bower install web3

// ...或者其他。
```

甚至，你可以从 [github](https://github.com/ethereum/web3.js/blob/1.0/dist/web3.min.js) 直接下载压缩后的 `.js` 文件 然后包含到你的项目文件中：

```html
<script language="javascript" type="text/javascript" src="web3.min.js"></script>
```

因为我们不想让你花太多在项目环境搭建上，在本教程中我们将使用上面的 `script` 标签来将 Web3.js 引入。

## 实战演习

我们为你建立了一个HTML 项目空壳 —— `index.html`。假设在和 `index.html` 同个文件夹里有一份 `web3.min.js`

1. 使用上面的 `script` 标签代码把 `web3.js` 添加进去以备接下来使用。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>CryptoZombies front-end</title>
    <script language="javascript" type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script language="javascript" type="text/javascript" src="web3.min.js"></script>
  </head>
  <body>

    <script>
      // Start here
    </script>
  </body>
</html>
```

# 6.2 Web3 提供者

太棒了。现在我们的项目中有了Web3.js, 来初始化它然后和区块链对话吧。

首先我们需要 ***Web3 Provider\***.

要记住，以太坊是由共享同一份数据的相同拷贝的 **_节点_** 构成的。 在 Web3.js 里设置 Web3 的 `Provider`（提供者） 告诉我们的代码应该和 **哪个节点** 交互来处理我们的读写。这就好像在传统的 Web 应用程序中为你的 API 调用设置远程 Web 服务器的网址。

你可以运行你自己的以太坊节点来作为 Provider。 不过，有一个第三方的服务，可以让你的生活变得轻松点，让你不必为了给你的用户提供DApp而维护一个以太坊节点— ***Infura\***.

## Infura

[Infura](https://infura.io/) 是一个服务，它维护了很多以太坊节点并提供了一个缓存层来实现高速读取。你可以用他们的 API 来免费访问这个服务。 用 Infura 作为节点提供者，你可以不用自己运营节点就能很可靠地向以太坊发送、接收信息。

你可以通过这样把 Infura 作为你的 Web3 节点提供者：

```js
var web3 = new Web3(new Web3.providers.WebsocketProvider("wss://mainnet.infura.io/ws"));
```

不过，因为我们的 DApp 将被很多人使用，这些用户不单会从区块链读取信息，还会向区块链 **_写_** 入信息，我们需要用一个方法让用户可以用他们的私钥给事务签名。

> 注意: 以太坊 (以及通常意义上的 blockchains )使用一个公钥/私钥对来对给事务做数字签名。把它想成一个数字签名的异常安全的密码。这样当我修改区块链上的数据的时候，我可以用我的公钥来 **证明** 我就是签名的那个。但是因为没人知道我的私钥，所以没人能伪造我的事务。

加密学非常复杂，所以除非你是个专家并且的确知道自己在做什么，你最好不要在你应用的前端中管理你用户的私钥。

不过幸运的是，你并不需要，已经有可以帮你处理这件事的服务了： ***Metamask\***.

## Metamask

[Metamask](https://metamask.io/) 是 Chrome 和 Firefox 的浏览器扩展， 它能让用户安全地维护他们的以太坊账户和私钥， 并用他们的账户和使用 Web3.js 的网站互动（如果你还没用过它，你肯定会想去安装的——这样你的浏览器就能使用 Web3.js 了，然后你就可以和任何与以太坊区块链通信的网站交互了）

作为开发者，如果你想让用户从他们的浏览器里通过网站和你的DApp交互（就像我们在 CryptoZombies 游戏里一样），你肯定会想要兼容 Metamask 的。

> **注意**: Metamask 默认使用 Infura 的服务器做为 web3 提供者。 就像我们上面做的那样。不过它还为用户提供了选择他们自己 Web3 提供者的选项。所以使用 Metamask 的 web3 提供者，你就给了用户选择权，而自己无需操心这一块。

## 使用 Metamask 的 web3 提供者

Metamask 把它的 web3 提供者注入到浏览器的全局 JavaScript对象`web3`中。所以你的应用可以检查 `web3` 是否存在。若存在就使用 `web3.currentProvider` 作为它的提供者。

这里是一些 Metamask 提供的示例代码，用来检查用户是否安装了MetaMask，如果没有安装就告诉用户需要安装MetaMask来使用我们的应用。

```js
window.addEventListener('load', function() {

  // 检查web3是否已经注入到(Mist/MetaMask)
  if (typeof web3 !== 'undefined') {
    // 使用 Mist/MetaMask 的提供者
    web3js = new Web3(web3.currentProvider);
  } else {
    // 处理用户没安装的情况， 比如显示一个消息
    // 告诉他们要安装 MetaMask 来使用我们的应用
  }

  // 现在你可以启动你的应用并自由访问 Web3.js:
  startApp()

})
```

你可以在你所有的应用中使用这段样板代码，好检查用户是否安装以及告诉用户安装 MetaMask。

> 注意: 除了MetaMask，你的用户也可能在使用其他他的私钥管理应用，比如 **Mist** 浏览器。不过，它们都实现了相同的模式来注入 `web3` 变量。所以我这里描述的方法对两者是通用的。

## 实战演习

我们在HTML文件中的 `</body>` 标签前面放置了一个空的 `script` 标签。可以把这节课的 JavaScript 代码写在里面。

1. 把上面用来检测 MetaMask 是否安装的模板代码粘贴进来。请粘贴到以 `window.addEventListener` 开头的代码块中。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>CryptoZombies front-end</title>
    <script
      language="javascript"
      type="text/javascript"
      src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"
    ></script>
    <script
      language="javascript"
      type="text/javascript"
      src="web3.min.js"
    ></script>
  </head>
  <body>
    <script>
      // Start here
      window.addEventListener('load', function () {
        // 检查web3是否已经注入到(Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // 使用 Mist/MetaMask 的提供者
          web3js = new Web3(web3.currentProvider);
        } else {
          // 处理用户没安装的情况， 比如显示一个消息
          // 告诉他们要安装 MetaMask 来使用我们的应用
        }

        // 现在你可以启动你的应用并自由访问 Web3.js:
        startApp();
      });
    </script>
  </body>
</html>
```

# 6.3 和合约对话

现在，我们已经用 MetaMask 的 Web3 提供者初始化了 Web3.js。接下来就让它和我们的智能合约对话吧。

Web3.js 需要两个东西来和你的合约对话: 它的 **_地址_** 和它的 ***ABI\***。

## 合约地址

在你写完了你的智能合约后，你需要编译它并把它部署到以太坊。我们将在**下一课**中详述**部署**，因为它和写代码是截然不同的过程，所以我们决定打乱顺序，先来讲 Web3.js。

在你部署智能合约以后，它将获得一个以太坊上的永久地址。如果你还记得第二课，CryptoKitties 在以太坊上的地址是 `0x06012c8cf97BEaD5deAe237070F9587f8E7A266d`。

你需要在部署后复制这个地址以来和你的智能合约对话。

## 合约 ABI

另一个 Web3.js 为了要和你的智能合约对话而需要的东西是 ***ABI\***。

ABI 意为应用二进制接口（Application Binary Interface）。 基本上，它是以 JSON 格式表示合约的方法，告诉 Web3.js 如何以合同理解的方式格式化函数调用。

当你编译你的合约向以太坊部署时(我们将在第七课详述)， Solidity 编译器会给你 ABI，所以除了合约地址，你还需要把这个也复制下来。

因为我们这一课不会讲述部署，所以现在我们已经帮你编译了 ABI 并放在了名为`cryptozombies_abi.js`，文件中，保存在一个名为 `cryptoZombiesABI` 的变量中。

如果我们将`cryptozombies_abi.js` 包含进我们的项目，我们就能通过那个变量访问 CryptoZombies ABI 。

## 实例化 Web3.js

一旦你有了合约的地址和 ABI，你可以像这样来实例化 Web3.js。

```js
// 实例化 myContract
var myContract = new web3js.eth.Contract(myABI, myContractAddress);
```

## 实战演习

1. 在文件的 `<head>` 标签块中，用 `script` 标签引入`cryptozombies_abi.js`，好把 ABI 的定义引入项目。
2. 在 `<body>` 里的 `<script>` 开头 , 定义一个`var`，取名 `cryptoZombies`， 不过不要对其赋值，稍后我们将用这个这个变量来存储我们实例化合约。
3. 接下来，创建一个名为 `startApp()` 的 `function`。 接下来两步来完成这个方法。
4. `startApp()` 里应该做的第一件事是定义一个名为`cryptoZombiesAddress` 的变量并赋值为`"你的合约地址"` (这是你的合约在以太坊主网上的地址)。
5. 最后，来实例化我们的合约。模仿我们上面的代码，将 `cryptoZombies` 赋值为 `new` `web3js.eth.Contract` (使用我们上面代码中通过 `script` 引入的 `cryptoZombiesABI` 和 `cryptoZombiesAddress`)。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>CryptoZombies front-end</title>
    <script language="javascript" type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script language="javascript" type="text/javascript" src="web3.min.js"></script>
    <!-- 1. Include cryptozombies_abi.js here -->
    <script language="javascript" type="text/javascript" src="cryptozombies_abi.js"></script>
  </head>
  <body>

    <script>
      // 2. Start code here
      var cryptoZombies;

      function startApp() {
        var cryptoZombiesAddress = "你的合约地址";
        cryptoZombies = new web3js.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);
      }
      window.addEventListener('load', function() {

        // Checking if Web3 has been injected by the browser (Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // Use Mist/MetaMask's provider
          web3js = new Web3(web3.currentProvider);
        } else {
          // Handle the case where the user doesn't have Metamask installed
          // Probably show them a message prompting them to install Metamask
        }

        // Now you can start your app & access web3 freely:
        startApp()

      })
    </script>
  </body>
</html>
```

# 6.4 调用和合约函数

我们的合约配置好了！现在来用 Web3.js 和它对话。

Web3.js 有两个方法来调用我们合约的函数: `call` and `send`.

### Call

`call` 用来调用 `view` 和 `pure` 函数。它只运行在本地节点，不会在区块链上创建事务。

> **复习:** `view` 和 `pure` 函数是只读的并不会改变区块链的状态。它们也不会消耗任何gas。用户也不会被要求用MetaMask对事务签名。

使用 Web3.js，你可以如下 `call` 一个名为`myMethod`的方法并传入一个 `123` 作为参数：

```js
myContract.methods.myMethod(123).call()
```

### Send

`send` 将创建一个事务并改变区块链上的数据。你需要用 `send` 来调用任何非 `view` 或者 `pure` 的函数。

> **注意:** `send` 一个事务将要求用户支付gas，并会要求弹出对话框请求用户使用 Metamask 对事务签名。在我们使用 Metamask 作为我们的 web3 提供者的时候，所有这一切都会在我们调用 `send()` 的时候自动发生。而我们自己无需在代码中操心这一切，挺爽的吧。

使用 Web3.js, 你可以像这样 `send` 一个事务调用`myMethod` 并传入 `123` 作为参数：

```js
myContract.methods.myMethod(123).send()
```

语法几乎 `call()`一模一样。

## 获取僵尸数据

来看一个使用 `call` 读取我们合约数据的真实例子

回忆一下，我们定义我们的僵尸数组为 `公开`(public):

```solidity
Zombie[] public zombies;
```

在 Solidity 里，当你定义一个 `public`变量的时候， 它将自动定义一个公开的 "getter" 同名方法， 所以如果你像要查看 id 为 `15` 的僵尸，你可以像一个函数一样调用它： `zombies(15)`.

这是如何在外面的前端界面中写一个 JavaScript 方法来传入一个僵尸 id，在我们的合同中查询那个僵尸并返回结果

> 注意: 本课中所有的示例代码都使用 Web3.js 的 **1.0 版**，此版本使用的是 Promises 而不是回调函数。你在线上看到的其他教程可能还在使用老版的 Web3.js。在1.0版中，语法改变了不少。如果你从其他教程中复制代码，先确保你们使用的是相同版本的Web3.js。

```js
function getZombieDetails(id) {
  return cryptoZombies.methods.zombies(id).call()
}

// 调用函数并做一些其他事情
getZombieDetails(15)
.then(function(result) {
  console.log("Zombie 15: " + JSON.stringify(result));
});
```

我们来看看这里都做了什么

`cryptoZombies.methods.zombies(id).call()` 将和 Web3 提供者节点通信，告诉它返回从我们的合约中的 `Zombie[] public zombies`，`id`为传入参数的僵尸信息。

注意这是 **异步的**，就像从外部服务器中调用API。所以 Web3 在这里返回了一个 Promises. (如果你对 JavaScript的 Promises 不了解，最好先去学习一下这方面知识再继续)。

一旦那个 `promise` 被 `resolve`, (意味着我们从 Web3 提供者那里获得了响应)，我们的例子代码将执行 `then` 语句中的代码，在控制台打出 `result`。

`result` 是一个像这样的 JavaScript 对象：

```json
{
  "name": "H4XF13LD MORRIS'S COOLER OLDER BROTHER",
  "dna": "1337133713371337",
  "level": "9999",
  "readyTime": "1522498671",
  "winCount": "999999999",
  "lossCount": "0" // Obviously.
}
```

我们可以用一些前端逻辑代码来解析这个对象并在前端界面友好展示。

## 实战演习

我们已经帮你把 `getZombieDetails` 复制进了代码。

1. 先为`zombieToOwner` 创建一个类似的函数。如果你还记得 `ZombieFactory.sol`，我们有一个长这样的映射：

   ```solidity
   mapping (uint => address) public zombieToOwner;
   ```

   定义一个 JavaScript 方法，起名为 `zombieToOwner`。和上面的 `getZombieDetails` 类似， 它将接收一个`id` 作为参数，并返回一个 Web3.js `call` 我们合约里的`zombieToOwner` 。

2. 之后在下面，为 `getZombiesByOwner` 定义一个方法。如果你还能记起 `ZombieHelper.sol`，这个方法定义像这样：

   ```solidity
   function getZombiesByOwner(address _owner)
   ```

   我们的 `getZombiesByOwner` 方法将接收 `owner` 作为参数，并返回一个对我们函数 `getZombiesByOwner`的 Web3.js `call`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>CryptoZombies front-end</title>
    <script language="javascript" type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script language="javascript" type="text/javascript" src="web3.min.js"></script>
    <script language="javascript" type="text/javascript" src="cryptozombies_abi.js"></script>
  </head>
  <body>

    <script>
      var cryptoZombies;

      function startApp() {
        var cryptoZombiesAddress = "YOUR_CONTRACT_ADDRESS";
        cryptoZombies = new web3js.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);
      }

      function getZombieDetails(id) {
        return cryptoZombies.methods.zombies(id).call()
      }

      // 1. Define `zombieToOwner` here
      function zombieToOwner(id) {
        return cryptoZombies.methods.zombieToOwner(id).call();
      }
      // 2. Define `getZombiesByOwner` here
      function getZombiesByOwner(owner) {
        return cryptoZombies.methods.getZombiesByOwner(owner).call();
      }
      window.addEventListener('load', function() {

        // Checking if Web3 has been injected by the browser (Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // Use Mist/MetaMask's provider
          web3js = new Web3(web3.currentProvider);
        } else {
          // Handle the case where the user doesn't have Metamask installed
          // Probably show them a message prompting them to install Metamask
        }

        // Now you can start your app & access web3 freely:
        startApp()

      })
    </script>
  </body>
</html>
```

# 6.5 MetaMask 和账户

太棒了！你成功地写了一些前端代码来和你的第一个智能合约交互。

接下来我们综合一下——比如我们想让我们应用的首页显示用户的整个僵尸大军。

毫无疑问我们首先需要用 `getZombiesByOwner(owner)` 来查询当前用户的所有僵尸ID。

但是我们的 Solidity 合约需要 `owner` 作为 Solidity `address`。我们如何能知道应用用户的地址呢？

## 获得 MetaMask中的用户账户

MetaMask 允许用户在扩展中管理多个账户。

我们可以通过这样来获取 `web3` 变量中激活的当前账户：

```js
var userAccount = web3.eth.accounts[0]
```

因为用户可以随时在 MetaMask 中切换账户，我们的应用需要监控这个变量，一旦改变就要相应更新界面。例如，若用户的首页展示它们的僵尸大军，当他们在 MetaMask 中切换了账号，我们就需要更新页面来展示新选择的账户的僵尸大军。

我们可以通过 `setInterval` 方法来做:

```js
var accountInterval = setInterval(function() {
  // 检查账户是否切换
  if (web3.eth.accounts[0] !== userAccount) {
    userAccount = web3.eth.accounts[0];
    // 调用一些方法来更新界面
    updateInterface();
  }
}, 100);
```

这段代码做的是，每100毫秒检查一次 `userAccount` 是否还等于 `web3.eth.accounts[0]` (比如：用户是否还激活了那个账户)。若不等，则将 当前激活用户赋值给 `userAccount`，然后调用一个函数来更新界面。

## 实战演习

我们来让应用在页面第一次加载的时候显示用户的僵尸大军，监控当前 MetaMask 中的激活账户，并在账户发生改变的时候刷新显示。

1. 定义一个名为`userAccount`的变量，不给任何初始值。

2. 在 `startApp()`函数的最后，复制粘贴上面样板代码中的 `accountInterval` 方法进去。

3. 将 `updateInterface();`替换成一个 `getZombiesByOwner` 的 `call` 函数，并传入 `userAccount`。

4. 在 `getZombiesByOwner` 后面链式调用`then` 语句，并将返回的结果传入名为 `displayZombies` 的函数。 (语句像这样: `.then(displayZombies);`).

   我们还没有 `displayZombies` 函数，将于下一章实现。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>CryptoZombies front-end</title>
    <script language="javascript" type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script language="javascript" type="text/javascript" src="web3.min.js"></script>
    <script language="javascript" type="text/javascript" src="cryptozombies_abi.js"></script>
  </head>
  <body>

    <script>
      var cryptoZombies;
      // 1. declare `userAccount` here
      var userAccount;
      function startApp() {
        var cryptoZombiesAddress = "YOUR_CONTRACT_ADDRESS";
        cryptoZombies = new web3js.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);

        // 2. Create `setInterval` code here
        var accountInterval = setInterval(function() {
            // 检查账户是否切换
            if (web3.eth.accounts[0] !== userAccount) {
              userAccount = web3.eth.accounts[0];
              // 调用一些方法来更新界面
              getZombiesByOwner(userAccount).then(displayZombies);
            }
        }, 100);
      }

      function getZombieDetails(id) {
        return cryptoZombies.methods.zombies(id).call()
      }

      function zombieToOwner(id) {
        return cryptoZombies.methods.zombieToOwner(id).call()
      }

      function getZombiesByOwner(owner) {
        return cryptoZombies.methods.getZombiesByOwner(owner).call()
      }

      window.addEventListener('load', function() {

        // Checking if Web3 has been injected by the browser (Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // Use Mist/MetaMask's provider
          web3js = new Web3(web3.currentProvider);
        } else {
          // Handle the case where the user doesn't have Metamask installed
          // Probably show them a message prompting them to install Metamask
        }

        // Now you can start your app & access web3 freely:
        startApp()

      })
    </script>
  </body>
</html>

```

# 6.6 发送事务

这下我们的界面能检测用户的 MetaMask 账户，并自动在首页显示它们的僵尸大军了，有没有很棒？

现在我们来看看用 `send` 函数来修改我们智能合约里面的数据。

相对 `call` 函数，`send` 函数有如下主要区别:

1. `send` 一个事务需要一个 `from` 地址来表明谁在调用这个函数（也就是你 Solidity 代码里的 `msg.sender` )。 我们需要这是我们 DApp 的用户，这样一来 MetaMask 才会弹出提示让他们对事务签名。

2. `send` 一个事务将花费 gas

3. 在用户 `send` 一个事务到该事务对区块链产生实际影响之间有一个不可忽略的延迟。这是因为我们必须等待事务被包含进一个区块里，以太坊上一个区块的时间平均下来是15秒左右。如果当前在以太坊上有大量挂起事务或者用户发送了过低的 gas 价格，我们的事务可能需要等待数个区块才能被包含进去，往往可能花费数分钟。

   所以在我们的代码中我们需要编写逻辑来处理这部分异步特性。

## 生成一个僵尸

我们来看一个合约中一个新用户将要调用的第一个函数: `createRandomZombie`.

作为复习，这里是合约中的 Solidity 代码：

```js
function createRandomZombie(string _name) public {
  require(ownerZombieCount[msg.sender] == 0);
  uint randDna = _generateRandomDna(_name);
  randDna = randDna - randDna % 100;
  _createZombie(_name, randDna);
}
```

这是如何在用 MetaMask 在 Web3.js 中调用这个函数的示例:j

```js
function createRandomZombie(name) {
  // 这将需要一段时间，所以在界面中告诉用户这一点
  // 事务被发送出去了
  $("#txStatus").text("正在区块链上创建僵尸，这将需要一会儿...");
  // 把事务发送到我们的合约:
  return cryptoZombies.methods.createRandomZombie(name)
  .send({ from: userAccount })
  .on("receipt", function(receipt) {
    $("#txStatus").text("成功生成了 " + name + "!");
    // 事务被区块链接受了，重新渲染界面
    getZombiesByOwner(userAccount).then(displayZombies);
  })
  .on("error", function(error) {
    // 告诉用户合约失败了
    $("#txStatus").text(error);
  });
}
```

我们的函数 `send` 一个事务到我们的 Web3 提供者，然后链式添加一些事件监听:

- `receipt` 将在合约被包含进以太坊区块上以后被触发，这意味着僵尸被创建并保存进我们的合约了。
- `error` 将在事务未被成功包含进区块后触发，比如用户未支付足够的 gas。我们需要在界面中通知用户事务失败以便他们可以再次尝试。

> 注意:你可以在调用 `send` 时选择指定 `gas` 和 `gasPrice`， 例如： `.send({ from: userAccount, gas: 3000000 })`。如果你不指定，MetaMask 将让用户自己选择数值。

## 实战演习

我们添加了一个`div`， 指定 ID 为 `txStatus` — 这样我们可以通过更新这个 div 来通知用户事务的状态。

1. 在 `displayZombies`下面， 复制粘贴上面 `createRandomZombie` 的代码。

2. 我们来实现另外一个函数 `feedOnKitty`：

   调用 `feedOnKitty` 的逻辑几乎一样 — 我们将发送一个事务来调用这个函数，并且成功的事务会为我们创建一个僵尸，所以我们希望在成功后重新绘制界面。

   在 `createRandomZombie` 下面复制粘贴它的代码，改动这些地方:

   a) 给其命名为 `feedOnKitty`， 它将接收两个参数 `zombieId` 和 `kittyId`

   b) `#txStatus` 的文本内容将更新为: `"正在吃猫咪，这将需要一会儿..."`

   c) 让其调用我们合约里面的 `feedOnKitty` 函数并传入相同的参数

   d) `#txStatus` 里面的的成功信息应该是 `"吃了一只猫咪并生成了一只新僵尸！"`

```solidity
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>CryptoZombies front-end</title>
    <script language="javascript" type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script language="javascript" type="text/javascript" src="web3.min.js"></script>
    <script language="javascript" type="text/javascript" src="cryptozombies_abi.js"></script>
  </head>
  <body>
    <div id="txStatus"></div>
    <div id="zombies"></div>

    <script>
      var cryptoZombies;
      var userAccount;

      function startApp() {
        var cryptoZombiesAddress = "YOUR_CONTRACT_ADDRESS";
        cryptoZombies = new web3js.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);

        var accountInterval = setInterval(function() {
          // Check if account has changed
          if (web3.eth.accounts[0] !== userAccount) {
            userAccount = web3.eth.accounts[0];
            // Call a function to update the UI with the new account
            getZombiesByOwner(userAccount)
            .then(displayZombies);
          }
        }, 100);
      }

      function displayZombies(ids) {
        $("#zombies").empty();
        for (id of ids) {
          // Look up zombie details from our contract. Returns a `zombie` object
          getZombieDetails(id)
          .then(function(zombie) {
            // Using ES6's "template literals" to inject variables into the HTML.
            // Append each one to our #zombies div
            $("#zombies").append(`<div class="zombie">
              <ul>
                <li>Name: ${zombie.name}</li>
                <li>DNA: ${zombie.dna}</li>
                <li>Level: ${zombie.level}</li>
                <li>Wins: ${zombie.winCount}</li>
                <li>Losses: ${zombie.lossCount}</li>
                <li>Ready Time: ${zombie.readyTime}</li>
              </ul>
            </div>`);
          });
        }
      }

      function createRandomZombie(name) {
        // This is going to take a while, so update the UI to let the user know
        // the transaction has been sent
        $("#txStatus").text("Creating new zombie on the blockchain. This may take a while...");
        // Send the tx to our contract:
        return cryptoZombies.methods.createRandomZombie(name)
        .send({ from: userAccount })
        .on("receipt", function(receipt) {
          $("#txStatus").text("Successfully created " + name + "!");
          // Transaction was accepted into the blockchain, let's redraw the UI
          getZombiesByOwner(userAccount).then(displayZombies);
        })
        .on("error", function(error) {
          // Do something to alert the user their transaction has failed
          $("#txStatus").text(error);
        });
      }

      function feedOnKitty(zombieId, kittyId) {
        $("#txStatus").text("Eating a kitty. This may take a while...");
        return cryptoZombies.methods.feedOnKitty(zombieId, kittyId)
        .send({ from: userAccount })
        .on("receipt", function(receipt) {
          $("#txStatus").text("Ate a kitty and spawned a new Zombie!");
          getZombiesByOwner(userAccount).then(displayZombies);
        })
        .on("error", function(error) {
          $("#txStatus").text(error);
        });
      }

      // Start here

      function getZombieDetails(id) {
        return cryptoZombies.methods.zombies(id).call()
      }

      function zombieToOwner(id) {
        return cryptoZombies.methods.zombieToOwner(id).call()
      }

      function getZombiesByOwner(owner) {
        return cryptoZombies.methods.getZombiesByOwner(owner).call()
      }

      window.addEventListener('load', function() {

        // Checking if Web3 has been injected by the browser (Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // Use Mist/MetaMask's provider
          web3js = new Web3(web3.currentProvider);
        } else {
          // Handle the case where the user doesn't have Metamask installed
          // Probably show them a message prompting them to install Metamask
        }

        // Now you can start your app & access web3 freely:
        startApp()

      })
    </script>
  </body>
</html>
```

# 6.7 调用 Payable 函数

`attack`, `changeName`, 以及 `changeDna` 的逻辑将非常雷同，所以本课将不会花时间在上面。

> 实际上，在调用这些函数的时候已经有了非常多的重复逻辑。所以最好是重构代码把相同的代码写成一个函数。（并对`txStatus`使用模板系统——我们已经看到用类似 Vue.js 类的框架是多么整洁）

我们来看看另外一种 Web3.js 中需要特殊对待的函数 — `payable` 函数。

## 升级！

回忆一下在 `ZombieHelper` 里面，我们添加了一个 payable 函数，用户可以用来升级:

```solidity
function levelUp(uint _zombieId) external payable {
  require(msg.value == levelUpFee);
  zombies[_zombieId].level++;
}
```

和函数一起发送以太非常简单，只有一点需要注意： 我们需要指定发送多少 `wei`，而不是以太。

## 啥是 Wei?

一个 `wei` 是以太的最小单位 — 1 `ether` 等于 10^18 `wei`

太多0要数了，不过幸运的是 Web3.js 有一个转换工具来帮我们做这件事：

```
// 把 1 ETH 转换成 Wei
web3js.utils.toWei("1", "ether");
```

在我们的 DApp 里， 我们设置了 `levelUpFee = 0.001 ether`，所以调用 `levelUp` 方法的时候，我们可以让用户用以下的代码同时发送 `0.001` 以太:

```js
cryptoZombies.methods.levelUp(zombieId)
.send({ from: userAccount, value: web3js.utils.toWei("0.001","ether") })
```

## 实战演习

在 `feedOnKitty` 下面添加一个 `levelUp` 方法。代码和 `feedOnKitty` 将非常相似。不过：

1. 函数将接收一个参数, `zombieId`
2. 在发送事务之前，`txStatus` 的文本应该是 `"正在升级您的僵尸..."`
3. 当它调用合约里的`levelUp`时，它应该发送`"0.001"` ETH，并用 `toWei` 转换，如同上面例子里那样。
4. 成功之后应该显示 `"不得了了！僵尸成功升级啦！"`
5. 我们 **不** 需要在调用 `getZombiesByOwner` 后重新绘制界面 — 因为在这里我们只是修改了僵尸的级别而已。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>CryptoZombies front-end</title>
    <script language="javascript" type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script language="javascript" type="text/javascript" src="web3.min.js"></script>
    <script language="javascript" type="text/javascript" src="cryptozombies_abi.js"></script>
  </head>
  <body>
    <div id="txStatus"></div>
    <div id="zombies"></div>

    <script>
      var cryptoZombies;
      var userAccount;

      function startApp() {
        var cryptoZombiesAddress = "YOUR_CONTRACT_ADDRESS";
        cryptoZombies = new web3js.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);

        var accountInterval = setInterval(function() {
          // Check if account has changed
          if (web3.eth.accounts[0] !== userAccount) {
            userAccount = web3.eth.accounts[0];
            // Call a function to update the UI with the new account
            getZombiesByOwner(userAccount)
            .then(displayZombies);
          }
        }, 100);
      }

      function displayZombies(ids) {
        $("#zombies").empty();
        for (id of ids) {
          // Look up zombie details from our contract. Returns a `zombie` object
          getZombieDetails(id)
          .then(function(zombie) {
            // Using ES6's "template literals" to inject variables into the HTML.
            // Append each one to our #zombies div
            $("#zombies").append(`<div class="zombie">
              <ul>
                <li>Name: ${zombie.name}</li>
                <li>DNA: ${zombie.dna}</li>
                <li>Level: ${zombie.level}</li>
                <li>Wins: ${zombie.winCount}</li>
                <li>Losses: ${zombie.lossCount}</li>
                <li>Ready Time: ${zombie.readyTime}</li>
              </ul>
            </div>`);
          });
        }
      }

      function createRandomZombie(name) {
        // This is going to take a while, so update the UI to let the user know
        // the transaction has been sent
        $("#txStatus").text("Creating new zombie on the blockchain. This may take a while...");
        // Send the tx to our contract:
        return cryptoZombies.methods.createRandomZombie(name)
        .send({ from: userAccount })
        .on("receipt", function(receipt) {
          $("#txStatus").text("Successfully created " + name + "!");
          // Transaction was accepted into the blockchain, let's redraw the UI
          getZombiesByOwner(userAccount).then(displayZombies);
        })
        .on("error", function(error) {
          // Do something to alert the user their transaction has failed
          $("#txStatus").text(error);
        });
      }

      function feedOnKitty(zombieId, kittyId) {
        $("#txStatus").text("Eating a kitty. This may take a while...");
        return cryptoZombies.methods.feedOnKitty(zombieId, kittyId)
        .send({ from: userAccount })
        .on("receipt", function(receipt) {
          $("#txStatus").text("Ate a kitty and spawned a new Zombie!");
          getZombiesByOwner(userAccount).then(displayZombies);
        })
        .on("error", function(error) {
          $("#txStatus").text(error);
        });
      }

      // Start here
      function levelUp(zombieId) {
        $("#txStatus").text("正在升级您的僵尸...");
        return cryptoZombies.methods.levelUp(zombieId)
        .send({ from: userAccount, value: web3js.utils.toWei("0.001","ether") })
        .on("receipt", function(receipt) {
          $("#txStatus").text("不得了了！僵尸成功升级啦！");
        })
        .on("error", function(error) {
          $("#txStatus").text(error);
        });
      }
      function getZombieDetails(id) {
        return cryptoZombies.methods.zombies(id).call()
      }

      function zombieToOwner(id) {
        return cryptoZombies.methods.zombieToOwner(id).call()
      }

      function getZombiesByOwner(owner) {
        return cryptoZombies.methods.getZombiesByOwner(owner).call()
      }

      window.addEventListener('load', function() {

        // Checking if Web3 has been injected by the browser (Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // Use Mist/MetaMask's provider
          web3js = new Web3(web3.currentProvider);
        } else {
          // Handle the case where the user doesn't have Metamask installed
          // Probably show them a message prompting them to install Metamask
        }

        // Now you can start your app & access web3 freely:
        startApp()

      })
    </script>
  </body>
</html>
```

