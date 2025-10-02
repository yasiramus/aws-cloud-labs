# Working with Amazon EBS

![ebs](../week-07-networking/assets/diagram/working%20_with%20_Amazon_EBS.drawio.png)

## Lab Overview

Amazon Elastic Block Store (Amazon EBS) is a scalable, high-performance block-storage service designed for Amazon EC2.

## 🎯 Objectives:

- Create an EBS volume.
- Attach and mount an EBS volume to an EC2 instance.
- Create a snapshot of an EBS volume.
- Create an EBS volume from a snapshot.

---

## Task 1: Creating a New EBS Volume

1. Open the **EC2 Management Console**.
2. Locate the **Lab instance**. Noted its **Availability Zone** (e.g., `us-west-2a`).
3. Navigate to **Elastic Block Store → Volumes**.
4. Choose **Create Volume** and configure:
   - Type: `gp2`
   - Size: `1 GiB`
   - Availability Zone: Same as Lab instance
   - Tag: `Name=My Volume`
5. Verify the volume appears with state `Available`.

📸 _Screenshot: Volume created_

---

## Task 2: Attaching the Volume

1. Select **My Volume → Actions → Attach volume**.
2. Attach to **Lab instance** with device name `/dev/sdb`.
3. Verify the status changes to **In-use**.

📸 _Screenshot: Volume attached_

---

## Task 3: Connecting to EC2 Instance

1. Use **EC2 Instance Connect → Connect**.
2. Verify access via the terminal.

---

## Task 4: Creating & Mounting File System

Run the following commands:

```bash
sudo mkfs -t ext3 /dev/sdb
sudo mkdir /mnt/data-store
sudo mount /dev/sdb /mnt/data-store
echo "/dev/sdb   /mnt/data-store ext3 defaults,noatime 1 2" | sudo tee -a /etc/fstab
```

````

Verify storage:

```bash
df -h
```

📸 _Screenshot: Mounted volume visible_

Create a test file:

```bash
sudo sh -c "echo some text has been written > /mnt/data-store/file.txt"
cat /mnt/data-store/file.txt
```

---

## Task 5: Creating an Amazon EBS Snapshot

1. From **Volumes**, select **My Volume → Actions → Create snapshot**.

   - Tag: `Name=My Snapshot`

2. Verify snapshot status changes to **Completed**.

📸 _Screenshot: Snapshot created_

Delete test file:

```bash
sudo rm /mnt/data-store/file.txt
```

---

## Task 6: Restoring from Snapshot

### 6.1 Create Volume from Snapshot

1. Select **My Snapshot → Actions → Create volume from snapshot**.
2. Availability Zone: Same as Lab instance
3. Tag: `Name=Restored Volume`

📸 _Screenshot: Restored volume created_

### 6.2 Attach Restored Volume

1. Attach to **Lab instance** with device `/dev/sdc`.

### 6.3 Mount Restored Volume

```bash
sudo mkdir /mnt/data-store2
sudo mount /dev/sdc /mnt/data-store2
ls /mnt/data-store2/file.txt
```

📸 _Screenshot: File restored from snapshot_

---

## Conclusion

✅ Created and mounted an EBS volume
✅ Took an EBS snapshot
✅ Restored a volume from snapshot

🎉 **Lab completed successfully**

```

---

todos:
- Save as `docs/ebs-lab.md` (so it sits alongside `lab-troubleshooting.md`).
- Add a **short link in README.md** like:
  - `- [VPC Troubleshooting Lab](docs/lab-troubleshooting.md)`
  - `- [Amazon EBS Lab](docs/ebs-lab.md)`

````
