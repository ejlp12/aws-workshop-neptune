# Loading Sample Data into your Cluster

In this section, you will load some sample data into your newly created Neptune instance using **Neptune's Bulk Loader**.  Ensure your primary Neptune database is in available state.

The data that are going to be loaded are Apache TinkerPop Gremlin data in the CSV format. The vertices and the edges are specified in separate files.

Sample data will be stored in Amazon S3 bucket then we will use `curl` command to start the loader. 
The loader can load from multiple vertex files and multiple edge files in a single load job.

For each load command, the set of files to be loaded must be in the same folder in the Amazon S3 bucket,

Ref: https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load-tutorial-format-gremlin.html

!!! Tips "Another Way to Load Data"
    **AWS Database Migration Service (AWS DMS)** can load data into Neptune from supported source databases (eg. MySQL, PostgreSQL, Oracle, Microsoft SQL Server, MongoDB, S3, etc). The source database remains fully operational during the migration, minimizing downtime for applications that rely on it. 

### Upload sample data to S3

To load data into Neptune database, perform the following:

1. In your Cloud9 Terminal, copy data by cloning a repository that contains the sample data
    ```
    git clone https://github.com/ejlp12/Neptune
    ```
2. Explore the sample data files in the `Neptune/data` directory. There are 2 sub directories `creditcardfraud` and `fraudring` that represent different use cases. 
   
    The following commands will create bucket in S3 then copy all folders and files in the `data` folder to S3 bucket. Change `ejlp` to your name.
   
    ```bash
    YOUR_NAME="ejlp"
    cd ~/environment/Neptune/data
    aws s3 mb s3://neptune-workshop-${YOUR_NAME}-data
    aws s3 sync . s3://neptune-workshop-${YOUR_NAME}-data
    ```

3. Now you can check your data from the [S3 Console](https://s3.console.aws.amazon.com/s3/home). 
    
    In the S3 Console, select `neptune-workshop-YOURNAME-data` and you should see 2 folders:
    
    ![](assets/images/s3_data.png)
    
    Click `creditcardfraud` folder and you should see 3 txt files:
    
    ![](assets/images/s3_data_creditcardfraud.png)

### Create S3 Endpoint

Neptune's Buld Loader will access to S3 bucket to retrive the files. Neptune instances that located your VPC can be connected to S3 via VPC endpoint. Let's build it!

1. Go to [VPC Console](https://console.aws.amazon.com/vpc/home){target=_blank}
2. Select **Endpoint** in the navigation bar
3. Click **Create Endpoint** button
4. In **Service Catagory** keep it the section on **AWS Services**
   
    ![](assets/images/create_s3_vpc_endpoint1.png)

5. In the **Service Name** table, filter using keyword "s3" then press Enter, then choose "com.amazonaws.us-east-1.s3" from the table.
   
    !!! Warning
        Please make sure your S3 bucket that you created in the previous step is in `us-east-1`
   
6. Select VPC where Neptune cluster is reside. If you use AWS Event Engine in a workshop then you should only have 1 VPC.
   
7. Checked **Route Table ID** 
   
8. Keep **Policy** setting to **Full Access**
   
    ![](assets/images/create_s3_vpc_endpoint2.png)

9. Click **Create Endpoint**.
    
    Once it created, you should see your new enpoint in the list:
    
    ![](assets/images/create_s3_vpc_endpoint.png)
   
   

### Create access key ID and secret access key

We will load data by sending REST API request to Neptune cluster, so we need a credential for the authentication.

1. Go to [IAM Console](https://console.aws.amazon.com/iam/home)
2. Select **User** in the left menu
3. Click **Add User** button
4. **User name**: neptune-admin
5. On the **Access type** select only **Programmatic access** to create an access key ID and secret access key for the AWS API or CLI.
6. Click **Next: Permission**, then select **Attach existing policies directly** box
7. In the Filter policies, search for "neptune" and hit Enter, then select **NeptuneFullAccess** and **NeptuneConsoleFullAccess**
     
     ![](assets/images/iam_policies1.png)

8. Change search box to "s3" and hit Enter, then select **AmazonS3FullAccess**
    
     ![](assets/images/iam_policies_s3.png)

9.  Click **Next: Tags**
    
10. Click **Next: Review**

11. Click **Create User**

12. Click **Download CSV** and save the file in your machine and open it using text editor. We will need the value of **Access key ID** and **Secret access key** in next step.
     
     ![](assets/images/iam_accesskey.png)


### Load sample data

!!! Warning 
    Make sure you already set the certificate location in the environment variable: `CURL_CA_BUNDLE` as descibed in the previous step
   
1. Back to you Cloud9 Terminal 
2. Run following command to load data on `creditcardfraud` folder in S3 bucket. 
   
    Please change the folling:
    - Neptune URL with your real Neptune end point 
    - Value of `source` with your S3 bucket name.
    - `accessKey` & `secretKey` with the value generated in the previous step.
   
    ```
    curl -v -X POST -H 'Content-Type: application/json' \
    https://neptune-{YOURNAME}-cluster.cluster-xxxx.us-east-1.neptune.amazonaws.com:8182/loader  \
    -d '{ 
      "source" : "s3://neptune-workshop-ejlp-data/creditcardfraud/",
      "accessKey" : "AKIA446S4KKJ7NBWKQ5L",
      "secretKey" : "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",
      "format" : "csv",
      "region" : "us-east-1",
      "failOnError" : "FALSE"
    }'   
    ```
    You should get following response:
    ```
    {
    "status" : "200 OK",
    "payload" : {
        "loadId" : "0026a3e2-0b80-4e79-8f6b-9539a9ac2262"
    }
    ```
    Copy the `loadId` value.

3. Check the status of data loding process.
    
    Change the `NEPTUNE_ENDPOINT` to your real Neptune endpoint and change `LOAD_ID` to the value of `loadId` from previous response (in the example above is `0026a3e2-0b80-4e79-8f6b-9539a9ac2262`):
   ```
   curl -X GET 'https://NEPTUNE_CLUSTER_ENDPOINT:8182/loader/LOAD_ID'  
   ```
   You should get simlar response as following if the loading is successfully finished:                                                                  

    ```
    {
        "status" : "200 OK",
        "payload" : {
            "feedCount" : [
                {
                    "LOAD_COMPLETED" : 3
                }
            ],
            "overallStatus" : {
                "fullUri" : "s3://neptune-workshop-ejlp-data/creditcardfraud/",
                "runNumber" : 1,
                "retryNumber" : 0,
                "status" : "LOAD_COMPLETED",
                "totalTimeSpent" : 6,
                "startTime" : 1595070679,
                "totalRecords" : 360,
                "totalDuplicates" : 0,
                "parsingErrors" : 0,
                "datatypeMismatchErrors" : 0,
                "insertErrors" : 0
            }
        }
    ```