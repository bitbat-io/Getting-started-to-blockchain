# 1. Intoduction and Basic Prototype


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
As a result of their work, a block is incorporated into blockchain in a secure way, which maintains the stability of rhe  whole blockchain database. 

This whole `do hard work and prove` mechanism is called proof-of-work. It's hard because it requires lot's of computation power to perform this operation, Even high performance computers cannot do it quickly. 

Moreover, the difficulty of this work is increases from time to time to keep new blocks are ar about 6 - 8 blocks per hour. In **Bitcoin**, the goal of such work is to find a hash for a block, that meets some requirements. It's this hash that serves the proof of work. Thus finding the proof is actual work.

Calculating the new hash that meets the some preset requirements is hard to calculate but easy to verify, thus it a **NP-Complete** type problem.

## Hashcash
Bitcoin uses **hashcahs**, a Proof of Work algorithm that was initially developed to for email spam prevention. Ot can be split into these steps - 
- Take some public data (in email its receiver email address and in bitcoin its block header)
- Add a counter to it.
- Get a hash of the (data + counter) combined.
- Check if it's meet certain requirements
-- if it dose then you are done
-- else increase counter and repeat the process

This is a brute force algorithm: you has to increase counter, and calculate hash until you dont find the the perfect hash that meet those requirements. That's why it's computationally expensive.

Now let's look closer at the requirements a hash has to meet. In the original Hashcash implementation, the requirement sounds like 'first 20 bits of a hash must be zeros'. In bitcoin, the requirement is adjusted from time to time, because by design, a block must be generated every 10 minutes, despite computation power increasing with time and more and more miners joining the network.

Lets create our requirement that hash has to starts with 3 zero-bytes:
```
'I Like Blockdag' ---> SHA256(...) ---> 00000000345abs34scdc.......
```

### Implimentation

First, let's define the difficulty of mining
```es6
const targetBits = 24;
```

In bitcoin **targetBits** is the amount of difficulty at which bitcoins are mined and these targetBits are stored in header part of block. In actual bitcoin blockchain their is an ***targetBits*** adjustment algorithm that increase the difficulty of amount over the time.
***We will not implement this algoritm for sake of simplicity.***🤗

24 is an arbitrary number, our goal is to have a target that takes less than 256 bits in memory. Difficulty must be significantly enought but not too much.

```es6
class ProofOfWork {
 constructor() {
  this.block;
  this.target;
 }
 
 NewProofOfWork() {
  
 }
}
````

