# 1. Intoduction & Basic Prototype


Blockchain is the one of most revolutionary technology of 21st century, which is still maturing 
and which potential is not fully realized yet. In its essence, blockchain is just 
a distributed glorified Linked List or we can say its a distributed database of records.

But what makes it unique is that its's not a private database, but a public one, 
i.e. everyone who uses it has a full or partial copy of it. A new record is only added whith a con
consent of other keepers of the database.

## Block
Let's start with the 'BLOCK' part of 'blockchain'. In blockchain it's blocks that sote valuable information. For example, bitcoin blocks store transactions, the essence of any cryptocurrency. Besides this, a block contains some technical information, like its version, current timestamp and the hash of the pervious block.


``` golang
type Block struct {
 Timestamp int64
 Data []byte
 PrevBlockHash []byte
 Hash []byte
}
```
