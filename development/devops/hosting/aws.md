<!-- TITLE: AWS -->
<!-- SUBTITLE:  -->

# AWS

## Pricing Breakdown
### Server
Can be one or many, but they should be of the same type. Doing 3 t2.micro’s does not mean you can now handle 31 concurrent users. 
**Pricing:** https://aws.amazon.com/ec2/pricing/reserved-instances/pricing/

#### t2.micro
**Users:** ~15.625 concurrent
**Size:** 1 CPU / 1 GB RAM

#### t2.small
**Users:** ~31.25 concurrent
**Size:** 1 CPU / 2 GB RAM

#### t2.medium
**Users:** ~62.5 concurrent
**Size:** 2 CPU / 4 GB RAM

### Database

Necessary for clients who require multiple applications that need to access a singular datasource, or require the use of a load balancer. It is generally recommended that all clients use an external database for ease of scaling, performance, and data-recovery. It should be considered a requirement for any client who is looking at the potential of rapid growth in traffic.
**Pricing:** https://aws.amazon.com/rds/mysql/pricing/

#### db.t2.micro
**Users:** ~50 concurrent
**Size:** 1 CPU / 1 GB RAM

#### db.t2.small
**Users:** ~100 concurrent
**Size:** 1 CPU / 2 GB RAM

#### db.t2.medium
**Users:** ~200 concurrent
**Size:** 2 CPU / 4 GB RAM

### Load Balancer

If a client is using two servers, they will require a load balancer in addition to a database server. Load balancing servers to maintain a consistent experience for a user as traffic increases and decreases. It also helps prevent against server crashes. If an instance fails, traffic will all be directed to the remaining servers allowing time to fix an the issue without the site going down.

Billing for a load balancer is calculate per hour based off of use. Assuming the site receives an average of 1 new connection per second, each lasting 2 minutes, the monthly cost should be around $6.22.

Assuming 100 new connections per second, lasting roughly 3 minutes each, the average monthly cost would be around $34.56 per month.

All costs are variable and depend upon use, requests made, and duration of stay.

**Pricing:** https://aws.amazon.com/elasticloadbalancing/pricing/

### Auto Scaling

Auto scaling is a way of adding and removing servers as demand increases or decreases. It can be significantly more expensive and far more variable as only the initial server cost is set. Beyond that, any additional server that is spun up to handle the increase in traffic is done so on a per hour use cost. Auto Scaling is primarily recommended for ecommerce websites.

Auto scaling does not incur additional charges outside of the cost of the additional on-demand instance.


### S3

S3 is an external storage for assets to be loaded. Think dropbox. If a setup uses 2 servers + load balancing, or Auto scaling, they will require an S3 bucket to serve up assets to their site.

**Pricing: ** https://aws.amazon.com/s3/pricing/

