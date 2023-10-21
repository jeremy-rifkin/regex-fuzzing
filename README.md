# Regex fuzzing driver

This is a driver script that invokes https://github.com/ucsb-seclab/regulator-dynamic to fuzz a regular expression for
ReDoS vulnerabilities. More info at https://www.usenix.org/system/files/sec22summer_mclaughlin.pdf.

## Fuzzer Setup instructions

Omit virtualbox instructions if not using virtualbox.

```bash
sudo apt update
sudo apt upgrade
sudo apt install virtualbox-guest-utils virtualbox-guest-x11 virtualbox-guest-dkms git make build-essential net-tools
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt install nodejs openssh-server build-essential python3 python3-distutils libicu-dev vim cmake python-is-python3 zlib1g-dev libbz2-dev libffi-dev libncurses-dev libncursesw5-dev libreadline-dev libssl-dev sqlite-devel libsqlite3-dev lzma liblzma-dev libbz2-dev
sudo vim /etc/ssh/sshd_config # disable password authentication
ssh-keygen
vim .ssh/authorized_keys # add your key
sudo systemctl restart sshd.service

git clone https://github.com/ucsb-seclab/regulator-dynamic.git
curl https://pyenv.run | bash
eval "$(pyenv virtualenv-init -)" # in bashrc
eval "$(pyenv init -)" # in bashrc
export PATH="${HOME}/.pyenv/bin:$PATH" # in bashrc
pyenv install --list
pyenv install 3.8.0
pyenv global 3.8.0

git clone https://github.com/ucsb-seclab/regulator-dynamic.git
cd regulator-dynamic/fuzzer
make -j 4 # you can go higher but the link steps use a lot of ram

# if you want to try a simple regex
cd ..
export REGULATOR_FUZZER=`realpath fuzzer/build/fuzzer_stripped`
cd driver
python3 main.py --help
python3 main.py --regex "\\[([^\\]]*)]\\[([^\\]]*)]" --flags "g" -v
```
