# 3.1 智能协议的永固性

到现在为止，我们讲的 Solidity 和其他语言没有质的区别，它长得也很像 JavaScript。

但是，在有几点以太坊上的 DApp 跟普通的应用程序有着天壤之别。

第一个例子，在你把智能协议传上以太坊之后，它就变得***不可更改\***, 这种永固性意味着你的代码永远不能被调整或更新。

你编译的程序会一直，永久的，不可更改的，存在以太坊上。这就是 Solidity 代码的安全性如此重要的一个原因。如果你的智能协议有任何漏洞，即使你发现了也无法补救。你只能让你的用户们放弃这个智能协议，然后转移到一个新的修复后的合约上。

但这恰好也是智能合约的一大优势。代码说明一切。如果你去读智能合约的代码，并验证它，你会发现，一旦函数被定义下来，每一次的运行，程序都会严格遵照函数中原有的代码逻辑一丝不苟地执行，完全不用担心函数被人篡改而得到意外的结果。

## 外部依赖关系

在第2课中，我们将加密小猫（CryptoKitties）合约的地址硬编码到 DApp 中去了。有没有想过，如果加密小猫出了点问题，比方说，集体消失了会怎么样？ 虽然这种事情几乎不可能发生，但是，如果小猫没了，我们的 DApp 也会随之失效 -- 因为我们在 DApp 的代码中用“硬编码”的方式指定了加密小猫的地址，如果这个根据地址找不到小猫，我们的僵尸也就吃不到小猫了，而按照前面的描述，我们却没法修改合约去应付这个变化！

因此，我们不能硬编码，而要采用“函数”，以便于 DApp 的关键部分可以以参数形式修改。

比方说，我们不再一开始就把猎物地址给写入代码，而是写个函数 `setKittyContractAddress`, 运行时再设定猎物的地址，这样我们就可以随时去锁定新的猎物，也不用担心加密小猫集体消失了。

## 实战演习

请修改第2课的代码，使得可以通过程序更改 CryptoKitties 合约地址。

1. 删除采用硬编码 方式的 `ckAddress` 代码行。
2. 之前创建 `kittyContract` 变量的那行代码，修改为对 `kittyContract` 变量的声明 -- 暂时不给它指定具体的实例。
3. 创建名为 `setKittyContractAddress` 的函数， 它带一个参数 `_address`（`address`类型）， 可见性设为`external`。
4. 在函数内部，添加一行代码，将 `kittyContract` 变量设置为返回值：`KittyInterface（_address）`。

> 注意：你可能会注意到这个功能有个安全漏洞，别担心 - 咱们到下一章里解决它;）

```solidity
pragma solidity ^0.4.19;

import "./zombiefactory.sol";

contract KittyInterface {
  function getKitty(uint256 _id) external view returns (
    bool isGestating,
    bool isReady,
    uint256 cooldownIndex,
    uint256 nextActionAt,
    uint256 siringWithId,
    uint256 birthTime,
    uint256 matronId,
    uint256 sireId,
    uint256 generation,
    uint256 genes
  );
}

contract ZombieFeeding is ZombieFactory {

  // 1. 移除这一行:
  // 2. 只声明变量:
  KittyInterface kittyContract;
  // 3. 增加 setKittyContractAddress 方法
  function setKittyContractAddress(address _address) external {
    kittyContract = KittyInterface(_address);
  }
  function feedAndMultiply(uint _zombieId, uint _targetDna, string species) public {
    require(msg.sender == zombieToOwner[_zombieId]);
    Zombie storage myZombie = zombies[_zombieId];
    _targetDna = _targetDna % dnaModulus;
    uint newDna = (myZombie.dna + _targetDna) / 2;
    if (keccak256(species) == keccak256("kitty")) {
      newDna = newDna - newDna % 100 + 99;
    }
    _createZombie("NoName", newDna);
  }

  function feedOnKitty(uint _zombieId, uint _kittyId) public {
    uint kittyDna;
    (,,,,,,,,,kittyDna) = kittyContract.getKitty(_kittyId);
    feedAndMultiply(_zombieId, kittyDna, "kitty");
  }

}

```

# 3.2 Ownable Contracts

上一章中，您有没有发现任何安全漏洞呢？

呀！`setKittyContractAddress` 可见性居然申明为“外部的”（`external`），岂不是任何人都可以调用它！ 也就是说，任何调用该函数的人都可以更改 CryptoKitties 合约的地址，使得其他人都没法再运行我们的程序了。

我们确实是希望这个地址能够在合约中修改，但我可没说让每个人去改它呀。

要对付这样的情况，通常的做法是指定合约的“所有权” - 就是说，给它指定一个主人（没错，就是您），只有主人对它享有特权。

## OpenZeppelin库的`Ownable` 合约

下面是一个 `Ownable` 合约的例子： 来自 **_ OpenZeppelin _** Solidity 库的 `Ownable` 合约。 OpenZeppelin 是主打安保和社区审查的智能合约库，您可以在自己的 DApps中引用。等把这一课学完，您不要催我们发布下一课，最好利用这个时间把 OpenZeppelin 的网站看看，保管您会学到很多东西！

把楼下这个合约读读通，是不是还有些没见过代码？别担心，我们随后会解释。

```solidity
/**
 * @title Ownable
 * @dev The Ownable contract has an owner address, and provides basic authorization control
 * functions, this simplifies the implementation of "user permissions".
 */
contract Ownable {
  address public owner;
  event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

  /**
   * 构造函数，执行一次
   */
  function Ownable() public {
    owner = msg.sender;
  }

  /**
   * 自定义修饰符,执行到 onlyOwner 中的 _; 语句时，程序再返回并执行 likeABoss 中的代码。
   */
  modifier onlyOwner() {
    require(msg.sender == owner);
    _;
  }

  /**
   * onlyOwner自定义函数的修饰符，事先检验require(msg.sender == owner)
   */
  function transferOwnership(address newOwner) public onlyOwner {
    require(newOwner != address(0));
    OwnershipTransferred(owner, newOwner);
    owner = newOwner;
  }
}
```

下面有没有您没学过的东东？

