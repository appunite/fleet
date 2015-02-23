# Fleet configuration

## Setup fleet ctl

```bash
# clone repostiory
git clone https://github.com/coreos/fleet.git
cd fleet
# build
./build
# Copy to your bash path
cp bin/fleetctl ~/bin/fleetctl

# Add your ssh key to agent
ssh-add ~/.ssh/id_rsa
# setup tunnel address
export FLEETCTL_TUNNEL=coreos1.appunite.net
# test connection
fleetctl list-machines
> MACHINE		IP		METADATA
> 1210e283...	8.8.8.127	-
> 85945e91...	8.8.8.113	-
> f7e21643...	8.8.8.112	-
```

## Run test service

```bash
fleetctl submit test.service 
fleetctl start test
```

Test:

```bash
fleetctl list-units
> test.service				85945e91.../62.210.244.113	inactive	dead
```

Show logs:

```bash
fleetctl journal test
```

Test if working:

```bash
# find ip via list-units
ssh core@62.210.244.113
# Run some docker
docker run --tty --interactive --link=testservice:test --rm busybox
# Test connection
telnet test 8888
> OK
```

Remove test:
```bash
fleetctl destroy test
```


## Services

* [Nginx](nginx/README.md)
* [Datadog monitoring](datadog/README.md)
* [Jenkins](jenkins/README.md)
* [cAdvisor](cadvisor/README.md)
* [deis router](deis/README.md)
* [google storage backup](backup/README.md)

## License

    Copyright [2015]
    
		Contributors:
		 * Jacek Marchwicki  <jacek.marchwicki@gmail.com>
		 * Karol Wojtaszek <karol@appunite.com>
		
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
    
    	http://www.apache.org/licenses/LICENSE-2.0
        
    
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
