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

## Steps

### 1️⃣ Create VPC Network

Create a VPC named:

privatenet

Subnet:

privatenet-us  
CIDR: 10.130.0.0/20

<img width="1920" height="1080" alt="gc1" src="https://github.com/user-attachments/assets/eaf7bd86-d8d4-4e7f-9c31-457b823e9259" />

---

### 2️⃣ Create Firewall Rule

Allow SSH using IAP range:

35.235.240.0/20

Port:

tcp:22

<img width="1920" height="1080" alt="gc2" src="https://github.com/user-attachments/assets/e97964f5-7502-4e81-be07-53c2ff02ed4a" />

<img width="1920" height="1080" alt="gc3" src="https://github.com/user-attachments/assets/ebabd60c-6cc1-4359-a3c5-477bf09f22dd" />

---

### 3️⃣ Create VM Without External IP

VM name:

vm-internal

External IP:

None

<img width="1920" height="1080" alt="gc4" src="https://github.com/user-attachments/assets/73130c14-93f0-4225-bb95-6e0b5e237166" />

---

### 4️⃣ Connect Using IAP

```
gcloud compute ssh vm-internal --zone europe-west1-c --tunnel-through-iap
```

<img width="1920" height="1080" alt="gc5" src="https://github.com/user-attachments/assets/811cd11b-eb6f-48b4-b139-58ce4443e2d5" />

---

### 5️⃣ Enable Private Google Access

Go to:

VPC Network → Subnets → privatenet-us

Enable:

Private Google Access = ON

<img width="1920" height="1080" alt="gc6" src="https://github.com/user-attachments/assets/a81880d5-77a9-44ac-8494-140e39b3ef3b" />

---

### 6️⃣ Test Access to Cloud Storage

```
gcloud storage cp gs://cloud-training/gcpnet/private/access.svg gs://$MY_BUCKET
```

<img width="1920" height="1080" alt="gc7" src="https://github.com/user-attachments/assets/9ac948b2-5f81-49f3-ba7e-bc0691e2b090" />

---

### 7️⃣ Configure Cloud NAT

Network services → Cloud NAT

Create:

nat-config

Router:

nat-router

<img width="1920" height="1080" alt="gc8" src="https://github.com/user-attachments/assets/ab25c958-021f-4ae5-9b11-7f59d9ec52f3" />

---

### 8️⃣ Test Internet Access

```
sudo apt-get update
```

If successful, the VM can access the internet through Cloud NAT.

<img width="1920" height="1080" alt="gc9" src="https://github.com/user-attachments/assets/d6633ddd-5a4b-449c-95da-27b808da4ef4" />

---

### 9️⃣ NAT Logs

View NAT logs in **Logs Explorer**

<img width="1920" height="1080" alt="gc10" src="https://github.com/user-attachments/assets/058e8527-2e0f-4f77-ae19-936b742870ab" />

---

## Result

The VM instance without an external IP can:

- Connect securely using **IAP**
- Access Google APIs using **Private Google Access**
- Access the internet using **Cloud NAT**
