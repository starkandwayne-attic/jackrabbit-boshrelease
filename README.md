# BOSH Release for jackrabbit

## Usage

To use this bosh release, first upload it to your bosh:

```
bosh target BOSH_HOST
git clone https://github.com/cloudfoundry-community/jackrabbit-boshrelease.git
cd jackrabbit-boshrelease
bosh upload release releases/jackrabbit-1.yml
```

For [bosh-lite](https://github.com/cloudfoundry/bosh-lite), you can quickly create a deployment manifest & deploy a cluster:

```
templates/make_manifest warden
bosh -n deploy
```

For AWS EC2, create a single VM:

```
templates/make_manifest aws-ec2
bosh -n deploy
```

For AWS VPC, to create a single VM you first need to create an additional configuration file containing your subnet ID, etc. Assume its called `my-network.yml`:

``` yaml
meta:
  subnet_id: subnet-5a20091c
  port: 5000
  security_groups:
  - webapp-vpc-cec034ab

jobs:
  - name: jackrabbit_z1
    networks:
      - name: floating
        static_ips:
        - 54.84.122.115
```

Security group `webapp-vpc-cec034ab` would need to have ports 22 and 5000 open.

To generate the deployment file and deploy you need to include your config file above:

```
templates/make_manifest aws-vpc my-network.yml
bosh -n deploy
```


### Override security groups

For AWS & Openstack, the default deployment assumes there is a `default` security group. If you wish to use a different security group(s) then you can pass in additional configuration when running `make_manifest` above.

Create a file `my-networking.yml`:

``` yaml
---
networks:
  - name: home/vagrantjackrabbit1
    type: dynamic
    cloud_properties:
      security_groups:
        - jackrabbit
```

Where `- jackrabbit` means you wish to use an existing security group called `jackrabbit`.

You now suffix this file path to the `make_manifest` command:

```
templates/make_manifest openstack-nova my-networking.yml
bosh -n deploy
```