- 构造函数：`function Ownable()`是一个 **_ constructor_** (构造函数)，构造函数不是必须的，它与合约同名，构造函数一生中唯一的一次执行，就是在合约最初被创建的时候。
- 函数修饰符：`modifier onlyOwner()`。 修饰符跟函数很类似，不过是用来修饰其他已有函数用的， 在其他语句执行前，为它检查下先验条件。 在这个例子中，我们就可以写个修饰符 `onlyOwner` 检查下调用者，确保只有合约的主人才能运行本函数。我们下一章中会详细讲述修饰符，以及那个奇怪的`_;`。
- `indexed` 关键字：别担心，我们还用不到它。

所以`Ownable` 合约基本都会这么干：

1. 合约创建，构造函数先行，将其 `owner` 设置为`msg.sender`（其部署者）
2. 为它加上一个修饰符 `onlyOwner`，它会限制陌生人的访问，将访问某些函数的权限锁定在 `owner` 上。
3. 允许将合约所有权转让给他人。

`onlyOwner` 简直人见人爱，大多数人开发自己的 Solidity DApps，都是从复制/粘贴 `Ownable` 开始的，从它再继承出的子类，并在之上进行功能开发。

既然我们想把 `setKittyContractAddress` 限制为 `onlyOwner` ，我们也要做同样的事情。

## 实战演习

首先，将 `Ownable` 合约的代码复制一份到新文件 `ownable.sol` 中。 接下来，创建一个 `ZombieFactory`，继承 `Ownable`。

1.在程序中导入 `ownable.sol` 的内容。 如果您不记得怎么做了，参考下 `zombiefeeding.sol`。

2.修改 `ZombieFactory` 合约， 让它继承自 `Ownable`。 如果您不记得怎么做了，看看 `zombiefeeding.sol`。

```solidity
pragma solidity ^0.4.19;

// 1. 在这里导入
import "./ownable.sol";
// 2. 在这里继承:
contract ZombieFactory is Ownable{

    event NewZombie(uint zombieId, string name, uint dna);

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    struct Zombie {
        string name;
        uint dna;
    }

    Zombie[] public zombies;

    mapping (uint => address) public zombieToOwner;
    mapping (address => uint) ownerZombieCount;

    function _createZombie(string _name, uint _dna) internal {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        zombieToOwner[id] = msg.sender;
        ownerZombieCount[msg.sender]++;
        NewZombie(id, _name, _dna);
    }

    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }

    function createRandomZombie(string _name) public {
        require(ownerZombieCount[msg.sender] == 0);
        uint randDna = _generateRandomDna(_name);
        randDna = randDna - randDna % 100;
        _createZombie(_name, randDna);
    }

}

```

# 3.3 onlyOwner 函数修饰符

现在我们有了个基本版的合约 `ZombieFactory` 了，它继承自 `Ownable` 接口，我们也可以给 `ZombieFeeding` 加上 `onlyOwner` 函数修饰符。

这就是合约继承的工作原理。记得：

```solidity
ZombieFeeding is ZombieFactory
ZombieFactory is Ownable
```

因此 `ZombieFeeding` 也是个 `Ownable`, 并可以通过 `Ownable` 接口访问父类中的函数/事件/修饰符。往后，`ZombieFeeding` 的继承者合约们同样也可以这么延续下去。

## 函数修饰符

函数修饰符看起来跟函数没什么不同，不过关键字`modifier` 告诉编译器，这是个`modifier(修饰符)`，而不是个`function(函数)`。它不能像函数那样被直接调用，只能被添加到函数定义的末尾，用以改变函数的行为。

咱们仔细读读 `onlyOwner`:

```solidity
/**
 * @dev 调用者不是‘主人’，就会抛出异常
 */
modifier onlyOwner() {
  require(msg.sender == owner);
  _;
}
```

`onlyOwner` 函数修饰符是这么用的：

```solidity
contract MyContract is Ownable {
  event LaughManiacally(string laughter);

  //注意！ `onlyOwner`上场 :
  function likeABoss() external onlyOwner {
    LaughManiacally("Muahahahaha");
  }
}
```

注意 `likeABoss` 函数上的 `onlyOwner` 修饰符。 当你调用 `likeABoss` 时，**首先执行** `onlyOwner` 中的代码， 执行到 `onlyOwner` 中的 `_;` 语句时，程序再返回并执行 `likeABoss` 中的代码。

可见，尽管函数修饰符也可以应用到各种场合，但最常见的还是放在函数执行之前添加快速的 `require`检查。

因为给函数添加了修饰符 `onlyOwner`，使得**唯有合约的主人**（也就是部署者）才能调用它。

> 注意：主人对合约享有的特权当然是正当的，不过也可能被恶意使用。比如，万一，主人添加了个后门，允许他偷走别人的僵尸呢？

> 所以非常重要的是，部署在以太坊上的 DApp，并不能保证它真正做到去中心，你需要阅读并理解它的源代码，才能防止其中没有被部署者恶意植入后门；作为开发人员，如何做到既要给自己留下修复 bug 的余地，又要尽量地放权给使用者，以便让他们放心你，从而愿意把数据放在你的 DApp 中，这确实需要个微妙的平衡。

## 实战演习

现在我们可以限制第三方对 `setKittyContractAddress`的访问，除了我们自己，谁都无法去修改它。

1. 将 `onlyOwner` 函数修饰符添加到 `setKittyContractAddress` 中。

```solidity
pragma solidity ^0.4.19;

import "./zombiefactory.sol";

contract KittyInterface {
  function getKitty(uint256 _id) external view returns (
    bool isGestating,
    bool isReady,
    uint256 cooldownIndex,
    uint256 nextActionAt,
    uint256 siringWithId,
    uint256 birthTime,
    uint256 matronId,
    uint256 sireId,
    uint256 generation,
    uint256 genes
  );
}

contract ZombieFeeding is ZombieFactory {

  KittyInterface kittyContract;

  // 修改这个函数:
  function setKittyContractAddress(address _address) external onlyOwner{
    kittyContract = KittyInterface(_address);
  }

  function feedAndMultiply(uint _zombieId, uint _targetDna, string species) public {
    require(msg.sender == zombieToOwner[_zombieId]);
    Zombie storage myZombie = zombies[_zombieId];
    _targetDna = _targetDna % dnaModulus;
    uint newDna = (myZombie.dna + _targetDna) / 2;
    if (keccak256(species) == keccak256("kitty")) {
      newDna = newDna - newDna % 100 + 99;
    }
    _createZombie("NoName", newDna);
  }

  function feedOnKitty(uint _zombieId, uint _kittyId) public {
    uint kittyDna;
    (,,,,,,,,,kittyDna) = kittyContract.getKitty(_kittyId);
    feedAndMultiply(_zombieId, kittyDna, "kitty");
  }
}
```

