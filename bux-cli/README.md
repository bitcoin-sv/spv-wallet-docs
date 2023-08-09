# Bux CLI

This is a command line interface for the BuxOrg ecosystem. You can easily use it to generate xpubs, xprivs, addresses, destinations and more.

> Details about all the commands and the repo itself can be found [here](https://github.com/BuxOrg/bux-cli)

## Installation
* With brew (macOS)
```bash
brew tap BuxOrg/bux-cli && brew install bux-cli
buxcli
```
* With go
```bash
go get github.com/BuxOrg/bux-cli
cd /$GOPATH/src/github.com/BuxOrg/bux-cli && make install
buxcli
```

## Usage
* Generate xpubs and xprivs
  > In this example I will use the go run command to run the bux-cli without installing it:

First step - create new xpriv
```bash
go run main.go xpriv new
```
output:
```bash
{
  "private_key": "",
  "wif": "",
  "xpriv": "xprv9s21ZrQH143K446DnEcXo2FZ6diAyQDCsXgH1aC1o8snaaY22ekZ3XfuXE1YgRJfU7MopKVMHwVwtdry1absLwdZGurHakZpPTf5FAa8LyN",
  "xpub": ""
}
```

Then, we can ask for they details of the xpriv we just created:
```bash
go run main.go xpriv info <<here put xpriv from previous command>>
```

output:
```bash
{
  "private_key": "4e995bcc0941e176e6a21124cf5ad25a27eab00b0bd1cfd24aec596bb465c34d",
  "wif": "5JQuHiD2C4afMz22PdwyfH1nLtD1UvLM27b8ZhqPH36XJrMJgCN",
  "xpriv": "xprv9s21ZrQH143K446DnEcXo2FZ6diAyQDCsXgH1aC1o8snaaY22ekZ3XfuXE1YgRJfU7MopKVMHwVwtdry1absLwdZGurHakZpPTf5FAa8LyN",
  "xpub": "xpub661MyMwAqRbcGYAgtG9YAACHefYfNrw4EkbsoxbdMUQmTNsAaC4obKzPNWzukeH6Vd2PnCzmdt15NsLV2vYketWzntsMpfChoYGvKZrPFfi"
}
```
