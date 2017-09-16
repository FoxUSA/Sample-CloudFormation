# Sample-CloudFormation

## Naming scheme
I recommend always prefixing stack names, and exports with the environment the stack is a part of. IE `Prod-Web`, `Dev-DB`, `...`. This lets you groups environments together and avid collisions.

For CloudFormation files: use a `.cf.yml` or `.cf.json` as the extension to indicate the file is CloudFormation. `.template` is a generic term and has/could be used for other things. Editors may not know what format `.template` is in and cannot provide syntax highlighting.

`.cf.yml` and `.cf.json` has a benefit over simply `.yml` and `.json`   because you can see its a CloudFormation template. Many projects with have lots of configuration files and it will save you an few accidental clicks. Plus it makes them easier to find.

## Variables
Do not go crazy with parameters. Use parameters for things that actually differ between environments. Keep it simple.

Use stack exports to track backplane information such as VPC and subnets. You can easily reference the output by convention along with an environment parameter to retrieve environment specific information

## Patterns
### Producer/Consumer pattern
To leverage interconnectivity between resources, I generally do not recommend using IP based Security Groups(SG) except if they are broad(Summary masks). IE the public subnet is allowed to access this or this VPC is allows to access this.

For fine grain control, use a security group as a bus to facilitate security interconnectivity. Buses are used in computing to connect components with the minimal number of interconnection points. This is method is sometimes called `access security groups`.

![](images/producer-consumer.png)

In the above diagram, each resource has the following security groups attached to it.

| Resource | Security Groups |
| --- | --- |
| Bastion | Bastion SG, Bation Bus SG |
| Web App | Web App SG, DB Buss SG |
| DB | DB SG |

Being a consumer in this model is to be a member of the bus group. Being a member allows a resource to access the resource across the bus.

Being a producer is allowing access from a bus SG.

This may seem backwards at first. Think of the DB. The DB is a producer and the web app consumes the database. Oddly the Bastion is a consumer of management traffic the Web App produces.

A network interface by default can have up to 5 security groups attached [[1]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Appendix_Limits.html#vpc-limits-security-groups). More than enough to facilitate complicated solutions. This limit can be increased to 10 with some restrictions on number of group entries.

You can see examples of this in the templates in this repo.

## Why should you listen to my
- I have AWS certs one of which is a pro level.
- I work in Professional Services so I basically do the same thing over and over with dozens of clients. This is the best way to date to wire stuff up.
