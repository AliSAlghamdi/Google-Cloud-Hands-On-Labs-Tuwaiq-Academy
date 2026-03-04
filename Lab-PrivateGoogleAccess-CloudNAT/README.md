# Lab – Implement Private Google Access and Cloud NAT

## Objective
Learn how to allow a VM without a public IP address to access Google APIs and the internet securely using:

- Private Google Access
- Cloud NAT
- Identity-Aware Proxy (IAP)

---

## Architecture

VM (No Public IP)  
↓  
Private Google Access → Access Google APIs  
↓  
Cloud NAT → Access Internet

---

# Steps

---

## 1️⃣ Create VPC Network

Create a custom VPC network called:

privatenet

Subnet:

privatenet-us  
CIDR:

10.130.0.0/20

This network will host the internal VM used in this lab.

<img width="1920" height="1080" alt="gc3" src="https://github.com/user-attachments/assets/ebabd60c-6cc1-4359-a3c5-477bf09f22dd" />

---

## 2️⃣ Create Firewall Rule (Allow SSH via IAP)

Create a firewall rule to allow SSH access using Google's IAP tunnel IP range.

Allow:

35.235.240.0/20

Port:

tcp:22

This allows secure SSH access to the internal VM through Identity-Aware Proxy.

<img width="1920" height="1080" alt="gc4" src="https://github.com/user-attachments/assets/73130c14-93f0-4225-bb95-6e0b5e237166" />

---

## 3️⃣ Create VM Without External IP

Create a VM instance named:

vm-internal

Important configuration:

External IP = None

This ensures the VM is **not directly accessible from the internet**.

<img width="1920" height="1080" alt="gc5" src="https://github.com/user-attachments/assets/811cd11b-eb6f-48b4-b139-58ce4443e2d5" />

---

## 4️⃣ Connect to VM Using IAP Tunnel

Use Cloud Shell to connect to the VM through IAP:

```
gcloud compute ssh vm-internal --zone europe-west1-c --tunnel-through-iap
```

This allows secure SSH access without requiring a public IP address.

<img width="1920" height="1080" alt="gc6" src="https://github.com/user-attachments/assets/a81880d5-77a9-44ac-8494-140e39b3ef3b" />

---

## 5️⃣ Enable Private Google Access

Enable **Private Google Access** on the subnet.

Navigation:

VPC Network → Subnets → privatenet-us

Set:

Private Google Access = ON

This allows instances without public IPs to reach **Google APIs and services**.

<img width="1920" height="1080" alt="gc8" src="https://github.com/user-attachments/assets/ab25c958-021f-4ae5-9b11-7f59d9ec52f3" />

---

## 6️⃣ Test Private Google Access Using Cloud Storage

Create a Cloud Storage bucket and copy a test file.

Example commands:

```
export MY_BUCKET=ali-bucket-v1
gcloud storage cp gs://cloud-training/gcpnet/private/access.svg gs://$MY_BUCKET
```

Then attempt to download the file.

Successful transfer confirms access to **Google APIs through Private Google Access**.

<img width="1920" height="1080" alt="gc7" src="https://github.com/user-attachments/assets/9ac948b2-5f81-49f3-ba7e-bc0691e2b090" />

---

## 7️⃣ Verify File Transfer from Bucket

The VM successfully copied the file from the bucket.

This confirms:

- Private Google Access is working
- The VM can access Google services without a public IP

<img width="1920" height="1080" alt="gc9" src="https://github.com/user-attachments/assets/d6633ddd-5a4b-449c-95da-27b808da4ef4" />

---

## 8️⃣ Configure Cloud NAT

Create a Cloud NAT gateway.

Configuration:

Gateway name:

nat-config

Router:

nat-router

Network:

privatenet

Region:

europe-west1

This allows internal VMs to access the internet **without exposing them with public IPs**.

<img width="1920" height="1080" alt="gc10" src="https://github.com/user-attachments/assets/058e8527-2e0f-4f77-ae19-936b742870ab" />

---

# Result

The VM instance without an external IP can now:

- Connect securely using **IAP**
- Access **Google APIs and services** using Private Google Access
- Access the **internet securely through Cloud NAT**

This architecture improves security by preventing direct public exposure of VM instances.