# 3.4  Gas

厉害！现在我们懂了如何在禁止第三方修改我们的合约的同时，留个后门给咱们自己去修改。

让我们来看另一种使得 Solidity 编程语言与众不同的特征：

## Gas - 驱动以太坊DApps的能源

在 Solidity 中，你的用户想要每次执行你的 DApp 都需要支付一定的 ***gas\***，gas 可以用以太币购买，因此，用户每次跑 DApp 都得花费以太币。

一个 DApp 收取多少 gas 取决于功能逻辑的复杂程度。每个操作背后，都在计算完成这个操作所需要的计算资源，（比如，存储数据就比做个加法运算贵得多）， 一次操作所需要花费的 ***gas\*** 等于这个操作背后的所有运算花销的总和。

由于运行你的程序需要花费用户的真金白银，在以太坊中代码的编程语言，比其他任何编程语言都更强调优化。同样的功能，使用笨拙的代码开发的程序，比起经过精巧优化的代码来，运行花费更高，这显然会给成千上万的用户带来大量不必要的开销。

## 为什么要用 ***gas\*** 来驱动？

以太坊就像一个巨大、缓慢、但非常安全的电脑。当你运行一个程序的时候，网络上的每一个节点都在进行相同的运算，以验证它的输出 —— 这就是所谓的“去中心化” 由于数以千计的节点同时在验证着每个功能的运行，这可以确保它的数据不会被被监控，或者被刻意修改。

可能会有用户用无限循环堵塞网络，抑或用密集运算来占用大量的网络资源，为了防止这种事情的发生，以太坊的创建者为以太坊上的资源制定了价格，想要在以太坊上运算或者存储，你需要先付费。

> 注意：如果你使用侧链，倒是不一定需要付费，比如咱们在 Loom Network 上构建的 CryptoZombies 就免费。你不会想要在以太坊主网上玩儿“魔兽世界”吧？ - 所需要的 gas 可能会买到你破产。但是你可以找个算法理念不同的侧链来玩它。我们将在以后的课程中咱们会讨论到，什么样的 DApp 应该部署在太坊主链上，什么又最好放在侧链。

## 省 gas 的招数：结构封装 （Struct packing）

在第1课中，我们提到除了基本版的 `uint` 外，还有其他变种 `uint`：`uint8`，`uint16`，`uint32`等。

通常情况下我们不会考虑使用 `uint` 变种，因为无论如何定义 `uint`的大小，Solidity 为它保留256位的存储空间。例如，使用 `uint8` 而不是`uint`（`uint256`）不会为你节省任何 gas。

除非，把 `uint` 绑定到 `struct` 里面。

如果一个 `struct` 中有多个 `uint`，则尽可能使用较小的 `uint`, Solidity 会将这些 `uint` 打包在一起，从而占用较少的存储空间。例如：

```solidity
struct NormalStruct {
  uint a;
  uint b;
  uint c;
}

struct MiniMe {
  uint32 a;
  uint32 b;
  uint c;
}

// 因为使用了结构打包，`mini` 比 `normal` 占用的空间更少
NormalStruct normal = NormalStruct(10, 20, 30);
MiniMe mini = MiniMe(10, 20, 30); 
```

所以，当 `uint` 定义在一个 `struct` 中的时候，尽量使用最小的整数子类型以节约空间。 并且把同样类型的变量放一起（即在 struct 中将把变量按照类型依次放置），这样 Solidity 可以将存储空间最小化。例如，有两个 `struct`：

```solidity
uint c; uint32 a; uint32 b;` 和 `uint32 a; uint c; uint32 b;
```

前者比后者需要的gas更少，因为前者把`uint32`放一起了。

## 实战演习

在本课中，咱们给僵尸添2个新功能：`level` 和 `readyTime` - 后者是用来实现一个“冷却定时器”，以限制僵尸猎食的频率。

让我们回到 `zombiefactory.sol`。

1. 为 `Zombie` 结构体 添加两个属性：`level`（`uint32`）和`readyTime`（`uint32`）。因为希望同类型数据打成一个包，所以把它们放在结构体的末尾。

32位足以保存僵尸的级别和时间戳了，这样比起使用普通的`uint`（256位），可以更紧密地封装数据，从而为我们省点 gas。

````solidity
pragma solidity ^0.4.19;

import "./ownable.sol";

contract ZombieFactory is Ownable {

    event NewZombie(uint zombieId, string name, uint dna);

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    struct Zombie {
        string name;
        uint dna;
        //在这里添加数据
        uint32 level;
        uint32 readyTime;
    }

    Zombie[] public zombies;

    mapping (uint => address) public zombieToOwner;
    mapping (address => uint) ownerZombieCount;

    function _createZombie(string _name, uint _dna) internal {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        zombieToOwner[id] = msg.sender;
        ownerZombieCount[msg.sender]++;
        NewZombie(id, _name, _dna);
    }

    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }

    function createRandomZombie(string _name) public {
        require(ownerZombieCount[msg.sender] == 0);
        uint randDna = _generateRandomDna(_name);
        randDna = randDna - randDna % 100;
        _createZombie(_name, randDna);
    }

}
````

# 3.5 时间单位

`level` 属性表示僵尸的级别。以后，在我们创建的战斗系统中，打胜仗的僵尸会逐渐升级并获得更多的能力。

`readyTime` 稍微复杂点。我们希望增加一个“冷却周期”，表示僵尸在两次猎食或攻击之之间必须等待的时间。如果没有它，僵尸每天可能会攻击和繁殖1,000次，这样游戏就太简单了。

为了记录僵尸在下一次进击前需要等待的时间，我们使用了 Solidity 的时间单位。

