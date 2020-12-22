
## Setting up a Git Annex Special Remote with IPFS

First, install git annex
```
brew install git-annex
```

Next, clone this repository and add `git-annex-remote-ipfs` to PATH. Make sure it is executable. In this example, I’ve downloaded the git annex remote ipfs executable and placed it in the /usr/local/ipfs directory.
```
sudo mkdir /usr/local/ipfs
export PATH="/usr/local/ipfs:$PATH"
```

*Don’t forget to update your login script (`bash_profile` / `bashrc` / `zshrc`) with your new PATH addition!*

Next make sure the ipfs daemon is initialized and running
```
ipfs init && ipfs daemon
```

Create a new git annex repository
```
mkdir ~/ipfs-demo && cd ~/ipfs-demo
git init && git annex init origin
```

Initialize the special remote, you can use [encryption](https://git-annex.branchable.com/encryption/) for sensitive files.
```
git annex initremote ipfs type=external externaltype=ipfs encryption=none
```

Add the file to the annex
```
git annex add file.nii.gz
```
		
Copy to ipfs
```
git annex copy --to ipfs
copy file.nii.gz (to ipfs...)
ok
(recording state in git...)
```

See where the file is
```
git annex whereis
whereis nicap55_T1w.nii.gz (2 copies)
  	463b1fa7-e0e3-4c5b-bfe0-7281bc75a1f5 -- [ipfs]
   	adac19e4-1684-4ce3-bca7-c56f460c64fc -- origin [here]

  ipfs: ipfs:QmbUomkaQwWjE16VY9wpkMaz746Y2AMrRuxHNhm59h4gmn
ok
```

Note that the ipfs url listed above can also be used to retrieve the file using the ipfs client. But now let’s try to retrieve the file using git! 

On another machine, we clone a repository from github containing all of the metadata required to retrieve our data from the IPFS network. You can try cloning the demo repository or try it with your own dataset.
```
git clone https://github.com/seldamat/ipfs-test.git
Cloning into 'ipfs-test'...
remote: Enumerating objects: 41, done.
remote: Counting objects: 100% (41/41), done.
remote: Compressing objects: 100% (29/29), done.
remote: Total 41 (delta 10), reused 38 (delta 7), pack-reused 0
Unpacking objects: 100% (41/41), done.
```

Next update the central repository on gitub. This creates a record of all clones of the dataset and is useful for keeping track of collaboration projects.
```
git annex sync
Remote origin not usable by git-annex; setting annex-ignore
commit
On branch master
nothing to commit, working tree clean
ok
pull origin
ok
push origin
Enumerating objects: 23, done.
Counting objects: 100% (23/23), done.
Delta compression using up to 16 threads
Compressing objects: 100% (18/18), done.
Writing objects: 100% (23/23), 2.17 KiB | 741.00 KiB/s, done.
Total 23 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
To https://github.com/seldamat/ipfs-test
 * [new branch]      git-annex -> synced/git-annex
 * [new branch]      master -> synced/master
```

You can confirm that the central repository has been updated by typing git annex info to get a list of all the current clones and remotes
```
git annex info 
trusted repositories: 0
semitrusted repositories: 2
	00000000-0000-0000-0000-000000000001 -- web
 	00000000-0000-0000-0000-000000000002 -- bittorrent
 	2845ce5e-45e1-411d-872a-548453e68b68 -- [ipfs]
 	4c49c195-d8dc-405c-a446-f442c602aa93 -- admin@sentia.local:~/Projects/ipfs-test [here]
 	59c39440-6fe6-4669-a0f5-5a32669dce5a -- origin
untrusted repositories: 0
transfers in progress: none
available local disk space: XX terabytes (+1 megabyte reserved)
local annex keys: 1
local annex size: 1.16 megabytes
annexed files in working tree: 1
size of annexed files in working tree: 1.16 megabytes
bloom filter size: 32 mebibytes (0% full)
backend usage:
	SHA256E: 1
```

Next enable the ipfs remote
```
git annex enableremote ipfs
enableremote ipfs ok
(recording state in git...)
```

Lastly retrieve the file from ipfs
```
git annex copy --from ipfs
copy sub-hebbianloop_ses-2019a_acq-mprage_t1w.nii.gz (from ipfs...)
Saving file(s) to .git/annex/tmp/SHA256E-s1157145--c1a8dcd6b83e31ee40784dce2ba709f98a6d697ef4778a813584e5d5d52a2bf0.nii.gz
 1.10 MiB / 1.10 MiB [==============================================] 100.00% 0s
(checksum...) ok
(recording state in git...)
```

That’s it! You’ve successfully published and retrieved a file from the interplanetary file system for peer-to-peer sharing of neuroimaging data.

Some things to note:
* You cannot `git annex drop` a file from ipfs, there will always be a permanent copy on the ipfs network.
* That being said, use encryption for sensitive data! You can add public keys of known collaborators to enable permissionless sharing between trusted parties.
* Data availability will depend on the number of seeders for a particular file. Share your dataset widely and encourage ipfs use to maximize the availability of your data.
