# ☁️ Project 3 — Day 2: CloudWatch Alarms + SNS Notifications

## 🎯 Goal

Create a CloudWatch Alarm that triggers when EC2 CPU usage exceeds 50% and sends a real email notification via AWS SNS.

---

## 📋 Prerequisites

- EC2 instance running Ubuntu (from Day 1)
- CloudWatch Agent installed and running
- AWS Console access
- A Gmail address to receive alerts

---

## 🏗️ How It Works

```
EC2 Instance → CloudWatch Alarm → SNS Topic → Email Notification
(CPU Spike)      (CPU > 50%)     (cloudwatch-Alerts01)   (Gmail)
```

---

## 🔧 Step 1 — Create an SNS Topic

SNS (Simple Notification Service) is what sends the email when your alarm fires.

1. Go to **AWS Console → SNS → Topics**
2. Click **Create Topic**
3. Type → **Standard**
4. Name →
   ```
   cloudwatch-Alerts01
   ```
5. Scroll down → Click **Create Topic**

---

## 🔧 Step 2 — Subscribe Your Email to the Topic

1. Inside your topic page → click **Create Subscription**
2. **Protocol** → select `Email`
3. **Endpoint** → enter your Gmail address
4. Click **Create Subscription**

> ⚠️ Check ALL Gmail folders for the confirmation email:
> - Primary inbox
> - Promotions tab
> - Spam folder
> - All Mail

---

## 🔧 Step 3 — Confirm Email Subscription

1. Open your Gmail inbox immediately after subscribing
2. Look for email with subject:
   ```
   AWS Notification - Subscription Confirmation
   ```
3. Click the blue **Confirm subscription** link inside the email
4. Go back to **SNS → Subscriptions**
5. Status must show: `Confirmed` ✅

> ⚠️ AWS will NOT send alerts until you confirm the subscription!

---

## 🔧 Step 4 — Test SNS Directly

Before connecting to CloudWatch, verify SNS can actually send emails.

1. Go to **SNS → Topics → cloudwatch-Alerts01**
2. Click **Publish message**
3. Fill in:
   - **Subject** → `Test Alert`
   - **Message body** → `This is a test notification from AWS SNS`
4. Leave everything else empty
5. Click **Publish message**

Check your Gmail — you should receive the test email within 30 seconds ✅

---

## 🔧 Step 5 — Create CloudWatch Alarm

1. Go to **AWS Console → CloudWatch → Alarms → All Alarms**
2. Click **Create Alarm**
3. Click **Select Metric**
4. Click **EC2 → Per-Instance Metrics**
5. Search your **Instance ID** in the search box
6. Select **CPUUtilization** → click **Select Metric**

### Configure the conditions:

| Field | Value |
|---|---|
| Statistic | Average |
| Period | 1 minute |
| Threshold type | Static |
| Condition | Greater than |
| Value | `50` |

> This means → alert me when CPU goes above 50%

---

## 🔧 Step 6 — Configure Alarm Actions

1. Click **Next**
2. **Alarm state trigger** → select `In alarm`
3. **Send a notification to** → select `Select an existing SNS topic`
4. Select **cloudwatch-Alerts01** from the dropdown
5. Click **Next**
6. Fill in:
   - **Alarm name** → `High-CPU-Alarm`
   - **Description** → `Triggers when CPU exceeds 50% for 1 minute`
7. Click **Next → Create Alarm** ✅

---

## 🔧 Step 7 — Stress Test the CPU

Now let's trigger the alarm for real by spiking the CPU!

### Install stress tool on EC2:
```bash
sudo apt update && sudo apt install stress -y
```

### Run the stress test:
```bash
stress --cpu 2 --timeout 300
```

> This hammers the CPU for 5 minutes — enough to trigger the alarm ✅

### Watch CPU live (open a second terminal):
```bash
top
```

> You should see CPU shooting up to 90-100% 🔥

### Watch alarm in AWS Console:
1. Go to **CloudWatch → Alarms**
2. Watch `High-CPU-Alarm` change:
   ```
   OK → In alarm 🔴
   ```
3. This takes about **2-3 minutes** to trigger
4. Check your **Gmail** — alert email should arrive!

### Stop the stress test:
```bash
Ctrl + C
```

> Alarm will return to OK state after 2-3 minutes ✅

---

## 📊 Alarm Configuration Summary

| Setting | Value |
|---|---|
| Alarm Name | High-CPU-Alarm |
| Metric | CPUUtilization |
| Namespace | AWS/EC2 |
| Statistic | Average |
| Period | 1 minute |
| Threshold | Greater than 50% |
| Action | Send notification to cloudwatch-Alerts01 |
| Notification | Email via SNS (Gmail) |

---

## 🐛 Troubleshooting

| Issue | Fix |
|---|---|
| No confirmation email received | Check Gmail Promotions & Spam tabs |
| Delete button greyed out on subscription | Subscription is Pending — confirm email first |
| Alarm stuck in Insufficient data | Wait 2-3 mins after stress test starts |
| Alarm triggered but no email | Check subscription status is Confirmed |
| CPU not spiking | Run: `stress --cpu 2 --timeout 300` |
| Wrong SNS topic in alarm | Edit alarm → update notification to correct topic |

---

## ✅ Day 2 — Completion Checklist

- [x] SNS Topic created (cloudwatch-Alerts01)
- [x] Email subscription created
- [x] Subscription confirmed via Gmail
- [x] SNS publish message test successful
- [x] CloudWatch Alarm created (High-CPU-Alarm)
- [x] Alarm linked to SNS topic
- [x] CPU stress test ran successfully
- [x] Alarm triggered (IN ALARM state 🔴)
- [x] Email alert received from AWS 📧