## 时间单位

Solidity 使用自己的本地时间单位。

变量 `now` 将返回当前的unix时间戳（自1970年1月1日以来经过的秒数）。我写这句话时 unix 时间是 `1515527488`。

> 注意：Unix时间传统用一个32位的整数进行存储。这会导致“2038年”问题，当这个32位的unix时间戳不够用，产生溢出，使用这个时间的遗留系统就麻烦了。所以，如果我们想让我们的 DApp 跑够20年，我们可以使用64位整数表示时间，但为此我们的用户又得支付更多的 gas。真是个两难的设计啊！

Solidity 还包含`秒(seconds)`，`分钟(minutes)`，`小时(hours)`，`天(days)`，`周(weeks)` 和 `年(years)` 等时间单位。它们都会转换成对应的秒数放入 `uint` 中。所以 `1分钟` 就是 `60`，`1小时`是 `3600`（60秒×60分钟），`1天`是`86400`（24小时×60分钟×60秒），以此类推。

下面是一些使用时间单位的实用案例：

```solidity
uint lastUpdated;

// 将‘上次更新时间’ 设置为 ‘现在’
function updateTimestamp() public {
  lastUpdated = now;
}

// 如果到上次`updateTimestamp` 超过5分钟，返回 'true'
// 不到5分钟返回 'false'
function fiveMinutesHavePassed() public view returns (bool) {
  return (now >= (lastUpdated + 5 minutes));
}
```

有了这些工具，我们可以为僵尸设定“冷静时间”功能。

## 实战演习

现在咱们给DApp添加一个“冷却周期”的设定，让僵尸两次攻击或捕猎之间必须等待 **1天**。

1. 声明一个名为 `cooldownTime` 的`uint`，并将其设置为 `1 days`。（没错，”1 days“使用了复数， 否则通不过编译器）

2. 因为在上一章中我们给 `Zombie` 结构体中添加 `level` 和 `readyTime` 两个参数，所以现在创建一个新的 `Zombie` 结构体时，需要修改 `_createZombie()`，在其中把新旧参数都初始化一下。

   修改 `zombies.push` 那一行， 添加加2个参数：`1`（表示当前的 `level` ）和`uint32（now + cooldownTime）`（现在+冷却时间，表示下次允许攻击的时间 `readyTime`）。

> 注意：必须使用 `uint32（...）` 进行强制类型转换，因为 `now` 返回类型 `uint256`。所以我们需要明确将它转换成一个 `uint32` 类型的变量。

`now + cooldownTime` 将等于当前的unix时间戳（以秒为单位）加上”1天“里的秒数 - 这将等于从现在起1天后的unix时间戳。然后我们就比较，看看这个僵尸的 `readyTime`是否大于 `now`，以决定再次启用僵尸的时机有没有到来。

下一章中，我们将讨论如何通过 `readyTime` 来规范僵尸的行为。

```solidity
pragma solidity ^0.4.19;

import "./ownable.sol";

contract ZombieFactory is Ownable {

    event NewZombie(uint zombieId, string name, uint dna);

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
    // 1. 在这里定义 `cooldownTime`
    uint cooldownTime = 1 days;
    struct Zombie {
        string name;
        uint dna;
        uint32 level;
        uint32 readyTime;
    }

    Zombie[] public zombies;

    mapping (uint => address) public zombieToOwner;
    mapping (address => uint) ownerZombieCount;

    function _createZombie(string _name, uint _dna) internal {
        // 2. 修改下面这行:
        uint id = zombies.push(Zombie(_name, _dna, 1, uint32(now + cooldownTime))) - 1;
        zombieToOwner[id] = msg.sender;
        ownerZombieCount[msg.sender]++;
        NewZombie(id, _name, _dna);
    }

    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }

    function createRandomZombie(string _name) public {
        require(ownerZombieCount[msg.sender] == 0);
        uint randDna = _generateRandomDna(_name);
        randDna = randDna - randDna % 100;
        _createZombie(_name, randDna);
    }
}
```

# 3.6 僵尸冷却

现在，`Zombie` 结构体中定义好了一个 `readyTime` 属性，让我们跳到 `zombiefeeding.sol`， 去实现一个”冷却周期定时器“。

按照以下步骤修改 `feedAndMultiply`：

1. ”捕猎“行为会触发僵尸的”冷却周期“
2. 僵尸在这段”冷却周期“结束前不可再捕猎小猫

这将限制僵尸，防止其无限制地捕猎小猫或者整天不停地繁殖。将来，当我们增加战斗功能时，我们同样用”冷却周期“限制僵尸之间打斗的频率。

首先，我们要定义一些辅助函数，设置并检查僵尸的 `readyTime`。

## 将结构体作为参数传入

由于结构体的存储指针可以以参数的方式传递给一个 `private` 或 `internal` 的函数，因此结构体可以在多个函数之间相互传递。

遵循这样的语法：

```solidity
function _doStuff(Zombie storage _zombie) internal {
  // do stuff with _zombie
}
```

这样我们可以将某僵尸的引用直接传递给一个函数，而不用是通过参数传入僵尸ID后，函数再依据ID去查找。

## 实战演习

1. 先定义一个 `_triggerCooldown` 函数。它要求一个参数，`_zombie`，表示一某个僵尸的存储指针。这个函数可见性设置为 `internal`。
2. 在函数中，把 `_zombie.readyTime` 设置为 `uint32（now + cooldownTime）`。
3. 接下来，创建一个名为 `_isReady` 的函数。这个函数的参数也是名为 `_zombie` 的 `Zombie storage`。这个功能只具有 `internal` 可见性，并返回一个 `bool` 值。
4. 函数计算返回`(_zombie.readyTime <= now)`，值为 `true` 或 `false`。这个功能的目的是判断下次允许猎食的时间是否已经到了。

