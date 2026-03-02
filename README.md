
# AWS Assignment 

## Objective
# The objective of this assignment is to Replace Unencrypted EC2 Root Volume with an Encrypted Root Volume Using an EBS Snapshot, without losing any data.



# ✅ STEP 1 – Launch EC2 Instance with Unencrypted Root Volume
🔹 Instance Details

. Instance Name: unencrypted-root-instance

. Description:
EC2 instance launched with an unencrypted root EBS volume to demonstrate replacement with an encrypted volume using snapshot method.

. AMI: Amazon Linux 2

. Instance Type: t2.micro

. Region: Asia Pacific (Mumbai) (ap-south-1)

![Amazon Machine Image](screenshots/1_screenshot_ec2_ami.png)


🔑 Key Pair Creation

. Key Pair Name: unencrypted-keypair

. Key Pair Type: RSA

. Private Key Format: .pem

After creating the key pair:

. Download the .pem file

. sStore it securely (Example: C:\AWS-Keys\unencrypted-keypair.pem)

![created key pair](screenshots\04_screenshot_ec2_running.png)


💾 Storage Configuration

During launch:

Root Volume Type: gp2 or gp3

Encryption: ❌ Disabled (Intentionally)

This ensures the root volume is unencrypted.

Screenshot Reference:
💾 Storage Configuration

During launch:

. Root Volume Type: gp2 or gp3

. Encryption: ❌ Disabled (Intentionally)

This ensures the root volume is unencrypted.

Screenshot Reference:
screenshots/01_launch_instance.png

![encryption disabled](screenshots\3_screenshot_check_encryption_diabled.png)


# ✅ STEP 2 – Connect to Linux Instance

Use:

. MobaXterm (recommended)
. select SSH
. provide hostname and username
. advanced SSH settings
. browse to .pem key
. accept and connected successfully to linux

![conect to linux via mobexterm](screenshots\4_screenshot_connect_to_linux.png)

. Switch to Root User : sudo su
. ls : test_data.txt   test_data_.txt.save
. cat /home/ec2-user/test_data.txt : The file exists on the EC2 instance within expected data. This file will serve as a checkpoint to confirm that no data is lost when the root volume is replaced with an encrypted volume.


# ✅ STEP 5 – Stop the Instance

Go to AWS Console:

. Select Instance

. Click Instance State

. Click Stop

![click_stop]
(screenshots\5_screenshot_stop_ec2.png)

. Wait until instance state becomes Stopped

![unencrypted_root_volume_stopped]
 (screenshots\6_screenshot_stopped_successfully.png)


# ✅ STEP 6 – Create Snapshot of Unencrypted Root Volume

. Go to Elastic Block Store → Volumes

. Select the root volume attached to the instance

. Click Actions → Create Snapshot
![snapshot of unencrypted root valume ap south 1a](screenshots\7_screenshot_snapshot_of_unencrypted_volume.png)

🔹 Snapshot Details:

. Name: unencrypted-root-snapshot

. Description: Snapshot of unencrypted root volume before encryption

Screenshot Reference: 8_screenshot_snapshot_details.png

![Name and Description](screenshots\8_screenshot_snapshot_details.png)

. snapshot created successfully

![snapshot created](screenshots\9_screenshot_snapshot_created_successfully.png)


# ✅ STEP 7 – Copy Snapshot with Encryption Enabled

Now we create an encrypted copy.

. Go to Snapshots

. Select unencrypted-root-snapshot

. Click Actions → Copy Snapshot

![select unencrypted_root_volume](screenshots\10_screenshot_copy_snapshot.png)

In copy options:

. description : Encrypted copy of unencrypted root snapshot for root volume replacement in same region (ap-south-1)
      ![destination region ap-south1](screenshots\copy_snapshot_with_encryption_enabledt.png)

✅ Enable Encryption

. Select default AWS KMS key

🔹 Encrypted Snapshot Name:

encrypted-root-snapshot

Screenshot screenshots\14_screenshot_copy_snapshot_created_successfully.png

![successfully created snapshot copy](screenshots\14_screenshot_copy_snapshot_created_successfully.png)




# ✅ STEP 8 – Create Encrypted Volume from Encrypted Snapshot

Select encrypted-root-snapshot

Click Actions → Create Volume
![create volume from snapshot](screenshots\15_screenshot_create_volume.png)

IMPORTANT:

Choose same Availability Zone as original instance

Encryption: ✅ Enabled (automatically)

![encryption enabled automatically](sscreenshots\16_screenshot_encryption_enabled.png)

🔹 Volume Name:

encrypted-root-volume

Screenshot Reference:
screenshots\17_screenshot_volume_name.png

![volume name tag](screenshots\17_screenshot_volume_name.png)

Field	Value
Volume Type	gp3 (default)
Size	Same as snapshot (8 GiB)
Availability Zone	Same as your EC2 instance (ap-south-1a)

![volume type](screenshots\18_screenshot_volume_type.png)

![volume availability zone](screenshots\19_screenshot_volume_az.png)


# ✅ STEP 9 – Detach Old Root Volume
🔹 Step 1 – Go to Volumes

. In the AWS Console, go to EC2 Dashboard

. In the left sidebar, click Volumes (under Elastic Block Store)

🔹 Step 2 – Identify the Old Root Volume

. Look for the volume attached to your instance:

. Check Attachment column → it should say your instance ID (i-07a37690e7d7801cd)

. Check Size → matches your root volume (e.g., 8 GiB)

. ssCheck Encryption → should be No for the unencrypted root

This is the original unencrypted root volume.

🔹 Step 3 – Detach Volume

. Tick the checkbox next to that volume

. Click Actions → Detach Volume

![Detach volume](screenshots\20_screenshot_detach_volume.png)

. AWS will ask for confirmation → Click Yes / Detach

![Detached successfully](screenshots\21_screenshot_detached_successfully.png)

. Wait until State shows available (not attached to any instance)

💡 Important Notes:

. Make sure your instance is still stopped

. Do not delete the old volume yet — just detach it.

. This ensures the root volume can be safely replaced with the encrypted one.

. After this, we can move to attach the new encrypted volume as the root volume.


# ✅ STEP 10 – Attach New Encrypted Volume as Root

🔹 Step 1 – Go to Volumes

. In AWS Console, go to EC2 → Volumes (left sidebar under Elastic Block Store)

. Find your encrypted-root-volume (the one you created from the encrypted snapshot)

🔹 Step 2 – Select the Volume

. Tick the checkbox next to encrypted-root-volume

🔹 Step 3 – Attach Volume

. Click Actions → Attach Volume

![attached volume](screenshots\22_screenshot_volume_attached.png)

🔹 Step 4 – Configure Attachment

In the popup:

. Instance: Select unencrypted_root_instance

. Device Name: Enter /dev/xvda (must match original root device)

. Click Attach
![Volume configured]s(screenshots\23_screenshot_volume_configuered_successfully.png)

🔹 Step 5 – Confirm

. Volume state will change to in-use

![volume inuse](screenshots\24_screenshot_volume_inuse.png)

. The root volume is now your encrypted EBS volume



# ✅ STEP 11 – Start the EC2 Instance

Step 1 – Go to Instances

. In AWS Console, go to EC2 → Instances (left sidebar)

. Make sure the region = ap-south-1 (Mumbai)

. Find your stopped instance: unencrypted_root_instance

Step 2 – Start the Instance

. Tick the checkbox next to your instance

. Click Instance state → Start instance

![start the stopped instance](screenshots\26_screenshot_start_the_stopped_instance.png)

A confirmation popup may appear → Click Yes / Start

Step 3 – Wait for Status Checks

. The Instance State will change from stopped → pending → running

