# Setup IPFS as special remote for datalad
### Install packages
install git-annex, datalad if you haven't already
```
// Ubuntu
apt install git-annex datalad // Switch package manager for the distribution you're using
// Mac
brew install git-annex datalad
```
Make sure you've installed least v0.14+ for datalad. There are some issues with previous versions.
If your package manager installed an old version, uninstall the old version, then reinstall datalad using pip
```
// use this for datalad installation if package manager installs old version
python3 -m pip install --upgrade datalad
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

### Initialize the IPFS remote
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

### Create sibling on github
```
datalad create-sibling-github -d . demo-dataset --publish-depends ipfs
```

### push to ipfs
```
datalad push --to ipfs
```
You can check the locations of all files in the annex:
```
git annex whereis
```

### push metadata to github
```
datalad push --to github
```
This will not push the actual data files to github, since we've defined the github sibling as dependent on ipfs

## Fetching dataset from ipfs on another machine
First make sure all the packages are installed and the ipfs remote is configured as described above (till the step where we run ```git annex initremote ...``` )

next, clone the github repository
```
// Notice ... datalad clone, not git clone
datalad clone <github repo link>
cd demo-dataset
```

alternatively, if you want to pull changes into an already cloned repo,
```
datalad update --merge -s github
```

enable ipfs remote (this needs the ipfs daemon to be running on this machine too)
```
git annex enableremote ipfs
```

retrieve the file
```
datalad get file1.txt
```