```solidity
pragma solidity ^0.4.19;

import "./zombiefactory.sol";

contract KittyInterface {
  function getKitty(uint256 _id) external view returns (
    bool isGestating,
    bool isReady,
    uint256 cooldownIndex,
    uint256 nextActionAt,
    uint256 siringWithId,
    uint256 birthTime,
    uint256 matronId,
    uint256 sireId,
    uint256 generation,
    uint256 genes
  );
}

contract ZombieFeeding is ZombieFactory {

  KittyInterface kittyContract;

  function setKittyContractAddress(address _address) external onlyOwner {
    kittyContract = KittyInterface(_address);
  }

  // 1. 在这里定义 `_triggerCooldown` 函数
  function _triggerCooldown(Zombie storage _zombie) internal {
    _zombie.readyTime = uint32(now + cooldownTime);
  }
  // 2. 在这里定义 `_isReady` 函数
function _isReady(Zombie storage _zombie) internal view returns (bool) {
  return (_zombie.readyTime <= now);
}
  function feedAndMultiply(uint _zombieId, uint _targetDna, string species) public {
    require(msg.sender == zombieToOwner[_zombieId]);
    Zombie storage myZombie = zombies[_zombieId];
    _targetDna = _targetDna % dnaModulus;
    uint newDna = (myZombie.dna + _targetDna) / 2;
    if (keccak256(species) == keccak256("kitty")) {
      newDna = newDna - newDna % 100 + 99;
    }
    _createZombie("NoName", newDna);
  }

  function feedOnKitty(uint _zombieId, uint _kittyId) public {
    uint kittyDna;
    (,,,,,,,,,kittyDna) = kittyContract.getKitty(_kittyId);
    feedAndMultiply(_zombieId, kittyDna, "kitty");
  }
}
```

# 3.7 公有函数和安全性

现在来修改 `feedAndMultiply` ，实现冷却周期。

回顾一下这个函数，前一课上我们将其可见性设置为`public`。你必须仔细地检查所有声明为 `public` 和 `external`的函数，一个个排除用户滥用它们的可能，谨防安全漏洞。请记住，如果这些函数没有类似 `onlyOwner` 这样的函数修饰符，用户能利用各种可能的参数去调用它们。

检查完这个函数，用户就可以直接调用这个它，并传入他们所希望的 `_targetDna` 或 `species` 。打个游戏还得遵循这么多的规则，还能不能愉快地玩耍啊！

仔细观察，这个函数只需被 `feedOnKitty()` 调用，因此，想要防止漏洞，最简单的方法就是设其可见性为 `internal`。

## 实战演习

1. 目前函数 `feedAndMultiply` 可见性为 `public`。我们将其改为 `internal` 以保障合约安全。因为我们不希望用户调用它的时候塞进一堆乱七八糟的 DNA。
2. `feedAndMultiply` 过程需要参考 `cooldownTime`。首先，在找到 `myZombie` 之后，添加一个 `require` 语句来检查 `_isReady()` 并将 `myZombie` 传递给它。这样用户必须等到僵尸的 `冷却周期` 结束后才能执行 `feedAndMultiply` 功能。
3. 在函数结束时，调用 `_triggerCooldown(myZombie)`，标明捕猎行为触发了僵尸新的冷却周期。

```solidity
pragma solidity ^0.4.19;

import "./zombiefactory.sol";

contract KittyInterface {
  function getKitty(uint256 _id) external view returns (
    bool isGestating,
    bool isReady,
    uint256 cooldownIndex,
    uint256 nextActionAt,
    uint256 siringWithId,
    uint256 birthTime,
    uint256 matronId,
    uint256 sireId,
    uint256 generation,
    uint256 genes
  );
}

contract ZombieFeeding is ZombieFactory {

  KittyInterface kittyContract;

  function setKittyContractAddress(address _address) external onlyOwner {
    kittyContract = KittyInterface(_address);
  }

  function _triggerCooldown(Zombie storage _zombie) internal {
    _zombie.readyTime = uint32(now + cooldownTime);
  }

  function _isReady(Zombie storage _zombie) internal view returns (bool) {
      return (_zombie.readyTime <= now);
  }

  // 1. 使这个函数的可见性为 internal
  function feedAndMultiply(uint _zombieId, uint _targetDna, string species) internal {
    require(msg.sender == zombieToOwner[_zombieId]);
    Zombie storage myZombie = zombies[_zombieId];
    // 2. 在这里为 `_isReady` 增加一个检查
    require(_isReady(myZombie));
    _targetDna = _targetDna % dnaModulus;
    uint newDna = (myZombie.dna + _targetDna) / 2;
    if (keccak256(species) == keccak256("kitty")) {
      newDna = newDna - newDna % 100 + 99;
    }
    _createZombie("NoName", newDna);
    // 3. 调用 `_triggerCooldown`
    _triggerCooldown(myZombie);
  }

  function feedOnKitty(uint _zombieId, uint _kittyId) public {
    uint kittyDna;
    (,,,,,,,,,kittyDna) = kittyContract.getKitty(_kittyId);
    feedAndMultiply(_zombieId, kittyDna, "kitty");
  }

}
```

# 3.8 进一步了解函数修饰符

相当不错！我们的僵尸现在有了“冷却定时器”功能。

接下来，我们将添加一些辅助方法。我们为您创建了一个名为 `zombiehelper.sol` 的新文件，并且将 `zombiefeeding.sol` 导入其中，这让我们的代码更整洁。

我们打算让僵尸在达到一定水平后，获得特殊能力。但是达到这个小目标，我们还需要学一学什么是“函数修饰符”。

## 带参数的函数修饰符

之前我们已经读过一个简单的函数修饰符了：`onlyOwner`。函数修饰符也可以带参数。例如：

```solidity
// 存储用户年龄的映射
mapping (uint => uint) public age;

// 限定用户年龄的修饰符
modifier olderThan(uint _age, uint _userId) {
  require(age[_userId] >= _age);
  _;
}

// 必须年满16周岁才允许开车 (至少在美国是这样的).
// 我们可以用如下参数调用`olderThan` 修饰符:
function driveCar(uint _userId) public olderThan(16, _userId) {
  // 其余的程序逻辑
}
```

看到了吧， `olderThan` 修饰符可以像函数一样接收参数，是“宿主”函数 `driveCar` 把参数传递给它的修饰符的。

来，我们自己生产一个修饰符，通过传入的`level`参数来限制僵尸使用某些特殊功能。

## 实战演习