![stopped](screenshots\27_screenshot_status_stopped.png)

![pensding](screenshots\28_screenshot_status_pending.png)

![running](screenshots\29_screenshot_status_running.png)

. Wait until Status Checks show: 2/2 checks passed ✅

Step 4 – Verify in Console

. Once running, you can connect using MobaXterm / SSH

. The instance now uses the encrypted root volume

💡 Screenshot Tip for Assignment:

. Take a screenshot showing the instance state = running and Status checks = 2/2 passed

![3/3 checks passed](screenshots\30_screenshot_3_by_3_checks_passed.png)

After this, the final step is to connect via SSH and check your test file to ensure no data was lost.




# ✅ STEP 12 – Verify Data is NOT Lost

🔹 How to Verify

. Connect to your instance using MobaXterm / SSH

Reconnect:
1️⃣ Switch to root (optional, if you need permissions) : sudo su

![linux](screenshots\31_screenshot_ connected_linux.png)

2️⃣ Navigate to your home directory: cd /home/ec2-user

![cd /home/ec2-user](screenshots\34_screenshot_cd_home_ec2-user_.png)

3️⃣ List files to make sure your test file exists:ls -l

![ls -1](screenshots\32_screenshot_connected_linux_ls.png)

. You should see: test_data.txt

. This confirms the file is still on the root volume.

4️⃣ View the contents of your test file:
cat test_data.txt

. Output should show exactly what you wrote before replacing the root volume.

Example: The file exists on the EC2 instance with the expected data. This file will serve as a checkpoint to confirm that no data is lost when the root volume is replaced with an encrypted volume.

5️⃣ Check disk usage to confirm volume is mounted:
df -h

![df -h](screenshots\33_screenshot_df_-h.png)

. Look for /dev/xvda mounted at /

. Confirms the instance is using the new encrypted root volume.


🎉 This confirms:

These steps are enough to prove your assignment objective: the encrypted root volume replaced the unencrypted one without losing any data. ✅

Root volume is now encrypted

No data was lost

Replacement successful

Screenshot Reference:
screenshots/07_data_verified.png


snapshots: [root@ip-172-31-41-203 ec2-user]# df -h
Perfect — this confirms your root volume is mounted and in use. ✅

Here’s what the df -h output tells us:

| Filesystem         | Size | Used | Avail | Mounted on  |
| ------------------ | ---- | ---- | ----- | ----------- |
| `/dev/nvme0n1p1`   | 8.0G | 1.6G | 6.4G  | `/`         |
| `/dev/nvme0n1p128` | 10M  | 1.3M | 8.7M  | `/boot/efi` |

. /dev/nvme0n1p1 → this is your root volume mounted at /

. Size and usage match your previous root volume

. This shows the encrypted root volume is properly attached and active

✅ Next Step – Verify Your Data snapshot: cat /home/ec2-user/test_data.txt

. If you see your original content, it confirms no data was lost during the root volume replacement.

final screenshot: 
After this, you can take a final screenshot for your assignment showing:

Instance running

Status checks passed

File content displayed in the terminal

![successfull](screenshots\35_screenshot_conclusion.png)




# Conclusion

In this assignment, the original unencrypted root volume was successfully replaced with an encrypted root volume using an EBS snapshot.

Key Points:

- Direct encryption of an existing volume is not possible.
- A snapshot must be created first.
- A new encrypted volume must be created from that snapshot.
- The instance must be stopped before replacing the root volume.
- No data was lost during the process.

The objective of converting an unencrypted root volume into an encrypted root volume was successfully achieved.


# 🎯 Final Result

✔ Data preserved
✔ Encryption enabled
✔ Instance running successfully





1️⃣ Terminate the EC2 Instance
🔹 Once terminated, you can no longer connect via SSH.

2️⃣ Delete EBS Volumes

3️⃣ Delete Snapshots
🔹 Once deleted, you cannot recover these snapshots, so only do this if you don’t need them