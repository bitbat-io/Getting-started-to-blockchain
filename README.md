# 1. Intoduction & Basic Prototype


Blockchain is the one of most revolutionary technology of 21st century, which is still maturing 
and which potential is not fully realized yet. In its essence, blockchain is just 
a distributed glorified Linked List or we can say its a distributed database of records.

But what makes it unique is that its's not a private database, but a public one, 
i.e. everyone who uses it has a full or partial copy of it. A new record is only added whith a con
consent of other keepers of the database.

## Block
Let's start with the 'BLOCK' part of 'blockchain'. In blockchain it's blocks that sote valuable information. For example, bitcoin blocks store transactions, the essence of any cryptocurrency. Besides this, a block contains some technical information, like its version, current timestamp and the hash of the pervious block.


``` es6
class Block {
 constructor(Data) {
   this.Timestamp; //number
   this.Data = Data; //string
   this.PrevBlockHash; //string
   this.Hash; //string
 }
 ...
}
```
Let's explain all these terms.
- **Timestamp** is the timestamp of the time when the block is created.
- **Data** is the valuable information which we want to store.
- **PrevBlockHash** hash string of previous block
- **Hash** hash of current block

The key concept of blockchain is how Hash is calculated and that is what maked blockchain secure.
The thing is that calculating a hash is a computationally difficult operation, it takes some time.
That difficulty of calculating new hashes makes hard to modify after they are added.


For sake of simplicity we will simply calculate `SHA-256 hash`

```es6
import sha256 from 'js-sha256';

class Block {
 constructor(Data, PrevBlockHash) {
   this.Timestamp = this.getCurrentTimestamp();
   this.Data = Data;
   this.PrevBlockHash = PrevBlockHash;
   this.Hash = this.setHash();
 }
 
 getCurrentTimestamp() {
  return Math.floor(Date.now() / 1000);
 }
 
 setHash() {
  const headers = `${this.PrevBlockHash}${this.Data}${this.Timestamp}`;
  const hash = sha256(headers);
  return hash;
 }
 
 getHash() {
  return this.Hash;
 }
}
```

Les's create our first block of our blockchain - 

```es6
const Block1 = new Block('This is new data', 'PREVIOUS_BLOCK_HASH');
```

## Blockchain

Blockchain is just a list of bunch of data blocks or we can say some type of glorified linked list. i In simple linked list each `node` of `linked list` is storing address of previous node to keep track of sequence.

```
simple linked list
 NODE0 --> NODE1 --> NODE2 --> NODE3
```

The blocks are store in order of insertion i.e latest block will be sotred on the edge of blockchain.
this structure makes it quickly to get the latest block in a chain and get efficienty by its hash;

```es6
class Blockchain {
 constructor() {
  this.blocks = [] // array of Blocks
 }
 ...
}
```
This is out first blockchain!!!
I’ve never thought it would be so easy 😉

Let's implement the function to add new block

```es6
class Blockchain {
 constructor() {
  this.blocks = [] // array of Blocks
  }
  
  /**
   * Function to add new block to blockchain 
   * data {string} - valuable data, which we want to store in block
   **/
  addBlock(data) {
    const prevBlock = this.blocks[this.blocks.length - 1];
    const newBlock = new Block(data, prevBlock.getHash());
    this.blocks = this.blocks.push(newBlock); // push new block to blockchain
  }
 }
```

To add new block we need at least one block to have PrevBlockHash, but their is no block when blockchain is created.
Thus their must be at least one block in blockchain. The very initial block of blockchain is called `genesis block`.

```es6
class Blockchain {
 constructor() {
  this.blocks = this.initializeBlockchain();
  }
   /**
   * Function to initialize blockchain
   * by creating genesis block
   **/
  initializeBlockchain() {
   return [new Block('Genesis Block', '')];
  }
  
  /**
   * Function to add new block to blockchain 
   * data {string} - valuable data, which we want to store in block
   **/
  addBlock(data) {
    const prevBlock = this.blocks[this.blocks.length - 1];
    const newBlock = new Block(data, prevBlock.getHash());
    this.blocks = this.blocks.push(newBlock); // push new block to blockchain
  }
 }
```


# 2. Proof of Work

In previous section we seen how to build simple blockchain class and push new blocks  to the blockchain by simply calculating hash of concataion of **data**, **timestamp** and **previous block hash** but in real world blockchain adding new block to blockchain takes very high computation power. Our previous blockchain has flaw that adding block is cheat and easy.

## Proof of Work

The key idea of blockchain is that one has to perform some work to put data in it. It is this hard work maksed blockchain **secure** and **consistent**. also reward is paid for this hard work(🤑🤑mining🤑🤑)

The concept of mining in blockchain is very similar of real work mining `to get gold, one has to work hard`.
In blockchain, some participants(miners) of the network work to sustain the network, to add new blocks to it, and get reward for their work👷.

