Amazon Neptune failovers your primary instance to your standby instance.  If a standby instance is not created the primary instance failovers to an Amazon Neptune read replica. 

Amazon Neptune read replicas are associated with a priority tier (0-15).  In the event of a failover, Amazon RDS will promote the read replica that has the highest priority (the lowest numbered tier). If two or more replicas have the same priority, it will promote the one that is the same size as the previous primary instance.

To failover your Amazon Neptune instance

1.	Sign in to the AWS Management Console, click **Service** and select [Neptune](https://console.aws.amazon.com/neptune/).
2.	In the navigation pane, choose **Databases**
3.	Select `neptune-{YOUR_NAME}` instance
4.	Click on instance **Actions**
5.	Select **Failover**
6.	In the confirmation page, click **Failover**
7.	In the navigation pane, choose **Events**
8.	Notice that the Database cluster failed over to your standby node
    
     ![](assets/images/failover_event.png)

1. Once the failover process completed, you will see the list of events as following

     ![](assets/images/failover_event_completed.png)

2.  In the left navigation pane, click Database and exemine the database instance of your Aurora cluster that has Writer role has changed.     