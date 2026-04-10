# 🚀 Project 3 — AWS CloudWatch Agent Setup on EC2

## 🎯 Goal
Install and configure the AWS CloudWatch Agent on an EC2 instance to send logs and metrics to AWS CloudWatch.

---

## 📋 Prerequisites

- An EC2 instance running Ubuntu
- AWS Console access
- CloudWatch Agent already installed on the EC2 instance

---

## 🔧 Step 1 — Create IAM Role for CloudWatch

1. Go to **AWS Console → IAM → Roles → Create Role**
2. **Trusted entity type** → select `AWS Service`
3. **Use case** → select `EC2` → click **Next**
4. Search and attach the policy:
   ```
   CloudWatchAgentServerPolicy
   ```
5. Click **Next → Next**
6. **Role name:** `CloudWatchAgentRole`
7. Click **Create Role**

---

## 🔧 Step 2 — Attach IAM Role to EC2 Instance

1. Go to **EC2 → Instances → select your instance**
2. Click **Actions → Security → Modify IAM Role**
3. Select `CloudWatchAgentRole` from the dropdown
4. Click **Update IAM Role**

### ✅ Verify the role is attached (on EC2):
```bash
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

curl -s -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/iam/security-credentials/
```
> You should see your role name printed (e.g., `CloudWatchAgentRole`)

---

## 🔧 Step 3 — Create the CloudWatch Agent Config File

Switch to root and create the config file:

```bash
sudo su -
```

```bash
cat > /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json << 'EOF'
{
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/syslog",
            "log_group_name": "/ec2/project3/syslog",
            "log_stream_name": "{instance_id}",
            "timestamp_format": "%Y-%m-%dT%H:%M:%S"
          },
          {
            "file_path": "/var/log/auth.log",
            "log_group_name": "/ec2/project3/auth",
            "log_stream_name": "{instance_id}",
            "timestamp_format": "%Y-%m-%dT%H:%M:%S"
          }
        ]
      }
    }
  },
  "metrics": {
    "metrics_collected": {
      "cpu": {
        "measurement": ["cpu_usage_user", "cpu_usage_system", "cpu_usage_idle"],
        "metrics_collection_interval": 60
      },
      "mem": {
        "measurement": ["mem_used_percent"],
        "metrics_collection_interval": 60
      },
      "disk": {
        "measurement": ["used_percent"],
        "resources": ["/"],
        "metrics_collection_interval": 60
      }
    }
  }
}
EOF
```

### ✅ Verify the file was created:
```bash
cat /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

---

## 🔧 Step 4 — Load Config and Start the Agent

```bash
/opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config -m ec2 -s \
  -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

---

## 🔧 Step 5 — Verify Agent is Running

```bash
sudo systemctl status amazon-cloudwatch-agent
```

> You should see `Active: active (running)` ✅

---

## 🔧 Step 6 — Check Agent Logs

```bash
sudo tail -f /opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log
```

Look for these lines which confirm logs are flowing:
```
I! Reading from offset range [0-XXXXXX] in /var/log/syslog
I! [logagent] piping log from /ec2/project3/syslog/i-XXXXXXXXX to cloudwatchlogs
I! [logagent] piping log from /ec2/project3/auth/i-XXXXXXXXX to cloudwatchlogs
```

---

## 🔧 Step 7 — Generate a Test Log Entry

```bash
sudo logger "Test log from CloudWatch setup - Project 3"
```

---

## 🔧 Step 8 — Verify Logs in AWS Console

1. Go to **AWS Console → CloudWatch → Log Groups**
2. Make sure you are in the **correct region** (match your EC2 region)
3. You should see:
   ```
   /ec2/project3/auth
   /ec2/project3/syslog
   ```
4. Click on a log group → click the stream (your instance ID)
5. View your live logs! 🎉

---

## 🐛 Troubleshooting

| Issue | Fix |
|---|---|
| Agent not starting | Check `sudo journalctl -u amazon-cloudwatch-agent -n 50` |
| Config file missing | Recreate using Step 3 as root (`sudo su -`) |
| No log groups in CloudWatch | Verify region in AWS Console matches EC2 region |
| IAM errors in agent log | Ensure `CloudWatchAgentServerPolicy` is attached to the role |
| `curl` returns nothing for IAM role | Use IMDSv2 token-based curl (see Step 2) |

### Check which region your EC2 is in:
```bash
curl -s http://169.254.169.254/latest/meta-data/placement/region
```

---

## ✅ What Gets Monitored

### Logs shipped to CloudWatch:
| Log File | CloudWatch Log Group |
|---|---|
| `/var/log/syslog` | `/ec2/project3/syslog` |
| `/var/log/auth.log` | `/ec2/project3/auth` |

### Metrics collected every 60 seconds:
| Metric | Details |
|---|---|
| CPU | `cpu_usage_user`, `cpu_usage_system`, `cpu_usage_idle` |
| Memory | `mem_used_percent` |
| Disk | `used_percent` on `/` |

---

## 📁 Project Structure

```
project3-cloudwatch/
│
├── README.md                          ← This file
└── amazon-cloudwatch-agent.json       ← Agent config file
```

---

## 🔜 What's Next (Upcoming Days)

- **Day 2** — Create CloudWatch Alarms (CPU, Memory alerts)
- **Day 3** — Set up CloudWatch Dashboards to visualize metrics
- **Day 4** — Create Log Metric Filters (trigger alarms from log patterns)
- **Day 5** — Set up SNS notifications (email alerts when alarm triggers)

---

## 🛠️ Tech Stack

- **AWS EC2** — Ubuntu instance
- **AWS CloudWatch** — Log groups, metrics, monitoring
- **AWS IAM** — Role-based permissions
- **Amazon CloudWatch Agent** — v1.300064.1b1344
