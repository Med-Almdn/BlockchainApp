# Building a Blockchain from Scratch

This project is a simple Blockchain system in Python with an API for interaction via Postman. It allows users to create transactions, mine blocks, and view the blockchain, providing a practical understanding of blockchain concepts.

## Table of Contents

1. [Constructing the Blockchain](#step-1-constructing-the-blockchain)  
    1.1 [The `Blockchain` Class](#the-blockchain-class)  
    1.2 [What Does a `Block` Look Like?](#what-does-a-block-look-like)  
    1.3 [Proof of Work Algorithm](#proof-of-work-algorithm)  

2. [Building the Blockchain API](#step-2-building-the-blockchain-api)  
    2.1 [Setting Up Flask](#setting-up-flask)  
    2.2 [Adding Transactions](#adding-transactions)  
    2.3 [Mining Endpoint](#mining-endpoint)  
  

---

## Step 1: Constructing the Blockchain

### The `Blockchain` Class
To start, this class initializes an empty list to store the chain itself and another list for transactions.

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

### Proof of Work Algorithm
The proof of work algorithm seeks to find a number `q` such that the hash of the product `p * q` begins with a specified number of leading zeroes (in this example, four). Here, `last_proof` is used as `p`, and `proof` is incremented as `q` until a solution is found:

```python
def proof_of_work(last_proof):
    proof = 0
    while not valid_proof(last_proof, proof):
        proof += 1
    return proof
```

---

## Step 2: Building the Blockchain API

We'll create a web API using Python's Flask framework to interact with our blockchain through HTTP requests. Three primary endpoints will be defined:

- **`/transactions/new`**: Adds a new transaction to the next block.
- **`/mine`**: Mines a new block and adds it to the chain.
- **`/chain`**: Returns the full blockchain.

### Setting Up Flask
The server forms a single node in the blockchain network. Below is the basic setup:

```python
class Blockchain:
    # Define the blockchain class
    ...

app = Flask(__name__)
node_identifier = str(uuid4()).replace('-', '')
blockchain = Blockchain()

@app.route('/mine', methods=['GET'])
def mine():
    return "We'll mine a new Block"

@app.route('/transactions/new', methods=['POST'])
def new_transaction():
    return "We'll add a new transaction"

@app.route('/chain', methods=['GET'])
def full_chain():
    response = {
        'chain': blockchain.chain,
        'length': len(blockchain.chain),
    }
    return jsonify(response), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### Adding Transactions
Transactions are added through a `POST` request to `/transactions/new`, which expects data like:
```json
{
 "sender": "my address",
 "recipient": "someone else's address",
 "amount": 5
}
```

Transaction endpoint code:
```python
@app.route('/transactions/new', methods=['POST'])
def new_transaction():
    values = request.get_json()
    required = ['sender', 'recipient', 'amount']
    if not all(k in values for k in required):
        return 'Missing values', 400
    index = blockchain.new_transaction(values['sender'], values['recipient'], values['amount'])
    response = {'message': f'Transaction will be added to Block {index}'}
    return jsonify(response), 201
```

### Mining Endpoint
The mining endpoint:
1. Runs the proof-of-work algorithm.
2. Rewards the miner with 1 coin.
3. Adds the new block to the chain.

```python
@app.route('/mine', methods=['GET'])
def mine():
    last_block = blockchain.last_block
    last_proof = last_block['proof']
    proof = blockchain.proof_of_work(last_proof)
    blockchain.new_transaction(sender="0", recipient=node_identifier, amount=1)
    previous_hash = blockchain.hash(last_block)
    block = blockchain.new_block(proof, previous_hash)
    response = {
        'message': "New Block Forged",
        'index': block['index'],
        'transactions': block['transactions'],
        'proof': block['proof'],
        'previous_hash': block['previous_hash'],
    }
    return jsonify(response), 200
```

---

## Step 3: Interacting with the Blockchain
Run the server with `$ python blockchain.py`. Use cURL or Postman to interact:
- **Mine a Block**: `GET http://localhost:5000/mine`
- **Create a Transaction**: `POST http://localhost:5000/transactions/new`
- **View the Chain**: `GET http://localhost:5000/chain`

---

## Step 4: Testing the Blockchain API

### Testing Endpoints with cURL

#### 1. Testing the `/mine` Endpoint
Run the following command to mine a new block:
```bash
curl -X GET http://localhost:5000/mine
```

#### 2. Testing the `/transactions/new` Endpoint
Submit a new transaction:
```bash
curl -X POST -H "Content-Type: application/json" -d '{
    "sender": "address_1",
    "recipient": "address_2",
    "amount": 5
}' http://localhost:5000/transactions/new
```

#### 3. Testing the `/chain` Endpoint
View the full blockchain:
```bash
curl -X GET http://localhost:5000/chain
```

### Testing with Postman
- **Add a Transaction**: Select POST, add JSON to the body.
- **Mine a Block**: Use a GET request.
- **View the Chain**: Use a GET request for `/chain`.

### Verifying the Test Results
Ensure transactions are added to the chain correctly, mined blocks contain the new transactions, and all hashes and block links are accurate.

---

## What I Learned

Working on this blockchain project helped me develop and reinforce several key skills:

1. **Core Concepts of Blockchain**:
   - I gained a deeper understanding of blockchain fundamentals, including how blocks are structured, how they are linked together through cryptographic hashes, and how transactions are securely recorded within each block.

2. **Proof of Work and Consensus**:
   - I implemented a basic Proof of Work algorithm, learning how consensus mechanisms help secure blockchain networks by requiring computational work to validate new blocks.

3. **Cryptography and Hashing**:
   - Using hashing functions for block validation and linkage improved my understanding of cryptographic methods and their essential role in maintaining data integrity within the blockchain.

4. **API Development with Flask**:
   - Setting up a web API with Flask allowed me to practice building RESTful services, handling HTTP requests, and formatting responses in JSON to make the blockchain accessible to users.

5. **Transaction and Block Management**:
   - I learned how transactions are organized and stored within blocks and how mining rewards are used as an incentive in blockchain ecosystems.

6. **Testing and Interacting with APIs**:
   - I practiced using tools like Postman and cURL to interact with

 and test APIs, ensuring that each endpoint performed as expected.

