https://youtu.be/yOeWcPBT458

## Enabling Performance Insight

??? Info 
    When you create a new DB instance, Performance Insights is enabled when you choose **Enable Performance Insights** in the **Performance Insights** section.

To enable Performance Insights for a DB instance using the console

1. Sign in to the AWS Management Console and open the Amazon RDS console at https://console.aws.amazon.com/rds/.

2. Choose Databases.

3. Choose the DB instance that you want to modify, and choose **Modify**.

    ??? Warning "No Performance Insight section?"
        Amazon RDS Performance Insights is not supported for MariaDB version 10.0, 10.1, or 10.3, or for MySQL version 5.5 or 8.0.

        For Amazon RDS for MariaDB and MySQL, Performance Insights is not supported on the following DB instance classes: db.t2.micro, db.t2.small, db.t3.micro, and db.t3.small.

        On Aurora MySQL, Performance Insights is not supported on db.t2 or db.t3 DB instance classes.

        Performance Insights is not supported for Aurora MySQL DB clusters enabled for parallel query.

4. In the **Performance Insights** section, choose **Enable Performance Insights**.
   
    ??? Tips  
        If **Performance Insights** section is not shown, back on the top of the page, on the **Instance Specification**, for **DB Instance class** select **db.r3.large -- 2 vCPU, 15.25 GiB RAM**

5. You have the following options when you choose Enable Performance Insights:

    - Retention – The amount of time to retain Performance Insights data. Choose either 7 days (the default) or 2 years.

    - Master key – Specify your AWS Key Management Service (AWS KMS) key. Performance Insights encrypts all potentially sensitive data using your AWS KMS key. Data is encrypted in flight and at rest. For more information, see Encrypting Amazon Aurora Resources.

6. Choose **Continue**.

    For Scheduling of Modifications, choose **Apply immediately**

7. Choose **Modify instance**.

## View Performance Dashboard

To view the Performance Insights dashboard in the AWS Management Console

1. Open the Amazon RDS console at https://console.aws.amazon.com/rds/.

2. In the navigation pane, choose **Performance Insights**.

3. Choose a DB instance. The Performance Insights dashboard is displayed for that DB instance.