1. 在`ZombieHelper` 中，创建一个名为 `aboveLevel` 的`modifier`，它接收2个参数， `_level` (`uint`类型) 以及 `_zombieId` (`uint`类型)。
2. 运用函数逻辑确保僵尸 `zombies[_zombieId].level` 大于或等于 `_level`。
3. 记住，修饰符的最后一行为 `_;`，表示修饰符调用结束后返回，并执行调用函数余下的部分。

```solidity
pragma solidity ^0.4.19;

import "./zombiefeeding.sol";

contract ZombieHelper is ZombieFeeding {

  // 在这里开始
  modifier aboveLevel(uint _level, uint _zombieId) {
    require(zombies[_zombieId].level >= _level);
    _;
  }
}

```

# 3.9 僵尸修饰符

现在让我们设计一些使用 `aboveLevel` 修饰符的函数。

作为游戏，您得有一些措施激励玩家们去升级他们的僵尸：

- 2级以上的僵尸，玩家可给他们改名。
- 20级以上的僵尸，玩家能给他们定制的 DNA。

是实现这些功能的时候了。以下是上一课的示例代码，供参考：

```solidity
// 存储用户年龄的映射
mapping (uint => uint) public age;

// 限定用户年龄的修饰符
modifier olderThan(uint _age, uint _userId) {
  require (age[_userId] >= _age);
  _;
}

// 必须年满16周岁才允许开车 (至少在美国是这样的).
// 我们可以用如下参数调用`olderThan` 修饰符:
function driveCar(uint _userId) public olderThan(16, _userId) {
  // 其余的程序逻辑
}
```

## 实战演习

1. 创建一个名为 `changeName` 的函数。它接收2个参数：`_zombieId`（`uint`类型）以及 `_newName`（`string`类型），可见性为 `external`。它带有一个 `aboveLevel` 修饰符，调用的时候通过 `_level` 参数传入`2`， 当然，别忘了同时传 `_zombieId` 参数。
2. 在这个函数中，首先我们用 `require` 语句，验证 `msg.sender` 是否就是 `zombieToOwner [_zombieId]`。
3. 然后函数将 `zombies[_zombieId] .name` 设置为 `_newName`。
4. 在 `changeName` 下创建另一个名为 `changeDna` 的函数。它的定义和内容几乎和 `changeName` 相同，不过它第二个参数是 `_newDna`（`uint`类型），在修饰符 `aboveLevel` 的 `_level` 参数中传递 `20` 。现在，他可以把僵尸的 `dna` 设置为 `_newDna` 了。

```solidity
pragma solidity ^0.4.19;

import "./zombiefeeding.sol";

contract ZombieHelper is ZombieFeeding {

  modifier aboveLevel(uint _level, uint _zombieId) {
    require(zombies[_zombieId].level >= _level);
    _;
  }

  // 在这里开始
  function changeName(uint _zombieId, string _newName) external aboveLevel(2, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].name = _newName;
  }

  function changeDna(uint _zombieId, uint _newDna) external aboveLevel(20, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].dna = _newDna;
  }
}
```

# 3.10 利用 'View' 函数节省 Gas

酷炫！现在高级别僵尸可以拥有特殊技能了，这一定会鼓动我们的玩家去打怪升级的。你喜欢的话，回头我们还能添加更多的特殊技能。

现在需要添加的一个功能是：我们的 DApp 需要一个方法来查看某玩家的整个僵尸军团 - 我们称之为 `getZombiesByOwner`。

实现这个功能只需从区块链中读取数据，所以它可以是一个 `view` 函数。这让我们不得不回顾一下“gas优化”这个重要话题。

## “view” 函数不花 “gas”

当玩家从外部调用一个`view`函数，是不需要支付一分 gas 的。

这是因为 `view` 函数不会真正改变区块链上的任何数据 - 它们只是读取。因此用 `view` 标记一个函数，意味着告诉 `web3.js`，运行这个函数只需要查询你的本地以太坊节点，而不需要在区块链上创建一个事务（事务需要运行在每个节点上，因此花费 gas）。

稍后我们将介绍如何在自己的节点上设置 web3.js。但现在，你关键是要记住，在所能只读的函数上标记上表示“只读”的“`external view` 声明，就能为你的玩家减少在 DApp 中 gas 用量。

> 注意：如果一个 `view` 函数在另一个函数的内部被调用，而调用函数与 `view` 函数的不属于同一个合约，也会产生调用成本。这是因为如果主调函数在以太坊创建了一个事务，它仍然需要逐个节点去验证。所以标记为 `view` 的函数只有在外部调用时才是免费的。

## 实战演习

我们来写一个”返回某玩家的整个僵尸军团“的函数。当我们从 `web3.js` 中调用它，即可显示某一玩家的个人资料页。

这个函数的逻辑有点复杂，我们需要好几个章节来描述它的实现。

1. 创建一个名为 `getZombiesByOwner` 的新函数。它有一个名为 `_owner` 的 `address` 类型的参数。
2. 将其申明为 `external view` 函数，这样当玩家从 `web3.js` 中调用它时，不需要花费任何 gas。
3. 函数需要返回一个`uint []`（`uint`数组）。

先这么声明着，我们将在下一章中填充函数体。

```solidity
pragma solidity ^0.4.19;

import "./zombiefeeding.sol";

contract ZombieHelper is ZombieFeeding {

  modifier aboveLevel(uint _level, uint _zombieId) {
    require(zombies[_zombieId].level >= _level);
    _;
  }

  function changeName(uint _zombieId, string _newName) external aboveLevel(2, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].name = _newName;
  }

  function changeDna(uint _zombieId, uint _newDna) external aboveLevel(20, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].dna = _newDna;
  }

  // 在这里创建你的函数
  function getZombiesByOwner(address _owner) external view returns(uint[]){}
}
```

# 3.11 存储非常昂贵

Solidity 使用`storage`(存储)是相当昂贵的，”写入“操作尤其贵。

这是因为，无论是写入还是更改一段数据， 这都将永久性地写入区块链。”永久性“啊！需要在全球数千个节点的硬盘上存入这些数据，随着区块链的增长，拷贝份数更多，存储量也就越大。这是需要成本的！

