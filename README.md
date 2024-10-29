# Building a Blockchain from Scratch

## Step 1: Constructing the Blockchain

### The `Blockchain` Class
To start, This class initializes an empty list to store the chain itself and another list for transactions.

**Class Blueprint:**
```python
class Blockchain(object):
    def __init__(self):
        # Initialize the blockchain with an empty list of blocks and transactions
        self.chain = []
        self.current_transactions = []

    def new_block(self):
        # Create and add a new block to the blockchain
        pass

    def new_transaction(self):
        # Record a new transaction in the list of transactions
        pass

    @staticmethod
    def hash(block):
        # Generate a SHA-256 hash of a block
        pass

    @property
    def last_block(self):
        # Return the most recent block in the blockchain
        pass
```

### What Does a `Block` Look Like?
Each block in this blockchain structure includes the following components:
- `index`: The block number
- `transactions`: A list of transactions within the block
- `proof`: A number used in Proof of Work
- `previous_hash`: The hash of the previous block

**Example Block:**
```python
block = {
    "index": 2,
    "previous_hash": "fa7b1a942468688d7f603eff85be612eaec555ea49fc4f573bbfcfc13fa27da7",
    "proof": 35293,
    "transactions": [
        {
            "amount": 3,
            "recipient": "8fc15221b8bc44abb7cbbed12584e2fd",
            "sender": "8527147fe1f5426f9dd545de4b27ee00"
        }
    ]
}
```