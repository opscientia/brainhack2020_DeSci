# Setup IPFS as special remote for datalad
### Install packages
install git-annex, datalad if you haven't already
```
// Ubuntu
apt install git-annex datalad // Switch package manager for the distribution you're using
// Mac
brew install git-annex datalad
```
Install ipfs CLI. Instructions are [here](https://docs.ipfs.io/install/command-line/)

### Add special remote script to PATH
Clone this repository:
```
git clone https://github.com/opscientia/brainhack2020_DeSci
cd brainhack2020_DeSci
```
Copy special remote script to where your OS can find it
```
sudo mkdir /usr/local/ipfs
//Add the following to your login script (your bashrc or zshrc etc.)
export PATH="/usr/local/ipfs:$PATH"
```

## Create a new datalad dataset
### Directory for the dataset
```
mkdir demo-dataset
cd demo-dataset
```

### Add some data.
```
// text file for demo
touch dataset1.txt
```

### Create the dataset
```
datalad create --force --description "datalad with ipfs demo"
datalad save -m "add file dataset1.txt"
```

###Initialize the IPFS remote
We need a local IPFS node running. In another terminal, start the ipfs daemon.
```
ipfs init && ipfs daemon
```

initialize special remote for git-annex
```
git annex initremote ipfs type=external externaltype=ipfs encryption=none
```

After you run this command, you can see the new ipfs sibling
```
datalad siblings
> .: here(+) [git]
> .: ipfs(+) [ipfs]
```

## Create sibling on github
```
datalad create-sibling-github -d . demo-dataset
```

## push to ipfs
```
datalad push --to ipfs
```