为了降低成本，不到万不得已，避免将数据写入存储。这也会导致效率低下的编程逻辑 - 比如每次调用一个函数，都需要在 `memory`(内存) 中重建一个数组，而不是简单地将上次计算的数组给存储下来以便快速查找。

在大多数编程语言中，遍历大数据集合都是昂贵的。但是在 Solidity 中，使用一个标记了`external view`的函数，遍历比 `storage` 要便宜太多，因为 `view` 函数不会产生任何花销。 （gas可是真金白银啊！）。

我们将在下一章讨论`for`循环，现在我们来看一下看如何如何在内存中声明数组。

## 在内存中声明数组

在数组后面加上 `memory`关键字， 表明这个数组是仅仅在内存中创建，不需要写入外部存储，并且在函数调用结束时它就解散了。与在程序结束时把数据保存进 `storage` 的做法相比，内存运算可以大大节省gas开销 -- 把这数组放在`view`里用，完全不用花钱。

以下是申明一个内存数组的例子：

```solidity
function getArray() external pure returns(uint[]) {
  // 初始化一个长度为3的内存数组
  uint[] memory values = new uint[](3);
  // 赋值
  values.push(1);
  values.push(2);
  values.push(3);
  // 返回数组
  return values;
}
```

这个小例子展示了一些语法规则，下一章中，我们将通过一个实际用例，展示它和 `for` 循环结合的做法。

> 注意：内存数组 **必须** 用长度参数（在本例中为`3`）创建。目前不支持 `array.push()`之类的方法调整数组大小，在未来的版本可能会支持长度修改。

## 实战演习

我们要要创建一个名为 `getZombiesByOwner` 的函数，它以`uint []`数组的形式返回某一用户所拥有的所有僵尸。

1. 声明一个名为`result`的`uint [] memory'` （内存变量数组）
2. 将其设置为一个新的 `uint` 类型数组。数组的长度为该 `_owner` 所拥有的僵尸数量，这可通过调用 `ownerZombieCount [_ owner]` 来获取。
3. 函数结束，返回 `result` 。目前它只是个空数列，我们到下一章去实现它。

```solidity
pragma solidity ^0.4.19;

import "./zombiefeeding.sol";

contract ZombieHelper is ZombieFeeding {

  modifier aboveLevel(uint _level, uint _zombieId) {
    require(zombies[_zombieId].level >= _level);
    _;
  }

  function changeName(uint _zombieId, string _newName) external aboveLevel(2, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].name = _newName;
  }

  function changeDna(uint _zombieId, uint _newDna) external aboveLevel(20, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].dna = _newDna;
  }

  function getZombiesByOwner(address _owner) external view returns(uint[]) {
    // 在这里开始
    uint[] memory result = new uint[](ownerZombieCount[_ owner]);
    return result;
  }

}

```

# 3.12 For 循环

在之前的章节中，我们提到过，函数中使用的数组是运行时在内存中通过 `for` 循环实时构建，而不是预先建立在存储中的。

为什么要这样做呢？

为了实现 `getZombiesByOwner` 函数，一种“无脑式”的解决方案是在 `ZombieFactory` 中存入”主人“和”僵尸军团“的映射。

```
mapping (address => uint[]) public ownerToZombies
```

然后我们每次创建新僵尸时，执行 `ownerToZombies [owner] .push（zombieId）` 将其添加到主人的僵尸数组中。而 `getZombiesByOwner` 函数也非常简单：

```
function getZombiesByOwner(address _owner) external view returns (uint[]) {
  return ownerToZombies[_owner];
}
```

### 这个做法有问题

做法倒是简单。可是如果我们需要一个函数来把一头僵尸转移到另一个主人名下（我们一定会在后面的课程中实现的），又会发生什么？

这个“换主”函数要做到：

1.将僵尸push到新主人的 `ownerToZombies` 数组中， 2.从旧主的 `ownerToZombies` 数组中移除僵尸， 3.将旧主僵尸数组中“换主僵尸”之后的的每头僵尸都往前挪一位，把挪走“换主僵尸”后留下的“空槽”填上， 4.将数组长度减1。

但是第三步实在是太贵了！因为每挪动一头僵尸，我们都要执行一次写操作。如果一个主人有20头僵尸，而第一头被挪走了，那为了保持数组的顺序，我们得做19个写操作。

由于写入存储是 Solidity 中最费 gas 的操作之一，使得换主函数的每次调用都非常昂贵。更糟糕的是，每次调用的时候花费的 gas 都不同！具体还取决于用户在原主军团中的僵尸头数，以及移走的僵尸所在的位置。以至于用户都不知道应该支付多少 gas。

> 注意：当然，我们也可以把数组中最后一个僵尸往前挪来填补空槽，并将数组长度减少一。但这样每做一笔交易，都会改变僵尸军团的秩序。

由于从外部调用一个 `view` 函数是免费的，我们也可以在 `getZombiesByOwner` 函数中用一个for循环遍历整个僵尸数组，把属于某个主人的僵尸挑出来构建出僵尸数组。那么我们的 `transfer` 函数将会便宜得多，因为我们不需要挪动存储里的僵尸数组重新排序，总体上这个方法会更便宜，虽然有点反直觉。

## 使用 `for` 循环

`for`循环的语法在 Solidity 和 JavaScript 中类似。

来看一个创建偶数数组的例子：

```solidity
function getEvens() pure external returns(uint[]) {
  uint[] memory evens = new uint[](5);
  // 在新数组中记录序列号
  uint counter = 0;
  // 在循环从1迭代到10：
  for (uint i = 1; i <= 10; i++) {
    // 如果 `i` 是偶数...
    if (i % 2 == 0) {
      // 把它加入偶数数组
      evens[counter] = i;
      //索引加一， 指向下一个空的‘even’
      counter++;
    }
  }
  return evens;
}
```

这个函数将返回一个形为 `[2,4,6,8,10]` 的数组。

## 实战演习

我们回到 `getZombiesByOwner` 函数， 通过一条 `for` 循环来遍历 DApp 中所有的僵尸， 将给定的‘用户id'与每头僵尸的‘主人’进行比较，并在函数返回之前将它们推送到我们的`result` 数组中。

