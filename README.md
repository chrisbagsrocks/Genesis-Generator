# Genesis Block Generator
A python script for creating the parameters required for a unique genesis block. SHA256/scrypt/X11/X13/X15.
## Please Read First
I struggled using this program for about a week due to the poor "writeups". Many have cloned this but never added anything to it, and seeing how the original creator has not updated it in years, I wanted to do my best to help as many people as possible. The original "Tutorial" gives little information on how to use the program to generate your own merkle root, genesis block, etc and more just how to replicate other chains information. I have modified it to make it easier for the average person to understand.

### Dependencies
    sudo pip install scrypt construct==2.5.2
    
```
sudo apt install git
```

To create geneses based on X11 algorithm you will also need to install the [x11 Hash](https://github.com/chrisbagsrocks/X11-Hash) module. 
For X13 you will need the [x13 Hash](https://github.com/chrisbagsrocks/X13-Hash) module and for X15 the [x15 Hash](https://github.com/chrisbagsrocks/X15-Hash) module.

<details><summary>Understanding Code</summary>
<p>
    
Something not mentioned anywhere except in the code, is that there are defaults set, so if an item is left blank, the code will default to: 

The message in your genesis block, can be changed to anything. Many coins use a news article that was influential during that time.
```
"-z", "--timestamp", dest="timestamp", default="The Times 03/Jan/2009 Chancellor on brink of second bailout for banks",
```
nonce is an abbreviation for "number only used once, it is the number that blockchain miners are solving to receive the block reward.
```
"-n", "--nonce", dest="nonce", default=0,
```
Makes default algorithm SHA256, which is for Bitcoin
```
"-a", "--algorithm", dest="algorithm", default="SHA256",
```
Sets Public key to a random key without knowing private key
```
"-p", "--pubkey", dest="pubkey", default="04678afdb0fe5548271967f1a67130b7105cd6a828e03909a67962e0ea1f61deb649f6bc3f4cef38c4f35504e51ec112de5c384df7ba0b8d578a4c702b6bf11d5f",
```
This is the last value in your src/chainparams.cpp file looking at the CreateGenesisBlocks section. Take the "50 * COIN" value, 

50 * 100000000 = 5000000000. This is the output for the genesis block, It's best to leave it default just for ease of use 
```
"-v", "--value", dest="value", default=5000000000,
```
All except bits (-b) include a default in code but the bits will default to 0x1e0ffff0 which is the lowest difficulty for the genesis block

</p>
</details>

<details><summary>Steps</summary>
<p>
    
## 1. Generate a Private and Public Key
You will need a few things, first you need to generate a Private key and use that to generate a Public key. You can use many programs to do this, just make sure you are not using an online generator. That may put your project at risk if they log the private keys. Use something local, you can run the command: 
```
openssl rand -hex 32 
``` 
which will output a secure 32-bit private key, which can be used to generate a public key with: 

[blockchain-dev-tools](https://github.com/chrisbagsrocks/blockchain-dev-tools) with command ```./pubkey.py -u YOUR PRIVATE KEY```

You must define "COIN" with two commands, ```COIN=examplecoin``` and ```export COIN```
    
## 2. Download Repo and Prepare Command
On your Desktop,
```
git clone https://github.com/chrisbagsrocks/Genesis-Generator.git && cd Genesis-Generator
```
Now that we are in the directory, we need to prepare the command to find our parameters. An important thing to note is the difference in ```"``` vs ```“```. Very similar parenthesis, but ```“``` causes errors and ```"``` will allow you to put your full pszTimestamp.

At the bottom of the Examples tab, it includes 2 examples on what it SHOULD look like. Before starting you will need to know:
	
Algorithm (-a) - [SHA256|scrypt|X11|X13|X15]
	
pszTimestamp (-z) - Piece of text that is used in Genesis Block
	
pubkey (-p) Generated in Step 1, should be 130 characters long.
	
time (-t) Epoch Time can be calculated with command ```date +%s``` or [Epoch Converter](https://www.epochconverter.com/)

### Optional
	
If you were to look at Bitcoins chainparams.cpp, you will see a line that says: 

```genesis = CreateGenesisBlock(1231006505, 2083236893, 0x1d00ffff, 1, 50 * COIN);```
	
nonce (-n) - By default is 0 in equation but will output a different number. Equivalent to ```2083236893``` in Bitcoin File.
	
value (-v) - By default is 5000000000. Calculated by multiplying "Coin Value" by 100000000. Equivalent to ```50``` in Bitcoin File.
	
bits (-b) - By default is 1. This is the genesis blocks mining difficulty. Equivalent to ```0x1d00ffff``` in Bitcoin File.
	
</p>
</details>

<details><summary>Examples</summary>
<p>
    
### Examples
Create the original genesis hash found in Bitcoin

    python genesis.py -z "The Times 03/Jan/2009 Chancellor on brink of second bailout for banks" -n 2083236893 -t 1231006505
Output:

    algorithm: sha256
    merkle hash: 4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b
    pszTimestamp: The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
    pubkey: 04678afdb0fe5548271967f1a67130b7105cd6a828e03909a67962e0ea1f61deb649f6bc3f4cef38c4f35504e51ec112de5c384df7ba0b8d578a4c702b6bf11d5f
    time: 1231006505
    bits: 0x1d00ffff
    Searching for genesis hash..
    genesis hash found!
    nonce: 2083236893
    genesis hash: 000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f
Create the regtest genesis hash found in Bitcoin

    python genesis.py -z "The Times 03/Jan/2009 Chancellor on brink of second bailout for banks" -n 2 -t 1296688602 -b 0x207fffff

Create the original genesis hash found in Litecoin

    python genesis.py -a scrypt -z "NY Times 05/Oct/2011 Steve Jobs, Apple’s Visionary, Dies at 56" -p "040184710fa689ad5023690c80f3a49c8f13f8d45b8c857fbcbc8bc4a8e4d3eb4b10f4d4604fa08dce601aaf0f470216fe1b51850b4acf21b179c45070ac7b03a9" -t 1317972665 -n 2084524493
    
Create a unique genesis hash with custom pszTimestamp

    python genesis.py -a scrypt -z "Time flies like an arrow. Fruit flies like a banana."
    
Create the original genesis hash found in DarkCoin. (requires [xcoin-hash](https://github.com/lhartikk/xcoin-hash))

    python genesis.py -a X11 -z "Wired 09/Jan/2014 The Grand Experiment Goes Live: Overstock.com Is Now Accepting Bitcoins" -t 1317972665 -p "040184710fa689ad5023690c80f3a49c8f13f8d45b8c857fbcbc8bc4a8e4d3eb4b10f4d4604fa08dce601aaf0f470216fe1b51850b4acf21b179c45070ac7b03a9" -n 28917698 -t 1390095618 -v 5000000000

Create the original genesis hash found in HiroCoin (requires [xcoin-hash](https://github.com/lhartikk/xcoin-hash)).

    python genesis.py -a X11 -z "JapanToday 13/Mar/2014 Ways eyed to make planes easier to find in ocean" -p "040184710fa689ad5023690c80f3a49c8f13f8d45b8c857fbcbc8bc4a8e4d3eb4b10f4d4604fa08dce601aaf0f470216fe1b51850b4acf21b179c45070ac7b03a9" -n 1234746574 -t 1394723131 -v 40000000000

EXAMPLE BLANK
```    
python genesis.py -a ALGO -z "ARTICLE OR PHRASE" -p PUBKEY -t EPOCH TIME -b DIFFICULTY (-b optional, used for regtest primarily)
```
EXAMPLE FILLED
```    
python genesis.py -a scrypt -z "NY Times 01/Jan/2022 Stock Market Blah Blah Blah" -p 04c8da8cca440aad3dfe2bc8a30fd966df9bbcbda3c4da502aafc56ab089e1ddaa8bf08be60b834212cbbe2b9ddc4d7858798bc5a2c9a1cb494cfa31123962bc14 -t 1661722736 -b 0x207fffff
```
</p>
</details>

### Options
    Usage: genesis.py [options]
    
    Options:
      -h, --help show this help message and exit
      
      -t TIME, --time=TIME  the (unix) time when the genesisblock is created
      
      -z TIMESTAMP, --timestamp=TIMESTAMP
         the pszTimestamp found in the coinbase of the genesisblock
         
      -n NONCE, --nonce=NONCE
         the first value of the nonce that will be incremented
         when searching the genesis hash
         
      -a ALGORITHM, --algorithm=ALGORITHM
         the PoW algorithm: [SHA256|scrypt|X11|X13|X15]
         
      -p PUBKEY, --pubkey=PUBKEY
         the pubkey found in the output script
         
      -v VALUE, --value=VALUE
         the value in coins for the output, full value (exp. in Bitcoin, 50 * 100000000 = 5000000000 
         To get other coins value: Block Value * 100000000)
         
      -b BITS, --bits=BITS
         the target in compact representation, associated to a difficulty of 1


