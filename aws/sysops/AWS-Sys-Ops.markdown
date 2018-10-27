# AWS Certified Sysops Certification

## A Cloud Guru course

### About the Exam
  * 130 minutes in length
  * 65 questions
  * Results immediately
  * Passing is 720, top score of 1000
  * Multiple choice
  * Qualification is valid for 2 years
  * Scenario based questions

### Monitoring and Reporting

#### Cloudwatch
  * What is cloudwatch?
    * Used for montioring, things like
        * Compute
        * Database and analytics
        * Other
            * Estimated check on billings
    * Monitors host level metrics by default
        * CPU
        * Network
        * Disk
        * Status Check
    * Can't monitoing things like RAM utilization
    * Default EC2 monitoring is 5 minute intervals, unless you enable detailed monitoring which will then make it 1 minute intervals
    * Logs are stored for as long as you want
        * Default is indefinitely

  * Alarms
    * You can create an alerm to monitor an Amazon CloudWatch metric
        * EC2 CPU Utilization
        * ELB Latency
  * Exam tips
    * CloudWatch is a monitoring service to monitor AWS resources
    * Host level metrics consist of:
        * CPU
        * Network
        * Disk
        * Status Check
    * Ram utilization is a custom metric
    * Custom metrics minimum granularity is 1 minute.
    * You can retrieve data from any terminated EC2 or ELB instance after its termination. CloudWatch Logs by default are stored indefinitely.
    * Metric Granularity
        * 1 minute for detailed monitoring
        * 5 minute for standard monitoring
    * Cloudwatch can be used on premise
        * Just need to download and install the SSm agent and CloudWatch agent

#### Monitoring EBS
    * Different volume types
        * General purpose (SSD) - gp2
            * 3 IOPS per/GiB of volume size
            * Bigger the drive, the better performance
                * Up to 10k IOPS
                    * Anything above needs to be provisioned IOPs
        * Provisioned IOPS (SSD) - io1
        * Throughput Optimized (HDD) -st1
            * Big data
            * Data warehouses
            * Log processing
        * Cold (HDD) - hc1
    * Pre-Warming EBS Volumes
        * New EBS volumes receive their maximum performance the moment they are available
        * Storage blocks on volumes that were restored from snapshots must be pulled down from Amazon S# and wrtiten to the volume
            * You can avoid this performance hit in prod by reading from all of the blocks on your volume bfore you use it
        * For a new volume created from a snapshot, you should read all the blocks that have data before using the volume.
    * Volume status checks
        * Degraded or severely degraded = warning
        * Stalled or Not Available = impaired
    * Modifying EBS volumes
        * You can increase its size, type, or adjusts its IOPs performance
    * Exam tips
        * Types
            * General purpose (SSD) - gp2
                * Most workloads <10k IOPs
                * Burst up to 3k OPS
            * Provisioned IOPS (SSD) - io1
                * Critical business apps that require sustained IOPS performance
                * Large database workloads
            * Throughput Optimized (HDD) -st1
                * Streaming workloads
            * Cold (HDD) - hc1
                * Throughput-oriented storage for data that is infrequently accessed
                * Cannot be a boot volume
        * EBS Metrics
            * Good metric for calculating performance
                * Volume Read Ops/Volume Write Ops
                    * Total number of IO Ops in a specific period of time
                Volume queue length
                    * Number of read operations and write operation request waiting to be completed in a specific period of time.
