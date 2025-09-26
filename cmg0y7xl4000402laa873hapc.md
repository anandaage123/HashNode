---
title: "Automating EC2 Start/Stop with EventBridge Scheduler and IAM Roles (Using Custom Trust Policy)"
datePublished: Fri Sep 26 2025 14:40:04 GMT+0000 (Coordinated Universal Time)
cuid: cmg0y7xl4000402laa873hapc
slug: automating-ec2-startstop-with-eventbridge-scheduler-and-iam-roles-using-custom-trust-policy
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1758897543809/0a7b4486-ab7d-4d06-9608-9a9e6322f123.png
tags: ec2, aws, devops, eventbridge

---

Efficient management of EC2 instances is critical for cost optimization and operational efficiency in AWS environments. Automating the start and stop of EC2 instances during off-hours can significantly reduce costs.

⚠️ **Note:** Directly stopping EC2 instances can cause service disruption or data loss in production environments. This guide is intended **for educational purposes or non-critical workloads**. Always implement appropriate safeguards, such as graceful shutdown scripts or snapshots, for production instances.

This guide demonstrates how to set up an **EventBridge Scheduler** to automate EC2 instance management, with a focus on the **custom IAM trust policy** required for this setup.

---

## Step 1: Create an IAM Role for EventBridge Scheduler

1. Navigate to **IAM → Roles → Create role**.
    
2. Select **Custom trust policy** and provide the following JSON:
    

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "scheduler.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

### Importance of a Custom Trust Policy

* EventBridge **Scheduler** is distinct from EventBridge **Rules** and requires its own service principal: [`scheduler.amazonaws.com`](http://scheduler.amazonaws.com).
    
* The custom trust policy explicitly allows Scheduler to assume the role and perform EC2 operations.
    

3. Click **Next: Permissions**.
    
4. Attach the **AmazonEC2FullAccess** policy.
    
5. Name the role **EventBridgeEC2Role** and create it.
    

---

## Step 2: Configure an EventBridge Schedule to Stop EC2 Instances

1. Open the **EventBridge** console.
    
2. Navigate to **Scheduler → Create schedule**.
    
3. Name the schedule **StopEC2InstanceRule**.
    
4. Select **Recurring schedule** and provide a cron expression for the desired stop time. Example:
    

```bash
0 20 * * ? *
```

> Adjust the cron expression according to your preferred stop time and time zone.

5. Set **Flexible time window → Off**.
    
6. Click **Next** to configure the target.
    
7. Specify the target details:
    
    * **Target type**: All API calls
        
    * **Service**: Amazon EC2
        
    * **Action**: StopInstances
        
    * **Parameters**:
        
    
    ```json
    {
      "InstanceIds": ["i-xxxxxxxxxxxxxx"]
    }
    ```
    
    * **Role**: Select **EventBridgeEC2Role**.
        
8. Review and create the schedule.
    

---

## Step 3: Configure an EventBridge Schedule to Start EC2 Instances

1. Create a new schedule named **StartEC2InstanceRule**.
    
2. Provide a cron expression for the desired start time. Example:
    

```bash
0 7 * * ? *
```

> Adjust the cron expression according to your preferred start time and time zone.

3. Configure the target:
    
    * **Target type**: All API calls
        
    * **Service**: Amazon EC2
        
    * **Action**: StartInstances
        
    * **Parameters**:
        
    
    ```json
    {
      "InstanceIds": ["i-xxxxxxxxxxxxxx"]
    }
    ```
    
    * **Role**: Select **EventBridgeEC2Role**.
        
4. Review and create the schedule.
    

---

## Why a Custom Trust Policy is Essential

* The default IAM role creation options do not include EventBridge Scheduler.
    
* The service principal must explicitly be [`scheduler.amazonaws.com`](http://scheduler.amazonaws.com).
    
* Without the custom trust policy, the schedules will fail with an access error, preventing the role from being assumed.
    

---

## Conclusion

With this configuration, EC2 instances will:

* **Stop at the scheduled off-hours**
    
* **Start at the scheduled operational hours**
    

This setup ensures cost efficiency while maintaining operational readiness. Always verify that the trust policy correctly reflects the service principal required for the automation to function successfully.