1.声明一个变量 `counter`，属性为 `uint`，设其值为 `0` 。我们用这个变量作为 `result` 数组的索引。

2.声明一个 `for` 循环， 从 `uint i = 0` 到 `i <zombies.length`。它将遍历数组中的每一头僵尸。

3.在每一轮 `for` 循环中，用一个 `if` 语句来检查 `zombieToOwner [i]` 是否等于 `_owner`。这会比较两个地址是否匹配。

4.在 `if` 语句中：

1. 通过将 `result [counter]` 设置为 `i`，将僵尸ID添加到 `result` 数组中。
2. 将counter加1（参见上面的for循环示例）。

就是这样 - 这个函数能返回 `_owner` 所拥有的僵尸数组，不花一分钱 gas。

```solidity
pragma solidity ^0.4.19;

import "./zombiefeeding.sol";

contract ZombieHelper is ZombieFeeding {

  modifier aboveLevel(uint _level, uint _zombieId) {
    require(zombies[_zombieId].level >= _level);
    _;
  }

  function changeName(uint _zombieId, string _newName) external aboveLevel(2, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].name = _newName;
  }

  function changeDna(uint _zombieId, uint _newDna) external aboveLevel(20, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].dna = _newDna;
  }

  function getZombiesByOwner(address _owner) external view returns(uint[]) {
    uint[] memory result = new uint[](ownerZombieCount[_owner]);
    // 在这里开始
    uint counter = 0;
    for (uint i = 0; i < zombies.length; i++){
      if (zombieToOwner[i] == _owner) {
        result[counter] = i;
        counter++;
      }
    }
    return result;
  }
}
```

# 代码

## ownable.sol

```solidity
/**
 * @title Ownable
 * @dev The Ownable contract has an owner address, and provides basic authorization control
 * functions, this simplifies the implementation of "user permissions".
 */
contract Ownable {
  address public owner;

  event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

  /**
   * @dev The Ownable constructor sets the original `owner` of the contract to the sender
   * account.
   */
  function Ownable() public {
    owner = msg.sender;
  }


  /**
   * @dev Throws if called by any account other than the owner.
   */
  modifier onlyOwner() {
    require(msg.sender == owner);
    _;
  }


  /**
   * @dev Allows the current owner to transfer control of the contract to a newOwner.
   * @param newOwner The address to transfer ownership to.
   */
  function transferOwnership(address newOwner) public onlyOwner {
    require(newOwner != address(0));
    OwnershipTransferred(owner, newOwner);
    owner = newOwner;
  }
}
```

## zombieFactory.sol

```solidity
pragma solidity ^0.4.19;

import "./ownable.sol";

contract ZombieFactory is Ownable {

    event NewZombie(uint zombieId, string name, uint dna);

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
    uint cooldownTime = 1 days;

    struct Zombie {
      string name;
      uint dna;
      uint32 level;
      uint32 readyTime;
    }

    Zombie[] public zombies;

    mapping (uint => address) public zombieToOwner;
    mapping (address => uint) ownerZombieCount;

    function _createZombie(string _name, uint _dna) internal {
        uint id = zombies.push(Zombie(_name, _dna, 1, uint32(now + cooldownTime))) - 1;
        zombieToOwner[id] = msg.sender;
        ownerZombieCount[msg.sender]++;
        NewZombie(id, _name, _dna);
    }

    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }

    function createRandomZombie(string _name) public {
        require(ownerZombieCount[msg.sender] == 0);
        uint randDna = _generateRandomDna(_name);
        randDna = randDna - randDna % 100;
        _createZombie(_name, randDna);
    }
}
```

## zombieFeeding.sol

```solidity
pragma solidity ^0.4.19;

import "./zombiefactory.sol";

contract KittyInterface {
  function getKitty(uint256 _id) external view returns (
    bool isGestating,
    bool isReady,
    uint256 cooldownIndex,
    uint256 nextActionAt,
    uint256 siringWithId,
    uint256 birthTime,
    uint256 matronId,
    uint256 sireId,
    uint256 generation,
    uint256 genes
  );
}

contract ZombieFeeding is ZombieFactory {

  KittyInterface kittyContract;

  function setKittyContractAddress(address _address) external onlyOwner {
    kittyContract = KittyInterface(_address);
  }

  function _triggerCooldown(Zombie storage _zombie) internal {
    _zombie.readyTime = uint32(now + cooldownTime);
  }

  function _isReady(Zombie storage _zombie) internal view returns (bool) {
      return (_zombie.readyTime <= now);
  }

  function feedAndMultiply(uint _zombieId, uint _targetDna, string _species) internal {
    require(msg.sender == zombieToOwner[_zombieId]);
    Zombie storage myZombie = zombies[_zombieId];
    require(_isReady(myZombie));
    _targetDna = _targetDna % dnaModulus;
    uint newDna = (myZombie.dna + _targetDna) / 2;
    if (keccak256(_species) == keccak256("kitty")) {
      newDna = newDna - newDna % 100 + 99;
    }
    _createZombie("NoName", newDna);
    _triggerCooldown(myZombie);
  }

  function feedOnKitty(uint _zombieId, uint _kittyId) public {
    uint kittyDna;
    (,,,,,,,,,kittyDna) = kittyContract.getKitty(_kittyId);
    feedAndMultiply(_zombieId, kittyDna, "kitty");
  }
}
```

## zombieHelper.sol

```solidity
pragma solidity ^0.4.19;

import "./zombiefeeding.sol";

contract ZombieHelper is ZombieFeeding {

  modifier aboveLevel(uint _level, uint _zombieId) {
    require(zombies[_zombieId].level >= _level);
    _;
  }

  function changeName(uint _zombieId, string _newName) external aboveLevel(2, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].name = _newName;
  }

  function changeDna(uint _zombieId, uint _newDna) external aboveLevel(20, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].dna = _newDna;
  }

  function getZombiesByOwner(address _owner) external view returns(uint[]) {
    uint[] memory result = new uint[](ownerZombieCount[_owner]);
    uint counter = 0;
    for (uint i = 0; i < zombies.length; i++) {
      if (zombieToOwner[i] == _owner) {
        result[counter] = i;
        counter++;
      }
    }
    return result;
  }

}
```

