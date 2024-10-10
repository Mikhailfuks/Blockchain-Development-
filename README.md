
using namespace std;
using namespace CryptoPP;

// Structure to represent a block
struct Block {
    int index;
    string timestamp;
    string data;
    string previousHash;
    string hash;

    Block(int index, string timestamp, string data, string previousHash) 
        : index(index), timestamp(timestamp), data(data), previousHash(previousHash) {
        calculateHash();
    }

    // Calculate the hash of the block
    void calculateHash() {
        SHA256 hash;
        StringSource ss(
            (index + timestamp + data + previousHash).c_str(), true,
            new HashFilter(hash, new StringSink(hash)));
        hash.Update(hash);
        HexEncoder encoder;
        encoder.Attach(new StringSink(hash));
        StringSource(hash, true, new Redirector(encoder));
        this->hash = hash;
    }
};

// Create a new block
Block createBlock(int index, string timestamp, string data, string previousHash) {
    return Block(index, timestamp, data, previousHash);
}

// Generate a genesis block (first block in the chain)
Block genesisBlock() {
    return createBlock(0, "2023-09-27T10:00:00Z", "Genesis Block", "");
}

// Create a blockchain (simple chain with no consensus)
vector<Block> createBlockchain(int numBlocks) {
    vector<Block> blockchain;
    Block previousBlock = genesisBlock();
    blockchain.push_back(previousBlock);

    for (int i = 1; i < numBlocks; i++) {
        string timestamp = "2023-09-27T10:00:" + to_string(i) + "Z";
        string data = "Block " + to_string(i);
        Block block = createBlock(i, timestamp, data, previousBlock.hash);
        blockchain.push_back(block);
        previousBlock = block;
    }

    return blockchain;
}

// Print the blockchain
void printBlockchain(const vector<Block>& blockchain) {
    cout << "Blockchain:" << endl;
    for (const Block& block : blockchain) {
        cout << "Index: " << block.index << endl;
        cout << "Timestamp: " << block.timestamp << endl;
        cout << "Data: " << block.data << endl;
        cout << "Previous Hash: " << block.previousHash << endl;
        cout << "Hash: " << block.hash << endl << endl;
    }
}

int main() {
    vector<Block> blockchain = createBlockchain(5);
    printBlockchain(blockchain);
    return 0;
}
