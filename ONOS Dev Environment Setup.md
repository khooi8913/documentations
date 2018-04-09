# ONOS Development Environment Setup



```
sudo apt-get install git
sudo apt-get install git-review

sudo apt-get install software-properties-common -y && \
sudo add-apt-repository ppa:webupd8team/java -y && \
sudo apt-get update && \
echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | sudo debconf-set-selections && \
sudo apt-get install oracle-java8-installer oracle-java8-set-default -y

git clone https://gerrit.onosproject.org/onos

export ONOS_ROOT=~/onos
source $ONOS_ROOT/tools/dev/bash_profile
```



```
$ONOS_ROOT/tools/build/onos-buck build onos --show-output
```

### Run ONOS

```
tools/build/onos-buck run onos-local -- clean debug
tools/test/bin/onos localhost
```

